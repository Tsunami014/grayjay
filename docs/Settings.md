# Settings
In a plugin's `config.json` file, you can specify settings that the user can change to customize the plugin. This is done by adding a `settings` field to the config file, which has a list of settings in order. Each setting is an object with the following fields:
```json
{
    "settings": [
        {
            "variable": "varname", // Optional; the variable name to store the value in. If not present, the name is used instead.
			"name": "Example variable", // The name of the setting present to users
			"description": "This is an example variable!", // A description of the setting
			"type": "Dropdown", // The type of setting (e.g. boolean, dropdown)
			"default": "0", // Optional; the default value of the setting
			"warningDialog": "Make sure you want to enable!", // Optional; a warning dialog that the user gets when enabling the setting, giving the user a chance to cancel
            "dependency": "varname2", // Optional; another setting that needs to be enabled for this setting to show up at all
			"options": ["Option 1", "Option 2"] // Optional; A list of options for dropdown settings
        }
    ]
}
```

# Types of settings
## Header
A header is a setting that doesn't have a value, but is used to display info about what subsequent settings do. It uses the following fields:
```json
{
    "variable": "varname", // Optional
    "name": "Header 1",
    "description": "A description about what this header is for",
    "type": "Header"
}
```
The name of the header is large and white, and the description is smaller and gray; but still larger than regular settings' descriptions.

## Boolean
A boolean setting is a simple on/off switch. It uses the following fields:
```json
{
    "variable": "varname", // Optional
    "name": "Example boolean",
    "description": "Example description",
    "type": "Boolean",
    "default": "true" // Optional
}
```
For the `default` field, `true`, `True` or `1` all are valid for True, otherwise it is treated as False (but it's still good practice to use `false`, `False` or `0`)

These will appear as a togglable switch in the settings.

## Dropdown
A dropdown setting is a setting where the user can choose from a list of options. It uses the following fields:
```json
{
    "variable": "varname", // Optional
    "name": "Example dropdown",
    "description": "Example description",
    "type": "Dropdown",
    "default": "0", // Optional
    "options": ["Option 1", "Option 2"]
}
```
The `default` field should be the index of the default option in the `options` list.

# Other settings fields
## `warningDialog`
The `warningDialog` field is optional, and if present, will show a dialog to the user when they enable the setting. This is useful for settings that might have unintended consequences or that the user might not be aware of, or to confirm that the user really wants to enable the setting.

## `dependency`
The `dependency` field is optional, and if present, will only show the setting if the (Boolean) setting specified in the `dependency` field is enabled. This is useful for settings that are only relevant if another setting is enabled; as if the setting is not enabled, the dependent setting will not be visible at all.

# How to use settings
A source has 2 ways settings can be set; `source.setSettings` or `source.enable`.

If you don't plan on modifying anything based off of the settings and just deal with them when needed, you could have a very simple setup like so;
```javascript
var _settings = {};
source.setSettings = function(settings) {
	_settings = settings;
}

source.enable = function (conf, settings) {
    _settings = settings ?? {};
}
```
And just access them anywhere as `_settings["varname"]`.
