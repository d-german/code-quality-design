# Decorator Pattern

Recall the facade pattern we discussed earlier? It provides a simplified interface to a complex subsystem.

```PlantUML
@startuml
title "SearchHighlightGenerator - Facade Pattern"
hide empty members

interface ISearchHighlightGenerator {
    + GenerateHighlightedContent(query: string, identifier: DocumentIdentifier): string
}

class SearchHighlightGenerator {
    - _userSession: IUserSession
    - _searchEngine: ISearchEngine
    - _documentRenderer: IDocumentRenderer
    - _documentAccessProvider: IDocumentAccessProvider
    - _searchRepository: ISearchRepository
    - _fileReader: IFileReader
    - _utilityHelper: IUtilityHelper
    + SearchHighlightGenerator(userSession: IUserSession, searchEngine: ISearchEngine, documentRenderer: IDocumentRenderer, 
                               documentAccessProvider: IDocumentAccessProvider, searchRepository: ISearchRepository, 
                               fileReader: IFileReader, utilityHelper: IUtilityHelper)
    + GenerateHighlightedContent(query: string, identifier: DocumentIdentifier): string
}

ISearchHighlightGenerator <|.. SearchHighlightGenerator : implements

@enduml

```

## Introduction to the Problem

Our search highlight generator faced two issues:

1. Form controls within search results weren't being rendered as read-only
2. Text areas containing anchor links weren't displaying properly

Rather than modifying our existing `SearchHighlightGenerator` facade class, we implemented the Decorator pattern to
extend functionality without changing the original code.

```PlantUML
@startuml
title "Decorator Pattern Example"
hide empty members

interface IComponent {
    + operation(): void
}

class Decorator {
    - component: IComponent
    + operation(): void
}

IComponent <|... Decorator : implements
Decorator o--- IComponent : composition

@enduml


```

The essence of the Decorator pattern is that each decorator class both implements an interface and contains a reference
to an object implementing that same interface. This dual relationship—implementation and composition of the same
interface—allows the decorator to seamlessly extend behavior by first delegating to the wrapped component and then
adding its own functionality, all while maintaining the original interface contract.

## The Decorator Pattern Solution

The decorator pattern allows us to wrap objects with new behavior. In our case, we created two decorators that enhance
the base functionality while adhering to the same interface.

### Making Form Controls Read-Only

```PlantUML

@startuml
title "SearchResultReadonlyDecorator - Decorator Pattern"
hide empty members

interface ISearchHighlightGenerator {
    + GenerateHighlightedContent(query: string, identifier: DocumentIdentifier): string
}

class SearchResultReadonlyDecorator {
    - _innerGenerator: ISearchHighlightGenerator
    + SearchResultReadonlyDecorator(innerGenerator: ISearchHighlightGenerator)
    + GenerateHighlightedContent(query: string, identifier: DocumentIdentifier): string
}

ISearchHighlightGenerator <|... SearchResultReadonlyDecorator : implements
SearchResultReadonlyDecorator o-- ISearchHighlightGenerator : composition

@enduml

```

```C#
public class SearchResultReadonlyDecorator : ISearchHighlightGenerator
{
    private static readonly ImmutableArray<string> FormElementXPathsToDisable = ImmutableArray.Create(
        "//input",
        "//textarea",
        "//select",
        "//button");
    private const string AttrDisabled = "disabled";
    private readonly ISearchHighlightGenerator _innerGenerator;
    
    public SearchResultReadonlyDecorator(ISearchHighlightGenerator innerGenerator)
    {
        _innerGenerator = innerGenerator ?? throw new ArgumentNullException(nameof(innerGenerator));
    }
    
    public string GenerateHighlightedContent(string query, DocumentIdentifier identifier)
    {
        HtmlDocument htmlDoc = new HtmlDocument();
        htmlDoc.LoadHtml(_innerGenerator.GenerateHighlightedContent(query, identifier));
        
        foreach (string xpathQuery in FormElementXPathsToDisable)
        {
            SetDisabled(htmlDoc, xpathQuery);
        }
        
        return htmlDoc.DocumentNode.OuterHtml;
    }
    
    private static void SetDisabled(HtmlDocument doc, string xpath)
    {
        HtmlNodeCollection nodes = doc.DocumentNode.SelectNodes(xpath);
        if (nodes == null) return;
        
        foreach (HtmlNode node in nodes)
        {
            if (node.Attributes[AttrDisabled] == null)
            {
                node.SetAttributeValue(AttrDisabled, AttrDisabled);
            }
        }
    }
}
```

### Dependency Injection Configuration

