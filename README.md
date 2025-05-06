# setup-poetry

Set up your GitHub Actions workflow with a specific version of poetry.

- Install a version of python with poetry and add it to PATH;
- Install dependencies for the project;
- Cache the installed version of poetry and dependencies from virtual environment to speed up consecutive runs on self-hosted runners;
- Persist the poetry's cache in the GitHub Actions Cache.

## Usage

### Install a required-version or latest (default)

```yaml
- name: Install the latest version of poetry
  uses: danfimov/setup-poetry@v1
```

If you do not specify a version with `poetry-version` argument,  the latest version will be installed.

### Install a specific version

```yaml
- name: Install a specific version of poetry
  uses: danfimov/setup-poetry@v1
  with:
    version: "2.1.2"
```

### Python version

You can use the input `python-version` to set python version that will be used by poetry (default `3.13`).

```yaml
- name: Install the latest version of poetry and set the python version to 3.13t
  uses: danfimov/setup-poetry@v1
  with:
    python-version: 3.13t
```

You can combine this with a matrix to test multiple python versions:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.9", "3.10", "3.11", "3.12"]
    steps:
      - uses: actions/checkout@v4
      - name: Install the latest version of poetry and set the python version
        uses: danfimov/setup-poetry@v1
        with:
          python-version: ${{ matrix.python-version }}
      - name: Test with python ${{ matrix.python-version }}
        run: poetry run pytest
```

### Dependency installation

By default the action will install your dendencies with `poetry install --no-interaction --no-root`. You can specify extra arguments with `install-args`:

```yaml
- name: "Install poetry and all dependencies for the project"
  uses: danfimov/setup-poetry@v1
  with:
    install-args: --all-extras
```

Don't forget to add `--no-interaction` and `--no-root` if you still need them.

## FAQ

### How caching works?

This action implements a two-layer caching system to optimize workflow performance, particularly on self-hosted runners: poetry binary caching and dependencies caching.

Note that `danfimov/setup-poetry` will automatically use a separate cache key for each host architecture, python version, and platform.

### Do I still need `actions/setup-python` alongside `setup-poetry`?

With `setup-poetry`, you don't need `actions/setup-python` anymore, because it will be used under the hood.

### Do I need to activate virtual environment manually?

This action will create and activate virtual environment for you automatically. No additional steps are required.

### Is it just a wrapper for other actions?

Yes, this action simplifies the setup by using other actions:
- Python setup via [`actions/setup-python`](https://github.com/actions/setup-python);
- Poetry install via [`snok/install-poetry`](https://github.com/snok/install-poetry);
- Poetry binary and dependency caching via [`actions/cache`](https://github.com/actions/cache).

Moving forward, I plan to adopt an approach similar to [`setup-uv`](https://github.com/marketplace/actions/astral-sh-setup-uv). My goal is to create a unified solution for complete control of your Poetry projects in GitHub CI/CD. To accomplish this, I intend to replace the current combination of actions with my own codebase, providing a more seamless experience and enhanced flexibility for end users.
