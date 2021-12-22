---
title: Extensions, Relationships and Assertions
---

[TOC]

## Introduction

It is useful for research to have access to historical information, such as which artist was previously believed to be the creator of a work, or previous valuations of an object.  The majority of use cases, however, are to get the current information.  The assignment of attributes model allows for this additional information to be associated, without making every property a list of historical values.

This pattern is also used for context-specific assertions, such as when an object is given a label or description for the purposes of an exhibition or other event.  This exhibition label does not replace the owning museum's title, but is useful for historical comparison and research purposes.

The use of context specific assertions or other attribute assignments should be kept to a minimum if possible. The data structure is significantly more complex than other patterns, which reduces the likelihood of implementation and increases the difficult of search queries. This pattern should only be used if there is no other way to express the knowledge, and it is important to capture with all of the details.

## Assignment of Attributes

The `AttributeAssignment` class is an `Activity`, carried out by curators or researchers rather than by artists or collectors, that assigns information to resources in the model. This can be used to assign any property or relationship on any resource that can be the subject of such a property.  The general Activity properties of `carried_out_by`, `timespan` and `took_place_at` are available for when and where the assignment happened, and who made it.  The `timespan` is the moment when the assignment took place, rather than the length of time that the assignment was held to be true by some audience.

The value of the assignment (the thing being assigned) is given using `assigned`, and it can be a reference to any resource or entity. It cannot be a string or numeric value. The resource that the value is assigned to is given using the `assigned_to` property on the `AttributeAssignment` or on the entity being attributed using the `attributed_by` property. The relationship between them is given using `assigned_property`. Thus an `AttributeAssignment` can assign an `Actor` to a `Production` with the `carried_out_by` relationship, or a `Name` to an `Actor` with the `identified_by` relationship.  

The example below demonstrates associating a title with an object by a curator.

```crom
top = vocab.Painting(ident="auto int-per-segment",art=1)
top._label = "Painting"
aa = model.AttributeAssignment()
aa.assigned_property = "identified_by"
name = model.Name()
name.content = "Assigned Painting Title"
aa.assigned = name
top.attributed_by = aa
who = model.Person()
who._label = "Painting Curator"
aa.carried_out_by = who
ts = model.TimeSpan()
ts.begin_of_the_begin = "1980-05-19"
ts.end_of_the_end = "1980-05-19"
aa.timespan = ts
```

### "Style Of" Attribution

There is a common special case of wanting to assign not an individual (e.g. Rembrandt) or a group with specific identity (Workshop of Rembrandt) to the production of an object, but simply to say that it was produced as if it had been produced by some other actor.  This is traditionally recorded as being "in the style of" or "in the manner of" a known artist. It is not correct to say that Rembrandt carried out the production, but a search for objects attributed (loosely speaking) to Rembrandt should discover this object. The assessment of "style of" attribution is a judgement decision that might be changed later as new evidence of the actual creator comes to light.

The approach taken for this case is to use an `AttributeAssignment` that associates a Production activity that is `influenced_by` the artist and `classified_as` being in the "style of" (_aat:300404285_).  This prevent systems from mistakenly infering that the actor `carried_out` the production, but is consistent with the overall pattern.

This would also apply to cases where there is a "circle of" or "follower of" and similar attributions in which there is doubt that there was an actual coherent group, and thus there is reluctance to give that hypothetical group an identity.  Instead of using the "style of" AAT concept, it would use another [attribution qualifiers](http://www.getty.edu/vow/AATHierarchy?find=&logic=AND&note=&page=1&subjectid=300404264).

```crom
top = vocab.Painting(ident="auto int-per-segment",art=1)
top._label = "Example Painting"
aa = model.AttributeAssignment()
aa.assigned_property = "produced_by"
who = model.Person()
who._label = "Well Known Artist"
prod = model.Production()
prod.influenced_by = who
prod.classified_as = vocab.instances['style of']
aa.assigned = prod
top.attributed_by = aa
by = model.Person()
by._label = "Painting Curator"
aa.carried_out_by = by
```

## Context Specific Assertions

The basic pattern for making an assertion within some context is to reuse the `AttributeAssignment` activity, and have it be part of some larger activity.  A good example of this is the assignment of a particular title to a work during an [Exhibition](/model/exhibition/#exhibition-specific-labels). This could equally be part of the larger cataloging activity of an organization, an art dealer taking inventory, or any number of other contexts.

There are two relationships to note with this pattern.  The first is that the broader activity has a `part` which is the `AttributeAssignment`.  This gives some basic temporal context for the assignment. There is also in many cases a set of objects or other resources that provide additional context. In the Exhibition case, the set of objects and their labels in the exhibit is `involved` in the provision of the `Name` of the individual object.


```crom
top = vocab.Exhibition(ident="auto int-per-segment")
top._label = "Example Exhibition"
agg = model.Set()
top.used_specific_object = agg
obj = vocab.Painting(art=1)
obj._label = "Real Painting Name"
agg.member = obj
aa = model.AttributeAssignment()
aa.assigned_property = "identified_by"
name = model.Name()
name.content = "Exhibition Specific Name"
aa.assigned = name
aa.assigned_to = obj
curator = model.Person()
curator._label = "A. Curator"
aa.carried_out_by = curator
aa.involved = agg
top.part = aa
```

## Inferred Data

Some assertions, or even entire resources, are computationally inferred from other data rather than being evidenced in primary source literature or history. It is useful to tag these resources as such, so that they can be treated appropriately when it comes to research making use of them: if the underlying data has errors, these errors will have been propogated to this resource.

The way that this can be signalled in the data is to add the "computer-generated" concept _aat:300202389_ to the resource in the `classified_as` field.  

```crom
top = model.Activity(ident="auto int-per-segment")
top._label = "Inferred Activity"
top.classified_as = vocab.instances['computer generated']
a = model.Actor()
a._label = "Performer of inferred activity"
top.carried_out_by = a
```
