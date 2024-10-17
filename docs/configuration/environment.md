# Environment variables

uv は、次のコマンドライン引数を環境変数として受け入れます：

- `UV_INDEX_URL`: `--index-url` コマンドライン引数に相当します。設定されている場合、uv はこの URL をパッケージ検索のベースインデックスとして使用します。
- `UV_EXTRA_INDEX_URL`: `--extra-index-url` コマンドライン引数に相当します。設定されている場合、uv はこのスペースで区切られた URL のリストをパッケージ検索時の追加インデックスとして使用します。
- `UV_FIND_LINKS`: `--find-links` コマンドライン引数に相当します。設定されている場合、uv はこのカンマで区切られた追加の場所のリストをパッケージ検索に使用します。
- `UV_CACHE_DIR`: `--cache-dir` コマンドライン引数に相当します。設定されている場合、uv はデフォルトのキャッシュディレクトリの代わりにこのディレクトリをキャッシュに使用します。
- `UV_NO_CACHE`: `--no-cache` コマンドライン引数に相当します。設定されている場合、uv はいかなる操作でもキャッシュを使用しません。
- `UV_RESOLUTION`: `--resolution` コマンドライン引数に相当します。たとえば、`lowest-direct` に設定されている場合、uv はすべての直接依存関係の最も低い互換バージョンをインストールします。
- `UV_PRERELEASE`: `--prerelease` コマンドライン引数に相当します。たとえば、`allow` に設定されている場合、uv はすべての依存関係に対してプレリリースバージョンを許可します。
- `UV_SYSTEM_PYTHON`: `--system` コマンドライン引数に相当します。`true` に設定されている場合、uv はシステム `PATH` で見つかった最初の Python インタープリタを使用します。警告：`UV_SYSTEM_PYTHON=true` は継続的インテグレーション（CI）またはコンテナ化された環境での使用を意図しており、システム Python を変更すると予期しない動作が発生する可能性があるため、注意して使用する必要があります。
- `UV_PYTHON`: `--python` コマンドライン引数に相当します。パスに設定されている場合、uv はすべての操作にこの Python インタープリタを使用します。
- `UV_BREAK_SYSTEM_PACKAGES`: `--break-system-packages` コマンドライン引数に相当します。`true` に設定されている場合、uv はシステムにインストールされたパッケージと競合するパッケージのインストールを許可します。警告：`UV_BREAK_SYSTEM_PACKAGES=true` は継続的インテグレーション（CI）またはコンテナ化された環境での使用を意図しており、システム Python を変更すると予期しない動作が発生する可能性があるため、注意して使用する必要があります。
- `UV_NATIVE_TLS`: `--native-tls` コマンドライン引数に相当します。`true` に設定されている場合、uv はバンドルされた `webpki-roots` クレートの代わりにシステムの信頼ストアを使用します。
- `UV_INDEX_STRATEGY`: `--index-strategy` コマンドライン引数に相当します。たとえば、`unsafe-any-match` に設定されている場合、uv は最初のインデックス URL に制限せず、すべてのインデックス URL で利用可能なパッケージのバージョンを考慮します。
- `UV_REQUIRE_HASHES`: `--require-hashes` コマンドライン引数に相当します。`true` に設定されている場合、uv はすべての依存関係に要件ファイルでハッシュが指定されていることを要求します。
- `UV_CONSTRAINT`: `--constraint` コマンドライン引数に相当します。設定されている場合、uv はこのファイルを制約ファイルとして使用します。スペースで区切られたファイルのリストを使用します。
- `UV_BUILD_CONSTRAINT`: `--build-constraint` コマンドライン引数に相当します。設定されている場合、uv はこのファイルをソースディストリビューションビルドの制約として使用します。スペースで区切られたファイルのリストを使用します。
- `UV_OVERRIDE`: `--override` コマンドライン引数に相当します。設定されている場合、uv はこのファイルをオーバーライドファイルとして使用します。スペースで区切られたファイルのリストを使用します。
- `UV_LINK_MODE`: `--link-mode` コマンドライン引数に相当します。設定されている場合、uv はこれをリンクモードとして使用します。
- `UV_NO_BUILD_ISOLATION`: `--no-build-isolation` コマンドライン引数に相当します。設定されている場合、uv はソースディストリビューションのビルド時に分離をスキップします。
- `UV_CUSTOM_COMPILE_COMMAND`: `--custom-compile-command` コマンドライン引数に相当します。`uv pip compile` によって生成された `requirements.txt` ファイルの出力ヘッダーで uv をオーバーライドするために使用されます。ラッパースクリプト内から `uv pip compile` が呼び出されるユースケースを対象としており、出力ファイルにラッパースクリプトの名前を含めるために使用されます。
- `UV_KEYRING_PROVIDER`: `--keyring-provider` コマンドライン引数に相当します。設定されている場合、uv はこの値をキーチェーンプロバイダーとして使用します。
- `UV_CONFIG_FILE`: `--config-file` コマンドライン引数に相当します。ローカルの `uv.toml` ファイルへのパスを期待します。
- `UV_NO_CONFIG`: `--no-config` コマンドライン引数に相当します。設定されている場合、uv は現在のディレクトリ、親ディレクトリ、またはユーザー構成ディレクトリから構成ファイルを読み取りません。
- `UV_EXCLUDE_NEWER`: `--exclude-newer` コマンドライン引数に相当します。設定されている場合、uv は指定された日付以降に公開されたディストリビューションを除外します。
- `UV_PYTHON_PREFERENCE`: `--python-preference` コマンドライン引数に相当します。uv がシステムまたは管理された Python バージョンを優先するかどうか。
- `UV_PYTHON_DOWNLOADS`: [`python-downloads`](../reference/settings.md#python-downloads) 設定および無効化時の `--no-python-downloads` オプションに相当します。uv が Python のダウンロードを許可するかどうか。
- `UV_COMPILE_BYTECODE`: `--compile-bytecode` コマンドライン引数に相当します。設定されている場合、uv はインストール後に Python ソースファイルをバイトコードにコンパイルします。
- `UV_PUBLISH_URL`: `--publish-url` コマンドライン引数に相当します。`uv publish` で使用するインデックスのアップロードエンドポイントの URL。
- `UV_PUBLISH_TOKEN`: `uv publish` の `--token` コマンドライン引数に相当します。設定されている場合、uv はこのトークン（ユーザー名 `__token__` と共に）を公開に使用します。
- `UV_PUBLISH_USERNAME`: `uv publish` の `--username` コマンドライン引数に相当します。設定されている場合、uv はこのユーザー名を公開に使用します。
- `UV_PUBLISH_PASSWORD`: `uv publish` の `--password` コマンドライン引数に相当します。設定されている場合、uv はこのパスワードを公開に使用します。
- `UV_NO_SYNC`: `--no-sync` コマンドライン引数に相当します。設定されている場合、uv は環境の更新をスキップします。

いずれの場合も、対応するコマンドライン引数が環境変数よりも優先されます。

さらに、uv は次の環境変数を尊重します：

- `UV_CONCURRENT_DOWNLOADS`: uv が任意の時点で実行する最大同時ダウンロード数を設定します。
- `UV_CONCURRENT_BUILDS`: uv が任意の時点で同時にビルドするソースディストリビューションの最大数を設定します。
- `UV_CONCURRENT_INSTALLS`: パッケージのインストールおよび解凍時に使用するスレッド数を制御するために使用されます。
- `UV_TOOL_DIR`: uv が管理ツールを保存するディレクトリを指定するために使用されます。
- `UV_TOOL_BIN_DIR`: uv がツールの実行ファイルをインストールする "bin" ディレクトリを指定するために使用されます。
- `UV_PROJECT_ENVIRONMENT`: プロジェクト仮想環境に使用するディレクトリのパスを指定するために使用されます。詳細については、[プロジェクトドキュメント](../concepts/projects.md#configuring-the-project-environment-path) を参照してください。
- `UV_PYTHON_INSTALL_DIR`: uv が管理された Python インストールを保存するディレクトリを指定するために使用されます。
- `UV_PYTHON_INSTALL_MIRROR`: 管理された Python インストールは [`python-build-standalone`](https://github.com/indygreg/python-build-standalone) からダウンロードされます。この変数をミラー URL に設定して、Python インストールの別のソースを使用できます。提供された URL は、たとえば `https://github.com/indygreg/python-build-standalone/releases/download/20240713/cpython-3.12.4%2B20240713-aarch64-apple-darwin-install_only.tar.gz` の `https://github.com/indygreg/python-build-standalone/releases/download` を置き換えます。`file://` URL スキームを使用してローカルディレクトリからディストリビューションを読み取ることができます。
- `UV_PYPY_INSTALL_MIRROR`: 管理された PyPy インストールは [python.org](https://downloads.python.org/) からダウンロードされます。この変数をミラー URL に設定して、PyPy インストールの別のソースを使用できます。提供された URL は、たとえば `https://downloads.python.org/pypy/pypy3.8-v7.3.7-osx64.tar.bz2` の `https://downloads.python.org/pypy` を置き換えます。`file://` URL スキームを使用してローカルディレクトリからディストリビューションを読み取ることができます。
- `XDG_CONFIG_HOME`: Unix システムで uv ユーザーレベルの構成ディレクトリのパスを指定するために使用されます。
- `XDG_CACHE_HOME`: Unix システムで uv がキャッシュファイルを保存するディレクトリを指定するために使用されます。
- `XDG_DATA_HOME`: Unix システムで uv が管理された Python インストールおよび管理ツールを保存するディレクトリを指定するために使用されます。
- `XDG_BIN_HOME`: 実行ファイルがインストールされるディレクトリを指定するために使用されます。
- `SSL_CERT_FILE`: 設定されている場合、uv はシステムの信頼ストアの代わりにこのファイルを証明書バンドルとして使用します。
- `SSL_CLIENT_CERT`: 設定されている場合、uv はこのファイルを mTLS 認証に使用します。これは、証明書と秘密鍵の両方を PEM 形式で含む単一のファイルである必要があります。
- `RUST_LOG`: 設定されている場合、uv は `--verbose` 出力のログレベルとしてこの値を使用します。`tracing_subscriber` クレートと互換性のあるフィルタを受け入れます。たとえば、`RUST_LOG=trace` はトレースレベルのログを有効にします。詳細については、[tracing ドキュメント](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/filter/struct.EnvFilter.html#example-syntax) を参照してください。
- `HTTP_PROXY`, `HTTPS_PROXY`, `ALL_PROXY`: すべての HTTP/HTTPS リクエストに使用するプロキシ。
- `HTTP_TIMEOUT`（または `UV_HTTP_TIMEOUT`）: 設定されている場合、uv は HTTP 読み取りのタイムアウトとしてこの値（秒単位）を使用します（デフォルト：30 秒）。
- `PYC_INVALIDATION_MODE`: `--compile` で実行する場合の検証モード。参照：[PycInvalidationMode](https://docs.python.org/3/library/py_compile.html#py_compile.PycInvalidationMode)。
- `VIRTUAL_ENV`: アクティブ化された仮想環境を検出するために使用されます。
- `CONDA_PREFIX`: アクティブ化された Conda 環境を検出するために使用されます。
- `PROMPT`: Windows コマンドプロンプト（PowerShell ではなく）の使用を検出するために使用されます。
- `VIRTUAL_ENV_DISABLE_PROMPT`: 仮想環境がアクティブ化される前に `1` に設定されている場合、仮想環境名はターミナルプロンプトに追加されません。
- `NU_VERSION`: NuShell の使用を検出するために使用されます。
- `FISH_VERSION`: Fish シェルの使用を検出するために使用されます。
- `BASH_VERSION`: Bash シェルの使用を検出するために使用されます。
- `ZSH_VERSION`: Zsh シェルの使用を検出するために使用されます。
- `MACOSX_DEPLOYMENT_TARGET`: `--python-platform macos` および関連するバリアントで使用され、デプロイメントターゲット（つまり、サポートされる最小の macOS バージョン）を設定します。デフォルトは `12.0` で、執筆時点での最も古い非 EOL の macOS バージョンです。
- `NO_COLOR`: 色を無効にします。`FORCE_COLOR` よりも優先されます。参照：[no-color.org](https://no-color.org)。
- `FORCE_COLOR`: TTY サポートに関係なく色を強制します。参照：[force-color.org](https://force-color.org)。
