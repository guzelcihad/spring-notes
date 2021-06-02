Customizing your services for different people around the world.<br>
We define `message.properties` files for exery language and write a code like this

```
@Autowired
	private MessageSource messageSource; 

@GetMapping(path = "/hello-world-internationalized")
	public String helloWorldInternationalized() {
		return messageSource.getMessage("good.morning.message", null, 
									LocaleContextHolder.getLocale());
	}
```

* `LocaleContextHolder.getLocale()` is an abstraction that handle the data come from Request Header. IF we dont use it
we need to use `@RequestHeader(name="Accept-Language", required=false Locale locale)` in method parameter.