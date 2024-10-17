# Using uv with FastAPI

[FastAPI](https://github.com/fastapi/fastapi) は、モダンで高性能な Python ウェブフレームワークです。uv を使用して FastAPI プロジェクトを管理し、依存関係のインストール、環境の管理、FastAPI アプリケーションの実行などを行うことができます。

!!! note

    このガイドのソースコードは [uv-fastapi-example](https://github.com/astral-sh/uv-fastapi-example) リポジトリで確認できます。

## 既存の FastAPI プロジェクトの移行

例として、[FastAPI ドキュメント](https://fastapi.tiangolo.com/tutorial/bigger-applications/) に定義されているサンプルアプリケーションを考えてみましょう。このアプリケーションは次のように構成されています：

```plaintext
project
└── app
    ├── __init__.py
    ├── main.py
    ├── dependencies.py
    ├── routers
    │   ├── __init__.py
    │   ├── items.py
    │   └── users.py
    └── internal
        ├── __init__.py
        └── admin.py
```

このアプリケーションで uv を使用するには、`project` ディレクトリ内で次のコマンドを実行します：

```console
$ uv init --app
```

これにより、`pyproject.toml` ファイルを含む[アプリケーションプロジェクト](../../concepts/projects.md#applications)が作成されます。

次に、FastAPI の依存関係を追加します：

```console
$ uv add fastapi --extra standard
```

これで、次のような構成になります：

```plaintext
project
├── pyproject.toml
└── app
    ├── __init__.py
    ├── main.py
    ├── dependencies.py
    ├── routers
    │   ├── __init__.py
    │   ├── items.py
    │   └── users.py
    └── internal
        ├── __init__.py
        └── admin.py
```

そして、`pyproject.toml` ファイルの内容は次のようになります：

```toml title="pyproject.toml"
[project]
name = "uv-fastapi-example"
version = "0.1.0"
description = "FastAPI project"
readme = "README.md"
requires-python = ">=3.12"
dependencies = [
    "fastapi[standard]",
]
```

ここから、次のコマンドで FastAPI アプリケーションを実行できます：

```console
$ uv run fastapi dev
```

`uv run` はプロジェクトの依存関係を自動的に解決およびロックし（つまり、`pyproject.toml` の隣に `uv.lock` を作成）、仮想環境を作成し、その環境でコマンドを実行します。

http://127.0.0.1:8000/?token=jessica をウェブブラウザで開いてアプリをテストします。

## デプロイ

Docker を使用して FastAPI アプリケーションをデプロイするには、次の `Dockerfile` を使用できます：

```dockerfile title="Dockerfile"
FROM python:3.12-slim

# Install uv.
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Copy the application into the container.
COPY . /app

# Install the application dependencies.
WORKDIR /app
RUN uv sync --frozen --no-cache

# Run the application.
CMD ["/app/.venv/bin/fastapi", "run", "app/main.py", "--port", "80", "--host", "0.0.0.0"]
```

次のコマンドで Docker イメージをビルドします：

```console
$ docker build -t fastapi-app .
```

次のコマンドで Docker コンテナをローカルで実行します：

```console
$ docker run -p 8000:80 fastapi-app
```

ブラウザで http://127.0.0.1:8000/?token=jessica にアクセスして、アプリが正しく動作していることを確認します。

!!! tip

    uv を Docker と一緒に使用する方法の詳細については、[Docker ガイド](./docker.md)を参照してください。
