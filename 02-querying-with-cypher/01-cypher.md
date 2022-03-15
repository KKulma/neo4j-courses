Cypher is a declarative query language that allows for expressive and efficient querying and updating of graph data. Cypher is a relatively simple and very powerful language.

Optimized for being read by humans, Cypher’s construct uses English prose and iconography (called ASCII Art) to make queries more self-explanatory.

**Cypher expresses what to retrieve, not how to retrieve it***
 You can think of Cypher as mapping English language sentence structure to patterns in a graph. For example, the nouns are nodes of the graph, the verbs are the relationships in the graph, and the adjectives and adverbs are the properties.

### Nodes
Cypher uses a pair of parentheses like `()`, `(n)` to represent a node, much like a circle on a whiteboard.
When you specify (n) for a node, you are telling the query processor that for this query, use the variable n to represent nodes that will be processed later in the query for further query processing or for returning values from the query. If you do not need to do anything with the node, you can skip the use of the variable. This is called an anonymous node.


### Labels
Here are examples for specifying nodes with labels for nodes with variables and also annonymous nodes without variables:
~~~~sql
(:Person)
(p:Person)
(:Location)
(l:Location)
(x:Residence)
(x:Location:Residence)
~~~~

A node can be retrieved using one or more Labels ma

```shell
// anonymous node not be referenced later in the query
()
// variable p, a reference to a node used later
(p)
// anonymous node of type Person
(:Person)
// p, a reference to a node of type Person
(p:Person)
// p, a reference to a node of types Actor and Director
(p:Actor:Director)
```

### Examining the data model
When you are first learning about the data (nodes, labels, etc.) in a graph, it is helpful to examine the data model of the graph. You do so by executing `CALL db.schema.visualization()`, which calls the Neo4j procedure that returns information about the nodes, labels, and relationships in the graph.


### Syntax: Using MATCH and RETURN
The MATCH clause performs a pattern match against the data in the graph. During the query processing, the graph engine traverses the graph to find all nodes that match the graph pattern. As part of query, you can return nodes or data from the nodes using the RETURN clause. The RETURN clause must be the last clause of a query to the graph

```shell
MATCH (p:Person) 	// returns all Person nodes in the graph
RETURN p
```
When we execute the Cypher statement, MATCH (p:Person) RETURN p, the graph engine returns all nodes with the label Person.
The default view of the returned nodes are the nodes that were referenced by the variable p.


#### Properties 
In Neo4j, a node (and a relationship, which you will learn about later) can have properties that are used to further define a node. A property is identified by its property key. Recall that nodes are used to represent the entities of your business model. A property is defined for a node and not for a type of node. **All nodes of the same type need not have the same properties.**


**Examining property keys**
As you prepare to create Cypher queries that use property values to filter a query, you can view the values for property keys of a graph by simply clicking the Database icon in Neo4j Browser. Alternatively, you can execute `CALL db.propertyKeys()`, which calls the Neo4j library method that returns the property keys for the graph.

### Syntax: Retrieving nodes filtered by a property value

```shell
MATCH (variable {propertyKey: propertyValue})
RETURN variable
```

```shell
MATCH (variable:Label {propertyKey: propertyValue, propertyKey2: propertyValue2})
RETURN variable
```


**Example: Filtering query by year born**
```shell
MATCH (p:Person {born: 1970})
RETURN p
```

```shell
MATCH (m:Movie {released: 2003, tagline: 'Free your mind'})
RETURN m
```


### Syntax: Returning property values
You can use the RETURN clause to return property values of nodes retrieved.

```shell
MATCH (variable:Label {prop1: value, prop1: value})
RETURN variable.prop2, variable.prop3
```

```shell
MATCH (p:Person {born: 1965})
RETURN p.name, p.born
```


### Syntax: Specifying aliases for column headings
If you want to customize the headings for a table containing property values, you can specify aliases for column headers.

```shell
MATCH (variable:Label {propertyKey1: propertyValue1})
RETURN variable.propertyKey2 AS alias2
```

Example
```shell
MATCH (p:Person {born: 1965})
RETURN p.name AS name, p.born AS `birth year`
```

### ASCII art
Thus far, you have learned how to specify a node in a MATCH clause. You can specify nodes and their relationships to traverse the graph and quickly find the data of interest.
Here is how Cypher uses ASCII art to specify the path used for a query:

```shell
()          // a node
()--()      // 2 nodes have some type of relationship
()-[]-()    // 2 nodes have some type of relationship
()-->()     // the first node has a relationship to the second node
()<--()     // the second node has a relationship to the first node
```

### Syntax: Querying using relationships
In your MATCH clause, you specify how you want a relationship to be used to perform the query. The relationship can be specified with or without direction.

Here are simplified syntax examples for retrieving a set of nodes that satisfy one or more directed and typed relationships:

```shell
MATCH (node1)-[:REL_TYPEA | REL_TYPEB]->(node2)
RETURN node1, node2
```

Where `REL_TYPEA` , `REL_TYPEB` are the relationships from node1 to node2. The nodes are returned if at least one of the relationships exists.

**Example**
```shell
MATCH (p:Person)-[rel:ACTED_IN]->(m:Movie {title: 'The Matrix'})
RETURN p, rel, m
```


**Important:** You specify node labels whenever possible in your queries as it optimizes the retrieval in the graph engine. That is, you must not specify the previous query as:

```shell
MATCH (p)-[rel:ACTED_IN]->(m {title:'The Matrix'})
RETURN p,m
```

### Using anonymous nodes in a query
Suppose you wanted to retrieve the actors that acted in The Matrix, but you do not need any information returned about the Movie node. You need not specify a variable for a node in a query if that node is not returned or used for later processing in the query. You can simply use the anonymous node in the query as follows:

