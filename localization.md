What is the best database structure to keep multilingual data
- for localization, use `react-native-localize` + `i18n`
- pass in Accept-Language in header to toggle language, e.g. Accept-Language: en or Accept-Language: ja
- only one language will be returned (no array of possible languages), if a json has the field insurer name, only the localized version will be returned
- if there's only one language, how to know what the text means? prepare two devices, one english, another japanese
- only strings (name) will be localized, date time, currency localization should be done on the client side. Why? If I send premium back as localized string (comma separated), then client side have to convert it back to float, too many unnecessary conversions. If I send formatted date, and the client needs to display it in different format, I won't want to send back all possible format.
- create localization files for english and japanese. Split by:
  - 1) screens, e.g. login_header, login_subheader, login_reset_password, settings_options
  - 2) split by component, e.g. button_ok, button_cancel
  - 3) global, normally the brand name, or terms that occured everywhere, e.g. global_policy, global_user
