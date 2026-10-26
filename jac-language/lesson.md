# Lesson: Thinking and programming in Jac

## 1. Why graph-native programming?

Many application domains are fundamentally relational. A learner enrolls in courses, a package depends on libraries, a customer follows accounts, and a road joins places. A conventional program can represent these facts with tables, IDs, dictionaries, and repeated lookup code. Jac instead makes the graph a first-class part of the programming model.

A useful mental model is:

- Nodes hold entity state.
- Edges hold relationship state.
- Walkers hold short-lived computation state and move through the graph.

This does not remove the need for data modeling. It makes relationships visible in both the program structure and the execution model.

## 2. Main constructs

The exact syntax varies by Jac version, but a modern Jac program commonly uses declarations such as `node`, `edge`, `walker`, `ability`, and `with entry`.

```jac
node Person {
    has name: str;
}

edge Knows {
    has since: int;
}
```

Here, every `Person` node has a `name`; every `Knows` edge can record the year the relationship began. Use nodes for things with independent identity and edges for facts about a connection.

An ability expresses behavior associated with a construct:

```jac
node Person {
    has name: str;

    ability label -> str {
        return self.name;
    }
}
```

The receiver spelling and typing conventions should be checked against the documentation for the installed Jac release. The design point is stable: keep behavior near the graph object whose state it uses.

## 3. Entry points and ordinary computation

A `with entry` block provides code that runs when the module or program is invoked.

```jac
with entry {
    greeting = "Hello, Jac";
    print(greeting);
}
```

Use ordinary variables, expressions, conditionals, loops, and functions for non-graph work. Graph-native constructs are tools, not replacements for basic decomposition. A good program keeps pure calculations simple and introduces graph traversal only when relationships are central to the task.

## 4. Walkers: computation that moves

A walker represents an active process. It can store fields, react when it encounters graph elements, and decide where to continue. Consider a conceptual walker that gathers names:

```jac
walker CollectNames {
    has names: list[str] = [];

    can visit_person with Person entry {
        self.names.append(here.name);
        visit [-->];
    }
}
```

Read this as follows:

1. A `CollectNames` walker starts at some node.
2. When it enters a `Person`, it adds that person's name to its own list.
3. `visit [-->]` asks to continue across outgoing connections.

The precise event/ability syntax and edge-selection notation are release-dependent. The important semantic distinction is that `here` refers to the current graph location, while `self` refers to the walker and its carried state.

## 5. Traversal controls

Three recurring ideas help make walkers safe and understandable.

### Visit

`visit` schedules graph elements for traversal. Restrict it whenever possible: select a direction, edge type, or neighbor condition rather than expanding through every connection by default.

### Ignore

`ignore` marks elements that should not be processed by this walker. It is useful when an otherwise reachable node is irrelevant, forbidden, or already handled.

### Disengage

`disengage` ends the current traversal. Use it after finding a target, exceeding a budget, or detecting invalid state.

Conceptual search logic:

```jac
walker FindPerson {
    has target: str;
    has found: bool = False;

    can inspect with Person entry {
        if here.name == self.target {
            self.found = True;
            disengage;
        }
        visit [-->];
    }
}
```

A real implementation should also define how repeated nodes are handled. A cyclic graph can otherwise cause needless revisits or nontermination, depending on traversal semantics and the safeguards used.

## 6. Building a graph

Graph construction normally involves creating nodes and connecting them with edges. Pseudocode-like Jac illustrates the intent:

```jac
with entry {
    alice = Person(name="Alice");
    bob = Person(name="Bob");
    alice ++> Knows(since=2022) ++> bob;
}
```

The chained connection notation above communicates “connect Alice to Bob through a `Knows` edge.” Confirm constructor and connection syntax in your Jac version before running it.

Before writing code, answer these questions:

1. What counts as a node with its own identity?
2. What is a relationship, and which facts belong on that relationship?
3. Is direction meaningful?
4. What traversal questions must the application answer?
5. What stops a traversal?

## 7. State placement

Place long-lived domain data on nodes or edges. Place temporary request-specific data on walkers.

For example, in a route finder:

- `Station` node: name and coordinates.
- `Route` edge: distance, line, or travel time.
- `FindRoute` walker: destination, current cost, predecessor map, and a search limit.

Putting route-search state on nodes may accidentally leak one request's state into another. Putting durable relationship metadata on a walker makes the data vanish when the walker finishes. This distinction is a major practical benefit of the model.

## 8. A disciplined development workflow

1. Draw a tiny graph on paper.
2. Declare the smallest node and edge types that represent it.
3. Write an entry block that creates a two- or three-node example.
4. Create one walker whose output can be checked by hand.
5. Add filters and termination conditions.
6. Test disconnected graphs, cycles, and missing properties.
7. Only then introduce persistence, APIs, concurrency, or large datasets.

## 9. Common pitfalls

- **Using a graph where a list is enough:** graph machinery has a cost. Use it when connections and navigation matter.
- **Unbounded expansion:** traversing every neighbor without depth, count, or target controls can be expensive.
- **Confusing walker state with node state:** `self` and the current location serve different roles.
- **Ignoring cycles:** social, dependency, and recommendation graphs often contain them.
- **Treating conceptual syntax as version-proof:** always validate syntax and runtime behavior against the official docs for the installed Jac release.
