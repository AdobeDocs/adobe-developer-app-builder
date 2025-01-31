---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
contributors:
  - 'https://github.com/duynguyen'
title: 'Lesson 3: Develop custom worker calling Photoshop APIs'
---

# Lesson 3: Develop Custom Worker Calling Photoshop APIs

Now that you have the development environment set up locally and can run a basic worker, let's enhance it to do something more complex.

[Adobe Photoshop APIs](https://developer.adobe.com/photoshop/) let you to build plugins and integrations that harness the power of the world’s best image editing and graphic design software to transform creative workflows for users everywhere. In this Code Lab, you use the Photoshop APIs to generate custom renditions in AEM Assets.

You will need the [App Builder Files SDK](https://github.com/adobe/aio-lib-files) to store images, the [Photoshop API SDK](https://github.com/adobe/aio-lib-photoshop-api) to call Photoshop APIs, and UUID to generate unique folder names for renditions of different images. Add them as dependencies in your `package.json` file.

```json
"dependencies": {
    "@adobe/aio-lib-photoshop-api": "0.0.4-beta.4",
    "@adobe/aio-sdk": "^2.3.0",
    "@adobe/asset-compute-sdk": "^2.2.1",
    "uuid": "^8.0.0"
}
```

> Note: At the time of writing, Photoshop API SDK is in beta release and its version is constantly changing. Please visit its [GitHub repository](https://github.com/adobe/aio-lib-photoshop-api) to find out the latest version.

Make sure you run `npm install` after updating the `package.json` file, so that all the npm modules are installed.

Then update your action code in `actions/<worker-name>/index.js` to use the Photoshop API SDK. In this example, it calls the [Remove Background API](https://developer.adobe.com/firefly-services/docs/photoshop/api/photoshop_removeBackground/) to remove the background of an image.

```javascript
const { worker, SourceCorruptError } = require('@adobe/asset-compute-sdk');
const fs = require('fs').promises;
const { Files } = require('@adobe/aio-sdk');
const Photoshop = require('@adobe/aio-lib-photoshop-api');
const { v4: uuid4 } = require('uuid');

exports.main = worker(async (source, rendition, params) => {
    const files = await Files.init();

    // obtain access token and org ID
    const accessToken = params.auth && params.auth.accessToken;
    const orgId = params.auth && params.auth.orgId;

    // init Photoshop SDK client
    const psClient = await Photoshop.init(orgId, params.apiKey, accessToken, files);

    // create a new directory in aio-lib-files with unique name
    const imageId = uuid4();
    const aioSourcePath = `${imageId}/source.png`;
    const aioRenditionPath = `${imageId}/rendition.png`;

    await files.copy(source.path, aioSourcePath, { localSrc: true });

    // call Photoshop API to do cutout processing, and poll status until it's successful
    const result = await psClient.createCutout(aioSourcePath, aioRenditionPath);
    await result.pollUntilDone(1000);

    // download the rendition to local AEM Assets destination
    await files.copy(aioRenditionPath, rendition.path, { localDest: true });

    // clean up files processing folder in aio-lib-files
    await files.delete(`${imageId}/`);
});
```

Because the SDK requires `apiKey` to be passed in the input params, update your `ext.config.yaml` in the `src/dx-asset-compute-worker-1/` folder to include it.

```yaml
operations:
  workerProcess:
    - type: action
      impl: dx-asset-compute-worker-1/worker
hooks:
  post-app-run: adobe-asset-compute devtool
  test: adobe-asset-compute test-worker
actions: actions
runtimeManifest:
  packages:
    dx-asset-compute-worker-1:
      license: Apache-2.0
      actions:
        worker:
          function: actions/worker/index.js
          web: 'yes'
          runtime: 'nodejs:14'
          limits:
            concurrency: 10
          inputs:
              apiKey: $SERVICE_API_KEY
          annotations:
            require-adobe-auth: true
```

Your worker should now be set. 

Run the `aio app deploy` command to deploy your action to test in the development tool UI.

Then execute the command `aio app run` to test it. In the development tool UI, select an existing image or upload a new one, define the rendition request, and click the Run button. You will see the rendition result with the background removed.

There are various options of other photo magics that you can use to enhance your custom worker. Consult the [Photoshop API documentation](https://developer.adobe.com/firefly-services/docs/photoshop/) for further details.
