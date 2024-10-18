# Using uv in GitHub Actions

## Installation

For use with GitHub Actions, we recommend the official
[`astral-sh/setup-uv`](https://github.com/astral-sh/setup-uv) action, which installs uv, adds it to
PATH, (optionally) persists the cache, and more, with support for all uv-supported platforms.

GitHub Actions で使用するには、公式の [`astral-sh/setup-uv`](https://github.com/astral-sh/setup-uv) アクションをお勧めします。これにより、uv がインストールされ、PATH に追加され、（オプションで）キャッシュが永続化され、すべての uv 対応プラットフォームをサポートします。

最新バージョンの uv をインストールするには：

```yaml title="example.yml" hl_lines="11-12"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3
```

特定の uv バージョンにピン留めすることがベストプラクティスとされています。例えば：

```yaml title="example.yml" hl_lines="14 15"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3
        with:
          # Install a specific version of uv.
          version: "0.4.24"
```

## Setting up Python

Python can be installed with the `python install` command:

Python は `python install` コマンドでインストールできます：

```yaml title="example.yml" hl_lines="14 15"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3

      - name: Set up Python
        run: uv python install
```

This will respect the Python version pinned in the project.

これはプロジェクトでピン留めされた Python バージョンを尊重します。

Or, when using a matrix, as in:

または、次のようにマトリックスを使用する場合：

```yaml title="example.yml"
strategy:
  matrix:
    python-version:
      - "3.10"
      - "3.11"
      - "3.12"
```

Provide the version to the `python install` invocation:

`python install` 呼び出しにバージョンを指定します：

```yaml title="example.yml" hl_lines="14 15"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3

      - name: Set up Python ${{ matrix.python-version }}
        run: uv python install ${{ matrix.python-version }}
```

Alternatively, the official GitHub `setup-python` action can be used. This can be faster, because
GitHub caches the Python versions alongside the runner.

または、公式の GitHub `setup-python` アクションを使用することもできます。これにより、GitHub はランナーと一緒に Python バージョンをキャッシュするため、より高速になります。

Set the
[`python-version-file`](https://github.com/actions/setup-python/blob/main/docs/advanced-usage.md#using-the-python-version-file-input)
option to use the pinned version for the project:

[`python-version-file`](https://github.com/actions/setup-python/blob/main/docs/advanced-usage.md#using-the-python-version-file-input) オプションを設定して、プロジェクトのピン留めされたバージョンを使用します：

```yaml title="example.yml" hl_lines="14 15 16 17"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3

      - name: "Set up Python"
        uses: actions/setup-python@v5
        with:
          python-version-file: ".python-version"
```

Or, specify the `pyproject.toml` file to ignore the pin and use the latest version compatible with
the project's `requires-python` constraint:

または、`pyproject.toml` ファイルを指定してピンを無視し、プロジェクトの `requires-python` 制約と互換性のある最新バージョンを使用します：

```yaml title="example.yml" hl_lines="17"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3

      - name: "Set up Python"
        uses: actions/setup-python@v5
        with:
          python-version-file: "pyproject.toml"
```

## Syncing and running

Once uv and Python are installed, the project can be installed with `uv sync` and commands can be
run in the environment with `uv run`:

uv と Python がインストールされると、プロジェクトは `uv sync` でインストールされ、コマンドは `uv run` で環境内で実行できます：

```yaml title="example.yml" hl_lines="17-22"
name: Example

jobs:
  uv-example:
    name: python
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v3

      - name: Set up Python
        run: uv python install

      - name: Install the project
        run: uv sync --all-extras --dev

      - name: Run tests
        # For example, using `pytest`
        run: uv run pytest tests
```

!!! tip

    The
    [`UV_PROJECT_ENVIRONMENT` setting](../../concepts/projects.md#configuring-the-project-environment-path) can
    be used to install to the system Python environment instead of creating a virtual environment.

    [`UV_PROJECT_ENVIRONMENT` 設定](../../concepts/projects.md#configuring-the-project-environment-path) を使用して、仮想環境を作成する代わりにシステム Python 環境にインストールできます。

## Caching

It may improve CI times to store uv's cache across workflow runs.

ワークフローの実行間で uv のキャッシュを保存することで、CI 時間が改善される可能性があります。

The [`astral-sh/setup-uv`](https://github.com/astral-sh/setup-uv) has built-in support for
persisting the cache:

[`astral-sh/setup-uv`](https://github.com/astral-sh/setup-uv) には、キャッシュを永続化するための組み込みサポートがあります：

```yaml title="example.yml"
- name: Enable caching
  uses: astral-sh/setup-uv@v3
  with:
    enable-cache: true
```

You can configure the action to use a custom cache directory on the runner:

アクションを構成して、ランナー上のカスタムキャッシュディレクトリを使用するようにできます：

```yaml title="example.yml"
- name: Define a custom uv cache path
  uses: astral-sh/setup-uv@v3
  with:
    enable-cache: true
    cache-local-path: "/path/to/cache"
```

Or invalidate it when the lockfile changes:

または、ロックファイルが変更されたときに無効にします：

```yaml title="example.yml"
- name: Define a cache dependency glob
  uses: astral-sh/setup-uv@v3
  with:
    enable-cache: true
    cache-dependency-glob: "uv.lock"
```

Or when any requirements file changes:

または、任意の要件ファイルが変更されたとき：

```yaml title="example.yml"
- name: Define a cache dependency glob
  uses: astral-sh/setup-uv@v3
  with:
    enable-cache: true
    cache-dependency-glob: "requirements**.txt"
```

Note that `astral-sh/setup-uv` will automatically use a separate cache key for each host
architecture and platform.

`astral-sh/setup-uv` は、各ホストアーキテクチャおよびプラットフォームごとに別々のキャッシュキーを自動的に使用します。

Alternatively, you can manage the cache manually with the `actions/cache` action:

または、`actions/cache` アクションを使用してキャッシュを手動で管理できます：

```yaml title="example.yml"
jobs:
  install_job:
    env:
      # Configure a constant location for the uv cache
      UV_CACHE_DIR: /tmp/.uv-cache

    steps:
      # ... setup up Python and uv ...

      - name: Restore uv cache
        uses: actions/cache@v4
        with:
          path: /tmp/.uv-cache
          key: uv-${{ runner.os }}-${{ hashFiles('uv.lock') }}
          restore-keys: |
            uv-${{ runner.os }}-${{ hashFiles('uv.lock') }}
            uv-${{ runner.os }}

      # ... install packages, run tests, etc ...

      - name: Minimize uv cache
        run: uv cache prune --ci
```

The `uv cache prune --ci` command is used to reduce the size of the cache and is optimized for CI.
Its effect on performance is dependent on the packages being installed.

`uv cache prune --ci` コマンドは、キャッシュのサイズを削減するために使用され、CI 用に最適化されています。そのパフォーマンスへの影響は、インストールされているパッケージに依存します。

!!! tip

    If using `uv pip`, use `requirements.txt` instead of `uv.lock` in the cache key.

    `uv pip` を使用する場合、キャッシュキーには `uv.lock` の代わりに `requirements.txt` を使用してください。

!!! note

    [post-job-hook]: https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/running-scripts-before-or-after-a-job

    When using non-ephemeral, self-hosted runners the default cache directory can grow unbounded.
    In this case, it may not be optimal to share the cache between jobs. Instead, move the cache
    inside the GitHub Workspace and remove it once the job finishes using a
    [Post Job Hook][post-job-hook].

    非エフェメラルなセルフホストランナーを使用する場合、デフォルトのキャッシュディレクトリは無制限に成長する可能性があります。この場合、ジョブ間でキャッシュを共有することは最適ではないかもしれません。代わりに、キャッシュを GitHub ワークスペース内に移動し、ジョブが終了したら [Post Job Hook][post-job-hook] を使用して削除します。

    ```yaml
    install_job:
      env:
        # Configure a relative location for the uv cache
        UV_CACHE_DIR: ${{ github.workspace }}/.cache/uv
    ```

    Using a post job hook requires setting the `ACTIONS_RUNNER_HOOK_JOB_STARTED` environment
    variable on the self-hosted runner to the path of a cleanup script such as the one shown below.

    ポストジョブフックを使用するには、セルフホストランナーで `ACTIONS_RUNNER_HOOK_JOB_STARTED` 環境変数を以下のようなクリーンアップスクリプトのパスに設定する必要があります。

    ```sh title="clean-uv-cache.sh"
    #!/usr/bin/env sh
    uv cache clean
    ```

## Using `uv pip`

If using the `uv pip` interface instead of the uv project interface, uv requires a virtual
environment by default. To allow installing packages into the system environment, use the `--system`
flag on all `uv` invocations or set the `UV_SYSTEM_PYTHON` variable.

`uv` プロジェクトインターフェースの代わりに `uv pip` インターフェースを使用する場合、uv はデフォルトで仮想環境を必要とします。システム環境にパッケージをインストールできるようにするには、すべての `uv` 呼び出しで `--system` フラグを使用するか、`UV_SYSTEM_PYTHON` 変数を設定します。

The `UV_SYSTEM_PYTHON` variable can be defined in at different scopes.

`UV_SYSTEM_PYTHON` 変数は、異なるスコープで定義できます。

Opt-in for the entire workflow by defining it at the top level:

トップレベルで定義することで、ワークフロー全体にオプトインします：

```yaml title="example.yml"
env:
  UV_SYSTEM_PYTHON: 1

jobs: ...
```

Or, opt-in for a specific job in the workflow:

または、ワークフロー内の特定のジョブにオプトインします：

```yaml title="example.yml"
jobs:
  install_job:
    env:
      UV_SYSTEM_PYTHON: 1
    ...
```

Or, opt-in for a specific step in a job:

または、ジョブ内の特定のステップにオプトインします：

```yaml title="example.yml"
steps:
  - name: Install requirements
    run: uv pip install -r requirements.txt
    env:
      UV_SYSTEM_PYTHON: 1
```

To opt-out again, the `--no-system` flag can be used in any uv invocation.

再度オプトアウトするには、任意の uv 呼び出しで `--no-system` フラグを使用できます。
