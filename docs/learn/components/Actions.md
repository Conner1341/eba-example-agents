## Actions

Actions are the means by which the assistant provides real data to corresponding concepts. Each skill contains a set of actions, where each action is designated by a set of [constraints](#constraints), [input](#input), and [output](#output). An action's signature follows the format `constraints => input -> output`, where the constraints are optional. Given a configuration of input concepts that are subject to certain constraints, the system is able to produce the specified output after it executes the body of the action. In addition to this signature, an action has a name and a function to be invoked when the action is selected. For example, consider the following action:

```
:Products(:Trending) -> data :Products
```

This action does not have any constraints - constraints are optional. This action effectively states that if the assistant recognizes a `:Products` concept along with a `:Trending` concept, then the system can produce real data for the `:Products` concepts. The data is produced by the body of this action, which can query a database, call an API, etc.

Actions can also take data that is provided by other actions:

```
:Products(data :Category) -> data :Products
```

This action expects a data node `:Category` that must be created by another action. The action can be triggered when the user asks for products by category, for example:

Q: _show me products for category "Jeans"_

Inside the body you can access this data by using params helpers:

```
const eba = require("eba")
module.exports.main = async function(params) {
	let p = new eba.Params(params)
	let category = await p.get(":Category")
	let products = ... get products by category here
	return new eba.Result().setData(":Products", products)
}
```

To get a category by its name, the following action can be used:

```
:Category(data :UserString) -> data :Category
```

Processing of quoted strings is available out of the box with EBA. Each quoted string is annotated with the `:UserString` concept and the corresponding data is created. You can get this data in your action body:

```
let categoryName = await p.get(":UserString")
```

To clarify the sense of some input parameters, add auxiliary concepts to the signature:

```
:Category(:WithName(data :UserString)) -> data :Category
```

In this case, the action is triggered __ONLY__ when `:UserString` node has an auxiliary neighbor concept `:WithName` as in questions like:

Q: _show me products for category named "Jeans"_

or

Q: _show me products for category with name "Jeans"_

The auxiliary concept can be marked as optional:
```
:Category(optional :WithName(data :UserString)) -> data :Category
```

This action can be triggered in both questions with or without the concept `:WithName`.

The data parameters can be optional too:

```
:Products(optional data :Category) -> data :Products
```

In this case, products that are related to a certain category can be returned if there is category data, otherwise all the products are returned.

The input parameters can be implicit:

```
:Products(implicit data :Category) -> data :Products
```

This action can be triggered without the `:Category` concept in the question thread (an optional parameter) but the agent is still able to search for the `:Category` concept data in the context. For example, the following scenario works with the implicit `:Category` parameter:

Q: _show me category "Jeans"_

A: Agent gets the category "Jeans"

Q: _show me products_

A: Agent gets products for category "Jeans"

If no concept is found in the context, the agent tries to recover this concept data by using available actions so questions like _show me products for "Jeans"_ can be asked.

### Constraints

Constraints are a way to qualify your concepts by using RDF triples in the format of *subject*, *predicate*, *object*, just like with of ontology. The subject and object must be either a concept or another symbol, and the predicate can be anything provided it is used consistently within your configuration. Constraints are used within EBA to denote a polymorphic parameter. For instance, `a subClassOf :List` constrains the symbol `a` to be *any* list. Constraints hold scope local to the signature they are defined in. Within the Development Lab, a warning is issued if a constraint is defined but never used within the remainder of the signature. Likewise, a warning is issued if a constraint symbol is used within the signature but never declared. Therefore, `a subClassOf :RankingMetric => :Mailings -> data :Mailings` contains a superfluous constraint, while `:Mailings(a) -> :Mailings` contains an undeclared symbol `a`. This example is a valid signature: `a subClassOf :RankingMetric => :Mailings(a) -> data :Mailings`.

In certain cases, it is possible to refer to a constraint parameter multiple times within a single signature, so it becomes necessary to distinguish between these references. For instance, `a subClassOf :Showable => a(reference:Direct, context data a @src)` uses the constraint parameter `a` twice. To distinguish the second occurrence of the parameter, an alias, `@src`, is added. Now, within the body of the action, data can be required by indexing the dependencies as "src".

### ParamType and queryType

Concepts within a signature can be qualified with additional specifications to apply different semantics to the action. These specifications are applied by using `paramTypes` and `queryTypes`. `paramTypes` qualify the parameters within a particular signature, while `queryTypes` qualify the actual query.

Three `paramTypes` are supported: `concept`, `data`, and `promise`. `concept` is the default `paramType` and no keyword is required to denote it. It signifies that a signature requires a concept to be present in the parse tree. `data` denotes the real data that is associated with an action (which is produced by another action). For instance, `edi:Submit(data edi:Invoice)` denotes a submission request that requires real invoice data. A [promise](./Promises.md) is used to denote a concept that is intended to be covered with real data after some further preliminary data is acquired. For example, promises to return weather data after the user's geographic location is ascertained.

Four `queryTypes` are supported: `regular`, `optional`, `context`, and `implicit`. `regular` is the default `queryType` and no keyword is required to denote it. It signifies that a query matches a concept in the current question as expected. `optional` qualifies the query to accept an optional parameter, for example, `:Mailings(optional :All)` means that the action supports questions such as "show me all mailings" or simply "show me mailings". `context` means that the query can search the conversational context for a parameter. For instance, `edi:Modify (context data edi:Invoice)` is an action that modifies an already queried invoice. `implicit` gives the assistant license to create new concepts by using other actions before it executes the current action. Let's use the following two actions as an example: `:SendVolume(optional :Relation(implicit data :Mailings))` and  `:Mailings -> data :Mailings`. When the assistant is asked a question about send volume, it might execute the mailings action first so that it has the data to complete the action for send volume. Mailings nodes are tagged within the information space as `virtual`. Because `implicit` actions can create additional concepts as a side effect and thus significantly alter the complexity of answering a question, they should be used judiciously. In fact, try to avoid `implicit` parameters whenever possible. Both `context` and `implicit` should be used only to qualify `data` nodes.

### Input

An action's input is represented as a tree of concepts. These concepts must be matched against in order for the action to be selected for execution. The concepts in this tree can be qualified with `paramTypes` and `queryTypes`. A common usage of `paramTypes` within input is to specify that an action requires real data. For instance, `sc:Order(sc:Identifier)` fires when the system notices a request such as "*show me order #1234*". However, the real data that is associated with the identifier, 1234 in this case, is not supplied to your action because only the concept `sc:Identifier` is required. Signify that `sc:Identifier` is a parameter with `data` by denoting the input as `sc:Order(data sc:Identifier)` to use this real data. Denoting that a parameter can be taken from content that already exists is a common usage of `queryTypes`. For instance, the confirmation request action`:Confirmation(context data expo:DeleteProduct)`requires real data that is associated with a deleted product to already exist within the context of the conversation. Both `paramTypes` and `queryTypes` can be used in conjunction with one another when you define input signatures, as demonstrated in the previous example.

Since input is represented as a tree, an ordering is imposed on the concepts. For instance, `:News(:Relation(:Organization)) -> data :News` implies the following ordering "news" -> "related to" -> "organization". EBA is able to understand such inherent dependencies when it builds the syntax tree for your query, enabling it to distinguish between a similar question such as "organization" -> "related to" -> "news".

### Output

Whereas input was a tree, output is a flat list of concepts. These concepts can be qualified only with `paramTypes`. Most often the output of an action is strictly a single concept that is qualified as data, for example `-> data Mailings`.

Next up, [rules](./Rules.md)
