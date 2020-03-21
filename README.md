TinyMCE extension for laravel-admin
======

这是一个`laravel-admin`扩展，用来将`tinyMCE`集成进`laravel-admin`的表单中

## 截图

![截图演示](https://tva1.sinaimg.cn/large/00831rSTly1gcqectsfz1j31km0ckgmx.jpg)

## 安装

首先

```composer require super-eggs/laravel-admin-tinymce```

然后

```php artisan vendor:publish --provider=Encore\TinyMCE\TinyMCEServiceProvider```

## 配置

```
'tinymce' => [
            // Set to false if you want to disable this extension
            'enable' => true,
            // Editor configuration
            'config' => [
                'resize'=> false,
                'plugins'=> 'advlist autolink link image lists preview code help fullscreen table autoresize ',   // 插件
                'toolbar'=> 'undo redo | styleselect | fontsizeselect bold italic | link image blockquote removeformat | indent outdent bullist numlist code',   // 配置工具栏
                'images_upload_url'=> '/api/v1/images',  //图片上传接口  返回格式：{ location : "/demo/image/1.jpg" }
            ]
        ]
```
更多配置: http://tinymce.ax-z.cn/  文档写的非常棒,清晰明了!! (感谢 莫若卿 写的中文文档.)



## 使用

在`form`中使用

```
$form->tinymce('content');
```

## 上传图片

1.创建控制器

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

class UploadImageController
{
    public function index(Request $request)
    {
        $image = $request->file();
        $file = $image['file'];

        //判断上传是否成功
        if ($file->isValid()) {
            //获取文件的扩展名
            $ext = $file->getClientOriginalExtension();

            //获取文件的临时绝对路径
            $path = $file->getRealPath();

            $filename = time().mt_rand(999, 9999).'.'.$ext;

            //把上传的文件从临时文件夹移动到指定文件夹
            Storage::disk('admin')->put($filename, file_get_contents($path));
        }
        return json_encode(array('location' => env('APP_URL').'/uploads/'.$filename));
    }
}
```
2.将路由指向该控制器

3.修改配置选项中的 images_upload_url => '路由'

> php配置 http://tinymce.ax-z.cn/advanced/php-upload-handler.php

## License

Licensed under The [MIT License (MIT).](https://github.com/super-eggs/tinymce/blob/master/LICENSE)
