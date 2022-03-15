### Using count() to aggregate

A common way to aggregate data in Cypher is to count. Cypher has a count() function that you can use to perform a count of nodes, relationships, paths, rows during query processing. When you aggregate in a Cypher query, this means that the query must process all patterns in the MATCH clause to complete the aggregation to either return results or perform the next part of the query.

Here is an example:

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(d:Person)
RETURN a.name, d.name, count(m)
```
Aggregation in Cypher is different from aggregation in SQL. In Cypher, you need not specify a grouping key. As soon as an aggregation function is used, all non-aggregated result columns become grouping keys. The grouping is implicitly done, based upon the fields in the RETURN clause.

### Collecting results
Cypher has a built-in function, collect() that enables you to aggregate a value into a list. The value can be a property value, a node, a relationship, or a path.

Here is an example where we collect the list of movie titles that Tom Cruise acted in:

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name ='Tom Cruise'
RETURN collect(m.title) AS `movies for Tom Cruise`
```


### Collecting nodes
As you gain more experience with Cypher queries, you will learn to collect nodes, in addition to string values. For example, rather than collecting the values of the title properties for all movies that Tom Cruise acted in, we can collect the nodes. For this simple query, it is the same as returning m, but for more complex queries, you will find that collecting nodes and using them for a later step of the query is useful.

```shell
MATCH (p:Person)-[:ACTED_IN]->(m:Movie)
WHERE p.name ='Tom Cruise'
RETURN collect(m) AS `movies for Tom Cruise`
```


### Counting and collecting
The Cypher count() function is very useful when you want to count the number of occurrences of a particular query result. If you specify count(n), the graph engine calculates the number of occurrences of n. If you specify count(*), the graph engine calculates the number of rows retrieved, including those with null values. When you use count(), the graph engine does an implicit "group by" based upon the aggregation.

Here is an example where we count the paths retrieved where an actor and director collaborated in a movie and the count() function is used to count the number of paths found for each actor/director collaboration.

```
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(director:Person)
RETURN actor.name, director.name,
       count(m) AS collaborations, collect(m.title) AS movies
```


### Using collect() and size()
There are more aggregating functions such as min() or max() that you can also use in your queries. These are described in the Aggregating Functions section of the Neo4j Cypher Manual.

You can either use count() to count the number of rows, or alternatively, you can return the size of the collected results. The size() function returns the number of elements in a list.

**`count()` is more efficient because it gets its values from the internal count store of the graph!!**

```shell
MATCH (actor:Person)-[:ACTED_IN]->(m:Movie)<-[:DIRECTED]-(director:Person)
RETURN actor.name, director.name, size(collect(m)) AS collaborations,
       collect(m.title) AS movies
```


### Lists
There are many built-in Cypher functions that you can use to build or access elements in lists.

Here we return the cast list for every movie, as well as the size of the cast:

```
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN m.title, collect(a) as cast, size(collect(a)) as castSize
```

Notice that when viewing nodes in table view, each node is shown with {} notation and key value pairs. This structure is called a **map** in Cypher.


#### Using strings in lists
We can adjust this query slightly so that the list contains the names, rather than the entire set of Person node properties.

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN m.title, collect(a.name) as cast, size(collect(a.name)) as castSize
```

#### Accessing elements of the list
You can access particular elements of the list using the [index-value] notation where a list begins with index 0.

In this example we return the first cast member for each movie.

```shell
MATCH (a:Person)-[:ACTED_IN]->(m:Movie)
RETURN m.title, collect(a.name)[0] as `A cast member`,
       size(collect(a.name)) as castSize
```


#### Working with maps
A Cypher map is like Python dictionary:  list of key/value pairs where each element of the list is of the format 'key': value. For example, a map of months and the number of days per month could be:

```shell
RETURN {Jan: 31, Feb: 28, Mar: 31, Apr: 30 , May: 31, Jun: 30 ,
       Jul: 31, Aug: 31, Sep: 30, Oct: 31, Nov: 30, Dec: 31}['Feb'] AS DaysInFeb
```

#### Map projections

Suppose we want to return the Movie node information, but without the tagline property? You can do so as follows using map projections:

```shell
MATCH (m:Movie)
WHERE m.title CONTAINS 'Matrix'
RETURN m { .title, .released } AS movie
```


### Working with dates
Neo4j has these several basic formats for storing date/time data. There are a number of other types of data such as Time, LocalTime, LocalDateTime, and Duration which are described in the documentation.

```shell
RETURN date(), datetime(), time(), timestamp()
```


#### Accessing components of dates
With date() and datetime(), you can use properties such as day, year, time to extract the values that you need:

```shell
RETURN date().day, date().week, date().month, date().quarter, date().year
```

```shell
RETURN datetime().hour, datetime().minute, datetime().second, datetime().day, datetime().week, datetime().month, datetime().quarter, datetime().yearRETURN datetime().hour, datetime().minute, datetime().second, datetime().day, datetime().week, datetime().month, datetime().quarter, datetime().year
```

### Working with timestamp()
Working with timestamp() is different as its value is a long integer that represents time. The value of datetime().epochmillis is the same as timestamp(). To extract a month, year, or time from a timestamp, you would do the following:


```shell
RETURN datetime({epochmillis:timestamp()}).day,
       datetime({epochmillis:timestamp()}).year,
       datetime({epochmillis:timestamp()}).month
```

### Type and data conversions
Depending on how you need to convert data in the graph, you can use any of the built-in Cypher functions to convert the values.

Here are some of the built-in conversion functions:

- toInteger()
- toLower()
- toUpper( )
- toString()