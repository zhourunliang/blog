---
layout: post
title:  "PHP大文下载方法"
date:   2019-04-04
tags: PHP
---

# 代码
基于thinkphp5.0框架
```php
public function downItem($id)
{
    $info = db('item')->where('id',$id)->find();
    $file = $info['file'];

    if (strtoupper(substr(PHP_OS,0,3))==='WIN') {
        $file = str_replace("/", "\\", $file);
        $file = iconv('utf-8','gb2312',$file);
    }
    
    $filename = ROOT_PATH.'public'.$file;
    return $this->downFile($filename, array('zip'));
}


function downFile($filename,$allowDownExt=array ('jpg','jpeg','gif', 'rar','zip','png','txt','html')) {
    //检测文件是否存在，并且可读
    if(!is_file($filename) && is_readable($filename)) {
        return false;
    }

    //获取文件的扩展名
    $fileext=strtolower(pathinfo($filename,PATHINFO_EXTENSION));

    //检测文件类型是否允许下载
    if(!in_array($fileext,$allowDownExt)) {
        return false;
    }

    //设置脚本的最大执行时间，设置为0则无时间限制
    set_time_limit(0);

    //通过header()发送头信息
    //因为不知道文件是什么类型的，告诉浏览器输出的是字节流
    header('content-type:application/octet-stream');

    //告诉浏览器返回的文件大小类型是字节
    header('Accept-Ranges:bytes');

    //获得文件大小
    $filesize=filesize($filename);

    //告诉浏览器返回的文件大小
    header('Accept-Length:'.$filesize);

    //告诉浏览器文件作为附件处理并且设定最终下载完成的文件名称
    header('content-disposition:attachment;filename='.preg_replace('/^.+[\\\\\\/]/', '', $filename));

    //针对大文件，规定每次读取文件的字节数为4096字节，直接输出数据
    $read_buffer=4096;
    $handle=fopen($filename, 'rb');
    //总的缓冲的字节数
    $sum_buffer=0;

    //只要没到文件尾，就一直读取
    while(!feof($handle) && $sum_buffer<$filesize) {           
        echo fread($handle,$read_buffer);
        $sum_buffer+=$read_buffer;
    }
    //关闭句柄
    fclose($handle);
    exit;
}
```