---
layout: page
title: 'Programming with Git'
categories: [tech]
tags: [original]
excerpt: "Following good Git practices is key to maximizing its effectiveness. This article introduces the basic principles of Git usage, Git-friendly syntax, appropriate commit granularity, commit message conventions, and more."
---

> Author: Li Pinghai

## Basic Principles of Git Usage

- Include only files that need to be shared/recorded, avoiding large files, unnecessary compiled results, system-specific files, or redundant files.
- Use Git-friendly syntax, project structure, and file naming.
- Create a new branch locally or in a specific branch to develop new features.
- Rebase/merge changes to the appropriate commit granularity.
- Write commit messages in the correct format.
- Delete merged branches.
- Tag release nodes.

## Git-Friendly Syntax, Project Structure, and File Naming

Git tracks changes at the line/file level, so it's essential to avoid meaningless line/file changes. Do not write multiple logics in one line to make it easier to identify what has changed in the record.

### Write One Logic Per Line (Assignment/Comparison)

#### Separate Object Assignments, Write HTML/CSS in Multiple Lines

```javascript
// incorrect
let a, b = 1, c = 2;

let a = b = c = 1;

// correct
let a; 
let b = 1; 
let c = 2;
```

```html
<!--incorrect-->
<div><span>Label 1</span></div>

<!--correct-->
<div>
  <span>Label 1</span>
</div>
```

```css
// incorrect
div{width: 100px; height:100px;}

// correct
div{
  width: 100px; 
  height:100px;
}
```

#### Write Object Properties/Array Elements on Separate Lines

```js
// incorrect
let c = {foo: 1, bar: 2};

// correct
let c = {
  foo: 1, 
  bar: 2,
};
```

#### Avoid Lengthy Ternary Logic/Ternary Branch Assignment

```js
// incorrect
result ? success && success() : error && error('The current browser does not support copy operations, please copy manually!');

// correct
if (result) {
  success && success();
} else {
  error && error('The current browser does not support copy operations, please copy manually!');
}

// incorrect
  _SDKApp['load'].indexOf('clientGame') !== -1 ? pageInfo = 'Client Game Product' : pageInfo = 'Web Game Product';

// correct
if (_SDKApp['load'].indexOf('clientGame') !== -1){
  pageInfo = 'Client Game Product';
} else {
  pageInfo = 'Web Game Product';
}
```

#### Avoid Assigning/Calling Methods and Comparing in One Line

```js
// incorrect
if (arrSource[i].split('=')[0].toLowerCase() === paramName) {
  //...
}

// correct
let sourceName = arrSource[i].split('=')[0].toLowerCase();
if(sourceName === paramName){
  //...
}
```

### Avoid Meaningless Changes

#### Add Commas/Semicolons to the Last Line

```javascript
// incorrect

let c = {
  foo: 1, 
  bar: 2
};

// correct

let c = {
  foo: 1, 
  bar: 2,
};
```
```css
// incorrect
  img {
    position: absolute;
    top: 0;
    height: 20px
  }

// correct
  img {
    position: absolute;
    top: 0;
    height: 20px;
  }
```

### Be Aware of System Differences

#### Avoid Including System-Specific Files

```
// .gitignore
.DS_Store
Thumbs.db
```

#### File Name Case Sensitivity? Line Endings?

```
// Disable automatic line ending conversion globally
git config --global core.autocrlf false

// Set global case sensitivity
git config --global core.ignorecase false 

// Force file update
git mv --force filename FILENAME
```

## Commit/Branch/Tag Granularity

The commit is the basic unit of Git operations. Controlling the appropriate commit granularity is crucial to the quality of Git usage.

### What is the Granularity of a Commit?

#### A Commit Should Be:

- A bug fix (`fix`)
- An addition/deletion of a code feature (`feat`)
- A code optimization/refactoring (`style`, `refactor`)
- A documentation change (`docs`)
- A test change (`test`)
- A release node (`release`)
- A rollback (`revert`)
- A merge (`merge`)

#### A Commit Should Not Be:

- Meaningless changes (empty lines/trailing characters/LF or CRLF)
- Incomplete changes/non-working changes (a day's code, a week's code)
- Mixed multiple features/changes

### The Meaning of Branches

#### Branches on the Repository

- A historical version
- A specific version
- A new feature

#### Local Branches

- Developing new features

### The Meaning of Tags

- New project version number
- A public release/project online version node

## Git Commit Message Conventions

### The Benefits of Writing Commit Messages According to the Convention

- Key information in the commit history is more distinct.
- Facilitates the use of `git bisect` to find errors (ignores irrelevant commits).
- Easier to generate `CHANGELOG.md` with scripts.

### Angular Convention:

```md
type[(scope)]: subject
// Leave a blank line
body (brief explanation in the first person)
// Leave a blank line
footer (incompatible changes/closing issues)
```

An example from a Vue commit:

```
fix(ssr): render initial and used async css chunks (#7902)

compatibility with webpack 4 + mini CSS extraction

close #7897 
```

### Chinese Convention

For non-open source business projects, using English is not convenient or intuitive. We can try using a Chinese commit convention.

#### Chinese Commit Format

```md
类型[(影响范围)] 标题
// Leave a blank line
Brief description
// Leave a blank line
Related issue/Incompatibility warning
```

#### Commit Types

Type | Meaning
-|-
新增 | New API/Feature
修复 | Bug Fix
删除 | Deleting APIs/Parameters, etc.
更新 | Code refactoring/optimization, changes to configuration parameters in the code/project configuration
优化 | Format optimization/documentation updates/optimized tests, etc.
发布 | New version/release/tag (CHANGELOG changes in this commit)
回滚 | Revert a previous commit
合并 | Merge conflicts/PRs

#### Notes

- API changes should clearly specify the API name.

Chinese convention example:

```
新增 Xx.xxx接口

This interface is very powerful, feel free to pass parameters, not compatible with IE8 and below.

close #1
```

## Git Commit Message Operations

It's inevitable to make mistakes or use temporary commits when using Git. We need to optimize commits reasonably.

### Modify the Most Recent Commit Message

```
git commit --amend
```

### Modify Multiple Commits

```
git rebase -i CommitHash
```

### Move Commit Records

```
git rebase --onto TargetBranch/TargetCommit 
```

For detailed operations, refer to [Git Rebase](https://git-scm.com/book/en/v2/Git-Branching-Rebasing).

## References

[Linux Commit Messages](https://github.com/torvalds/linux/commits/master)

[Vue Commit Messages](https://github.com/vuejs/vue/commits/)

[Angular Commit Convention](https://gist.github.com/stephenparish/9941e89d80e2bc58a153)

[Ruanyifeng on Commit Messages](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

[Git Demo](http://gitlab.game.yypm.com:10080/lipinghai/git-demo)

[conventional-changelog-yygame Convention](https://github.com/LiPinghai/conventional-changelog-yygame)
