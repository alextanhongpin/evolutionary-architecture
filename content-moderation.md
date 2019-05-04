## Comment
How to create a content-moderation system for comments?

- can also be categorized as approval system
- aside from comment, can be used for content suggestion


## Content Suggestion

Users can submit multiple suggestions (e.g. reviews for a shop/place they visited, address, contact number) or validate if the information is correct.

Using [EAV](https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model) for more flexible changes.

- https://www.mediawiki.org/wiki/Multi-Content_Revisions/Database_Schema
- https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model
- https://stackoverflow.com/questions/17075577/database-design-with-change-history
- https://stackoverflow.com/questions/39281/database-design-for-revisions
- http://database-programmer.blogspot.com/2008/07/history-tables.html

## Thread Design

- https://www.vertabelo.com/blog/technical-articles/database-model-for-an-online-discussion-forum-part-2
- https://www.vertabelo.com/blog/technical-articles/database-model-for-an-online-discussion-forum-part-3


## Statuses

A content to be moderated can be in different states. For example:

- pending
- approved
- rejected
- postponed
- stale (?)
- expired (?)

When the content is created, it will normally be in the `pending` state. The moderator/ops/admin can then `approve` or `reject` it. If the content is `approved`, then it can be published (made public) to the user, or if it's already public, then a status can be shown on the UI to indicate that the content has been approved. The opposite applies for the `rejected` post, where the content would normally be unpublished. The moderator can provide a reject reason to the end user to indicate the reason of rejection. The `postponed` status is slightly unique. Since the approval requires manual intervention, and sometimes an additional approval externally (outside the system), the moderator can first choose to postpone the approval. This gives the moderator some buffer before processing the content, and for the UI, we can display a `processing` status instead of `pending` to indicate that the content is already seen by the moderator, but not yet processed. Another advantage is we can setup an alert system using the `postponed_till_date` to remind the moderator to process it. The same applies for the `pending` status. The content should not be in the `pending` state forever, moderator should be notified 1 week later (example only, can be other periods) to approve it, and the status should change to `stale`. If there are no further actions taken, the status will change to `expired` and be removed automatically from the system.

NOTE: Take a look at multi-approval system.

Keywords:

- content moderation
- multi-content revision
- history table
