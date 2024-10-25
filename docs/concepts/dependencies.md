# 依存関係の指定

uvでは、プロジェクトの依存関係は `pyproject.toml` テーブルの `project.dependencies` と `tool.uv.sources` に分けて宣言されます。

`project.dependencies` は、PyPI にアップロードする際やホイールをビルドする際に伝播される標準準拠の依存関係メタデータを定義します。

`tool.uv.sources` は、開発中に組み込まれる追加のソースで依存関係メタデータを強化します。依存関係のソースは、Git リポジトリ、URL、ローカルパス、または代替レジストリである可能性があります。

`tool.uv.sources` は、uv が `project.dependencies` 標準ではサポートされていない編集可能なインストールや相対パスなどの一般的なパターンをサポートできるようにします。例えば：

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

`project.dependencies` テーブルは、PyPI にアップロードする際やホイールをビルドする際に使用される依存関係を表します。個々の依存関係は、[依存関係指定子](https://packaging.python.org/en/latest/specifications/dependency-specifiers/) の構文を使用して指定され、このテーブルは [PEP 621](https://packaging.python.org/en/latest/specifications/pyproject-toml/) 標準に従います。

`project.dependencies` は、プロジェクトに必要なパッケージのリストと、それらをインストールする際に使用するバージョン制約を定義します。各エントリには依存関係の名前とバージョンが含まれます。エントリには、プラットフォーム固有のパッケージのためのエクストラや環境マーカーが含まれる場合があります。例えば：

```toml title="pyproject.toml"
[project]
name = "albatross"
version = "0.1.0"
dependencies = [
  # この範囲内の任意のバージョン
  "tqdm >=4.66.2,<5",
  # torch のこのバージョン
  "torch ==2.2.2",
  # torch エクストラ付きの transformers をインストール
  "transformers[torch] >=4.39.3,<5",
  # 古い python バージョンでのみこのパッケージをインストール
  # 詳細は「環境マーカー」を参照
  "importlib_metadata >=7.1.0,<8; python_version < '3.10'",
  "mollymawk ==0.1.0"
]
```

プロジェクトが標準パッケージインデックスからのパッケージのみを必要とする場合、`project.dependencies` だけで十分です。プロジェクトが Git、リモート URL、またはローカルソースからのパッケージに依存している場合、`tool.uv.sources` を使用して標準準拠の `project.dependencies` テーブルから逸脱することなく依存関係メタデータを強化できます。

!!! tip

    CLI から `pyproject.toml` の依存関係を追加、削除、または更新する方法については、[プロジェクト](./projects.md#managing-dependencies) のドキュメントを参照してください。

## 依存関係のソース

開発中、プロジェクトは PyPI で利用できないパッケージに依存することがあります。uv がサポートする追加のソースは次のとおりです：

- インデックス：特定のパッケージインデックスから解決されたパッケージ。
- Git：Git リポジトリ。
- URL：リモートホイールまたはソースディストリビューション。
- パス：ローカルホイール、ソースディストリビューション、またはプロジェクトディレクトリ。
- ワークスペース：現在のワークスペースのメンバー。

非 uv プロジェクトが Git またはパス依存関係としてソースを持つプロジェクトを使用する場合、`project.dependencies` および `project.optional-dependencies` のみが尊重されます。ソーステーブルに提供された情報は、他のパッケージマネージャーに特有の形式で再指定する必要があります。

uv に `tool.uv.sources` テーブルを無視するよう指示するには（例：パッケージの公開されたメタデータで解決をシミュレートするため）、`--no-sources` フラグを使用します：

```console
$ uv lock --no-sources
```

`--no-sources` の使用は、特定の依存関係を満たすために発見される可能性のある[ワークスペースメンバー](#workspace-member) を uv が無視することも防ぎます。

### インデックス

Python パッケージを特定のインデックスに固定するには、`pyproject.toml` に名前付きインデックスを追加します：

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

`explicit` フラグはオプションであり、インデックスが `tool.uv.sources` で明示的に指定されているパッケージに対してのみ使用されるべきであることを示します。`explicit` が設定されていない場合、他のパッケージは他の場所で見つからない場合にインデックスから解決されることがあります。

### Git

Git 依存関係ソースを追加するには、クローンする Git 互換 URL に `git+` をプレフィックスとして付けます。

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

リビジョン（例：コミット）、タグ、またはブランチも含めることができます：

```console
$ uv add git+https://github.com/encode/httpx --tag 0.27.0
$ uv add git+https://github.com/encode/httpx --branch main
$ uv add git+https://github.com/encode/httpx --rev 326b9431c761e1ef1e00b9f760d1f654c8db48c6
```

Git 依存関係は、`{ git = <url> }` 構文を使用して `pyproject.toml` に手動で追加または編集することもできます。ターゲットリビジョンは、`rev`（例：コミット）、`tag`、または `branch` のいずれかで指定できます。

=== "tag"

    ```toml title="pyproject.toml"
    [project]
    dependencies = [
        "httpx",
    ]

    [tool.uv.sources]
    httpx = { git = "https://github.com/encode/httpx", tag = "0.27.0" }
    ```

=== "branch"

    ```toml title="pyproject.toml"
    [project]
    dependencies = [
        "httpx",
    ]

    [tool.uv.sources]
    httpx = { git = "https://github.com/encode/httpx", branch = "main" }
    ```

=== "rev"

    ```toml title="pyproject.toml"
    [project]
    dependencies = [
        "httpx",
    ]

    [tool.uv.sources]
    httpx = { git = "https://github.com/encode/httpx", rev = "326b9431c761e1ef1e00b9f760d1f654c8db48c6" }
    ```

パッケージがリポジトリルートにない場合、`subdirectory` を指定できます。

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

URL 依存関係は、`{ url = <url> }` 構文を使用して `pyproject.toml` に手動で追加または編集することもできます。ソースディストリビューションがアーカイブルートにない場合、`subdirectory` を指定できます。

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

    ディレクトリの通常のインストールはデフォルトでは[編集可能なインストール](#editable-dependencies)として使用されません。プロジェクトディレクトリの編集可能なインストールを要求するには：

    ```console
    $ uv add --editable ~/projects/bar/
    ```

    同じリポジトリ内の複数のパッケージの場合、[_ワークスペース_](./workspaces.md) がより適している場合があります。

### ワークスペースメンバー

ワークスペースメンバーへの依存関係を宣言するには、メンバー名を `{ workspace = true }` として追加します。すべてのワークスペースメンバーは明示的に記載する必要があります。ワークスペースメンバーは常に[編集可能](#editable-dependencies)です。ワークスペースの詳細については、[ワークスペース](./workspaces.md) のドキュメントを参照してください。

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

ソースを特定のプラットフォームや Python バージョンに限定するには、ソースに[依存関係指定子](https://packaging.python.org/en/latest/specifications/dependency-specifiers/)互換の環境マーカーを提供します。

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

単一の依存関係に対して複数のソースを指定するには、[PEP 508](https://peps.python.org/pep-0508/#environment-markers)互換の環境マーカーで区別されたソースのリストを提供します。

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

この戦略は、環境マーカーに基づいて異なるインデックスからパッケージを取得することにも拡張されます。例えば、プラットフォームに基づいて異なる PyTorch インデックスから `torch` を取得するには：

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

ライブラリとして公開されるプロジェクトでは、デフォルトの依存関係ツリーを減らすために一部の機能をオプションにすることが一般的です。例えば、Pandas には、Excel パーサーや `matplotlib` を明示的に必要としない限りインストールしないようにするための [`excel` エクストラ](https://pandas.pydata.org/docs/getting_started/install.html#excel-files) や [`plot` エクストラ](https://pandas.pydata.org/docs/getting_started/install.html#visualization) があります。エクストラは `package[<extra>]` 構文を使用して要求されます。例：`pandas[plot, excel]`。

オプションの依存関係は `[project.optional-dependencies]` に指定され、TOML テーブルはエクストラ名からその依存関係へのマッピングを行い、[依存関係指定子](#dependency-specifiers-pep-508) の構文に従います。

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

オプションの依存関係とは異なり、開発依存関係はローカルのみであり、プロジェクトが PyPI や他のインデックスに公開される際にはプロジェクトの要件に含まれません。そのため、開発依存関係は `[project]` ではなく `[tool.uv]` に含まれます。

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

## ビルド依存関係

プロジェクトが[Python パッケージ](./projects.md#build-systems)として構成されている場合、プロジェクトをビルドするために必要な依存関係を宣言することができますが、実行するためには必要ありません。これらの依存関係は、[PEP 518](https://peps.python.org/pep-0518/) に従って `build-system.requires` の下の `[build-system]` テーブルに指定されます。

例えば、プロジェクトがビルドバックエンドとして `setuptools` を使用する場合、`setuptools` をビルド依存関係として宣言する必要があります：

```toml title="pyproject.toml"
[project]
name = "pandas"
version = "0.1.0"

[build-system]
requires = ["setuptools>=42"]
build-backend = "setuptools.build_meta"
```

デフォルトでは、uv はビルド依存関係を解決する際に `tool.uv.sources` を尊重します。例えば、ローカルバージョンの `setuptools` を使用してビルドするには、ソースを `tool.uv.sources` に追加します：

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

パッケージを公開する際には、`tool.uv.sources` が無効になっている場合（他のビルドツール、例えば [`pypa/build`](https://github.com/pypa/build) を使用する場合と同様に）、パッケージが正しくビルドされることを確認するために `uv build --no-sources` を実行することをお勧めします。

## 編集可能な依存関係

ディレクトリにある Python パッケージの通常のインストールは、最初にホイールをビルドし、そのホイールを仮想環境にインストールし、すべてのソースファイルをコピーします。パッケージのソースファイルが編集されると、仮想環境には古いバージョンが含まれます。

編集可能なインストールは、この問題を解決するために、仮想環境内にプロジェクトへのリンク（`.pth` ファイル）を追加し、インタープリタにソースファイルを直接含めるように指示します。

編集可能なインストールにはいくつかの制限があります（主に：ビルドバックエンドがそれをサポートする必要があり、ネイティブモジュールはインポート前に再コンパイルされません）が、開発には便利です。仮想環境は常にパッケージの最新の変更を使用します。

uv はデフォルトでワークスペースパッケージに対して編集可能なインストールを使用します。

編集可能な依存関係を追加するには、`--editable` フラグを使用します：

```console
$ uv add --editable ./path/foo
```

または、ワークスペースで編集可能な依存関係をオプトアウトするには：

```console
$ uv add --no-editable ./path/foo
```

## 依存関係指定子（PEP 508）

uv は、以前は [PEP 508](https://peps.python.org/pep-0508/) として知られていた[依存関係指定子](https://packaging.python.org/en/latest/specifications/dependency-specifiers/)を使用します。依存関係指定子は、次の順序で構成されます：

- 依存関係の名前
- 必要なエクストラ（オプション）
- バージョン指定子
- 環境マーカー（オプション）

バージョン指定子はカンマで区切られ、追加されます。例：`foo >=1.2.3,<2,!=1.4.0` は「バージョン 1.2.3 以上、2 未満、および 1.4.0 ではない `foo` のバージョン」と解釈されます。

指定子は必要に応じて末尾にゼロを追加してパディングされるため、`foo ==2` は `foo 2.0.0` も一致します。

等号を使用する場合、最後の桁にアスタリスクを使用できます。例：`foo ==2.1.*` は 2.1 シリーズの任意のリリースを受け入れます。同様に、`~=` は最後の桁が等しいか高い場合に一致します。例：`foo ~=1.2` は `foo >=1.2,<2` と等しく、`foo ~=1.2.3` は `foo >=1.2.3,<1.3` と等しいです。

エクストラは名前とバージョンの間に角括弧でカンマ区切りで指定されます。例：`pandas[excel,plot] ==2.2`。エクストラ名の間の空白は無視されます。

一部の依存関係は特定の環境でのみ必要です。例：特定の Python バージョンやオペレーティングシステム。例えば、`importlib.metadata` モジュールのバックポートである `importlib-metadata` をインストールするには、`importlib-metadata >=7.1.0,<8; python_version < '3.10'` を使用します。Windows で `colorama` をインストールし（他のプラットフォームでは省略）、`colorama >=0.4.6,<5; platform_system == "Windows"` を使用します。

マーカーは `and`、`or`、および括弧で組み合わせられます。例：`aiohttp >=3.7.4,<4; (sys_platform != 'win32' or implementation_name != 'pypy') and python_version >= '3.10'`。マーカー内のバージョンは引用符で囲む必要がありますが、マーカーの外側のバージョンは引用符で囲む必要はありません。
