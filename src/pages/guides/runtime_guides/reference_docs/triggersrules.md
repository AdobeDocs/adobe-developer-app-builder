# Using Triggers and Rules

Actions invoked directly in the CLI have limited application; Adobe I/O Runtime is most useful when it is configured to respond to events. Runtime features that support this are triggers and rules:

* **Triggers** are events: each trigger you create represents a class of event.
* **Rules** are associations between triggers and actions. Rules are the way you make Runtime execute code in response to an event.

## About triggers

Triggers are a way to name a class of events. Examples include:

* Updating a user's location on a mobile app
* Uploading a file to cloud storage
* Sending an email or text message

Almost anything can be classified as an event by defining it as a trigger. In Runtime, events are defined by a set of key-value pairs, so they can be passed to an action as a JSON object. Triggers are used to define the keys for these pairs. When a trigger is activated (_fired_), it reads the values of these keys from the event source and passes on a dictionary of key-value pairs that define the event.

You can fire a trigger explicitly in the CLI yourself, but it's much more common - and useful - for an event source to be configured to fire triggers via a feed. For more information about this, see [Using Feeds](feeds.md "Using feeds").

## About rules

Rules link triggers with actions. Each rule links a specific trigger with a single action: every time the trigger is fired, the rule causes the action to be invoked with the trigger's event used as input parameters. By combining rules, however, you can cause a trigger to fire multiple actions, or cause an action to respond to multiple triggers. 

For example, consider a mobile app that tracks the ser's location when the user takes a photo, records the user's GPS coordinates, and uploads the photo to the user's Adobe Assets account in a library named "My Photos." The hypothetical app also captures images sent or received by text or email and uploads them to the Adobe Assets in another library named "Shared with Me." You could then configure these actions:

* `catalogImg`: calls a Sensei Studio function to analyze the image and recognize objects
* `mapImg`: adds metadata to the image to log its GPS coordinates
* `thumbImg`: creates a thumbnail of an image

Using the mobile app as an event source, you could set the following triggers:

* `myPhoto`: fires when the app uploads a photo taken by the user
* `sharedImg`: fires when the app uploads an image sent or received by email or text message

To process these images in Runtime, you could set the following rules:

* `myPhoto -> catalogImg`
* `myPhoto -> mapImg`
* `myPhoto -> thumbImg`
* `sharedImg -> catalogImg`
* `sharedImg -> thumbImg`

With these rules, images taken by the user and images sent or received by email or text are catalogued and thumbnails created; for the user's own images only, GPS coordinates are also tracked.

## Creating and firing triggers

Creating triggers is easy. Consider a trigger to update a user's location: every time the trigger is fired, it sends the user's name and GPS coordinates. To create this trigger in the CLI:

`aio rt:trigger:create updateLoc`

```
ok: created trigger updateLoc
```

Just as with packages and actions, you can list the triggers  you've created:

`aio  rt:trigger:list`

```
triggers
/yourNamespace/updateLoc                                              private
```

This creates a label for a channel through which events can be passed to the system. To fire this trigger, specifying the parameters:

`aio rt:trigger:fire updateLoc --param name Jocasta latitude 38.316667 longitude 23.316667`

```
ok: triggered updateLoc with id 94dddb5ef8ca46e9ba4648eeb9bda80f
```

Now the trigger has been fired, but nothing happened: there is no rule to link the trigger with an action. Triggers that fire with no associated rule have no effect.

## Creating and using rules

Rules link triggers with actions. To create one, you need the name of your trigger, the name of the action to execute when the trigger is fired, and a name for the rule itself. 

To give your trigger an appropriate action, create an action named `helloLoc` using the code below. See [Deploying your first Adobe I/O Runtime function](../../../get_started/runtime_getting_started/deploy.md "Deploying your first function") for instructions on creating an action:

```js
function main(params) {
   return {payload:  'Hello, ' + params.name + ', I see you are at latitude ' + params.latitude ', longitude ' + params.longitude };
}
```

Now you can create the rule linking the trigger to the action:

`aio rt:rule:create greetLoc updateLoc helloLoc`

This creates a rule named `greetLoc` that executes `helloLoc` every time the trigger `updateLoc` is fired. Now, fire the trigger again:

`aio rt:trigger:fire updateLoc --param name Jocasta latitude 38.316667 longitude 23.316667`

```
ok: triggered updateLoc with id 00e0dd4cce3f43768dabb99d67731b50
```

To see what happened as a result, check your activations. To list them, enter:

`aio rt:activation:list`

Your `hello` action should be first on the list. If the list is long, , you may want to show only recent activations:

`aio rt:activation:list --limit 1 helloLoc`

```
activations
a48069e1da3c4aa9bc48ac979c5ee140             helloLoc
```

To see the results of this activation:

`aio rt:activation:result a48069e1da3c4aa9bc48ac979c5ee140`

```
{
    payload: "Hello, Jocasta, I see you are at latitude 38.316667, longitude 23.316667"
}
```

The helloLoc action received the parameters from updateLoc and returned the result you would expect.

Finally, to stop a rule, disable it:

`aio rt:rule:disable greetLoc`

## Working with triggers and rules

Triggers, rules, and actions can be combined in different ways: you can associate a trigger with multiple actions by using a different rule for each action, and an action can be the target of multiple triggers, again by creating a rule for each trigger. 

You cannot, however, link a trigger to more than one action with a single rule, nor can you link more than one trigger to an action with a single rule.

Triggers and rules stand outside packages: you cannot place either in a package. You can, however, create a rule that addresses an action in a package. For example, suppose the `helloLoc` action is in a package named `manageLoc`:

`aio rt:rule:create greetLoc updateLoc /manageLoc/helloLoc`

## Next steps

Return to the [Runtime Reference Documentation Index](index.md).

Return to the [Guides Index](../../guides_index.md).
