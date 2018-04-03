# Search

There are several ways to integrate search into your application:

- frontend UI with search input and select dropdowns for filter. Backend is using RDBMS with simple text search and filter queries. Good for starters when search is really not important.
- use ElasticSearch or Solr. Backend requires indexing the data to these secondary storage. Can perform much more complicated searches with scoring, but the search query must still be constructed manually for the best performance.
- use Named Entity Recognition (NER) to extract relevant features before constructing the query to send to Elasticsearch or Solr. This allows the frontend ui to be google-like, accepting any text input and predicting the context of the input.

Some of the "myth" when designing search:

- I want to create listings that are city specific - do I need to hardcode them? Yes, and no. If you want to ensure the data is clean, moderate them. But do not index what is not created. If there are 14 cities, and you only have data for two cities, just index what exists.
