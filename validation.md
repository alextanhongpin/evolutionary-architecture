## JSON Schema

Use JSON schema to build reusable schemas and host them in a central server. The schemas can be used on both the client (not necessarily frontend, can be another server) and server too as validation. You can also use the schema to generate reusable forms on the frontend.

## Typical mistakes

- `start_date` and `end_date` is not properly formatted. In the case where both dates are the same, the difference will result in 0, and might cause mistakes in calculation involving days. It is always a good practice to set the start date as `00:00:00` and end date as `23:59:59`, and round up the difference.
- whitespaces. trim the strings to ensure the length is valid.
- orientation for different languages (left to right and vice versa).
- emoji in database. For mysql, set the character set to utf8mb4

## Standard Format

- email (go find out)
- url (can use the length of 2083 in mysql, and also suggest the usage of url shortener for client)
- date (store as UTC, and RFC3339)
- password (see password hashing, PHC format algorithm)

