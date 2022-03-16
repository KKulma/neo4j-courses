### Example: Using WITH
During the execution of a MATCH clause, you can specify that you want some intermediate calculations or values that will be used for further processing of the query. You use the WITH clause to perform intermediate processing that is not possible in a RETURN clause.

Here is an example where we start the query processing by retrieving all actors and their movies. During the query processing, we want to only return actors that have 2 or 3 movies. All other actors and the aggregated results are filtered out. This type of query is a replacement for SQL’s "HAVING" clause. The WITH clause does the counting and collecting, and the intermediate result is used in the subsequent WHERE clause to test.

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a, count(m) AS numMovies, collect(m.title) as movies
WHERE 1 < numMovies < 4
RETURN a.name, numMovies, movies
```

> You have to name all expressions with an alias in a WITH that are existing variables.

### Using WITH and UNWIND
You have learned to create lists of nodes during a query using the collect() function. If you have collected a subset of nodes in a query, you can use UNWIND to return the rows for a collection.

```shell
MATCH (m:Movie)<-[:ACTED_IN]-(p:Person)
WITH collect(p) AS actors,
count(p) AS actorCount, m
UNWIND actors AS actor
RETURN m.title, actorCount, actor.name
```

The query retrieves all people who acted in movies. During the query, for each movie, the list of actors are collected, as well as the count of actors. The WITH clause makes the variables actors, actorCount, and m available for the rest of the query processing. The UNWIND clause turns the list of actors into rows of actors. Then the query returns the title of the movie, the actor count, and the actor name for each row of the actors collection.


### Example: Subqueries with WITH
Here is an example where we retrieve all movies reviewed by a person. For a particular movie found, we want the list of directors of the movie so we do a second query, a subquery as follows:

```shell
MATCH (m:Movie)<-[rv:REVIEWED]-(r:Person)
WITH m, rv, r
MATCH (m)<-[:DIRECTED]-(d:Person)
RETURN m.title, rv.rating, r.name, collect(d.name)
```

For the second MATCH clause, we use the found movie nodes, m. The RETURN clause has access to the movie, rating by that reviewer, the name of the reviewer, and the collection of director names for that movie.


####  Example: Another subquery
Here is another example where we want to find all actors who have acted in at least five movies, and find (optionally) the movies they directed and return the person and those movies.

```shell
MATCH (p:Person)
WITH p, size((p)-[:ACTED_IN]->()) AS movies
WHERE movies >= 5
OPTIONAL MATCH (p)-[:DIRECTED]->(m:Movie)
RETURN p.name, m.title
```
In this example, we first retrieve all people, but then specify a pattern in the WITH clause where we calculate the number of :ACTED_IN relationships retrieved using the size() function. If this value is greater than five, we then also retrieve the :DIRECTED paths to return the name of the person and the title of the movie they directed. In the result, we see that these actors acted in more than five movies, but Tom Hanks is the only actor who directed a movie and thus the only person to have a value for the movie. Notice here that m only refers to movies that were directed by p.

### Performing subqueries with CALL
Another way that you can perform a subquery is to use the CALL clause. In a CALL clause, you specify a query that returns, typically a set of nodes. The set of nodes returned in the CALL clause can be used for a subsequent query.

Here is an example:

```shell
CALL
{MATCH (p:Person)-[:REVIEWED]->(m:Movie)
RETURN  m}
MATCH (m) WHERE m.released=2000
RETURN m.title, m.released
```



#### exercises 
7.1: Retrieve the actors who have acted in exactly five movies, returning the name of the actor, and the list of movies for that actor.

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
WITH  a, count(m) AS numMovies, collect(m.title) AS movies
WHERE numMovies = 5
RETURN a.name, movies
```

7.3 Retrieve the movies that have at least 2 directors, and optionally the names of people who reviewed the movies.

official solution:
```shell
MATCH (m:Movie)
WITH m, size((:Person)-[:DIRECTED]->(m)) AS directors
WHERE directors >= 2
OPTIONAL MATCH (p:Person)-[:REVIEWED]->(m)
RETURN  m.title, p.name
```

my solution: 
```shell
MATCH (d:Person)-[:DIRECTED]->(m:Movie)
WITH  m, count(d) AS numDirectors, collect(d.name) AS directors
WHERE numDirectors >= 2
OPTIONAL MATCH (r:Person)-[:REVIEWED]-(m)
RETURN m.title, numDirectors, directors, collect(r.name) as reviewers
```


7.4 Write a Cypher query that retrieves all actors that acted in movies, and collects the list of movies for any actor that acted in more than five movies. Return the name of the actor and the list.
Hint: Use a WITH clause and test the size of the collected list using the size() function.


```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, collect(m) AS movies
WHERE size(movies)  > 5
RETURN p.name, movies
```


7.5 Modify the query you just wrote so that before the query processing ends, you unwind the list of movies and then return the name of the actor and the title of the associated movie

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WITH p, collect(m) AS movies
WHERE size(movies)  > 5
WITH p, movies UNWIND movies AS movie
RETURN p.name, movie.title
```