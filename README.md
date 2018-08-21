# 关于企业微信免登以及用户部门获取的工具
部署使用说明

1.初始化

需要自定义listener，在contextInitialized方法中初始化钉钉api执行的必要条件环境
```
@Override
public void contextInitialized(ServletContextEvent sce) {
    DefaultContext.init(String corpSecret, String corpId, String addressCorpSecret);
}
```

2.企业微信应用中免登url的格式

```
https://open.weixin.qq.com/connect/oauth2/authorize?appid=CORPID&redirect_uri=REDIRECT_URI
&response_type=code&scope=SCOPE&agentid=AGENTID&state=STATE#wechat_redirect
```
说明：

redirect_uri ： 授权后重定向的回调链接地址，请使用urlencode对链接进行处理

scope : 
<p>&nbsp;&nbsp;&nbsp;&nbsp;snsapi_base：静默授权，可获取成员的的基础信息（UserId与DeviceId）；</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;snsapi_userinfo：静默授权，可获取成员的详细信息，但不包含手机、邮箱；</p>
<p>&nbsp;&nbsp;&nbsp;&nbsp;snsapi_privateinfo：手动授权，可获取成员的详细信息，包含手机、邮箱</p>

agentid、state为非必须参数


3.在后台接收处理redirect_uri请求，企业微信认证后会在redirect_uri 后添加code参数(redirect_uri?code=XXX),获取code后，调用如下方法获取登录用户详细信息，执行第三方免登操作。
```
WeChatUtil.getUserInfoByCode(code); //获取登录用户信息，包含userId以及user_ticket

CommonUtil.getValue(userInfoByCode, "user_ticket"); //获取user_ticket

WeChatUtil.getUserInfoByUserTicket(user_ticket); //根据user_ticket获取详细用户信息
```


4.获取企业微信部门，用户信息

调用WeChatUserAndGroup类中的方法即可。
```
getDepartment(int departmentId); //获取指定部门id下的子部门信息

getDepartment(); //获取全量部门信息

getUserByDepartmentId(int departmentId, int fetch_child); //获取指定部门id下所有用户的详细信息, fetch_child 表示是否递归获取所有的

getUserByDepartmentId(int departmentId);  //获取指定部门id下所有用户的详细信息,非递归

getUserByUserId(String userId); //根据userId获取用户信息
```
