# プロジェクト

Python プロジェクトは、複数のファイルにまたがる Python アプリケーションを管理するのに役立ちます。

!!! tip

    uv でプロジェクトを作成するための紹介をお探しですか？まずは[プロジェクトガイド](../guides/projects.md)をご覧ください。

## プロジェクトメタデータ

Python プロジェクトメタデータは、`pyproject.toml` ファイルで定義されます。

!!! tip

    `uv init` を使用して新しいプロジェクトを作成できます。詳細については、[プロジェクトの作成](#creating-projects)を参照してください。

最小限のプロジェクト定義には、名前、バージョン、および説明が含まれます：

```toml title="pyproject.toml"
[project]
name = "example"
version = "0.1.0"
description = "Add your description here"
```

[project] セクションに Python バージョンの要件を含めることをお勧めしますが、必須ではありません：

```toml title="pyproject.toml"
requires-python = ">=3.12"
```

Python バージョンの要件を含めることで、プロジェクトで許可される Python 構文が定義され、依存関係のバージョンの選択に影響を与えます（同じ Python バージョン範囲をサポートする必要があります）。

`pyproject.toml` には、`project.dependencies` および `project.optional-dependencies` フィールドにプロジェクトの依存関係もリストされます。uv は、`uv add` および `uv remove` を使用してコマンドラインからプロジェクトの依存関係を変更することをサポートしています。uv はまた、`tool.uv.sources` に[パッケージソース](./dependencies.md)を追加することで、標準の依存関係定義を拡張することをサポートしています。

!!! tip

    `pyproject.toml` の作成に関する詳細については、公式の[`pyproject.toml` ガイド](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)を参照してください。

## エントリポイントの定義

uv は、プロジェクトのエントリポイントを定義するために標準の `[project.scripts]` テーブルを使用します。

たとえば、`example_package_app` モジュールの `hello` 関数を呼び出す `hello` というコマンドを宣言するには：

```toml title="pyproject.toml"
[project.scripts]
hello = "example_package_app:hello"
```

!!! important

    `[project.scripts]` を使用するには、[ビルドシステム](#build-systems)を定義する必要があります。

## ビルドシステム

プロジェクトは、`pyproject.toml` に `[build-system]` を定義することができます。ビルドシステムは、プロジェクトのパッケージ化およびインストール方法を定義します。

uv は、プロジェクトにビルドシステムが定義されているかどうかを使用して、プロジェクトにインストールされるパッケージが含まれているかどうかを判断します。ビルドシステムが定義されていない場合、uv はプロジェクト自体をビルドまたはインストールしようとせず、その依存関係のみをビルドおよびインストールします。ビルドシステムが定義されている場合、uv はプロジェクトをビルドし、プロジェクト環境にインストールします。デフォルトでは、プロジェクトは[編集可能モード](https://setuptools.pypa.io/en/latest/userguide/development_mode.html)でインストールされ、ソースコードの変更が即座に反映され、再インストールが不要です。

### プロジェクトパッケージの構成

uv は、[`tool.uv.package`](../reference/settings.md#package) 設定を使用して、プロジェクトがパッケージ化されるかどうかを手動で宣言することもできます。

`tool.uv.package = true` を設定すると、プロジェクトがビルドされ、プロジェクト環境���インストールされるように強制されます。ビルドシステムが定義されていない場合、uv は setuptools レガシーバックエンドを使用します。

`tool.uv.package = false` を設定すると、プロジェクトパッケージがビルドおよびプロジェクト環境にインストールされないように強制されます。uv は、プロジェクトと対話する際に宣言されたビルドシステムを無視します。

## プロジェクトの作成

uv は `uv init` を使用してプロジェクトを作成することをサポートしています。

uv は作業ディレクトリにプロジェクトを作成するか、名前を指定してターゲットディレクトリにプロジェクトを作成します。例：`uv init foo`。ターゲットディレクトリに既にプロジェクトが存在する場合、つまり `pyproject.toml` が存在する場合、uv はエラーを出して終了します。

プロジェクトを作成する際、uv は[**アプリケーション**](#applications)と[**ライブラリ**](#libraries)の2種類を区別します。

デフォルトでは、uv はアプリケーション用のプロジェクトを作成します。代わりに `--lib` フラグを使用してライブラリ用のプロジェクトを作成できます。

### アプリケーション

アプリケーションプロジェクトは、Web サーバー、スクリプト、およびコマンドラインインターフェースに適しています。

アプリケーションは `uv init` のデフォルトターゲットですが、`--app` フラグを使用して指定することもできます：

```console
$ uv init --app example-app
$ tree example-app
example-app
├── .python-version
├── README.md
├── hello.py
└── pyproject.toml
```

アプリケーションを作成する際、uv は最小限の `pyproject.toml` を生成します。ビルドシステムは定義されておらず、ソースコードはトップレベルディレクトリにあります。例：`hello.py`。プロジェクトには、プロジェクト環境にビルドおよびインストールされるパッケージは含まれていません。

```toml title="pyproject.toml"
[project]
name = "example-app"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.11"
dependencies = []
```

作成されたスクリプトは、いくつかの標準的なボイラープレートを含む `main` 関数を定義します：

```python title="hello.py"
def main():
    print("Hello from example-app!")


if __name__ == "__main__":
    main()
```

そして `uv run` で実行できます：

```console
$ uv run hello.py
Hello from example-project!
```

### ライブラリ

ライブラリは、Python パッケージとしてビルドおよび配布されることを目的としたプロジェクトです。たとえば、PyPI にアップロードすることができます。ライブラリは、他のプロジェクトが使用する関数やオブジェクトを提供します。

`--lib` フラグを使用してライブラリを作成できます：

```console
$ uv init --lib example-lib
$ tree example-lib
example-lib
├── .python-version
├── README.md
├── pyproject.toml
└── src
    └── example_lib
        ├── py.typed
        └── __init__.py
```

ライブラリを作成する際、uv はビルドシステムを定義し、ソースコードを `src` ディレクトリに配置します。これにより、ライブラリがプロジェクトルートでの `python` 呼び出しから分離され、配布されるライブラリコードがプロジェクトの他のソースコードから適切に分離されます。プロジェクトには、`src/example_lib` にパッケージが含まれており、プロジェクト環境にビルドおよびインストールされます。

```toml title="pyproject.toml"
[project]
name = "example-lib"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.11"
dependencies = []

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

!!! note

    uv はまだビルドバックエンドを提供していません。デフォルトでは `hatchling` が使用されますが、他のオプションもあります。プロジェクト構造に合わせて `hatchling` を構成するために [hatch build](https://hatch.pypa.io/1.9/config/build/) オプションを使用する必要があるかもしれません。

    uv ビルドバックエンドの進捗状況は [astral-sh/uv#3957](https://github.com/astral-sh/uv/issues/3957) で追跡できます。

作成されたモジュールは、シンプルな API 関数を定義します：

```python title="__init__.py"
def hello() -> str:
    return "Hello from example-lib!"
```

そして、`uv run` を使用してインポートおよび実行できます：

```console
$ uv run --directory example-lib python -c "import example_lib; print(example_lib.hello())"
Hello from example-lib!
```

`--build-backend` を使用して、`hatchling`、`flit-core`、`pdm-backend`、`setuptools`、`maturin`、または `scikit-build-core` などの異なるビルドバックエンドテンプレートを選択できます。

```console
$ uv init --lib --build-backend maturin example-lib
$ tree example-lib
example-lib
├── .python-version
├── Cargo.toml
├── README.md
├── pyproject.toml
└── src
    ├── lib.rs
    └── example_lib
        ├── py.typed
        ├── __init__.py
        └── _core.pyi
```

そして、`uv run` を使用してインポートおよび実行できます：

```console
$ uv run --directory example-lib python -c "import example_lib; print(example_lib.hello())"
Hello from example-lib!
```

!!! tip

バイナリビルドバックエンド（`maturin` や `scikit-build-core` など）を使用する場合、`lib.rs` や `main.cpp` の変更には `--reinstall` を実行する必要があります。

### パッケージ化されたアプリケーション

`--package` フラグを `uv init` に渡して、配布可能なアプリケーションを作成できます。例：コマンドラインインターフェースを PyPI 経由で公開したい場合。uv はプロジェクトのビルドバックエンドを定義し、`[project.scripts]` エントリポイントを含め、プロジェクトパッケージをプロジェクト環境にインストールします。

プロジェクト構造はライブラリと同じように見えます：

```console
$ uv init --app --package example-packaged-app
$ tree example-packaged-app
example-packaged-app
├── .python-version
├── README.md
├── pyproject.toml
└── src
    └── example_packaged_app
        └── __init__.py
```

しかし、モジュールは CLI 関数を定義します：

```python title="__init__.py"
def main() -> None:
    print("Hello from example-packaged-app!")
```

そして、`pyproject.toml` にはスクリプトエントリポイントが含まれています：

```toml title="pyproject.toml" hl_lines="9 10"
[project]
name = "example-packaged-app"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.11"
dependencies = []

[project.scripts]
example-packaged-app = "example_packaged_app:main"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

これは `uv run` で実行できます：

```console
$ uv run --directory example-packaged-app example-packaged-app
Hello from example-packaged-app!
```

!!! tip

    既存のアプリケーションを配布可能なパッケージとして再定義するには、ビルドシステムを追加します。ただし、ビルドバックエンドによってはプロジェクトディレクトリ構造の変更が必要になる場合があります。

さらに、`--build-backend` を指定して、バイナリビルドバックエンド（`maturin` など）を含むパッケージ化されたアプリケーションのビルドバックエンドをカスタマイズできます。

```console
$ uv init --app --package --build-backend maturin example-packaged-app
$ tree example-packaged-app
example-packaged-app
├── .python-version
├── Cargo.toml
├── README.md
├── pyproject.toml
└── src
    ├── lib.rs
    └── example_packaged_app
        ├── __init__.py
        └── _core.pyi
```

これも `uv run` で実行できます：

```console
$ uv run --directory example-packaged-app example-packaged-app
Hello from example-packaged-app!
```

## プロジェクト環境

uv を使用してプロジェクトで作業する際、必要に応じて仮想環境が作成されます。一部の uv コマンドは一時的な環境を作成します（例：`uv run --isolated`）。uv はまた、プロジェクトとその依存関係を含む永続的な環境を `pyproject.toml` の隣の `.venv` ディレクトリに管理します。これは、エディタが見つけやすいようにプロジェクト内に保存されます。エディタはコード補完や型ヒントを提供するために環境を必要とします。`.venv` ディレクトリをバージョン管理に含めることは推奨されません。内部の `.gitignore` ファイルで自動的に `git` から除外されます。

プロジェクト環境でコマンドを実行するには、`uv run` を使用します。代わりに、プロジェクト環境を通常の仮想環境としてアクティブ化することもできます。

`uv run` が呼び出されると、プロジェクト環境がまだ存在しない場合は作成され、存在する場合は最新の状態に保たれます。プロジェクト環境は `uv sync` を使用して明示的に作成することもできます。

プロジェクト環境を手動で変更することは推奨されません。例：`uv pip install` を使用して変更すること。プロジェクトの依存関係には、`uv add` を使用して環境にパッケージを追加します。一度限りの要件には、[`uvx`](../guides/tools.md) または [`uv run --with`](#running-commands-with-additional-dependencies) を使用します。

!!! tip

    uv にプロジェクト環境を管理させたくない場合は、[`managed = false`](../reference/settings.md#managed) を設定して、プロジェクトの自動ロックおよび同期を無効にします。例：

    ```toml title="pyproject.toml"
    [tool.uv]
    managed = false
    ```

デフォルトでは、プロジェクトは編集可能モードでインストールされ、ソースコードの変更が即座に環境に反映されます。`uv sync` および `uv run` はどちらも `--no-editable` フラグを受け入れ、uv に非編集可能モードでプロジェクトをインストールするよう指示します。`--no-editable` は、プロジェクトを展開環境に含めるべきであり、元のソースコードに依存しない場合の展開ユースケースに使用されます。

### プロジェクト環境パスの構成

`UV_PROJECT_ENVIRONMENT` 環境変数を使用して、プロジェクト仮想環境パス（デフォルトでは `.venv`）を構成できます。

相対パスが指定された場合、ワークスペースルートに対して解決されます。絶対パスが指定された場合、そのまま使用されます。つまり、環境のための子ディレクトリは作成されません。指定されたパスに環境が存在しない場合、uv はそれを作成します。

このオプションを使用して、システム Python 環境に書き込むことができますが、推奨されません。`uv sync` はデフォルトで環境から不要なパッケージを削除し、そのためシステムが壊れた状態になる可能性があります。

!!! important

    絶対パスが指定され、この設定が複数のプロジェクトで使用される場合、環境は各プロジェクトの呼び出しによって上書きされます。この設定は、CI または Docker イメージで単一のプロジェクトに使用することをお勧めします。

!!! note

    uv はプロジェクト操作中に `VIRTUAL_ENV` 環境変数を読み取りません。`VIRTUAL_ENV` がプロジェクトの環境とは異なるパスに設定されている場合、警告が表示されます。

## プロジェクトロックファイル

uv は `pyproject.toml` の隣に `uv.lock` ファイルを作成します。

`uv.lock` は、すべての可能な Python マーカー（オペレーティングシステム、アーキテクチャ、Python バージョンなど）にわたってインストールされるパッケージをキャプチャする _ユニバーサル_ または _クロスプラットフォーム_ ロックファイルです。

`pyproject.toml` はプロジェクトの広範な要件を指定するために使用されますが、ロックファイルにはプロジェクト環境にインストールされる正確な解決バージョンが含まれています。このファイルはバージョン管理にチェックインされ、マシン間で一貫性のある再現可能なインストールを可能にします。

ロックファイルは、プロジェクトに取り組む開発者が一貫したパッケージバージョンセットを使用していることを保証します。さらに、アプリケーションとしてプロジェクトを展開する際に、使用される正確なパッケージバージョンセットが既知であることを保証します。

ロックファイルは、プロジェクト環境を使用する uv 呼び出し中に作成および更新されます。例：`uv sync` および `uv run`。ロックファイルは `uv lock` を使用して明示的に更新することもできます。

`uv.lock` は人間が読み取れる TOML ファイルですが、uv によって管理され、手動で編集するべきではありません。現在のところ、ロックファイルの Python 標準は存在しないため、このファイルの形式は uv に特有であり、他のツールでは使用できません。

!!! tip

    uv を他のツールやワークフローと統合する必要がある場合、`uv.lock` を `requirements.txt` 形式にエクスポートできます。`uv export --format requirements-txt` を使用します。生成された `requirements.txt` ファイルは `uv pip install` を使用してインストールするか、`pip` などの他のツールでインストールできます。

    一般的には、`uv.lock` と `requirements.txt` ファイルの両方を使用することはお勧めしません。`uv.lock` ファイルをエクスポートする場合は、ユースケースを議論するために問題を開くことを検討してください。

### ロックファイルが最新であるかどうかの確認

`uv sync` および `uv run` 呼び出し中にロックファイルを更新しないようにするには、`--frozen` フラグを使用します。

`uv run` 呼び出し中に環境を更新しないようにするには、`--no-sync` フラグを使用します。

ロックファイルがプロジェクトメタデータと一致するかどうかを確認するには、`--locked` フラグを使用します。ロックファイルが最新でない場合、ロックファイルを更新する代わりにエラーが発生します。

### ロックされたパッケージバージョンのアップグレード

デフォルトでは、uv は `uv sync` および `uv lock` 実行時にロックされたバージョンのパッケージを優先します。プロジェクトの依存関係制約が以前のロックされたバージョンを除外しない限り、パッケージバージョンは変更されません。

すべてのパッケージをアップグレードするには：

```console
$ uv lock --upgrade
```

単一のパッケージを最新バージョンにアップグレードし、他のすべてのパッケージのロックされたバージョンを保持するには：

```console
$ uv lock --upgrade-package <package>
```

特定のバージョンに単一のパッケージをアップグレードするには：

```console
$ uv lock --upgrade-package <package>==<version>
```

!!! note

    すべての場合において、アップグレードはプロジェクトの依存関係制約に制限されます。たとえば、プロジェクトがパッケージの上限を定義している場合、アップグレードはそのバージョンを超えません。

### 制限された解決環境

プロジェクトがより限定されたプラットフォームまたは Python バージョンのセットをサポートしている場合、`environments` 設定を使用して解決されるプラットフォームのセットを制約できます。この設定は PEP 508 環境マーカーのリストを受け入れます。たとえば、ロックファイルを macOS および Linux に制約し、Windows を除外するには：

```toml title="pyproject.toml"
[tool.uv]
environments = [
    "sys_platform == 'darwin'",
    "sys_platform == 'linux'",
]
```

`environments` 設定のエントリは互いに排他的である必要があります（つまり、重複してはなりません）。たとえば、`sys_platform == 'darwin'` と `sys_platform == 'linux'` は排他的ですが、`sys_platform == 'darwin'` と `python_version >= '3.9'` は排他的ではありません。両方が同時に真である可能性があるためです。

### オプションの依存関係

uv は、プロジェクトによって宣言されたすべてのオプションの依存関係（「エクストラ」）が互換性があることを要求し、ロックファイルを作成する際にすべてのオプションの依存関係を一緒に解決します。

1つのエクストラで宣言されたオプションの依存関係が別のエクストラの依存関係と互換性がない場合、uv はプロジェクトの要件を解決できず、エラーを発生させます。

!!! note

    現在、競合するオプションの依存関係を宣言する方法はありません。サポートの追跡については、[astral.sh/uv#6981](https://github.com/astral-sh/uv/issues/6981) を参照してください。

## 依存関係の管理

uv は CLI を使用して依存関係を追加、更新、および削除することができます。

依存関係を追加するには：

```console
$ uv add httpx
```

uv は、[編集可能な依存関係](./dependencies.md#editable-dependencies)、[開発依存関係](./dependencies.md#development-dependencies)、[オプションの依存関係](./dependencies.md#optional-dependencies)、および代替の[依存関係ソース](./dependencies.md#dependency-sources)を追加することをサポートしています。詳細については、[依存関係の指定](./dependencies.md)のドキュメントを参照してください。

依存関係を解決できない場合、uv はエラーを発生させます。例：

```console
$ uv add 'httpx>9999'
error: Because only httpx<=9999 is available and example==0.1.0 depends on httpx>9999, we can conclude that example==0.1.0 cannot be used.
And because only example==0.1.0 is available and you require example, we can conclude that the requirements are unsatisfiable.
```

依存関係を削除するには：

```console
$ uv remove httpx
```

既存の依存関係を更新するには、例：`httpx` バージョンに下限を追加するには：

```console
$ uv add 'httpx>0.1.0'
```

!!! note

    依存関係の「更新」とは、`pyproject.toml` の依存関係の制約を変更することを指します。依存関係のロックされたバージョンは、新しい制約を満たすために必要な場合にのみ変更されます。パッケージバージョンを制約内で最新に強制的に更新するには、`--upgrade-package <name>` を使用します。例：

    ```console
    $ uv add 'httpx>0.1.0' --upgrade-package httpx
    ```

    パッケージバージョンのアップグレードの詳細については、[ロックファイル](#upgrading-locked-package-versions)セクションを参照してください。

または、`httpx` の制約を変更するには：

```console
$ uv add 'httpx<0.2.0'
```

依存関係ソースを追加するには、例：開発中に GitHub から `httpx` を使用するには：

```console
$ uv add git+https://github.com/encode/httpx
```

### プラットフォーム固有の依存関係

特定のプラットフォームまたは特定の Python バージョンでのみ依存関係をインストールするには、Python の標準化された[環境マーカー](https://peps.python.org/pep-0508/#environment-markers)構文を使用します。

たとえば、Linux で `jax` をインストールし、Windows や macOS ではインストールしないようにするには：

```console
$ uv add 'jax; sys_platform == "linux"'
```

結果として得られる `pyproject.toml` には、依存関係定義に環境マーカーが含まれます：

```toml title="pyproject.toml" hl_lines="6"
[project]
name = "project"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = ["jax; sys_platform == 'linux'"]
```

同様に、Python 3.11 以降で `numpy` を含めるには：

```console
$ uv add 'numpy; python_version >= "3.11"'
```

利用可能なマーカーおよび演算子の完全な列挙については、Python の[環境マーカー](https://peps.python.org/pep-0508/#environment-markers)ドキュメントを参照してください。

## コマンドの実行

プロジェクトで作業する際、プロジェクトは `.venv` に仮想環境としてインストールされます。この環境はデフォルトで現在のシェルから分離されているため、プロジェクトを必要とする呼び出し（例：`python -c "import example"`）は失敗します。代わりに、`uv run` を使用してプロジェクト環境でコマンドを実行します：

```console
$ uv run python -c "import example"
```

`run` を使用する際、uv は指定されたコマンドを実行する前にプロジェクト環境が最新であることを確認します。

指定されたコマンドはプロジェクト環境によって提供されるか、外部に存在することができます。例：

```console
$ # プロジェクトが `example-cli` を提供していると仮定します
$ uv run example-cli foo

$ # プロジェクトが利用可能である必要がある `bash` スクリプトを実行します
$ uv run bash scripts/foo.sh
```

### 追加の依存関係を持つコマンドの実行

追加の依存関係や異なるバージョンの依存関係を呼び出しごとに要求できます。

`--with` オプションを使用して、呼び出しのために依存関係を含めます。例：異なるバージョンの `httpx` を要求するには：

```console
$ uv run --with httpx==0.26.0 python -c "import httpx; print(httpx.__version__)"
0.26.0
$ uv run --with httpx==0.25.0 python -c "import httpx; print(httpx.__version__)"
0.25.0
```

要求されたバージョンは、プロジェクトの要件に関係なく尊重されます。たとえば、プロジェクトが `httpx==0.24.0` を要求している場合でも、上記の出力は同じです。

### スクリプトの実行

インラインメタデータを宣言するスクリプトは、自動的にプロジェクトから分離された環境で実行されます。詳細については、[スクリプトガイド](../guides/scripts.md#declaring-script-dependencies)を参照してください。

たとえば、次のスクリプトがあるとします：

```python title="example.py"
# /// script
# dependencies = [
#   "httpx",
# ]
# ///

import httpx

resp = httpx.get("https://peps.python.org/api/peps.json")
data = resp.json()
print([(k, v["title"]) for k, v in data.items()][:10])
```

`uv run example.py` の呼び出しは、指定された依存関係のみを持つ _分離された_ 環境で実行されます。

## 多くのパッケージを含むプロジェクト

多くのパッケージで構成されるプロジェクトで作業している場合は、[ワークスペース](./workspaces.md)のドキュメントを参照してください。

## プロジェクトのビルド

プロジェクトを他の人に配布するには（例：PyPI などのインデックスにアップロードするには）、配布可能な形式にビルドする必要があります。

Python プロジェクトは通常、ソースディストリビューション（sdist）とバイナリディストリビューション（ホイール）の両方として配布されます。前者は通常、プロジェクトのソースコードと追加のメタデータを含む `.tar.gz` または `.zip` ファイルであり、後者は直接インストール可能な事前ビルドのアーティファクトを含む `.whl` ファイルです。

`uv build` を使用して、プロジェクトのソースディストリビューションおよびバイナリディストリビューションの両方をビルドできます。デフォルトでは、`uv build` は現在のディレクトリでプロジェクトをビルドし、ビルドされたアーティファクトを `dist/` サブディレクトリに配置します：

```console
$ uv build
$ ls dist/
example-0.1.0-py3-none-any.whl
example-0.1.0.tar.gz
```

`uv build` にパスを指定して、別のディレクトリでプロジェクトをビルドできます。例：`uv build path/to/project`。

`uv build` は最初にソースディストリビューションをビルドし、そのソースディストリビューションからバイナリディストリビューション（ホイール）をビルドします。

`uv build` を `uv build --sdist` を使用してソースディストリビューションのビルドに限定するか、`uv build --wheel` を使用してバイナリディストリビューションのビルドに限定するか、`uv build --sdist --wheel` を使用してソースから両方のディストリビューションをビルドすることができます。

`uv build` は `--build-constraint` を受け入れ、ビルドプロセス中のビルド要件のバージョンを制約するために使用できます。`--require-hashes` と組み合わせると、uv はプロジェクトのビルドに使用される要件が特定の既知のハッシュと一致することを強制し、再現性を確保します。

たとえば、次の `constraints.txt` があるとします：

```text
setuptools==68.2.2 --hash=sha256:b454a35605876da60632df1a60f736524eb73cc47bbc9f3f1ef1b644de74fd2a
```

次のコマンドを実行すると、指定されたバージョンの `setuptools` を使用してプロジェクトがビルドされ、ダウンロードされた `setuptools` ディストリビューションが指定されたハッシュと一致することが確認されます：

```console
$ uv build --build-constraint constraints.txt --require-hashes
```

## ビルドの分離

デフォルトでは、uv はすべてのパッケージを分離された仮想環境でビルドします。これは [PEP 517](https://peps.python.org/pep-0517/) に準拠しています。一部のパッケージはビルドの分離と互換性がありません。意図的な場合（例：重いビルド依存関係の使用、主に PyTorch）や、意図しない場合（例：レガシーパッケージ設定の使用）があります。

特定の依存関係のビルド分離を無効にするには、`pyproject.toml` の `no-build-isolation-package` リストに追加します：

```toml title="pyproject.toml"
[project]
name = "project"
version = "0.1.0"
description = "..."
readme = "README.md"
requires-python = ">=3.12"
dependencies = ["cchardet"]

[tool.uv]
no-build-isolation-package = ["cchardet"]
```

ビルドの分離なしでパッケージをインストールするには、パッケージのビルド依存関係をパッケージ自体をインストールする _前に_ プロジェクト環境にインストールする必要があります。これを実現するために、ビルド依存関係とそれを必要とするパッケージを別々のエクストラに分けることができます。例えば：

```toml title="pyproject.toml"
[project]
name = "project"
version = "0.1.0"
description = "..."
readme = "README.md"
requires-python = ">=3.12"
dependencies = []

[project.optional-dependencies]
build = ["setuptools", "cython"]
compile = ["cchardet"]

[tool.uv]
no-build-isolation-package = ["cchardet"]
```

上記の例では、ユーザーは最初に `build` 依存関係を同期します：

```console
$ uv sync --extra build
 + cython==3.0.11
 + foo==0.1.0 (from file:///Users/crmarsh/workspace/uv/foo)
 + setuptools==73.0.1
```

次に `compile` 依存関係を同期します：

```console
$ uv sync --extra compile
 + cchardet==2.1.7
 - cython==3.0.11
 - setuptools==73.0.1
```

`uv sync --extra compile` はデフォルトで `cython` および `setuptools` パッケージをアンインストールします。ビルド依存関係を保持するには、2回目の `uv sync` 呼び出しで両方のエクストラを含めます：

```console
$ uv sync --extra build
$ uv sync --extra build --extra compile
```

一部のパッケージ（上記の `cchardet` など）は、`uv sync` のインストールフェーズでのみビルド依存関係を必要とします。他のパッケージ（`flash-attn` など）は、プロジェクトのロックファイルを解決するための解決フェーズでもビルド依存関係が必要です。

このような場合、ビルド依存関係は、`uv lock` または `uv sync` コマンドを実行する前に、低レベルの `uv pip` API を使用してインストールする必要があります。例：

```toml title="pyproject.toml"
[project]
name = "project"
version = "0.1.0"
description = "..."
readme = "README.md"
requires-python = ">=3.12"
dependencies = ["flash-attn"]

[tool.uv]
no-build-isolation-package = ["flash-attn"]
```

次のコマンドシーケンスを実行して `flash-attn` を同期できます：

```console
$ uv venv
$ uv pip install torch
$ uv sync
```

または、[`dependency-metadata`](../reference/settings.md#dependency-metadata) 設定を使用して `flash-attn` メタデータを事前に提供し、依存関係解決フェーズ中にパッケージをビルドする必要を回避できます。例：`flash-attn` メタデータを事前に提供するには、次の内容を `pyproject.toml` に含めます：

```toml title="pyproject.toml"
[[tool.uv.dependency-metadata]]
name = "flash-attn"
version = "2.6.3"
requires-dist = ["torch", "einops"]
```

!!! tip

    `flash-attn` のようなパッケージのメタデータを確認するには、適切な Git リポジトリに移動するか、[PyPI](https://pypi.org/project/flash-attn) で検索し、パッケージのソースディストリビューションをダウンロードします。パッケージの要件は通常、`setup.py` または `setup.cfg` ファイルに記載されています。

    （パッケージにビルドディストリビューションが含まれている場合、解凍して `METADATA` ファイルを見つけることができます。ただし、ビルドディストリビューションが存在する場合、uv に既に利用可能であるため、事前にメタデータを提供する必要はありません。）

一度含めると、2段階の `uv sync` プロセスを使用してビルド依存関係をインストールできます。次の `pyproject.toml` があるとします：

```toml title="pyproject.toml"
[project]
name = "project"
version = "0.1.0"
description = "..."
readme = "README.md"
requires-python = ">=3.12"
dependencies = []

[project.optional-dependencies]
build = ["torch", "setuptools", "packaging"]
compile = ["flash-attn"]

[tool.uv]
no-build-isolation-package = ["flash-attn"]

[[tool.uv.dependency-metadata]]
name = "flash-attn"
version = "2.6.3"
requires-dist = ["torch", "einops"]
```

次のコマンドシーケンスを実行して `flash-attn` を同期できます：

```console
$ uv sync --extra build
$ uv sync --extra build --extra compile
```

!!! note

    `tool.uv.dependency-metadata` の `version` フィールドは、レジストリベースの依存関係に対してはオプションです（省略された場合、uv はメタデータがパッケージのすべてのバージョンに適用されると見なします）。ただし、直接 URL 依存関係（Git 依存関係など）に対しては必須です。
