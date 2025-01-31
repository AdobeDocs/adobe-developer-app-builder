# Using Packages with Adobe I/O Runtime

Individual actions (functions) can be effective, but you may also need to create sets of actions that interact with each other, or make them available to other Developers. Runtime meets these requirements with packages.

Packages are bundles of related actions; they can contain both actions and feeds, or streams of events. Feeds are created by feed actions that specify a trigger to fire for a given event source, so that each time the event is received, the selected trigger is fired and the associated action executed. For more information, see [Using Feeds](reference_docs/feeds.md).

## Creating packages

Packages bundle actions and feeds, and allow sharing a set of parameters in common across all entities in the package. You can create a package in the Runtime CLI in one step:

`aio rt:package:create hellopackage`

This creates a package in your namespace and sends this acknowledgement:

```
ok: created package hellopackage
```

To request a summary of the package, enter:

`aio rt:package:get hellopackage`

It will repond like this:

```
package /<yourNamespace>/hellopackage
   (parameters: none defined)
```

## Adding actions to a package

The package you created is empty. To add an action to it, use the source code from the `hello` action created in [Deploying your first Adobe I/O Runtime action](../../get_started/runtime_getting_started/deploy.md). Here is the code:

```
function main(params) {
  return { payload: 'Hello ' + params.name };
}
```

Add the action to the package with this command:

`aio rt:action:create hellopackage/hello hello.js`

The CLI will respond:

```
ok: created action hellopackage/hello
```

This is similar to creating an action, but prefaces the action name with the name of the package you want to put it in. But note that this action isn't the same one you created from this code earlier; that action still exists in your namespace, outside any package. You've created a new action and stored it in the package: there's no way to move an existing action into a package, or move an action from one package to another.

Another request for a summary produces this message:

```
package /<yourNamespace>/hellopackage
   (parameters: none defined)
 action /<yourNamespace>/hellopackage/hello
   (parameters: none defined)
```

<InlineAlert slots="text"/>

Packages can't be nested, that is, they can't contain other packages.

## Invoking actions in a package

Actions are invoked by prefacing the action's name with the package name:

`aio rt:action:invoke --result hellopackage/hello --param name <your name>`

You should get this output:

```json
{
  "payload": "Hello <your name>"
}
```

## Adding parameters to a package

The package summaries above contain the statement `(parameters: none defined)`. You can add default parameters to a package; when you do, all entities in the package will inherit them:

`aio rt:package:update hellopackage --param name Patricia`

```
ok: updated package hellopackage
```

### Default parameters and encryption

We will discuss how to set and use default parameters below. But since many Developers use default parameters as a way to provision actions with the secrets and passwords needed to authenticate against databases, services, APIs, and other systems, we need to introduce some security concepts first.

To support the use of package parameters to provision actions with secrets and passwords, all default parameters are automatically encrypted, then decrypted just before the action code is executed. The decrypted values are therefore available only during execution of the action code.

A CLI command for getting an action or package returns a listing of default parameter names with hashes of the associated values,  not the actual values.

You can see what parameters have been added to a package. The `summary` flag is left out here to receive a complete report:

`aio rt:package:get hellopackage`

The response shows:

```
ok: got package hellopackage
...
"parameters": [
    {
        "key": "name",
        "value": some_hash
    }
]
...
```

To verify that your actions are inheriting the parameters you set for the package, enter:

`aio rt:action:get hellopackage/hello`

And see this response:

```
ok: got action hello
...
"parameters": [
    {
        "key": "name",
        "value": some_hash
    }
]
...
```

If you get the package summary again, you'll see a listing of the default parameters:

```
package /<yourNamespace>/hellopackage: Returns a result based on parameter name
   (parameters: *name)
 action /<yourNamespace>/hellopackage/hello
   (parameters: none defined)
```

The asterisk in "*name" above indicates that the parameter is bound: see [Package bindings](#package-bindings) below. A single asterisk means the parameter has a defined default value; a double asterisk means its default value is finalized, and cannot be changed by the user.

If you invoke the action without specifying any parameters, you will see that it inherits them from the package:

`aio rt:action:invoke --result hellopackage/hello`

```json
{
  "payload": "Hello Patricia"
}
```

Supplying your own values overrides the defaults:

`aio rt:action:invoke --result hellopackage/hello --param name Jennifer`

```json
{
  "payload": "Hello Jennifer"
}
```

You can also override a package's default parameters for an action by setting default parameters specifically for that action:

`aio rt:action:update hellopackage/hello --param name Christine`

```
ok: updated action hellopackage/hello
```

## Package bindings

Depending on your use of packages, you may need to invoke an action with default parameters, without them, or with a different set of default parameters. Rather than create a set of default parameters directly on a package, you can create a package binding: a named set of default parameters associated with a package.

`aio rt:package:bind hellopackage helloMyName --param name`&nbsp;_`<your name>`_

```
ok: created binding helloMyName
```

A package binding is a shortcut to call a package with named set of parameters. This leaves the package and its entities open to be called without the parameters defined in the binding, or with the parameters defined in a different binding. There is no limit to the number of bindings you can create for a package.

<InlineAlert slots="text"/>

Package bindings are important, because there is no way to remove default parameters from a package or action once they've been set. Using bindings is the only way to preserve the option to call an action in the package without the defaults.

Each package binding is used as if it were a package itself. To call an action in the package associated with a given binding, replace the name of the package in your invocation with the name of the binding:

`aio rt:action:invoke --result helloMyName/hello`

```json
{
  "payload": "Hello Christine"
}
```

<InlineAlert slots="text"/>

The output above reads `Hello Christine` because we've defined a default value at the action level, which takes precedence over values set at the package level or invocation time. When you share a package, this ensures that your parameters can't be overwritten at execution time. For values you want the user to define, don't define defaults .

You can also substitute the name of the binding for the package name in other package commands, for example:

`aio rt:package:get helloMyName`

## Browsing packages

To see all the packages in your namespace, enter this CLI command:

`aio rt:package:list`

It will show all your packages and the bindings you created:

```
packages
/<yourNamespace>/hellopackage                                            private
/<yourNamespace>/helloMyName                                             private
```

## Sharing packages

Once your package is ready for others to use, you can share it. The packages listed when you browsed your namespace were shown as `private`; only you can see them. To share a package, enter:

`aio rt:package:update hellopackage --shared yes`

```
ok: updated package hellopackage
```

Verify that your package is shared this way:

`aio rt:package:get hellopackage`

```
ok: got package hellopackage
...
"publish": true
...
```

List your packages again, and shared packages will be listed like this:

```
packages
/<yourNamespace>/hellopackage                                            shared
/<yourNamespace>/helloMyName                                             private
```

### Unsharing packages

To make your package private again, enter: 

`aio rt:package:update hellopackage --shared no` 

Verify that it is now private like this:

`aio rt:package:get hellopackage`

```
...
"publish": false
...
```

Listing your packages again will show the package you made private this way:

```
packages
/<yourNamespace>/hellopackage                                            private
/<yourNamespace>/helloMyName                                             private
```

Others can use shared packages just as you can, so long as you give them the fully qualified name of the package, including your namespace.

## Shared packages and permissions

Shared packages enforce `execute-only` permission for any operation initiated from outside the namespace that owns the package. Assuming there is a shared package `my-package` in namespace `a`,  and a namespace `b` that uses the shared package, then:

* `my-package` actions may be invoked from either namespace `b` or `a`
* Attempts to read, update, or delete `my-package` code from namespace `b` will fail
* Attempts to create, read, update, or delete `my-package` will work only from namespace `a`

## Next step

Return to [Guides Index](../guides_index.md).