```
MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: 'The Matrix'})
RETURN p.name
```

> A best practice is to place named nodes (those with variables) before anonymous nodes in a MATCH clause.

### Using an anonymous relationship for a query
Suppose you want to find all people who are in any way connected to the movie, The Matrix. You can specify an empty relationship type in the query so that all relationships are traversed and the appropriate results are returned. In this example, we want to retrieve all Person nodes that have any type of connection to the Movie node, with the title, The Matrix. This query returns more nodes with the relationships types, DIRECTED, ACTED_IN, and PRODUCED.

```shell
MATCH (p:Person)-->(m:Movie {title: 'The Matrix'})
RETURN p, m
```

More exmaples of anonymous relationship queries: 

```shell
Copy to Clipboard
MATCH (p:Person)--(m:Movie {title: 'The Matrix'})
RETURN p, m
```

``` 
MATCH (p:Person)-[]-(m:Movie {title: 'The Matrix'})
RETURN p, m
```

```
MATCH (m:Movie)<--(p:Person {name: 'Keanu Reeves'})
RETURN p, m
```


### Retrieving the relationship types
There is a built-in function, `type()` that returns the type of a relationship.

```shell
MATCH (p:Person)-[rel]->(:Movie {title:'The Matrix'})
RETURN p.name, type(rel)
```

### Filtering using relationship properties
Just as you can specify property values for filtering nodes for a query, you can specify property values for a relationship. This query returns the name of the person who gave the movie a rating of 65.

```shell
MATCH (p:Person)-[:REVIEWED {rating: 65}]->(:Movie {title: 'The Da Vinci Code'})
RETURN p.name
```


### Using patterns for queries
We can perform a query that returns all Person nodes who follow Angela Scope:

```shell
MATCH  (p:Person)-[:FOLLOWS]->(:Person {name:'Angela Scope'})
RETURN p
```
For this query the Person node for Angela Scope is the anchor of the query. It is the first node that is retrieved from the graph. Then the query engine looks for all relationships into this node and retrieves them. In this case there is only one relationship that is defined that points to the Angela Scope node, Paul Blythe.


**Reversing the traversal**
If we reverse the direction in the pattern, the query returns different results:

```shell
MATCH  (p:Person)<-[:FOLLOWS]-(:Person {name:'Angela Scope'})
RETURN p
```

**Querying a relationship in both directions**
We can also find out what Person nodes are connected by the FOLLOWS relationship in either direction by removing the directional arrow from the pattern.

```shell
MATCH  (p1:Person)-[:FOLLOWS]-(p2:Person {name:'Angela Scope'})
RETURN p1, p2
```


**Traversing multiple relationships**
Since we have a graph, we can traverse through nodes to obtain relationships further into the traversal.

For example, we can write a Cypher query to return all followers of the followers of Jessica Thompson.

```shell
MATCH  (p:Person)-[:FOLLOWS]->(:Person)-[:FOLLOWS]->(:Person {name:'Jessica Thompson'})
RETURN p
```

**Variation on the traversal**
This query could also be modified to return each person along the matched path by specifying variables for the nodes and returning them. For example:

```shell
MATCH  (p:Person)-[:FOLLOWS]->(p2:Person)-[:FOLLOWS]->(p3:Person {name:'Jessica Thompson'})
RETURN p.name, p2.name, p3.name
```


### Using patterns to focus the query
As you gain more experience with Cypher, you will see how patterns in your queries enable you to focus on the relationships in the graph. For example, suppose we want to retrieve all unique relationships between an actor, a movie, and a director. This query will return many unique rows of information that provide this pattern in the graph:

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
RETURN a.name, m.title, d.name
```


### Returning paths
In addition, you can assign a variable to the path and return the path as follows:

```shell
MATCH  path = (:Person)-[:FOLLOWS]->(:Person)-[:FOLLOWS]->(:Person {name:'Jessica Thompson'})
RETURN  path
```

### Returning multiple paths
Here is another example where multiple paths are returned. The query is to return all paths from actors to a movie that was directed by Ron Howard

```
MATCH  path = (:Person)-[:ACTED_IN]->(:Movie)<-[:DIRECTED]-(:Person {name:'Ron Howard'})
RETURN  path
```

Multiple paths are returned. **Even if we set Neo4j Browser to not connect result nodes, the nodes are shown as connected in the visualization because we are returning paths, not nodes.**



### Cypher style recommendations
Here are the Neo4j-recommended Cypher coding standards that we use in this training:

- Node labels are CamelCase and begin with an upper-case letter (examples: Person, NetworkAddress). Note that node labels are case-sensitive. 
- Property keys, variables, parameters, aliases, and functions are camelCase and begin with a lower-case letter (examples: businessAddress, title). Note that these elements are case-sensitive. 
- Relationship types are in upper-case and can use the underscore. (examples: ACTED_IN, FOLLOWS). Note that relationship types are case-sensitive and that you cannot use the "-" character in a relationship type. 
- Cypher keywords are upper-case (examples: MATCH, RETURN). Note that Cypher keywords are case-insensitive, but a best practice is to use upper-case. 
- String constants are in single quotes, unless the string contains a quote or apostrophe (examples: 'The Matrix', "Something’s Gotta Give"). Note that you can also escape single or double quotes within strings that are quoted with the same using a backslash character. 
- Specify variables only when needed for use later in the Cypher statement. 
- Place named nodes and relationships (that use variables) before anonymous nodes and relationships in your MATCH clauses when possible. 
- Specify anonymous relationships with `-->`, `--`, or `<--`.
