### DSL folder location
The location of the DSL folder, containing all of the DSL files, can be configured using the `application.config-path` property:
```
application:
    config-path: user/desired/location
```

### Exception handling
* `stopInCaseOfException` - defines whether DSL execution should be halted when an exception is encountered during processing or not.
  * if `true`, then on exception processing of further steps is halted and a response is immediately returned
  * if `false`, then processing of further steps continues and a response is returned when processing is done
```
application:
    stopInCaseOfException: true
```

### Incoming requests
* `incomingRequests.allowedMethodTypes` - defines the allowed method types for Ruuters DSL endpoint. Any request with an invalid method type is "denied" service.
```
application:
  incomingRequests:
    allowedMethodTypes: [ POST, GET ]
```

### Max step recursions
* `maxStepRecursions` - defines how many times can one step be executed to execute a step a specific number of times and to avoid infinite loops.
* When a step has reached its maximum recursions, DSL will continue from a next step that was not in a loop.
* Max recursions can also be defined on a step level, which will override this global limit only if it is smaller than the global limit.
* More information about step level max recursions [here](./general/max-recursions.md).
```
application:
    maxStepRecursions: 10
```

### External forwarding
If the following values are defined, then every incoming request is forwarded to the defined endpoint.
* `method` - accepted values: `GET`, `POST`
* `paramsToPass.get` - whether to forward query params from incoming request
* `paramsToPass.post` - whether to forward body from incoming request
* `paramsToPass.headers` - whether to forward headers from incoming request
* `proceedPredicate.httpStatusCode` - defines the accepted http status codes for proceeding with DSL processing. If the external forwarding request
  receives a response not included in the list, then the request is not processed.
    * values can be defined as single status codes: `200`, `201`, `202`...
    * values can be defined as a range, using `..` special syntax: `200..202`
```
application:
    incomingRequests:
        externalForwarding:
            method: POST
            endpoint: "https://turvis/ruuter-incoming"
            paramsToPass:
                GET: true
                POST: false
                headers: true
            proceedPredicate:
                httpStatusCode: [ 200..202, 204 ]
```

### Final Response status code

Final response status code can be defined in application.yml. If defined, all final responses by Ruuter will respond with this status code no matter what.
There are two options. One is for DSLs that have a response and the other one is for DSLs that do not have a response.

The business value lies in not allowing perpetrators to fish for exceptions, weaknesses, etc in back-end systems via requests from public network.

Nevertheless, applications (including Ruuter's) log files contain actual HTTP response codes, being the appropriate source for debugging, alerting, etc.
```
finalResponse:
    dslWithResponseHttpStatusCode: 200
    dslWithoutResponseHttpStatusCode: 300
```

### CORS

CORS CrossOrigin - whitelists domains which are permitted to access this application.
CORS configuration reads CrossOrigin URLs from `application.yml`.

```
application:
    CORS:
        allowedOrigins: [https://test.buerokratt.ee, https://admin.test.buerokratt.ee/, https://tim.test.buerokratt.ee/]
```

### Default service

Default service is a service file that will be executed when the request status code is not within the allowlist of HTTP response codes.

Default service can be defined in the `application.yml` file by the name of `defaultServiceInCaseOfException`. The service itself is yet another service in `/DSL/POST` folder. Both `body` and `query` parameters are optional and covered here as an example.

```
defaultServiceInCaseOfException:
    service: default-service
    body:
      someVal: "Hello World"
    query:
      anotherVal: 123
```


### Http request default headers

There is an example for POST requests. To add default headers to other requests in application.yml, define it in application.yml, also in
ApplicationProperties class the same way as HttpPost is defined and use addHeaders method from HttpQueryArgs to add these default headers to the request.
