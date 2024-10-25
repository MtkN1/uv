# ビルドの失敗

このページでは、解決およびインストールがビルドエラーで失敗する一般的な理由とその修正方法を一覧にしています。

### uvがパッケージをビルドする理由

クロスプラットフォームのロックファイルを生成する際、uvはすべてのパッケージの依存関係を特定する必要があります。たとえ他のプラットフォームでのみインストールされるものであってもです。uvは解決中にパッケージのビルドを避けるように努めます。バージョンが存在する場合は任意のホイールを使用し、次にソースディストリビューションの静的メタデータ（主に静的な`project.version`、`project.dependencies`、`project.optional-dependencies`を含むpyproject.tomlまたは少なくともバージョン2.2のMETADATA）を探します。それらすべてが失敗した場合にのみ、パッケージをビルドします。

インストール時には、uvは各パッケージの現在のプラットフォーム用のホイールを持っている必要があります。インデックスに一致するホイールが存在しない場合、uvはソースディストリビューションをビルドしようとします。

PyPIプロジェクトの「ダウンロードファイル」セクションでどのホイールが存在するかを確認できます。例：https://pypi.org/project/numpy/2.1.1/#files。`...-py3-none-any.whl`ファイル名のホイールはどこでも動作し、他のホイールはファイル名にオペレーティングシステムとプラットフォームが含まれています。リンクされたnumpyバージョンでは、MacOS、Linux、およびWindows上のPython 3.10から3.13がサポートされていることがわかります。

### 修正と回避策

- ビルドエラーが欠落しているヘッダーやライブラリに言及している場合、システムパッケージマネージャーに一致するパッケージがあることがよくあります。

       例：Ubuntuで`uv pip install mysqlclient==2.2.4`が失敗する場合、MySQLヘッダーをインストールするために`sudo apt install default-libmysqlclient-dev build-essential pkg-config`を実行する必要があります（[https://pypi.org/project/mysqlclient/2.2.4/](https://pypi.org/project/mysqlclient/2.2.4/#Linux)）

- ビルドエラーが失敗するインポートに言及している場合、[ビルドの分離を無効にする](https://docs.astral.sh/uv/concepts/projects/#build-isolation)ことを検討してください。
- パッケージが解決中にビルドに失敗し、ビルドに失敗したバージョンが使用したいバージョンよりも古い場合は、下限を持つ[制約](https://docs.astral.sh/uv/reference/settings/#constraint-dependencies)を追加してみてください（例：`numpy>=1.17`）。アルゴリズムの制限により、uvリゾルバが不合理に古いパッケージを使用して適合するバージョンを見つけようとすることがあり、下限を使用することでこれを防ぐことができます。
- ロックおよび/またはインストールのために異なるPythonバージョンを使用することを検討してください（`-p`）。古いPythonバージョンを使用している場合、特に科学的なコードの場合、ネイティブコードを含む特定のパッケージの古いバージョンを使用する必要があるかもしれません。例：torch 1.12.0はPython 3.7から3.10をサポートしています（https://pypi.org/project/torch/1.12.0/#files）、numpy 2.1.0はPython 3.10から3.13をサポートしています（https://numpy.org/doc/stable/release/2.1.0-notes.html#numpy-2-1-0-release-notes）、したがって両方を一緒に使用するにはPython 3.10が必要です（またはtorchをアップグレードします）。
- サポートしていないプラットフォームからのパッケージのビルドが原因でロックが失敗する場合、サポートされているプラットフォームを使用して[リゾルバ環境を宣言する](https://docs.astral.sh/uv/reference/settings/#environments)ことを検討してください。
- 幅広いPythonバージョンをサポートしている場合、マーカーを使用して古いPythonバージョンには古いバージョンを使用し、新しいPythonバージョンには新しいバージョンを使用することを検討してください。例では、numpyは一度に4つのPythonマイナーバージョンをサポートする傾向があるため、Python 3.8から3.13をサポートするには、バージョンを分割する必要があります：

      ```
      numpy>=1.23; python_version >= "3.10"
      numpy<1.23; python_version < "3.10"
      ```

- 異なるプラットフォームからのパッケージのビルドが原因でロックが失敗する場合、エスケープハッチとして[依存関係メタデータを手動で提供する](https://docs.astral.sh/uv/reference/settings/#dependency-metadata)ことができます。uvはこの情報を検証できないため、このオーバーライドで正しいメタデータを指定することが重要です。
