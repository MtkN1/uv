# uv

非常に高速な Python パッケージおよびプロジェクトマネージャー、Rust で書かれています。

<p align="center">
  <img alt="Shows a bar chart with benchmark results." src="https://github.com/astral-sh/uv/assets/1309177/629e59c0-9c6e-4013-9ad4-adb2bcf5080d#only-light">
</p>

<p align="center">
  <img alt="Shows a bar chart with benchmark results." src="https://github.com/astral-sh/uv/assets/1309177/03aa9163-1c79-4a87-a31d-7a9311ed9310#only-dark">
</p>

<p align="center">
  <i>ウォームキャッシュで<a href="https://trio.readthedocs.io/">Trio</a>の依存関係をインストールしています。</i>
</p>

## ハイライト

- 🚀 `pip` 、 `pip-tools` 、 `pipx` 、 `poetry` 、 `pyenv` 、 `virtualenv` などを置き換える単一のツール。
- ⚡️ `pip` よりも[10-100 倍高速](https://github.com/astral-sh/uv/blob/main/BENCHMARKS.md)。
- 🐍 Python バージョンを[インストールおよび管理](#python-management)。
- 🛠️ Python アプリケーションを[実行およびインストール](#tool-management)。
- ❇️ [インライン依存関係メタデータ](./guides/scripts.md#declaring-script-dependencies)をサポートしてスクリプトを[実行](#script-support)。
- 🗂️ [ユニバーサルロックファイル](./concepts/projects.md#project-lockfile)を使用して[包括的なプロジェクト管理](#project-management)を提供。
- 🔩 [pip 互換インターフェース](#the-pip-interface)を含み、馴染みのある CLI でパフォーマンスを向上。
- 🏢 スケーラブルなプロジェクトのための Cargo スタイルの[ワークスペース](./concepts/workspaces.md)をサポート。
- 💾 依存関係の重複排除のための[グローバルキャッシュ](./concepts/cache.md)でディスクスペース効率が高い。
- ⏬ `curl` または `pip` を介して Rust や Python なしでインストール可能。
- 🖥️ macOS、Linux、Windows をサポート。

uv は [Ruff](https://github.com/astral-sh/ruff) の作成者である [Astral](https://astral.sh) によってサポートされています。

## はじめに

公式のスタンドアロンインストーラーを使用して uv をインストールします：

=== "macOS と Linux"

    ```console
    $ curl -LsSf https://astral.sh/uv/install.sh | sh
    ```

=== "Windows"

    ```console
    $ powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

次に、[最初のステップ](./getting-started/first-steps.md)を確認するか、以下の概要を読み進めてください。

!!! tip

    uv は `pip` 、 Homebrew などでもインストールできます。すべての方法は[インストールページ](./getting-started/installation.md)で確認できます。

## プロジェクト管理 {#project-management}

uv は、`rye` や `poetry` のように、ロックファイル、ワークスペースなどをサポートしてプロジェクトの依存関係と環境を管理します：

```console
$ uv init example
プロジェクト `example` を `/home/user/example` に初期化しました

$ cd example

$ uv add ruff
仮想環境を作成中：.venv
170ms で 2 つのパッケージを解決しました
   example @ file:///home/user/example をビルドしました
627ms で 2 つのパッケージを準備しました
1ms で 2 つのパッケージをインストールしました
 + example==0.1.0 (file:///home/user/example から)
 + ruff==0.5.4

$ uv run ruff check
すべてのチェックが合格しました！
```

[プロジェクトガイド](./guides/projects.md)を参照して始めてください。

## ツール管理 {#tool-management}

uv は、Python パッケージによって提供されるコマンドラインツールを実行およびインストールします。これは `pipx` に似ています。

一時的な環境でツールを実行するには、 `uvx` （ `uv tool run` のエイリアス）を使用します：

```console
$ uvx pycowsay 'hello world!'
167ms で 1 つのパッケージを解決しました
9ms で 1 つのパッケージをインストールしました
 + pycowsay==0.0.0.2
  """

  ------------
< hello world! >
  ------------
   \   ^__^
    \  (oo)\_______
       (__)\       )\/\
           ||----w |
           ||     ||
```

`uv tool install` を使用してツールをインストールします：

```console
$ uv tool install ruff
6ms で 1 つのパッケージを解決しました
2ms で 1 つのパッケージをインストールしました
 + ruff==0.5.4
1 つの実行可能ファイルをインストールしました：ruff

$ ruff --version
ruff 0.5.4
```

[ツールガイド](./guides/tools.md)を参照して始めてください。

## Python 管理 {#python-management}

uv は Python をインストールし、バージョン間の迅速な切り替えを可能にします。

複数の Python バージョンをインストールします：

```console
$ uv python install 3.10 3.11 3.12
Python バージョンに一致するものを検索中：Python 3.10
Python バージョンに一致するものを検索中：Python 3.11
Python バージョンに一致するものを検索中：Python 3.12
3.42 秒で 3 つのバージョンをインストールしました
 + cpython-3.10.14-macos-aarch64-none
 + cpython-3.11.9-macos-aarch64-none
 + cpython-3.12.4-macos-aarch64-none
```

必要に応じて Python バージョンをダウンロードします：

```console
$ uv venv --python 3.12.0
CPython 3.12.0 を使用中
仮想環境を作成中：.venv
有効化するには：source .venv/bin/activate

$ uv run --python pypy@3.8 -- python
Python 3.8.16 (a9dbdca6fc3286b0addd2240f11d97d8e8de187a, Dec 29 2022, 11:45:30)
[PyPy 7.3.11 with GCC Apple LLVM 13.1.6 (clang-1316.0.21.2.5)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>>
```

現在のディレクトリで特定の Python バージョンを使用します：

```console
$ uv python pin pypy@3.11
`.python-version` を `pypy@3.11` に固定しました
```

[Python のインストールガイド](./guides/install-python.md)を参照して始めてください。

### スクリプトサポート {#script-support}

uv は、単一ファイルのスクリプトの依存関係と環境を管理します。

新しいスクリプトを作成し、その依存関係を宣言するインラインメタデータを追加します：

```console
$ echo 'import requests; print(requests.get("https://astral.sh"))' > example.py

$ uv add --script example.py requests
`example.py` を更新しました
```

次に、スクリプトを分離された仮想環境で実行します：

```console
$ uv run example.py
インラインスクリプトメタデータを読み取り中：example.py
12ms で 5 つのパッケージをインストールしました
<Response [200]>
```

[スクリプトガイド](./guides/scripts.md)を参照して始めてください。

## pip インターフェース {#the-pip-interface}

uv は、一般的な `pip` 、 `pip-tools` および `virtualenv` コマンドのドロップイン置き換えを提供します。

uv は、依存関係のバージョンオーバーライド、プラットフォーム非依存の解決、再現可能な解決、代替解決戦略などの高度な機能を備えたインターフェースを拡張します。

既存のワークフローを変更せずに uv に移行し、 `uv pip` インターフェースで 10-100 倍の速度向上を体験してください。

プラットフォーム非依存の要件ファイルに要件をコンパイルします：

```console
$ uv pip compile docs/requirements.in \
   --universal \
   --output-file docs/requirements.txt
12ms で 43 のパッケージを解決しました
```

仮想環境を作成します：

```console
$ uv venv
CPython 3.12.3 を使用中
仮想環境を作成中：.venv
有効化するには：source .venv/bin/activate
```

ロックされた要件をインストールします：

```console
$ uv pip sync docs/requirements.txt
11ms で 43 のパッケージを解決しました
208ms で 43 のパッケージをインストールしました
 + babel==2.15.0
 + black==24.4.2
 + certifi==2024.7.4
 ...
```

[pip インターフェースのドキュメント](./pip/index.md)を参照して始めてください。

## 詳細を学ぶ

[最初のステップ](./getting-started/first-steps.md)を確認するか、[ガイド](./guides/index.md)に直接ジャンプして uv の使用を開始してください。
