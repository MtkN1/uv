# uv インストーラーの構成

## インストールパスの変更

デフォルトでは、uv は `~/.local/bin` にインストールされます。`XDG_BIN_HOME` が設定されている場合、それが使用されます。同様に、`XDG_DATA_HOME` が設定されている場合、ターゲットディレクトリは `XDG_DATA_HOME/../bin` として推測されます。

インストールパスを変更するには、`UV_INSTALL_DIR` を使用します:

=== "macOSおよびLinux"

    ```console
    $ curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/custom/path" sh
    ```

=== "Windows"

    ```powershell
    $env:UV_INSTALL_DIR = "C:\Custom\Path" powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

## シェルの変更を無効にする

インストーラーは、uvバイナリが`PATH`に含まれるようにシェルプロファイルを更新することがあります。この動作を無効にするには、`INSTALLER_NO_MODIFY_PATH`を使用します。例えば:

```console
$ curl -LsSf https://astral.sh/uv/install.sh | env INSTALLER_NO_MODIFY_PATH=1 sh
```

`INSTALLER_NO_MODIFY_PATH`を使用してインストールした場合、`uv self update`のような後続の操作はシェルプロファイルを変更しません。

## 管理されていないインストール

CIのようなエフェメラル環境では、`UV_UNMANAGED_INSTALL`を使用して特定のパスにuvをインストールし、インストーラーがシェルプロファイルや環境変数を変更しないようにします:

```console
$ curl -LsSf https://astral.sh/uv/install.sh | env UV_UNMANAGED_INSTALL="/custom/path" sh
```

`UV_UNMANAGED_INSTALL`を使用すると、`uv self update`による自己更新も無効になります。

## インストールスクリプトにオプションを渡す

環境変数を使用することをお勧めします。これはプラットフォーム間で一貫しているためです。ただし、オプションを直接インストールスクリプトに渡すこともできます。例えば、利用可能なオプションを確認するには:

```console
$ curl -LsSf https://astral.sh/uv/install.sh | sh -s -- --help
```

