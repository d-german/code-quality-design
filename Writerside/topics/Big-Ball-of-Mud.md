# Big Ball of Mud

A **Big Ball of Mud** is a haphazardly structured, sprawling, sloppy, duct-tape-and-baling-wire, spaghetti-code jungle.
These systems show unmistakable signs of unregulated growth and repeated, expedient repair. Information is shared
promiscuously among distant elements of the system, often to the point where nearly all the important information
becomes global or duplicated. The overall structure of the system may never have been well defined.

Unfortunately, this architecture anti-pattern occurs quite commonly in the real world. Few designers intend to create
one, but many projects inadvertently manage to create a mess because of lack of governance around code quality and
structure.

For example, Neal worked with a client project whose structure appears here:

<img alt="Architecture Coupling Visualization" src="big-ball-of-mud.png" width="512"/>

The client (whose name is withheld for obvious reasons) created a Java-based web application as quickly as possible over
several years. The technical visualization shows their architectural coupling: each dot on the perimeter of the circle
represents a class, and each line represents connections between the classes, where bolder lines indicate stronger
connections. In this code base, any change to a class makes it difficult to predict rippling side effects to other
classes, making change a terrifying affair.

---
See Also:

- [Coupling](Coupling.md)
- [Cohesion](Cohesion.md)
- [Modularity, Module](Modularity-Module.md)
- [Motivation for Good Design](Motivation-for-Good-Design.md)
- [The Dependency Rule](The-Dependency-Rule.md)