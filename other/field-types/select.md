# `select`

A single-select input field. The contents of the menu are set using the [`choices`](../properties/choices.md) property and its sub-properties.

## Settings

|  Property | Type   | Default | Description | Sub-properties |
|---|---|---|---|---|
|name | `string` | | Sets the name of the field in the database |
|label | `string` | | Sets the label of the field that the user sees |
|required | `boolean` | false | If true, the field is mandatory |
|type | `string` | | Specifies the field type |
|readOnly | `boolean` | false | If true, prevents the user from editing the field |
|help | `string` | | Help text for the field that will appear with the field's label |
|htmlHelp | `string` | | Help text with support for HTML markup |
|[choices](../properties/choices.md) | `array` |  | An array of choices that the user can select from. Each must be an object with value and label properties. |  [**showFields**](../properties/choices.md#showfields) |
|widgetContols | `boolean` | false | If true, `checkbox` fields can be edited in line on the page if the field is in a widget |
|contextual | `boolean` | false | If true, will prevent the `checkbox` field from appearing in the dialog box for a widget |
