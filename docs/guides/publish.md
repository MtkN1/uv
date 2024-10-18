# Publishing a package

uvは、`uv build`を介してソースおよびバイナリディストリビューションにPythonパッケージをビルドし、`uv publish`を使用してレジストリにアップロードすることをサポートしています。

## Preparing your project for packaging

プロジェクトを公開する前に、配布用にパッケージ化する準備ができていることを確認する必要があります。

プロジェクトが`pyproject.toml`に`[build-system]`定義を含まない場合、uvはデフォルトでビルドしません。これは、プロジェクトが配布の準備ができていない可能性があることを意味します。ビルドシステムを宣言する効果については、[プロジェクトの概念](../concepts/projects.md#build-systems)のドキュメントを参照してください。

## Building your package

`uv build`を使用してパッケージをビルドします：

```console
$ uv build
```

デフォルトでは、`uv build`は現在のディレクトリでプロジェクトをビルドし、ビルドされたアーティファクトを`dist/`サブディレクトリに配置します。

また、`uv build <SRC>`を使用して指定されたディレクトリ内のパッケージをビルドすることもできますし、`uv build --package <PACKAGE>`を使用して現在のワークスペース内の指定されたパッケージをビルドすることもできます。

!!! info

    デフォルトでは、`uv build` は `pyproject.toml` の `build-system.requires` セクションからビルド依存関係を解決する際に `tool.uv.sources` を尊重します。パッケージを公開する際には、`uv build --no-sources` を実行して、他のビルドツール（例えば[`pypa/build`](https://github.com/pypa/build)）を使用する場合と同様に `tool.uv.sources` が無効になっているときにパッケージが正しくビルドされることを確認することをお勧めします。

## Publishing your package

`uv publish`を使用してパッケージを公開します：

```console
$ uv publish
```

PyPIトークンを`--token`または`UV_PUBLISH_TOKEN`で設定するか、ユーザー名を`--username`または`UV_PUBLISH_USERNAME`で設定し、パスワードを`--password`または`UV_PUBLISH_PASSWORD`で設定します。

!!! info

    GitHub ActionsからPyPIに公開する場合、資格情報を設定する必要はありません。代わりに、[PyPIプロジェクトに信頼できるパブリッシャーを追加](https://docs.pypi.org/trusted-publishers/adding-a-publisher/)してください。

!!! note

    PyPIは、ユーザー名とパスワードでの公開をサポートしていません。代わりにトークンを生成する必要があります。トークンを使用することは、`--username __token__`を設定し、パスワードとしてトークンを使用することと同等です。

## Installing your package

`uv run`を使用してパッケージがインストールおよびインポートできることをテストします：

```console
$ uv run --with <PACKAGE> --no-project -- python -c "import <PACKAGE>"
```

`--no-project`フラグは、ローカルプロジェクトディレクトリからパッケージをインストールしないようにするために使用されます。

!!! tip

    最近パッケージをインストールした場合、キャッシュされたバージョンのパッケージを使用しないようにするために、`--refresh-package <PACKAGE>`オプションを含める必要があるかもしれません。

## Next steps

パッケージの公開について詳しく知るには、[PyPAガイド](https://packaging.python.org/en/latest/guides/section-build-and-publish/)のビルドおよび公開に関するセクションを参照してください。

または、uvの概念についての詳細を読むために進んでください。
