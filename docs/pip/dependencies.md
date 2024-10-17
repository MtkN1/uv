# Declaring dependencies

依存関係は、アドホックなインストールで環境を変更するのではなく、静的なファイルに宣言するのがベストプラクティスです。依存関係が定義されると、それらを [ロック](./compile.md) して、一貫性のある再現可能な環境を作成できます。

## Using `pyproject.toml`

`pyproject.toml` ファイルは、プロジェクトの構成を定義するための Python 標準です。

`pyproject.toml` ファイルでプロジェクトの依存関係を定義するには:

```toml title="pyproject.toml"
[project]
dependencies = [
  "httpx",
  "ruff>=0.3.0"
]
```

`pyproject.toml` ファイルでオプションの依存関係を定義するには:

```toml title="pyproject.toml"
[project.optional-dependencies]
cli = [
  "rich",
  "click",
]
```

各キーは「エクストラ」を定義しており、`--extra` および `--all-extras` フラグまたは `package[<extra>]` 構文を使用してインストールできます。詳細については、[パッケージのインストール](./packages.md#installing-packages-from-files) に関するドキュメントを参照してください。

`pyproject.toml` の使い方についての詳細は、公式の [`pyproject.toml` ガイド](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/) を参照してください。

## Using `requirements.in`

プロジェクトの依存関係を宣言するために、軽量な `requirements.txt` フォーマットを使用することも一般的です。各要件は独自の行で定義されます。通常、このファイルはロックされた依存関係に使用される `requirements.txt` と区別するために `requirements.in` と呼ばれます。

`requirements.in` ファイルで依存関係を定義するには:

```python title="requirements.in"
httpx
ruff>=0.3.0
```

この形式ではオプションの依存関係グループはサポートされていません。
