### 如何做分离
#### 分离职责

前后端仅通过异步接口来编程，前后端都各自有自己的开发流程，构建工具，测试集合。

![前后端数据流](/images/posts/前后端数据流.jpg)

#### 接口规范
##### 规范原则

- 接口返回数据即显示：前端仅做渲染逻辑处理
- 渲染逻辑禁止跨多个接口调用
- 前端关注交互、渲染逻辑，尽量避免业务逻辑处理的出现
- 请求响应传输数据格式：JSON，JSON数据尽量简单轻量，避免多级JSON的出现

#### 基本格式
##### 请求格式

**get**
```json
user/login?body={"uname":"root","pwd":"root"}
```
**post**
```json
{"uname":"root","pwd":"root"}
```
**返回格式**
```json
{
  "code":200,        //返回状态码 200成功，其他失败
  "message":"请求成功",  //返回信息描述
  "data": jsonObj  //返回值
}
```
#### java后端规范

- domain中现有POJO已经满足接口要求的，直接返回POJO
- domain中现有POJO无法满足接口要求的，如返回结果需要组合用户/角色/权限三种POJO，请声明一个新的POJO，以**业务名+Vo**命名，如 ```UserRolePrivilegeVo```
- 后端新增ResultData，依据返回格式：

```java
public class AjaxResult extends HashMap<String, Object> {
    private static final long serialVersionUID = 1L;
    public static final String CODE_TAG = "code";
    public static final String MSG_TAG = "msg";
    public static final String DATA_TAG = "data";
}
```
- 后端接口不论业务成功与否，统一返回AjaxResult对象，方便前端做拦截和异常统一提示
- 老的前端代码包含业务逻辑，请将这些逻辑迁移到后端实现
- 老的前端代码包含参数校验规则，请将这些规则交由前端实现