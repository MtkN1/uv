# Inspecting environments

## Listing installed packages

環境内のすべてのパッケージをリストするには：

```console
$ uv pip list
```

パッケージを JSON 形式でリストするには：

```console
$ uv pip list --format json
```

環境内のすべてのパッケージを `requirements.txt` 形式でリストするには：

```console
$ uv pip freeze
```

## Inspecting a package

インストールされているパッケージに関する情報を表示するには、例：`numpy`：

```console
$ uv pip show numpy
```

複数のパッケージを一度に検査できます。

## Verifying an environment

複数のステップでインストールされた場合、環境に競合する要件を持つパッケージをインストールすることができます。

環境内の競合や欠落している依存関係を確認するには：

```console
$ uv pip check
```
