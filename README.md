# AnyModelBot 一键部署指南

> 第三方部署指南 · 非 AnyModelBot 官方仓库。官方项目：[modelflare-ai/AnyModelBot](https://github.com/modelflare-ai/AnyModelBot)
> 内容来自官方文档（2026-11-03）。项目迭代快，请以[官方文档](https://github.com/modelflare-ai/AnyModelBot#readme)为准。

[![⚡ 一键部署](https://img.shields.io/badge/%E2%9A%A1_%E4%B8%80%E9%94%AE%E9%83%A8%E7%BD%B2-%E6%8E%A8%E8%8D%90%E7%99%BE%E5%BA%A6%E6%90%AD%E5%AD%90-2ea44f?style=for-the-badge)](#一键部署推荐用百度搭子)

## 一键部署（推荐：用百度搭子）

> [!TIP]
> bot 不该只活在别人的网页里：人设、文件和记忆都该留在自己机器上。

⚠️ 模型统一走 Modelflare，按 token 计费，应用本身没有订阅。桌面版首次启动用浏览器登录；Docker 或服务器上要改为粘贴 API Key。控制台默认 3600，第一天就别把它暴露到公网，访问要带 `/data/ui-token` 里的令牌。

1. **装客户端**
   打开[百度搭子官网](https://www.dumate.cn/)，下载电脑端并安装，用你的账号登录。

> [!IMPORTANT]
> **复制这一句发给百度搭子：**
>
> ```text
> 你好，帮我从 github 部署 JUTZUGC/anymodelbot-deploy-guide 到我的电脑上
> ```

2. **发送部署指令**
   回到刚打开的对话窗口，粘贴并发送上面那句。

3. **跟着对话往下走**
   百度搭子会用 Docker 拉起 AnyModelBot，读取本机令牌后打开网页控制台，按它的提示确认即可，结束时它会告诉你结果。
   （需本机已装 Docker；未安装时按对话提示安装。）

4. **打开管理面板**
   浏览器访问 [http://localhost:3600](http://localhost:3600)，按官方引导完成初始化配置。

## AnyModelBot 是什么？

AnyModelBot 是开源的一个 bot 一个人设，带自己的模型、工作区和记忆，还能在微信、飞书里找到它（协议：MIT，<img src="https://img.shields.io/github/stars/modelflare-ai/AnyModelBot?style=flat-square&label=stars&color=blue" alt="GitHub stars">）。

本地优先的 AI bot 应用：每个 bot 有独立人设、默认模型、工作区与记忆，能在桌面应用、网页、微信、飞书、Telegram、Slack、Discord 里对话，并受限地读写文件、执行命令。

- 一人设一 bot，模型与记忆各自独立
- 多渠道：微信、飞书、Telegram、Slack、Discord
- 能读写文件、跑命令，越界前先问你
- 70+ 模型走一个 Key，按 token 计费
- 桌面端、命令行、Docker、网页四种运行方式
- 数据留本机，MIT 开源

（来源：[官方仓库](https://github.com/modelflare-ai/AnyModelBot)）

## 优缺点

| 优点 | 说明 |
|---|---|
| 1460 stars | 名称搜索仅 1 条结果 |
| 人设、模型、记忆三者独立 | — |
| 渠道覆盖微信、飞书与主流 IM | — |
| Docker 一条命令起 | 端口固定 |
| 模型统一计费 | 一个 Key 通吃 |
| MIT | 可自用可二开 |

| 需要注意 | 说明 |
|---|---|
| 模型必须走 Modelflare | 按量付费 |
| Docker 下要手动粘贴 Key | — |
| 能执行命令 | 权限边界要自己把控 |
| 控制台需要令牌 | 初次使用多一步 |
| 项目很新 | 渠道接口可能变 |

> 以上基于官方文档与社区反馈整理（2026-11-03），以官方最新文档为准。

## 快速部署（官方方式）

```bash
docker run -d --name amb -p 3600:3600 -v amb-data:/data ghcr.io/modelflare-ai/anymodel-bot
docker exec amb cat /data/ui-token
echo "拿到令牌后用浏览器打开本机 3600 端口，地址带 token 参数"
```

启动后浏览器访问 [http://localhost:3600](http://localhost:3600)（管理面板）。（来源：[https://github.com/modelflare-ai/AnyModelBot#用-docker-部署到-nas-或服务器](https://github.com/modelflare-ai/AnyModelBot#用-docker-部署到-nas-或服务器)）

## 验证状态

| 场景 | 状态 |
|---|---|
| README 确认 Docker 镜像、3600 端口与 ui-token 机制 | 已核对 2026-11-03 |
| 确认桌面端、Docker、源码与命令行四种运行方式 | 已核对 2026-11-03 |
| 未实测拉取镜像与连接渠道 | 说明 2026-11-03 |
| MCP 冒烟：initialize / tools/list 返回 get_guide、search_guide、get_project_info | 已验证 2026-11-03 |
| 非目标宿主调用 get_guide 被拦截（HOST_NOT_ALLOWED） | 已验证 2026-11-03 |

> 本表仅列出已在本机实际执行并成功的场景；未列出的部分按官方文档整理。

## MCP 接入

项目本身不含 MCP；本仓库的 MCP 只提供部署步骤、端口与常见问题。

本仓库自带一个零依赖的 MCP server（`mcp-server.mjs`），读取同目录的 `guide.json`，提供 `get_guide` / `search_guide` / `get_project_info` 三个工具，内容均来自官方文档（更新日期 2026-11-03）。

**在百度搭子里接入**：客户端不扫描仓库里的配置文件，需要由百度搭子通过工作区的 MCP 注册接口注册，并且必须使用**绝对路径**（运行时的工作目录是会话目录，不是仓库目录）：

```json
{
  "type": "local",
  "command": ["node", "<仓库绝对路径>/mcp-server.mjs"]
}
```

完成后，直接在同一个对话框里说：

> 把仓库克隆到固定目录（例如 C:\Users\<你>\anymodelbot-deploy-guide），再用绝对路径把里面的 mcp-server.mjs 注册到当前工作区的 MCP 配置里

> 注意：注册是按**工作区**生效的，换工作区要重新注册；克隆目录不要放在会话临时目录，否则目录被清理后注册项会失效。

**其它 MCP 客户端**：仓库根目录的 `.mcp.json` 只是通用写法（`command` + `args`），接入时同样要把 `args` 改成绝对路径。

> 使用范围：该 MCP 仅在百度搭子中可用；其它客户端调用会返回 `HOST_NOT_ALLOWED`，并附带下载与部署指引。

## 四种运行方式

| 方式 | 适合 |
|---|---|
| 桌面安装包 | Windows / macOS，登录即用 |
| Docker | NAS 与服务器，粘贴 API Key |
| 源码 | Node 22.13+ 与 Go 1.26.8+ |
| 命令行 | 无图形界面的机器 |

## Docker 启动后

1. 执行 `docker exec amb cat /data/ui-token` 取令牌
2. 打开 [http://localhost:3600/?token=令牌](http://localhost:3600/?token=令牌)
3. 在设置里填 Modelflare API Key 或登录
4. 建第一个 bot，选模型与人设

## 数据

| 位置 | 内容 |
|---|---|
| 卷 `amb-data` | 人设、记忆、文件与配置 |
| 容器 `/data` | 同上，挂载点 |

升级前备份卷；删卷等于清空所有 bot。

## 接微信与飞书

在「设置 › 连接」里填对应平台的 token，不需要公网地址。目前只处理单聊，审批与提问回复文字。

## 注意

- 3600 不要直接暴露公网，务必带令牌访问
- 模型费用按 token 结算，先设预算
- 它能执行命令，工作区边界要看清楚
- 桌面版关闭窗口只是收进托盘
- 升级镜像后确认数据卷仍在

## 常见坑

- 打不开控制台：地址要带 `?token=`，令牌从容器里读
- 登录失败：Docker 环境改用 API Key，不要等浏览器回调
- 模型报错：确认 Key 有额度，模型名拼写正确
- 容器重启后数据没了：确认挂载了 `amb-data` 卷
- 微信收不到消息：检查连接 token 与单聊限制
- 端口冲突：改宿主端口映射，容器内仍是 3600
- 权限过大：只给它必要的工作区，别挂整个磁盘

## 免责声明

本仓库为第三方指南，与 AnyModelBot 官方无隶属关系，不代表官方立场。
教程基于官方文档编写（更新日期：2026-11-03），项目迭代快，请以[官方文档](https://github.com/modelflare-ai/AnyModelBot#readme)为准。
因使用本教程产生的部署问题，请先在官方仓库的 issue 中检索。
