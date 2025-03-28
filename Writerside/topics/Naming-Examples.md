# Naming Examples

```C#
public IEnumerable<ConfigurationSetting>
GetConfigurationSettings(string[] key)
{
    IEnumerable<ConfigurationSetting> ConfigurationSettings =
    _configurationRepos ...
    if (ConfigurationSettings != null)
    {
        ...
    }
    ...
}

```
---
See Also:
- [Code Review Checklist](Code-Review-Checklist.md) (mentions naming/style)
- [ETC Principle](ETC-Principle.md) (mentions naming importance)