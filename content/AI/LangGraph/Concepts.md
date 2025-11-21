

1. [`State`](https://langchain-ai.github.io/langgraph/concepts/low_level/#state): A shared data structure that represents the current snapshot of your application. It can be any data type, but is typically defined using a shared state schema.
    
2. [`Nodes`](https://langchain-ai.github.io/langgraph/concepts/low_level/#nodes): Functions that encode the logic of your agents. They receive the current state as input, perform some computation or side-effect, and return an updated state.
    
3. [`Edges`](https://langchain-ai.github.io/langgraph/concepts/low_level/#edges): Functions that determine which `Node` to execute next based on the current state. They can be conditional branches or fixed transitions.

## Reference

- https://langchain-ai.github.io/langgraph/concepts/low_level/