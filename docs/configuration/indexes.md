# パッケージインデックス

デフォルトでは、uvは依存関係の解決とパッケージのインストールに[Pythonパッケージインデックス（PyPI）](https://pypi.org)を使用します。ただし、uvは`[[tool.uv.index]]`設定オプション（およびコマンドラインオプションの`--index`）を介して、他のパッケージインデックス（プライベートインデックスを含む）を使用するように構成できます。

## インデックスの定義

依存関係を解決する際に追加のインデックスを含めるには、`pyproject.toml`に`[[tool.uv.index]]`エントリを追加します：

```toml
[[tool.uv.index]]
# インデックスのオプションの名前。
name = "pytorch"
# インデックスの必須URL。
url = "https://download.pytorch.org/whl/cpu"
```

インデックスは定義された順序で優先され、構成ファイルに最初にリストされたインデックスが依存関係を解決する際に最初に参照され、コマンドラインで提供されたインデックスが構成ファイル内のインデックスよりも優先されます。

デフォルトでは、uvは「デフォルト」インデックスとしてPythonパッケージインデックス（PyPI）を含みます。これは、パッケージが他のインデックスのいずれにも見つからない場合に使用されるインデックスです。PyPIをインデックスのリストから除外するには、別のインデックスエントリに`default = true`を設定します（またはコマンドラインオプションの`--default-index`を使用します）：

```toml
[[tool.uv.index]]
name = "pytorch"
url = "https://download.pytorch.org/whl/cpu"
default = true
```

デフォルトのインデックスは常に最も低い優先度として扱われ、その位置に関係なくインデックスのリストの最後に表示されます。

インデックス名には、英数字、ダッシュ、アンダースコア、およびピリオドのみを含めることができ、有効なASCIIである必要があります。

## パッケージをインデックスに固定する

パッケージを特定のインデックスに固定するには、そのインデックスを`tool.uv.sources`エントリに指定します。たとえば、`torch`が常に`pytorch`インデックスからインストールされるようにするには、次の内容を`pyproject.toml`に追加します：

```toml
[tool.uv.sources]
torch = { index = "pytorch" }

[[tool.uv.index]]
name = "pytorch"
url = "https://download.pytorch.org/whl/cpu"
```

同様に、プラットフォームに基づいて異なるインデックスから取得するには、環境マーカーで区別されたソースのリストを提供できます：

```toml title="pyproject.toml"
[project]
dependencies = ["torch"]

[tool.uv.sources]
torch = [
  { index = "pytorch-cu118", marker = "sys_platform == 'darwin'"},
  { index = "pytorch-cu124", marker = "sys_platform != 'darwin'"},
]

[[tool.uv.index]]
name = "pytorch-cu118"
url = "https://download.pytorch.org/whl/cu118"

[[tool.uv.index]]
name = "pytorch-cu124"
url = "https://download.pytorch.org/whl/cu124"
```

インデックスを`explicit = true`としてマークすると、そのインデックスに明示的に固定されていない限り、パッケージがそのインデックスからインストールされるのを防ぐことができます。たとえば、`torch`が`pytorch`インデックスからインストールされるが、他のすべてのパッケージがPyPIからインストールされるようにするには、次の内容を`pyproject.toml`に追加します：

```toml
[tool.uv.sources]
torch = { index = "pytorch" }

[[tool.uv.index]]
name = "pytorch"
url = "https://download.pytorch.org/whl/cpu"
explicit = true
```

`tool.uv.sources`を介して参照される名前付きインデックスは、プロジェクトの`pyproject.toml`ファイル内で定義されている必要があります。コマンドライン、環境変数、またはユーザーレベルの構成で提供されたインデックスは認識されません。

## 複数のインデックスを検索する

デフォルトでは、uvは指定されたパッケージが利用可能な最初のインデックスで停止し、その最初のインデックスに存在する解決に制限します（`first-match`）。

たとえば、`[[tool.uv.index]]`を介して内部インデックスが指定されている場合、uvの動作はその内部インデックスにパッケージが存在する場合、常にその内部インデックスからインストールされ、PyPIからはインストールされないようになります。これは、「依存関係の混乱」攻撃を防ぐことを目的としています。攻撃者が内部パッケージと同じ名前の悪意のあるパッケージをPyPIに公開し、その結果、悪意のあるパッケージが内部パッケージの代わりにインストールされることを防ぎます。たとえば、2022年12月の[torchtriton攻撃](https://pytorch.org/blog/compromised-nightly-dependency/)を参照してください。

ユーザーは、`--index-strategy`コマンドラインオプションまたは`UV_INDEX_STRATEGY`環境変数を介して代替のインデックス動作にオプトインできます。これらのオプションは次の値をサポートします：

- `first-match`（デフォルト）：すべてのインデックスで各パッケージを検索し、パッケージを含む最初のインデックスに存在する候補バージョンに制限します。
- `unsafe-first-match`：すべてのインデックスで各パッケージを検索しますが、他のインデックスで新しいバージョンが利用可能であっても、互換性のあるバージョンを含む最初のインデックスを優先します。
- `unsafe-best-match`：すべてのインデックスで各パッケージを検索し、候補バージョンの組み合わせから最適なバージョンを選択します。

`unsafe-best-match`はpipの動作に最も近いですが、「依存関係の混乱」攻撃のリスクにさらされます。

## 資格情報の提供

ほとんどのプライベートレジストリは、パッケージにアクセスするために認証を必要とし、通常はユーザー名とパスワード（またはアクセストークン）を使用します。

プライベートインデックスで認証するには、環境変数を介して資格情報を提供するか、URLに埋め込みます。

たとえば、ユーザー名（`public`）とパスワード（`koala`）を必要とする`internal-proxy`という名前のインデックスがある場合、資格情報なしでインデックスを`pyproject.toml`に定義します：

```toml
[[tool.uv.index]]
name = "internal-proxy"
url = "https://example.com/simple"
```

そこから、環境変数 `UV_INDEX_INTERNAL_PROXY_USERNAME` と `UV_INDEX_INTERNAL_PROXY_PASSWORD` を設定できます。ここで、`INTERNAL_PROXY` はインデックス名の大文字バージョンであり、非英数字はアンダースコアに置き換えられます：

```sh
export UV_INDEX_INTERNAL_PROXY_USERNAME=public
export UV_INDEX_INTERNAL_PROXY_PASSWORD=koala
```

環境変数を介して資格情報を提供することで、プレーンテキストの`pyproject.toml`ファイルに機密情報を保存するのを避けることができます。

または、資格情報をインデックス定義に直接埋め込むこともできます：

```toml
[[tool.uv.index]]
name = "internal"
url = "https://public:koala@pypi-proxy.corp.dev/simple"
```

セキュリティ上の理由から、資格情報は`uv.lock`ファイルに保存されることはなく、インストール時に認証されたURLにアクセスできる必要があります。

## `--index-url`および`--extra-index-url`

`[[tool.uv.index]]`設定オプションに加えて、uvは互換性のためにpipスタイルの`--index-url`および`--extra-index-url`コマンドラインオプションをサポートしています。`--index-url`はデフォルトのインデックスを定義し、`--extra-index-url`は追加のインデックスを定義します。

これらのオプションは`[[tool.uv.index]]`設定オプションと組み合わせて使用することができ、同じ優先順位ルールに従います：

- デフォルトのインデックスは常に最も低い優先度として扱われ、レガシーの`--index-url`引数、推奨される`--default-index`引数、または`default = true`が設定された`[[tool.uv.index]]`エントリのいずれかで定義されます。
- インデックスは定義された順序で参照され、レガシーの`--extra-index-url`引数、推奨される`--index`引数、または`[[tool.uv.index]]`エントリのいずれかで定義されます。

実際には、`--index-url`および`--extra-index-url`は名前のない`[[tool.uv.index]]`エントリと見なすことができ、前者には`default = true`が有効になっています。この文脈では、`--index-url`は`--default-index`に対応し、`--extra-index-url`は`--index`に対応します。
