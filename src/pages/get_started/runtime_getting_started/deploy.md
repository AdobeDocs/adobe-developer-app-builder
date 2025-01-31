# Deploy Your First Adobe I/O Runtime Action

In this step, you will create an I/O Runtime action, test it, and deploy it. 

## Create a function and an action

Create the following function in any editor and save it as `hello.js`:

```js
function main(params) {
  return { payload: 'Hello ' + params.name };
}
```

<InlineAlert slots="text"/>

Next, open a command-line window and navigate to the folder where you saved the function. Create a Runtime action from your function with this command:

`aio rt:action:create hello hello.js`

This command uploads the code contained in `hello.js` to Runtime and stores it as an action named `hello`. If the command is successful, you will see this acknowledgement in the command-line window:

`ok: created action hello`

Your function is now deployed as a Runtime action.

## Test the action

Now invoke the new action:

`aio rt:action:invoke --result hello --param name <your name>`

You should see this output:

```json
{
  "payload": "Hello <your name>"
}
```

## Next step

Next, review the results of your action, at [Retrieve Action Invocation Results](activations.md).
