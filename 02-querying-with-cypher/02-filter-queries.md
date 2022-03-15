### Testing equality
Previously, you learned to write simple query as follows:

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie {released: 2008})
RETURN p, m
```
Here is a way you specify the same query using the `WHERE` clause:

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008
RETURN p, m
```


### Testing multiple values

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE m.released = 2008 OR m.released = 2009
RETURN p, m
```

You can specify conditions in a WHERE clause that return a value of true or false (for example predicates). For testing numeric values, you use the standard numeric comparison operators. Each condition can be combined for runtime evaluation using the boolean operators AND, OR, XOR, and NOT. There are a number of numeric functions you can use in your conditions. See the Neo4j Cypher Manual’s section Mathematical Functions for more information.


### Testing labels
Thus far, you have used the node labels to filter queries in a MATCH clause. You can filter node labels in the WHERE clause also:

For example, these two Cypher query:

```shell
MATCH (p:Person)-[:ACTED_IN]->(:Movie {title: 'The Matrix'})
RETURN p.name
```

can be rewritten using WHERE clauses as follows:

```shell
MATCH (p)-[:ACTED_IN]->(m)
WHERE p:Person AND m:Movie AND m.title='The Matrix'
RETURN p.name
```

### Testing existence of a property
Recall that a property is associated with a particular node or relationship. A property is not associated with a node with a particular label or relationship type. In one of our queries earlier, we saw that the movie "Something’s Gotta Give" is the only movie in the Movie database that does not have a tagline property. Suppose we only want to return the movies that the actor, Jack Nicholson acted in with the condition that they must all have a tagline.

Here is the query to retrieve the specified movies where we test the existence of the tagline property:

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name='Jack Nicholson' AND exists(m.tagline)
RETURN m.title, m.tagline
```

### Testing strings
Cypher has a set of string-related keywords that you can use in your WHERE clauses to test string property values. You can specify `STARTS WITH`, `ENDS WITH`, and `CONTAINS`.

For example, to find all actors in the Movie database whose first name is Michael, you would write:

```shell
MATCH (p:Person)-[:ACTED_IN]->()
WHERE p.name STARTS WITH 'Michael'
RETURN p.name
```

### String comparisons are case-sensitive
Note that the comparison of strings is case-sensitive. There are a number of string-related functions you can use to further test strings. For example, if you want to test a value, regardless of its case, you could call the `toLower()` function to convert the string to lower case before it is compared.

```shell
MATCH (p:Person)-[:ACTED_IN]->()
WHERE toLower(p.name) STARTS WITH 'michael'
RETURN p.name
```


### Testing with regular expressions
If you prefer, you can test property values using regular expressions. You use the syntax `=~` to specify the regular expression you are testing with. Here is an example where we test the name of the Person using a regular expression to retrieve all Person nodes with a name property that begins with 'Tom':

```shell
MATCH (p:Person)
WHERE p.name =~'Tom.*'
RETURN p.name
```


### Example: Testing with patterns

Here is an example where we want to return all Person nodes of people who wrote movies:

```shell
MATCH (p:Person)-[:WROTE]->(m:Movie)
RETURN p.name, m.title
```

Next, we modify this query to exclude people who also directed that movie:

```shell
MATCH (p:Person)-[:WROTE]->(m:Movie)
WHERE NOT exists( (p)-[:DIRECTED]->(m) )
RETURN p.name, m.title
```


Here is another example where we want to find Gene Hackman and the movies that he acted in with another person who also directed the movie.

```shell
MATCH (gene:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(other:Person)
WHERE gene.name= 'Gene Hackman'
AND exists( (other)-[:DIRECTED]->(m) )
RETURN  gene, other, m
```


This could also be written as follows: 

```shell
MATCH (gene:Person)-[:ACTED_IN]->(m:Movie)<-[:ACTED_IN]-(other:Person)
WHERE(other)-[:DIRECTED]->(m)
AND gene.name= 'Gene Hackman'
RETURN  gene, other, m
```


### Testing with list values

```shell
MATCH (p:Person)
WHERE p.born IN [1965, 1970]
RETURN p.name as name, p.born as yearBorn
```

### Testing list values in the graph

```shell
MATCH (p:Person)-[r:ACTED_IN]->(m:Movie)
WHERE  'Neo' IN r.roles AND m.title='The Matrix'
RETURN p.name
```

EXERCISES: In the query edit pane of Neo4j Browser, execute the browser command:
`:play 4.0-intro-neo4j-exercises`