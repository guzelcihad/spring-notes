## HATEOAS-Hypermedia as the Engine of the Application State
For ex. when we open a github repositories page, we can see all the repositories we have.<br>
But in addition to the repositories, we can also see fork, start operations.<br>
What if we can add such kind of features to the REST. <br>
This where HATEOS comes in to play.

<p>
How is it in action? When a resource gets called within API, the API returns additional
links related with the resource that we can perform an action on it.
<p>

In Controller classes, we return `EntityModel<T>` object. 
```
//create entity model
EntityModel<USer> model = EntityModel.of(user);

//create related methods.  we define method at the end
// in this example getAllUsers()
WebMvcLinkBuilder linkToUsers =
        linkTo(methodOn(this.getClass())).getAllUsers();

// all users is the alias for getAllUsers method
model.add(linkToUsers.withRel("all-users"));

return model;
```

We can also use ResponseEntity as well.
```
@GetMapping("/users/{id}")
	public ResponseEntity<Object> getUser(@PathVariable int id)
	{
		User user = userS.getUser(id);
		
		if(user!=null)
		{
			Resource<User> resource = new Resource<User>(user);
			ControllerLinkBuilder linkTo = linkTo(methodOn(this.getClass()).getUsers());
			resource.add(linkTo.withRel("All Users"));
			return ResponseEntity.ok(resource);//OK p/ user encontrado
		}
		
		return ResponseEntity.notFound().build();//Not Found p/ user ñ encontrado
	} 
```