## Going offline

Keeping a local cache on the device/web can improve the responsiveness of the application, especially when it needs to fetch a lot of data, or the data needs to be recomputed on the fly. Example of such data is expenses transactions. If we want to allow users to fetch transactions for all the period and calculate the weekly, monthly, yearly or a specific period of time, we need to fetch all the data. But fetching all the data can be resource-intensive. The ideal approach is to fetch all the data that is after the last fetched timestamp, a.k.a delta index. However, the complexity lies in keeping the data in sync with the online database. This can be tricky especially when there are network failure and we do not know where is the single source of truth (we assume it will always be the server, then let the user retry the last failed operations).

## Simplest sync server

- The initial idea is to hash the data using md5 and compare the hash of the data on the server against the client. But then hashing again is computationally expensive.
- The second idea is to check the last updated time. In mysql, we can always set the updated at date time to update whenever there is a change.
- On the client side, if they are offline, then just push the items to be updated in a local queue/database. When the app is back online, then update it. There can only be a max of 10 items to be updated when it is offline. 
- The logic to update is as follow: The content can only be updated if the created at date (t0) of the content of the client is greater than the updated at date (t1) of the content on the server side and t0 is less than NOW. This is to avoid having the content to be overwritten by the stale  data (e.g. is user is offline on the mobile, and has a content that has not yet been synced online. The user update the content on the web, and then goes online on the mobile. The mobile content should not be synced, since the server already has the latest content.
e.g.

- User is online. User create book `{date: 1 Jan 2019, updated_at: 10:00pm}`.  User goes offline.
- User is offline. User update book `{date: 1 Jan 2019, created_at: 10:11pm}`. User goes online.

```
if book_local.created_at > book_server.updated_at:
	can_update
```
