# Installing Python

Python がすでにシステムにインストールされている場合、uv は設定なしでそれを[検出して使用](#using-an-existing-python-installation)します。ただし、uv は Python バージョンをインストールおよび管理することもできます。

!!! tip

    uv は必要に応じて[自動的に Python バージョンを取得](#automatic-python-downloads)します。始めるために Python をインストールする必要はありません。

<!-- TODO(zanieb): I don't love this heading. -->

## Getting started

最新の Python バージョンをインストールするには：

```console
$ uv python install
```

これにより、システムにすでに Python インストールがある場合でも、uv 管理の Python バージョンがインストールされます。以前に uv で Python をインストールしたことがある場合、新しいバージョンはインストールされません。

!!! note

    Python は公式の配布可能なバイナリを公開していません。そのため、uv は [`python-build-standalone`](https://github.com/indygreg/python-build-standalone) プロジェクトからのサードパーティ配布を使用します。このプロジェクトは部分的に uv のメンテナによって維持されており、他の著名な Python プロジェクト（例：[Rye](https://github.com/astral-sh/rye)、[Bazel](https://github.com/bazelbuild/rules_python)）でも使用されています。詳細については、[Python ディストリビューション](../concepts/python-versions.md#managed-python-distributions)のドキュメントを参照してください。

<!-- TODO(zanieb): Restore when Python shim management is added
Note that when an automatic Python installation occurs, the `python` command will not be added to the shell. Use `uv python install-shim` to ensure the `python` shim is installed.

Once Python is installed, it can be invoked via `python`:

```console
$ python --version
```

To prevent uv from managing Python system-wide, provide the `--no-shim` option during installation.
-->

Once Python is installed, it will be used by `uv` commands automatically.

!!! important

    uv によって Python がインストールされると、グローバルには利用できません（つまり、`python` コマンドを介して）。この機能のサポートは将来のリリースで予定されています。それまでの間、`uv run` を使用するか、仮想環境を作成してアクティブ化して `python` を直接使用してください。

## Installing a specific version

特定の Python バージョンをインストールするには：

```console
$ uv python install 3.12
```

複数の Python バージョンをインストールするには：

```console
$ uv python install 3.11 3.12
```

代替の Python 実装（例：PyPy）をインストールするには：

```console
$ uv python install pypy@3.12
```

詳細については、[`python install`](../concepts/python-versions.md#installing-a-python-version)のドキュメントを参照してください。

## Viewing Python installations

利用可能およびインストール済みの Python バージョンを表示するには：

```console
$ uv python list
```

詳細については、[`python list`](../concepts/python-versions.md#viewing-available-python-versions)のドキュメントを参照してください。

<!--TODO(zanieb): The above should probably link to a CLI reference and that content should be moved out of that file -->

## Automatic Python downloads

uv を使用するために Python を明示的にインストールする必要はありません。デフォルトでは、uv は必要に応じて Python バージョンを自動的にダウンロードします。たとえば、次のコマンドは Python 3.12 がインストールされていない場合にダウンロードします：

```console
$ uv run --python 3.12 python -c 'print("hello world")'
```

特定の Python バージョンが要求されていなくても、uv は必要に応じて最新バージョンをダウンロードします。たとえば、次のコマンドは新しい仮想環境を作成し、Python が見つからない場合は管理された Python バージョンをダウンロードします：

```console
$ uv venv
```

!!! tip

    自動 Python ダウンロードは、Python をダウンロードするタイミングをより制御したい場合に[簡単に無効にできます](../concepts/python-versions.md#disabling-automatic-python-downloads)。

<!-- TODO(zanieb): Restore when Python shim management is added
Note that when an automatic Python installation occurs, the `python` command will not be added to the shell. Use `uv python install-shim` to ensure the `python` shim is installed.
-->

## Using an existing Python installation

uv は、システムに存在する既存の Python インストールを使用します。この動作には設定は必要ありません：uv はコマンドの実行要件を満たす場合にシステム Python を使用します。詳細については、[Python の検出](../concepts/python-versions.md#discovery-of-python-versions)のドキュメントを参照してください。

uv にシステム Python を使用させるには、`--python-preference only-system` オプションを指定します。詳細については、[Python バージョンの優先順位の調整](../concepts/python-versions.md#adjusting-python-version-preferences)のドキュメントを参照してください。

## Next steps

`uv python` の詳細については、[Python バージョンの概念](../concepts/python-versions.md)ページおよび[コマンドリファレンス](../reference/cli.md#uv-python)を参照してください。

または、読み進めて uv で[スクリプトを実行](./scripts.md)し、Python を呼び出す方法を学んでください。
