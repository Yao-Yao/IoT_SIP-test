# SIP over WAPI Security Protocol Implementation

## 项目概述

本项目是一个基于C语言实现的WAPI（无线局域网认证和隐私基础设施）安全协议，专为SIP（会话初始协议）视频监控系统设计。它提供了完整的设备认证、密钥协商、密钥分发和对等通信安全机制，支持IPC（网络摄像机）、SIP服务器、NVR（网络视频录像机）和客户端等多种设备类型。

项目实现了WAPI协议中的以下核心流程：
- 设备注册与身份认证
- 证书验证与数字签名
- ECDH密钥交换
- 单播密钥协商
- 对等密钥分发
- P2P认证令牌生成与验证

## 功能特性

- **多设备角色支持**：IPC、SIP服务器、NVR、客户端
- **完整的WAPI协议栈**：实现从认证激活到密钥分发的完整流程
- **基于证书的身份验证**：使用X.509证书进行设备身份验证
- **强密码学支持**：集成OpenSSL库，支持SHA256、HMAC-SHA256、ECDH、RSA签名等
- **安全的密钥管理**：支持主密钥、加密密钥、完整性密钥等多种密钥类型
- **可扩展的测试框架**：包含完整的端到端测试用例

## 文件结构

```
.
├── interface.h              # 主要头文件，定义数据结构、函数声明和协议常量
├── interface.c              # 核心实现文件，包含WAPI协议处理函数
├── test.h                   # 测试辅助函数声明
├── test.c                   # 主测试程序，模拟完整的协议流程
├── .cproject                # Eclipse CDT项目文件
├── .project                 # Eclipse项目文件
├── as20140710.tar           # 源代码归档（可能包含早期版本）
├── cert/                    # 证书目录
│   ├── usercert0.pem       # 用户0的证书
│   ├── usercert2.pem       # 用户2的证书
│   ├── usercert11111.pem   # 用户11111的证书
│   └── usercertuser2.pem   # 用户user2的证书
├── private/                 # 私钥目录
│   ├── userkey2.pem        # 用户2的私钥
│   ├── userkey11111.pem    # 用户11111的私钥
│   └── userkeyuser2.pem    # 用户user2的私钥
└── Debug/                   # 构建目录
    ├── makefile            # 主Makefile
    ├── objects.mk          # 对象文件定义
    ├── sources.mk          # 源文件定义
    ├── subdir.mk           # 子目录定义
    └── SIP-test            # 编译后的可执行文件
```

## 依赖

- **编译器**: GCC
- **加密库**: OpenSSL (1.0.2或更高版本)
- **构建工具**: GNU Make
- **系统**: Linux/Unix

## 构建说明

### 1. 安装依赖

确保系统已安装OpenSSL开发库：

```bash
# Ubuntu/Debian
sudo apt-get install libssl-dev

# CentOS/RHEL
sudo yum install openssl-devel
```

### 2. 编译项目

项目使用Eclipse CDT的生成Makefile系统。进入Debug目录并执行：

```bash
cd Debug
make all
```

这将编译生成可执行文件 `SIP-test`。

### 3. 清理构建

```bash
make clean
```

## 使用方式

### 运行测试程序

编译后，直接运行可执行文件：

```bash
./Debug/SIP-test
```

测试程序将模拟完整的WAPI协议流程，包括：
1. **注册阶段**：设备认证激活、访问认证请求、证书认证请求/响应
2. **密钥协商阶段**：单播密钥协商请求/响应/确认
3. **密钥分发阶段**：P2P密钥分发
4. **P2P通信阶段**：P2P认证令牌生成与验证

### 协议流程概述

1. **认证激活** (AuthActive): SIP服务器向SIP UA(NVR)发送认证激活包
2. **访问认证请求** (AccessAuthRequ): SIP UA(NVR)向SIP服务器发送访问认证请求
3. **证书认证请求** (CertificateAuthRequ): SIP服务器向Radius服务器发送证书认证请求
4. **证书认证响应** (CertificateAuthResp): Radius服务器返回认证结果
5. **访问认证响应** (AccessAuthResp): SIP服务器向SIP UA(NVR)返回认证结果
6. **单播密钥协商** (UnicastKeyNego): 设备间进行密钥协商
7. **P2P密钥分发** (P2PKeyDistribution): 服务器向设备分发对等通信密钥
8. **P2P认证令牌** (P2PAuthToken): 设备间生成和验证认证令牌

### 自定义配置

可以通过修改测试程序中的以下参数来适应不同环境：

