---
layout: page
title: 'Why Does node-sass Always Fail to Install?'
categories: [tech]
tags: [original]
excerpt: 'node-sass is a common dependency in our development process, known for its lengthy installation time and frequent errors. There are many reasons for installation failures. Before we discuss them, let’s analyze the installation process of node-sass.'
---

node-sass is a common dependency in our development process, known for its lengthy installation time and frequent errors.
There are many reasons for installation failures. Before we discuss them, let’s analyze the installation process of node-sass (node version v10.15.3):

```shell
PS D:\demo> npm i node-sass

// Installing to node_modules from npm source
> node-sass@4.13.0 install D:\demo\node_modules\node-sass
> node scripts/install.js

// Downloading binding.node
Downloading binary from https://github.com/sass/node-sass/releases/download/v4.13.0/win32-x64-64_binding.node
Download complete .] - :
Binary saved to D:\demo\node_modules\node-sass\vendor\win32-x64-64\binding.node

// Caching binding.node
Caching binary to C:\Users\leepi\AppData\Roaming\npm-cache\node-sass\4.13.0\win32-x64-64_binding.node

> node-sass@4.13.0 postinstall D:\demo\node_modules\node-sass
> node scripts/build.js

Binary found at D:\demo\node_modules\node-sass\vendor\win32-x64-64\binding.node
Testing binary
Binary is fine

// Writing package-lock.json
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN demo@1.0.0 No description
npm WARN demo@1.0.0 No repository field.

+ node-sass@4.13.0
added 174 packages from 138 contributors and audited 529 packages in 24.379s
found 0 vulnerabilities
```

As we can see, the installation of node-sass involves several steps:

1. Check if node-sass is already installed in node_modules and if the version matches.
2. If not installed or the version does not match, install the node-sass package from npm.
3. Check if `binding.node` exists in the global cache or locally. If it exists, skip the installation.
4. If `binding.node` is not found, download the binary from GitHub and cache it globally.
5. If the download of `binding.node` fails, attempt to compile it locally.
6. Write the version information to `package-lock.json`.

Thus, node-sass actually depends on a binary file called `binding.node`. After installing the main package from npm, it will download `binding.node` from GitHub.

Here are the common reasons for node-sass installation failures:

## Reason 1: Slow npm source

Due to the well-known network environment in China, installing dependencies from the official npm source can be very slow. You can set the npm source to a domestic mirror (e.g., Taobao npm):

```shell
npm config set registry https://registry.npm.taobao.org
```

Or set it through the `.npmrc` file:

```
// .npmrc
registry=https://registry.npm.taobao.org/
```

## Reason 2: `binding.node` source is inaccessible or slow

In addition to the npm part of node-sass, a binary file `binding.node` is also downloaded, and the default source is GitHub, which can be slow or inaccessible in China. You can also change it to a domestic source:

```shell
// On Linux or macOS
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm install node-sass

// On Windows
set SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ && npm install node-sass
```

Or set it through the `.npmrc` file:

```
// .npmrc
sass_binary_site=https://npm.taobao.org/mirrors/node-sass/
```

Other common dependencies with similar issues include `chromedriver`, `phantomjs`, and `electron`. You can include them all in your `.npmrc` file:

```
// .npmrc
sass_binary_site=https://npm.taobao.org/mirrors/node-sass
chromedriver_cdnurl=https://npm.taobao.org/mirrors/chromedriver
phantomjs_cdnurl=https://npm.taobao.org/mirrors/phantomjs
electron_mirror=https://npm.taobao.org/mirrors/electron
```

## Reason 3: Incompatibility between node version and node-sass version

node-sass has poor compatibility with different node versions. The node-sass dependency in an older project may not be compatible with newer node versions. The compatibility is as follows (or refer to the [official repository](https://github.com/sass/node-sass)):

NodeJS | Minimum node-sass version | Node Module
---- | --- | ---
Node 13 | 4.13+ | 79
Node 12 | 4.12+ | 72
Node 11 | 4.10+ | 67
Node 10 | 4.9+ | 64
Node 8 | 4.5.3+ | 57

In the installation example at the beginning of this article, the version of `binding.node` is `v4.13.0/win32-x64-64_binding.node`. As you can see, it includes the node-sass version `v4.13.0`, platform `win32`, architecture `x64`, and the `Node Module` version 64. Node Module is a module of node, and its version can be found in the process `process.versions`:

```shell
PS D:\demo> node
> console.log(process.versions);
{ http_parser: '2.8.0',
  node: '10.15.3',
  v8: '6.8.275.32-node.51',
  uv: '1.23.2',
  zlib: '1.2.11',
  ares: '1.15.0',
  modules: '64',
  nghttp2: '1.34.0',
  napi: '3',
  openssl: '1.1.0j',
  icu: '62.1',
  unicode: '11.0',
  cldr: '33.1',
  tz: '2018e' }
undefined
>
```

As shown above, the module version corresponding to node 10.15.3 is 64.
If node-sass and node versions are incompatible, it will not find the corresponding `binding.node` and throw an error. For example, if your node version is 10.15.3, and you try to install node-sass 4.6.1, it will attempt to install `v4.6.1/win32-x64-64_binding.node`, but this version of `binding.node` does not exist.
In this case, change the version of node-sass or node.

## Reason 4: Inconsistent `binding.node` version in the cache
If the local node version has changed, or if you are running on different machines with inconsistent node versions, you may encounter an error like this:
```shell
Found bindings for the following environments:
  - Windows 64-bit with Node.js 6.x
```
This is because the original `binding.node` cache does not match the current node version. Follow the prompt to `npm rebuild node-sass` or clear the cache and reinstall.

## Reason 5: Reinstalling after a failed installation
After a failed installation, reinstalling may not have permission to delete the already installed content. In this case, run `npm uninstall node-sass` or manually delete the original directory before reinstalling.

## Reason 6: Compilation failure, missing Python installation, build failure, etc.
If the download of `binding.node` fails, node-sass will attempt to compile `binding.node` locally, which requires Python. If you have resolved the issues mentioned earlier, local compilation should not occur. Therefore, we won't discuss this failure within a failure situation.
