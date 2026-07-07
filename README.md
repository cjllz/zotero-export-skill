# zotero-export-skill
# Zotero 文献翻译 + 分类 Skill

## 这个 skill 是干嘛的

你搞科研的时候要读论文。论文是英文的，数量一多就乱。这个 skill 帮你：

1. **自动翻译** — 把论文标题和摘要翻成中文
2. **自动分类** — AI 读内容后打上领域标签（一氧化氮治疗、FeNO、肺功能...）
3. **自动管理** — 标记哪些翻过了、哪些没翻，一眼不混
4. **一键导出** — 翻译结果导出成 Markdown 或 Word 文件

**你只做两件事**：浏览器里点一下把论文存进 Zotero，然后跟 Agent 说「处理新文献」。剩下的全自动。

---

## 准备工作

### 1. 装 Zotero

如果还没有 Zotero：[https://www.zotero.org/download/](https://www.zotero.org/download/)

下载安装后打开，注册一个免费账号（右上角登录）。免费套餐够用。

### 2. 装 Zotero Connector（浏览器插件）

同一个下载页面，装对应浏览器的 Zotero Connector 扩展。

装完后浏览器右上角会多一个 Zotero 图标，看到论文网页点一下就能保存。

### 3. 开 Zotero 云同步

Zotero 桌面版右上角点那个弯箭头图标（Sync），登录刚才注册的账号。

同步图标变成绿色打勾说明好了。

### 4. 开 Zotero 本地 API

Zotero 菜单 → Edit → Settings → Advanced → 勾选「Allow local API access」。

这是因为脚本需要通过本地接口读取你的文献库。

### 5. 创建 Zotero API 密钥

打开 [https://www.zotero.org/settings/keys](https://www.zotero.org/settings/keys)，登录。

点 **Create New Private Key**：
- Key Description 随便写，比如「翻译脚本」
- 勾选 **Allow library access**
- 勾选 **Allow write access**
- 点 Save Key

页面会显示一串密钥和你的 User ID（一串数字）。**复制保存好**。

### 6. 安装这个 skill

如果你拿到的是压缩包：

```bash
# 解压
tar -xzf zotero-export-skill.tar.gz -C ~/.agents/skills/

# 装 Python 依赖
pip install python-docx pyyaml
```

### 7. 配置标签体系

打开（或创建）文件 `~/.zotero_tags.yaml`：

```yaml
# 你的研究领域标签，AI 只在你列出的标签里选。随时改
tags:
  - 一氧化氮治疗
  - FeNO/呼出气NO
  - 肺功能检测
  - 气道炎症
  - 哮喘/COPD
  - 临床研究
  - 综述/指南
  - 基础研究
  - 方法学
  - 其他
```

这个文件决定了 AI 会把你的论文分成哪些类别。**按你的研究方向改**，上面的只是例子。

### 8. 告诉 Agent 你的密钥

在终端运行（把尖括号里的内容换成你的）：

```bash
export DEEPSEEK_API_KEY="sk-你的DeepSeek密钥"
export ZOTERO_API_KEY="你的Zotero API密钥"
export ZOTERO_USER_ID="你的Zotero User ID（一串数字）"
```

这些是密钥，Agent 用它们来调翻译服务和操作 Zotero。

---

## 日常使用

### 怎么往 Zotero 里加论文

在浏览器里打开一篇论文的网页（比如 PubMed、arXiv、期刊官网），点浏览器右上角的 **Zotero Connector 图标**。论文自动保存到 Zotero 里。

Zotero 桌面版会自动同步看到这篇新论文。

### 怎么跟 Agent 说

Agent 就是你的 AI 助手，在终端里打字就行。不需要记命令。

| 你想干什么 | 跟 Agent 说（直接说人话） |
|-----------|------------------------|
| 看看现在文献库什么情况 | 「看一下我的 Zotero 文献总览」 |
| 把新论文处理了 | 「把新导入的论文翻译分类」 |
| 导出某个领域的文献 | 「把 FeNO 相关的文献导出成 Word」 |
| 每个领域各抽几篇 | 「每个领域各抽 3 篇导出」 |
| 给某篇打管理标签 | 「给 ABC123 这篇标记为精读」 |
| 分类不满意全重来 | 「重新分类所有文献」 |
| 某篇翻译不满意重翻 | 「重翻 ABC123 这篇」 |

Agent 会自动找到这个 skill 然后执行。你看到终端在跑就等着。

### 翻译完成后在哪看

**在 Zotero 里看标签：**

打开 Zotero 桌面版，左下角有个 **Tags** 面板。点 `FeNO` 只看 FeNO 的文献，点 `已翻译` 只看翻完的。

**导出的文件：**

翻译结果会生成两个文件在 `outputs/` 目录：

- `zotero_abstracts.md` — 用记事本或 Obsidian 打开
- `zotero_abstracts.docx` — 用 Word 打开

里面是标题（中英对照）+ 作者 + 年份 + 原文摘要 + 中文翻译。

---

## 标签体系说明

每篇文献上会同时出现多层标签，各管各的，不冲突：

### 第一层：生命周期标签（脚本自动打，你不用管）

| 标签 | 含义 |
|------|------|
| `待翻译` | 新导入的，脚本还没碰过 |
| `已翻译` | 脚本已经处理完了 |
| `本月翻译` | 这个月处理的，每月自动更新 |

### 第二层：领域标签（AI 自动打，按你的 `~/.zotero_tags.yaml` 来）

比如：`一氧化氮治疗` `FeNO/呼出气NO` `临床研究`

AI 读论文标题和摘要，在你配置的标签列表里选最合适的 1-3 个打上。

分类不对怎么办？两种方式改：
- Zotero 里直接改标签（右键文献 → Remove Tag / Add Tag）
- 跟 Agent 说「去掉 ABC123 的一氧化氮标签」

### 第三层：管理标签（你自己手打，想打才打）

| 标签 | 你自己决定什么时候加 |
|------|---------------------|
| `精读` | 这篇得仔细读 |
| `重要` | 重点引用文献 |
| `已读` | 已经读完了 |

跟 Agent 说「标记 ABC123 为精读」，或者直接在 Zotero 里操作。

---

## 在 Zotero 里怎么找文献

### 方法一：左下角标签面板（最常用）

Zotero 左下角有 Tags 面板，列出所有标签。点 `FeNO` → 只看 FeNO 领域的。点 `已翻译` → 只看翻完的。

选中多个标签（Ctrl+点）可以组合筛选，比如同时选 `一氧化氮治疗` + `精读`。

### 方法二：Saved Search（固定入口，建议建两个）

右键 Zotero 左侧 **My Library** → **New Saved Search...**：

| Name | 条件 |
|------|------|
| 📂 已翻译 | `Tags` → `is` → `已翻译` |
| 📂 待翻译 | `Tags` → `is not` → `已翻译` |

建完后左侧栏会多两个文件夹，点进去就行。

### 方法三：高级搜索（复杂条件）

Ctrl+Shift+F → 可以组合：`Tags is 一氧化氮治疗 AND Tags is 精读 AND Date Added in the last 30 days`

---

## 常见问题

**Q: 我改了 `~/.zotero_tags.yaml`，旧文献标签会变吗？**

不会自动变。想要重分类就 Agent 说「重新分类所有文献」。

**Q: AI 自动分类分错了怎么办？**

Zotero 里直接删掉错误标签、加正确的，或者 Agent 说「把 ABC123 改标签」。

**Q: 翻译质量不满意？**

Zotero 里找到那篇，删掉它的 `已翻译` 标签，下次 `--incremental` 自动重翻。

**Q: 某篇论文我不想翻译？**

在 Zotero 里给那篇打个标签叫 `跳过`。脚本会自动忽略它。

**Q: 不小心删了标签怎么办？**

跑一次 `--force --auto-tag` 全量重新处理。

**Q: Zotero 同步没反应？**

右上角点一下同步按钮（绿色弯箭头）。确保你的账号已登录。

**Q: 提示 "local API not reachable"？**

Zotero 没在运行，或者本地 API 没开。打开 Zotero，Settings → Advanced → 勾上 Allow local API。

---

## 文件位置一览

```
这个 skill 装在哪：
  ~/.agents/skills/zotero-export/
    ├── SKILL.md                ← Agent 靠这个文件发现 skill
    ├── README.md               ← 你在看的这个
    └── scripts/
        └── export_abstracts.py ← 核心脚本

你的配置文件（你自己维护）：
  ~/.zotero_tags.yaml           ← 领域标签分类体系

密钥（Agent 运行需要）：
  DEEPSEEK_API_KEY              ← DeepSeek 翻译用的
  ZOTERO_API_KEY                ← Zotero API 密钥
  ZOTERO_USER_ID                ← Zotero 用户 ID

导出结果：
  outputs/
    ├── zotero_abstracts.md
    └── zotero_abstracts.docx
```
