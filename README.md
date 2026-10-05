# 基于 Spring Boot 的宠物医疗管理系统

本科毕业设计课题《基于 Spring Boot 的宠物医疗管理系统设计与实现》的实现仓库。

## 一、项目简介

面向中小型宠物医院，采用前后端分离的 B/S 架构，构建覆盖
**宠物档案 → 预约挂号 → 接诊诊疗 → 处方用药 → 药品库存 → 收费统计**
完整业务闭环的信息化管理系统，服务 **管理员、医生、宠物主人** 三类角色。

## 二、技术栈

| 层次 | 技术选型 |
| --- | --- |
| 前端 | Vue 3 + Element Plus + Vue Router + Pinia + Axios + Vite |
| 后端 | Spring Boot 3.x + Spring MVC + MyBatis-Plus |
| 认证授权 | JWT + 自定义拦截器（基于 RBAC） |
| 数据库 | MySQL 8.0 |
| 构建 | Maven（后端）、npm（前端） |
| 部署 | Nginx（前端）+ 可执行 Jar（后端） |

## 三、目录结构

```
.
├── backend/       Spring Boot 后端工程
├── frontend/      Vue 3 前端工程
├── database/      SQL 建表脚本、初始化数据
├── deploy/        部署配置（Nginx、启动脚本等）
├── scripts/       辅助工具脚本
├── docs/          全部过程文档与交付文档（见 03-文档策略.md）
│   ├── 00-项目规范/
│   ├── 01-开题/
│   ├── 02-需求分析/
│   ├── 03-系统设计/
│   ├── 04-测试/
│   └── 05-论文与答辩/
├── .gitignore
├── .gitattributes
└── README.md
```

## 四、分支说明

- `main`：稳定分支，存放可运行、可演示/可答辩的版本
- `dev`：集成分支，各功能分支合并到此联调
- `feature/*`、`fix/*`：功能与修复分支

详见 `docs/00-项目规范/02-Git策略.md`。

## 五、环境要求

- JDK 17
- Node.js 18+
- MySQL 8.0
- Maven 3.8+（或直接用项目自带的 Maven Wrapper `mvnw`，无需单独安装）

## 六、快速开始

> 工程尚未初始化，以下命令在后端/前端脚手架生成后生效。

后端：

```bash
cd backend
mvn spring-boot:run
```

前端：

```bash
cd frontend
npm install
npm run dev
```

数据库：执行 `database/` 下的建表脚本。

## 七、文档索引

- [文件策略](docs/00-项目规范/01-文件策略.md)
- [Git 策略](docs/00-项目规范/02-Git策略.md)
- [文档策略](docs/00-项目规范/03-文档策略.md)
- [开题报告](docs/01-开题/)
