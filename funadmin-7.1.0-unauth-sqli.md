# FunAdmin 7.1.0 前台未授权 SQL 注入

用于 CVE / 漏洞库提交的公开描述。不含利用代码、请求样本或复现步骤。

## 基本信息

| 项 | 内容 |
| --- | --- |
| 漏洞名称 | FunAdmin 前台未授权 SQL 注入 |
| 漏洞类型 | SQL Injection |
| CWE | CWE-89 Improper Neutralization of Special Elements used in an SQL Command |
| 影响对象 | Web 应用 |
| 是否组件漏洞 | 否 |
| 认证要求 | 无需登录 |
| 攻击向量 | 远程 |
| 影响 | 数据库敏感信息泄露（机密性） |

## 厂商与产品

- 厂商：苏州泛爱软件科技有限公司
- 官网：http://fanhantech.com/
- 产品：FunAdmin
- 受影响版本：7.1.0
- 项目地址：https://github.com/funadmin/funadmin

## CVE 描述（英文，适合 MITRE / CNA）

FunAdmin 7.1.0 contains an unauthenticated SQL injection vulnerability in the frontend AJAX list interface. User-controlled field selection input is incorporated into a SQL SELECT clause without sufficient neutralization. A remote attacker without credentials can cause the application to evaluate attacker-supplied SQL expressions and return database contents, resulting in disclosure of sensitive information.

## 漏洞描述（中文，适合国内漏洞库）

FunAdmin 7.1.0 前台 Ajax 列表接口存在未授权 SQL 注入。应用将用户可控的字段选择参数拼入 SQL 查询的 SELECT 子句，且未对标识符做充分校验与转义。未持有任何凭证的远程请求者即可让服务端执行其提供的 SQL 表达式，并在接口响应中获得查询结果，从而读取数据库中的敏感数据。

## 根因（公开级别）

列表查询逻辑把调用方传入的字段名直接用于构造 SELECT 列。当该输入不是合法列名、而是可被数据库引擎求值的表达式时，表达式会作为查询的一部分执行，结果随 JSON 列表接口返回。该接口位于前台，不要求后台会话。

## 影响评估

- 机密性：高。可读取库内业务数据与账号相关记录。
- 完整性：中。同一注入面在可写场景下可能被用于修改数据。
- 可用性：低到中。恶意表达式可能增加数据库负载。
- 整体：高。前台未授权、结果回显，属于高危 SQL 注入。

建议 CVSS v3.1 向量（待 CVE 编号分配后由 CNA 核定）：

`CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:L/A:L`

基础分约 8.6（High）。

## 受影响范围

已知受影响发布版本为 FunAdmin 7.1.0。凡部署该版本且前台应用可被网络访问的站点，均可能受同一缺陷影响。

## 修复建议

厂商侧：

1. 对列表接口的字段选择参数实施严格白名单，仅允许预定义列名。
2. 禁止将用户输入作为 SQL 表达式或原始 SELECT 片段。
3. 前台匿名接口默认关闭动态字段选择，或与后台授权查询分离。
4. 发布安全更新，并在发行说明中标明受影响版本与修复版本。

运营侧临时措施：

1. 限制或关闭前台 Ajax 列表中的动态字段选择能力。
2. 在 WAF / 网关对异常字段选择参数做拦截。
3. 审计数据库账号权限，避免 Web 进程使用高权限库账号。
4. 关注厂商补丁并尽快升级。

## 参考

- 产品仓库：https://github.com/funadmin/funadmin
- 厂商站点：http://fanhantech.com/
- CWE-89：https://cwe.mitre.org/data/definitions/89.html
