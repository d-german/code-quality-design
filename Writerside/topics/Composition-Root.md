# Composition Root

Only one location in an application should have any knowledge of dependency injection: the composition root. This is
where classes are constructed when you are using Poor Man’s DI, or where interfaces and class mappings are registered
when you are using an Inversion of Control container.

The ideal is for the composition root to be as close to the entry point of the application as possible. This allows you
to bootstrap DI as soon as possible, gives you a recognized location to find the DI configuration, and helps you avoid
leaking dependencies on the container throughout the application.

McLean Hall, Gary