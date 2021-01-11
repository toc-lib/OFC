# auto/ACTION scripts

## Goals

- Encourage dev/ops to **capture management logic as scripts** in the code-base
- Provide tech guys with **an obvious place to look** for management scripts
- Make it **easy to list available management actions**

## Why

- In some cases, we allow "build" logic to leak into "CI" platforms (Buildkite, Jenkins), making it hard to version-control, or reproduce locally.
- In other cases, we track management logic as scripts in Git, but different teams put those scripts in different places, and use different naming conventions (sometimes even within the same code-base).


## How

Standardise the location and naming of automation entry-point scripts, we propose a convention of providing **./auto/ACTION** scripts within versioned-controlled code-bases, as an entry-point for automation (e.g. execution on build-servers). It would be even better if these scripts can wrapper via docker or other similar tools can make it run anywhere without any local dependencies. 

### Definition

Automation entry-points should be represented by executable files (typically shell-scripts) in an "auto" sub-directory, of the form **auto/ACTION**, where ACTION is the operation to perform, e.g.

- auto/build
- auto/test
- auto/package
- auto/release
- auto/deploy

Don't use any ambiguous ACTION word like "run", we recommend you to extend the script name with extra description words to make it easier to understand, e.g.

- auto/build-docker-image
- auto/run-unit-test

### Note

Scripts specific to a particular automation platform should not go in "auto". Instead, create a platform-specific sub-directory if you have to put it here, e.g.

- auto/.buildkite
- auto/.jenkins

Scripts which is releated with the application itself should not go in 'auto'. Instead, put it in the src or any other meaningful directories.

## FAQ

### Why "auto"?

Other options discussed were considered too narrow, e.g.

- "build" - it's about more than just building things
- "ci" - activities such as "deploy" go beyond [the accepted definition of "Continuous Integration"](https://www.thoughtworks.com/continuous-integration)
- "cd" - too vague ("cd ./cd", anyone?)

### Why not a ".sh" suffix?

- Scripts might be implemented in sh/bash, but that's an implementation detail. 
- We don't need to change the references if rewrite it with another language like python.