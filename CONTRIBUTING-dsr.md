<!--
 Licensed to the Apache Software Foundation (ASF) under one
 or more contributor license agreements.  See the NOTICE file
 distributed with this work for additional information
 regarding copyright ownership.  The ASF licenses this file
 to you under the Apache License, Version 2.0 (the
 "License"); you may not use this file except in compliance
 with the License.  You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing,
 software distributed under the License is distributed on an
 "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
 KIND, either express or implied.  See the License for the
 specific language governing permissions and limitations
 under the License.
-->

# Contributing

This file is based on the Apache Superset guide available [here](https://github.com/apache/superset/blob/master/CONTRIBUTING.md). Please refer to it for more information.

## Table of Contents

- [Contributing to RM Stable Branch](#contribute-dsr-superset)
- [Pull Request Guidelines](#pull-request-guidelines)
  - [Protocol](#protocol)
    - [Authoring](#authoring)
    - [Reviewing](#reviewing)
    - [Merging](#merging)
- [Design Guidelines](#design-guidelines)
  - [Capitalization guidelines](#capitalization-guidelines)
- [Reporting a Security Vulnerability](#reporting-a-security-vulnerability)
- [Setup Local Environment for Development](#setup-local-environment-for-development)
  - [Flask server](#flask-server)
    - [OS Dependencies](#os-dependencies)
    - [Dependencies](#dependencies)
  - [Frontend](#frontend)
    - [Prerequisite](#prerequisite)
    - [Install dependencies](#install-dependencies)
    - [Build assets](#build-assets)
    - [Feature flags](#feature-flags)
  - [Documentation](#documentation)
- [Git Hooks](#git-hooks)
- [Linting](#linting)
  - [Python](#python)
  - [TypeScript](#typescript)
- [Testing](#testing)
  - [Python Testing](#python-testing)
  - [Frontend Testing](#frontend-testing)
  - [Integration Testing](#integration-testing)
  - [Storybook](#storybook)
- [Tips](#tips)
  - [Adding a new datasource](#adding-a-new-datasource)


## Contributing to RM Stable Branch
- Every time you want to contribute to this branch, you should create a new branch and the corresponding Pull Request
- Assign Jira ticket to reviewer when PR is ready
- Create one PR per problem whenever possible
- Try to follow every guideline mentioned in the [Pull Request Guidelines](#pull-request-guidelines) section so that it is easy to create PRs to the main repository (apache/superset) whenever it is desired.

### Deployment
- Whenever a PR is approved and merged into RM stable branch, a docker image will be built and pushed to a private repository
  - To use this image on an installation copy the docker folder and the docker-compose-non-dev.yml file. Additionally, create the appropriate file structure for the data volumes and update it in the docker-compose-non-dev.yml file:
    - .data/db_home
    - .data/redis
    - .data/superset_home

## Pull Request Guidelines

- Create an issue before creating a Pull Request to separate the problem from possible solutions.
- Break your work into smaller independent PRs and refer to the same issue. This will greatly reduce turnaround time.
- If you wish to share your work which is not ready to merge yet, create a PR as Draft
- If the PR is part of multiple PRs to complete a large feature and cannot work on its own, create a feature branch and merge all related PRs into the feature branch before creating a PR from feature branch to master.

### Protocol

#### Authoring

- Fill in all sections of the PR template.
- Title the PR with one of the following semantic prefixes (inspired by [Karma](http://karma-runner.github.io/0.10/dev/git-commit-msg.html)):
  - `feat` (new feature)
  - `fix` (bug fix)
  - `docs` (changes to the documentation)
  - `style` (formatting, missing semi colons, etc; no application logic change)
  - `refactor` (refactoring code)
  - `test` (adding missing tests, refactoring tests; no application logic change)
  - `chore` (updating tasks etc; no application logic change)
  - `perf` (performance-related change)
  - `build` (build tooling, Docker configuration change)
  - `ci` (test runner, GitHub Actions workflow changes)
  - `other` (changes that don't correspond to the above -- should be rare!)

- Add prefix `[WIP]` to title if not ready for review (WIP = work-in-progress)
- If you believe your PR contributes a potentially breaking change, put a `!` after the semantic prefix but before the colon in the PR title, like so: `feat!: Added foo functionality to bar`
- **Screenshots/GIFs:** Changes to user interface require before/after screenshots, or GIF for interactions
  - If no screenshot is provided, the committers will mark the PR with `need:screenshot` label and will not review until screenshot is provided.
- **Dependencies:** Be careful about adding new dependency and avoid unnecessary dependencies.
  - For Python, include it in `setup.py` denoting any specific restrictions and in `requirements.txt` pinned to a specific version which ensures that the application build is deterministic.
  - For TypeScript/JavaScript, include new libraries in `package.json`
- **Tests:** The pull request should include tests, either as doctests, unit tests, or both.
- **Documentation:** If the pull request adds functionality, the docs should be updated as part of the same PR.
- **CI:** Reviewers will not review the code until all CI tests are passed. Sometimes there can be flaky tests. You can close and open PR to re-run CI test.
- **Code coverage:** Please ensure that code coverage does not decrease.

#### Reviewing

- Use constructive tone when writing reviews.
- If there are changes required, state clearly what needs to be done before the PR can be approved.
- If you are asked to update your pull request with some changes there's no need to create a new one. Push your changes to the same branch.
- The committers reserve the right to reject any PR and in some cases may request the author to file an issue.

#### Merging

- At least one approval is required for merging a PR.
- PR is usually left open for at least 24 hours before merging.
- After the PR is merged, [close the corresponding issue(s)](https://help.github.com/articles/closing-issues-using-keywords/).
- Project maintainers may contact the PR author if new issues are introduced by the PR.
- Project maintainers may revert your changes if a critical issue is found, such as breaking master branch CI.

## Design Guidelines

### Capitalization guidelines

Use sentence-case capitalization for everything in the UI, except:
- Input labels, buttons and UI tabs are all caps
- User input values (e.g. column names, SQL Lab tab names) should be in their original case

When writing about a UI element, use the same capitalization as used in the UI, e.g. an input field labeled “Name” must be referred as the “Name input field”

## Reporting a Security Vulnerability

Please report security vulnerabilities to private@superset.apache.org.
In the event a community member discovers a security flaw in Superset, it is important to follow the [Apache Security Guidelines](https://www.apache.org/security/committers.html) and release a fix as quickly as possible before public disclosure. Reporting security vulnerabilities through the usual GitHub Issues channel is not ideal as it will publicize the flaw before a fix can be applied.


## Setup Local Environment for Development

### Flask server

#### OS Dependencies

Make sure your machine meets the [OS dependencies](https://superset.apache.org/docs/installation/installing-superset-from-scratch#os-dependencies) before following these steps.
You also need to install MySQL or [MariaDB](https://mariadb.com/downloads).

Ensure that you are using Python version 3.8, 3.9 or 3.10, then proceed with:

```bash
# Create a virtual environment and activate it (recommended)
python3 -m venv venv # setup a python3 virtualenv
source venv/bin/activate

# Install external dependencies
pip install -r requirements/testing.txt

# Install Superset in editable (development) mode
pip install -e .

# Initialize the database
superset db upgrade

# Create an admin user in your metadata database (use `admin` as username to be able to load the examples)
superset fab create-admin

# Create default roles and permissions
superset init

# Load some data to play with.
# Note: you MUST have previously created an admin user with the username `admin` for this command to work.
superset load-examples

# Start the Flask dev web server from inside your virtualenv.
# Note that your page may not have CSS at this point.
# See instructions below how to build the front-end assets.
FLASK_ENV=development superset run -p 8088 --with-threads --reload --debugger
```

Or you can install via our Makefile

```bash
# Create a virtual environment and activate it (recommended)
$ python3 -m venv venv # setup a python3 virtualenv
$ source venv/bin/activate

# install pip packages + pre-commit
$ make install

# Install superset pip packages and setup env only
$ make superset

# Setup pre-commit only
$ make pre-commit
```

**Note: the FLASK_APP env var should not need to be set, as it's currently controlled
via `.flaskenv`, however if needed, it should be set to `superset.app:create_app()`**

If you have made changes to the FAB-managed templates, which are not built the same way as the newer, React-powered front-end assets, you need to start the app without the `--with-threads` argument like so:
`FLASK_ENV=development superset run -p 8088 --reload --debugger`

#### Dependencies

If you add a new requirement or update an existing requirement (per the `install_requires` section in `setup.py`) you must recompile (freeze) the Python dependencies to ensure that for CI, testing, etc. the build is deterministic. This can be achieved via,

```bash
$ python3 -m venv venv
$ source venv/bin/activate
$ python3 -m pip install -r requirements/integration.txt
$ pip-compile-multi --no-upgrade
```

When upgrading the version number of a single package, you should run `pip-compile-multi` with the `-P` flag:

```bash
$ pip-compile-multi -P my-package
```

To bring all dependencies up to date as per the restrictions defined in `setup.py` and `requirements/*.in`, run pip-compile-multi` without any flags:

```bash
$ pip-compile-multi
```

This should be done periodically, but it is recommended to do thorough manual testing of the application to ensure no breaking changes have been introduced that aren't caught by the unit and integration tests.

### Frontend

Frontend assets (TypeScript, JavaScript, CSS, and images) must be compiled in order to properly display the web UI. The `superset-frontend` directory contains all NPM-managed frontend assets. Note that for some legacy pages there are additional frontend assets bundled with Flask-Appbuilder (e.g. jQuery and bootstrap). These are not managed by NPM and may be phased out in the future.

#### Prerequisite

First, be sure you are using the following versions of Node.js and npm:

- `Node.js`: Version 16
- `npm`: Version 7

#### Install dependencies

Install third-party dependencies listed in `package.json` via:

```bash
# From the root of the repository
cd superset-frontend

# Install dependencies from `package-lock.json`
npm ci
```

#### Build assets

There are three types of assets you can build:

1. `npm run build`: the production assets, CSS/JSS minified and optimized
2. `npm run dev-server`: local development assets, with sourcemaps and hot refresh support
3. `npm run build-instrumented`: instrumented application code for collecting code coverage from Cypress tests

- Run the dev server on a non-default port
  `npm run dev-server -- --port=9001`

- Proxy backend requests to a Flask server running on a non-default port
  `npm run dev-server -- --supersetPort=8081`

- Proxy to a remote backend but serve local assets
  `npm run dev-server -- --superset=https://superset-dev.example.com`

The `--superset=` option is useful in case you want to debug a production issue or have to setup Superset behind a firewall. It allows you to run Flask server in another environment while keep assets building locally for the best developer experience.

Alternatively, there are other NPM commands you may find useful:

1. `npm run build-dev`: build assets in development mode.
2. `npm run dev`: built dev assets in watch mode, will automatically rebuild when a file changes

#### Feature flags

Superset supports a server-wide feature flag system, which eases the incremental development of features. To add a new feature flag, simply modify `superset_config.py` with something like the following:

```python
FEATURE_FLAGS = {
    'SCOPED_FILTER': True,
}
```

If you want to use the same flag in the client code, also add it to the FeatureFlag TypeScript enum in [@superset-ui/core](https://github.com/apache-superset/superset-ui/blob/master/packages/superset-ui-core/src/utils/featureFlags.ts). For example,

```typescript
export enum FeatureFlag {
  SCOPED_FILTER = "SCOPED_FILTER",
}
```

`superset/config.py` contains `DEFAULT_FEATURE_FLAGS` which will be overwritten by
those specified under FEATURE_FLAGS in `superset_config.py`. For example, `DEFAULT_FEATURE_FLAGS = { 'FOO': True, 'BAR': False }` in `superset/config.py` and `FEATURE_FLAGS = { 'BAR': True, 'BAZ': True }` in `superset_config.py` will result
in combined feature flags of `{ 'FOO': True, 'BAR': True, 'BAZ': True }`.

The current status of the usability of each flag (stable vs testing, etc) can be found in `RESOURCES/FEATURE_FLAGS.md`.

### Documentation

The latest documentation and tutorial are available at https://superset.apache.org/.

The documentation site is built using [Docusaurus 2](https://docusaurus.io/), a modern static website generator, the source for which resides in `./docs`.

#### Local Development

To set up a local development environment with hot reloading for the documentation site:

```shell
cd docs
yarn install  # Installs NPM dependencies
yarn start  # Starts development server at http://localhost:3000
```

#### Build

To create and serve a production build of the documentation site:

```shell
yarn build
yarn serve
```

#### Deployment

Commits to `master` trigger a rebuild and redeploy of the documentation site. Submit pull requests that modify the documentation with the `docs:` prefix.

## Git Hooks

Superset uses Git pre-commit hooks courtesy of [pre-commit](https://pre-commit.com/). To install run the following:

```bash
pip3 install -r requirements/integration.txt
pre-commit install
```

A series of checks will now run when you make a git commit.

Alternatively it is possible to run pre-commit via tox:

```bash
tox -e pre-commit
```

Or by running pre-commit manually:

```bash
pre-commit run --all-files
```

## Linting

### Python

We use [Pylint](https://pylint.org/) for linting which can be invoked via:

```bash
# for python
tox -e pylint
```

In terms of best practices please avoid blanket disablement of Pylint messages globally (via `.pylintrc`) or top-level within the file header, albeit there being a few exceptions. Disablement should occur inline as it prevents masking issues and provides context as to why said message is disabled.

Additionally, the Python code is auto-formatted using [Black](https://github.com/python/black) which
is configured as a pre-commit hook. There are also numerous [editor integrations](https://black.readthedocs.io/en/stable/integrations/editors.html)

### TypeScript

```bash
cd superset-frontend
npm ci
npm run lint
```

If using the eslint extension with vscode, put the following in your workspace `settings.json` file:

```json
"eslint.workingDirectories": [
  "superset-frontend"
]
```

## Testing

### Python Testing

All python tests are carried out in [tox](https://tox.readthedocs.io/en/latest/index.html)
a standardized testing framework.
All python tests can be run with any of the tox [environments](https://tox.readthedocs.io/en/latest/example/basic.html#a-simple-tox-ini-default-environments), via,

```bash
tox -e <environment>
```

For example,

```bash
tox -e py38
```

Alternatively, you can run all tests in a single file via,

```bash
tox -e <environment> -- tests/test_file.py
```

or for a specific test via,

```bash
tox -e <environment> -- tests/test_file.py::TestClassName::test_method_name
```

Note that the test environment uses a temporary directory for defining the
SQLite databases which will be cleared each time before the group of test
commands are invoked.

There is also a utility script included in the Superset codebase to run python integration tests. The [readme can be
found here](https://github.com/apache/superset/tree/master/scripts/tests)

To run all integration tests for example, run this script from the root directory:

```bash
scripts/tests/run.sh
```

You can run unit tests found in './tests/unit_tests' for example with pytest. It is a simple way to run an isolated test that doesn't need any database setup

```bash
pytest ./link_to_test.py
```

### Frontend Testing

We use [Jest](https://jestjs.io/) and [Enzyme](https://airbnb.io/enzyme/) to test TypeScript/JavaScript. Tests can be run with:

```bash
cd superset-frontend
npm run test
```

To run a single test file:

```bash
npm run test -- path/to/file.js
```

### Integration Testing

We use [Cypress](https://www.cypress.io/) for integration tests. Tests can be run by `tox -e cypress`. To open Cypress and explore tests first setup and run test server:

```bash
export SUPERSET_CONFIG=tests.integration_tests.superset_test_config
export SUPERSET_TESTENV=true
export CYPRESS_BASE_URL="http://localhost:8081"
superset db upgrade
superset load_test_users
superset load-examples --load-test-data
superset init
superset run --port 8081
```

Run Cypress tests:

```bash
cd superset-frontend
npm run build-instrumented

cd cypress-base
npm install

# run tests via headless Chrome browser (requires Chrome 64+)
npm run cypress-run-chrome

# run tests from a specific file
npm run cypress-run-chrome -- --spec cypress/integration/explore/link.test.ts

# run specific file with video capture
npm run cypress-run-chrome -- --spec cypress/integration/dashboard/index.test.js --config video=true

# to open the cypress ui
npm run cypress-debug

# to point cypress to a url other than the default (http://localhost:8088) set the environment variable before running the script
# e.g., CYPRESS_BASE_URL="http://localhost:9000"
CYPRESS_BASE_URL=<your url> npm run cypress open
```

See [`superset-frontend/cypress_build.sh`](https://github.com/apache/superset/blob/master/superset-frontend/cypress_build.sh).

As an alternative you can use docker-compose environment for testing:

Make sure you have added below line to your /etc/hosts file:
`127.0.0.1 db`

If you already have launched Docker environment please use the following command to assure a fresh database instance:
`docker-compose down -v`

Launch environment:

`CYPRESS_CONFIG=true docker-compose up`

It will serve backend and frontend on port 8088.

Run Cypress tests:

```bash
cd cypress-base
npm install
npm run cypress open
```

### Storybook

Superset includes a [Storybook](https://storybook.js.org/) to preview the layout/styling of various Superset components, and variations thereof. To open and view the Storybook:

```bash
cd superset-frontend
npm run storybook
```

When contributing new React components to Superset, please try to add a Story alongside the component's `jsx/tsx` file.

## Tips

### Adding a new datasource

1. Create Models and Views for the datasource, add them under superset folder, like a new my_models.py
   with models for cluster, datasources, columns and metrics and my_views.py with clustermodelview
   and datasourcemodelview.

1. Create DB migration files for the new models

1. Specify this variable to add the datasource model and from which module it is from in config.py:

   For example:

   ```python
   ADDITIONAL_MODULE_DS_MAP = {'superset.my_models': ['MyDatasource', 'MyOtherDatasource']}
   ```

   This means it'll register MyDatasource and MyOtherDatasource in superset.my_models module in the source registry.
