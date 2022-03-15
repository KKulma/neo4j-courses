### Specify multiple MATCH patterns.

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie),
      (m)<-[:DIRECTED]-(d:Person)
WHERE m.released = 2000
RETURN a.name, m.title, d.name
```

It returns all Person nodes for people who acted in these three movies and using that same movie node, m it retrieves the Person node who is the director for that movie, m.

### Specifying a single pattern

However, a better way to write this same query would be:

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
WHERE m.released = 2000
RETURN a.name, m.title, d.name
```

There are, however, some queries where you will need to specify two or more patterns. Multiple patterns are used when a query is complex and cannot be satisfied with a single pattern. This is useful when you are looking for a specific node in the graph and want to connect it to a different node.

### Example: Using two patterns in a MATCH

```shell
MATCH (keanu:Person)-[:ACTED_IN]->(movie:Movie)<-[:ACTED_IN]-(n:Person),
     (hugo:Person)
WHERE keanu.name='Keanu Reeves' AND
      hugo.name='Hugo Weaving'
AND NOT (hugo)-[:ACTED_IN]->(movie)
RETURN n.name
```

When you perform this type of query, you may see a warning in the query edit pane stating that the pattern represents a cartesian product and may require a lot of resources to perform the query. You only perform these types of queries if you know the data well and the implications of doing the query.

Suppose we want to retrieve the movies that Meg Ryan acted in and their respective directors, as well as the other actors that acted in these movies. Here is the query to do this:
```shell
MATCH (meg:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person),
      (other:Person)-[:ACTED_IN]->(m)
WHERE meg.name = 'Meg Ryan'
RETURN m.title as movie, d.name AS director , other.name AS `co-actors`
```

An important thing to understand about multiple patterns in a single MATCH statement is that the query processor will never traverse a relationship more than once. That is why the Meg Ryan node is not retrieved in the other node retrievals. All other nodes of people who acted in that same movie are retrieved.

Traversal with patterns
During a query, you want to minimize the number of paths traversed. In some cases, however, you can only retrieve the nodes, relationships, or paths of interest using multiple patterns or even multiple MATCH clauses.

Here is an example query where multiple MATCH clauses are used:

```shell
MATCH (valKilmer:Person)-[:ACTED_IN]->(m:Movie)
MATCH (actor:Person)-[:ACTED_IN]->(m)
WHERE valKilmer.name = 'Val Kilmer'
RETURN m.title as movie , actor.name
```


### Specifying varying length paths
You write a MATCH clause where you want to find all of the followers of the followers of a Person by specifying a numeric value for the number of hops in the path. Here is an example where we want to retrieve all Person nodes that are exactly two hops away:

```shell
MATCH (follower:Person)-[:FOLLOWS*2]->(p:Person)
WHERE follower.name = 'Paul Blythe'
RETURN p.name
```

If we had specified `[:FOLLOWS*]` rather than `[:FOLLOWS*2]`, the query would return all Person nodes that are in the :FOLLOWS path from Paul Blythe.

Retrieve the paths of length 3 with the relationship, :RELTYPE from nodeA to nodeB:

```shell
(nodeA)-[:RELTYPE*3]->(nodeB)
```

Retrieve the paths of lengths 1, 2, or 3 with the relationship, :RELTYPE from nodeA to nodeB, nodeB to nodeC, as well as, nodeC to nodeD) (up to three hops):

```shell
(nodeA)-[:RELTYPE*1..3]->(nodeB)
```


### Finding the shortest path
A built-in function that you may find useful in a graph that has many ways of traversing the graph to get to the same node is the `shortestPath()` function. Using the shortest path between two nodes improves the performance of the query.
notice that we specify * for the relationship. This means any relationship; for the traversal.
```shell
MATCH p = shortestPath((m1:Movie)-[*]-(m2:Movie))
WHERE m1.title = 'A Few Good Men' AND
      m2.title = 'The Matrix'
RETURN  p
```

### Returning a subgraph
In using shortestPath(), the return type is a path. A subgraph is essentially a set of paths derived from your MATCH clause.

For example, here is an example where we want a subgraph of all nodes connected to the movie, The Replacements:

```shell
MATCH paths = (m:Movie)--(p:Person)
WHERE m.title = 'The Replacements'
RETURN paths
```


### Specifying optional pattern matching
OPTIONAL MATCH matches patterns with your graph, just like MATCH does. The difference is that if no matches are found, OPTIONAL MATCH will use nulls for missing parts of the pattern. OPTIONAL MATCH could be considered the Cypher equivalent of the outer join in SQL.

Here is a subgraph of our movies graph with all people named James and their relationships:

Here is an example where we query the graph for all people whose name starts with James. The OPTIONAL MATCH is specified to include people who have reviewed movies:

```shell
MATCH (p:Person)
WHERE p.name STARTS WITH 'James'
OPTIONAL MATCH (p)-[r:REVIEWED]->(m:Movie)
RETURN p.name, type(r), m.title
```