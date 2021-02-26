## Running cron

- use system crontab
- use application level cron
- do a HTTP post to a protected endpoint to trigger the cron
- active service pattern is just a cron job. How to pull data periodically? Return the last update date, a.k.a. delta index. MD5 hash the content, or check if the updated date is greater than the previously updated data. If you are pulling a collection, then the greatest last updated date is the indicator.
- do a mutex - ensure the task is executed once only
- make sure the tasks are immutable


## Running distributed cron

- running the cron in multiple instances requires some coordinate - allow only the leader to trigger the cronjob. Else, ensure the operation are immutable.
- use pg advisory locks to prevent multiple process running at the same time
