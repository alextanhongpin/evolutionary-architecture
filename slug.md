## Redirection Strategy

When entity has been updated, url will change/break, and sometimes we want to allow old urls to redirect to new ones:

- for user generated content that always changes, e.g. question title, page title, generate slug with uuid behind, e.g. question-slug-xyz
- for product names, generate slug
- for user name, display name can be duplicate, but slug should be unique, e.g. user John will have slug john-1, john-2

Claiming slug
- note that if we keep the history of the slug, once the slug has been taken, it cannot be used anymore. 
- e.g. slug `john` is used, and John change his name to `jane`. Now both john and jane cannot be used anymore, since we expect user visiting john to redirect to jane.