- **设备ID**: 在`test.c`中修改`rc->self_id`和`rc->peer_id`
- **IP地址**: 修改`rc->peer_ip`
- **MAC地址**: 修改`rc->self_MACaddr`和`rc->peer_MACaddr`
- **证书和密钥**: 在`cert/`和`private/`目录中添加相应的PEM文件

## 测试

项目包含完整的端到端测试，覆盖所有协议阶段。测试程序通过模拟不同设备角色（IPC、SIP服务器、NVR）的交互来验证协议的正确性。

要添加新的测试用例，可以修改`test.c`文件，创建新的测试场景。

## 证书和密钥管理

项目使用PEM格式的X.509证书和RSA私钥进行身份验证和签名。证书和密钥文件存储在`cert/`和`private/`目录中。

### 现有证书/密钥对

| 用户ID | 证书文件 | 私钥文件 | 用途 |
|--------|----------|----------|------|
| 0 | `cert/usercert0.pem` | 未提供 | 测试用途 |
| 2 | `cert/usercert2.pem` | `private/userkey2.pem` | IPC设备 |
| 11111 | `cert/usercert11111.pem` | `private/userkey11111.pem` | SIP服务器 |
| user2 | `cert/usercertuser2.pem` | `private/userkeyuser2.pem` | NVR设备 |

### 生成新的证书/密钥对

可以使用OpenSSL工具生成新的证书和密钥：

```bash
# 生成私钥
openssl genrsa -out private/user_new.pem 2048

# 生成证书签名请求
openssl req -new -key private/user_new.pem -out user_new.csr

# 生成自签名证书（或由CA签名）
openssl x509 -req -days 365 -in user_new.csr -signkey private/user_new.pem -out cert/usercert_new.pem
```

## 开发说明

### 代码结构

- **interface.h**: 定义所有数据结构、常量和函数原型
  - 设备类型枚举 (DeviceType): IPC, SIPserver, NVR, Client
  - 连接状态枚举 (ConnectStatus)
  - 核心数据结构: RegisterContext, KeyRing, KeyBox, AuthActive, AccessAuthRequ等
  - 密码学函数声明: SHA256, HMAC-SHA256, 签名生成/验证等

- **interface.c**: 实现所有WAPI协议处理函数
  - 认证激活处理: `ProcessWAPIProtocolAuthActive`, `HandleWAPIProtocolAuthActive`
  - 访问认证请求处理: `ProcessWAPIProtocolAccessAuthRequest`, `HandleWAPIProtocolAccessAuthRequest`
  - 证书认证处理: `ProcessWAPIProtocolCertAuthRequest`, `talk_to_asu`
  - 密钥协商处理: `ProcessUnicastKeyNegoRequest`, `HandleUnicastKeyNegoRequest`等
  - 密钥分发处理: `ProcessP2PKeyDistribution`, `HandleP2PKeyDistribution`
  - P2P认证令牌处理: `ProcessP2PAuthToken`, `HandleP2PAuthToken`

- **test.c**: 主测试程序，模拟完整的协议交互流程

### 扩展协议

要添加新的协议消息或修改现有协议，需要：

1. 在`interface.h`中定义新的数据结构
2. 在`interface.c`中实现相应的处理函数
3. 在`test.c`中更新测试流程

## 故障排除

### 常见问题

1. **编译错误: openssl头文件找不到**
   - 确保已安装OpenSSL开发包
   - 检查OpenSSL库路径，可能需要修改Makefile中的`-L/usr/local/openssl/lib`

2. **运行时错误: 证书文件找不到**
   - 确保`cert/`和`private/`目录中存在相应的PEM文件
   - 检查文件权限，确保程序有读取权限

3. **签名验证失败**
   - 检查证书和私钥是否匹配
   - 验证证书格式是否正确（PEM格式）

4. **密钥协商失败**
   - 检查ECDH参数是否一致
   - 验证随机数生成是否正确

### 调试信息

程序包含详细的调试输出，可以通过修改`annotation`变量控制输出级别：

- `annotation = 0`: 最小输出
- `annotation = 1`: 中等输出
- `annotation = 2`: 详细输出

## 许可证

本项目许可证信息未明确指定。请参考项目文件或联系作者获取详细信息。

## 贡献

欢迎提交问题报告和功能请求。对于代码贡献，请遵循现有的代码风格和结构。

## 作者

项目由多个贡献者开发，包括yaoyao等。

## 版本历史

- **2014-07-10**: 创建项目归档 `as20140710.tar`
- **2014-04-22**: 初始版本，包含基本WAPI协议实现
- **持续更新**: 添加密钥协商、密钥分发、P2P通信等功能

---

*注意：本项目专为研究和教育目的设计，在实际生产环境中使用前应进行充分的安全评估和测试。*