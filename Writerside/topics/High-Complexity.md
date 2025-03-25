# High Complexity

The following has a Cyclomatic Complexity value of 16 and a Cognitive Complexity value of 27.

```C#
/// <summary>
/// Converts a page range string into an array of page numbers. Values
/// are comma separated, and can be a combination of specific pages,
/// and ranges. Page numbers are 1-based.
/// </summary>
/// <param name="pattern">
/// The page range string, similar to that on a print dialog
/// </param>
/// <param name="pageCount">
/// The maximum number of pages available
/// </param>
/// <returns>
/// An array of page numbers to process
/// </returns>
private static IEnumerable<int> ResolvePages(
    string pattern,
    int pageCount) // mildly complex(60%) => very complex(72%)
{
    if (string.IsNullOrWhiteSpace(pattern))
        return Array.Empty<int>();

    var returnValue = new List<int>();

    foreach (var p in pattern.Split(','))
    {
        var r = p.Split('-')
            .Select(c => c.Trim())
            .ToArray();

        if (r.Length == 1 && int.TryParse(r[0], out var v))
        {
            if (v >= 1 && v <= pageCount)
            {
                returnValue.Add(v);
            }
        }
        else if (r.Length == 2 && string.Compare(r[0], r[1]) == 0)
        {
            if (int.TryParse(r[0], out var v2)
                && v2 >= 1 && v2 <= pageCount)
            {
                returnValue.Add(v2);
            }
        }
        else if (r.Length == 2)
        {
            if (int.TryParse(r[0], out var start)
                && int.TryParse(r[1], out var end))
            {
                start = Math.Max(1, Math.Min(start, pageCount));
                end = Math.Max(1, Math.Min(end, pageCount));

                if (start <= end)
                {
                    for (var x = start; x <= end; x++)
                    {
                        returnValue.Add(x);
                    }
                }
                else
                {
                    for (var x = start; x >= end; x--)
                    {
                        returnValue.Add(x);
                    }
                }
            }
        }
    }

    return returnValue;
}
```

See Cyclomatic Complexity and Cognitive Complexity.