# redis-setup

```docker run --name redis-commander -d -p 8081:8081 --env REDIS_HOSTS=local:host.docker.internal:6379 ghcr.io/joeferner/redis-commander:latest```


# Commands

## Graph

- ### GRAPH.LIST
  - show all graph's keys
    ```
    GRAPH.LIST
    ```
- ### GRAPH.QUERY
  - show every thing in a key
    ```
    GRAPH.QUERY [key] "MATCH (r) RETURN r"
    ```
  - cerate new [node] in [key]
    ```
    GRAPH.QUERY [key] "create ([name]:[type] [json like data. ex:{name:'dog'}]), (...), ..."
    ```
  - change [data] inside [node]
    ```
    GRAPH.QUERY [key] "MATCH(r:[type 1]) WHERE r.[prop]='[value]' SET r.[prop]='[new value]'" // can add [RETURN r]
    GRAPH.QUERY [key] "CYPHER name='[new value]' MATCH(r:[type 1]) WHERE r.[prop]='[value]' SET r.[prop]=$name" // can add [RETURN r]
    ```
  - Add Relation between two [nodes]
    ```
    GRAPH.QUERY [key] "MATCH (r:[type 1]), (t:[type 2]) WHERE r.name = 'Jose' and t.name = 'Luis' CREATE (r)-[:amigos]->(t)
    ```
  - find by relation nad properties
    ```
    GRAPH.QUERY [key] "MATCH (r:[type 1])-[:[relation name]]->(t:[type 2] {[key]:[value]}) RETURN count(r)"
    ```
  - delete [node]
    ```
    GRAPH.QUERY [key] "MATCH (r:[type 1] {[key]:[value]}) DELETE r"
    ```
  - delete [relation]
    ```
    GRAPH.QUERY [key] "MATCH (:[type 1] {[key]:[value]})-[r:rides]->() DELETE r"
    ```
- ### GRAPH.DELETE
  - delete [key]
    ```
    GRAPH.DELETE [key]
    ```
