---
keywords:
  - Adobe I/O
  - Extensibility
  - API Documentation
  - Developer Tooling
title: Optimizing App Builder Apps
---

# Optimizing App Builder Apps

There are many ways to optimize a web app: for security, performance, or operational cost, to name a few. Here are some techniques to help you get the most from your App Builder applications.

## Caching HTTP responses

This method works extremely well if your action returns some results based on repeated user inputs as query params, for example details about a webshop item or visualization of static data. In such cases your back-end action is invoked once only: subsequent requests receive results directly from the cache by the expiry time. Serving results from cache is not only faster, but saves the cost of action invocations.

To configure the cache, use the `Cache-Control` directive. Below is an example of an action that sets the cache with a TTL of 30 minutes. In the response object you will find an entry with `X-GW-Cache: HIT` or `X-GW-Cache: MISS`, depending on whether the answer was returned from cache or not.

```javascript
async function main (params) {
  return {
      headers: {
        'Cache-Control': 'max-age=1800'
      },
      statusCode: 200,
      body: { message: 'I am cached for 30 minutes.' }
  }
}
```

When you test this functionality for web action using Postman or a web browser with Developer tools opened, be sure that `Cache-Control` is not set automatically for all the requests.

## Returning large response payloads

An Adobe I/O Runtime action can return a [response payload of 1MB](../runtime_guides/system_settings.md) - enough for most use cases. If your action will return a larger payload, we provide a scalable solution with [App Builder Files SDK](https://github.com/adobe/aio-lib-files). It allows you to [persist a binary file to the blob storage](https://github.com/adobe/aio-lib-files/blob/master/doc/api.md#Files+write), obtain [a temporary downloadable URL](https://github.com/adobe/aio-lib-files/blob/master/doc/api.md#Files+generatePresignURL) and return an [HTTP Redirect response](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/302) to the file with this URL. Here is a demonstration:

```javascript
const fileLocation = '/private-dir/large-image.png'
const files = await Files.init()
await files.write(fileLocation, fileContent)

// Generate a presigned URL of the file that is valid for 60 seconds only
const presignUrl = await files.generatePresignURL(fileLocation, { expiryInSeconds: 60 })

return {
  headers: { location: presignUrl }, 
  statusCode: 302
}
```

## Implementing move operations for the App Builder Files SDK

When using using [App Builder Files SDK](https://github.com/adobe/aio-lib-files) to handle large files within a Runtime action, you may need to move those files to another location in the underlying cloud storage, for example to archive them when computing is over.

We have not exposed a move operation in our abstraction, because:

- Some cloud storage APIs do not offer an atomic move operation, forcing a tradeoff between abstraction and consistency. Users might expect files operations on single files and folders to be atomic when they're not.
- It's easy to implement and test the abstraction at application level.

Here is our recommendation to implement the move operation at application level using [App Builder Files SDK](https://github.com/adobe/aio-lib-files) primitives:

```javascript
/**
 * Note: this operation is not atomic.
 * Moves files from one location to another in the remote storage. 
 *  
 * @param {Files} files the files instance
 * @param {string} src source file/folder
 * @param {string} dest destination file/folder
 * @param {object} [options={}] move options
 * @param {Function} [options.progressCallback] a function that will be called every
 *   time the operation completes on a single file, the srcPath and destPath to the moved
 *   file are passed as argument to the callback `progressCallback(srcPath, destPath)`
 */
async function move (files, src, dest, options = {}) {
  try {
    const res = await files.copy(src, dest, { progressCallback: options.progressCallback })
    await files.delete(src)
    return res
  } catch (e) {
    e.message = `Move operation failed, reason: ${e.message}`
    throw e
  }
}
```

Here are some usage examples:

```javascript
const files = await Files.init()
await move(files, 'my/remote/src/folder/', 'my/remote/dest/')
await move(files, 'my/remote/src/folder/file.txt', 'my/remote/dest/file2.md') // will move and rename the file
await move(files, 'my/remote/src/folder/file.txt', 'my/remote/dest/') // will move file.txt to dest folder
await move(files, 'my/remote/src/folder/', 'my/remote/dest/') // move folder to the dest folder
await move(files, 'my/remote/folder/', 'my/remote/dest') // will rename folder to dest
```

## Next step

Return to [Guides Index](../guides_index.md).
