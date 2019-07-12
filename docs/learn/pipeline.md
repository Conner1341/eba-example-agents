## Examining EBA's understanding

At the core of EBA is its unique machine reasoning. The following diagram gives you an overview of how a user's input moves through the pipeline, from query to response.

(Diagram flow: user input --> annotation --> reasoning --> execution --> response)

The journey begins with _user input_. The user enters their query by using natural language constructs. The input text is then parsed and tagged according to its morphological and syntactical features and is represented as a syntax tree.

A set of concepts and relationships that are created by the developer tells the assistant what it can understand and reason about. It is this encoded conceptual knowledge that is used to _annotate_ the syntax tree, mapping natural language within the user's query to conceptual entities.

With this information in hand, EBA considers all possible outcome paths and _reasons_ to produce the most relevant path.

After the appropriate action path is selected, EBA _executes_ that path, including any side effects that it might produce.

EBA generates its _response_ to the user's query by using **Natural Language Generation** (NLG). A process known as lexicalization decides which expressions are used to refer to conceptual entities, and linguistic realization uses grammar rules to convert abstract representations of sentences into actual text. EBA's responses are **not** hardcoded!


Next up: [core components](./core-components.md)
