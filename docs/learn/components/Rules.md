## Rules

Rewriting rules are a way to transform a single concept into a cluster of other related concepts. Each skill can contain a set of rules, where each rule is designated by a set of [constraints](#constraints), and [input and output](#input-and-output). Often times natural language is short and allows for the omission of certain words or concepts. Rewriting rules enables the assistant to handle such cases effectively. While EBA's concepts are well-formed, it is anticipated that users will use imprecise language to inquire about them. For example, a user might ask for "the best mailing" or "trending products". Rewriting rules can be implemented to reduce higher-level notions such as "best" and "trending" into a cluster of concepts that the assistant can more easily reason about. For example, consider the following rewriting rule:

```
:TheBest(:Mailing) -> :HighValue(:ClickToOpenRate, :Mailings)
```

Here, the best mailing is rewritten to be converted into lower-level concepts. `:HighValue` is a concept that is used by the system to return highest quarter data points and `:ClickToOpenRate` is an attribute of `:Mailing`. The assistant is effectively programmed to recognize "the best mailings" as being equivalent to "mailings with clickToOpenRate attribute in the highest quarter".


### Constraints

Constraints are a way to qualify your concepts by using RDF triples in the format of *subject*, *predicate*, *object*, just like with of ontology. The subject and object must be either a concept or another symbol, and the predicate can be anything provided it is used consistently within your configuration. Constraints are used within EBA to denote a polymorphic parameter. For instance, `a subClassOf :List` constrains the symbol `a` to be *any* list. Constraints hold scope local to the signature they are defined in. Within the Development Lab, a warning is issued if a constraint is defined but never used within the remainder of the signature. Likewise, a warning is issued if a constraint symbol is used within the signature but never declared. Therefore, `a subClassOf :RankingMetric => :Mailings -> data :Mailings` contains a superfluous constraint, while `:Mailings(a) -> :Mailings` contains an undeclared symbol `a`. This example is a valid signature: `a subClassOf :RankingMetric => :Mailings(a) -> data :Mailings`. Additionally, constraints can use a wildcard symbol to denote that it accepts any type, for example, `a isListOf _` denotes that constraint `a` is a list of anything.

In certain cases, it is possible to refer to a constraint parameter multiple times within a single signature, so it becomes necessary to distinguish between these references. For instance, `a subClassOf :Showable => a(reference:Direct, context data a @src)` uses the constraint parameter `a` twice. To distinguish the second occurrence of the parameter, an alias, `@src`, is added. Now, within the body of the action, data can be required by indexing the dependencies as "src".

### Input and output

Both input and output to rules are represented as a tree of concepts. There is no `paramTypes` or `queryTypes` associated with these concepts. A rule converts the input (higher level) into the output (lower level).

