# Entities and Core Concepts

Adobe I/O Runtime's computing model is composed of functional elements or entities, and the interactions among them. This glossary defines the most important entities, outlines their interactions, and links to relevant resources in the Guides and elsewhere in this documentation.

## Actions

Actions are stateless functions that run on Adobe I/O Runtime. Actions can be used to detect faces in an image, respond to a database change, respond to an API call, or post a Tweet. Actions are invoked in response to events, and produce some observable output.

For more information, see:

- [Creating Actions](../../guides/runtime_guides/creating_actions.md)
- [Securing Web Actions](../../guides/runtime_guides/securing_web_actions.md)
- [CI/CD Pipeline](../../guides/runtime_guides/ci-cd_pipeline.md)

## Namespaces

All Adobe I/O Runtime entities are organized into namespaces. A namespace is essentially a prefex attached to entity names to organize them. Every organization on Runtime gets its own namespace, and all entities created in Runtime for that organization get that namespace applied as a prefix. 

## Triggers and rules

Triggers are a way to name a class of events. In Runtime, events are defined by a set of key-value pairs; this allows them to be passed to an action as a JSON object. Triggers are used to define the keys for these pairs. When a trigger is activated, it reads the values of these keys from the event source, and passes on a dictionary of key-value pairs that define the event.

Rules link triggers with actions. Each rule links a specific trigger with a single action: every time the trigger is activated, the rule causes the action to be invoked, with the trigger's event used an input parameter. Combining rules can cause a trigger to initiate multiple actions, or cause a single action to respond to multiple triggers.

For more information, see [Triggers and Rules](../../guides/runtime_guides/reference_docs/triggersrules.md).

## Packages

In OpenWhisk, packages can bundle a set of related actions and share them with others. Packages can include actions and feeds. See:

- [Pre-installed Packages](../../guides/runtime_guides/reference_docs/prepackages.md)
- [Working with Packages](../../guides/runtime_guides/reference_docs/packages.md)

## Sequences and compositions

A powerful feature of Adobe I/O Runtime is the ability to compose actions together. Two types of composed actions are possible:

- **Sequences:** are sets of actions composed so the output of one action is fed as a parameter to the next action. Sequence actions take parameters like regular actions, but they apply only to the first action in the sequence. The execution order of actions is set at the time the sequence is defined.

- **Compositions** use conductor actions and combinator methods to make up actions. These methods implement the typical control-flow constructs of a sequential imperative programming language. Compositions allow actions to execute in different orders depending on logic flow. Effectively, compositions are actions whose logic consists of component actions, offering a flexible way to combine and reuse actions.

## Feeds

Feeds are streams of events, distinct from triggers:

* **Triggers** classify events: each defines a type of event and is activated when that event is received by Runtime. Triggers are linked to actions by rules; each rule links a trigger to a single action.
* **Feeds** are streams of events handled by the same trigger. Feeds are controlled by feed actions that manage the stream: creating, deleting, pausing, or resuming it as needed. In this way, feeds therefore control the flow of input to triggers. Feed actions use a REST API to interact with external services to produce the events to which triggers are set to respond.

For more information, see [Using Feeds](../../guides/runtime_guides/reference_docs/feeds.md).

## Next step

Next we'll see how these entities interact to deliver Runtime function-as-a-service, in [How Adobe I/O Runtime works](how_runtime_works.md)
