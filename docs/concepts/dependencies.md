# Specifying dependencies

uv では、プロジェクトの依存関係は `pyproject.toml` の 2 つのテーブル `project.dependencies` と `tool.uv.sources` に分けて宣言されます。

`project.dependencies` は、PyPI にアップロードする際やホイールをビルドする際に伝播される、標準に準拠した依存関係のメタデータを定義します。

`tool.uv.sources` は、開発中に組み込まれる追加のソースで依存関係のメタデータを強化します。依存関係のソースは、Git リポジトリ、URL、ローカルパス、または代替レジストリである可能性があります。

`tool.uv.sources` により、uv は標準の `project.dependencies` ではサポートされていない編集可能なインストールや相対パスなどの一般的なパターンをサポートできます。例えば：

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
dependencies = [
  "bird-feeder",
]

[tool.uv.sources]
bird-feeder = { path = "./packages/bird-feeder" }
```

## プロジェクトの依存関係

`project.dependencies` テーブルは、PyPI にアップロードする際やホイールをビルドする際に使用される依存関係を表します。個々の依存関係は [PEP 508](#pep-508) 構文を使用して指定され、テーブルは [PEP 621](https://packaging.python.org/en/latest/specifications/pyproject-toml/) 標準に従います。

`project.dependencies` は、プロジェクトに必要なパッケージのリストと、それらをインストールする際に使用するバージョン制約を定義します。各エントリには依存関係の名前とバージョンが含まれます。エントリには、プラットフォーム固有のパッケージのためのエクストラや環境マーカーを含めることができます。例えば：

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
dependencies = [
  # この範囲の任意のバージョン
  "tqdm >=4.66.2,<5",
  # 正確にこのバージョンの torch
  "torch ==2.2.2",
  # torch エクストラ付きの transformers をインストール
  "transformers[torch] >=4.39.3,<5",
  # 古い python バージョンでのみこのパッケージをインストール
  # 詳細は「環境マーカー」を参照
  "importlib_metadata >=7.1.0,<8; python_version < '3.10'",
  "mollymawk ==0.1.0"
]
```

プロジェクトが標準パッケージインデックスからのパッケージのみを必要とする場合、`project.dependencies` だけで十分です。プロジェクトが Git、リモート URL、またはローカルソースからのパッケージに依存している場合、`tool.uv.sources` を使用して、標準に準拠した `project.dependencies` テーブルから逸脱することなく依存関係のメタデータを強化できます。

