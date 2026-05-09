# 概述

此存储库具有用于将 GitHub 存储库的源代码和发行版同步到 GitCode 的相应存储库的可重用工作流，您可在相关工作流中调用这些可重用工作流。

# 用法

## PushToGitCode.yaml

### 必须传入的参数

| 参数 | 说明 |
| --- | --- |
| `default_branch` | 要推送到 GitCode 的分支名称，若分支为 `master`，则可省略该参数 |
| `gitcode_username` | 您的 GitCode 用户名，用于推送到 GitCode 时验证身份 |
| `gitcode_repository` | 您的 GitCode 存储库路径，例如 `nvdacn/sync_to_gitcode`，若您设置了 `GITCODE_REPOSITORY` [存储库变量](https://docs.github.com/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository)或您的 GitCode 存储库路径与 GitHub 完全相同，则可省略该参数 |
| `GITCODE_TOKEN` | GitCode 的[个人访问令牌](https://gitcode.com/setting/token-classic)，应通过 [GitHub 机密](https://docs.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)传入该参数 |

### 调用示例

``` yaml
  gitcode_push:
    name: Push to GitCode
    uses: nvdacn/sync_to_gitcode/.github/workflows/PushToGitCode.yaml@master
    with:
      default_branch: main
      gitcode_username: <owner>
      gitcode_repository: <owner>/<repo>
    secrets:
      GITCODE_TOKEN: ${{ secrets.GITCODE_TOKEN }}
```

## CreateReleaseOnGitCode.yaml

### 必须传入的参数

| 参数 | 说明 |
| --- | --- |
| `needs` | 此处需填写必须的 job id，如 `build`，只有这些工作流成功运行后，才能运行后续步骤，必须添加处理要上传到 GitCode 的文件的 job id |
| `artifact_name` | 要下载的工件名称，可使用通配符，用于后续上传到 GitCode |
| `default_branch` | 要推送到 GitCode 的分支名称，若分支为 `master`，则可省略该参数 |
| `gitcode_repository` | 您的 GitCode 存储库路径，例如 nvdacn/sync_to_gitcode，若您设置了 `GITCODE_REPOSITORY` [存储库变量](https://docs.github.com/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository)或您的 GitCode 存储库路径与 GitHub 完全相同，则可省略该参数 |
| `tag_name` | GitCode 标签名称，推荐与 GitHub 相同 |
| `body` | 发布正文 |
| `prerelease` | 是否为预发布版本 |
| `file_name` | 要上传到 GitCode 的文件名，支持通配符，若该文件名可以匹配到多个文件，则会全部上传 |
| `GITCODE_TOKEN` | GitCode 的[个人访问令牌](https://gitcode.com/setting/token-classic)，应通过 [GitHub 机密](https://docs.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)传入该参数 |

### 调用示例

``` yaml
  gitcode_release:
    name: Create Release on GitCode
    needs: 
      - gitcode_push
    uses: nvdacn/sync_to_gitcode/.github/workflows/CreateReleaseOnGitCode.yaml@master
    with:
      artifact_name: <filename>
      gitcode_repository: <owner>/<repo>
      default_branch: main
      tag_name: ${{ github.ref_name }}
      body: <release_notes>
      prerelease: true
      file_name: <file_to_upload>
    secrets:
      GITCODE_TOKEN: ${{ secrets.GITCODE_TOKEN }}
```

