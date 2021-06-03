Companies use different techniques for versioning. Each of them has trade-offs.<br>
For ex: twitter use uri versioning, amazon use parameter versioning.<br>

The basic approach is uri versioning. Separates version in uri like this<br>
`localhost:/v2/person` or `localhost:v1/person`.

In the back end you can have different model for this names for ex PersonV1, PersonV2.<br>
And in the controller you expose different api for this.

## Parameter Versioning
Another way is parameter versioning.
We use 

`@GetMapping(value="/person/param", params="version=1")` and type 
`localhost:8080/person/param?version=1` in the url.

## Header Versioning
`@GetMapping(value="/person/header", headers="X-API-VERSION=1")` and type 
`localhost:8080/person/header` and set `X-API-VERSION` in header value 1.

## Accept Header Versioning
`@GetMapping(value="/person/produces", produces="application/vnd.company.app-v1+json")` and type 
`localhost:8080/person/produces` and set `Accept` in header value `application/vnd.company.app-v1+json`.