# GitVersion pipeline

It is a tool that generates a semantic versioning (SemVer) base on its Git history.


## Mainline mode

The version number is typically incremented with each commit to the main branch.

When you commit changes to your main branch, Gitversion will automatically generate a new version number base on the rules.
- Patch: If the commit is just a normal change, GitVersion will increment the patch version. e.g from 1.0.0 to 1.0.0
- Minor: If the commit contains the commit message, such as #minor, GitVersion can increment the Minor. from 1.0.0 to 1.1.0
- Major. This represent a breaking changes

