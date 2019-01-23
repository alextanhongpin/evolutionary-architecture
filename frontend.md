## Confirm actions

Instead of a popup asking users if they confirm performing an action, show two number box `[ ][ ]` and ask the users to fill up the 4 randomly generated numbers to validate the action.

```
Enter the number 1264:
[12]-[64] [ Confirm ] 
```

This will allow less mistakes when performing action.


## Events in Tabs

Say we are logged in the web app, and we have several tabs opened. When we click logout, we need to ensure the user is logged out for all browsers. There are several ways to do it:

- storage event: https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/storage/onChanged
- `BroadcastChannel API`

## Autocomplete for input fields
