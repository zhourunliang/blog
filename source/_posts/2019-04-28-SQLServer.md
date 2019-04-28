---
layout: post
title:  "SQLServer笔记"
date:   2019-04-28
tags: SQLServer
---
# 安装
这里用的是SQL Server 2008 R2 SP2 - Express Edition
- 下载安装[SQLEXPRADV_x64_CHS.exe](https://www.microsoft.com/zh-CN/download/details.aspx?id=30438)
- 下载安装[msodbcsql.msi](https://www.microsoft.com/zh-CN/download/details.aspx?id=36434)

# php扩展
我们用的是php5.5版本，这是需要[下载](https://www.microsoft.com/en-us/download/details.aspx?id=20098)相应的dll文件
修改php.ini文件
```ini
; php_sqlsrv
extension=php_sqlsrv_55_ts.dll
extension=php_pdo_sqlsrv_55_ts.dll
```

# thinkphp5配置
application/database.php
```php
// 数据库类型
'type'            => 'sqlsrv',
// 服务器地址
'hostname'        => 'localhost',
// 数据库名
'database'        => 'test',
// 用户名
'username'        => 'sa',
// 密码
'password'        => '123456',
// 端口
'hostport'        => '1433',
// 连接dsn
'dsn'             => 'sqlsrv:Server=localhost;Database=test',
```

# 查询
方式一：
用模型
```php
use app\index\model\User as UserModel;
class Sqlsrv extends \think\Controller
{
    public function query()
    {
        $user = new UserModel();
		// 查询数据集
        $list = $user->select();
        $this->view->assign('list',$list);
        return $this->view->fetch();
    }
}
```

方式二：
```php
$list = db('student_subject_relation')->alias('ssr')
        ->join('student st','st.id = ssr.student_id','LEFT')
        ->join('subject su','su.id = ssr.subject_id','LEFT')              
        ->field('
                    st.name as student_name,
                    su.name as subject_name
                ')
        ->select();
```



## 参考资料
* [QLEXPRADV_x64_CHS.exe ,SQLEXPRWT_x64_CHS.exe和SQLEXPR_x64_CHS.exe应该装哪个](https://www.zhihu.com/question/21356790)

