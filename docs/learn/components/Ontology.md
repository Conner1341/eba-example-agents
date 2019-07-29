## Ontology

EBA follows an ontology-based paradigm, meaning that it derives understanding about the world based on a relationship of concepts. Concepts and their relationship to other concepts are defined in an ontology.

A concept is an object in your domain that the assistant is able to recognize. For example, in the marketing domain, there are concepts for mailings, open-rates, click-rates, and more. In the agent definition, a concept is denoted by a colon `:`. For instance `:Mailings` denotes the mailings concept. Additionally, prefix concepts with the domain of your skill. For example, `marketing:Mailings` signifies the Mailings concept in the marketing domain.

Therefore, an ontology is a set of relationships between different concepts in RDF format, _subject-predicate-object_, triples. Consider the following examples:

```
:OpenRate subClassOf :Showable
```

This example establishes the _subject_, `:OpenRate`, which is a concept that is derived from, or subclasses, the `:Showable` concept. The _predicate_ discloses what the subject does, in this case `subClassOf`. EBA visually renders all `:Showable` objects, which makes the open rate concept viewable to the user.

```
:Mailings isListOf :Mailing
```

This example specifies that the `:Mailings` concept is a list that is composed of individual `:Mailing` concepts. Knowing the composition of the list becomes useful for actions that the assistant performs when working with collections.


Certain built-in predicates have significant meaning for the system, two of which are detailed here:
- `subClassOf`  -- `a subClassOf b` means that _a_ inherits all of the attributes that _b_ contains. This is useful when a hierarchical domain needs to be modeled. For instance, `:SalesOrder subClassOf :Order` allows an entity to automatically derive attributes from its base concept.
- `isListOf`    -- `a listOf b` means that _a_ is a collection of _b_. The list enables EBA to automatically perform data operations on top of the collection. For instance, given the examples `:Orders isListOf :Order` and `:Order hasAttribute :Quantity`, questions such as "show me all orders where quantity is above 500" can immediately be asked.


Next up, [attributes](./components/Attributes.md)
