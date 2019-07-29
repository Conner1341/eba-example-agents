## Examining EBA's understanding

At the core of EBA is its unique machine reasoning. The following diagram gives you an overview of how a user's input moves through the pipeline, from query to response.

(Diagram end-to-end flow: user input -> annotation -> reasoning -> execution -> response)

The journey begins with user input. The user enters their query by using natural language constructs. The input text is then parsed, and tagged according to its morphological and syntactical features and is represented as a syntax tree.

This syntax tree is then annotated with a set of domain concepts, which are encoded by the developer relative to their domain of expertise. Concepts often correspond to business entities and processes and as such, forms the atomic building blocks, which the assistant uses to understand and reason about the outside world. These annotations effectively map the user's _natural_ language to the assistant's _conceptual_ language.

Given a set of actions formulated in terms of these domain concepts, EBA performs _reasoning_ to select the best interpretation to resolve the user input. During the reasoning process, EBA composes multiple actions together in an input and output sequence to form an outcome. As it reasons, it scores each outcome according to its syntactic and semantic properties to select the best possible interpretation. 

After the appropriate action path is selected, EBA _executes_ that path, including any side effects that it might produce.

EBA generates its _response_ to the user's query by using **Natural Language Generation** (NLG). A process known as lexicalization decides which expressions are used to refer to conceptual entities, and linguistic realization uses grammar rules to convert abstract representations of sentences into actual text. EBA's responses are **not** hardcoded!


Next up: [core components](./core-components.md)
