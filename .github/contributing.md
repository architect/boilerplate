# Architect contributor guidelines

## First: go read the [Architect Code of Conduct](/.github/code_of_conduct.md)

### Agreement to the Architect Code of Conduct

By participating in and contributing to the Architect community — including, but not limited to its open source projects, any related online venues such as GitHub, Slack, and in-person events, etc. — you agree to the [Architect Code of Conduct](./code_of_conduct.md).

Lack of familiarity with this Code of Conduct is not an excuse for not adhering to it.

---

## Guidelines

### Comms

- GitHub Issues and PRs are great places to contribute
- The [Architect Slack](https://architecture-as-text.slack.com) is another good place to communicate
- Commit messages, tweets, etc. are likely to be seen, but may be missed


## Release workflow & checklist

This section defines how contributors publishing Architect modules (including `@architect/architect`) should go about doing so.

This list does not include all the specific things one ought to do in order to prep a PR for release (please see the [PR template](./pull_request_template.md) for that), but the meta-process for working with and publishing modules.

Note: Most of these processes should be retired in favor of automated workflows, and will be as time and tooling become available.

1. **Always work in a branch**
  - Architect modules maintain a fairly expansive matrix of runtime and platform compatibility. Please do not commit directly to master, even for minor stuff.
  - Cutting branches is super easy if you want to add this helpful script to your `~/.bashrc` or `~/.bash_profile`:
    - `crb () { git branch "$1" && git push origin "$1" && git checkout "$1" && git push --set-upstream origin "$1"; }`
    - Usage: `crb new-branch` creates a new branch on GitHub, checks it out locally, and prepares it to be pushed to
  - Create a PR (of course)

2. **Initial testing can be done by installing locally to a test project**
  - Example: `$ /user/my/sandbox/project/ npm i ../../architect/sandbox`
  - Caveat: testing this way generally works fine, can have unintendend and seemingly-transiet side effects related to file paths, calling subdependencies, etc.
  - For anything relatively important (e.g. core logic), this is ONLY suitable for first-pass testing – you should never solely test with locally installed modules
  - To put a super fine point on this: we have released modules that were only tested locally, and they failed when re-packaged and distributed via NPM as proper dependencies. This really happens! So...

3. **Cut an RC**
  - Do a dry run with `npm publish --dry-run`
  - Create an RC by running `npm run rc` (which is simply an alias for `npm version prerelease --preid RC`)
    - All RCs must be tagged with `RC`
  - Push to your branch, and CI/CD will automatically publish your RC to npm + GitHub Package Repository

4. **Prep for release**
  a. Update deps
    - Prior to shipping a module, strongly suggest using [`ncu -u`](https://www.npmjs.com/package/npm-check-updates) to set the minimum dep versions to their latest releases
    - Remove `node_modules` and `package-lock.json`, and install from scratch: `rm -rf node_modules package-lock.json && npm i`
      - This quite often cleans up a lot of subdependency cruft that isn't picked up by normal installation processes
  b. Update the changelog and docs
    - Just do it :)
  c. Assuming tests are passing: merge the PR
  d. Helpful but optional; dry run: `npm publish --dry-run`
  e. From master, tag: `npm version [major|minor|patch]`
  f. `git push`
  g. Pour yourself a tasty beverage, because that was way too much ceremony
