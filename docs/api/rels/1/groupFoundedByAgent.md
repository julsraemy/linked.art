---
title: "HAL Link: groupFoundedByAgent"
---

## groupFoundedByAgent

Return the groups that were founded by the person or group.

See the related [model documentation](/model/actor/#birth-and-death-formation-and-dissolution)

### Example

From the record for Katherine Dreier, Man Ray or Marcel Duchamps, the group Societe Anonyme would be in the response


### Details

* Class Given: Agent
* Returns Class: Group
* Relationship: foundedBy


### SPARQL
```
SELECT DISTINCT ?group WHERE {
   BIND (<%current%>AS ?who)
.   ?group crm:P95i_was_formed_by ?formation .
   ?formation crm:P14_carried_out_by ?who .
 }
```

