# Brief

This repo showcases a very specific case of errors [installing Python packages with pipenv](https://circleci.com/developer/orbs/orb/circleci/python#usage-work-with-pipenv) using the [CircleCI Python orb](https://circleci.com/developer/orbs/orb/circleci/python).

**NOTE** that this is to showcase when a project has a local Pyenv config (e.g., `/path/to/project/.python-version`) that conflicts with the Python runtime version used in the job.

## Details

In this project, we specified the Python runtime to `3.8.6`.
> See `.python-version`


However, as of today (Aug 18, 2021), the latest `3.8` Python version available on CircleCI's `cimg/python` Docker image points to `3.8.11`.

```sh
# prints Python 3.8.11
docker run cimg/python:3.8 python --version
```


We also know that [Pipenv does not respect Pyenv version automatically](https://pipenv.pypa.io/en/latest/diagnose/#pipenv-does-not-respect-pyenvs-global-and-local-python-versions)

> Pipenv by default uses the Python it is installed against to create the virtualenv.

## Solutions

I found 2 ways to solve this, and solution 1 preferred for its correctness and simplicity.

### Solution 1

We simply use the right and specific Python runtime required. In this CircleCI config, this is a matter of specifying the [`tag` value in the provided executor](https://circleci.com/developer/orbs/orb/circleci/python#executors-default).

This is in line with Pipenv's behaviour since Pipenv will thus be installed in this specified Python version.

See https://github.com/mt-kelvintaywl/circleci-python-orb-pyenv-conflict-demo/pull/1

### Solution 2

We remove the `.python-version` config as a pre-step before installation.

This works but in our CI build case, it means the installation of packages happens against Python `3.8.11` rather than `3.8.6` that we desired.

When newer 3.8.x versions for Python are available with the `cimg/python` Docker image, it is possible some unexpected breakages may happen.

We should strive to keep the CI build the same as what is intended in the production environment, and ideally for local environment too.
> Of course, with semantic versioning, we trust that packages installed on any Python 3.8.x should still work.

See https://github.com/mt-kelvintaywl/circleci-python-orb-pyenv-conflict-demo/pull/2
