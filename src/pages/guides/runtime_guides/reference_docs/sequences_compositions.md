# Sequences and Compositions

There are two ways to orchestrate a series of action calls into a flow: sequences and compositions.

Sequences are strings of actions invoked one after another. At each step the result of the preceding action feeds into the arguments of the next one. There is no support for skipping actions.

Compositions execute a tree of actions or use the result of one action to determine the next, as in an if/then/else control structure.

## Sequences

Assuming two actions created in a package called `my-package`:

```
/my-package/actionA
/my-package/actionB
```

Create a sequence using the `--sequence` flag in addition to the usual command for creating an action. Be sure to add the namespace to the action name or you'll receive an unauthorized access error:
`aio rt:action:create mySequence --sequence /[your-namespace]/my-package/actionA,/[your-namespace]/my-package/actionB`

Invoke this as you would any other action, for example:
`aio rt:action:invoke --result mySequence`

### Sequences and timeout

When invoking a sequence in a blocking manner, there is a 60-second timeout limit that can't be changed: the sum of the execution times of all actions in the sequence must be 60 seconds or less. 

If we apply this limitation to the example above, then `actionA` and `actionB` have a total budget of 60 seconds. Although the system allows you to set a higher timeout value for `mySequence`, it will ignore the value and enforce the 60-second limit.

If one of your actions needs more than 60 seconds (therefore putting the sequence over the limit), the only solution is to invoke it as a non-blocking action using the OpenWhisk npm module. So, using the same example, you could have `actionA` calling another action in a non-blocking manner. You can see an example of how to do this [here](../../runtime_guides/asynchronous_calls.md).

Read more about sequences on the [OpenWhisk documentation page](https://github.com/apache/incubator-openwhisk/blob/master/docs/actions.md#creating-action-sequences).

## Compositions

To orchestrate a more complex flow without having to jam all the code into a single action, use Apache OpenWhisk Composer. Composer lets you assemble actions, including other compositions, together and control the executions using control-flow structure: if, while, repeat. The result of a composition gives you a single entry point and behaves like an action: it supports default parameters, can be placed into a package, or used as web action.

### Install Composer

To use this featuren, install the Composer Node.js package:

```
npm install -g openwhisk-composer
```

Use it to create the JSON file you need to deploy a composition. The flow for creating a composition is:

1. Define the composition using JavaScript
2. Run the `compose` command to generate the JSON file out of this JavaScript file
3. Run `deploy` to deploy the composition using the JSON file you generated in the previous step

For, example, assume you have actions deployed called `a`, `b`, and `c` and want to create a composition that executes `a` and, in case of success, `b`; and if not;  `c`. Use the Composer package to define this composition in a JavaScript file `myComp.js`: 

```
const composer = require('openwhisk-composer')

module.exports = composer.if(‘a’, ‘b’, ‘c’)
```

With the composition defined, generate the JSON definition needed for deployment:

```
compose myComp.js > myComp.json
```

To deploy:

```
deploy compositionA myComp.json
```

This creates a composition called `compositionA`. Invoke it like any other action - `aio rt:action:invoke compositionA`. When you do this, action `a` is invoked first. Then, if `a` is successful, action `b` is invoked; if not, action `c` is invoked.

More information is available at:

* Apache OpenWhisk Composer [home page]( https://github.com/apache/incubator-openwhisk-composer)
* [Combinators](https://github.com/apache/incubator-openwhisk-composer/blob/master/docs/COMBINATORS.md), with a complete list of the control-flow structure.

### Parallel compositions

I/O Runtime does not support parallel compositions.

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).
