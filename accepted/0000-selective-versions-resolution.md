# Selective Versions Resolution



There are a few options for resolution semantics in `package.json`:

- A `resolutions` map similar to the one used by `yarn` to support [Selective Versions Resolution](https://github.com/yarnpkg/rfcs/blob/master/implemented/0000-selective-versions-resolutions.md) would be capable of defining the specific package version to use, using the wildcard prefix to ensure it applies at all depths of the tree `"**/package-name": "^2.0.0"`.

- Use the *existing* `dependencies` map.  At the root level, any version of a singleton package specified as a direct dependency of the package would establish *the* version that any other transitive dependency for the same package would resolve to.

### Nested version conflict resolution

Prior approaches for resolving package version conflicts, such as with `yarn install --flat` and `bower install` only support defining resolutions at the top-level package.  This has the effect of requiring every package developer to deal with the same conflict resolutions, even in cases where conflicts exist transitively from a single direct dependency.

A proposal for a resolution mechanism which is more in-line with the philosophy of the current recursive node module resolution scheme is to support resolutions of singleton packages defined in the transitive dependencies as well as the root.  This could be achieved by prioritizing resolutions which are expressed closer to the root package.  Doing so would enable full control over resolutions at the root, but would not needlessly force it to be a root package concern when the conflict has already been resolved nearer its source.

Consider the following dependency graph (with no deduplication), wherein there are 2 versions of 4 libraries, lib-a, lib-b, lib-c and lib-d.  Let's say that ALL of these libraries are intended to be singleton packages:

```
my-app@1
 + lib-a@1
 |  + lib-b@1
 |  |  + lib-c@1
 |  + lib-d@1
 + lib-b@2
    + lib-c@2
       + lib-d@2
```

If we leverage the `resolutions` or `dependencies` map of each package while traversing the graph breadth-first outward from `my-app@1`, we will arrive at the following resolved versions, which is basically a flattened version of the tree, preferring shallow nodes:

```
my-app@1
 + lib-a@1
 + lib-b@2
 + lib-c@2
 + lib-d@1
```

An explicit `resolutions` map has an advantage over just using `dependencies` in that it expresses a clear intent to resolve a conflict between versions, whereas `dependencies` may do so only incidentally.  It also happens to be the map used by `yarn`, so as long as semantics/patterns are consistent, this has some existing support.  However, `yarn` currently only honors resolutions specified in the root package, so honoring transitive resolutions would be an extension, though not necessarily incompatible.