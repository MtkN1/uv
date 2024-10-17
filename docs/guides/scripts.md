# Running scripts

Pythonスクリプトは、`python <script>.py`でスタンドアロンで実行することを意図したファイルです。uvを使用してスクリプトを実行することで、スクリプトの依存関係が手動で環境を管理することなく管理されます。

!!! note

    Python環境に慣れていない場合：すべてのPythonインストールには、パッケージをインストールできる環境があります。通常、スクリプトごとに必要なパッケージを分離するために[_仮想_環境](https://docs.python.org/3/library/venv.html)を作成することが推奨されます。uvは仮想環境を自動的に管理し、依存関係に対して[宣言的](#declaring-script-dependencies)なアプローチを好みます。

## Running a script without dependencies

スクリプトに依存関係がない場合は、`uv run`を使用して実行できます：

```python title="example.py"
print("Hello world")
```

```console
$ uv run example.py
Hello world
```

<!-- TODO(zanieb): Once we have a `python` shim, note you can execute it with `python` here -->

同様に、スクリプトが標準ライブラリのモジュールに依存している場合、他に何もする必要はありません：

```python title="example.py"
import os

print(os.path.expanduser("~"))
```

```console
$ uv run example.py
/Users/astral
```

引数をスクリプトに提供することもできます：

```python title="example.py"
import sys

print(" ".join(sys.argv[1:]))
```

```console
$ uv run example.py test
test

$ uv run example.py hello world!
hello world!
```

さらに、スクリプトを直接標準入力から読み取ることもできます：

```console
$ echo 'print("hello world!")' | uv run -
```

または、シェルが[ヒアドキュメント](https://en.wikipedia.org/wiki/Here_document)をサポートしている場合：

```bash
uv run - <<EOF
print("hello world!")
EOF
```

`uv run`を_プロジェクト_（つまり、`pyproject.toml`を含むディレクトリ）で使用する場合、スクリプトを実行する前に現在のプロジェクトをインストールします。スクリプトがプロジェクトに依存していない場合は、`--no-project`フラグを使用してこれをスキップします：

```console
$ # フラグがスクリプトの_前_に来ることが重要です
$ uv run --no-project example.py
```

プロジェクトでの作業の詳細については、[プロジェクトガイド](./projects.md)を参照してください。

## Running a script with dependencies

スクリプトが他のパッケージを必要とする場合、それらはスクリプトが実行される環境にインストールする必要があります。uvはこれらの環境をオンデマンドで作成することを好み、手動で管理する長期間の仮想環境を使用する代わりに、スクリプトの依存関係を明示的に宣言することを要求します。一般的には、依存関係を宣言するために[プロジェクト](./projects.md)または[インラインメタデータ](#declaring-script-dependencies)を使用することが推奨されますが、uvは呼び出しごとに依存関係を要求することもサポートしています。

例えば、次のスクリプトは`rich`を必要とします。

```python title="example.py"
import time
from rich.progress import track

for i in track(range(20), description="For example:"):
    time.sleep(0.05)
```

依存関係を指定せずに実行すると、このスクリプトは失敗します：

```console
$ uv run --no-project example.py
Traceback (most recent call last):
  File "/Users/astral/example.py", line 2, in <module>
    from rich.progress import track
ModuleNotFoundError: No module named 'rich'
```

`--with`オプションを使用して依存関係を要求します：

```console
$ uv run --with rich example.py
For example: ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:01
```

特定のバージョンが必要な場合は、要求された依存関係に制約を追加できます：

```console
$ uv run --with 'rich>12,<13' example.py
```

複数の依存関係を要求するには、`--with`オプションを繰り返します。

`uv run`が_プロジェクト_で使用される場合、これらの依存関係はプロジェクトの依存関係に_追加_されます。この動作をオプトアウトするには、`--no-project`フラグを使用します。

## Creating a Python script

Pythonは最近、[インラインスクリプトメタデータ](https://packaging.python.org/en/latest/specifications/inline-script-metadata/#inline-script-metadata)の標準形式を追加しました。これにより、Pythonバージョンの選択と依存関係の定義が可能になります。`uv init --script`を使用して、インラインメタデータを持つスクリプトを初期化します：

```console
$ uv init --script example.py --python 3.12
```

## Declaring script dependencies

インラインメタデータ形式を使用すると、スクリプトの依存関係をスクリプト自体に宣言できます。

uvはインラインスクリプトメタデータの追加と更新をサポートしています。`uv add --script`を使用して、スクリプトの依存関係を宣言します：

```console
$ uv add --script example.py 'requests<3' 'rich'
```

これにより、スクリプトの先頭にTOMLを使用して依存関係を宣言する`script`セクションが追加されます：

```python title="example.py"
# /// script
# dependencies = [
#   "requests<3",
#   "rich",
# ]
# ///

import requests
from rich.pretty import pprint

resp = requests.get("https://peps.python.org/api/peps.json")
data = resp.json()
pprint([(k, v["title"]) for k, v in data.items()][:10])
```

uvはスクリプトを実行するために必要な依存関係を持つ環境を自動的に作成します。例えば：

```console
$ uv run example.py
[
│   ('1', 'PEP Purpose and Guidelines'),
│   ('2', 'Procedure for Adding New Modules'),
│   ('3', 'Guidelines for Handling Bug Reports'),
│   ('4', 'Deprecation of Standard Modules'),
│   ('5', 'Guidelines for Language Evolution'),
│   ('6', 'Bug Fix Releases'),
│   ('7', 'Style Guide for C Code'),
│   ('8', 'Style Guide for Python Code'),
│   ('9', 'Sample Plaintext PEP Template'),
│   ('10', 'Voting Guidelines')
]
```

!!! important

    インラインスクリプトメタデータを使用する場合、`uv run`が_プロジェクト_で使用されている場合でも、プロジェクトの依存関係は無視されます。`--no-project`フラグは必要ありません。

uvはPythonバージョンの要件も尊重します：

```python title="example.py"
# /// script
# requires-python = ">=3.12"
# dependencies = []
# ///

# Python 3.12で追加された構文を使用
type Point = tuple[float, float]
print(Point)
```

!!! note

    `dependencies`フィールドは空であっても提供する必要があります。

`uv run`は必要なPythonバージョンを検索して使用します。Pythonバージョンがインストールされていない場合はダウンロードされます。詳細については、[Pythonバージョン](../concepts/python-versions.md)のドキュメントを参照してください。

## Improving reproducibility

uvは、スクリプトメタデータの`tool.uv`セクションに`exclude-newer`フィールドをサポートしており、特定の日付より前にリリースされたディストリビューションのみを考慮するようにuvを制限します。これは、後でスクリプトを実行する際の再現性を向上させるために役立ちます。

日付は[RFC 3339](https://www.rfc-editor.org/rfc/rfc3339.html)タイムスタンプ（例：`2006-12-02T02:07:43Z`）として指定する必要があります。

```python title="example.py"
# /// script
# dependencies = [
#   "requests",
# ]
# [tool.uv]
# exclude-newer = "2023-10-16T00:00:00Z"
# ///

import requests

print(requests.__version__)
```

## Using different Python versions

uvは、スクリプトの呼び出しごとに任意のPythonバージョンを要求することを許可します。例えば：

```python title="example.py"
import sys

print(".".join(map(str, sys.version_info[:3])))
```

```console
$ # デフォルトのPythonバージョンを使用します。マシンによって異なる場合があります
$ uv run example.py
3.12.6
```

```console
$ # 特定のPythonバージョンを使用します
$ uv run --python 3.10 example.py
3.10.15
```

Pythonバージョンの要求の詳細については、[Pythonバージョンの要求](../concepts/python-versions.md#requesting-a-version)のドキュメントを参照してください。

## Using GUI scripts

Windowsでは、uvは`.pyw`拡張子で終わるスクリプトを`pythonw`を使用して実行します：

```python title="example.pyw"
from tkinter import Tk, ttk

root = Tk()
root.title("uv")
frm = ttk.Frame(root, padding=10)
frm.grid()
ttk.Label(frm, text="Hello World").grid(column=0, row=0)
root.mainloop()
```

```console
PS> uv run example.pyw
```

![Run Result](../assets/uv_gui_script_hello_world.png){: style="height:50px;width:150px"}

同様に、依存関係がある場合も動作します：

```python title="example_pyqt.pyw"
import sys
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QGridLayout

app = QApplication(sys.argv)
widget = QWidget()
grid = QGridLayout()

text_label = QLabel()
text_label.setText("Hello World!")
grid.addWidget(text_label)

widget.setLayout(grid)
widget.setGeometry(100, 100, 200, 50)
widget.setWindowTitle("uv")
widget.show()
sys.exit(app.exec_())
```

```console
PS> uv run --with PyQt5 example_pyqt.pyw
```

![Run Result](../assets/uv_gui_script_hello_world_pyqt.png){: style="height:50px;width:150px"}

## Next steps

`uv run`の詳細については、[コマンドリファレンス](../reference/cli.md#uv-run)を参照してください。

または、uvで[ツールを実行およびインストール](./tools.md)する方法を学ぶために読み進めてください。
