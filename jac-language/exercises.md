# Exercises

Use the Jac documentation for your installed version when turning conceptual snippets into executable source. State any syntax assumptions you make.

## 1. Model a study group

A study group has learners and pairwise collaboration relationships.

1. Identify a node type and an edge type.
2. List two properties that belong on the learner node.
3. List two properties that belong on the collaboration edge.
4. Explain whether the edge should be directed, undirected, or represented as two directed edges.

## 2. State placement

For a walker that searches for a course prerequisite path, classify each item as node state, edge state, walker state, or external/application state:

- course title
- prerequisite relationship
- maximum allowed path length for this search
- current path being explored
- instructor's office hours

Justify two of your classifications.

## 3. Read a traversal

Interpret this conceptual code in plain language:

```jac
walker FindTarget {
    has target: str;

    can inspect with Person entry {
        if here.name == self.target {
            print(here.name);
            disengage;
        }
        visit [-->];
    }
}
```

What is `self.target`? What is `here.name`? Give two reasons this walker needs additional safeguards before use on a large social graph.

## 4. Design a safe neighborhood walker

Write pseudocode or version-correct Jac for a walker that starts at a `Person` and returns names of people reachable within at most two outgoing `Knows` relationships. Your design must specify:

1. how depth is tracked;
2. how duplicate visits are prevented or tolerated;
3. why it terminates.

## 5. Edges versus nodes

A music service records that a listener rated an album with a score, date, and optional review text. Should `Rating` be an edge, a node, or either? Defend your answer. Describe one requirement that would make a separate `Rating` node more attractive.

## 6. Debugging scenario

A walker intended to find a user named `Nia` runs for a long time in a graph with mutual friendships. Provide a debugging plan with at least four concrete checks or changes. Include one correctness concern and one performance concern.

## 7. Mini-design

Sketch a graph-native design for package dependencies. Include node and edge types, three properties, a walker goal, and one cycle-related policy.
