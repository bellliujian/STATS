<!-- TOC depth:6 withLinks:1 updateOnSave:1 -->
- [接口文档](#)
	- [0. 接口简介](#1-)
		- [0.1 权限验证](#11-)
		- [0.2 通讯加密](#12-)
		- [0.3 状态代码](#13-)
		- [0.4 消息格式](#14-)
	- [1. 相关API](#1-api)
		- [1.1 个人中心](#21-)
			- [1.1.1 用户同步接口 `IM`](#111-im)	
			- [1.1.2 用户登录接口 `STATS`](#112-stats)
			- [1.1.3 用户资料更新 `STATS`](#113-stats)
			- [1.1.4 用户注册 `STATS`](#114-stats)
			- [1.1.5 获取用户登录验证码 `STATS`](#115-stats)
			- [1.1.6 获取用户角色信息 `STATS`](#116-stats)
			- [1.1.7 获取个人信息 `STATS`](#117-stats)

		- [1.2 我的成果](#12-)
		- [1.3 我的拍卖](#13-)
	    - [1.4 我的需求](#14-)
	    - [1.5 我的项目](#15-)
	    - [1.6 交易盾](#16-)
			

<!-- /TOC -->



[状态码]:#03-
[消息类型码]:#14-

## 0. 接口简介
1. 接口采用 Http + WebSocket 模式进行开发，数据传输则采用 Json 格式。
2. 登录使用 Http，大文件传输使用 Http (分块上传)。
3. API 路径的基本规则为 `http://{url}/api/{control}/{action}`。
4. IM"交易通"简称，STATS为"科技转化成果平台"简称,接口用STATS结尾的需要提供相关接口。


**POST**
```curl
curl -X POST {{url}}/{control}/{action} -d '{"name":"Sam Winchester","action":"greeting"}' -H "Content-Type: application/json"
```

### 0.1 权限验证
通过post中参数验证

### 0.2 通讯加密
*待定*

### 0.3 状态代码

返回值中的 message 部分，需要加入具体描述，如`用户名/密码不正确，请重新登录`。

状态码 | 描述 | 备注
--- | --- | ---
**code,int** | **message,string** |
 | |
**2xxx** | **-** | **操作成功**
2000 | 操作成功 | *通用状态*
**6xxx** | **-** | **服务器自身错误需要重新请求**
6000 | 超时重试 | *通用状态*
6101 | 更新数据失败 |
6102 | 服务器内部错误 |
**4xxx** | **-** | **待用错误操作**
4000 | 用户验证无效 | *通用状态*




**environment**

environment | IM
--- | ---
url | ws://115.29.179.22:20000

environment | STATS
--- | ---
url | http://*.*.*.*:20001



## 1. 用户相关API

### 1.1 用户体系

**表名称**: `uc_Users`、`uc_Roles`

#### 1.1.1 个人资料更新同步接口`IM`
```curl
curl -X POST {{url}}/api/usersync -d '{"appName":"STATS","appToken":"verify-code","mobile":"13545678521","userName":"1001","password":"670B14728AD9902AECBA32E22FA4F6BD","nickName":"管理员","avatar":"头像Url","create":1422416686183,"modified":1422416686183}' -H "Content-Type: application/json"
```
**Action**: `usersync`

**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥
mobile | 手机号 | string | 必填 |
userName | 用户名 | string | 必填 | 用户UID
nickName| 昵称 | string | 必填 |
password | 密码 | string | 选填 | md5编码
avatar | 头像 | string | 必填 | 头像url地址 可为空
create | 创建时间 | long | 必填 |
modified | 编辑时间 | long | 选填 |

```json
{
  "appName": "STATS",
  "appToken": "verify-code",
  "mobile": "13805711111",
  "userName": "1001",
  "nickName": "管理员",
  "password" : "670B14728AD9902AECBA32E22FA4F6BD",
  "avatar": "",
  "create": 1422416686183,
  "modified": 1422416686183
}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
action | api 动作码 | string | 必要 | verify-code
code | 状态码 | int | 必要 | [状态码][]
message | 状态信息 | string | 必要 | 错误信息

**Success**

```json
{
  "action": "usersync",
  "code": 2000,
  "message": ""
}
```

**Fail**

```json
{
  "action": "usersync",
  "code": 6101,
  "message": "数据更新失败"
}
```







**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
action | api 动作码 | string | 必要 | client-tags
code | 状态码 | int | 必要 | [状态码][]
message | 状态信息 | string | 必要 |



**Success**

```json
{

  "action": "readsession",
  "code": 2000,
  "message": ""
}
```

**Fail**

```json
{
  "action": "readsession",
  "code": 6102,
  "message": ""
}
```


#### 1.1.2 用户登录接口`STATS`
```curl
curl -X POST {{url}}/webapi/Users/login -d '{"appName":"STATS","appToken":"verify-code","userName":"admin","password":"670B14728AD9902AECBA32E22FA4F6BD"}' -H "Content-Type: application/json"
```
**Action**: `login`

**备注**:登陆成功返回用户uid 而不是userName

**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥
userName | 用户名 | string | 必填 | 登录用户名
password | 密码 | string | 必填 | 

```json
{
  "appName": "STATS",
  "appToken": "verify-code",
  "userName": "admin",
  "password" : "123456",

}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---

code | 状态码 | int | 必要 | [状态码][]
uid | 用户uid | string | 选要 | 登陆成功必须返回

**Success**

```json
{
 
  "code": 2000,
  "uid": "1001"
}
```

**Fail**

```json
{

  "code": 4000
}
```


#### 1.1.3 用户资料更新`STATS`
```curl
curl -X POST {{url}}/webapi/users/edit -d '{"appName":"STATS","appToken":"verify-code","userName":"1001","oldPassword":"111111","newPassword":"123456","email":"123456","mobileNumber":"13805711111","phoneNumber":"","address":"","trueName":"管理员","idCard","330101198003111212"}' -H "Content-Type: application/json"
```


**备注**:当字段参数为为空的时候用户不需要更新对应字段

**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥
userName | 用户名 | string | 必填 | 用户uid
oldPassword | 原密码 | string | 选填 | 
newPassword | 新密码 | string | 选填 | 
mobileNumber |手机号| string | 选填|
phoneNumber |邮件| string | 选填|
address |住址| string | 选填|
trueName |真实姓名| string | 选填|
idCard |身份证号码| string | 选填|


```json
{
  "appName": "STATS",
  "appToken": "verify-code",
  "userName": "1001",
  "password" : "123456",
  "oldPassword":"111111",
  "newPassword":"123456",
  "email":"123456",
  "mobileNumber":"13805711111",
  "phoneNumber":"",
  "address":"",
  "trueName":"管理员",
  "idCard","330101198003111212"

}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
action | api 动作码 | string | 必要 | verify-code
code | 状态码 | int | 必要 | [状态码][]
message | 用户uid | string | 选要 | 操作失败必须返回

**Success**

```json
{

  "code": 2000,
 
}
```

**Fail**

```json
{
  "message": "原密码错误",
  "code": 6101,
}
```




#### 1.1.4 用户注册`STATS`
```curl
curl -X POST {{url}}/webapi/users/create -d '{"appName":"STATS","appToken":"verify-code","userName":"1001","password":"111111","mobileNumber":"13805711111","mobileVerifyCode":"5100","roolId",1013,"email":"67843214@qq.com","province":"浙江省"，"city":"湖州市"，"district":"德清县"}' -H "Content-Type: application/json"
```


**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥
userName | 用户名 | string | 必填 | 用户名
password | 原密码 | string | 必填 |
mobileNumber |手机号| string | 必填 |
mobileVerifyCode|  验证码| string | 必填 |
roleId | 账户类型 | int | 必填 |
email  | 邮件 | string | 必填 |
province | 省份 | string | 必填 |
city | 城市 | string |必填 
district | 县区 | string | 必填


```json
{
  "appName": "STATS",
  "appToken": "verify-code",
  "userName": "admin",
  "password" : "123456",
   "mobileNumber":"13805711111",
   "mobileVerifyCode":"5100",
   "roolId",1013,
   "email":"67843214@qq.com",
   "province":"浙江省"
   "city":"湖州市"，
   "district":"德清县"
}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
code | 状态码 | int | 必要 | [状态码][]
uid | 用户uid | string | 选要 | 操作成功必须返回
message | 错误信息 | string | 选要 | 操作失败必须返回

**Success**

```json
{

  "code": 2000,
  "uid":1003
}
```

**Fail**

```json
{
  "message": "原密码错误",
  "code": 6101,
}
```

#### 1.1.5 获取用户登录验证码`STATS`



```curl
curl -X POST {{url}}/webapi/users/SendMobileVerifyCode -d '{"appName":"STATS","appToken":"verify-code","phone":"13805711111"}' -H "Content-Type: application/json"
```


**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥
phone | 用户名 | string | 必填 | 手机号


```json
{
  "appName": "STATS",
  "appToken": "verify-code",
  "phone": "13805711111"
}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
code | 状态码 | int | 必要 | [状态码][]
message | 错误信息 | string | 选要 | 操作失败必须返回

**Success**

```json
{

  "code": 2000
}
```

**Fail**

```json
{
  "message": "未知错误",
  "code": 6102,
}
```



#### 1.1.6 获取用户角色信息`STATS`



```curl
curl -X POST {{url}}/webapi/users/roleslist -d '{"appName":"STATS","appToken":"verify-code"}' -H "Content-Type: application/json"
```


**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥


```json
{
  "appName": "STATS",
  "appToken": "verify-code",
}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
code | 状态码 | int | 必要 | [状态码][]
message | 错误信息 | string | 选要 | 操作失败必须返回

date | 返回结果 | List<RolesModel> | 必填 | 



**Success**

```json
{

  "code": 2000
  "date":[
    {
      "roleId":1001,
      "roleName":"管理员"
    }，
    {
     "roleId":1001,
     "roleName":"业务员"
    }

  ]
}
```

**Fail**

```json
{
  "message": "未知错误",
  "code": 6102,
}
```



#### 1.1.7 获取个人信息`STATS`
```curl
curl -X POST {{url}}/webapi/users/info -d '{"appName":"STATS","appToken":"verify-code","userName":"1001"}' -H "Content-Type: application/json"
```


**备注**:主要用于编辑用

**Input**

字段名 | 字段 | 数据类型 | 是否必填 | 描述及要求
--- | --- | --- | --- | ---
appName | app名称 | string | 必填 | 
apptoken | token值 | string | 必填 | 临时密钥
userName | 用户名 | string | 必填 | 用户uid


```json
{
  "appName": "STATS",
  "appToken": "verify-code",
  "userName": "1001"
}
```

**Output**

字段名 | 字段 | 数据类型 | 是否必要 | 描述及要求
--- | --- | --- | --- | ---
action | api 动作码 | string | 必要 | verify-code
code | 状态码 | int | 必要 | [状态码][]
Data | 用户信息 | usermodel | 必填 | 操作成功时必须返回

**Success**

```json
{

  "code": 2000,
  "data":  
   {
    "userName":"admin",
    "trueName":"管理员",
    "email":"123456",
    "mobileNumber":"13805711111",
    "phoneNumber":"",
    "address":"",
    "idCard","330101198003111212"
   }
}
```

**Fail**

```json
{
  "message": "内部错误",
  "code": 6101,
}
```






### 1.2 我的成果

**表名称**: `sta_Projects`


### 1.3 我的拍卖

**表名称**: `auc_Auctions`

### 1.4 我的需求

**表名称**: `sta_Problems`

### 1.5 我的项目
**表名称**: `sta_Trades`

**被对接的项目表名称**: `sta_Projects 和 sta_Trades`


### 2 成果
**表名称**: `sta_Projects`

### 3 需求

### 4 专家查询
**表名称**: `sta_Experts`

### 5 网上拍
**表名称**: `auc_Auctions`、`auc_AuctionRecords` `attachment`


### 6 展会
**表名称**: `sta_Expos`、`sta_ExpoItems` `sta_ExpoProcs`






