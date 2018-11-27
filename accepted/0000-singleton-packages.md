# Singleton Packages

## Summary

A Singleton Package is a package which depends on having only one instance of itself present in the package tree.  Add support for a flag property in `package.json` to identify Singleton Packages.

## Motivation

There are many situations where packages have implicit requirement to be the sole instance in the package tree:
 - Polyfills, which typically extend or wrap global methods and objects
 - Packages that wire global properties or methods onto to `window` or `document`
 - Packages that make use of implicit global registries such as `CustomElementsRegistry`
 - Frameworks which establish global concerns such as `mocha`, `react` or `angular`
 - Libraries which make use of `instanceof` in objects transacted by their public API

Existing discussions:
- https://github.com/arcanis/build-pnm/issues/1
- https://github.com/package-community/discussions/issues/3
- https://github.com/npm/npm/issues/20185

## Detailed Explanation

A `package.json` for a Singleton Package would include the new property `"singleton": true`, informing the package installer `npm` to permit installation of only one instance in the tree.  If the dependency graph can not resolve the package to a single version, installation would fail with a list of version conflicts and explicit resolution would need to be made.

There are several options to consider for specifying the resolutions in the `package.json`:
 - Support a new `resolutions` or `singletons` map that would essentially list package names and version specifications, identical in structure to the `dependencies` map.
 - Use the existing `dependencies` and/or `devDependencies` maps and simply prefer the specifications in packages closer to the root of the package graph, erroring out only in the event of a tie between packages at the same depth.
 - Support a new dependency map property `patchDependencies` which would remap specific packages/versions encountered in the dependency graph to other packages/versions.  This feature is described more fully in [Patch Dependencies](./0000-patch-dependencies.md).
 
A `resolutions` map property could be supported in the `package.json` which would take the same form of the `dependencies` block.

One mechanism for defining Singleton Package resolutions which integrates reasonably well with the philosophy of the current node module resolution scheme is to simply prioritize dependencies expressed at shallower levels of the graph over those at deeper levels, enabling full control over resolution at the top-level or "root" package, but not needlessly forcing it to be the concern of the root package.

I
This aleviates the need for adding anything like a special-purpose dependency `resolutions` property in the package file and supports delegation of resolutions where they are better understood, by package authors which expressed dependencies in the first.

Consider the following set of packages, *all* of which are defined as Singleton Packages:

| Package | Version | Dependencies             |
| ------- | ------- | ------------------------ |
| my-app  | 1.0.0   | lib-a@1.0.0, lib-b@2.0.0 |
| lib-a   | 1.0.0   | lib-b@1.0.0, lib-d@1.0.0 |
| lib-b   | 1.0.0   | lib-c@1.0.0              |
| lib-b   | 2.0.0   | lib-c@2.0.0              |
| lib-c   | 1.0.0   | lib-d@1.0.0              |
| lib-c   | 2.0.0   | lib-d@2.0.0              |
| lib-d   | 1.0.0   |                          |
| lib-d   | 2.0.0   |                          |

A breadth-first traversal of the graph from the root `my-app@1.0.0` towards leaf nodes provides the following resolutions:

| Package | Version | Resolved by   |
| ------- | ------- | ------------- |
| lib-a   | 1.0.0   | my-app@1.0.0  |
| lib-b   | 2.0.0   | my-app@1.0.0  |
| lib-c   | 2.0.0   | lib-b@2.0.0   |
| lib-d   | 1.0.0   | lib-a@1.0.0   |

## Rationale and Alternatives

{{Discuss 2-3 different alternative solutions that were considered. This is required, even if it seems like a stretch. Then explain why this is the best choice out of available ones.}}

## Implementation

{{Give a high-level overview of implementaion requirements and concerns. Be specific about areas of code that need to change, and what their potential effects are. Discuss which repositories and sub-components will be affected, and what its overall code effect might be.}}

{{THIS SECTION IS REQUIRED FOR RATIFICATION -- you can skip it if you don't know the technical details when first submitting the proposal, but it must be there before it's accepted}}

## Prior Art

{{This section is optional if there are no actual prior examples in other tools}}

{{Discuss existing examples of this change in other tools, and how they've addressed various concerns discussed above, and what the effect of those decisions has been}}

## Unresolved Questions and Bikeshedding

{{Write about any arbitrary decisions that need to be made (syntax, colors, formatting, minor UX decisions), and any questions for the proposal that have not been answered.}}

{{THIS SECTION SHOULD BE REMOVED BEFORE RATIFICATION}}
