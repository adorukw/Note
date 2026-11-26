# Arch Linux 下 Node / pnpm 的版本管理教程

> 结论先行：**不要用 `sudo npm i -g`**。全局包会装到 `/usr/lib/node_modules`，既需要 sudo，又会和 pacman 管理冲突，而且版本被锁死，换项目很麻烦。
>
> Arch 上的正确做法分两层：
>
> - **Node 本身**：用 `fnm`（或 `nvm`）管理多版本
> - **pnpm / yarn**：用项目里的 `packageManager` 字段 + `corepack` 管版本，或交给 fnm 当前 Node 版本隔离

---

## 1. 用 fnm 管理 Node 版本

`fnm` 在官方 `extra` 仓库，直接用 pacman 安装：

```bash
sudo pacman -S fnm
```

在 `~/.bashrc` 末尾添加：

```bash
eval "$(fnm env --use-on-cd --version-file-strategy recursive --shell bash)"
```

然后重新加载配置：

```bash
source ~/.bashrc
```

安装并固定一个 LTS 版本：

```bash
fnm install --lts
fnm default lts-latest
fnm list
node -v
```

### 常用命令

```bash
fnm install 22          # 安装某个大版本
fnm use 22              # 当前 shell 切换到 22
fnm default 22          # 设为默认版本
fnm list                # 查看已安装版本
```

### 项目级锁定 Node 版本

在项目根目录写一个 `.node-version`：

```bash
echo "22" > .node-version
```

因为配置了 `--use-on-cd`，`cd` 进目录会自动切到对应版本；版本没装会提示你 `fnm install`。`--version-file-strategy recursive` 让子目录也能找到上层的 `.node-version`。`.nvmrc` 同样默认支持。

> 关键点：**用了 fnm 就不要再 `sudo pacman -S nodejs npm`**。fnm 的 Node 放在自己的目录里，`node`、`npm` 都只在用户空间，不需要 sudo。

---

## 2. pnpm 的版本控制

`pnpm` 在 `extra` 仓库也有（`sudo pacman -S pnpm`），但那是系统级、跟着 pacman 走的版本。更推荐用 **corepack** 按项目管理。

在 fnm 安装的 Node 中执行：

```bash
corepack enable                 # 在 fnm 的 node bin 目录生成 pnpm/yarn 的 shim，无需 sudo
corepack use pnpm@latest        # 在项目里执行，自动写入 package.json
```

执行后 `package.json` 会多出一行，这就是版本锁：

```json
"packageManager": "pnpm@10.4.1+sha512...."
```

以后任何人（包括 CI）进入这个项目，corepack 都会自动使用这个 pnpm 版本。想在项目里换版本：

```bash
corepack use pnpm@10.4.1
```

> 注意：Node 25+ 开始不再内置 corepack。如果 `corepack` 命令不存在：
>
> ```bash
> sudo pacman -S corepack
> ```

### 更省事的替代方案

直接在 fnm 的 Node 下安装（**不加 sudo**）：

```bash
npm i -g pnpm
```

这样 pnpm 装进的是"当前这个 Node 版本"的目录，`fnm use 18` / `fnm use 22` 时各自隔离，不会污染系统，也不会出现权限报错。

---

## 3. 三种 Node 版本管理器对比

| 工具 | 仓库 | 特点 | 适合 |
|---|---|---|---|
| **fnm** | `extra` | Rust 编写，启动快，`--use-on-cd` 自动切版本 | 推荐，日常最省心 |
| **nvm** | `extra` | 老牌，shell 函数实现，稍慢 | 教程多，习惯 nvm 的人 |
| **volta** | AUR | 按项目自动切 node/npm/yarn | 团队协作固定工具链 |

nvm 也在官方仓库，不用去 GitHub 拉安装脚本：

```bash
sudo pacman -S nvm
```

在 `~/.bashrc` 里加载：

```bash
source /usr/share/nvm/init-nvm.sh
```

用法：

```bash
nvm install --lts
nvm use 22
```

---

## 4. 装完验证

```bash
node -v
npm -v
pnpm -v
which node          # 应指向 ~/.local/share/fnm/... 而不是 /usr/bin/node
```

再运行 `fastfetch`，`Node.js` 那行会从「未安装」变成版本号，`pnpm`、`npm` 同理。

---

## 附：常见问题

**Q：`fnm: command not found`？**
`~/.bashrc` 里的 `eval "$(fnm env ...)"` 没生效，确认已 `source ~/.bashrc` 或重新打开终端。

**Q：`which node` 还是 `/usr/bin/node`？**
说明系统里用 pacman 装过 nodejs，且 PATH 顺序不对。要么卸载系统 Node（`sudo pacman -Rns nodejs npm`），要么确保 fnm 的初始化在 PATH 设置的最后面执行。

**Q：每次 `cd` 都提示版本未安装？**
项目里的 `.node-version` 要求的版本没装，执行 `fnm install` 即可。

**Q：不想用 corepack，怎么锁 pnpm 版本？**
在 `package.json` 的 `engines` 里写 `"pnpm": ">=10"`，或团队约定用 `packageManager` 字段（推荐，corepack 能自动执行）。
