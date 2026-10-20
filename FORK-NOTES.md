# 这个 fork 是什么

上游 [bowenliang123/dsh-context](https://github.com/bowenliang123/dsh-context) 本身已经声明支持 DSH 0.2.1（`engines.dsh: ">=0.1.7-rc.2 || >=0.2.0-rc.2"`），
npm 上的 `dsh-context@0.73.3` 在 0.2.1-rc.2 上能正常加载。

唯一的坑是 **从 GitHub 源码装**：仓库里没有提交构建产物（`lib/` 在 `.gitignore` 里），
`package.json` 的 `prepare` 是 `husky && tsdown`，而 pnpm 默认拒绝执行 git 依赖的构建脚本：

```
ERR_PNPM_GIT_DEP_PREPARE_NOT_ALLOWED  ... needs to execute build scripts but is not in the "allowBuilds" allowlist
```

本 fork 只做打包层面的修补（未改任何插件源码）：

- 提交上游 npm 包里的预构建产物 `lib/client.js`、`lib/index.js`、`lib/index.d.ts`；
- 删除 `prepare` 脚本（其余 scripts 保留），这样 tarball 安装时不会再触发构建；
- 版本号 `0.73.3` → `0.73.4`，`repository`/`homepage`/`bugs` 指向本 fork。

# 安装

    dsh plugin --profile desktop add https://github.com/puny-code/dsh-context/releases/download/v0.73.4/dsh-context-0.73.4.tgz

或在「插件 → 添加插件」里粘贴该 tgz 地址。

# 与上游同步

插件功能与上游 0.73.3 完全一致。上游发布新版本后，重新从 npm tarball 拷 `lib/` 即可：

    npm pack dsh-context@<ver> && tar -xzf dsh-context-<ver>.tgz
    cp package/lib/*.js package/lib/*.d.ts lib/

# 实测

在独立 profile（复制 `~/.dsh/profiles/web`）里以 tarball 安装，`dsh --profile <p> --port 0 --no-open` 启动：
无 stderr、无 `exceptions`/`consoleErrors`/`badRequests`；侧栏「上下文洞察」入口打开后，
Token 统计、DeepSeek 余额、会话列表、Context 面板均正常渲染。
