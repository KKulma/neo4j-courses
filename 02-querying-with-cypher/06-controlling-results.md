In this module, we focus on controlling how results are processed in the RETURN and WITH clauses.

### Duplicated resutls 
In most cases, you want to eliminate duplicated results. You do so by using the DISTINCT keyword.

```shell
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
RETURN DISTINCT m.title, m.released
```

### Duplication in lists
You can also specify DISTINCT when collecting elements for a list. Here is another query where we collect the names of people who acted in, directed, or wrote movies released in 2003.

```shell
MATCH (p:Person)-[:ACTED_IN | DIRECTED | WROTE]->(m:Movie)
WHERE m.released = 2003
RETURN m.title, collect(p.name) AS credits
```

We can eliminate this duplication by calling DISTTINCT when collecting the results 

```shell
MATCH (p:Person)-[:ACTED_IN | DIRECTED | WROTE]->(m:Movie)
WHERE m.released = 2003
RETURN m.title, collect(DISTINCT p.name) AS credits
```

### Using WITH and DISTINCT to eliminate duplication
Another way that you can avoid duplication is to use WITH and DISTINCT together as follows:

```shell
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks'
WITH DISTINCT m
RETURN m.released, m.title
```


### Ordering results
If you want the results to be sorted, you specify the expression to use for the sort using the ORDER BY keyword and whether you want the order to be descending using the DESC keyword. Ascending order is the default.

In this example, we specify that the release date of the movies for Tom Hanks will be returned in descending order.

```shell
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks' OR p.name = 'Keanu Reeves'
RETURN DISTINCT m.title, m.released ORDER BY m.released DESC
```


### Ordering multiple results
Note that you can provide multiple sort expressions and the result will be sorted in that order. Here we want the rows to be sorted by the release date, descending, and then by title:

```shell
MATCH (p:Person)-[:DIRECTED | ACTED_IN]->(m:Movie)
WHERE p.name = 'Tom Hanks' OR p.name = 'Keanu Reeves'
RETURN DISTINCT m.title, m.released ORDER BY m.released DESC , m.title
```


### Limiting the number of results
Although you can filter queries to reduce the number of results returned, you may also want to limit the number of results returned. This is useful if you have very large result sets and you only need to see the beginning or end of a set of ordered results. You can use the LIMIT keyword to specify the number of results returned.
Suppose you want to see the titles of the ten most recently released movies. You could do so as follows where you limit the number of results using the LIMIT keyword as follows:

```shell
MATCH (m:Movie)
RETURN m.title as title, m.released as year ORDER BY m.released DESC LIMIT 10
```

### Limiting number of intermediate results
Furthermore, you can use the LIMIT keyword with the WITH clause to limit intermediate results. A best practice is to limit the number of rows processed before they are collected. Here is an example where we want to limit the number of actors collected in this query by the number 6:

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH m, p LIMIT 6
RETURN collect(p.name), m.title
```

### Another example using LIMIT
Here is another example of limiting results. Suppose we want to retrieve five movies and for each movie, return the :ACTED_IN path to at most two actors. Here is one way to perform this query:

```shell
MATCH (m:Movie)
WITH m LIMIT 5
MATCH path = (m)<-[:ACTED_IN]-(:Person)
WITH m, collect(path) AS paths
RETURN m, paths[0..2]
```

### Alternative to LIMIT
Another way that you can limit results is to collect or count them during the query and use the count to end the query processing. In this example, we count the number of movies during the query and we return the results once we have reached 5 movies. That is, the question we are asking is, "What actors acted in exactly five movies?".

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, count(*) AS numMovies, collect(m.title) AS movies
WHERE numMovies = 5
RETURN a.name, numMovies, movies
```
An alternative to the above code is:

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH a, collect(m.title) AS movies
WHERE size(movies) = 5
RETURN a.name, movies
```
