# dsh-feishu-mcp

飞书 / Lark 的 MCP server，25 个工具。**不需要公网 IP**：它走飞书长连接或 HTTP 回调拿事件，
你在任何一台能上网的机器上跑就行。

**主仓库在 [yinsang0910-star/dsh-feishu](https://github.com/yinsang0910-star/dsh-feishu)** ——
真源码、长连接机器人、卡片网关、四步安装向导都在那边。这个仓库是打包产物，
由主仓库的 `scripts/publish-mcp-repo.ps1` 单向同步生成，**请勿直接在此提交代码**。

## 跑

单文件自包含（SDK 和 core 都打进去了，不需要装依赖）：

```bash
npx github:yinsang0910-star/dsh-feishu-mcp
```

需要两个环境变量：

```bash
export FEISHU_APP_ID=cli_xxxxxxxx
export FEISHU_APP_SECRET=xxxxxxxx
```

## 挂到 DSH

```yaml
- insert:
    - id: mcp-feishu
      name: '@deepseek-ai/dsh-mcp-client'
      config:
        serverName: feishu
        transport: stdio
        command: node
        args: ['/绝对路径/dsh-feishu-mcp.mjs']
        toolCallTimeoutMs: 660000
```

写进 `~/.dsh/profiles/<profile>/cordis.patch.yml`，重启 DSH 后 agent 会多出
`mcp__feishu__*` 25 个工具。

## 25 个工具

| 组 | 工具 |
|---|---|
| 身份 | `feishu_whoami` `feishu_send_info_card` |
| 消息 | `feishu_send_text` `feishu_send_card` `feishu_update_card` `feishu_reply` |
| 文件 | `feishu_send_file` `feishu_send_image` `feishu_download` |
| 上下文 | `feishu_recent` `feishu_ask` |
| 会话 | `feishu_chats` `feishu_members` |
| 多维表格 | `feishu_bitable_tables` `feishu_bitable_fields` `feishu_bitable_records` `feishu_bitable_add` `feishu_bitable_update` `feishu_sheet_create` |
| 云文档 | `feishu_drive_list` `feishu_doc_read` `feishu_doc_create` `feishu_wiki_spaces` `feishu_wiki_node` `feishu_user_lookup` |

其中 `feishu_send_file` 是把本地文件真的发进会话（不是给个路径），
`feishu_update_card` 是让一张进度卡片原地刷新，`feishu_ask` 会阻塞到有人
点卡片按钮或打字回复为止。

## 权限

应用要开的消息类 scope：`im:message` `im:message:send_as_bot` `im:chat:readonly`
`im:resource`；云文档类：`drive:drive` `docx:document` `wiki:wiki:readonly`
`bitable:app`。漏开时工具会直接告诉你缺哪个。

## License

MIT