# Minimize Accessibility of Classes and Members

Minimizing accessibility is one of several rules that encourage encapsulation. If you’re wondering whether something
should be public, internal, private, or protected, one school of thought is that you should favor the strictest level of
privacy that’s workable. I (McConnell) think that’s a fine guideline, but I think the more important guideline is, “What
best preserves the integrity of the interface abstraction?” If exposing the item is consistent with the abstraction,
it’s probably fine to expose it. If you’re not sure, hiding more is better than hiding less. (McConnell 139)

---
See Also:
- [Pillars of OOP Encapsulation Abstraction](Pillars-of-OOP-Encapsulation-Abstraction.md)
- [Some Object-Oriented Design Heuristics - Riel updated](Some-Object-Oriented-Design-Heuristics-Riel-updated.md) (Heuristics 2.1, 2.5, 2.6, 5.3)