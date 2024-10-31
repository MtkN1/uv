# 代替パッケージインデックスの使用

uvはデフォルトで公式のPythonパッケージインデックス（PyPI）を使用しますが、代替パッケージインデックスもサポートしています。ほとんどの代替インデックスはさまざまな形式の認証を必要とし、初期設定が必要です。

!!! important

    uvで[複数のインデックスを使用する](../../pip/compatibility.md#packages-that-exist-on-multiple-indexes)ドキュメントを読んでください。デフォルトの動作は依存関係の混乱攻撃を防ぐためにpipとは異なりますが、これによりuvが期待通りにパッケージのバージョンを見つけられない場合があります。

## Azure Artifacts

uvは[Azure DevOps Artifacts](https://learn.microsoft.com/en-us/azure/devops/artifacts/start-using-azure-artifacts?view=azure-devops&tabs=nuget%2Cnugetserver)からパッケージをインストールできます。
[Personal Access Token](https://learn.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=Windows)（PAT）を使用してフィードに認証するか、[`keyring`](https://github.com/jaraco/keyring)パッケージを使用して対話的に認証します。

### PATの使用

PATが利用可能な場合（例：[Azureパイプラインの`$(System.AccessToken)`](https://learn.microsoft.com/en-us/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#systemaccesstoken)）、資格情報は「Basic」HTTP認証スキームを介して提供できます。URLのパスワードフィールドにPATを含めます。ユーザー名も含める必要がありますが、任意の文字列で構いません。

たとえば、トークンが`$ADO_PAT`環境変数に格納されている場合、次のようにインデックスURLを設定します：

```console
$ export UV_EXTRA_INDEX_URL=https://dummy:$ADO_PAT@pkgs.dev.azure.com/{organisation}/{project}/_packaging/{feedName}/pypi/simple/
```

### `keyring`の使用

PATが利用できない場合、[`keyring`](https://github.com/jaraco/keyring)パッケージと[the `artifacts-keyring` plugin](https://github.com/Microsoft/artifacts-keyring)を使用してArtifactsに認証します。これらの2つのパッケージはAzure Artifactsに認証するために必要であり、Artifacts以外のソースから事前にインストールする必要があります。

`artifacts-keyring`プラグインは[Azure Artifacts Credential Providerツール](https://github.com/microsoft/artifacts-credprovider)をラップします。資格情報プロバイダーは、対話的なログインを含むいくつかの異なる認証モードをサポートしています。設定に関する情報は[ツールのドキュメント](https://github.com/microsoft/artifacts-credprovider)を参照してください。

uvは[subprocessモード](https://github.com/astral-sh/uv/blob/main/PIP_COMPATIBILITY.md#registry-authentication)で`keyring`パッケージを使用することのみをサポートしています。`keyring`実行可能ファイルは`PATH`に含まれている必要があります。つまり、グローバルにインストールするか、アクティブな環境にインストールする必要があります。`keyring` CLIはURLにユーザー名を必要とするため、インデックスURLにはデフォルトのユーザー名`VssSessionToken`を含める必要があります。

```console
$ # 公開PyPIからkeyringとArtifactsプラグインを事前インストール
$ uv tool install keyring --with artifacts-keyring

$ # keyring認証を有効にする
$ export UV_KEYRING_PROVIDER=subprocess

$ # ユーザー名でインデックスURLを設定
$ export UV_EXTRA_INDEX_URL=https://VssSessionToken@pkgs.dev.azure.com/{organisation}/{project}/_packaging/{feedName}/pypi/simple/
```

## Google Artifact Registry

uv は [Google Artifact Registry](https://cloud.google.com/artifact-registry/docs) からパッケージをインストールできます。パスワード認証または [`keyring`](https://github.com/jaraco/keyring) パッケージを使用してリポジトリに認証します。

!!! note

    このガイドは、`gcloud` CLI が事前にインストールされ、設定されていることを前提としています。

### パスワード認証

資格情報は「Basic」HTTP 認証スキームを介して提供できます。URL のパスワードフィールドにアクセストークンを含めます。ユーザー名は `oauth2accesstoken` でなければなりません。そうでない場合、認証は失敗します。

たとえば、トークンが `$ARTIFACT_REGISTRY_TOKEN` 環境変数に格納されている場合、次のようにインデックス URL を設定します：

```bash
export ARTIFACT_REGISTRY_TOKEN=$(gcloud auth application-default print-access-token)
export UV_EXTRA_INDEX_URL=https://oauth2accesstoken:$ARTIFACT_REGISTRY_TOKEN@{region}-python.pkg.dev/{projectId}/{repositoryName}/simple
```

### `keyring` の使用

[`keyring`](https://github.com/jaraco/keyring) パッケージと [`keyrings.google-artifactregistry-auth` プラグイン](https://github.com/GoogleCloudPlatform/artifact-registry-python-tools) を使用して Artifact Registry に認証することもできます。これらの 2 つのパッケージは Artifact Registry に認証するために必要であり、Artifact Registry 以外のソースから事前にインストールする必要があります。

`artifacts-keyring` プラグインは [gcloud CLI](https://cloud.google.com/sdk/gcloud) をラップして短期間のアクセストークンを生成し、システムの keyring に安全に保存し、期限が切れたときに更新します。

uv は [subprocess モード](https://github.com/astral-sh/uv/blob/main/PIP_COMPATIBILITY.md#registry-authentication) で `keyring` パッケージを使用することのみをサポートしています。`keyring` 実行可能ファイルは `PATH` に含まれている必要があります。つまり、グローバルにインストールするか、アクティブな環境にインストールする必要があります。`keyring` CLI は URL にユーザー名を必要とし、それは `oauth2accesstoken` でなければなりません。

```bash
# 公開 PyPI から keyring と Artifact Registry プラグインを事前インストール
uv tool install keyring --with keyrings.google-artifactregistry-auth

# keyring 認証を有効にする
export UV_KEYRING_PROVIDER=subprocess

# ユーザー名でインデックス URL を設定
export UV_EXTRA_INDEX_URL=https://oauth2accesstoken@{region}-python.pkg.dev/{projectId}/{repositoryName}/simple
```

## AWS CodeArtifact

uvは[AWS CodeArtifact](https://docs.aws.amazon.com/codeartifact/latest/ug/using-python.html)からパッケージをインストールできます。

認証トークンは`awscli`ツールを使用して取得できます。

!!! note

    このガイドは、AWS CLIが事前に認証されていることを前提としています。

まず、CodeArtifactリポジトリの定数を宣言します：

```bash
export AWS_DOMAIN="<your-domain>"
export AWS_ACCOUNT_ID="<your-account-id>"
export AWS_REGION="<your-region>"
export AWS_CODEARTIFACT_REPOSITORY="<your-repository>"
```

次に、`awscli`からトークンを取得します：

```bash
export AWS_CODEARTIFACT_TOKEN="$(
    aws codeartifact get-authorization-token \
    --domain $AWS_DOMAIN \
    --domain-owner $AWS_ACCOUNT_ID \
    --query authorizationToken \
    --output text
)"
```

そして、インデックスURLを設定します：

```bash
export UV_EXTRA_INDEX_URL="https://aws:${AWS_CODEARTIFACT_TOKEN}@${AWS_DOMAIN}-${AWS_ACCOUNT_ID}.d.codeartifact.${AWS_REGION}.amazonaws.com/pypi/${AWS_CODEARTIFACT_REPOSITORY}/simple/"
```

### パッケージの公開

AWS CodeArtifactに独自のパッケージを公開する場合は、[公開ガイド](../publish.md)に記載されているように`uv publish`を使用できます。資格情報とは別に`UV_PUBLISH_URL`を設定する必要があります：

```bash
# uvをAWS CodeArtifactに設定
export UV_PUBLISH_URL="https://${AWS_DOMAIN}-${AWS_ACCOUNT_ID}.d.codeartifact.${AWS_REGION}.amazonaws.com/pypi/${AWS_CODEARTIFACT_REPOSITORY}/"
export UV_PUBLISH_USERNAME=aws
export UV_PUBLISH_PASSWORD="$AWS_CODEARTIFACT_TOKEN"

# パッケージを公開
uv publish
```

## その他のインデックス

uvは JFrog の Artifactory とも連携することが知られています。