```C#
// ...
services.TryAddScoped<SearchHighlightGenerator>();
services.TryAddScoped<ISearchHighlightGenerator>(provider => 
    new SearchResultReadonlyDecorator(provider.GetRequiredService<SearchHighlightGenerator>()));
```

## Further Extension with Decorator Pattern

The next decorator fixes issues with text areas containing anchor links by replacing them with properly styled div
elements.

```PlantUML

@startuml
title "TextareaSearchResultDecorator - Decorator Pattern"
hide empty members

interface ISearchHighlightGenerator {
    + GenerateHighlightedContent(query: string, identifier: DocumentIdentifier): string
}

class TextareaSearchResultDecorator {
    - _innerGenerator: ISearchHighlightGenerator
    + TextareaSearchResultDecorator(innerGenerator: ISearchHighlightGenerator)
    + GenerateHighlightedContent(query: string, identifier: DocumentIdentifier): string
    }


ISearchHighlightGenerator <|... TextareaSearchResultDecorator : implements
TextareaSearchResultDecorator o-- ISearchHighlightGenerator : composition

@enduml

```

```C#
public class TextareaSearchResultDecorator : ISearchHighlightGenerator
{
    private readonly ISearchHighlightGenerator _innerGenerator;
    private const string AdditionalStyle = @"
        border: 1px solid #ccc;           
        padding: .375rem 0rem .375rem 0rem;
        resize: both;
        overflow: auto;
        pointer-events: none;";
    private const string SearchTermHighlightTextareaXPath = "//textarea";
    private static readonly Regex AnchorHrefRegex = new Regex(@"<a[^>]*href=[""'][^""']*[""']", RegexOptions.Compiled);
    
    public TextareaSearchResultDecorator(ISearchHighlightGenerator innerGenerator)
    {
        _innerGenerator = innerGenerator ?? throw new ArgumentNullException(nameof(innerGenerator));
    }
    
    public string GenerateHighlightedContent(string query, DocumentIdentifier identifier)
    {
        var htmlContent = _innerGenerator.GenerateHighlightedContent(query, identifier);
        var htmlDoc = new HtmlDocument();
        htmlDoc.LoadHtml(htmlContent);
        
        var textAreaNodes = htmlDoc.DocumentNode.SelectNodes(SearchTermHighlightTextareaXPath);
        if (textAreaNodes == null) return htmlDoc.DocumentNode.OuterHtml;
        
        ReplaceTextAreasWithAnchorsAsDivs(textAreaNodes);
        return htmlDoc.DocumentNode.OuterHtml;
    }
    
    private static void ReplaceTextAreasWithAnchorsAsDivs(HtmlNodeCollection textAreaNodes)
    {
        foreach (var textAreaNode in textAreaNodes)
        {
            if (!AnchorHrefRegex.IsMatch(textAreaNode.InnerText)) continue;
            
            var divNode = HtmlNode.CreateNode("<div></div>");
            CopyContentAndAttributes(textAreaNode, divNode);
            ApplyAdditionalStyles(divNode);
            textAreaNode.ParentNode.ReplaceChild(divNode, textAreaNode);
        }
    }
    
    private static void CopyContentAndAttributes(HtmlNode sourceNode, HtmlNode targetNode)
    {
        targetNode.InnerHtml = sourceNode.InnerHtml;
        foreach (var attribute in sourceNode.Attributes)
        {
            targetNode.Attributes.Add(attribute.Name, attribute.Value);
        }
    }
    
    private static void ApplyAdditionalStyles(HtmlNode node)
    {
        var existingStyle = node.GetAttributeValue("style", string.Empty);
        node.SetAttributeValue("style", existingStyle + AdditionalStyle);
    }
}
```

### Stacking Decorators in Dependency Injection

```C#
// ...
services.TryAddScoped<ISearchHighlightGenerator>(provider =>
    new TextareaSearchResultDecorator(
        new SearchResultReadonlyDecorator(
            provider.GetRequiredService<SearchHighlightGenerator>())));
```

## Conclusion

Like Russian Matryoshka dolls, each decorator wraps around our core search highlight generator, enhancing its
functionality without altering it. This approach demonstrates how we can:

1. Add features without modifying existing code (Open/Closed Principle)
2. Layer functionality in a composable way
3. Maintain a clean separation of concerns
4. Easily add, remove, or reorder decorators as needed

![](Matryoshka.png)

By using decorators, we've maintained the integrity of our original facade implementation while seamlessly adding the
capability to handle read-only form controls and properly render text areas with anchor links.


---
See Also:
- [The Facade Pattern with the Single Responsibility Principle](Facade-Pattern-with-the-Single-Responsibility-Principle.md)
- [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
- [Composition](Composition.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Dependency Injection (DI)](Dependency-Injection-DI.md) (for how decorators are wired)
- [Interception](Interception.md)