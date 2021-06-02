Both `@RequestParam` and `@PathVariable` is used to extract values from the HTTP request, there is a subtle difference between them.<br>

`@RequestParam` is used to get the request parameters from URL, and known as query parameters and used in ***Spring RESTFul framework***<br>

`@PathVarible` extracts values from URI and used in ***Spring RESTFul framework***<br>

For example, if the incoming HTTP request to retrieve an album on topic "Classics" is
```
http://localhost:9090/musicshop/order/101/receipts?date=27-11-2020
```
here you can use `@RequestParam` annotation to retrieve the query parameter date and
ou can use `@PathVariable` to extract the ordered i.e., "101" .

```
@RequestMapping(value="/order/{orderId}/receipts", method=RequestMethod.GET)
public List listMusicInvoices(@PathVariable("orderId") int order,
@RequestParam(value = "date", required = false) Date orderedDate) {
}
```

`@PathParam` reads the value from a path part of the called URI and is location-dependent and used in ***Jersey(JAX-RS) framework***.<br>

`@QueryParam` is used to read the values from QueryParameters of a URI call and used in ***Jersey(JAX-RS) framework***.<br>

and are passed as a key-value pair and therefore their order is irrelevant to more than one `QueryParam`.<br>

For example
```
https://www.albumstore.com/<@PathParam>/?queryParamName=<@QueryParam>
```
Lastly, `@BeanParam` is allowed you to inject an application-specific class whose property methods or fields are annotated with any of the injection parameters. and it was introduced in JAX-RS 2.0 specifications.