In rest, we have resource and representations.
Representations are the json model that we return in output.<p>

We just add `jackson xml` dependency and specify `Accept: application/xml` in the request header.

PS: 
> Accept - what type of response are you expecting?
Content-Type - what type of content does the body of your request contain?
<br>

We can do it also adding just this annotation  
```
@RequestMapping(produces = MediaType.APPLICATION_XML_VALUE)
```