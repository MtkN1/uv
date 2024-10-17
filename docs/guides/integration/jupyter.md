# Using uv with Jupyter

[Jupyter](https://jupyter.org/) ノートブックは、インタラクティブなコンピューティング、データ分析、および可視化のための人気のあるツールです。uv を使用して Jupyter をプロジェクトと対話するため、またはスタンドアロンツールとして使用するためのいくつかの方法があります。

## Using Jupyter within a project

プロジェクト内で作業している場合は、次のコマンドを使用してプロジェクトの仮想環境にアクセスできる Jupyter サーバーを起動できます。

```console
$ uv run --with jupyter jupyter lab
```

デフォルトでは、`jupyter lab` は [http://localhost:8888/lab](http://localhost:8888/lab) でサーバーを起動します。

ノートブック内では、プロジェクト内の他のファイルと同様にプロジェクトのモジュールをインポートできます。たとえば、プロジェクトが `requests` に依存している場合、`import requests` はプロジェクトの仮想環境から `requests` をインポートします。

プロジェクトの仮想環境への読み取り専用アクセスを探している場合は、それ以上の設定は必要ありません。ただし、ノートブック内から追加のパッケージをインストールする必要がある場合は、いくつかの追加の詳細を考慮する必要があります。

### Creating a kernel

ノートブック内からパッケージをインストールする必要がある場合は、プロジェクト専用のカーネルを作成することをお勧めします。カーネルを使用すると、Jupyter サーバーを 1 つの環境で実行し、個々のノートブックを別々の環境で実行できます。

uv のコンテキストでは、`uv run --with jupyter jupyter lab` のように Jupyter 自体を隔離された環境にインストールしながら、プロジェクト用のカーネルを作成できます。プロジェクト用のカーネルを作成することで、ノートブックが正しい環境に接続され、ノートブック内からインストールされたパッケージがプロジェクトの仮想環境にインストールされることが保証されます。

カーネルを作成するには、`ipykernel` を開発依存関係としてインストールする必要があります。

```console
$ uv add --dev ipykernel
```

次に、次のコマンドを使用して `project` 用のカーネルを作成できます。

```console
$ uv run ipython kernel install --user --name=project
```

その後、次のコマンドでサーバーを起動します。

```console
$ uv run --with jupyter jupyter lab
```

ノートブックを作成する際に、ドロップダウンから `project` カーネルを選択します。次に、`!uv add pydantic` を使用して `pydantic` をプロジェクトの依存関係に追加するか、`!uv pip install pydantic` を使用して `pydantic` をプロジェクトの仮想環境にインストールします。この場合、プロジェクトの `pyproject.toml` または `uv.lock` ファイルには変更が保存されません。どちらのコマンドも、ノートブック内で `import pydantic` を機能させます。

### Installing packages without a kernel

カーネルを作成したくない場合でも、ノートブック内からパッケージをインストールできます。ただし、いくつかの注意点があります。

`uv run --with jupyter` は隔離された環境で実行されますが、ノートブック内では `!uv add` および関連するコマンドはカーネルがなくてもプロジェクトの環境を変更します。

たとえば、ノートブック内で `!uv add pydantic` を実行すると、`pydantic` がプロジェクトの依存関係および仮想環境に追加され、追加の設定やサーバーの再起動なしで `import pydantic` がすぐに機能します。

ただし、Jupyter サーバーが「アクティブな」環境であるため、`!uv pip install` はプロジェクト環境ではなく _Jupyter_ 環境にパッケージをインストールします。これらの依存関係は Jupyter サーバーの存続期間中は持続しますが、後続の `jupyter` 呼び出しでは消える可能性があります。

pip に依存するノートブックを使用している場合（例：`%pip` マジックを使用）、Jupyter サーバーを起動する前に `uv venv --seed` を実行してプロジェクトの仮想環境に pip を含めることができます。たとえば、次のようにします。

```console
$ uv venv --seed
$ uv run --with jupyter jupyter lab
```

その後のノートブック内の `%pip install` 呼び出しは、プロジェクトの仮想環境にパッケージをインストールします。ただし、そのような変更はプロジェクトの `pyproject.toml` または `uv.lock` ファイルには反映されません。

## Using Jupyter as a standalone tool

ノートブックにアドホックでアクセスする必要がある場合（つまり、Python スニペットをインタラクティブに実行するため）、`uv tool run jupyter lab` を使用していつでも Jupyter サーバーを起動できます。これにより、隔離された環境で Jupyter サーバーが実行されます。

## Using Jupyter with a non-project environment

プロジェクトに関連付けられていない仮想環境で Jupyter を実行する必要がある場合（例：`pyproject.toml` または `uv.lock` がない）、次のコマンドを使用して環境に直接 Jupyter を追加できます。

```console
$ uv venv --seed
$ uv pip install pydantic
$ uv pip install jupyterlab
$ .venv/bin/jupyter lab
```

ここから、ノートブック内で `import pydantic` が機能し、`!uv pip install` または `!pip install` を使用して追加のパッケージをインストールできます。

## Using Jupyter from VS Code

VS Code などのエディタ内から Jupyter ノートブックと対話することもできます。uv 管理のプロジェクトを VS Code 内の Jupyter ノートブックに接続するには、次のようにプロジェクト用のカーネルを作成することをお勧めします。

```console
# Create a project.
$ uv init project
# Move into the project directory.
$ cd project
# Add ipykernel as a dev dependency.
$ uv add --dev ipykernel
# Open the project in VS Code.
$ code .
```

プロジェクトディレクトリが VS Code で開かれたら、コマンドパレットから「Create: New Jupyter Notebook」を選択して新しい Jupyter ノートブックを作成できます。カーネルの選択を求められたら、「Python Environments」を選択し、先ほど作成した仮想環境（例：`.venv/bin/python`）を選択します。

!!! note

    VS Code では、プロジェクト環境に `ipykernel` が存在する必要があります。`ipykernel` を開発依存関係として追加したくない場合は、`uv pip install ipykernel` を使用してプロジェクト環境に直接インストールできます。

ノートブック内からプロジェクトの環境を操作する必要がある場合は、`uv` を明示的な開発依存関係として追加する必要があるかもしれません。

```console
$ uv add --dev uv
```

その後、`!uv add pydantic` を使用して `pydantic` をプロジェクトの依存関係に追加するか、`!uv pip install pydantic` を使用して `pydantic` をプロジェクトの仮想環境にインストールします。この場合、プロジェクトの `pyproject.toml` または `uv.lock` ファイルには変更が保存されません。
