# 将 dsh bundle 发布到 npm

本仓库中的 `dotnet-skills` 包是 `README.md` 中描述的 DeepSeek Harness (dsh) bundle，
由 [`.github/workflows/publish-npm.yml`](../.github/workflows/publish-npm.yml) 在 GitHub
release 发布时自动发布，采用 [npm 可信发布](https://docs.npmjs.com/trusted-publishers)。
仓库和 GitHub secrets 中均不存储 npm token：工作流用 GitHub OIDC token 兑换短期发布
凭据，npm 会自动附加来源证明（provenance）。

## npmjs.com 上的一次性配置

只需做一次，且必须在 `flyneth/dotnet-skills` 仓库上进行。先把 `publish-npm.yml`
合并到默认分支：npm 只接受仓库中真实存在的工作流文件名，并且是在首次发布时校验
publisher 配置，而不是在保存设置时校验。

1. 以 `dotnet-skills` 包维护者的身份登录 npmjs.com。
2. 打开 **Packages → dotnet-skills → Settings → Trusted publishing**，选择
   **GitHub Actions**。以下字段均区分大小写，必须完全一致：
   - **Organization or user**：`flyneth`
   - **Repository**：`dotnet-skills`
   - **Workflow filename**：`publish-npm.yml` —— 只填文件名（含 `.yml` 扩展名），
     不要填路径。
   - **Environment name**：留空。工作流没有声明 environment。如果之后添加了保护
     规则，请在工作流 `publish` job 中加上相同的名称。
3. 保存后立即执行一次发布（见下文）。可信发布配置如果在 **2 天内**没有完成首次
   发布就会过期。
4. 首次 OIDC 发布成功后，打开 **Settings → Publishing access**，勾选 **Require
   two-factor authentication and disallow tokens**。可信发布不受影响；手工保存的
   写入 token 将全部失效。

## 发布清单

1. 将仓库根目录 `package.json` 中的 `version` 改为要发布的版本（例如 `0.2.5`），
   并将该变更合并到 `main`。
2. 基于该提交创建 GitHub release，tag 为 `v<version>` —— 版本 `0.2.5` 对应
   `v0.2.5`。发布草稿 release 也会触发工作流。
3. 观察 `publish-npm` 运行。它会校验 tag 与 `package.json` 是否一致、打包 bundle、
   把 tarball 安装到一个一次性项目中并检查注册的 skill 数量与本仓库发布的一致，
   然后发布，最后确认该版本可以从 registry 读取。

工作流是幂等的：如果该版本已在 registry 上，会直接成功退出而不重复发布，因此
部分失败后重新运行是安全的。release tag 与 `package.json` 不匹配时会直接失败，
而不是猜测处理。

预发布版本（带 `-` 的版本号，例如 `0.3.0-rc.1`）会以 `next` dist-tag 发布，
因此永远不会移动 `latest`。

## 重新执行发布

使用 **Actions → publish-npm → Run workflow**，输入 release tag（例如 `v0.2.5`）。
运行会发布该 tag 的 `package.json` 版本，或报告该版本已发布。

npm 不允许覆盖已发布的版本。如果已发布的版本有问题，将其弃用
（`npm deprecate dotnet-skills@0.2.5 "…"`)，然后按正常流程发布新的补丁版本。

## 故障排查

| 现象 | 原因 | 解决方法 |
| --- | --- | --- |
| `ENEEDAUTH`，或 npm 提示无法认证 | 可信发布字段与运行环境不匹配 | 逐字符比对 organization、repository 和 workflow filename；确认 job 保留了 `id-token: write` 并运行在 GitHub 托管 runner 上 |
| `npm <version> cannot authenticate with trusted publishing` | runner 的 npm 低于 11.5.1 / Node 低于 22.14.0 | 前移工作流中的 `node-version` |
| `Release tag 'vX' does not match package.json version 'Y'` | 版本号未合并，或 tag 错误 | 修改 `package.json`、合并、重新打 tag |
| `EPUBLISHCONFLICT` / `cannot publish over the previously published versions` | 该版本已存在 | 发布新版本；绝不要重复使用已有的 tag |
| 可信发布配置过期 | 创建后 2 天内没有成功发布 | 删除连接，重新创建，并在 2 天内完成发布 |
| 包页面没有 provenance 声明 | 仓库是私有的，或使用了 token 发布 | provenance 要求公开仓库加 OIDC 发布 |

## 重命名或转移仓库

有三处记录了仓库身份，必须同时修改：工作流中 `publish` job 的 `if:` 条件、
`package.json` 的 `repository.url` 字段（npm 要求它与 GitHub 仓库完全一致），以及
npmjs.com 上的可信发布连接。已有连接无法编辑，只能删除后重建。
