# 概述

此存储库具有用于将 GitHub 存储库的源代码和发行版同步到 GitCode 的相应存储库的可重用工作流，您可在相关工作流中调用这些可重用工作流。

# 用法

## PushToGitCode.yaml

```
  gitcode_push:
    name: Push to GitCode
    uses: nvdacn/sync_to_gitcode/.github/workflows/PushToGitCode.yaml@master
    with:
      default_branch:
      # 要推送到 GitCode 的分支名称
      gitcode_username: 
      # 您的 GitCode 用户名，用于推送到 GitCode 时验证身份
      gitcode_repository: 
      # 您的 GitCode 存储库路径，例如 nvdacn/sync_to_gitcode，若您设置了 GITCODE_REPOSITORY 存储库变量或您的 GitCode 存储库路径与 GitHub 完全相同，则可省略该参数
    secrets:
      GITCODE_TOKEN: 
      # GitCode 访问令牌
```

## CreateReleaseOnGitCode.yaml

```
  gitcode_release:
    name: Create Release on GitCode
    needs: 
      - gitcode_push
      # 此处需填写必须的 job id，如 build，只有这些工作流成功运行后，才能运行后续步骤
    uses: nvdacn/sync_to_gitcode/.github/workflows/CreateReleaseOnGitCode.yaml@master
    with:
      artifact_name: 
      # 要下载的工件名称，可使用通配符，用于后续上传到 GitCode
      gitcode_repository: 
      # 您的 GitCode 存储库路径，例如 nvdacn/sync_to_gitcode，若您设置了 GITCODE_REPOSITORY 存储库变量或您的 GitCode 存储库路径与 GitHub 完全相同，则可省略该参数
      tag_name: ${{ github.ref_name }}
      # GitCode 标签名称，推荐与 GitHub 相同
      prerelease: 
      # 是否为预发布版本
      body: 
      # 发布正文
      file_name: 
      # 要上传到 GitCode 的文件名，若该文件名可以匹配到多个文件，则会全部上传
    secrets:
      GITCODE_TOKEN: ${{ secrets.GITCODE_TOKEN }}
      # GitCode 访问令牌
```

