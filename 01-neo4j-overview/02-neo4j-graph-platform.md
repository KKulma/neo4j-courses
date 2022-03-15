The Neo4j Graph Platform enables developers to create applications that are best architected as graph-powered systems, built upon the rich connectedness of data.
It consists of several components:
1. Neo4j DBMS 
2. Neo4j Aura 
3. Neo4j Sandbox 
4. Neo4j Desktop 
5. Neo4j graph applications 
   - Neo4j Browser 
   - Neo4j Bloom
6. Neo4j libraries 
7. Neo4j drivers 
8. Neo4j integration 
9. Neo4j administration tools 
   - cypher-shell


### 1. Neo4j DBMS
The heart of the Neo4j Graph Platform. 
Includes processes and resources needed to manage a single Neo4j instance or a set of Neo4j instances that form a cluster.

A Neo4j instance is a single process that runs the Neo4j server code. A Neo4j instance at a minimum contains two databases, the system database and the default database, neo4j.
The system database stores metadata about the databases for the installation as well as security configuration. The default database (named neo4j by default) is the "user" database where you implement your graph data model.

**Index-free adjacency**
One of the key features that makes Neo4j graph databases different from an RDBMS is that Neo4j implements index-free adjacency.


### 2. Neo4j Aura 
Neo4j Aura is the simplest way to run the Neo4j DBMS in the cloud.
- The Neo4j team manages the administration of Neo4j. 
- Developers focus on creating Neo4j applications.

### 3. Neo4j Sandbox
The Neo4j Sandbox is a way that you can begin development with Neo4j. It is a free, temporary, and cloud-based instance of a Neo4j Server with its associated graph that you can access from any Web browser.

### 4. Neo4j Desktop

Example Neo4j graph applications
Here are some of the graph applications you can use:

1. Neo4j Browser
- UI for testing Cypher queries and visualizing the graph.

2. Neo4j Bloom
- A tool for exploring graphs and generating Cypher code.

3. Neo4j ETL Tool
- UI for connecting to a data source to import into the graph.

4. Halin
- Monitor your Neo4j DBMS.

5. Query Log Analyzer
- Analyze queries that are executed on your system.

6. Neo4j Cloud Tool
- Tools for working with Neo4j Aura.

7. Data Science Playground
- Run graph algorithms and generate code for them.