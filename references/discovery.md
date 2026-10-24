# 岗位发现与本地数据

## 搜索方法

1. 用公司名称加“招聘 官网 校园招聘”发现官方入口，核验官网跳转关系。不要维护未经验证的静态域名大全。
2. 针对目标公司分别搜“日常实习”“暑期实习/Summer Internship”“校招/招聘届别/春招/秋招”。年份以用户毕业时间及官方项目实际名称为准，不能简单把当前年当届别。
3. 打开列表与详情，核对分页、地点和届别筛选。浏览器可用时读取实际列表；公开接口只有确认允许且存在真实文档/页面证据时使用，不能猜接口或绕鉴权。
4. 对招聘平台补充检索公司和岗位关键词。平台企业名与法律实体可能不同，保留原值；岗位类似也不自动认为同一个职位。
5. 原页面受登录、验证码或访问限制时，请用户完成必要登录；其他公司和公开渠道继续查。连续相同失败最多一次正常重试，记录阻塞，不靠刷请求解决。
6. 先输出少量已核验的高相关岗位，再按用户范围扩展。每项注明抓取日期；下次更新重新核验状态。

## 字段

每个岗位是 JSON 对象：
- 必填：`company`、`title`、`url`、`verified_at`（带时区 ISO 时间）、`types`。
- `types` 数组允许值：`daily_internship`、`summer_internship`、`campus`、`unknown`。多类型须有原文证据；不要同时使用 unknown 和明确类型。
- 推荐：`source`、`source_url`、`official_url`、`official_company_id`、`job_id`、`department`、`cities`、`cohort`、`graduation_window`、`availability`、`duration_months`、`days_per_week`、`conversion_evidence`、`published_at`、`deadline`、`status`、`requirements`、`type_evidence`、`eligibility`、`grade`、`uncertainties`。
- `status`：`open`、`closed`、`expired`、`unverified`、`inaccessible`；空缺事实使用 null 或显式未知。

## 去重与更新

优先同官方公司标识+岗位ID；没有稳定标识时使用规范化详情URL。公司标识是官方招聘实体而非随意简称。跨平台没有共同官方ID或官方URL时只标记疑似重复，不自动合并。

脚本保留查询参数原始顺序，仅删除已知跟踪参数（utm_*、spm、gclid、fbclid），保留全部其他查询参数与 hash 路由。补充官网链接或官方ID时，保留原 `url` 或原 `official_url` 作为关联证据；脚本保留原key和历史链接别名，避免影响申请记录。仅当至少一个已保存URL或官方ID一致时更新同一记录；无法建立关联时不猜测合并。多个旧记录同时匹配或同URL的官方ID冲突时拒绝写入，人工核对。不要仅凭标题合并不同城市、部门或届别的岗位。

脚本保存 first_seen/last_seen、变更历史；输入中未提供的旧字段保留，显式 null 表示清空字段。核验时间较旧的数据不覆盖较新记录。搜索列表不见某岗不等于关闭；只有详情页或官方公告证据才能更新关闭状态。

## 申请记录

applications.jsonl 独立记录 job_key、公司、岗位ID、招聘项目、志愿顺序、阶段、简历版本、申请时间、官方申请编号、页面可见回执及下一步。个人证件、密码、验证码不入日志。截止日期未知不能擅自创建提醒；申请失败不自动重复提交。

写入采用独占锁目录和原子替换；同时写入会明确报锁定错误，可稍后重试。程序异常退出可能遗留 jobs.jsonl.lock，确认无写入进程后再手动删除。不要放在不保证本地原子文件操作的远程同步盘供多人并发写入。官方公司ID与岗位ID须使用非空字符串。
