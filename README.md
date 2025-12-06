# dataforge-platform
webook/  # 项目根目录（Go mod 模块名对应的根）
├── cmd/             # 程序入口目录（唯一入口，避免多main函数混乱）
│   └── api/         # 接口服务入口
│       └── main.go  # 程序启动入口（初始化路由、数据库、配置等）
├── internal/        # 项目私有核心业务逻辑（不对外暴露）
│   ├── handler/     # 路由处理器（接收请求、返回响应，调用service）
│   │   └── user_handler.go  # 用户相关接口处理（注册、登录接口）
│   ├── service/     # 业务逻辑层（实现核心业务规则，调用dao）
│   │   └── user_service.go  # 用户业务逻辑（校验参数、加密密码等）
│   ├── dao/         # 数据访问层（与数据库交互，基于GORM）
│   │   └── user_dao.go  # 用户数据操作（查询、新增用户等）
│   ├── model/       # 数据模型层（数据库表映射、请求/响应结构体）
│   │   ├── user.go  # 用户数据库模型（对应users表，GORM结构体）
│   │   └── request.go  # 入参结构体（如注册请求的username、password）
│   └── router/      # 路由注册（定义接口路径、绑定handler）
│       └── router.go  # 注册所有路由（如POST /api/user/register）
├── pkg/             # 公共工具包（可复用、无业务依赖的工具）
│   ├── db/          # 数据库工具（GORM初始化、连接配置）
│   ├── crypto/      # 加密工具（密码bcrypt加密、token生成等）
│   └── response/    # 响应工具（统一返回格式、错误码定义）
├── config/          # 配置文件目录（区分开发/生产环境）
│   ├── config.go    # 配置结构体（映射配置文件内容）
│   └── app.yaml     # 配置文件（数据库地址、端口、密钥等）
├── migrations/      # 数据库迁移目录（GORM迁移脚本，创建/更新表）
│   └── 2024xxxx_create_users_table.go  # 创建用户表的迁移文件
├── docs/            # 接口文档目录（如Swagger文档）
│   └── swagger.json # 自动生成的接口文档
├── tests/           # 单元测试/集成测试目录
│   └── user_service_test.go  # 用户业务逻辑测试
├── scripts/         # 辅助脚本（如启动脚本、部署脚本）
│   └── run.sh       # 快速启动程序的脚本
├── go.mod           # Go模块依赖管理（项目依赖清单）
└── go.sum           # 依赖校验文件

// MVC 
目录结构的核心是 “分层”，调用规则必须严格遵守，否则结构就失去意义：
允许调用：handler → service → dao（一层调用下一层）、各层均可调用 pkg/ 工具包、model/ 结构体。
禁止调用：handler 直接调用 dao（跳过业务层，导致逻辑混乱）、service 调用 handler（反向依赖）、internal/ 内部包被外部依赖（internal 目录的特性就是只能本项目使用，外部项目无法导入）。
