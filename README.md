# Description of the Dependabot issue

### Package ecosystem
Python 3.8, poetry 1.2.2

### Updated dependency
`pylint` from 2.14.0 to 2.15.7

### Repo for reproducing the issue
[test-dependabot](https://github.com/angiexiong/test-dependabot)

### Actuall behaviors:
1. Dependabot created a PR to update dependencies versions.
2. Dependabot removes sub-dependencies(such as `tomli`, `setuptools`) that are used to be dependencies in `poetry.lock`. See PR [here](https://github.com/angiexiong/test-dependabot/pull/1/files#diff-f53a023eedfa3fbf2925ec7dc76eecdc954ea94b7e47065393dbad519613dc89L99)
3. Dependabot updates the value of `python-versions` in the `poetry.lock` file from `"^3.8"` to `"~3.11"` in [`metadata` section](https://github.com/angiexiong/test-dependabot/pull/1/files#r1035006380). It rewrites the `poetry.lock` file as if the only valid python versions are `~3.11`


### Behaviors expected:
1. Don’t delete sub-dependencies(`tomli`, `setuptools`, etc) in `poetry.lock` file.
2. Dependabot creates a separate PR to update python version (or other user noticeable ways).
