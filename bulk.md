# Bulk Operations

## Persist operations

If the bulk endpoint is time-consuming, persist the request (or commands) first with a single batch reference id, then return a link to track the progress of the batch operations. 

## Use CSV

1. For batch operations involving a lot of fields, allow the users to download the entity fields in a csv first
2. Users can edit the csv at their leisure and upload them again

## References

1. [Zendesk: From 100 Requests to 1: Introducing Our New Bulk and Batch APIs](https://developerblog.zendesk.com/from-100-requests-to-1-introducing-our-new-bulk-and-batch-apis-a5bb294e2132)
2. [Google Developers: Batching Requests](https://developers.google.com/classroom/guides/batch)
