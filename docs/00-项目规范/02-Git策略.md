# Git 策略

> 适用范围：本仓库的版本控制与协作方式。单人开发，但流程保持规范，
> 便于答辩时体现工程素养，也方便后期回滚与溯源。
> 版本：v1.0　生效日期：2026-10-05

## 一、仓库与远端

- 模式：**单仓多模块**（backend / frontend / database / docs 同一仓库）。
- 仓库根：项目根目录（即当前文件夹）。
- 远端：`origin → https://github.com/esneighbour9/Pet-Medical-Management.git`
- 默认分支：`main`。

## 二、分支模型（简化流程）

```
main   ●────────────●────────────●──────►  稳定/可答辩
        ╲          ╱ ╲          ╱
dev      ●───●────●   ●───●────●           集成/联调
          ╲ ╱          ╲ ╱
feature/x  ●            ●                   单个功能
```

| 分支 | 用途 | 规则 |
| --- | --- | --- |
| `main` | 稳定分支，存放可运行、可演示的版本 | 只接受来自 `dev` 的合并；每个里程碑打 tag |
| `dev` | 集成分支，功能联调 | 功能分支合并到此；阶段验收后再并入 `main` |
| `feature/*` | 单个功能开发 | 从 `dev` 切出，完成后合回 `dev` |
| `fix/*` | 缺陷修复 | 从 `dev`（紧急则从 `main`）切出 |

### 分支命名

`feature/模块名`、`fix/问题简述`，全小写、连字符分隔。

示例：`feature/user-auth`、`feature/appointment`、`feature/pet-record`、`fix/login-500`。

## 三、提交信息规范

采用 Conventional Commits 精简版：**`type(scope): 中文描述`**。

| type | 含义 |
| --- | --- |
| `feat` | 新增功能 |
| `fix` | 修复缺陷 |
| `docs` | 文档变更 |
| `style` | 格式调整（不影响逻辑） |
| `refactor` | 重构（不改功能） |
| `test` | 测试相关 |
| `chore` | 构建、依赖、配置等杂项 |

`scope` 建议取值：`backend`、`frontend`、`database`、`docs`、`规范`。

示例：

```
feat(backend): 新增预约挂号创建接口
feat(frontend): 完成宠物档案列表页
fix(backend): 修复 JWT 过期后仍可访问的问题
docs(规范): 初始化文件/Git/文档三大策略
chore(backend): 引入 MyBatis-Plus 依赖
```

## 四、提交粒度与频率

- **一个功能点一次提交**，不把多个不相关改动塞进同一提交。
- 每次提交都应能通过编译（后端）/不报错（前端）。
- 阶段性节点（模块完成、联调通过、论文定稿）务必提交并打 tag。
- 提交前先 `git status` 确认没有误提交 `target/`、`node_modules/` 或本地配置。

## 五、合并流程

功能开发：

```bash
git checkout dev
git pull origin dev            # 单人开发可省略
git checkout -b feature/pet-record
# ……开发……
git add .
git commit -m "feat(backend): 新增宠物档案增删改查接口"
git checkout dev
git merge feature/pet-record   # 或 git merge --no-ff
git branch -d feature/pet-record
git push origin dev
```

阶段验收（并入 main）：

```bash
git checkout main
git merge dev
git push origin main
```

## 六、Tag 与里程碑

在 `main` 上为关键节点打带注释的标签：

| tag | 含义 |
| --- | --- |
| `v0.1` | 脚手架与三大策略就位（本次） |
| `v0.2` | 需求分析与系统设计完成 |
| `v0.5` | 核心模块开发完成 |
| `v1.0` | 系统功能完整、可演示 |
| `v1.0-答辩` | 答辩前定稿 |

```bash
git tag -a v0.1 -m "脚手架与项目规范就位"
git push origin v0.1
```

## 七、.gitignore 要点

已在根目录配置，强制忽略：`target/`、`node_modules/`、`dist/`、
`.idea/`、`.vscode/`、`application-local.yml`、`*.log`、密钥证书等。
**任何含密码或密钥的文件不得入库**；本地配置请用 `application-local.yml`。

## 八、日常操作速查

```bash
git status                     # 看当前状态
git checkout -b feature/xxx    # 切功能分支
git add . && git commit -m "..."  # 提交
git checkout dev && git merge feature/xxx   # 合并回 dev
git log --oneline --graph --all   # 查看分支图
```
