Q1: Http is synchronous, how we use it async

> It is not like HTTP by default is blocking.

> Your OS is what sends a network request & manages connection on behalf of the application you write. OS provides both sync and async access to the connections.

> In Java with spring-web module with multiple threads till we get the response, we would wait. WebClient uses Netty client behind the scenes which in turn uses the OS async APIs   (make the kernel notify us if we get any response for any of the connections) for the network connections which makes it non-blocking.