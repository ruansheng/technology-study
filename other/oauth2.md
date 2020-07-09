## OAuth
```
OAuth是一种开放协议, 允许用户让第三方应用以安全且标准的方式获取该用户在某一网站，
移动或者桌面应用上存储的秘密的资源（如用户个人信息，照片，视频，联系人列表），而无需将用户名和密码提供给第三方应用。
```

### OAuth版本
```
OAuth 1.0:
   （RFC5849）作为一个指导性文档发布，是一个小社区的工作成果。
OAuth 2.0
   （RFC6749）不向后兼容OAuth 1.0
```

### OAuth2.0角色
```
资源所有者(Resource Owner)：
   能够许可受保护资源访问权限的实体。当资源所有者是个人时，它作为最终用户被提及。
用户代理(User Agent)：
   指的的资源拥有者授权的一些渠道。一般指的是浏览器、APP
客户端(Client) ：
   使用资源所有者的授权代表资源所有者发起对受保护资源的请求的应用程序。术语“客户端”并非特指任何特定的的实现特点（例如：应用程序是否在服务器、台式机或其他设备上执行）。
授权服务器(Authorization Server)：
   在成功验证资源所有者且获得授权后颁发访问令牌给客户端的服务器。授权服务器和资源服务器之间的交互超出了本规范的范围。授权服务器可以和资源服务器是同一台服务器，也可以是分离的个体。一个授权服务器可以颁发被多个资源服务器接受的访问令牌。
资源服务器(Resource Server)：
   托管受保护资源的服务器，能够接收和响应使用访问令牌对受保护资源的请求。
```

### OAuth2.0角色
```
资源所有者
资源服务器
客户端
授权服务器
```

### OAuth2.0授权模式
```
授权码模式（authorization code）
简化模式/隐藏式（implicit）
密码模式（resource owner password credentials）
客户端凭证模式（client credentials）
```

### 授权码模式（authorization code）
```
授权码（authorization code）方式，指的是第三方应用先申请一个授权码，然后再用该码获取令牌。授权码模式功能最完整、使用最广泛、流程最严密的授权模式

第一步：获取code：
eg：oauthServer+"/oauth/authorize?client_id="+clientId+"&response_type=code&redirect_uri="+redirectUrl+"&scope=all"
如果没有登录，则会跳转到统一身份认证登录页面。如果用户登录了，调用接口后，会重定向到redirect_uri，授权码会作为它的参数

第二步：授权通过，会重定向到redirect_uri，code码会作为它的参数
BusinessServer/callback#code=${code}&state=123

第三步：获取access_token
eg：oauthServer+"/oauth/token?code="+code+"&grant_type=authorization_code&client_secret="+clientSecret+"&redirect_uri="+redirectUri+"&client_id="+clientId

第四步：访问系统资源，此时统一认证服务会根据该认证客户端权限信息判断，决定是否返回信息。
访问：oauthServer/api/userinfo?access_token=${accept_token}
```

### 简化模式/隐藏式（implicit）
```
简化模式（implicit grant type）不通过第三方应用程序的服务器，直接在浏览器中向认证服务器申请令牌，跳过了"授权码"这个步骤，因此称简化模式

第一步：访问授权，要传client_id:客户端id，redirect_uri:重定向uri，response_type为token，scope是授权范围，state是其它自定义参数
oauthServer/oauth/authorize?client_id=cms&redirect_uri=BusinessServer/callback&response_type=token&scope=read&state=123

第二步：授权通过，会重定向到redirect_uri，access_token码会作为它的参数
BusinessServer/callback#access_token=${accept_token}&token_type=bearer&state=123&expires_in=120
令牌的位置是 URL 锚点（fragment），而不是查询字符串（querystring），这是因为 OAuth 2.0 允许跳转网址是 HTTP 协议，因此存在"中间人攻击"的风险，而浏览器跳转时，锚点不会发到服务器，就减少了泄漏令牌的风险。

第三步：拿到acceptToken之后，就可以直接访问资源
oauthServer/api/userinfo?access_token=${accept_token}
```

### 密码模式（resource owner password credentials）
```
密码模式中，用户向客户端提供自己的用户名和密码，这通常用在用户对客户端高度信任的情况

第一步：直接传username，password获取token
oauthServer/oauth/token?client_id=cms&client_secret=secret&username=admin&password=123456&grant_type=password&scope=all

第二步：拿到acceptToken之后，就可以直接访问资源
oauthServer/api/userinfo?access_token=${accept_token}
```

### 客户端凭证模式（client credentials）
```
客户端模式（client credentials）适用于没有前端的命令行应用，即在命令行下请求令牌

第一步： 获取token
oauthServer/oauth/token?client_id=cms&client_secret=123&grant_type=client_credentials&scope=all

第二步：拿到acceptToken之后，就可以直接访问资源
oauthServer/api/userinfo?access_token=${accept_token}
```