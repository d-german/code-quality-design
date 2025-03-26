# Prefer Dictionary to Switch Statement

## Comparing Ways to Get Filenames

Let's look at three ways in C# to get a filename based on a `mediaType` text.

### 1. Using `switch`

This code uses a `switch` block. It checks `mediaType` and returns the matching filename.

```C#
// Get filename using switch
public static string GetFileNameImperativeSwitchStatement(
    string mediaType)
{
    switch (mediaType)
    {
        case Dicom:
            return DicomStudyFileName;
        case Excel:
            return MsExcelFileName;
        case Html:
            return HtmlFileName;
        case MultipartForm:
            return ElectronicFormFileName;
        case Pdf: 
            return PdfFileName;
        case Word:
            return MsWordFileName;
        default: 
            return DefaultFileName;
    }
}
```

### 2. Using many `if` statements

This code uses many `if` checks. If `mediaType` matches, it returns the filename. The last `return` is used if no `if`
matches.

```C#
// Get filename using multiple if checks
public static string GetFileNameImperativeMultipleIf(
    string mediaType)
{
    if (mediaType == Dicom)
        return DicomStudyFileName;
    if (mediaType == Excel)
        return MsExcelFileName;
    if (mediaType == Html)
        return HtmlFileName;
    if (mediaType == MultipartForm)
        return ElectronicFormFileName;
    if (mediaType == Pdf) 
        return PdfFileName;
    if (mediaType == Word)
        return MsWordFileName;

    // If no 'if' was true, return default
    return DefaultFileName;
}
```
### 3. Using a Dictionary (Map)

This code first creates a `Dictionary`. A dictionary stores pairs of keys and values (like `mediaType` -> `filename`).
Then, the function looks up the `mediaType` in the dictionary.

```C#
// Make the list of media types and filenames
private static readonly IDictionary<string, string> ToFileName =
    new Dictionary<string, string>
    {
        [Dicom] = DicomStudyFileName,
        [Excel] = MsExcelFileName,
        [Html] = HtmlFileName,
        [MultipartForm] = ElectronicFormFileName,
        [Pdf] = PdfFileName, // Key = media type name
        [Word] = MsWordFileName
    }.ToImmutableDictionary();

public static string GetFileNameDeclarative(string mediaType)
{
    return ToFileName.TryGetValue(mediaType, out var fileName)
        ? fileName
        : DefaultFileName;
}
```

## Why the Dictionary is Often Better

All three ways work. But I like the dictionary better. Here's why:

1. **Easy to Read:** The dictionary clearly shows all the pairs (`mediaType` and `filename`) together. The code to *get*
   the filename is very short and simple. It separates the *list* from the *action*.
2. **Easy to Change:**
    * Need to add a new media type? Just add one line to the dictionary list. You don't change the
      `GetFileNameDeclarative` method.
    * Need to change or remove a type? Just change or remove it from the list.
    * Changing `switch` or many `if` statements means changing the function's logic, which can cause mistakes.
3. **Follows Good Practice (see OPC):** The dictionary idea follows a rule called the Open/Closed Principle. This
   means the main function (`GetFileNameDeclarative`) is *closed* (you don't need to change it). But it's *open* to work
   with new types if you add them to the dictionary list.
4. **Can Use External Data:** The list of types and names in the dictionary could come from a file or database later.
   This makes the program flexible without changing the `GetFileNameDeclarative` function.
5. **Good Speed:** Looking for something in a dictionary is usually very fast (often called O(1) time), even if the list
   is very long. A long list of `if` checks gets slower as the list grows (O(N) time). For a small list like this, the
   speed difference is small, but the dictionary works better for large lists.

See Open Closed Principle (OCP), Imperative vs Functional (Declarative, Expressive) Style of Programming