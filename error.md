## API Errors

Sending back the request id to the client allows them to use them to request for more details of the error if required.
```json
{
  "error": "the error message",
  "error_code": "xyz - the request id for the request for tracing"
}
```

## Sample error messages for validation

Backend/API error messages can be slightly more generic. If there are translations/custom error messages required, do it on the presentation layer.

```
'my hero "value" is not [sad]',
'must be one of [sad]',
'"a&#x28;&#x29;" must be a number',
'"b&#x28;&#x29;" is not allowed',
'"a()" must be a number',
'"b()" is not allowed',
'"notNumber" must be a number',
'"notString" must be a string',
'"notBoolean" must be a boolean',
'"x" must be a number',
'"1" at position 1 contains an excluded value',
'"x" must be a number',
'"blah" must be a string',
'my own value must be a string',
'"0" must be a boolean',
'"bleh" must be a string',
'"length" must be larger than or equal to 3',
'"a" must be one of [a, b, c, d]',
'"u" is required',
'"b" must be a string',
'"a" must be one of [a, b, c, d]',
'"2" must be one of [1, 2]',
'"3" must be one of [1, 2]',
'"5" must be one of [1, 2]',
'"0" must be larger than or equal to 4',
'"1" must be larger than or equal to 4',
'"1" must be less than or equal to 2',
'"2" must be less than or equal to 2',
'"0" must be a number',
'"0" must be larger than or equal to 4',
'"1" must be larger than or equal to 4',
'"1" must be less than or equal to 2',
'"2" must be less than or equal to 2',
'"0" must be larger than or equal to 4',
'"1" must be larger than or equal to 4',
'"1" must be less than or equal to 2',
'"2" must be less than or equal to 2',
'"x" must be a string',
'"x" must be a number',
'"x" must be a number of milliseconds or valid date string',
'"x" is not allowed',
'"z" must be a number',
'"foo" is not allowed',
'"y" must be a number of milliseconds or valid date string',
'"c" is not allowed',
'"b" must be an integer',
'"b" must be a positive number',
'"d" must be a string',
'"options.stripUnknown" validation failed because "options.stripUnknown" failed to meet requirement of having peer modify set to true',
'meet requirement of having peer modify set to true',

```

## Github Error for Rate Limit

```
non-200 OK status code: 403 Forbidden body: "{\n  \"documentation_url\": \"https://developer.github.com/v3/#abuse-rate-limits\",\n  \"message\": \"You have triggered an abuse detection mechanism. Please wait a few minutes before you try again.\"\n}\n"
```


## Others

- how to provide multilingual for errors
- how to capture client side errors more easily 
- how to retry when there are errors
