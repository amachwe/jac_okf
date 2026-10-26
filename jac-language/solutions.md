# Solutions

These are model answers. Other designs can be correct when their assumptions are explicit and their traversal behavior is safe.

## 1. Model a study group

A suitable node is `Learner`, with properties such as `name`, `program`, or `year`. A suitable edge is `CollaboratesWith`, with properties such as `project`, `last_met`, or a collaboration-strength score.

If collaboration means a symmetric social fact, the model may use an undirected relationship if the runtime supports it, or two directed edges maintained together. If it represents an action such as “asks for help from,” direction matters and a directed edge is preferable.

## 2. State placement

- Course title: node state.
- Prerequisite relationship: edge state or simply the edge type.
- Maximum allowed path length: walker state.
- Current path: walker state.
- Instructor office hours: node state if instructors are graph entities; otherwise external/application state.

The maximum length belongs to the walker because it is a rule for one search, not an enduring property of a course. The current path also belongs to the walker because concurrent searches may explore different paths at the same time.

## 3. Read a traversal

The walker checks each entered `Person`. `self.target` is data carried by the walker: the requested name. `here.name` is the name of the current `Person` node. When the names match, the walker prints the name and stops its traversal; otherwise, it requests traversal of outgoing neighbors.

Additional safeguards include a visited set or a runtime-supported no-revisit policy for cycles, a maximum depth or node budget, filtering to the intended edge type, and a defined result for “not found.” Without limits, highly connected graphs can produce excessive work even if they eventually terminate.

## 4. Design a safe neighborhood walker

One conceptual design stores a queue of `(node, depth)` pairs and a `seen` set keyed by stable node identity. Initialize the queue with the start node at depth zero and mark it seen. Repeatedly remove one pair:

```text
while queue is not empty:
    current, depth = queue.pop_front()
    if depth == 2:
        continue
    for neighbor in outgoing Knows neighbors of current:
        if neighbor.id not in seen:
            seen.add(neighbor.id)
            names.append(neighbor.name)
            queue.push_back((neighbor, depth + 1))
```

A Jac walker may encode equivalent state in walker fields and use `visit` to schedule selected neighbors. The design terminates because each reachable node is scheduled at most once, and only nodes through depth two are expanded. If the start person should not appear in results, do not append the depth-zero node.

## 5. Edges versus nodes

`Rating` is naturally an edge from `Listener` to `Album`, because it describes the relationship between exactly those two entities. Score, date, and review text fit well as edge properties.

A separate `Rating` node becomes attractive if ratings need their own relationships or identity: for example, many users can comment on a rating, a rating can be revised through a version history, or moderation workflows and attachments relate directly to a rating.

## 6. Debugging scenario

A sound plan is to:

1. Verify that the starting node and target comparison use the expected fields and casing.
2. Log or count each node entry and each scheduled neighbor to confirm that the intended edge direction and type are used.
3. Add a visited-node policy; mutual friendships create cycles.
4. Add a maximum depth, node count, or time budget and return a clear “not found within budget” result.
5. Stop immediately with `disengage` once Nia is found.
6. Measure branching factor and restrict traversal to `Friend` edges or eligible users; unrestricted expansion is the main performance risk.

The correctness concern is missing Nia due to a wrong direction, filter, or comparison. The performance concern is repeatedly expanding cycles or very high-degree nodes.

## 7. Mini-design

Use a `Package` node with `name`, `version`, and `license` properties. Use a directed `DependsOn` edge with properties such as `version_constraint`, `scope`, and `optional`.

A `ResolveDependencies` walker can start at a requested package and collect reachable dependencies while reporting incompatible version constraints. Its cycle policy can maintain a recursion-stack set: encountering a package already on the active path reports a dependency cycle rather than recursively expanding it. A separate visited set avoids repeating completed subgraphs.
