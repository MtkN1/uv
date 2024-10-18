# Using alternative package indexes

While uv uses the official Python Package Index (PyPI) by default, it also supports alternative
package indexes. Most alternative indexes require various forms of authentication, which requires
some initial setup.

!!! important

    Please read the documentation on [using multiple indexes](../../pip/compatibility.md#packages-that-exist-on-multiple-indexes)
    in uv — the default behavior is different from pip to prevent dependency confusion attacks, but
    this means that uv may not find the versions of a package as you'd expect.

## Azure Artifacts

uv can install packages from
[Azure DevOps Artifacts](https://learn.microsoft.com/en-us/azure/devops/artifacts/start-using-azure-artifacts?view=azure-devops&tabs=nuget%2Cnugetserver).
Authenticate to a feed using a
[Personal Access Token](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=Windows)
(PAT) or interactively using the [`keyring`](https://github.com/jaraco/keyring) package.

### Using a PAT

If there is a PAT available (eg
[`$(System.AccessToken)` in an Azure pipeline](https://learn.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#systemaccesstoken)),
credentials can be provided via the "Basic" HTTP authentication scheme. Include the PAT in the
password field of the URL. A username must be included as well, but can be any string.

例えば、トークンが `$ADO_PAT` 環境変数に保存されている場合、次のようにインデックス URL を設定します:

```console
$ export UV_EXTRA_INDEX_URL=https://dummy:$ADO_PAT@pkgs.dev.azure.com/{organisation}/{project}/_packaging/{feedName}/pypi/simple/
```

### `keyring` を使用する

PAT が利用できない場合は、[`keyring`](https://github.com/jaraco/keyring) パッケージと
[ `artifacts-keyring` プラグイン](https://github.com/Microsoft/artifacts-keyring) を使用して
Artifacts に認証します。これらの 2 つのパッケージは Azure Artifacts に認証するために必要であるため、
Artifacts 以外のソースから事前にインストールする必要があります。

`artifacts-keyring` プラグインは
[Azure Artifacts Credential Provider ツール](https://github.com/microsoft/artifacts-credprovider) をラップします。
このクレデンシャルプロバイダーは、対話型ログインを含むいくつかの異なる認証モードをサポートしています —
設定に関する情報は[ツールのドキュメント](https://github.com/microsoft/artifacts-credprovider) を参照してください。

uv は
[サブプロセスモード](https://github.com/astral-sh/uv/blob/main/PIP_COMPATIBILITY.md#registry-authentication) でのみ
`keyring` パッケージをサポートします。`keyring` 実行可能ファイルは `PATH` に存在する必要があります。
つまり、グローバルにインストールするか、アクティブな環境にインストールする必要があります。
`keyring` CLI は URL にユーザー名を必要とするため、インデックス URL にはデフォルトのユーザー名 `VssSessionToken` を含める必要があります。

```console
$ # 公開 PyPI から keyring と Artifacts プラグインを事前にインストールします
$ uv tool install keyring --with artifacts-keyring

$ # keyring 認証を有効にします
$ export UV_KEYRING_PROVIDER=subprocess

$ # ユーザー名でインデックス URL を設定します
$ export UV_EXTRA_INDEX_URL=https://VssSessionToken@pkgs.dev.azure.com/{organisation}/{project}/_packaging/{feedName}/pypi/simple/
```

## AWS CodeArtifact

uv は
[AWS CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/using-python.html) からパッケージをインストールできます。

認証トークンは `awscli` ツールを使用して取得できます。

!!! note

    このガイドは、AWS CLI が事前に認証されていることを前提としています。

まず、CodeArtifact リポジトリの定数を宣言します:

```bash
export AWS_DOMAIN="<your-domain>"
export AWS_ACCOUNT_ID="<your-account-id>"
export AWS_REGION="<your-region>"
export AWS_CODEARTIFACT_REPOSITORY="<your-repository>"
```

次に、`awscli` からトークンを取得します:

```bash
export AWS_CODEARTIFACT_TOKEN="$(
    aws codeartifact get-authorization-token \
    --domain $AWS_DOMAIN \
    --domain-owner $AWS_ACCOUNT_ID \
    --query authorizationToken \
    --output text
)"
```

そして、インデックス URL を設定します:

```bash
export UV_EXTRA_INDEX_URL="https://aws:${AWS_CODEARTIFACT_TOKEN}@${AWS_DOMAIN}-${AWS_ACCOUNT_ID}.d.codeartifact.${AWS_REGION}.amazonaws.com/pypi/${AWS_CODEARTIFACT_REPOSITORY}/simple/"
```

### パッケージの公開

独自のパッケージを AWS CodeArtifact に公開したい場合は、
[公開ガイド](../publish.md) に記載されているように `uv publish` を使用できます。
資格情報とは別に `UV_PUBLISH_URL` を設定する必要があります:

```bash
# Configure uv to use AWS CodeArtifact
export UV_PUBLISH_URL="https://${AWS_DOMAIN}-${AWS_ACCOUNT_ID}.d.codeartifact.${AWS_REGION}.amazonaws.com/pypi/${AWS_CODEARTIFACT_REPOSITORY}/"
export UV_PUBLISH_USERNAME=aws
export UV_PUBLISH_PASSWORD="$AWS_CODEARTIFACT_TOKEN"

# パッケージを公開します
uv publish
```

## その他のインデックス

uv は JFrog の Artifactory や Google Cloud Artifact Registry とも連携することが知られています。
