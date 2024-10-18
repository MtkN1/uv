# Using uv in Docker

## Getting started

!!! tip

    [`uv-docker-example`](https://github.com/astral-sh/uv-docker-example) プロジェクトをチェックして、Docker でアプリケーションを構築する際の uv のベストプラクティスの例を確認してください。

### Running uv in a container

uv のビルド済みバージョンが利用可能な Docker イメージが公開されています。コンテナ内で uv コマンドを実行するには:

```console
$ docker run ghcr.io/astral-sh/uv --help
```

### Available images

uv は `uv` バイナリを含む distroless Docker イメージを提供しています。以下のタグが公開されています:

- `ghcr.io/astral-sh/uv:latest`
- `ghcr.io/astral-sh/uv:{major}.{minor}.{patch}`, 例: `ghcr.io/astral-sh/uv:0.4.24`
- `ghcr.io/astral-sh/uv:{major}.{minor}`, 例: `ghcr.io/astral-sh/uv:0.4` (最新のパッチバージョン)

さらに、uv は以下のイメージも公開しています:

<!-- prettier-ignore -->
- `alpine:3.20` ベース:
    - `ghcr.io/astral-sh/uv:alpine`
    - `ghcr.io/astral-sh/uv:alpine3.20`
- `debian:bookworm-slim` ベース:
    - `ghcr.io/astral-sh/uv:debian-slim`
    - `ghcr.io/astral-sh/uv:bookworm-slim`
- `buildpack-deps:bookworm` ベース:
    - `ghcr.io/astral-sh/uv:debian`
    - `ghcr.io/astral-sh/uv:bookworm`
- `python3.x-alpine` ベース:
    - `ghcr.io/astral-sh/uv:python3.13-alpine`
    - `ghcr.io/astral-sh/uv:python3.12-alpine`
    - `ghcr.io/astral-sh/uv:python3.11-alpine`
    - `ghcr.io/astral-sh/uv:python3.10-alpine`
    - `ghcr.io/astral-sh/uv:python3.9-alpine`
    - `ghcr.io/astral-sh/uv:python3.8-alpine`
- `python3.x-bookworm` ベース:
    - `ghcr.io/astral-sh/uv:python3.13-bookworm`
    - `ghcr.io/astral-sh/uv:python3.12-bookworm`
    - `ghcr.io/astral-sh/uv:python3.11-bookworm`
    - `ghcr.io/astral-sh/uv:python3.10-bookworm`
    - `ghcr.io/astral-sh/uv:python3.9-bookworm`
    - `ghcr.io/astral-sh/uv:python3.8-bookworm`
- `python3.x-slim-bookworm` ベース:
    - `ghcr.io/astral-sh/uv:python3.13-bookworm-slim`
    - `ghcr.io/astral-sh/uv:python3.12-bookworm-slim`
    - `ghcr.io/astral-sh/uv:python3.11-bookworm-slim`
    - `ghcr.io/astral-sh/uv:python3.10-bookworm-slim`
    - `ghcr.io/astral-sh/uv:python3.9-bookworm-slim`
    - `ghcr.io/astral-sh/uv:python3.8-bookworm-slim`
<!-- prettier-ignore-end -->

distroless イメージと同様に、各イメージは uv バージョンタグ付きで公開されます:
`ghcr.io/astral-sh/uv:{major}.{minor}.{patch}-{base}` および
`ghcr.io/astral-sh/uv:{major}.{minor}-{base}`, 例: `ghcr.io/astral-sh/uv:0.4.24-alpine`.

詳細については、[GitHub Container](https://github.com/astral-sh/uv/pkgs/container/uv) ページを参照してください。

### Installing uv

上記の uv がプリインストールされたイメージのいずれかを使用するか、公式の distroless Docker イメージからバイナリをコピーして uv をインストールします:

```dockerfile title="Dockerfile"
FROM python:3.12-slim-bookworm
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/
```

または、インストーラーを使用して:

```dockerfile title="Dockerfile"
FROM python:3.12-slim-bookworm

# インストーラーはリリースアーカイブをダウンロードするために curl (および証明書) を必要とします
RUN apt-get update && apt-get install -y --no-install-recommends curl ca-certificates

# 最新のインストーラーをダウンロード
ADD https://astral.sh/uv/install.sh /uv-installer.sh

# インストーラーを実行してから削除
RUN sh /uv-installer.sh && rm /uv-installer.sh

# インストールされたバイナリが `PATH` にあることを確認
ENV PATH="/root/.cargo/bin/:$PATH"
```

これには `curl` が必要です。

いずれの場合も、特定の uv バージョンに固定することがベストプラクティスです。例:

```dockerfile
COPY --from=ghcr.io/astral-sh/uv:0.4.24 /uv /uvx /bin/
```

または、インストーラーを使用して:

```dockerfile
ADD https://astral.sh/uv/0.4.24/install.sh /uv-installer.sh
```

### Installing a project

uv を使用してプロジェクトを管理している場合、イメージにコピーしてインストールできます:

```dockerfile title="Dockerfile"
# プロジェクトをイメージにコピー
ADD . /app

# フローズンロックファイルを使用して新しい環境にプロジェクトを同期
WORKDIR /app
RUN uv sync --frozen
```

!!! important

    リポジトリ内の [`.dockerignore` ファイル](https://docs.docker.com/build/concepts/context/#dockerignore-files) に `.venv` を追加して、イメージビルドに含まれないようにすることがベストプラクティスです。プロジェクトの仮想環境はローカルプラットフォームに依存しており、イメージ内でゼロから作成する必要があります。

次に、デフォルトでアプリケーションを起動するには:

```dockerfile title="Dockerfile"
# プロジェクトが提供する `my_app` コマンドを前提としています
CMD ["uv", "run", "my_app"]
```

!!! tip

    依存関係のインストールとプロジェクト自体のインストールを分離する [中間レイヤー](#intermediate-layers) を使用して、Docker イメージのビルド時間を改善することがベストプラクティスです。

完全な例は [`uv-docker-example` プロジェクト](https://github.com/astral-sh/uv-docker-example/blob/main/Dockerfile) を参照してください。

### Using the environment

プロジェクトがインストールされたら、バイナリディレクトリをパスの先頭に配置してプロジェクトの仮想環境を _アクティブ化_ することができます:

```dockerfile title="Dockerfile"
ENV PATH="/app/.venv/bin:$PATH"
```

または、環境を必要とするコマンドに対して `uv run` を使用することができます:

```dockerfile title="Dockerfile"
RUN uv run some_script.py
```

!!! tip

    代わりに、[`UV_PROJECT_ENVIRONMENT` 設定](../../concepts/projects.md#configuring-the-project-environment-path) を同期前に設定して、システム Python 環境にインストールし、環境のアクティベーションを完全にスキップすることができます。

### Using installed tools

インストールされたツールを使用するには、[ツールのバインディレクトリ](../../concepts/tools.md#the-bin-directory) をパスに追加します:

```dockerfile title="Dockerfile"
ENV PATH=/root/.local/bin:$PATH
RUN uv tool install cowsay
```

```console
$ docker run -it $(docker build -q .) /bin/bash -c "cowsay -t hello"
  _____
| hello |
  =====
     \
      \
        ^__^
        (oo)\_______
        (__)\       )\/\
            ||----w |
            ||     ||
```

!!! note

    ツールのバインディレクトリの場所は、コンテナ内で `uv tool dir --bin` コマンドを実行して確認できます。

    代わりに、一定の場所に設定することもできます:

    ```dockerfile title="Dockerfile"
    ENV UV_TOOL_BIN_DIR=/opt/uv-bin/
    ```

### Installing Python in musl-based images

uv はイメージに利用可能な Python バージョンがない場合に[互換性のある Python バージョンをインストール](../install-python.md)しますが、uv はまだ musl ベースのディストリビューション用の Python インストールをサポートしていません。例えば、Python がインストールされていない Alpine Linux ベースのイメージを使用している場合、システムパッケージマネージャーで追加する必要があります:

```shell
apk add --no-cache python3~=3.12
```

## Developing in a container

開発時には、プロジェクトディレクトリをコンテナにマウントすることが有用です。このセットアップでは、プロジェクトの変更がイメージを再ビルドすることなくコンテナ化されたサービスに即座に反映されます。ただし、プロジェクトの仮想環境 (`.venv`) をマウントに含めないことが重要です。仮想環境はプラットフォーム固有であり、イメージ用にビルドされたものを保持する必要があります。

### Mounting the project with `docker run`

作業ディレクトリ内のプロジェクトを `/app` にバインドマウントし、[匿名ボリューム](https://docs.docker.com/engine/storage/#volumes) で `.venv` ディレクトリを保持します:

```console
$ docker run --rm --volume .:/app --volume /app/.venv [...]
```

!!! tip

    コンテナが終了したときにコンテナと匿名ボリュームがクリーンアップされるように、`--rm` フラグを含めます。

完全な例は [`uv-docker-example` プロジェクト](https://github.com/astral-sh/uv-docker-example/blob/main/run.sh) を参照してください。

### Configuring `watch` with `docker compose`

Docker compose を使用する場合、コンテナ開発のためのより高度なツールが利用可能です。[`watch`](https://docs.docker.com/compose/file-watch/#compose-watch-versus-bind-mounts) オプションは、バインドマウントよりも実用的な粒度でファイルの変更時にコンテナ化されたサービスの更新をトリガーすることができます。

!!! note

    この機能は Docker Desktop 4.24 にバンドルされている Compose 2.22.0 が必要です。

プロジェクトディレクトリを同期せずにマウントし、構成が変更されたときにイメージを再ビルドするために、[Docker compose ファイル](https://docs.docker.com/compose/compose-application-model/#the-compose-file) で `watch` を設定します:

```yaml title="compose.yaml"
services:
  example:
    build: .

    # ...

    develop:
      # アプリを更新するための `watch` 設定を作成
      #
      watch:
        # 作業ディレクトリをコンテナ内の `/app` ディレクトリと同期
        - action: sync
          path: .
          target: /app
          # プロジェクトの仮想環境を除外
          ignore:
            - .venv/

        # `pyproject.toml` の変更時にイメージを再ビルド
        - action: rebuild
          path: ./pyproject.toml
```

次に、`docker compose watch` を実行して開発セットアップでコンテナを実行します。

完全な例は [`uv-docker-example` プロジェクト](https://github.com/astral-sh/uv-docker-example/blob/main/compose.yml) を参照してください。

## Optimizations

### Compiling bytecode

Python ソースファイルをバイトコードにコンパイルすることは、通常、インストール時間の増加を犠牲にして起動時間を改善するため、プロダクションイメージにとって望ましいです。

バイトコードのコンパイルを有効にするには、`--compile-bytecode` フラグを使用します:

```dockerfile title="Dockerfile"
RUN uv sync --compile-bytecode
```

または、`UV_COMPILE_BYTECODE` 環境変数を設定して、Dockerfile 内のすべてのコマンドがバイトコードをコンパイルするようにします:

```dockerfile title="Dockerfile"
ENV UV_COMPILE_BYTECODE=1
```

### Caching

[キャッシュマウント](https://docs.docker.com/build/guide/mounts/#add-a-cache-mount) を使用して、ビルド間のパフォーマンスを向上させることができます:

```dockerfile title="Dockerfile"
ENV UV_LINK_MODE=copy

RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync
```

デフォルトの [`UV_LINK_MODE`](../../reference/settings.md#link-mode) を変更すると、キャッシュと同期ターゲットが別のファイルシステム上にあるため、ハードリンクを使用できないことに関する警告が抑制されます。

キャッシュをマウントしていない場合、`--no-cache` フラグを使用するか `UV_NO_CACHE` を設定して、イメージサイズを削減できます。

!!! note

    キャッシュディレクトリの場所は、コンテナ内で `uv cache dir` コマンドを実行して確認できます。

    代わりに、一定の場所に設定することもできます:

    ```dockerfile title="Dockerfile"
    ENV UV_CACHE_DIR=/opt/uv-cache/
    ```

### Intermediate layers

uv を使用してプロジェクトを管理している場合、依存関係のインストールを独自のレイヤーに移動することでビルド時間を改善できます。`--no-install` オプションを使用します。

`uv sync --no-install-project` はプロジェクトの依存関係をインストールしますが、プロジェクト自体はインストールしません。プロジェクトは頻繁に変更されますが、依存関係は一般的に静的であるため、これにより大幅な時間節約が可能です。

```dockerfile title="Dockerfile"
# uv をインストール
FROM python:3.12-slim
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# 作業ディレクトリを `app` ディレクトリに変更
WORKDIR /app

# 依存関係をインストール
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --frozen --no-install-project

# プロジェクトをイメージにコピー
ADD . /app

# プロジェクトを同期
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen
```

`pyproject.toml` はプロジェクトのルートと名前を識別するために必要ですが、プロジェクトの _内容_ は最終的な `uv sync` コマンドまでイメージにコピーされません。

!!! tip

    [ワークスペース](../../concepts/workspaces.md) を使用している場合は、`--no-install-workspace` フラグを使用して、プロジェクトおよびワークスペースメンバーを除外します。

    同期から特定のパッケージを除外する場合は、`--no-install-package <name>` を使用します。

### Non-editable installs

デフォルトでは、uv はプロジェクトおよびワークスペースメンバーを編集可能モードでインストールし、ソースコードの変更が環境に即座に反映されるようにします。

`uv sync` および `uv run` はどちらも `--no-editable` フラグを受け入れ、uv にプロジェクトを非編集可能モードでインストールするよう指示し、ソースコードへの依存を削除します。

マルチステージ Docker イメージのコンテキストでは、`--no-editable` を使用して、あるステージから同期された仮想環境にプロジェクトを含め、最終イメージには仮想環境のみ (ソースコードは含まない) をコピーすることができます。

例:

```dockerfile title="Dockerfile"
# uv をインストール
FROM python:3.12-slim AS builder
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# 作業ディレクトリを `app` ディレクトリに変更
WORKDIR /app

# 依存関係をインストール
RUN --mount=type=cache,target=/root/.cache/uv \
    --mount=type=bind,source=uv.lock,target=uv.lock \
    --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv sync --frozen --no-install-project --no-editable

# プロジェクトを中間イメージにコピー
ADD . /app

# プロジェクトを同期
RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen --no-editable

FROM python:3.12-slim

# 環境をコピー (ソースコードは含まない)
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# アプリケーションを実行
CMD ["/app/.venv/bin/hello"]
```

### Using uv temporarily

最終イメージで uv が不要な場合、各呼び出しでバイナリをマウントできます:

```dockerfile title="Dockerfile"
RUN --mount=from=ghcr.io/astral-sh/uv,source=/uv,target=/bin/uv \
    uv sync
```

## Using the pip interface

### Installing a package

コンテナはすでに隔離されているため、このコンテキストではシステム Python 環境を安全に使用できます。`--system` フラグを使用してシステム環境にインストールします:

```dockerfile title="Dockerfile"
RUN uv pip install --system ruff
```

システム Python 環境をデフォルトで使用するには、`UV_SYSTEM_PYTHON` 変数を設定します:

```dockerfile title="Dockerfile"
ENV UV_SYSTEM_PYTHON=1
```

代わりに、仮想環境を作成してアクティブ化することもできます:

```dockerfile title="Dockerfile"
RUN uv venv /opt/venv
# 仮想環境を自動的に使用
ENV VIRTUAL_ENV=/opt/venv
# 環境内のエントリポイントをパスの先頭に配置
ENV PATH="/opt/venv/bin:$PATH"
```

仮想環境を使用する場合、uv 呼び出しから `--system` フラグを省略する必要があります:

```dockerfile title="Dockerfile"
RUN uv pip install ruff
```

### Installing requirements

要件ファイルをインストールするには、コンテナにコピーします:

```dockerfile title="Dockerfile"
COPY requirements.txt .
RUN uv pip install -r requirements.txt
```

### Installing a project

要件と一緒にプロジェクトをインストールする場合、要件のコピーをソースコードの残りの部分から分離することがベストプラクティスです。これにより、プロジェクトの依存関係 (頻繁に変更されない) をプロジェクト自体 (非常に頻繁に変更される) とは別にキャッシュすることができます。

```dockerfile title="Dockerfile"
COPY pyproject.toml .
RUN uv pip install -r pyproject.toml
COPY . .
RUN uv pip install -e .
```
