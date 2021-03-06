<!-- Do not edit this file. It is automatically generated by API Documenter. -->

[Home](./index.md) &gt; [kibana-plugin-server](./kibana-plugin-server.md) &gt; [SavedObjectsClientContract](./kibana-plugin-server.savedobjectsclientcontract.md)

## SavedObjectsClientContract type

\#\# SavedObjectsClient errors

Since the SavedObjectsClient has its hands in everything we are a little paranoid about the way we present errors back to to application code. Ideally, all errors will be either:

1. Caused by bad implementation (ie. undefined is not a function) and as such unpredictable 2. An error that has been classified and decorated appropriately by the decorators in [SavedObjectsErrorHelpers](./kibana-plugin-server.savedobjectserrorhelpers.md)

Type 1 errors are inevitable, but since all expected/handle-able errors should be Type 2 the `isXYZError()` helpers exposed at `SavedObjectsErrorHelpers` should be used to understand and manage error responses from the `SavedObjectsClient`<!-- -->.

Type 2 errors are decorated versions of the source error, so if the elasticsearch client threw an error it will be decorated based on its type. That means that rather than looking for `error.body.error.type` or doing substring checks on `error.body.error.reason`<!-- -->, just use the helpers to understand the meaning of the error:

\`\`\`<!-- -->js if (SavedObjectsErrorHelpers.isNotFoundError(error)) { // handle 404 }

if (SavedObjectsErrorHelpers.isNotAuthorizedError(error)) { // 401 handling should be automatic, but in case you wanted to know }

// always rethrow the error unless you handle it throw error; \`\`\`

\#\#\# 404s from missing index

From the perspective of application code and APIs the SavedObjectsClient is a black box that persists objects. One of the internal details that users have no control over is that we use an elasticsearch index for persistance and that index might be missing.

At the time of writing we are in the process of transitioning away from the operating assumption that the SavedObjects index is always available. Part of this transition is handling errors resulting from an index missing. These used to trigger a 500 error in most cases, and in others cause 404s with different error messages.

From my (Spencer) perspective, a 404 from the SavedObjectsApi is a 404; The object the request/call was targeting could not be found. This is why \#14141 takes special care to ensure that 404 errors are generic and don't distinguish between index missing or document missing.

\#\#\# 503s from missing index

Unlike all other methods, create requests are supposed to succeed even when the Kibana index does not exist because it will be automatically created by elasticsearch. When that is not the case it is because Elasticsearch's `action.auto_create_index` setting prevents it from being created automatically so we throw a special 503 with the intention of informing the user that their Elasticsearch settings need to be updated.

See [SavedObjectsErrorHelpers](./kibana-plugin-server.savedobjectserrorhelpers.md)

<b>Signature:</b>

```typescript
export declare type SavedObjectsClientContract = Pick<SavedObjectsClient, keyof SavedObjectsClient>;
```
