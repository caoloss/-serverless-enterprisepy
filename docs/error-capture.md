# Capturing non-fatal errors in the Enterprise Dashboard
Your lambda function may throw an exception, but your function handles it in order to respond to the
requestor without throwing the error. One very common example is functions tied to HTTP endpoints.
Those usually should still return JSON, even if there is an error since the API Gateway integration
will fail rather than returning a meaningful error.

For this case, we provide a `captureError` function available on either the `context` or on the
module imported from `'./serverless-sdk'`. This will cause the invocation to still display as an
error in the serverless dashboard while allowing you to return an error to the user.

Here is an example of how to use it from the `context` object:

```javascript
module.exports.hello = async (event, context) => {
  try {
    // do some real stuff but it throws an error, oh no!
    throw new Error('aa')
  } catch (error) {
    context.captureError(error)
  }
  return {
    statusCode: 500,
    body: JSON.stringify({ name: 'bob' }),
  };
};
```

And to import it instead, import with `const { captureError } = require('./serverless-sdk')` then
call `captureError` instead of `context.captureError`.

```javascript
const { captureError } = require('./serverless_sdk')

module.exports.hello = async (event) => {
  try {
    // do some real stuff but it throws an error, oh no!
    throw new Error('aa')
  } catch (error) {
    captureError(error)
  }
  return {
    statusCode: 500,
    body: JSON.stringify({ name: 'bob' }),
  };
};
```