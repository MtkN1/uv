# uv のインストール

## インストール方法

uv をスタンドアロンインストーラーまたはお好みのパッケージマネージャーでインストールします。

### スタンドアロンインストーラー

uv は、uv をダウンロードしてインストールするためのスタンドアロンインストーラーを提供しています：

=== "macOS と Linux"

    ```console
    $ curl -LsSf https://astral.sh/uv/install.sh | sh
    ```

=== "Windows"

    ```console
    $ powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

特定のバージョンをリクエストするには、URL に含めます：

=== "macOS と Linux"

    ```console
    $ curl -LsSf https://astral.sh/uv/0.4.6/install.sh | sh
    ```

=== "Windows"

    ```console
    $ powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/0.4.6/install.ps1 | iex"
    ```

!!! tip

    インストールスクリプトは使用前に確認できます：

    === "macOS と Linux"

        ```console
        $ curl -LsSf https://astral.sh/uv/install.sh | less
        ```

    === "Windows"

        ```console
        $ powershell -c "irm https://astral.sh/uv/install.ps1 | more"
        ```

    また、インストーラーやバイナリを [GitHub](#github-releases) から直接ダウンロードすることもできます。

#### インストールの設定

デフォルトでは、uv は `~/.cargo/bin` にインストールされます。インストールパスを変更するには、
`UV_INSTALL_DIR` を使用します：

=== "macOS と Linux"

    ```console
    $ curl -LsSf https://astral.sh/uv/install.sh | env UV_INSTALL_DIR="/custom/path" sh
    ```

=== "Windows"

    ```powershell
    $env:UV_INSTALL_DIR = "C:\Custom\Path" powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
    ```

インストーラーはまた、uv バイナリが `PATH` に含まれるようにシェルプロファイルを更新します。この動作を無効にするには、`INSTALLER_NO_MODIFY_PATH` を使用します。例えば：

```console
$ curl -LsSf https://astral.sh/uv/install.sh | env INSTALLER_NO_MODIFY_PATH=1 sh
```

環境変数を使用することをお勧めします。これにより、プラットフォーム間で一貫性が保たれます。ただし、オプションをインストールスクリプトに直接渡すこともできます。例えば、利用可能なオプションを確認するには：

```console
$ curl -LsSf https://astral.sh/uv/install.sh | sh -s -- --help
```

CI などの一時的な環境では、`UV_UNMANAGED_INSTALL` を使用して特定のパスに uv をインストールし、インストーラーがシェルプロファイルや環境変数を変更するのを防ぎます:

```console
$ curl -LsSf https://astral.sh/uv/install.sh | env UV_UNMANAGED_INSTALL="/custom/path" sh
```

`UV_UNMANAGED_INSTALL` を使用すると、自己更新（`uv self update` 経由）も無効になります。

### PyPI

便宜上、uv は [PyPI](https://pypi.org/project/uv/) に公開されています。

PyPI からインストールする場合、uv を分離された環境にインストールすることをお勧めします。例えば、 `pipx` を使用します：

```console
$ pipx install uv
```

ただし、 `pip` も使用できます：

```console
$ pip install uv
```

!!! note

    uv は多くのプラットフォーム向けに事前にビルドされたディストリビューション（ホイール）を提供しています。特定のプラットフォーム向けのホイールが利用できない場合、uv はソースからビルドされます。これには Rust ツールチェーンが必要です。uv をソースからビルドする方法については、[貢献者向けセットアップガイド](https://github.com/astral-sh/uv/blob/main/CONTRIBUTING.md#setup) を参照してください。

### Cargo

uv は Cargo 経由で利用できますが、未公開のクレートに依存しているため、[crates.io](https://crates.io) ではなく Git からビルドする必要があります。

```console
$ cargo install --git https://github.com/astral-sh/uv uv
```

### Homebrew

uv は core Homebrew パッケージで利用できます。

```console
$ brew install uv
```

### Winget

uv は [winget](https://winstall.app/apps/astral-sh.uv) 経由で利用できます。

```console
$ winget install --id=astral-sh.uv  -e
```

### Docker

uv は
[`ghcr.io/astral-sh/uv`](https://github.com/astral-sh/uv/pkgs/container/uv) で Docker イメージを提供しています。

詳細については、[Docker で uv を使用する](../guides/integration/docker.md) ガイドを参照してください。

### GitHub リリース {#github-releases}

uv のリリースアーティファクトは
[GitHub リリース](https://github.com/astral-sh/uv/releases) から直接ダウンロードできます。

各リリースページには、すべてのサポートされているプラットフォーム向けのバイナリと、 `github.com` ではなく `astral.sh` を介してスタンドアロンインストーラーを使用するための手順が含まれています。

## uv のアップグレード

uv がスタンドアロンインストーラーを介してインストールされている場合、オンデマンドで自動更新できます：

```console
$ uv self update
```

!!! tip

    uv の更新はインストーラーを再実行し、シェルプロファイルを変更する可能性があります。この動作を無効にするには、 `INSTALLER_NO_MODIFY_PATH=1` を設定します。

他のインストール方法が使用されている場合、自動更新は無効になります。代わりにパッケージマネージャーのアップグレード方法を使用してください。例えば、 `pip` を使用する場合：

```console
$ pip install --upgrade uv
```

## シェルの自動補完

uv コマンドのシェル自動補完を有効にするには、次のいずれかを実行します：

=== "Linux と macOS"

    ```bash
    # シェルを確認し（例： `echo $SHELL` ）、次のいずれかを実行します：
    echo 'eval "$(uv generate-shell-completion bash)"' >> ~/.bashrc
    echo 'eval "$(uv generate-shell-completion zsh)"' >> ~/.zshrc
    echo 'uv generate-shell-completion fish | source' >> ~/.config/fish/config.fish
    echo 'eval (uv generate-shell-completion elvish | slurp)' >> ~/.elvish/rc.elv
    ```

=== "Windows"

    ```powershell
    Add-Content -Path $PROFILE -Value '(& uv generate-shell-completion powershell) | Out-String | Invoke-Expression'
    ```

uvx のシェル自動補完を有効にするには、次のいずれかを実行します：

=== "Linux と macOS"

    ```bash
    # シェルを確認し（例： `echo $SHELL` ）、次のいずれかを実行します：
    echo 'eval "$(uvx --generate-shell-completion bash)"' >> ~/.bashrc
    echo 'eval "$(uvx --generate-shell-completion zsh)"' >> ~/.zshrc
    echo 'uvx --generate-shell-completion fish | source' >> ~/.config/fish/config.fish
    echo 'eval (uvx --generate-shell-completion elvish | slurp)' >> ~/.elvish/rc.elv
    ```

=== "Windows"

    ```powershell
    Add-Content -Path $PROFILE -Value '(& uvx --generate-shell-completion powershell) | Out-String | Invoke-Expression'
    ```

その後、シェルを再起動するか、シェルの設定ファイルをソースにします。

## アンインストール

システムから uv を削除する必要がある場合は、 `uv` および `uvx` バイナリを削除します：

=== "macOS と Linux"

    ```console
    $ rm ~/.cargo/bin/uv ~/.cargo/bin/uvx
    ```

=== "Windows"

    ```powershell
    $ rm $HOME\.cargo\bin\uv.exe
    $ rm $HOME\.cargo\bin\uvx.exe
    ```

!!! tip

    uv が保存したデータを削除してからバイナリを削除することをお勧めします：

    ```console
    $ uv cache clean
    $ rm -r "$(uv python dir)"
    $ rm -r "$(uv tool dir)"
    ```

## 次のステップ

[最初のステップ](./first-steps.md) を確認するか、[ガイド](../guides/index.md) に直接ジャンプして uv の使用を開始してください。