!!! tip

    CLI から `pyproject.toml` から依存関係を追加、削除、または更新するには、[プロジェクト](./projects.md#managing-dependencies) ドキュメントを参照してください。

## 依存関係のソース

開発中、プロジェクトは PyPI で利用できないパッケージに依存することがあります。uv でサポートされている追加のソースは次のとおりです：

- インデックス: 特定のパッケージインデックスから解決されたパッケージ。
- Git: Git リポジトリ。
- URL: リモートホイールまたはソースディストリビューション。
- パス: ローカルホイール、ソースディストリビューション、またはプロジェクトディレクトリ。
- ワークスペース: 現在のワークスペースのメンバー。

各依存関係には単一のソースのみを定義できます。

非 uv プロジェクトが Git またはパス依存関係としてソースを持つプロジェクトを使用する場合、`project.dependencies` および `project.optional-dependencies` のみが尊重されます。ソーステーブルに提供された情報は、他のパッケージマネージャーに特有の形式で再指定する必要があります。

uv に `tool.uv.sources` テーブルを無視するよう指示するには（例：パッケージの公開されたメタデータで解決をシミュレートするため）、`--no-sources` フラグを使用します：

```console
$ uv lock --no-sources
```

`--no-sources` の使用は、uv が特定の依存関係を満たすために発見できる [ワークスペースメンバー](#workspace-member) を防ぐこともあります。

### Index
特定のインデックスに Python パッケージを固定するには、`pyproject.toml` に名前付きインデックスを追加します：

```toml title="pyproject.toml"
[project]
dependencies = [
  "torch",
]

[tool.uv.sources]
torch = { index = "pytorch" }

[[tool.uv.index]]
name = "pytorch"
url = "https://download.pytorch.org/whl/cpu"
explicit = true
```

`explicit` フラグはオプションであり、インデックスが `tool.uv.sources` で明示的に指定されたパッケージのみに使用されるべきことを示します。`explicit` が設定されていない場合、他のパッケージが他の場所で見つからない場合にインデックスから解決されることがあります。

### Git

Git 依存関係ソースを追加するには、クローンする Git 互換 URL に `git+` をプレフィックスします。

例えば：

```console
$ uv add git+https://github.com/encode/httpx
```

は、次のような `pyproject.toml` を生成します：

```toml title="pyproject.toml"
[project]
dependencies = [
    "httpx",
]

[tool.uv.sources]
httpx = { git = "https://github.com/encode/httpx" }
```

リビジョン、タグ、またはブランチも含めることができます：

```console
$ uv add git+https://github.com/encode/httpx --tag 0.27.0
$ uv add git+https://github.com/encode/httpx --branch main
$ uv add git+https://github.com/encode/httpx --rev 326b943
```

Git 依存関係は、`{ git = <url> }` 構文を使用して `pyproject.toml` に手動で追加または編集することもできます。パッケージがリポジトリのルートにない場合、`subdirectory` を指定できます。

### URL

URL ソースを追加するには、ホイール（`.whl` で終わる）またはソースディストリビューション（通常は `.tar.gz` または `.zip` で終わる）への `https://` URL を提供します。サポートされているすべての形式については[こちら](../concepts/resolution.md#source-distribution)を参照してください。

例えば：

```console
$ uv add "https://files.pythonhosted.org/packages/5c/2d/3da5bdf4408b8b2800061c339f240c1802f2e82d55e50bd39c5a881f47f0/httpx-0.27.0.tar.gz"
```

は、次のような `pyproject.toml` を生成します：

```toml title="pyproject.toml"
[project]
dependencies = [
    "httpx",
]

[tool.uv.sources]
httpx = { url = "https://files.pythonhosted.org/packages/5c/2d/3da5bdf4408b8b2800061c339f240c1802f2e82d55e50bd39c5a881f47f0/httpx-0.27.0.tar.gz" }
```

URL 依存関係は、`{ url = <url> }` 構文を使用して `pyproject.toml` に手動で追加または編集することもできます。ソースディストリビューションがアーカイブのルートにない場合、`subdirectory` を指定できます。

### パス

パスソースを追加するには、ホイール（`.whl` で終わる）、ソースディストリビューション（通常は `.tar.gz` または `.zip` で終わる）、または `pyproject.toml` を含むディレクトリのパスを提供します。サポートされているすべての形式については[こちら](../concepts/resolution.md#source-distribution)を参照してください。

例えば：

```console
$ uv add /example/foo-0.1.0-py3-none-any.whl
```

は、次のような `pyproject.toml` を生成します：

```toml title="pyproject.toml"
[project]
dependencies = [
    "foo",
]

[tool.uv.sources]
foo = { path = "/example/foo-0.1.0-py3-none-any.whl" }
```

パスは相対パスでもかまいません：

```console
$ uv add ./foo-0.1.0-py3-none-any.whl
```

または、プロジェクトディレクトリへのパス：

```console
$ uv add ~/projects/bar/
```

!!! important

    デフォルトでは、パス依存関係には[編集可能なインストール](#editable-dependencies)は使用されません。プロジェクトディレクトリに対して編集可能なインストールを要求するには：

    ```console
    $ uv add --editable ~/projects/bar/
    ```

    ただし、手動のパス依存関係の代わりに [_ワークスペース_](./workspaces.md) を使用することをお勧めします。

### ワークスペースメンバー

ワークスペースメンバーへの依存関係を宣言するには、メンバー名を `{ workspace = true }` として追加します。すべてのワークスペースメンバーは明示的に記載する必要があります。ワークスペースメンバーは常に[編集可能](#editable-dependencies)です。ワークスペースの詳細については、[ワークスペース](./workspaces.md) ドキュメントを参照してください。

```toml title="pyproject.toml"
[project]
dependencies = [
  "mollymawk ==0.1.0"
]

[tool.uv.sources]
mollymawk = { workspace = true }

[tool.uv.workspace]
members = [
  "packages/mollymawk"
]
```

### プラットフォーム固有のソース

ソースを特定のプラットフォームまたは Python バージョンに限定するには、[PEP 508](https://peps.python.org/pep-0508/#environment-markers) 互換の環境マーカーをソースに提供します。

例えば、macOS でのみ GitHub から `httpx` を取得するには、次のようにします：

```toml title="pyproject.toml"
[project]
dependencies = [
  "httpx",
]

[tool.uv.sources]
httpx = { git = "https://github.com/encode/httpx", tag = "0.27.2", marker = "sys_platform == 'darwin'" }
```

ソースにマーカーを指定することで、uv はすべてのプラットフォームで `httpx` を含めますが、macOS では GitHub からソースをダウンロードし、他のすべてのプラットフォームでは PyPI にフォールバックします。

### 複数のソース

単一の依存関係に対して複数のソースを指定するには、[PEP 508](https://peps.python.org/pep-0508/#environment-markers) 互換の環境マーカーで区別されたソースのリストを提供します。

例えば、macOS と Linux で異なる `httpx` コミットを取得するには：

```toml title="pyproject.toml"
[project]
dependencies = [
  "httpx",
]

[tool.uv.sources]
httpx = [
  { git = "https://github.com/encode/httpx", tag = "0.27.2", marker = "sys_platform == 'darwin'" },
  { git = "https://github.com/encode/httpx", tag = "0.24.1", marker = "sys_platform == 'linux'" },
]
```
この戦略は、環境マーカーに基づいて異なるインデックスからパッケージを取得することにも及びます。例えば、プラットフォームに基づいて異なる PyTorch インデックスから `torch` を取得するには、次のようにします：

```toml title="pyproject.toml"
[project]
dependencies = ["torch"]

[tool.uv.sources]
torch = [
  { index = "torch-cu118", marker = "sys_platform == 'darwin'"},
  { index = "torch-cu124", marker = "sys_platform != 'darwin'"},
]

[[tool.uv.index]]
name = "torch-cu118"
url = "https://download.pytorch.org/whl/cu118"

[[tool.uv.index]]
name = "torch-cu124"
url = "https://download.pytorch.org/whl/cu124"

```

## オプションの依存関係

ライブラリとして公開されるプロジェクトでは、デフォルトの依存関係ツリーを減らすために一部の機能をオプションにすることが一般的です。例えば、Pandas には、Excel パーサーや `matplotlib` を明示的に必要としない限りインストールしないようにするための [`excel` エクストラ](https://pandas.pydata.org/docs/getting_started/install.html#excel-files) や [`plot` エクストラ](https://pandas.pydata.org/docs/getting_started/install.html#visualization) があります。エクストラは `package[<extra>]` 構文を使用して要求されます。例えば、`pandas[plot, excel]`。

オプションの依存関係は、[PEP 508](#pep-508) 構文に従って、エクストラ名からその依存関係への TOML テーブルである `[project.optional-dependencies]` に指定されます。

オプションの依存関係は、通常の依存関係と同様に `tool.uv.sources` にエントリを持つことができます。

```toml title="pyproject.toml"
[project]
name = "pandas"
version = "1.0.0"

[project.optional-dependencies]
plot = [
  "matplotlib>=3.6.3"
]
excel = [
  "odfpy>=1.4.1",
  "openpyxl>=3.1.0",
  "python-calamine>=0.1.7",
  "pyxlsb>=1.0.10",
  "xlrd>=2.0.1",
  "xlsxwriter>=3.0.5"
]
```

オプションの依存関係を追加するには、`--optional <extra>` オプションを使用します：

```console
$ uv add httpx --optional network
```

## 開発依存関係

オプションの依存関係とは異なり、開発依存関係はローカルのみであり、PyPI や他のインデックスに公開する際にはプロジェクトの要件に含まれません。そのため、開発依存関係は `[project]` の代わりに `[tool.uv]` に含まれます。

開発依存関係は、通常の依存関係と同様に `tool.uv.sources` にエントリを持つことができます。

```toml title="pyproject.toml"
[tool.uv]
dev-dependencies = [
  "pytest >=8.1.1,<9"
]
```

開発依存関係を追加するには、`--dev` フラグを含めます：

```console
$ uv add ruff --dev
```

## Build dependencies

If a project is structured as [Python package](./projects.md#build-systems), it may declare
dependencies that are required to build the project, but not required to run it. These dependencies
are specified in the `[build-system]` table under `build-system.requires`, following
[PEP 518](https://peps.python.org/pep-0518/).

For example, if a project uses `setuptools` as its build backend, it should declare `setuptools` as
a build dependency:

```toml title="pyproject.toml"
[project]
name = "pandas"
version = "0.1.0"

[build-system]
requires = ["setuptools>=42"]
build-backend = "setuptools.build_meta"
```

By default, uv will respect `tool.uv.sources` when resolving build dependencies. For example, to use
a local version of `setuptools` for building, add the source to `tool.uv.sources`:

```toml title="pyproject.toml"
[project]
name = "pandas"
version = "0.1.0"

[build-system]
requires = ["setuptools>=42"]
build-backend = "setuptools.build_meta"

[tool.uv.sources]
setuptools = { path = "./packages/setuptools" }
```

When publishing a package, we recommend running `uv build --no-sources` to ensure that the package
builds correctly when `tool.uv.sources` is disabled, as is the case when using other build tools,
like [`pypa/build`](https://github.com/pypa/build).

## Editable dependencies

A regular installation of a directory with a Python package first builds a wheel and then installs
that wheel into your virtual environment, copying all source files. When the package source files
are edited, the virtual environment will contain outdated versions.

Editable installations solve this problem by adding a link to the project within the virtual
environment (a `.pth` file), which instructs the interpreter to include the source files directly.

There are some limitations to editables (mainly: the build backend needs to support them, and native
modules aren't recompiled before import), but they are useful for development, as the virtual
environment will always use the latest changes to the package.

uv uses editable installation for workspace packages by default.

To add an editable dependency, use the `--editable` flag:

```console
$ uv add --editable ./path/foo
```

Or, to opt-out of using an editable dependency in a workspace:

```console
$ uv add --no-editable ./path/foo
```

## PEP 508

[PEP 508](https://peps.python.org/pep-0508/) は依存関係の指定のための構文を定義しています。これは順に：

- 依存関係の名前
- 必要なエクストラ（オプション）
- バージョン指定子
- 環境マーカー（オプション）

バージョン指定子はカンマで区切られ、組み合わされます。例えば、`foo >=1.2.3,<2,!=1.4.0` は「`foo` のバージョンが 1.2.3 以上、2 未満、1.4.0 ではないもの」を意味します。

指定子は必要に応じて末尾にゼロを追加してパディングされるため、`foo ==2` は foo 2.0.0 も一致します。

等号を使用した場合、最後の桁にアスタリスクを使用できます。例えば、`foo ==2.1.*` は 2.1 シリーズの任意のリリースを受け入れます。同様に、`~=` は最後の桁が等しいかそれ以上であることを意味し、例えば `foo ~=1.2` は `foo >=1.2,<2` と等しく、`foo ~=1.2.3` は `foo >=1.2.3,<1.3` と等しいです。

エクストラは名前とバージョンの間に角括弧で囲まれたカンマ区切りで指定されます。例えば、`pandas[excel,plot] ==2.2`。エクストラ名の間の空白は無視されます。

一部の依存関係は特定の環境でのみ必要です。例えば、特定の Python バージョンやオペレーティングシステム。例えば、`importlib.metadata` モジュールのバックポートである `importlib-metadata` をインストールするには、`importlib-metadata >=7.1.0,<8; python_version < '3.10'` を使用します。Windows で `colorama` をインストールするには（他のプラットフォームでは省略）、`colorama >=0.4.6,<5; platform_system == "Windows"` を使用します。

## 編集可能な依存関係

ディレクトリ内の Python パッケージの通常のインストールは、最初にホイールをビルドし、そのホイールを仮想環境にインストールし、すべてのソースファイルをコピーします。パッケージのソースファイルが編集されると、仮想環境には古いバージョンが含まれます。

編集可能なインストールは、この問題を解決するために、仮想環境内にプロジェクトへのリンク（`.pth` ファイル）を追加し、インタープリタにソースファイルを直接含めるように指示します。

編集可能なインストールにはいくつかの制限があります（主に：ビルドバックエンドがそれをサポートする必要があり、ネイティブモジュールはインポート前に再コンパイルされません）が、開発には便利です。仮想環境は常にパッケージの最新の変更を使用します。

uv はデフォルトでワークスペースパッケージに対して編集可能なインストールを使用します。

編集可能な依存関係を追加するには、`--editable` フラグを使用します：

```console
$ uv add --editable ./path/foo
```

または、ワークスペースで編集可能な依存関係の使用をオプトアウトするには：

```console
$ uv add --no-editable ./path/foo
```

マーカーは `and`、`or`、および括弧で組み合わされます。例えば、`aiohttp >=3.7.4,<4; (sys_platform != 'win32' or implementation_name != 'pypy') and python_version >= '3.10'`。マーカー内のバージョンは引用符で囲む必要がありますが、マーカー外のバージョンは引用符で囲んではいけません。
