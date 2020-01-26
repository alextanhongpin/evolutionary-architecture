# Mutable vs immutable


When thinking about mutability, we also need to think about the lifetime of the objects we want to mutate. If the lifetime is limited to one function within a request, then it is safe to perform mutation on that object.

However, if the object is used by multiple functions, and can be executed multiple times with varying results, then it is not advised to mutate the object. Also, global states whose lifespan spans across the whole application lifetime should not be mutated too.
