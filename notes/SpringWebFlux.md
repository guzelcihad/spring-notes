
Reactive is an architectural style that allows an application to react to changes by being able to scale up and down
or recover from failures. We can also write a part of an application with reactive programming style
but this doesn't mean that the whole system is reactive.

A reactive system is;
* Responsive : it has to respond quickly to all users under all conditions.
* Resilient
* Scalable : up and down based on demand
* Message Driven : Reactive systems are driven by messages to ensure that the components of the system can be scaled independently
and loosely coupled.

Sometimes messages are called events but they are not the same thing. Unlike event messages has explicit destination.
When events happen there are listeners to listen those events.

Reactive programming involves three concepts.
* Non-blocking programming
* Asynchronous programming
* Functional/Declarative

## Non-blocking
Most of the time we use blocking operations like call something on db. Another example can be web servers.
They have a thread pool and request are handled by this threads via blocking API.
<br>
Those threads have their own stack and consumes memory. So when you need to scale up to more than 500 req/s
you need to increase the thread size. And that means you need more memory. So this is an example of ineffiecient usage of memory
and CPU cycles because program does nothing when it is waiting some resources.
<br>
Reactive web applications adopt non-blocking servers generally based on the event loop model.
In this model there is small number of thread handling requests.
<br>
When a request is handled by a thread, it is divided into events. These events wait to be processed.
But the important thing here is waiting time is very small. Because when there is blocking operation
needed like connecting database, connecting file system or intensive computation, these events handled
by other available worker threads. And when the operation is done a new event is generated a signal that the operation
is completed.
<br>
Thats why web servers work like this type are more scalable and handle more request.

## Asynchronous Programming
It is about executing operation in background so it can execute other things without waiting operation to finish.
We use callback to get the result when the operation finishes.
We can use Thread pools, ForkJoin Framework, Parallel Streams or Completable Future to implement callback operations.

## Functional/Declarative
Reactive libraries use concepts of functional programming.
Functional programming is more declarative and maintainable then imperative programming. 
In imperative programming you have to explicitly tell the program what to do and how to do it.
In functional programming style we use pure functions which is more readable, maintainable and also
use the power of modern multicore CPU's.
<br>
We process the sequence of events and this known as stream.
The iniative to standardized libraries that process the streams in an asynchronous, non-blocking way with
backpressure capabilities is called Reactive Streams.
This is different than the Java Stream API. The both have similar API but they work in a different ways.
<br>
The Stream API connects to a source, pulls the values and it can be used only once.
<br>
In a Reactive Stream values are not pulled, they are pushed through the pipeline of operators
when you subscribe and they have backpressure mechanism

## Spring MVC vs Spring Webflux
put image here

## Reactive Stack
The important thing is reactive systems is all the layers in reactive stack needs to be
non-blocking and asynchronous.
You have to use non-blocking server, web layer needs to be webflux, data layer needs to be reactive spring data.
Spring also have reactive starters in spring boot 2.

Webflux supports many implementation libraries like:
* Reactor
* RxJava
* RxJava2
* CompletableFuture

Project Reactor is the default and preferred reactive library to work with WebFlux.
NOTE: Reactive programming,reactive manifesto, reactive systems can be seem same but they are different.
Reactive programming is driven by events

# Reactive Streams
The purpose of the reactive streams is to provide a contract:
* Asynchronous
* Non-blocking
* Backpressure
to support this functionality. 

developed by lightbend, netflix,twitter