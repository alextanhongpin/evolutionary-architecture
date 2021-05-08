how to blacklist jwt?

all we need to do is to store the user last logged out date. if the token is created before the last logged out date, then they are invalid. note that since a user can be signed in multiple devices or platform, we need a way to uniquely identify them.


# frontend scenario

user is logged in, user make a long running request, then user logs out before the request is completed. the state is mutated after that. how to prevent this?

1. abort fetch on log out
2. store timestamps after logging out. any private request that enters after that (we know when it was requested) is discarded
3. add versioning? 
