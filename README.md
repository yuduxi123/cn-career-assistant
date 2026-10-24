# 国内求职助手 · cn-career-assistant

版本：1.1.0。适用于支持 `SKILL.md` 的 AI 助手。帮助 AI 助手搜索国内公司招聘官网和招聘平台、区分日常实习/暑期实习/校招、核验资格、结合简历匹配，以及在用户授权后辅助填写申请。

## 安装

### 方式一：Git 安装（推荐）

需要 Python 3.9+ 和 Git。在终端执行：

```bash
git clone https://github.com/yuduxi123/cn-career-assistant.git
cd cn-career-assistant
python3 scripts/install.py
```

### 方式二：下载安装包

在 [Releases](https://github.com/yuduxi123/cn-career-assistant/releases/latest) 下载 `cn-career-assistant-v1.1.0.zip`，解压并进入包含 `SKILL.md` 的文件夹，再执行：

```bash
python3 scripts/install.py
```

Windows 可将 `python3` 换成 `py -3`。这两种方式均安装整个 Skill，保留所需参考文件与脚本；不需要安装额外 Python 库。

默认安装到 `$CODEX_HOME/skills/cn-career-assistant`；未设置 CODEX_HOME 时使用 `~/.codex/skills/cn-career-assistant`。重启或重新打开助手后使用。

### 其他助手与自定义目录

其他支持 SKILL.md 的宿主，请按其实际发现规则选择目录：

```bash
python3 scripts/install.py --target agents
python3 scripts/install.py --dest /path/to/your/skills
```

`--target agents` 使用 `~/.agents/skills`，不代表所有助手都会识别。安装器不会覆盖已有目录；升级前把旧目录移到技能目录之外备份，再安装。卸载只删除安装的 cn-career-assistant 目录，保留独立的求职工作台。

## 依赖与能力范围

- Python 3.9+；两个脚本只用标准库，无需 pip install。
- 宿主必须支持 Skill 加载；实时搜岗需要其网络搜索或浏览器能力，网申填写需要浏览器控制能力。
- 未捆绑 BOSS、牛客或公司官网爬虫，不保证每个平台可访问；登录/验证码由用户处理，不绕过访问限制。
- 不自带持续监控、通知服务或自动投递引擎；提醒需要另外配置调度器。applications.jsonl 是手动/助手维护的约定，没有配套管理 CLI。
- 只有本地存储和安装脚本经过测试，未做真实账号端到端投递测试。

## 怎么用

直接告诉助手：

> 使用 cn-career-assistant，帮我搜索深圳技术实习。我预计2027年7月毕业，2026年11月可到岗，每周5天，优先有转正机会。不要产品岗；请核验岗位是否仍在招，并附原始链接和待确认条件。

个性化匹配时提供你自己的简历和实际情况。不要将真实简历、证件、账号、申请记录放进这个分享包或公开仓库。搜索不代表授权投递；需要申请时明确公司、岗位和操作范围。

## 可选：保存岗位

先创建自己的工作台和 JSON 数组文件，如 jobs-input.json：

```json
[{"company":"示例公司","title":"Python开发实习","url":"https://example.com/jobs/1","types":["daily_internship"],"verified_at":"2026-10-24T10:00:00+08:00","status":"unverified"}]
```

示例不是实际在招职位。填写实际核验时间，不照抄。进入 Skill 目录运行：

```bash
python3 scripts/job_store.py --store ~/Documents/国内求职工作台/jobs.jsonl ingest --input jobs-input.json
python3 scripts/job_store.py --store ~/Documents/国内求职工作台/jobs.jsonl list --type daily_internship
python3 -m unittest discover -s tests -v
```

身份补全时保留一个已保存URL或同一官方公司ID+岗位ID作为关联依据。已有key不会变化，方便申请记录引用。详情字段与锁恢复方式见 references/discovery.md。仅适合本机单工作台，锁冲突会报错，不会自动重试。

## 分享

直接分享整个 ZIP，或将本目录作为独立 GitHub 仓库提交。不要只分享 SKILL.md，references/ 和 scripts/ 也需要保留。打包时排除真实用户数据、缓存与备份。文件名和版本本身不构成平台适配承诺。

本项目采用 [MIT 许可证](LICENSE)。本项目独立编写，参考来源见 [references/sources.md](references/sources.md)，未打包第三方项目源码；本项目许可证不适用于这些外部项目。

## 常见问题

**安装后找不到 Skill？** 检查安装路径是否属于当前助手的 Skill 搜索目录，然后重新打开助手。可以明确输入“使用 cn-career-assistant”。仅把仓库下载到任意目录不等于完成安装。

**能免费用吗？** Skill 与本地脚本免费；AI 助手、模型、联网工具或招聘平台可能有各自费用，本项目不收取服务费，也不要求单独配置模型 API Key。

**能自动投递吗？** 可通过宿主浏览器辅助填写。具体投递需要用户授权并核对资料，不会因搜岗就自动发送消息或提交简历。

**已经安装旧版本怎么办？** 把原安装目录移到 Skill 目录之外备份，再运行安装命令。真实求职数据应一直放在独立工作台，不放在安装目录。

**遇到问题如何反馈？** 在 [Issues](https://github.com/yuduxi123/cn-career-assistant/issues) 描述助手名称、操作系统、命令、去除隐私后的错误信息；不要上传真实简历、证件、Cookie 或 Token。

## 本地检查

```bash
python3 -m unittest discover -s tests -v
```

当前包含 7 项测试，验证本地岗位存储与安装行为。CI 在 Linux、macOS、Windows 运行测试；通过测试不表示招聘网站已完成适配。
