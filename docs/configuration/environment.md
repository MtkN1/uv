# 環境変数

uvは次のコマンドライン引数を環境変数として受け入れます：

- `UV_INDEX`: `--index`コマンドライン引数と同等です。設定されている場合、uvはパッケージを検索する際にこのスペースで区切られたURLのリストを追加のインデックスとして使用します。
- `UV_DEFAULT_INDEX`: `--default-index`コマンドライン引数と同等です。設定されている場合、uvはパッケージを検索する際にこのURLをデフォルトのインデックスとして使用します。
- `UV_INDEX_URL`: `--index-url`コマンドライン引数と同等です。設定されている場合、uvはパッケージを検索する際にこのURLをデフォルトのインデックスとして使用します。（非推奨：代わりに`UV_DEFAULT_INDEX`を使用してください。）
- `UV_EXTRA_INDEX_URL`: `--extra-index-url`コマンドライン引数と同等です。設定されている場合、uvはパッケージを検索する際にこのスペースで区切られたURLのリストを追加のインデックスとして使用します。（非推奨：代わりに`UV_INDEX`を使用してください。）
- `UV_FIND_LINKS`: `--find-links`コマンドライン引数と同等です。設定されている場合、uvはパッケージを検索するための追加の場所としてこのカンマで区切られたリストを使用します。
- `UV_CACHE_DIR`: `--cache-dir`コマンドライン引数と同等です。設定されている場合、uvはデフォルトのキャッシュディレクトリの代わりにこのディレクトリをキャッシュに使用します。
- `UV_NO_CACHE`: `--no-cache`コマンドライン引数と同等です。設定されている場合、uvはすべての操作でキャッシュを使用しません。
- `UV_RESOLUTION`: `--resolution`コマンドライン引数と同等です。例えば、`lowest-direct`に設定されている場合、uvはすべての直接依存関係の最も互換性のあるバージョンをインストールします。
- `UV_PRERELEASE`: `--prerelease`コマンドライン引数と同等です。例えば、`allow`に設定されている場合、uvはすべての依存関係のプレリリースバージョンを許可します。
- `UV_SYSTEM_PYTHON`: `--system`コマンドライン引数と同等です。`true`に設定されている場合、uvはシステムの`PATH`で最初に見つかったPythonインタープリタを使用します。警告：`UV_SYSTEM_PYTHON=true`は継続的インテグレーション（CI）またはコンテナ化された環境での使用を意図しており、システムPythonを変更すると予期しない動作が発生する可能性があるため、注意して使用してください。
- `UV_PYTHON`: `--python`コマンドライン引数と同等です。パスに設定されている場合、uvはすべての操作でこのPythonインタープリタを使用します。
- `UV_BREAK_SYSTEM_PACKAGES`: `--break-system-packages`コマンドライン引数と同等です。`true`に設定されている場合、uvはシステムにインストールされたパッケージと競合するパッケージのインストールを許可します。警告：`UV_BREAK_SYSTEM_PACKAGES=true`は継続的インテグレーション（CI）またはコンテナ化された環境での使用を意図しており、システムPythonを変更すると予期しない動作が発生する可能性があるため、注意して使用してください。
- `UV_NATIVE_TLS`: `--native-tls`コマンドライン引数と同等です。`true`に設定されている場合、uvはバンドルされた`webpki-roots`クレートの代わりにシステムの信頼ストアを使用します。
- `UV_INDEX_STRATEGY`: `--index-strategy`コマンドライン引数と同等です。例えば、`unsafe-any-match`に設定されている場合、uvはすべてのインデックスURLで利用可能な特定のパッケージのバージョンを考慮し、最初のインデックスURLに限定せずに検索します。
- `UV_REQUIRE_HASHES`: `--require-hashes`コマンドライン引数と同等です。`true`に設定されている場合、uvはすべての依存関係に要件ファイルにハッシュが指定されていることを要求します。
- `UV_CONSTRAINT`: `--constraint`コマンドライン引数と同等です。設定されている場合、uvはこのファイルを制約ファイルとして使用します。スペースで区切られたファイルのリストを使用します。
- `UV_BUILD_CONSTRAINT`: `--build-constraint`コマンドライン引数と同等です。設定されている場合、uvはソースディストリビューションのビルドに対する制約としてこのファイルを使用します。スペースで区切られたファイルのリストを使用します。
- `UV_OVERRIDE`: `--override`コマンドライン引数と同等です。設定されている場合、uvはこのファイルをオーバーライドファイルとして使用します。スペースで区切られたファイルのリストを使用します。
- `UV_LINK_MODE`: `--link-mode`コマンドライン引数と同等です。設定されている場合、uvはこれをリンクモードとして使用します。
- `UV_NO_BUILD_ISOLATION`: `--no-build-isolation`コマンドライン引数と同等です。設定されている場合、uvはソースディストリビューションのビルド時に分離をスキップします。
- `UV_CUSTOM_COMPILE_COMMAND`: `--custom-compile-command`コマンドライン引数と同等です。`uv pip compile`によって生成された`requirements.txt`ファイルの出力ヘッダーでuvをオーバーライドするために使用されます。ラッパースクリプト内から`uv pip compile`が呼び出されるユースケースを意図しており、出力ファイルにラッパースクリプトの名前を含めます。
- `UV_KEYRING_PROVIDER`: `--keyring-provider`コマンドライン引数と同等です。設定されている場合、uvはこの値をキーチェーンプロバイダーとして使用します。
- `UV_CONFIG_FILE`: `--config-file`コマンドライン引数と同等です。ローカルの`uv.toml`ファイルへのパスを期待します。
- `UV_NO_CONFIG`: `--no-config`コマンドライン引数と同等です。設定されている場合、uvは現在のディレクトリ、親ディレクトリ、またはユーザー設定ディレクトリから設定ファイルを読み取りません。
- `UV_EXCLUDE_NEWER`: `--exclude-newer`コマンドライン引数と同等です。設定されている場合、uvは指定された日付以降に公開されたディストリビューションを除外します。
- `UV_PYTHON_PREFERENCE`: `--python-preference`コマンドライン引数と同等です。uvがシステムまたは管理されたPythonバージョンを優先するかどうかを指定します。
- `UV_PYTHON_DOWNLOADS`: [`python-downloads`](../reference/settings.md#python-downloads)設定および無効化された場合の`--no-python-downloads`オプションと同等です。uvがPythonのダウンロードを許可するかどうかを指定します。
- `UV_COMPILE_BYTECODE`: `--compile-bytecode`コマンドライン引数と同等です。設定されている場合、uvはインストール後にPythonソースファイルをバイトコードにコンパイルします。
- `UV_PUBLISH_URL`: `--publish-url`コマンドライン引数と同等です。`uv publish`で使用するインデックスのアップロードエンドポイントのURLです。
- `UV_PUBLISH_TOKEN`: `uv publish`の`--token`コマンドライン引数と同等です。設定されている場合、uvはこのトークンを（ユーザー名`__token__`と共に）公開に使用します。
- `UV_PUBLISH_USERNAME`: `uv publish`の`--username`コマンドライン引数と同等です。設定されている場合、uvは公開にこのユーザー名を使用します。
- `UV_PUBLISH_PASSWORD`: `uv publish`の`--password`コマンドライン引数と同等です。設定されている場合、uvは公開にこのパスワードを使用します。
- `UV_NO_SYNC`: `--no-sync`コマンドライン引数と同等です。設定されている場合、uvは環境の更新をスキップします。
- `UV_LOCKED`: `--locked`コマンドライン引数と同等です。設定されている場合、uvは`uv.lock`が変更されていないことを確認します。
- `UV_FROZEN`: `--frozen`コマンドライン引数と同等です。設定されている場合、uvは`uv.lock`ファイルを更新せずに実行します。

いずれの場合も、対応するコマンドライン引数が環境変数よりも優先されます。

さらに、uvは次の環境変数を尊重します：

- `UV_CONCURRENT_DOWNLOADS`: Sets the maximum number of in-flight concurrent downloads that uv will
  perform at any given time.
- `UV_CONCURRENT_BUILDS`: Sets the maximum number of source distributions that uv will build
  concurrently at any given time.
- `UV_CONCURRENT_INSTALLS`: Used to control the number of threads used when installing and unzipping
  packages.
- `UV_TOOL_DIR`: Used to specify the directory where uv will store managed tools.
- `UV_TOOL_BIN_DIR`: Used to specify the "bin" directory where uv will install tool executables.
- `UV_PROJECT_ENVIRONMENT`: Use to specify the path to the directory to use for a project virtual
  environment. See the
  [project documentation](../concepts/projects.md#configuring-the-project-environment-path) for more
  details.
- `UV_PYTHON_INSTALL_DIR`: Used to specify the directory where uv will store managed Python
  installations.
- `UV_PYTHON_INSTALL_MIRROR`: Managed Python installations are downloaded from
  [`python-build-standalone`](https://github.com/indygreg/python-build-standalone). This variable
  can be set to a mirror URL to use a different source for Python installations. The provided URL
  will replace `https://github.com/indygreg/python-build-standalone/releases/download` in, e.g.,
  `https://github.com/indygreg/python-build-standalone/releases/download/20240713/cpython-3.12.4%2B20240713-aarch64-apple-darwin-install_only.tar.gz`.
  Distributions can be read from a local directory by using the `file://` URL scheme.
- `UV_PYPY_INSTALL_MIRROR`: Managed PyPy installations are downloaded from
  [python.org](https://downloads.python.org/). This variable can be set to a mirror URL to use a
  different source for PyPy installations. The provided URL will replace
  `https://downloads.python.org/pypy` in, e.g.,
  `https://downloads.python.org/pypy/pypy3.8-v7.3.7-osx64.tar.bz2`. Distributions can be read from a
  local directory by using the `file://` URL scheme.
- `XDG_CONFIG_HOME`: Used to specify the path to uv user-level configuration directory on Unix
  systems.
- `XDG_CACHE_HOME`: Used to specify the directory where uv stores cache files on Unix systems.
- `XDG_DATA_HOME`: Used to specify the directory where uv stores managed Python installations and
  managed tools on Unix systems.
- `XDG_BIN_HOME`: Used to specify the directory where executables are installed into.
- `SSL_CERT_FILE`: If set, uv will use this file as the certificate bundle instead of the system's
  trust store.
- `SSL_CLIENT_CERT`: If set, uv will use this file for mTLS authentication. This should be a single
  file containing both the certificate and the private key in PEM format.
- `RUST_LOG`: If set, uv will use this value as the log level for its `--verbose` output. Accepts
  any filter compatible with the `tracing_subscriber` crate. For example, `RUST_LOG=trace` will
  enable trace-level logging. See the
  [tracing documentation](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/filter/struct.EnvFilter.html#example-syntax)
  for more.
- `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`: The proxy to use for all HTTP/HTTPS requests.
- `HTTP_TIMEOUT` (or `UV_HTTP_TIMEOUT`): If set, uv will use this value (in seconds) as the timeout
  for HTTP reads (default: 30 s).
- `NETRC`: If set, uv will read authentication information from this file instead of `~/.netrc`.
- `PYC_INVALIDATION_MODE`: The validation modes to use when run with `--compile`. See:
  [`PycInvalidationMode`](https://docs.python.org/3/library/py_compile.html#py_compile.PycInvalidationMode).
- `VIRTUAL_ENV`: Used to detect an activated virtual environment.
- `CONDA_PREFIX`: Used to detect an activated Conda environment.
- `PROMPT`: Used to detect the use of the Windows Command Prompt (as opposed to PowerShell).
- `VIRTUAL_ENV_DISABLE_PROMPT`: If set to `1` before a virtual environment is activated, then the
  virtual environment name will not be prepended to the terminal prompt.
- `NU_VERSION`: Used to detect the use of NuShell.
- `FISH_VERSION`: Used to detect the use of the Fish shell.
- `BASH_VERSION`: Used to detect the use of the Bash shell.
- `ZSH_VERSION`: Used to detect the use of the Zsh shell.
- `MACOSX_DEPLOYMENT_TARGET`: Used with `--python-platform macos` and related variants to set the
  deployment target (i.e., the minimum supported macOS version). Defaults to `12.0`, the
  least-recent non-EOL macOS version at time of writing.
- `NO_COLOR`: Disable colors. Takes precedence over `FORCE_COLOR`. See
  [no-color.org](https://no-color.org).
- `FORCE_COLOR`: Enforce colors regardless of TTY support. See
  [force-color.org](https://force-color.org).
