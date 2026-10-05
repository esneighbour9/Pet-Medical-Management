# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.

---
---

# 项目上下文

> 以下为本项目（本科毕业设计）的具体信息。改动这些约定前，请先更新 `docs/00-项目规范/` 下的对应文档。

## 项目概况

**课题**：基于 Spring Boot 的宠物医疗管理系统设计与实现（本科毕业设计 / 毕业论文 / 毕业答辩）
**目标**：面向中小型宠物医院，覆盖「宠物档案 → 预约挂号 → 接诊诊疗 → 处方用药 → 药品库存 → 收费统计」的完整业务闭环
**角色**：管理员 / 医生 / 宠物主人（三类，权限隔离）
**仓库根目录**：即本文件所在目录

## 技术栈

| 层次 | 选型 |
| --- | --- |
| 后端 | Java 17、Spring Boot 3.x、MyBatis-Plus 3.5.x |
| 认证授权 | **JWT + 自定义拦截器**（不使用 Spring Security，理由见下） |
| 数据库 | MySQL 8.0 |
| 前端 | Vue 3 + Element Plus + Vite + Pinia + Axios |
| 构建 | Maven（使用 Maven Wrapper `mvnw`，无需单独安装 Maven）、npm |
| 部署 | Nginx（前端）+ 可执行 Jar（后端） |

## 六个功能模块

| 编号 | 模块 | 主要内容 |
| --- | --- | --- |
| M1 | 用户与权限管理 | 注册登录、账号管理、RBAC、个人信息 |
| M2 | 宠物档案管理 | 档案增删改查、健康记录、历史就诊 |
| M3 | 预约挂号管理 | 号源查询、提交预约、确认/驳回、取消、排班 |
| M4 | 诊疗与电子病历 | 接诊登记、录入病历、开具处方、库存校验、病历查询 |
| M5 | 药品与库存管理 | 药品维护、入库、出库、库存预警 |
| M6 | 收费与统计管理 | 服务项目与价格、费用计算、收费结算、收入统计 |

**明确不做**（已从需求中移除，勿再添加）：公告与资讯、报表导出、短信/消息推送、爽约次数限制、病历留痕更正、用药禁忌自动提示。

## 目录结构

`backend/`（Spring Boot）`frontend/`（Vue 3）`database/`（SQL）`deploy/` `scripts/` `docs/`
详见 `docs/00-项目规范/01-文件策略.md`。

## 命名规范（摘要）

- **Java 包名** `com.petmedical`，分层：`common` `config` `security` `controller` `service` `mapper` `entity` `dto` `vo` `util`
- **数据库表前缀**：`sys_`（系统权限）`pet_`（宠物）`appt_`（预约）`med_`（诊疗处方）`stock_`（药品库存）`bill_`（收费）
- 所有表必备 `id`、`create_time`、`update_time`；逻辑删除字段 `deleted`
- 前端组件 `PascalCase.vue`，目录与路由 kebab-case
- 接口路径 `/api/<模块>/<资源>`

## 关键设计决策（不要擅自更改）

1. **金额一律以「分」为单位存储（整型）**，展示时再转换为两位小数，避免浮点误差
2. **预约防超订**：对「医生 + 时段」建立唯一约束，扣减号源时用乐观锁版本号，在事务内校验余量
3. **处方与库存扣减必须在同一事务内**，任一步失败整体回滚；库存字段设「不允许为负」约束兜底
4. **越权隔离**：宠物档案与就诊数据必须与主人账号绑定，后端在 Service 层强制附加「当前登录用户」过滤条件，**不信任前端传参**
5. **认证用 JWT + 拦截器，不用 Spring Security**：使用者 Spring Boot 经验有限，Spring Security 的配置复杂度性价比过低
6. 密码用 BCrypt 存储，任何情况下不得明文入库或写入日志

## 安全红线

- 数据库密码、JWT 密钥等**不写进被跟踪的文件**，一律放 `application-local.yml`（已被 `.gitignore` 拦截）
- 不提交 `target/`、`node_modules/`、`dist/`、IDE 配置、日志文件

## 工作方式

- **提交信息**：`type(scope): 中文描述`，type ∈ `feat` `fix` `docs` `style` `refactor` `test` `chore`
- **分支**：`main`（稳定，可答辩）/ `dev`（集成）/ `feature/*`（功能）
- 一个功能点提交一次；每次提交应能通过编译
- 改动目录结构或命名规范前，先更新 `docs/00-项目规范/`

## 当前进度

- ✅ 开题报告、项目脚手架与三大策略、需求分析（六模块 / 29 条功能需求）、开题答辩 PPT 与讲稿
- 🔄 开发环境搭建
- ⬜ 03-系统设计（数据库设计说明书、接口设计文档）
- ⬜ 后端骨架与六个模块实现
- ⬜ 前端实现
- ⬜ 毕业论文正文、毕业答辩材料

## 给 AI 助手的提示

**使用者的背景**：本科生，**Spring Boot 经验有限**。这一点会实质影响你应该怎么帮忙：

- **给代码的同时给解释**：不要只丢一段代码。说明关键代码在做什么、为什么这么做——这些解释同时是论文「系统实现」章节的素材，也是答辩时被追问的答案。
- **一次只推进一步**：不要一次性生成大量未验证的代码。按最小可运行单元推进，跑通了再往下。
- **解释要能支撑答辩**：使用者需要能讲清楚每一处关键设计，而不只是让代码跑起来。凡是「为什么这么写」讲不通的方案，宁可选更简单的。
- **优先保证能跑通、能讲清，其次才是功能多**。

## 相关文档

- `docs/00-项目规范/` — 文件策略、Git 策略、文档策略
- `docs/02-需求分析/需求规格说明书.md` — 功能需求与业务规则的权威来源
- `docs/01-开题/` — 开题报告、开题答辩 PPT 与讲稿

