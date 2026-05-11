# 概述

此存储库具有用于将 GitHub 存储库的源代码和发行版同步到 GitCode 的相应存储库的[可重用工作流](https://docs.github.com/actions/using-workflows/reusing-workflows)，您可在相关工作流中调用这些可重用工作流。

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

```yaml
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

### 前置条件

调用此工作流时，请在 Job 级别使用 `needs` 关键字来确保前置任务（如构建或推送代码）已成功完成。例如：

```yaml
  gitcode_release:
    needs: [build, gitcode_push] # 在此处定义依赖
    uses: nvdacn/sync_to_gitcode/.github/workflows/CreateReleaseOnGitCode.yaml@master
    with:
      ...
```

### 必须传入的参数

| 参数 | 说明 |
| --- | --- |
| `artifact_name` | 要下载的 artifact 名称，支持多文件与通配符。准备上传到 GitCode 发行版的附件。应是此前在当前工作流中由[actions/upload-artifact](https://github.com/actions/upload-artifact)上传的文件 |
| `default_branch` | 要推送到 GitCode 的分支名称，若分支为 `master`，则可省略该参数 |
| `gitcode_repository` | 您的 GitCode 存储库路径，例如 `nvdacn/sync_to_gitcode`，若您设置了 `GITCODE_REPOSITORY` [存储库变量](https://docs.github.com/actions/learn-github-actions/variables#creating-configuration-variables-for-a-repository)或您的 GitCode 存储库路径与 GitHub 完全相同，则可省略该参数 |
| `tag_name` | GitCode 标签名称，推荐与 GitHub 相同 |
| `body` | 发布正文 |
| `prerelease` | 是否为预发布版本 |
| `file_name` | 从下载的 artifact 中筛选要上传到 GitCode 发行版的附件，支持通配符，若匹配到多个文件，则会全部上传 |
| `GITCODE_TOKEN` | GitCode 的[个人访问令牌](https://gitcode.com/setting/token-classic)，应通过 [GitHub 机密](https://docs.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets)传入该参数 |

### 调用示例

```yaml
  gitcode_release:
    name: Create Release on GitCode
    needs:
      - gitcode_push
    if: ${{ startsWith(github.ref, 'refs/tags/') }}
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
