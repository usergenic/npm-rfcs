# Singleton Packages

## Summary

Add a step to the graph analysis phase of `npm install` that causes it to fail when unable to resolve certain packages to a single instance/version due to  semver constraints.

## Motivation

There are many situations where packages have implicit requirement to be the sole instance in the package tree:
 - Polyfills, which typically extend or wrap global methods and objects
 - Packages that wire global properties or methods onto to `window` or `document`
 - Packages that make use of implicit global registries such as `CustomElementsRegistry`
 - Frameworks which establish global concerns such as `mocha`, `react` or `angular`
 - Libraries which make use of `instanceof` in objects transacted by their public API

## Detailed Explanation

Any package which needs to ensure its uniqueness in the package tree would indicate so in its `package.json`, likely  `{"singleton": true}`, informing the install command to permit installation of only one instance in the entire tree.  If unable to resolve to a solitary version, due to semver constraints, installation would fail, providing a list of version conflicts and prompt with details about options for resolving the conflict.

A new feature for resolving version conflicts is neither part of this RFC nor a pre-requisite of it.

## Rationale and Alternatives

A failure during installation will be much easier to identify and troubleshoot than a runtime failure which may be obscure and may not have any noticable effect until breaking something in a production service.

Building this into the `npm` tool will protect the entire community of npm users from issues arising from version conflicts in singleton packages than deferring to opt-in packages/plugins.

Existing discussions:

- https://github.com/npm/npm/issues/20185
- https://github.com/package-community/discussions/issues/3
- https://github.com/arcanis/build-pnm/issues/1

## Implementation

Continuing from *Detailed Explanation*, consider the following session when running `npm install ` for my project:

```
$ npm install
npm ERR! Incompatible singleton dependencies @webcomponents/webcomponentsjs
npm ERR!   some-package@2.1.0 -> ^1.0.0
npm ERR!   other-package@0.9.0 -> ^2.0.0

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\usergenic\AppData\Roaming\npm-cache\_logs\2018-11-28T10_40_07_124Z-debug.log
```

I can clearly see that I have two packages `some-package@2.1.0` and `other-package@0.9.0` which each have semver incompatible dependencies on `@webcomponents/webcomponentsjs`.  I now know the nature of the problem I am dealing with.

## Prior Art

I am unaware of package managers which define packages as singleton packages conditionally.  Many package systems assume that everything is a singleton or that there is no such thing.  In package systems which assume everything is singleton, there are often override measures to support manual resolution of conflicts.

In the node ecosystem,  `yarn install --flat` attempts to flatten the tree of a node package's dependencies on installation, using a [resolutions](https://yarnpkg.com/lang/en/docs/package-json/#toc-resolutions) property to override conflicting versions.

## Unresolved Questions and Bikeshedding

{{Write about any arbitrary decisions that need to be made (syntax, colors, formatting, minor UX decisions), and any questions for the proposal that have not been answered.}}

{{THIS SECTION SHOULD BE REMOVED BEFORE RATIFICATION}}
