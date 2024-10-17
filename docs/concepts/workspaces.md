# Workspaces

Inspired by the [Cargo](https://doc.rust-lang.org/cargo/reference/workspaces.html) concept of the
same name, a workspace is "a collection of one or more packages, called _workspace members_, that
are managed together."

ワークスペースは、共通の依存関係を持つ複数のパッケージに分割することで、大規模なコードベースを整理します。たとえば、FastAPI ベースの Web アプリケーションと、同じ Git リポジトリ内でバージョン管理およびメンテナンスされる一連のライブラリを考えてみてください。

ワークスペースでは、各パッケージが独自の `pyproject.toml` を定義しますが、ワークスペースは単一のロックファイルを共有し、ワークスペースが一貫した依存関係セットで動作することを保証します。

したがって、`uv lock` はワークスペース全体で一度に操作し、`uv run` および `uv sync` はデフォルトでワークスペースルートで操作しますが、どちらも `--package` 引数を受け入れ、ワークスペースディレクトリの任意の場所から特定のワークスペースメンバーでコマンドを実行できます。

## 始めに

ワークスペースを作成するには、`pyproject.toml` に `tool.uv.workspace` テーブルを追加します。これにより、そのパッケージをルートとするワークスペースが暗黙的に作成されます。

!!! tip

    デフォルトでは、既存のパッケージ内で `uv init` を実行すると、新しく作成されたメンバーがワークスペースに追加され、ワークスペースルートに `tool.uv.workspace` テーブルがまだ存在しない場合は作成されます。

ワークスペースを定義する際には、`members`（必須）および `exclude`（オプション）キーを指定する必要があります。これらは、特定のディレクトリをメンバーとして含めたり除外したりするためのもので、グロブのリストを受け入れます。

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = ["bird-feeder", "tqdm>=4,<5"]

[tool.uv.sources]
bird-feeder = { workspace = true }

[tool.uv.workspace]
members = ["packages/*"]
exclude = ["packages/seeds"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

`members` グロブに含まれ（`exclude` グロブによって除外されていない）すべてのディレクトリには `pyproject.toml` ファイルが含まれている必要があります。ただし、ワークスペースメンバーは[アプリケーション](./projects.md#applications)または[ライブラリ](./projects.md#libraries)のいずれかである必要があります。どちらもワークスペースコンテキストでサポートされています。

すべてのワークスペースにはルートが必要であり、それもワークスペースメンバーです。上記の例では、`albatross` がワークスペースルートであり、ワークスペースメンバーには `packages` ディレクトリのすべてのプロジェクトが含まれますが、`seeds` は除外されています。

デフォルトでは、`uv run` および `uv sync` はワークスペースルートで操作します。たとえば、上記の例では、`uv run` および `uv run --package albatross` は同等であり、`uv run --package bird-feeder` は `bird-feeder` パッケージでコマンドを実行します。

## ワークスペースソース

ワークスペース内では、ワークスペースメンバーへの依存関係は [`tool.uv.sources`](./dependencies.md) を介して提供されます。例えば：

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = ["bird-feeder", "tqdm>=4,<5"]

[tool.uv.sources]
bird-feeder = { workspace = true }

[tool.uv.workspace]
members = ["packages/*"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

この例では、`albatross` プロジェクトはワークスペースのメンバーである `bird-feeder` プロジェクトに依存しています。`tool.uv.sources` テーブルの `workspace = true` キーと値のペアは、`bird-feeder` 依存関係が PyPI や他のレジストリから取得されるのではなく、ワークスペースによって提供されるべきであることを示しています。

ワークスペースルートの `tool.uv.sources` 定義は、特定のメンバーの `tool.uv.sources` でオーバーライドされない限り、すべてのメンバーに適用されます。例えば、次の `pyproject.toml` がある場合：

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = ["bird-feeder", "tqdm>=4,<5"]

[tool.uv.sources]
bird-feeder = { workspace = true }
tqdm = { git = "https://github.com/tqdm/tqdm" }

[tool.uv.workspace]
members = ["packages/*"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

すべてのワークスペースメンバーは、デフォルトで GitHub から `tqdm` をインストールしますが、特定のメンバーが独自の `tool.uv.sources` テーブルで `tqdm` エントリをオーバーライドしない限りです。

## ワークスペースレイアウト

最も一般的なワークスペースレイアウトは、ルートプロジェクトと一連の付随するライブラリとして考えることができます。

例えば、上記の例を続けると、このワークスペースには `albatross` という明示的なルートがあり、`packages` ディレクトリに 2 つのライブラリ（`bird-feeder` と `seeds`）があります。

```text
albatross
├── packages
│   ├── bird-feeder
│   │   ├── pyproject.toml
│   │   └── src
│   │       └── bird_feeder
│   │           ├── __init__.py
│   │           └── foo.py
│   └── seeds
│       ├── pyproject.toml
│       └── src
│           └── seeds
│               ├── __init__.py
│               └── bar.py
├── pyproject.toml
├── README.md
├── uv.lock
└── src
    └── albatross
        └── main.py
```

`pyproject.toml` で `seeds` が除外されているため、ワークスペースには合計 2 つのメンバーがあります：`albatross`（ルート）と `bird-feeder`。

## ワークスペースを使用する場合と使用しない場合

ワークスペースは、単一のリポジトリ内で複数の相互接続されたパッケージの開発を支援することを目的としています。コードベースが複雑になるにつれて、それをより小さく、再利用可能なパッケージに分割し、それぞれが独自の依存関係とバージョン制約を持つことが役立ちます。

ワークスペースは、分離と関心の分離を強制するのに役立ちます。例えば、uv では、コアライブラリとコマンドラインインターフェースのために別々のパッケージを持ち、コアライブラリを CLI とは独立してテストできるようにしています。

ワークスペースの他の一般的な使用例には次のようなものがあります：

- 拡張モジュール（Rust、C++ など）で実装されたパフォーマンスクリティカルなサブルーチンを持つライブラリ。
- プラグインシステムを持つライブラリで、各プラグインがルートに依存する別個のワークスペースパッケージである場合。

ワークスペースは、メンバーが競合する要件を持っている場合や、各メンバーに対して個別の仮想環境を必要とする場合には適していません。この場合、パス依存関係が好ましいことがよくあります。例えば、`albatross` とそのメンバーをワークスペースにグループ化する代わりに、各パッケージを独立したプロジェクトとして定義し、`tool.uv.sources` でパス依存関係としてパッケージ間の依存関係を定義できます。

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = ["bird-feeder", "tqdm>=4,<5"]

[tool.uv.sources]
bird-feeder = { path = "packages/bird-feeder" }

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

このアプローチは多くの同じ利点を提供しますが、依存関係の解決と仮想環境の管理に対するより細かい制御を可能にします（`uv run --package` が利用できなくなるという欠点があります。代わりに、コマンドは関連するパッケージディレクトリから実行する必要があります）。

最後に、uv のワークスペースは、すべてのメンバーの `requires-python` 値の交差を取り、ワークスペース全体で単一の `requires-python` を強制します。特定のメンバーをワークスペースの他の部分でサポートされていない Python バージョンでテストする必要がある場合は、そのメンバーを別の仮想環境にインストールするために `uv pip` を使用する必要があるかもしれません。

!!! note

    Python は依存関係の分離を提供しないため、uv はパッケージが宣言された依存関係のみを使用することを保証できません。特にワークスペースでは、uv はパッケージが他のワークスペースメンバーによって宣言された依存関係をインポートしないことを保証できません。
