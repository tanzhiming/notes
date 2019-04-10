# 授权码方式

1. 授权请求

"application/x-www-form-urlencoded" 格式参数

* response_type： 必需， 值为"code"
* client_id: 必需
* redirect_uri：可选
* scope: 可选
* state: 推荐的

例如： 
GET /authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com


2. 授权响应

“application/x-www-form-urlencoded”格式向重定向URI查询部分添加下列参数

* code： 必需， 授权码
* state： 必需

例如：
```
HTTP/1.1 302 Found
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz
```

错误响应：

“application/x-www-form-urlencoded”格式向重定向URI的查询部分添加下列参数通知客户端

* error： 必需
  
    * invalid_request
    * unauthorized_client
    * access_denied
    * unsupported_response_type
    * invalid_scope
    * server_error
    * temporarily_unavailable

* error_description: 可选
* error_uri: 可选
* state: 必需

例如： 
```
HTTP/1.1 302 Found
Location: https://client.example.com/cb?error=access_denied&state=xyz
```

3. 访问令牌请求

通过“application/x-www-form-urlencoded”格式在HTTP请求实体正文中发送下列UTF-8字符编码的参数向令牌端点发起请求：

grant_type： 必需的。值必须被设置为“authorization_code”。
code： 从授权服务器收到的授权码。
redirect_uri： 必需的。
client_id： 必需的。

例如： 

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
```

4. 访问令牌响应

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache
{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"example",
  "expires_in":3600,
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
  "example_parameter":"example_value"
}
```


# 隐式许可

1. 授权请求

客户端通过“application/x-www-form-urlencoded”格式向授权端点URI的查询部分添加下列参数构造请求URI

* response_type： 必需的。
* client_id： 必需的
* redirect_uri： 可选的。
* scope： 可选的。
* state： 推荐的。


例如：

```
GET /authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb HTTP/1.1
Host: server.example.com
```

2. 访问令牌响应

使用“application/x-www-form-urlencoded”格式向重定向URI的片段部分添加下列参数传递访问令牌至客户端:

* access_token： 必需的。授权服务器颁发的访问令牌
* token_type： 必需的
* expires_in： 推荐的。
* scope： 可选的
* state： 必需的

```
HTTP/1.1 302 Found
Location: http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA&state=xyz&token_type=example&expires_in=3600
```

3. 错误响应


* error： 必需
  
    * invalid_request
    * unauthorized_client
    * access_denied
    * unsupported_response_type
    * invalid_scope
    * server_error
    * temporarily_unavailable

* error_description: 可选
* error_uri: 可选
* state: 必需


# 资源所有者密码凭据许可

1. 访问令牌请求

“application/x-www-form-urlencoded”格式

* grant_type: 必需的, 值为“password”
* username: 必需的
* password: 必需的
* scope: 可选的

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=password&username=johndoe&password=A3ddj3w
```


2. 访问令牌响应

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache
{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"example",
  "expires_in":3600,
  "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
  "example_parameter":"example_value"
}
```


# 客户端凭证许可

1. 访问令牌请求

“application/x-www-form-urlencoded”格式

* grant_type: 必需的, 值为“client_credentials”
* scope: 可选的

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=client_credentials
```


2. 访问令牌响应

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
Cache-Control: no-store
Pragma: no-cache
{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"example",
  "expires_in":3600， "example_parameter":"example_value"
}
```


# 刷新令牌

“application/x-www-form-urlencoded”格式

* grant_type: 必需的。值必须设置为“refresh_token”。
* refresh_token: 必需的。
* scope: 可选

```
POST /token HTTP/1.1
Host: server.example.com
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=refresh_token&refresh_token=tGzv3JOkF0XG5Qx2TlKWIA
```


