# Debugging Node.js Actions

The easiest way to debug your actions is to use a local Node.js server and run the action code on your machine. But this is not the environment used by I/O Runtime to execute those actions. `wskdebug` assists with debugging and live development, and is integrated with Visual Studio Code.

## How it works

Using `wskdebug`, an action deployed on I/O Runtime is replaced with an agent. The agent sends the activation to the local container, and takes the activation result in the local container back to the original activation. The action actually executes on your local container, and this local environment has its debug port open so you can connect and IDE to it.

In the unlikely event that the agent is killed by I/O Runtime while it is running, you'll need to restore the original action content.

## Installing `wskdebug`

You'll need Node.js, version 10 or newer, and a local [Docker environment](https://www.docker.com/products/docker-desktop). You can get `wskdebug` from this [GitHub repository](https://github.com/adobe/wskdebug) or use npm to install it:

```
npm install -g @adobe/wskdebug
```

Assuming you have Docker configured locally, you need to run `wskdebug` in Terminal to pull the latest image used by I/O Runtime to execute Node.js actions onto your machine:

```
 wskdebug --inspect-brk=11932 <ACTION NAME> <FULL PATH TO THE SOURCE FILE> -l 
```

## Visual Studio code example

Before you can debug your action code, open the source file for your action and create the [launch.json](https://code.visualstudio.com/docs/editor/debugging#_launch-configurations) with this content:

```
"configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "wskdebug <ACTION NAME>",
            "runtimeExecutable": "wskdebug",
            "args": [ "<ACTION NAME>", "${workspaceFolder}/<ACTION SOURCE FILE>", "-l" ],
            "localRoot": "${workspaceFolder}",
            "remoteRoot": "/code",
            "outputCapture": "std"
        }
    ]
```

Next, place breakpoints in your action code and launch the debugger. Once you can confirm from the Debug Console window that the debugger was launched, invoke the action. When your action hits the first breakpoint, you can inspect the variables, step through, and even change the code. Once your code is saved, the action will automatically be re-executed.

For details of how `wskdebug` works, its limitations, and how to use it to its full potential, please read the [Adobe's `wskdebug` documentation](https://github.com/adobe/wskdebug).

## Next step

Return to the [Guides Index](../guides_index.md).
