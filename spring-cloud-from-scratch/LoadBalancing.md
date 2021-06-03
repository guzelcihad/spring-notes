Currency conversion service can request currency exchange service. But there are
multiple instances of exchange services. So, we can not define url hardcoded.<br>
We just remove this property from Proxy feign client 
```
@FeignClient(name="currency-exchange", url="localhost:8000")
```
it becomes
```
@FeignClient(name="currency-exchange")
public interface CurrencyExchangeProxy
```
## How?
Inside the conversion ms, there is a loadbalancer component which interact
between naming server, finding instances and do automatic load balancing.

<p>
To be able to start a microservice instances multiple times, just add this
JVM options in run configuration.
```
-Dserver.port=8001
```