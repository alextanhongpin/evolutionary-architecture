## Writing requirements

There are many ways to gather requirement, and most of the time, they tend to overlap. Say we want to design a url shortener service, there are many ways to describe the requirements:

With user story:
- As a user, I want to shorten the long url to post on Twitter, so that I am not capped by the 140 characters limit.

With functional requirement:
- System should return a short url given a long url.

With usecases:
- Shorten url use case

What are the differences in the approaches above? One thing to note is that there are different perspectives to look at the problem, one from the user side (and the role they are holding, can be many users with different roles), and another from the system side.

Most requirements will deal with both the user and system side, and it involves the `interactions` and `expectations`. The user interaction will normally result as an input to the system that is defined through a loose interface. In the case of the url shorter service, the user submits the shorten url through the web ui, which is then send to the system though an API.

Both user and system have different expectations - the user will expect to get a shortened url, and the system expects the long url submitted to be valid, and is stored safely in the database. In other words, the system expects the business rule (validates url) to be fulfilled. But in order to accomplish that, the system still needs to interact with other infrastructure such as database, api layer etc.

A good requirement would be able to distinguish both the roles, but at the same time able to connect them together to describe the full flow of the use case.

## Dealing with planned, but incomplete features

Use the in-out lists to manage the features and sort their priorities

- shorten url (in)
- allow user to select short url (out)
