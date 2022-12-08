# Description of the Dependabot issue
### Package ecosystem
Python 3.8, poetry 1.2.2
In `pyproject.toml`, we have a set the allowed python version range as follows:
```
[tool.poetry.dependencies]
python = "^3.8"
```
Which means python versions >= 3.8 but <4.

### Updated dependency
`pylint` from 2.14.0 to 2.15.7

### Repo for reproducing the issue
[test-dependabot](https://github.com/angiexiong/test-dependabot)

### Actual behaviour:
1. Dependabot created a PR to update dependencies versions.
2. Dependabot rewrote the `poetry.lock` file, removing dependencies and modifying dependency version constraints  by assuming that python 3.11 was being used. This resulted in packages not being installed for Python 3.8. (see below)
3. Dependabot updated the value of `python-versions` in the `poetry.lock` file from `"^3.8"` to `"~3.11"` in [`metadata` section](https://github.com/angiexiong/test-dependabot/pull/1/files#r1035006380). It rewrites the `poetry.lock` file as if the only valid python versions are `~3.11`


#### Examples of how `poetry.lock` was rewritten


##### tomli
`tomli`, a dependency of `pylint` in `poetry.lock` [in `main`](https://github.com/angiexiong/test-dependabot/blob/main/poetry.lock#L91):

```
tomli = {version = ">=1.1.0", markers = "python_version < \"3.11\""}
```

This was removed in the PR as the constraint was not valid for python 3.11

##### wrapt

```
[[package]]
name = "wrapt"
version = "1.14.1"
description = "Module for decorators, wrappers and monkey patching."
category = "dev"
optional = false
python-versions = "!=3.0.*,!=3.1.*,!=3.2.*,!=3.3.*,!=3.4.*,>=2.7"
```

for cases where their package dependency constraints are not satisfied by python 3.11. See PR [here](https://github.com/angiexiong/test-dependabot/pull/1/files#diff-f53a023eedfa3fbf2925ec7dc76eecdc954ea94b7e47065393dbad519613dc89L99). In `main`, we had `-tomli = {version = ">=1.1.0", markers = "python_version < \"3.11\""}` as its constraint for the tomli dependency of pylint in poetry.lock. Dependabot removed this dependency from poetry.lock and thus, the package is missing if we `poetry install` using Python 3.8. We can see in this [PR](https://github.com/angiexiong/test-dependabot/pull/1) and [Failing job](https://github.com/angiexiong/test-dependabot/actions/runs/3576765014/jobs/6014963250) that `wrapt` has had a constraint for python-version >=3.11 added to it and therefore it doesn't install on python 3.8.


### Expected Behaviour:
1. Dependencies are not deleted if required by versions of python that satisfy the range given in pyproject.toml.
2. Dependabot does not alter the python-versions value in the metadata section of poetry.lock.
