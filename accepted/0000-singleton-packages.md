# Singleton Packages

## Summary

There is a class of package, which has implicitly depends on being the only version present in a given environment.  This is most frequent in a browser context, where a package may participate in one or more global namespaces, such as attaching objects or wrapping methods on objects like `window` or `document`, calling `customElements.define()` and polyfilling platform features.  In non-browser contexts this matters too, when packages are use frameworks with package-wide scope, such as `mocha`.  When multiple versions of these "singleton" packages are installed, due to incompatible version specifications in the dependency graph, the consequences are no

## Motivation

{{Why are we doing this? What pain points does this resolve? What use cases does it support? What is the expected outcome? Use real, concrete examples to make your case!}}

## Detailed Explanation

{{Describe the expected changes in detail, }}

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
