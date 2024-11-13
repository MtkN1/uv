# 環境変数

uvは以下の環境変数を尊重します：

- <a id="UV_DEFAULT_INDEX"></a> [`UV_DEFAULT_INDEX`](#UV_DEFAULT_INDEX): コマンドライン引数 `--default-index` と同等です。設定されている場合、uvは
  このURLをパッケージ検索時のデフォルトインデックスとして使用します。
- <a id="UV_INDEX"></a> [`UV_INDEX`](#UV_INDEX): コマンドライン引数 `--index` と同等です。設定されている場合、uvは
  空白で区切られたURLのリストをパッケージ検索時の追加インデックスとして使用します。
- <a id="UV_INDEX_URL"></a> [`UV_INDEX_URL`](#UV_INDEX_URL): コマンドライン引数 `--index-url` と同等です。設定されている場合、uvは
  このURLをパッケージ検索時のデフォルトインデックスとして使用します。
  （非推奨：代わりに `UV_DEFAULT_INDEX` を使用してください。）
- <a id="UV_EXTRA_INDEX_URL"></a> [`UV_EXTRA_INDEX_URL`](#UV_EXTRA_INDEX_URL): コマンドライン引数 `--extra-index-url` と同等です。設定されている場合、uvは
  空白で区切られたURLのリストをパッケージ検索時の追加インデックスとして使用します。
  （非推奨：代わりに `UV_INDEX` を使用してください。）
- <a id="UV_FIND_LINKS"></a> [`UV_FIND_LINKS`](#UV_FIND_LINKS): コマンドライン引数 `--find-links` と同等です。設定されている場合、uvは
  カンマで区切られた追加のパッケージ検索場所のリストを使用します。
- <a id="UV_CACHE_DIR"></a> [`UV_CACHE_DIR`](#UV_CACHE_DIR): コマンドライン引数 `--cache-dir` と同等です。設定されている場合、uvは
  デフォルトのキャッシュディレクトリの代わりにこのディレクトリをキャッシュに使用します。
- <a id="UV_NO_CACHE"></a> [`UV_NO_CACHE`](#UV_NO_CACHE): コマンドライン引数 `--no-cache` と同等です。設定されている場合、uvは
  どの操作でもキャッシュを使用しません。
- <a id="UV_RESOLUTION"></a> [`UV_RESOLUTION`](#UV_RESOLUTION): コマンドライン引数 `--resolution` と同等です。例えば、`lowest-direct` に設定すると、uvはすべての直接依存関係の最も低い互換バージョンをインストールします。
- <a id="UV_PRERELEASE"></a> [`UV_PRERELEASE`](#UV_PRERELEASE): コマンドライン引数 `--prerelease` と同等です。例えば、`allow` に設定すると、uvはすべての依存関係でプレリリースバージョンを許可します。
- <a id="UV_SYSTEM_PYTHON"></a> [`UV_SYSTEM_PYTHON`](#UV_SYSTEM_PYTHON): コマンドライン引数 `--system` と同等です。`true` に設定されている場合、uvはシステムの `PATH` で最初に見つかったPythonインタープリタを使用します。
  警告：`UV_SYSTEM_PYTHON=true` は継続的インテグレーション（CI）やコンテナ化された環境での使用を意図しており、システムの
  Pythonを変更すると予期しない動作を引き起こす可能性があるため、注意して使用してください。
- <a id="UV_PYTHON"></a> [`UV_PYTHON`](#UV_PYTHON): コマンドライン引数 `--python` と同等です。パスが設定されている場合、uvは
  すべての操作でこのPythonインタープリタを使用します。
- <a id="UV_BREAK_SYSTEM_PACKAGES"></a> [`UV_BREAK_SYSTEM_PACKAGES`](#UV_BREAK_SYSTEM_PACKAGES): コマンドライン引数 `--break-system-packages` と同等です。`true` に設定されている場合、
  uvはシステムにインストールされているパッケージと競合するパッケージのインストールを許可します。
  警告：`UV_BREAK_SYSTEM_PACKAGES=true` は継続的インテグレーション
  （CI）やコンテナ化された環境での使用を意図しており、システムの
  Pythonを変更すると予期しない動作を引き起こす可能性があるため、注意して使用してください。
- <a id="UV_NATIVE_TLS"></a> [`UV_NATIVE_TLS`](#UV_NATIVE_TLS): コマンドライン引数 `--native-tls` と同等です。`true` に設定されている場合、uvは
  バンドルされた `webpki-roots` クレートの代わりにシステムの信頼ストアを使用します。
- <a id="UV_INDEX_STRATEGY"></a> [`UV_INDEX_STRATEGY`](#UV_INDEX_STRATEGY): コマンドライン引数 `--index-strategy` と同等です。例えば、
  `unsafe-any-match` に設定すると、uvは特定のパッケージのバージョンをすべてのインデックス
  URLで利用可能と見なし、パッケージ検索を最初にパッケージが存在するインデックスURLに制限しません。
- <a id="UV_REQUIRE_HASHES"></a> [`UV_REQUIRE_HASHES`](#UV_REQUIRE_HASHES): コマンドライン引数 `--require-hashes` と同等です。`true` に設定されている場合、
  uvはすべての依存関係に `requirements` ファイル内でハッシュが指定されていることを要求します。
- <a id="UV_CONSTRAINT"></a> [`UV_CONSTRAINT`](#UV_CONSTRAINT): コマンドライン引数 `--constraint` と同等です。設定されている場合、uvはこの
  ファイルを制約ファイルとして使用します。ファイルは空白で区切られたリストを使用します。
- <a id="UV_BUILD_CONSTRAINT"></a> [`UV_BUILD_CONSTRAINT`](#UV_BUILD_CONSTRAINT): コマンドライン引数 `--build-constraint` と同等です。設定されている場合、uvはこのファイルを
  ソースディストリビューションビルドの制約として使用します。ファイルは空白で区切られたリストを使用します。
- <a id="UV_OVERRIDE"></a> [`UV_OVERRIDE`](#UV_OVERRIDE): コマンドライン引数 `--override` と同等です。設定されている場合、uvはこのファイルを
  オーバーライドファイルとして使用します。ファイルは空白で区切られたリストを使用します。
- <a id="UV_LINK_MODE"></a> [`UV_LINK_MODE`](#UV_LINK_MODE): コマンドライン引数 `--link-mode` と同等です。設定されている場合、uvはこれをリンクモードとして使用します。
- <a id="UV_NO_BUILD_ISOLATION"></a> [`UV_NO_BUILD_ISOLATION`](#UV_NO_BUILD_ISOLATION): コマンドライン引数 `--no-build-isolation` と同等です。設定されている場合、uvは
  ソースディストリビューションをビルドする際の隔離をスキップします。
- <a id="UV_CUSTOM_COMPILE_COMMAND"></a> [`UV_CUSTOM_COMPILE_COMMAND`](#UV_CUSTOM_COMPILE_COMMAND): コマンドライン引数 `--custom-compile-command` と同等です。
  `uv pip compile` によって生成された `requirements.txt` ファイルの出力ヘッダをオーバーライドするために使用されます。
  ラッパースクリプト内から `uv pip compile` が呼び出されるケースで、出力ファイルにラッパースクリプトの名前を含めるために使用します。
- <a id="UV_KEYRING_PROVIDER"></a> [`UV_KEYRING_PROVIDER`](#UV_KEYRING_PROVIDER): コマンドライン引数 `--keyring-provider` と同等です。設定されている場合、uvはこの値をキーリングプロバイダーとして使用します。
- <a id="UV_CONFIG_FILE"></a> [`UV_CONFIG_FILE`](#UV_CONFIG_FILE): コマンドライン引数 `--config-file` と同等です。設定ファイルとして使用するローカル `uv.toml` ファイルへのパスを期待します。
- <a id="UV_NO_CONFIG"></a> [`UV_NO_CONFIG`](#UV_NO_CONFIG): コマンドライン引数 `--no-config` と同等です。設定されている場合、uvは現在のディレクトリ、親ディレクトリ、またはユーザー設定ディレクトリからの設定ファイルを読み込みません。
- <a id="UV_EXCLUDE_NEWER"></a> [`UV_EXCLUDE_NEWER`](#UV_EXCLUDE_NEWER): コマンドライン引数 `--exclude-newer` と同等です。設定されている場合、uvは
  指定された日付以降に公開されたディストリビューションを除外します。
- <a id="UV_PYTHON_PREFERENCE"></a> [`UV_PYTHON_PREFERENCE`](#UV_PYTHON_PREFERENCE): コマンドライン引数 `--python-preference` と同等です。uvがシステムまたは管理されたPythonバージョンを優先すべきかどうかを指定します。
- <a id="UV_PYTHON_DOWNLOADS"></a> [`UV_PYTHON_DOWNLOADS`](#UV_PYTHON_DOWNLOADS): 
  [`python-downloads`](../reference/settings.md#python-downloads) 設定と同等で、無効にすると
  `--no-python-downloads` オプションと同等になります。uvがPythonのダウンロードを許可するかどうかを指定します。
- <a id="UV_COMPILE_BYTECODE"></a> [`UV_COMPILE_BYTECODE`](#UV_COMPILE_BYTECODE): コマンドライン引数 `--compile-bytecode` と同等です。設定されている場合、uvは
  インストール後にPythonソースファイルをバイトコードにコンパイルします。
- <a id="UV_PUBLISH_URL"></a> [`UV_PUBLISH_URL`](#UV_PUBLISH_URL): コマンドライン引数 `--publish-url` と同等です。`uv publish` で使用するインデックスのアップロードエンドポイントのURLです。
- <a id="UV_PUBLISH_TOKEN"></a> [`UV_PUBLISH_TOKEN`](#UV_PUBLISH_TOKEN): `uv publish` のコマンドライン引数 `--token` と同等です。設定されている場合、uvはこのトークン（ユーザー名 `__token__` と共に）を使用して公開します。
- <a id="UV_PUBLISH_USERNAME"></a> [`UV_PUBLISH_USERNAME`](#UV_PUBLISH_USERNAME): `uv publish` のコマンドライン引数 `--username` と同等です。設定されている場合、uvは公開にこのユーザー名を使用します。
- <a id="UV_PUBLISH_PASSWORD"></a> [`UV_PUBLISH_PASSWORD`](#UV_PUBLISH_PASSWORD): `uv publish` のコマンドライン引数 `--password` と同等です。設定されている場合、uvは公開にこのパスワードを使用します。
- <a id="UV_PUBLISH_CHECK_URL"></a> [`UV_PUBLISH_CHECK_URL`](#UV_PUBLISH_CHECK_URL): インデックス上にファイルが既に存在する場合、ファイルをアップロードしません。値はインデックスのURLです。
- <a id="UV_NO_SYNC"></a> [`UV_NO_SYNC`](#UV_NO_SYNC): コマンドライン引数 `--no-sync` と同等です。設定されている場合、uvは
  環境の更新をスキップします。
- <a id="UV_LOCKED"></a> [`UV_LOCKED`](#UV_LOCKED): コマンドライン引数 `--locked` と同等です。設定されている場合、uvは
  `uv.lock` が変更されていないことを確認します。
- <a id="UV_FROZEN"></a> [`UV_FROZEN`](#UV_FROZEN): コマンドライン引数 `--frozen` と同等です。設定されている場合、uvは
  `uv.lock` ファイルを更新せずに実行します。
- <a id="UV_PREVIEW"></a> [`UV_PREVIEW`](#UV_PREVIEW): コマンドライン引数 `--preview` と同等です。プレビューモードを有効にします。
- <a id="UV_GITHUB_TOKEN"></a> [`UV_GITHUB_TOKEN`](#UV_GITHUB_TOKEN): セルフアップデートのための `--token` 引数と同等です。認証用のGitHubトークンです。
- <a id="UV_VERIFY_HASHES"></a> [`UV_VERIFY_HASHES`](#UV_VERIFY_HASHES): コマンドライン引数 `--verify-hashes` と同等です。含まれるハッシュを検証します。
- <a id="UV_INSECURE_HOST"></a> [`UV_INSECURE_HOST`](#UV_INSECURE_HOST): コマンドライン引数 `--allow-insecure-host` と同等です。
- <a id="UV_CONCURRENT_DOWNLOADS"></a> [`UV_CONCURRENT_DOWNLOADS`](#UV_CONCURRENT_DOWNLOADS): uvが同時に実行する最大のインフライトダウンロード数を設定します。
- <a id="UV_CONCURRENT_BUILDS"></a> [`UV_CONCURRENT_BUILDS`](#UV_CONCURRENT_BUILDS): uvが同時にビルドするソースディストリビューションの最大数を設定します。
- <a id="UV_CONCURRENT_INSTALLS"></a> [`UV_CONCURRENT_INSTALLS`](#UV_CONCURRENT_INSTALLS): パッケージのインストールおよび解凍時に使用するスレッド数を制御します。
- <a id="UV_NO_PROGRESS"></a> [`UV_NO_PROGRESS`](#UV_NO_PROGRESS): すべての進行状況出力を無効にします。例えば、スピナーやプログレスバー。
- <a id="UV_TOOL_DIR"></a> [`UV_TOOL_DIR`](#UV_TOOL_DIR): uvが管理ツールを保存するディレクトリを指定します。
- <a id="UV_TOOL_BIN_DIR"></a> [`UV_TOOL_BIN_DIR`](#UV_TOOL_BIN_DIR): ツール実行可能ファイルをインストールするための "bin" ディレクトリを指定します。
- <a id="UV_PROJECT_ENVIRONMENT"></a> [`UV_PROJECT_ENVIRONMENT`](#UV_PROJECT_ENVIRONMENT): プロジェクト仮想環境に使用するディレクトリへのパスを指定します。
  詳細は [プロジェクトのドキュメント](../concepts/projects.md#configuring-the-project-environment-path) を参照してください。
- <a id="UV_PYTHON_BIN_DIR"></a> [`UV_PYTHON_BIN_DIR`](#UV_PYTHON_BIN_DIR): 管理されたPython実行可能ファイルへのリンクを配置するディレクトリを指定します。
- <a id="UV_PYTHON_INSTALL_DIR"></a> [`UV_PYTHON_INSTALL_DIR`](#UV_PYTHON_INSTALL_DIR): 管理されたPythonインストールを保存するディレクトリを指定します。
- <a id="UV_PYTHON_INSTALL_MIRROR"></a> [`UV_PYTHON_INSTALL_MIRROR`](#UV_PYTHON_INSTALL_MIRROR): 管理されたPythonインストールは
  [`python-build-standalone`](https://github.com/indygreg/python-build-standalone) からダウンロードされます。
  この変数をミラーURLに設定することで、Pythonインストールの別のソースを使用できます。
  提供されたURLは、例えば
  `https://github.com/indygreg/python-build-standalone/releases/download/20240713/cpython-3.12.4%2B20240713-aarch64-apple-darwin-install_only.tar.gz`
  内の `https://github.com/indygreg/python-build-standalone/releases/download` を置き換えます。
  ディストリビューションは `file://` URLスキームを使用してローカルディレクトリから読み取ることができます。
- <a id="UV_PYPY_INSTALL_MIRROR"></a> [`UV_PYPY_INSTALL_MIRROR`](#UV_PYPY_INSTALL_MIRROR): 管理されたPyPyインストールは
  [python.org](https://downloads.python.org/) からダウンロードされます。この変数をミラーURLに設定することで、
  PyPyインストールの別のソースを使用できます。提供されたURLは、
  例えば
  `https://downloads.python.org/pypy/pypy3.8-v7.3.7-osx64.tar.bz2`
  内の `https://downloads.python.org/pypy` を置き換えます。
  ディストリビューションは `file://` URLスキームを使用してローカルディレクトリから読み取ることができます。
- <a id="UV_NO_WRAP"></a> [`UV_NO_WRAP`](#UV_NO_WRAP): 診断のための行の折り返しを無効にするために使用します。
- <a id="UV_STACK_SIZE"></a> [`UV_STACK_SIZE`](#UV_STACK_SIZE): uvで使用するスタックサイズを制御するために使用します。通常、デバッグモードのWindowsでより関連性があります。
- <a id="UV_INDEX_{name}_USERNAME"></a> [`UV_INDEX_{name}_USERNAME`](#UV_INDEX_{name}_USERNAME): HTTP Basic認証のユーザー名のための環境変数キーを生成します。
- <a id="UV_INDEX_{name}_PASSWORD"></a> [`UV_INDEX_{name}_PASSWORD`](#UV_INDEX_{name}_PASSWORD): HTTP Basic認証のパスワードのための環境変数キーを生成します。
- <a id="XDG_CONFIG_DIRS"></a> [`XDG_CONFIG_DIRS`](#XDG_CONFIG_DIRS): Unixシステム上のシステムレベルの設定ディレクトリへのパス。
- <a id="SYSTEMDRIVE"></a> [`SYSTEMDRIVE`](#SYSTEMDRIVE): Windowsシステム上のシステムレベルの設定ディレクトリへのパス。
- <a id="XDG_CONFIG_HOME"></a> [`XDG_CONFIG_HOME`](#XDG_CONFIG_HOME): Unixシステム上のユーザーレベルの設定ディレクトリへのパス。
- <a id="XDG_CACHE_HOME"></a> [`XDG_CACHE_HOME`](#XDG_CACHE_HOME): Unixシステム上のキャッシュディレクトリへのパス。
- <a id="XDG_DATA_HOME"></a> [`XDG_DATA_HOME`](#XDG_DATA_HOME): 管理されたPythonインストールおよびツールを保存するためのディレクトリへのパス。
- <a id="XDG_BIN_HOME"></a> [`XDG_BIN_HOME`](#XDG_BIN_HOME): 実行可能ファイルがインストールされるディレクトリへのパス。
- <a id="SSL_CERT_FILE"></a> [`SSL_CERT_FILE`](#SSL_CERT_FILE): SSL接続用のカスタム証明書バンドルファイルのパス。
- <a id="SSL_CLIENT_CERT"></a> [`SSL_CLIENT_CERT`](#SSL_CLIENT_CERT): 設定されている場合、uvはこのファイルをmTLS認証に使用します。
  これは、PEM形式で証明書と秘密鍵の両方を含む単一のファイルである必要があります。
- <a id="HTTP_PROXY"></a> [`HTTP_PROXY`](#HTTP_PROXY): HTTPリクエスト用のプロキシ。
- <a id="HTTPS_PROXY"></a> [`HTTPS_PROXY`](#HTTPS_PROXY): HTTPSリクエスト用のプロキシ。
- <a id="ALL_PROXY"></a> [`ALL_PROXY`](#ALL_PROXY): すべてのネットワークリクエスト用の一般的なプロキシ。
- <a id="UV_HTTP_TIMEOUT"></a> [`UV_HTTP_TIMEOUT`](#UV_HTTP_TIMEOUT): HTTPリクエストのタイムアウト（秒単位）。（デフォルト：30秒）
- <a id="UV_REQUEST_TIMEOUT"></a> [`UV_REQUEST_TIMEOUT`](#UV_REQUEST_TIMEOUT): HTTPリクエストのタイムアウト（秒単位）。 `UV_HTTP_TIMEOUT` と同等です。
- <a id="HTTP_TIMEOUT"></a> [`HTTP_TIMEOUT`](#HTTP_TIMEOUT): HTTPリクエストのタイムアウト（秒単位）。 `UV_HTTP_TIMEOUT` と同等です。
- <a id="PYC_INVALIDATION_MODE"></a> [`PYC_INVALIDATION_MODE`](#PYC_INVALIDATION_MODE): `--compile` オプションで実行するときに使用する検証モード。
  [`PycInvalidationMode`](https://docs.python.org/3/library/py_compile.html#py_compile.PycInvalidationMode) を参照してください。
- <a id="VIRTUAL_ENV"></a> [`VIRTUAL_ENV`](#VIRTUAL_ENV): アクティブな仮想環境を検出するために使用されます。
- <a id="CONDA_PREFIX"></a> [`CONDA_PREFIX`](#CONDA_PREFIX): アクティブなConda環境を検出するために使用されます。
- <a id="CONDA_DEFAULT_ENV"></a> [`CONDA_DEFAULT_ENV`](#CONDA_DEFAULT_ENV): アクティブなConda環境がベース環境かどうかを判断するために使用されます。
- <a id="VIRTUAL_ENV_DISABLE_PROMPT"></a> [`VIRTUAL_ENV_DISABLE_PROMPT`](#VIRTUAL_ENV_DISABLE_PROMPT): 仮想環境がアクティブ化される前に `1` に設定されている場合、
  仮想環境の名前がターミナルプロンプトに先頭に追加されません。
- <a id="PROMPT"></a> [`PROMPT`](#PROMPT): Windowsコマンドプロンプトの使用を検出するために使用されます（PowerShellとは対照的に）。
- <a id="NU_VERSION"></a> [`NU_VERSION`](#NU_VERSION): `NuShell` の使用を検出するために使用されます。
- <a id="FISH_VERSION"></a> [`FISH_VERSION`](#FISH_VERSION): Fishシェルの使用を検出するために使用されます。
- <a id="BASH_VERSION"></a> [`BASH_VERSION`](#BASH_VERSION): Bashシェルの使用を検出するために使用されます。
- <a id="ZSH_VERSION"></a> [`ZSH_VERSION`](#ZSH_VERSION): Zshシェルの使用を検出するために使用されます。
- <a id="ZDOTDIR"></a> [`ZDOTDIR`](#ZDOTDIR): Zshが使用されている場合にどの `.zshenv` を使用するかを判断するために使用されます。
- <a id="KSH_VERSION"></a> [`KSH_VERSION`](#KSH_VERSION): Kshシェルの使用を検出するために使用されます。
- <a id="MACOSX_DEPLOYMENT_TARGET"></a> [`MACOSX_DEPLOYMENT_TARGET`](#MACOSX_DEPLOYMENT_TARGET): `--python-platform macos` および関連するバリアントで使用され、
  デプロイメントターゲット（つまり、サポートされる最低限のmacOSバージョン）を設定します。
  記載時点での最も最近の非EOL macOSバージョン `12.0` にデフォルト設定されています。
- <a id="NO_COLOR"></a> [`NO_COLOR`](#NO_COLOR): 色付き出力を無効にします（ `FORCE_COLOR` より優先されます）。
  [no-color.org](https://no-color.org) を参照してください。
- <a id="FORCE_COLOR"></a> [`FORCE_COLOR`](#FORCE_COLOR): ターミナルのサポートに関係なく色付き出力を強制します。
  [force-color.org](https://force-color.org) を参照してください。
- <a id="CLICOLOR_FORCE"></a> [`CLICOLOR_FORCE`](#CLICOLOR_FORCE): `anstyle` を介して色を制御するために使用します。
- <a id="PATH"></a> [`PATH`](#PATH): 標準の `PATH` 環境変数。
- <a id="HOME"></a> [`HOME`](#HOME): 標準の `HOME` 環境変数。
- <a id="SHELL"></a> [`SHELL`](#SHELL): 標準の `SHELL` POSIX 環境変数。
- <a id="PWD"></a> [`PWD`](#PWD): 標準の `PWD` POSIX 環境変数。
- <a id="LOCALAPPDATA"></a> [`LOCALAPPDATA`](#LOCALAPPDATA): Microsoft StoreのPythonインストールを探すために使用されます。
- <a id="GITHUB_ACTIONS"></a> [`GITHUB_ACTIONS`](#GITHUB_ACTIONS): `uv publish` を介した信頼された公開に使用されます。
- <a id="ACTIONS_ID_TOKEN_REQUEST_URL"></a> [`ACTIONS_ID_TOKEN_REQUEST_URL`](#ACTIONS_ID_TOKEN_REQUEST_URL): `uv publish` を介した信頼された公開に使用されます。oidcトークンURLを含みます。
- <a id="ACTIONS_ID_TOKEN_REQUEST_TOKEN"></a> [`ACTIONS_ID_TOKEN_REQUEST_TOKEN`](#ACTIONS_ID_TOKEN_REQUEST_TOKEN): `uv publish` を介した信頼された公開に使用されます。oidcリクエストトークンを含みます。
- <a id="PYTHONPATH"></a> [`PYTHONPATH`](#PYTHONPATH): Pythonモジュール検索パスにディレクトリを追加します（例：`PYTHONPATH=/path/to/modules`）。
- <a id="NETRC"></a> [`NETRC`](#NETRC): .netrcファイルの場所を設定するために使用します。
- <a id="PAGER"></a> [`PAGER`](#PAGER): 標準の `PAGER` POSIX 環境変数。`uv` が適切なページャーを設定するために使用します。
- <a id="JPY_SESSION_NAME"></a> [`JPY_SESSION_NAME`](#JPY_SESSION_NAME): Jupyterノートブック内で実行されていることを検出するために使用されます。
- <a id="TRACING_DURATIONS_FILE"></a> [`TRACING_DURATIONS_FILE`](#TRACING_DURATIONS_FILE): `tracing-durations-export` 機能を介してトレースの期間ファイルを作成するために使用します。
- <a id="RUST_LOG"></a> [`RUST_LOG`](#RUST_LOG): 設定されている場合、uvはこの値を `--verbose` 出力のログレベルとして使用します。
  `tracing_subscriber` クレートと互換性のあるフィルターを受け入れます。
  例えば：
  * `RUST_LOG=uv=debug` はコマンドラインに `--verbose` を追加するのと同等です。
  * `RUST_LOG=trace` はトレースレベルのログを有効にします。
  詳細は [tracingドキュメント](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/filter/struct.EnvFilter.html#example-syntax) を参照してください。
- <a id="UV_ENV_FILE"></a> [`UV_ENV_FILE`](#UV_ENV_FILE): `uv run` コマンドを実行するときに環境変数をロードする `.env` ファイル。
- <a id="UV_NO_ENV_FILE"></a> [`UV_NO_ENV_FILE`](#UV_NO_ENV_FILE): `uv run` コマンドを実行するときに `.env` ファイルを無視します。
- <a id="UV_INSTALLER_GITHUB_BASE_URL"></a> [`UV_INSTALLER_GITHUB_BASE_URL`](#UV_INSTALLER_GITHUB_BASE_URL): スタンドアロンインストーラーと `self update` 機能を使用してuvをダウンロードするURLで、
  デフォルトのGitHub URLの代わりに使用します。
- <a id="UV_INSTALLER_GHE_BASE_URL"></a> [`UV_INSTALLER_GHE_BASE_URL`](#UV_INSTALLER_GHE_BASE_URL): スタンドアロンインストーラーと `self update` 機能を使用してuvをダウンロードするURLで、
  デフォルトのGitHub Enterprise URLの代わりに使用します。
- <a id="UV_INSTALL_DIR"></a> [`UV_INSTALL_DIR`](#UV_INSTALL_DIR): スタンドアロンインストーラーと `self update` 機能を使用してuvをインストールするディレクトリ。
  デフォルトは `~/.local/bin` です。
- <a id="UV_UNMANAGED_INSTALL"></a> [`UV_UNMANAGED_INSTALL`](#UV_UNMANAGED_INSTALL): CIのようなエフェメラル環境でuvを特定のパスにインストールし、インストーラーがシェルプロファイルや環境変数を変更するのを防ぐために使用します。
- <a id="INSTALLER_NO_MODIFY_PATH"></a> [`INSTALLER_NO_MODIFY_PATH`](#INSTALLER_NO_MODIFY_PATH): スタンドアロンインストーラーと `self update` 機能を使用してuvをインストールするときに `PATH` 環境変数を変更しないようにします。
