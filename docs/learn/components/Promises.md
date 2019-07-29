## Promises

As described in the [action component](./Actions.md), EBA supports a parameter type called a `promise`, which enables data to be produced for an action only after some preliminary data is acquired. This data collection is typically accomplished by the use of follow-up questions in a dialogue series. For a live example of this feature, try the [Weather agent](https://eba.ibm.com/assistant#/lab/agents/weather/actions). The following examples walk through the steps of implementing a few promise dialogs, by using the Weather agent as a reference.

### Asking for follow-up information

Consider a business entity that requires another data element, as a necessary condition in order to fetch the entity. Using the weather agent as an example, this is `weather:Weather`, which requires both a city and a timeframe.

Or, perhaps you are resolving a user's question and the runtime execution indicates an ambiguous case that needs to be resolved by a follow-up. For example, the user asks for a certain entity but you need to establish which type or which class it belongs to. In these cases, a follow-up dialog can be used to resolve any disambiguation.

In each of these cases a user can ask a fully qualified question and no follow-up dialog is required, such as '_show me the weather in San Fransisco two days from now_'. However, this is not always the case, and, as a result, developers are enabled to handle less qualified requests for data.

To handle less qualified requests, employ the following strategy. When a reference to your entity occurs, implement an action that returns a `promise` node as output, rather than a `data` node. For instance, `weather:Weather -> promise weather:Weather` signifies an unqualified case, where the user asks something general such as, "_what is the weather_". _Data_ cannot be produced for `weather:Weather` until a location is obtained. Such an action might look something like this:

```
const {Result} = require('eba')

module.exports.main = (params) => {
  return new Result()
    .setName('weather:Weather', ':Question')
    .setData('weather:Weather', 'Which location should I get the weather for?')
}
```

It is important to note two things from this code sample. First, the concept is renamed from `:Weather` to `:Question` by using the `setName` API detailed in [node helpers](../lab/NodeHelpers.md). Second, the follow-up question is supplied as data by using the `setData` API. With these two elements in place, the system is able to understand that `weather:Weather` cannot be resolved in the current execution; instead, it should display a follow-up question.

More complex cases can also be modeled. For example, `weather:Forecast` requires both a timeframe and a location in order for data to be produced. This can be modeled in three ways: as a 3-step sequence, where you ask for forecast, ask for location, and ask for timeframe. Or, it can be modeled as 2-step sequence, asking for forecast and timeframe or location, and then ask for the remaining element. Or you can be flexible enough to support both variants. This depends largely on the agent's business requirements. In the implementation of `weather:Forecast`, the second model was followed. The following signatures can be found in that implementation:

 - `weather:Forecast(optional :Relation(data :Timeframe) -> promise weather:Forecast`
 - `weather:Forecast(optional :Relation(city)) -> promise weather:Forecast`

The bodies of such actions all follow a pattern similar to the previous example, where the concept is renamed to `:Question` with a supplied Natural Language question.

### Generating data

With a `promise` node in place, the system attempts to resolve it by converting it to a `data` node whenever it is suitable. To enable such behavior, an additional action to produce real data should be implemented. This action will contain all concepts that are required to resolve the entity as input. In the weather example, we have an action as `weather:Forecast`, we have an action `weather:Forecast (optional :Relation (data city), optional :Relation (data :Timeframe)) -> data weather:Forecast`. This action requires both city and timeframe data and the output produces `data weather:Forecast`, meaning that it can provide the data which the user initially requested. The body of this action uses the supplied input to perform any API calls necessary. All actions for the weather agent are viewable to users. Feel free to look and explore this feature in your own implementations.

[Back to actions](./Actions.md)    or [Next, rules](./Rules.md)
