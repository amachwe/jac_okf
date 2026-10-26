# Jac Language: Graph-Native Programming Fundamentals

## Audience and prerequisites

This bundle is for programmers who know the basics of variables, functions, conditionals, and collections in at least one modern programming language. No prior graph-theory course is required, but learners should be willing to reason about relationships as well as individual objects.

Jac is a graph-native programming language: programs can model entities as **nodes**, relationships as **edges**, and mobile computations as **walkers**. This style is often called object-spatial programming because behavior can be expressed in terms of where computation is in a graph and how it moves through that graph.

> **Version note:** Jac evolves actively. Exact command-line commands, standard-library names, and some surface syntax can differ by installed release. Treat the examples as conceptual Jac-style code, then verify details against the documentation for your version.

## Learning objectives

After completing this bundle, a learner can:

1. Describe what nodes, edges, and walkers represent in a Jac application.
2. Declare simple typed graph objects and attach behavior with abilities.
3. Read the intent of walker traversal code using `visit`, `ignore`, and `disengage`.
4. Design a small graph-backed feature such as a social network or task dependency map.
5. Identify termination, duplication, and state-management concerns in graph traversal.

## Suggested sequence

1. Read `lesson.md` section by section and type the short examples into a local Jac environment.
2. Sketch the graph for each example before predicting its output.
3. Complete `exercises.md` without opening the solutions.
4. Compare your work to `solutions.md`, then consult the official materials in `references.md` for release-specific syntax.

## Core vocabulary

- **Node:** an entity or place in a graph, such as a user, task, document, or station.
- **Edge:** a relationship or connection between nodes, possibly with its own data.
- **Walker:** an active computation that traverses graph connections and can carry state.
- **Ability:** named behavior attached to a Jac construct.
- **Traversal:** controlled movement through connected graph elements.

The key design shift is simple: rather than repeatedly looking up related records and manually coordinating loops, model relationships explicitly and let a walker perform local work while navigating those relationships.
