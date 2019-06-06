# serverless-example

This project demonstrates the serverless offline issue found at:
https://github.com/dherault/serverless-offline/issues/676

The serverless.yml file was copied from:
https://www.npmjs.com/package/serverless-offline#request-body-validation

It contains a function with the path `hello-world` which requires a body containing a property `message` with a minimum length of 2.

## The issue

It works fine if we test it by running:
```
serverless offline start
```
And then:
```
curl --header "Content-Type:application/json" --data "{\"message\":\"test\"}" http://localhost:3000/hello-world
```
The response will be:
```
#{"message":"Succes!"}
```

However, when we change integration to lambda by uncommenting `#integration: lambda` in `serverless.yml`, and restart serverless offline, the above request responds with an error:
```
{
    "errorMessage":"Invalid request body for 'helloWorld' handler",
    "errorType":"SyntaxError",
    "stackTrace":[
        "SyntaxError: Unexpected token o in JSON at position 1","at JSON.parse (<anonymous>)",
        "at Object.validate (/home/me/projects/serverless-example/node_modules/serverless-offline/src/requestBodyValidator.js:18:35)",
        "at resolve (/home/me/projects/serverless-example/node_modules/serverless-offline/src/index.js:965:40)",
        "at new Promise (<anonymous>)",
        "at handler (/home/me/projects/serverless-example/node_modules/serverless-offline/src/index.js:680:20)",
        "at module.exports.internals.Manager.execute (/home/me/projects/serverless-example/node_modules/@hapi/hapi/lib/toolkit.js:41:33)",
        "at Object.internals.handler (/home/me/projects/serverless-example/node_modules/@hapi/hapi/lib/handler.js:46:48)",
        "at exports.execute (/home/me/projects/serverless-example/node_modules/@hapi/hapi/lib/handler.js:31:36)",
        "at Request._lifecycle (/home/me/projects/serverless-example/node_modules/@hapi/hapi/lib/request.js:312:68)"],
    "offlineInfo":"If you believe this is an issue with the plugin please submit it, thanks. https://github.com/dherault/serverless-offline/issues"
}
```

I believe this might be an issue with the plugin, since both cases work fine after deploying to AWS.

### Aditional notes

When disabling model validation ( `serverless offline start --disableModelValidation` ) the response is `{"message":"Succes!"}` in both cases.
