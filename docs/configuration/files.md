# 設定ファイル

uvは、プロジェクトレベルおよびユーザーレベルの両方で永続的な設定ファイルをサポートしています。

具体的には、uvは現在のディレクトリまたは最も近い親ディレクトリにある `pyproject.toml` または `uv.toml` ファイルを検索します。

!!! note

    ユーザーレベルで操作する `tool` コマンドの場合、ローカル設定ファイルは無視されます。代わりに、uvはユーザーレベルの設定（例：`~/.config/uv/uv.toml`）およびシステムレベルの設定（例：`/etc/uv/uv.toml`）からのみ読み取ります。

ワークスペースでは、uvはワークスペースメンバーに定義された設定を無視して、ワークスペースルートで検索を開始します。ワークスペースは単一のユニットとしてロックされるため、設定はすべてのメンバー間で共有されます。

`pyproject.toml` ファイルが見つかった場合、uvは `[tool.uv]` テーブルから設定を読み取ります。たとえば、永続的なインデックスURLを設定するには、次の内容を `pyproject.toml` に追加します：

```toml title="pyproject.toml"
[tool.uv]
index-url = "https://test.pypi.org/simple"
```

（そのようなテーブルが存在しない場合、`pyproject.toml` ファイルは無視され、uvはディレクトリ階層内の検索を続行します。）

uvは `uv.toml` ファイルも検索し、同一の構造に従いますが、`[tool.uv]` プレフィックスを省略します。たとえば：

```toml title="uv.toml"
index-url = "https://test.pypi.org/simple"
```

!!! note

    `uv.toml` ファイルは `pyproject.toml` ファイルよりも優先されるため、ディレクトリに `uv.toml` と `pyproject.toml` ファイルの両方が存在する場合、設定は `uv.toml` から読み取られ、付随する `pyproject.toml` の `[tool.uv]` セクションは無視されます。

uvはまた、macOSおよびLinuxでは `~/.config/uv/uv.toml`（または `$XDG_CONFIG_HOME/uv/uv.toml`）、Windowsでは `%APPDATA%\uv\uv.toml` でユーザーレベルの設定を発見し、macOSおよびLinuxでは `/etc/uv/uv.toml`（または `$XDG_CONFIG_DIRS/uv/uv.toml`）、Windowsでは `%SYSTEMDRIVE%\ProgramData\uv\uv.toml` でシステムレベルの設定を発見します。

ユーザーレベルおよびシステムレベルの設定は、Python _プロジェクト_ を定義することを意図しているため、`pyproject.toml` 形式ではなく `uv.toml` 形式を使用する必要があります。

プロジェクトレベル、ユーザーレベル、およびシステムレベルの設定ファイルが見つかった場合、設定はマージされ、プロジェクトレベルの設定がユーザーレベルの設定よりも優先され、ユーザーレベルの設定がシステムレベルの設定よりも優先されます。（複数のシステムレベルの設定ファイルが見つかった場合、例：`/etc/uv/uv.toml` と `$XDG_CONFIG_DIRS/uv/uv.toml` の両方に存在する場合、最初に発見されたファイルのみが使用され、XDGが優先されます。）

たとえば、文字列、数値、またはブール値がプロジェクトレベルおよびユーザーレベルの設定テーブルの両方に存在する場合、プロジェクトレベルの値が使用され、ユーザーレベルの値は無視されます。配列が両方のテーブルに存在する場合、配列は連結され、プロジェクトレベルの設定がマージされた配列の前に表示されます。

環境変数を介して提供される設定は永続的な設定よりも優先され、コマンドラインを介して提供される設定はその両方よりも優先されます。

uvは `--no-config` コマンドライン引数を受け入れ、提供された場合、永続的な設定の検出を無効にします。

uvはまた、設定ファイルとして使用する `uv.toml` へのパスを受け入れる `--config-file` コマンドライン引数を受け入れます。提供された場合、このファイルは発見された_すべての_設定ファイル（例：ユーザーレベルの設定は無視されます）の代わりに使用されます。

## 設定

利用可能な設定の列挙については、[設定リファレンス](../reference/settings.md)を参照してください。

## `.env`

`uv run` は、dotenv ファイル（例： `.env` 、 `.env.local` 、 `.env.development` ）から環境変数を読み込むことができます。これは [`dotenvy`](https://github.com/allan2/dotenvy) クレートによって提供されます。

専用の場所から `.env` ファイルを読み込むには、 `UV_ENV_FILE` 環境変数を設定するか、 `--env-file` フラグを `uv run` に渡します。

たとえば、現在の作業ディレクトリにある `.env` ファイルから環境変数を読み込むには、次のようにします：

```console
$ uv run --env-file .env -- echo $MY_ENV_VAR
```

`--env-file` フラグは複数回提供することができ、後続のファイルが前のファイルで定義された値を上書きします。 `UV_ENV_FILE` 環境変数を介して複数のファイルを提供するには、パスをスペースで区切ります（例： `UV_ENV_FILE="/path/to/file1 /path/to/file2"` ）。

dotenv の読み込みを無効にするには（例： `UV_ENV_FILE` または `--env-file` コマンドライン引数を上書きするため）、 `UV_NO_ENV_FILE` 環境変数を `1` に設定するか、 `--no-env-file` フラグを `uv run` に渡します。

同じ変数が環境と `.env` ファイルの両方に定義されている場合、環境からの値が優先されます。

## pipインターフェースの設定

`uv pip` コマンドラインインターフェースを設定するための専用の [`[tool.uv.pip]`](../reference/settings.md#pip) セクションが提供されています。このセクションの設定は `uv` 名前空間外のコマンドには適用されません。ただし、このセクションの設定の多くには、`uv.pip` セクションの値によってオーバーライドされない限り、`uv pip` インターフェースにも適用されるトップレベルの名前空間に対応する設定があります。

`uv.pip` 設定はpipのインターフェースに密接に従うように設計されており、グローバル設定が代替の設計（例：`--no-build`）を使用できるようにするために別々に宣言されています。

たとえば、次の `pyproject.toml` に `index-url` を `[tool.uv.pip]` の下に設定すると、`uv pip` サブコマンド（例：`uv pip install`）にのみ影響し、`uv sync`、`uv lock`、または `uv run` には影響しません：

```toml title="pyproject.toml"
[tool.uv.pip]
index-url = "https://test.pypi.org/simple"
```
