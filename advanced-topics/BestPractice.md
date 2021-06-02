```    
    /**
     * This method returns 201 Created message <br>
     * And returns a link in the response header to show the created location of the created resource <br>
     * Its like this: <p>
     * Location -> http://localhost:8080/users/4
     *
     *
     * @param user
     * @return
     */
    @PostMapping
    public ResponseEntity<User> save(@RequestBody User user) {
        User savedUser = userRepository.save(user);

        URI uri = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("{/id}")
                .buildAndExpand(savedUser.getId()).toUri();

        return ResponseEntity.created(uri).build();
    }
```
```
    /**
     * Returns 404 message and the body is empty
     * @param user
     */
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public void save(@RequestBody User user) {
        userRepository.save(user);
    }
```

```
    @GetMapping("{id}")
    public ResponseEntity<User> getProduct(@PathVariable Long id) {
        return userRepository.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }
```

```
//    /**
//     * If we don't use Http annotation in UserNotFoundException class, the error message would be 500
//     * which is not true. And the response body looks like:<p>
//     *
//     * <code>
//     * {
//     *     "timestamp": "2017-1....",
//     *     "status": 500,
//     *     "message": "id:1",
//     *     "path": "/users/500
//     * }
//     * </code>
//     * <p>
//     *  Instead we define 404 code in the exception class. And the output look.
//     *  <code>
//     *      {
//     *     "timestamp": "2021-06-02T13:31:37.507+00:00",
//     *     "status": 404,
//     *     "error": "Not Found",
//     *     "path": "/users/23"
//     * }
//     *  </code>
//     *
//     * @param id
//     * @return
//     */
//    @GetMapping("{id}")
//    public User getProduct(@PathVariable Long id) {
//        ConcurrentHashMap
//        return userRepository.findById(id)
//                .orElseThrow(() -> new UserNotFoundException("id:" + id));
//    }
```

```
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {

    public UserNotFoundException(String message) {
        super(message);
    }
}
```