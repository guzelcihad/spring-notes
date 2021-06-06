Container orchestration provides us:
* Auto Scaling: Scale containers based on demand
* Service Discovery: help microservices to find each other
* Load Balancer: Distribute load among multiple instances of a microservice
* Self Healing: Do health checks and replace failing instances
* Zero Downtime Deployments: Release new versions without downtime

We install gcloud and kubectl to the local machine.<br>
Then remove some dependencies in microservices. Because eureka, config, api server etc.
provided by kubernetes. Also add some config for liveness and readiness probes.

Create deployment
``` 
kubectl create deployment currency-exchange --image=guzelcihad/mmv2-currency-exchange-service:0.0.11-SANPSHOT
``` 
Expose service
``` 
kubectl expose deployment currency-exchange --type=LoadBalancer --port=8000
``` 

## PS
Remember we use feign client to make api calls like this
``` 
@FeignClient(name="currency-exchange")
``` 
It was ok with the docker setup. Now in Kubernetes we expose services to communicate so we need to provide hostname.
<br>
If you have a service called `currency-exchange` then Kubernetes expose an environment variable called
`CURRENCY_EXCHANGE_SERVICE_HOST` and assign this variable to PODs. We use this variable to specify hostname in Feign client like this.
``` 
@FeignClient(name = "currency-exchange", url = "${CURRENCY_EXCHANGE_SERVICE_HOST:http://localhost}:8000")
``` 
This simply say that, if I find CURRENCY_EXCHANGE_SERVICE_HOST than I am gonna
make a request this url, otherwise I am gonna make request to localhost:8080.

<p>

Go into the project folder and type to create deployment and service files
``` 
kubectl get deployment currency-exchange -o yaml >> deployment.yaml
kubectl get service currency-exchange -o yaml >> service.yaml
``` 

Then copy all the info from service.yaml and add "---" to the end of the deployment file
and paste service yaml info. This way we merge all the objects into one file.
<br>
Increase replica 1 to 2 in deployment file.
<br>
To see the difference type
``` 
kubectl diff -f deployment.yaml
``` 
And update
``` 
kubectl apply -f deployment.yaml
``` 

## Enable Logging and Tracing API's in Google Cloud
* Search APIs and Services
* Enable api and services
* Enable cloud logging api
* Enable all stack drivers api

## Communication Between Microservices
Instead of using kubernetes supplied environment variables, we can create our own variables.
First, change the name of the env variable in feign client class.<br>
Then open deployment.yaml file and create env. variables.<br>

We define it under containers info.
``` 
    spec:
      containers:
      - image: in28min/mmv2-currency-conversion-service:0.0.12-SNAPSHOT
        imagePullPolicy: IfNotPresent
        name: mmv2-currency-conversion-service
        env:
          - name: CURRENCY_EXCHANGE_URI
            value: http://currency-exchange
``` 
Host name refers to service name. This way kubernetes can distribute the load.<br>
We just harcoded define env. variables instead of that we use configmap.

``` 
kubectl create configmap currency-conversion --from-literal=CURRENCY_EXCHANGE_URI=http://currency-exchange
``` 
## Liveness and Readiness Probes
* If readiness probe is not successful, no traffic is sent
* If liveness proble is not successful, pod is restarted

Spring boot actuator (>= 2.3) provides inbuilt readiness and liveness probes
* health/readiness
* health/liveness

We add the actuator dependency to the project and add the specified configs in application.properties
``` 
management.endpoint.health.probes.enabled=true
management.health.livenessState.enabled=true
management.health.readinessState.enabled=true
``` 
