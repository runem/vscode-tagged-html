<div align="center" markdown="1">

<img src="https://raw.githubusercontent.com/runem/ts-lit-plugin/master/documentation/asset/lit-plugin@256w.png" width="200">

**vscode plugin that adds syntax highlighting, type checking and code completion for [lit-html](https://github.com/polymer/lit-html).**

[![](https://vsmarketplacebadge.apphb.com/version-short/runem.lit-plugin.svg)](https://marketplace.visualstudio.com/items?itemName=runem.lit-plugin)
[![](https://vsmarketplacebadge.apphb.com/downloads-short/runem.lit-plugin.svg)](https://marketplace.visualstudio.com/items?itemName=runem.lit-plugin)
[![](https://vsmarketplacebadge.apphb.com/rating-short/runem.lit-plugin.svg)](https://marketplace.visualstudio.com/items?itemName=runem.lit-plugin)
<a href="https://opensource.org/licenses/MIT"><img alt="MIT License" src="https://img.shields.io/badge/License-MIT-green.svg" height="20"></img></a>

![](documentation/asset/lit-plugin.gif)

</div>

## Feature overview

* [✅ Attribute/property type checking](#-attributeproperty-type-checking)
* [🔍 Automatically pick up on custom elements](#-automatically-pick-up-on-custom-elements)
* [🌎 Support for dependencies that extend the global HTMLElementTagNameMap](#-support-for-dependencies-that-extend-the-global-htmlelementtagnamemap)
* [📣 Report missing imports of custom elements](#-report-missing-imports-of-custom-elements)
* [⚡️Event checking](#event-checking)
* [📬 Slot checking](#-slot-checking)
* [🚶Goto definition](#goto-definition)
* [✏️ Code completions for css and html](#-code-completions-for-css-and-html)
* [📖 Quick info on hover for html tags and attributes](#-quick-info-on-hover-for-html-tags-and-attributes)
* [🙈 Support for @ts-ignore comments inside html](#-support-for-ts-ignore-comments-inside-html)
* [⚠️ Warning if required attributes are not included](#-warning-if-required-attributes-are-not-included)
* [💅 Reformat html](#-reformat-html)
* [🚪 Auto close tags](#-auto-close-tags)

See [Features](#features) for a description of each feature.

## Details

All features are provided by these three libraries:

-   **[ts-lit-plugin](https://github.com/runem/ts-lit-plugin)**: The typescript plugin that powers the logic through the typescript language service (code completion, type checking, eg.). Therefore issues regarding anything but syntax highlighting should be opened in `ts-lit-plugin` and not `vscode-lit-plugin`.
-   **[vscode-lit-html](https://github.com/mjbvz/vscode-lit-html)**: Provides highlighting for the html template tag.
-   **[vscode-styled-components](https://github.com/styled-components/vscode-styled-components)**: Provides highlighting for the css template tag.

This library couples it all together and adds `ts-lit-plugin` code completion in the plugin section in `tsconfig.json` while synchronizing relevant settings between vscode and `ts-lit-plugin`.

## Features

### ✅ Attribute/property type checking

`lit-plugin` type checks all attributes and property assignment, both on your own elements, library elements and built in elements. You will also get the following warnings:

-   Warning if you assign a complex type without using the `.` modifier.
-   Warning if you use the `?` modifier on a non-boolean type.

If the plugin doesn't pick up on your properties and attributes you can specify them using jsdoc on your element.

```javascript
/**
 * This is my element
 * @attr size
 * @attr {red|blue} color - The color of my element
 * @prop {String} value
 * @prop {Boolean} myProp - This is my property
 */
@customElement("my-element")
class MyElement extends LitElement {

}
```

### 🔍 Automatically pick up on custom elements

If you define a custom element somewhere in your code `lit-plugin` will automatically pick up on it. Then it will provide auto-import functionality, type checking and code completion out of the box by analyzing the element. [web-component-analyzer](https://github.com/runem/web-component-analyzer) is the tool that takes care of analyzing components.

### 🌎 Support for dependencies that extend the global HTMLElementTagNameMap

<img src="https://user-images.githubusercontent.com/5372940/53271293-4fc5f300-36ee-11e9-9ed9-31f1e50f898c.gif" width="500" />

If a dependency extends the global `HTMLElementTagNameMap` this plugin will pick up on the map between the tag name and the class. Below you will see an example of what to add to your library typescript definition files if you want type checking support for a given html tag.

```typescript
declare global {
  interface HTMLElementTagNameMap {
    "my-element": MyElement;
  }
}
```

**Two limitations using this approach as of now**

-   By using this approach the plugin wont see detailed information about a given element as (e.g @property decorators and initializers) because it can only read public fields and their corresponding types. Therefore all properties on custom elements imported from libraries are optional and wont respect meta information in @property decorators.
-   `lit-plugin` will only be able two find your elements if you somewhere in the code imports the file. Before your import the file it will complain that the element is unknown not that it can be imported. This due to the constraint that Typescript only adds library files to the array of program files once the file has been imported.

This plugin already supports [custom vscode html data format](https://code.visualstudio.com/updates/v1_31#_html-and-css-custom-data-support) (see the configuration section) and I will of course work on supporting more methods of shipping metadata alongside custom elements.

### 📣 Report missing imports of custom elements

When using custom elements `lit-plugin` checks if the element has been imported and is available in the current context. It's considered imported if any file in the path of imports defines the custom element. You can disable this check by setting `skipMissingImports` to true in the configuration (see [Configuring the plugin](#configuring-the-plugin)). Be aware that dependencies need to extend the global `HTMLElementTagNameMap` in order for this plugin to pick up on them.

### ⚡️Event checking

This plugin will check and suggest event names if you declare them in the jsdoc of the custom element class.

```javascript
/**
 * This is my element
 * @event change
 * @event commit - This event is dispatched when the user pressed 'commit'
 */
@customElement("my-element")
class MyElement extends LitElement {

}
```

### 📬 Slot checking

This plugin will check and suggest slot names if you declare them in the jsdoc of the custom element class. A `@slot` jsdoc tag without a name is the unnamed slot.

```javascript
/**
 * This is my element
 * @slot - Default content placed in the middle
 * @slot header - Content placed above the main content
 * @slot footer - Content placed below the main content
 */
@customElement("my-element")
class MyElement extends LitElement {

}
```


### 🚶Goto definition

`Cmd+Click (Mac)` / `Ctrl+Click (Windows)` on a tag, attribute, property or event name and goto the definition.

### ✏️ Code completions for css and html

<img src="https://user-images.githubusercontent.com/5372940/53271979-4f2e5c00-36f0-11e9-98a6-f9b7996d841c.gif" width="500" />

Press `Ctrl+Space` in an html or css context and to get code completions.


### 📖 Quick info on hover for html tags and attributes

Hover above a tag, attribute, property or event and see more information about the identifier such as type and jsdoc.


### 🙈 Support for @ts-ignore comments inside html

Add "@ts-ignore" comments to make `lit-plugin` quiet.

**In front of html template tags:**
This will make `lit-plugin` opt out of any checking inside the html template tag, but not the nested html template tags.

```javascript
// @ts-ignore
html`this is not checked ${html`this is checked`} `;
```

**Inside HTML:**
This will make `lit-plugin` opt out of any checking inside the div tag.

```javascript
return html`
  <h1>Foo</h1>

  <!-- @ts-ignore -->
  <div>
      <my-element></my-element>
  </div>

  <h1>Bar</h1>
`;
```


### ⚠️ Warning if required attributes are not included

**Note: This functionality has been temporarily disabled**

<img src="https://user-images.githubusercontent.com/5372940/53272219-f612f800-36f0-11e9-98d2-2810f8b14c60.gif" width="500" />


`lit-plugin` will warn you if you forget to set any required attributes on a given html tag. Right now this is based on the assumption that the property is required if it doesn't have an initializer and isn't assignable to `undefined` or `null`. Be aware that right now the plugin doesn't check if you assign it else where (for example in the constructor).

**lit-plugin will think that the following is a required property**:

```typescript
@customElement("my-element")
export class MyElement extends LitElement {
  @property({ type: String }) text!: string;
}
```

### 💅 Reformat html

**Note: This functionality has been temporarliy disabled. Please look into using prettier for reformating your html.**

`lit-plugin` will reformat html when you reformat code using your IDE. Keep in mind that right now there is an issue where the plugin does not take the current indentation of the html template tag into account. This means that the especially nested html template tags will look weird.

**For example**:

```javascript
return html`<div>${html`<h1>Foo</h1> <h2>Bar</h2>`}</div>`;
```

**Will become**:

```javascript
return html`
<div>
  ${html`<h1>Foo</h1>
<h2>Bar</h2>`}
</div>`;
```

**And not:**:

```javascript
return html`
  <div>
    ${html`
      <h1>Foo</h1>
      <h2>Bar</h2>
    `}
  </div>`;
```

### 🚪 Auto close tags

When typing html inside a template tag `lit-plugin` auto-closes tags as you would expect.


## Feature comparison
This plugin is similar to [vscode-lit-html](https://github.com/mjbvz/vscode-lit-html) on many points. The power of `vscode-lit-html` is that it covers all the basic functionality of HTML in tagged templates so it's a plugin that can be easily used with other libraries than `lit-html`. However `ts-lit-plugin` aims to be a specialized plugin for working with `lit-element` so for example it supports `css` and discovers web components out of the box. 

Below is a comparison table of the two plugins.

| Feature                 | [vscode-lit-html](https://github.com/mjbvz/vscode-lit-html)   | [vscode-lit-plugin](https://github.com/runem/vscode-lit-plugin) |
|-------------------------|------------|------------|
| CSS support             | ❌         | ✅         |
| Goto definition         | ❌         | ✅         |
| Check missing imports   | ❌         | ✅         |
| Auto discover web components | ❌    | ✅         |
| Template type checking  | ❌         | ✅         |
| Report unknown tag names | ❌        | ✅         |
| Report unknown attrs    | ❌         | ✅         |
| Report unknown props    | ❌         | ✅         |
| Report unknown events   | ❌         | ✅         |
| Report unknown slots    | ❌         | ✅         |
| Support for vscode custom data format | ❌| ✅    |
| Auto close tags         | ✅         | ✅         |
| Syntax Highlighting     | ✅         | ✅         |
| Completions             | ✅         | ✅         |
| Quick info on hover     | ✅         | ✅         |
| Code folding            | ✅         | ⚠️ (disabled until problem with calling 'program.getSourceFile' is fixed) |
| Formatting              | ✅         | ⚠️ (disabled until problem with nested templates is fixed) |


# Configuring the plugin

If you are using the vscode plugin you can configure these options directly from extension settings. If not you can add the options directly to the `compilerOptions.plugins` section of your `ts-config.json` file.


### General settings
#### disable
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Completely disable this plugin.

#### htmlTemplateTags
 
-   **Type**: string[]
-   **Default**: ["html", "raw"]
-   **Description**: List of template tags to enable html support in.
 
#### cssTemplateTags
 
-   **Type**: string[]
-   **Default**: ["css"]
-   **Description**: List of template tags to enable css support in.

#### format.disable
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Disable formatting the HTML on code reformat.

### Add checks
#### checkUnknownEvents
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Activating this setting will make the plugin emit errors on unknown events.

### Skip checks
#### skipMissingImports
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Skip reporting missing imports of custom elements.

#### skipSuggestions
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Don't attach suggestions alongside warnings and errors.
 

#### skipUnknownTags
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Skip reporting unknown html tags.

#### skipUnknownAttributes
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Skip reporting unknown html attributes.

#### skipUnknownProperties
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Skip reporting unknown properties on elements.

#### skipUnknownSlots
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Skip reporting unknown slot names.

#### skipTypeChecking
 
-   **Type**: boolean
-   **Default**: false
-   **Description**: Skip type checking of attributes and properties.
  
### Extra data
#### globalTags
 
-   **Type**: string[]
-   **Description**: List of html tag names that you expect to be present at all times.
 
#### globalAttributes
 
-   **Type**: string[]
-   **Description**: List of html attributes names that you expect to be present at all times.

#### globalEvents
 
-   **Type**: string[]
-   **Description**: List of event names that you expect to be present at all times.

#### customHtmlData
 
-   **Type**: [Vscode custom HTML data format](https://github.com/Microsoft/vscode-html-languageservice/blob/master/docs/customData.md) you can both specify a relative file paths or an entire objects with the data here. This value can both be an array and an object.
-   **Description**: This plugin support the [custom vscode html data format](https://code.visualstudio.com/updates/v1_31#_html-and-css-custom-data-support) through this setting.
