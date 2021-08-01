# Bulk Operations

## Persist operations

If the bulk endpoint is time-consuming, persist the request (or commands) first with a single batch reference id, then return a link to track the progress of the batch operations. 

## Use CSV

1. For batch operations involving a lot of fields, allow the users to download the entity fields in a csv first
2. Users can edit the csv at their leisure and upload them again


## Bulk API design

- naming should be based on custom methods, see [3]
- bulk create requires a different design on the Frontend. Ideally bulk operations could be done by uploading spreadsheets
- however, bulk operations with nested resources (one to many, many to many) are hard to be represented on spreadsheet
- validating in bulk appears to be tricky, especially when there are constraints that involves the collection to be validated as a whole (unique field, unique compound field, overlapping time, referential integrity with foreign keys, to name a few)
- there are two kinds of validation, application side and database. In the database, we can set unique constraints etc too. For bulk, it is probably best to let the db handle the constraints mentioned previously, but handle the errors after the db thorws the exception. 
- validation in db should be less prone to data race. However, the errors raised might not pinpoint the exact row that causes such error. 
- An alternative is to not batch the insert, and to run it serially for insertion or update.
- data size may grow which may make the update slow
- batch update could result in all rows having the same timestamp. If the exact timestamp is required, use clock_timestamp for postgres. This usually have impact for testing, especially when seeding, and attempting to retrieve data sorted by timestamp, which might lead to unexpected behaviour
- other times, you might want to disable the trigger that updates the updated at column. For example, you have comments that could be shown on the Frontend that is sorted by updated_at to indicate when it was last bump (another solution is to just dont reuse the updated at, asthe user update might accidentally bump the comment too). When admin performs bulk moderation like correcting grammar editing the comment, it should not affect the updated at date

## References

1. [Zendesk: From 100 Requests to 1: Introducing Our New Bulk and Batch APIs](https://developerblog.zendesk.com/from-100-requests-to-1-introducing-our-new-bulk-and-batch-apis-a5bb294e2132)
2. [Google Developers: Batching Requests](https://developers.google.com/classroom/guides/batch)
3. [Rest API Custom Method](https://cloud.google.com/apis/design/custom_methods)
