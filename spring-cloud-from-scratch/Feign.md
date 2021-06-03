Add this dependency.
```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-openfeign</artifactId>
		</dependency>
```

* Add `@EnableFeignClients` to the main class
* Add an interface and add the method signature. Take method signature from callee class.

```
@FeignClient(name="currency-exchange")
public interface CurrencyExchangeProxy {

    @GetMapping("/currency-exchange/from/{from}/to/{to}")
    public CurrencyConversion retrieveExchangeValue(
            @PathVariable String from,
            @PathVariable String to);

}
```
Than call
```
CurrencyConversion currencyConversion = proxy.retrieveExchangeValue(from, to);
```
