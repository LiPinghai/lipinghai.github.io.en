---
layout: page
title: 'Can’t Keep Track of Frontend Versions? Use This Plugin to Solve It in One Click'
categories: [tech]
tags: [original]
excerpt: 'In practice, we often deploy frontend code to the server through pipelines and automation scripts. Sometimes it’s hard to tell if the deployment was successful. When multiple people or branches share the same environment, it’s easy for things to get mixed up. With version-plugin, you can inject version information and easily identify the page code version with one click.'
---

In practice, we often deploy frontend code to the server through pipelines and automation scripts. Sometimes it’s hard to tell if the deployment was successful. When multiple people or branches share the same environment, it’s easy for things to get mixed up.

"Who overwrote my code again?!"

"Did someone actually deploy to production or not?"

"The pipeline failed but didn’t report an error?!"

Testers and developers are all frustrated.

How can we quickly identify what version of code is currently on the page? We can manually use `webpack.DefinePlugin` to inject information into the global scope.

Or, you can use **version-plugin** to solve it in one click, as shown below:

![image.png](./assets/version-plugin.png)

Below is the plugin documentation:

# [version-plugin](https://www.npmjs.com/package/version-plugin)

A webpack plugin used to inject *version information* into project code.

https://www.npmjs.com/package/version-plugin

## Getting Started

First, install `version-plugin`:

```
npm install version-plugin -D
```

Then, add the VersionPlugin configuration to your webpack setup:

**webpack.config.js**
```js
const VersionPlugin = require('version-plugin');

module.exports = {
  plugins: [new VersionPlugin()]
};

```

Run npm run dev or npm run build, and version-plugin will inject a global variable VERSION_INFO into your project.

## Options

### Plugin Options

|       Variable Name       |      Type       |       Default Value       |               Description               |
| :-----------------------: | :-------------: | :-----------------------: | :--------------------------------------: |
|        **`name`**         |    `{String}`   |      `VERSION_INFO`       |  Name of the global variable to inject   |
|    **[`mode`](#mode)**    |`{'all'｜String｜Array}`|   `development`   | Specifies in which webpack modes to enable |
| **[`dataOption`](#dataOption)** |   `{Object}`    |          {}             |      Specific version information        |

### mode

For security reasons, Version Plugin is **only enabled** in development mode by default. If set to `all`, it will be enabled in all modes. You can also pass a specific mode name or an array of modes.

### dataOption

Version Plugin will, by default, inject `git_branch` and `git_commit_hash` as version information.

The following additional information is also available for use:
```
git_commit_fullhash
git_commit_time
git_commit_author
git_commit_commiter
git_commit_message
package_version
build_time
```

Setting these options to `true` will enable them. If you pass a `String` / `Number` value or a function, it will override the default value. Besides the above 9 items, you can also pass custom fields.

Example:

```js
new VersionPlugin({
  name: '_v_',
  mode: ['production', 'development'],
  dataOption:{
    git_commit_hash: false,
    git_commit_fullhash: true,
    git_commit_author: true,
    package_version: () => '1.0.0',
    extra_data_foo: 'extra_data_bar'
  }
})
```

Then check the browser console:

```js

// window._v_

{
  git_branch: "develop",
  git_commit_fullhash: "c3252175510b100a4a139f2af4b3f73ef753483a",
  git_commit_author: 'LiPinghai',
  package_version: "1.0.0", 
  extra_data_foo: "extra_data_bar"
}
```