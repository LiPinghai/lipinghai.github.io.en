---
layout: page
title: 'How to Generate a CHANGELOG with Conventional Git Commit Messages?'
categories: [tech]
tags: [original]
excerpt: "The commit messages of the well-known frontend web project AngularJS are praised by many in the industry, and its convention is gradually being adopted. However, writing commit messages in English is not always convenient in daily development. Here's an introduction to a convention set compatible with Chinese—[conventional-changelog-common](https://github.com/LiPinghai/conventional-changelog-common)."
---

> Author: Li Pinghai

The commit messages of the well-known frontend web project AngularJS are praised by many in the industry, and its [convention](https://www.npmjs.com/package/conventional-changelog-angular) is gradually being adopted. However, writing commit messages in English is not always convenient in daily development. Here's an introduction to a convention set compatible with Chinese—[conventional-changelog-common](https://github.com/LiPinghai/conventional-changelog-common).

#### Commit Message Types

Chinese Type | English Type | Meaning
-|-|-
新增 | feat | New API/Feature
修复 | fix | Bug Fix
删除 | delete | Destructive changes such as deleting APIs/parameters
更新 | refactor | Code refactoring/optimization, changes to configuration parameters in the code/project configuration
优化 | perf/chore/docs | Format optimization/documentation updates/optimized tests, etc.
发布 | release | New version/release/tag (CHANGELOG changes in this commit)
回滚 | revert | Revert a previous commit
合并 | merge | Merge conflicts/PRs

#### Commit Message Format

```md
Type[(Scope)] Title
// Leave a blank line
Brief description
// Leave a blank line
Related issue/Incompatibility warning
```
Example:
```
新增 xx.xxx接口

This interface is used for xxxxx, with parameters xxx, not compatible with IE8 and below.

close #1
```

## How to Integrate into a Frontend Project?

Install the *conventional-changelog-cli* and *conventional-changelog-common* dependencies.

```shell
npm i conventional-changelog-cli conventional-changelog-common -D
```

## How to Generate a Changelog?

Add the following script to the package.json (for detailed parameters, run *npx conventional-changelog-cli --help*):
```json
{
    "scripts": {
        "changelog": "conventional-changelog -i CHANGELOG.md -s -r 1 -n ./node_modules/conventional-changelog-common" 
    }
}
```
Then run:
```shell
npm run changelog
```
This will generate/append a *CHANGELOG.md* file in the root directory of your project.

The CHANGELOG will look like this:

--------
<a name="0.3.30"></a>
## [0.3.30](https://github.com/LiPinghai/conventional-changelog-common) (2018-12-19)

### Updates:

* Update Replace all xx with xxx ([d3813ff](https://github.com/LiPinghai/conventional-changelog-common))
* Update Optimize parameters of xxx interface ([055d7c1](https://github.com/LiPinghai/conventional-changelog-common))
* Update Change xx.xxx() listener from xxx to xxx ([bb7a489](https://github.com/LiPinghai/conventional-changelog-common))

### Fixes:

* Fix Conflict issue with buried points in xx and xx modules ([1894a3a](https://github.com/LiPinghai/conventional-changelog-common))
* Fix Error in xx environment ([e577220](https://github.com/LiPinghai/conventional-changelog-common))

### New Features:

* Add xx interface to xx ([fb40edc](https://github.com/LiPinghai/conventional-changelog-common))
* Add xx module ([fe1d1bd](https://github.com/LiPinghai/conventional-changelog-common))
--------
