---
title: "HAL Link: activityCarriedOutByAgent"
---

## activityCarriedOutByAgent

Return the activities that were carried out by the person or group.

See the related [model documentation](/model/base/#events-and-activities /model/exhibition/#exhibition-activity)

### Example

From the record for the National Gallery of Art, the record for the Manet exhibition activity would be in the response


### Details

* Class Given: Agent
* Returns Class: Activity
* Relationship: carriedOutBy


### SPARQL
```
SELECT DISTINCT ?activity WHERE {
  BIND (<%current%>AS ?group)
.  ?activity crm:P14_carried_out_by ?group .
   }
```

