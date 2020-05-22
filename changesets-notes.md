# @changesets analysis

https://github.com/atlassian/changesets/

Using yarn (1) workspaces, and I mostly compare to our current situation with lerna & standard-version which uses conventional-commits to generate changelogs.

My experiments so far can be seen in the commit history:

- bumping a package with patch
  - [Making the change + changeset](https://github.com/jorenbroekema/modern-monorepo-test/commit/293369dd3e1410cc7e2290a437c1147c6bbfd22)
  - [Making the release](https://github.com/jorenbroekema/modern-monorepo-test/commit/19e85fa196db748a6be99703e29d73370b487e85)
- bumping a package with minor, one that has a dependent package with a fixed version
  - [Making the change + changeset](https://github.com/jorenbroekema/modern-monorepo-test/commit/88ffba8671d244b00012d36a62005e818ffd56ee)
  - [Making the release](https://github.com/jorenbroekema/modern-monorepo-test/commit/4ed46db4ed640847d7fbd32435550faad8aeec49)
- bumping a package with a minor, but has 2 dependents that are incompatible now with this bump
  - [Making the change + changeset](https://github.com/jorenbroekema/modern-monorepo-test/commit/f47d8faa838cb77932b15313d237afb54d3c6186)
  - [Making the release](https://github.com/jorenbroekema/modern-monorepo-test/commit/390d3fdb8930376d8a82ba3227ac31f5c2ae8d60)

Still need to test:

- GitHub actions to automate the process of
  - Making a release (consuming the changesets)
  - Commit it with custom message
  - Release to npm
  - Push back the commits + created tags

## Pros

- No releases without existing changesets when pushing to master. So you can change code and not be forced a bump for stuff like fixing a docs typo, which is sadly the case for lerna / standard-version. This means we can always have continuous integration for docs updates and we don't need to "keep it on a dev branch" until we have something that warrants a release.
- Human readable changelogs!!!! keep commits clean
- "intent of release" for contributors, makes it more transparent than "commit type", which is sometimes missed / not reviewed properly
- Updates dependents where necessary if they become incompatible
- The collection of changesets before releasing, and then upon releasing "consuming" the changesets, is a very nice workflow if you intend on holding off on a release

## Cons

- Extra step required, before you would just commit with the right commit type, now you also need a changeset if the intent is to release
  This could be an extra barrier for contributors
  Luckily, just running `yarn changeset` is all you need, the menu is super easy.
- no root aggregated changelog (feature request issue is already on their issues board)

## Problems

- configure changesets default commit message when `commit` option is turned on, currently it does "Version Packages" which would fail for commitlint :( We can work around that by doing `git add .` and `git commit -m "chore: make release"` ourselves. --> issue exists
- GPL licenses in dependencies. Not sure if it's a problem for us if changesets is just a dev dep, our users don't get these packages --> issue exists
- generated changelogs are not compliant with markdown lint --> issue not yet existing
- shortened 7-char commit hashes are added to changelogs but don't link to the actual commit :\ so it's not very useful and a bit ugly even.. --> issue not yet existing

## Other notes

- I think our commitlint / conventional commits should still be used, and whenever a feat or fix commit is used, it should always be accompanied by a changeset. This way someone can go through the commit history and find fix/feat commits and read the human readable changelog (changeset) that was added as part of that commit. So you have two entry points:
  - changelog --> find the commit --> check the code change
  - commit history --> find feat/fix --> check the code change --> see the summary of what changed and why

## Verdict

ool stuff :)!
Really a fan of human readable changelog and committing "an intent to release" (a changeset)
Nice features for bumping packages properly including dependent packages if incompatibilities are found!
