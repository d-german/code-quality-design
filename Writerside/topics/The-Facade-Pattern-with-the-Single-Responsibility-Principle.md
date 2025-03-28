# The Facade Pattern with the Single Responsibility Principle

## Introduction

![](facade-185.png)

The Facade Pattern simplifies interactions with complex systems by providing a single interface. This design pattern is
crucial for developers looking to create easy-to-use APIs over systems with multiple moving parts. Let's explore this
concept through a practical example.

## The Facade Pattern Explained

A facade is like the front of a building: it hides the complexity behind a neat and simplified interface. In software
design, it means providing a straightforward way to interact with a complex set of operations, improving usability and
reducing dependency on internal details.

### Example Implementation

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


``` C#
public interface ISearchHighlightGenerator
{
    string GenerateHighlightedContent(string query, DocumentIdentifier identifier);
}

public class SearchHighlightGenerator : ISearchHighlightGenerator
{
    private readonly IUserSession _userSession;
    private readonly ISearchEngine _searchEngine;
    private readonly IDocumentRenderer _documentRenderer;
    private readonly IDocumentAccessProvider _documentAccessProvider;
    private readonly ISearchRepository _searchRepository;
    private readonly IFileReader _fileReader;
    private readonly IUtilityHelper _utilityHelper;
    
    public SearchHighlightGenerator(
        IUserSession userSession,
        ISearchEngine searchEngine,
        IDocumentRenderer documentRenderer,
        IDocumentAccessProvider documentAccessProvider,
        ISearchRepository searchRepository,
        IFileReader fileReader,
        IUtilityHelper utilityHelper)
    {
        _userSession = userSession ?? throw new ArgumentNullException(nameof(userSession));
        _searchEngine = searchEngine ?? throw new ArgumentNullException(nameof(searchEngine));
        _documentRenderer = documentRenderer ?? throw new ArgumentNullException(nameof(documentRenderer));
        _documentAccessProvider = documentAccessProvider ?? throw new ArgumentNullException(nameof(documentAccessProvider));
        _searchRepository = searchRepository ?? throw new ArgumentNullException(nameof(searchRepository));
        _fileReader = fileReader ?? throw new ArgumentNullException(nameof(fileReader));
        _utilityHelper = utilityHelper ?? throw new ArgumentNullException(nameof(utilityHelper));
    }
    
    public string GenerateHighlightedContent(string query, DocumentIdentifier identifier)
    {
        if (query == null) throw new ArgumentNullException(nameof(query));
        if (string.IsNullOrWhiteSpace(query)) throw new ArgumentException("Value cannot be empty or whitespace only string.", nameof(query));
        if (identifier == null) throw new ArgumentNullException(nameof(identifier));
        if (identifier.Type != DocumentType.Standard) return string.Empty;
        
        var searchDocument = _documentAccessProvider.GetSearchObjectID(identifier.ID, identifier.RevisionID, identifier.FileTypeID);
        var rendition = _documentRenderer.GetRendition(_userSession, identifier);
        
        if (rendition == null) throw new InvalidOperationException("Rendition cannot be null");
        
        using (var stream = _utilityHelper.GetDocumentStream(_userSession, rendition))
        {
            return _fileReader.ReadAllText(
                _searchEngine.HighlightSearchTerms(
                    _searchRepository.GetSearchIndex(_userSession, searchDocument.CatalogID),
                    searchDocument.ObjectID,
                    query,
                    stream
                )
            );
        }
    }
}
```

This class, acting as a facade, simplifies the generation of content with highlighted search terms. It orchestrates
various services (like session management, search engines, and document rendering) behind a single method,
`GenerateHighlightedContent`. When provided with a search query and document identifier, it returns HTML content with
the search terms highlighted.

## The Single Responsibility Principle (SRP)

SRP states that a class should have one reason to change, equating to a single responsibility.
`SearchHighlightGenerator` adheres to SRP by focusing solely on generating highlighted search results, despite
interacting with multiple subsystems. It delegates specific tasks to services designed for those purposes, thus
maintaining a single responsibility—orchestrating the search-and-highlight workflow.

## Conclusion

The Facade Pattern, complemented by the Single Responsibility Principle, is a powerful tool in a developer's arsenal for
creating maintainable and user-friendly software. By abstracting complex interactions into a single, coherent API,
developers can provide clear, concise, and effective solutions.

---
See Also:
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Decorator Pattern](Decorator-Pattern.md) (builds upon the Facade example)
- [Composition](Composition.md) (Facade uses composition)
- [Dependency Injection (DI)](Dependency-Injection-DI.md) (Facade dependencies injected)
