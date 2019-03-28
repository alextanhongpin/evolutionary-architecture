## Use RFC3339

- store the timezone information alongside the datetime in utc
- check to ensure the time still matches for products that might expired/offered in different time zone (12.00 am UTC) is different in different zones

## Expiring products

- ensure products expiry matches the end date of the user + the locale the user bought the items

## Launching products in different timezone

- ensure the local time matches. 8.00 AM UTC time release might be different in different zone

## weekend

- weekdays starts on Monday, not Sunday (standardize).


## Date calculation
- make sure date calculation happens on the server side
- extract the timezone from the user's request 
