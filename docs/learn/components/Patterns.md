## Patterns

Patterns are natural language text samples that are annotated with concepts. When the assistant receives a query from the user, it tokenizes and parses this input into a syntax tree. By using natural language patterns, EBA is able to annotate this tree with the appropriate concepts. For example, consider the following patterns:

```
show [trending](wmt:Trending) [products](wmt:Products)
```

This pattern tells the system that the "trending" token corresponds to the concept `wmt:Trending` and the "products" token corresponds to the concept `wmt:Products`. From this point forward, EBA recognizes and considers "trending" and "products" for all further input. It also captures the syntactic part of speech that is associated with these tokens.

### Recommendations

Enter patterns in a way that logically separates concepts. For instance, `show me [product id](:ProductId)` annotates two concepts as one - "product" and "ID". The better practice is to enter patterns for ID `[id](wmt:Identifier)` and patterns for product `[product](wmt:Product)` separately since they are actually two different concepts. In applications that have multiple business entities and multiple ways to refer to these entities, you will find yourself entering many unnecessary patterns if they are not separated.

In addition, individual patterns should not be placed within one entry. For example, `[\#](:IDSign), [id](:IDSign), [no](:IDSign), [no.](:IDSign), [num](:IDSign)` represents :IDSign as being equivalent to "# ID no no. num". Instead, enter these patterns individually as a way to create equivalent references to the `:IDSign` concept.

Next up, [actions](./Actions.md)


Numerous unnecessary patterns 
