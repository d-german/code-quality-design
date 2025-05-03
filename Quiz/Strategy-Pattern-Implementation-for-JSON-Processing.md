**1. What problem with the original JSON converter did the Strategy pattern solve?**

- [ ] It improved runtime performance by caching parsed objects.
- [ ] It centralized logging and error handling for all JSON formats.
- [x] It eliminated rigid key-checking requiring code changes for new formats.
- [ ] It enforced compile-time type safety across JSON types.

**2. Which methods must a class implementing IJsonProcessor define?**

- [x] CanProcess(JObject json) and Process(JObject json).
- [ ] CanProcess(JObject json) and ReadJson(JsonReader reader,…).
- [ ] Process(JObject json) and RegisterServices(IServiceCollection).
- [ ] ReadJson(JsonReader reader,…) and RegisterServices(IServiceCollection).

**3. What is the effect of injecting IEnumerable<IJsonProcessor> into UserPreferencesService?**

- [ ] It throws an exception if more than one processor is registered.
- [ ] It caches handlers for reuse across requests.
- [ ] It binds only to the last-registered processor.
- [x] It supplies all registered JSON processors for runtime selection.

**4. Which sequence best describes ProcessJsonString’s workflow?**

- [ ] Parse JSON, throw if null, register processors, return default.
- [x] Check for null string, parse JSON, find matching processor, invoke Process.
- [ ] Create default object, parse JSON, log error, return new instance.
- [ ] Validate schema, transform to DTO, serialize back, return string.

**5. What are benefits of using the Strategy pattern in this JSON processing example?**

- [ ] Simplifies error handling by consolidating exceptions.
- [ ] Reduces dependency on external JSON libraries.
- [ ] Enforces a single JSON schema for all contexts.
- [x] All of the above.

**Open Response Questions**

**6. Describe how the Strategy pattern implementation adheres to the Open/Closed Principle.**

Bare minimum example response:  
It allows adding new IJsonProcessor implementations without modifying UserPreferencesService, keeping the service closed to change but open for extension.

**7. Explain the role of dependency injection in enabling extensible JSON processing.**

Bare minimum example response:  
Dependency injection provides all IJsonProcessor instances via IEnumerable, so the service can discover and use processors at runtime without knowing their concrete types.

**8. Compare the hard-coded converter approach to the Strategy pattern, noting two advantages of using strategies.**

Bare minimum example response:  
Hard-coded logic mixes all format checks in one class and requires code edits for new formats; strategies separate concerns into classes and let you register new formats without changing the core service.
