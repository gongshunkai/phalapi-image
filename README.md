# 图像处理扩展


## 安装和配置
修改项目下的composer.json文件，并添加：  
```
    "phalapi/image":"dev-master"
```
在/path/to/phalapi/config/app.php文件中，配置： 
```
    'Image' => array(
        //要使用的类库 1:标识GD库类型 2:标识imagick库类型
        'type' => 1,
        //图像路径
        'imgname' => null
    )
```
然后执行```composer update```。  

## 注册
在/path/to/phalapi/config/di.php文件中，注册：  
```php
$di->image = function() {
	return new \PhalApi\Image\Lite(\PhalApi\DI()->config->get('app.Image')['type'],\PhalApi\DI()->config->get('app.Image')['imgname']);
};
```

## 使用
1. 打开图片
```php
\PhalApi\DI()->image->open('./1.jpg');
```
2. 基础方法
```php
$width = \PhalApi\DI()->image->width(); // 返回图片的宽度
$height = \PhalApi\DI()->image->height(); // 返回图片的高度
$type = \PhalApi\DI()->image->type(); // 返回图片的类型
$mime = \PhalApi\DI()->image->mime(); // 返回图片的mime类型
$size = \PhalApi\DI()->image->size(); // 返回图片的尺寸数组 0 图片宽度 1 图片高度
```
3. 压缩裁剪
```php
/**
 * 可以支持其他类型的缩略图生成，设置包括下列常量或者对应的数字：
 * IMAGE_THUMB_SCALING = 1      //常量，标识缩略图等比例缩放类型
 * IMAGE_THUMB_FILLED = 2       //常量，标识缩略图缩放后填充类型
 * IMAGE_THUMB_CENTER = 3       //常量，标识缩略图居中裁剪类型
 * IMAGE_THUMB_NORTHWEST = 4    //常量，标识缩略图左上角裁剪类型
 * IMAGE_THUMB_SOUTHEAST = 5    //常量，标识缩略图右下角裁剪类型
 * IMAGE_THUMB_FIXED = 6        //常量，标识缩略图固定尺寸缩放类型
 */
// 按照原图的比例生成一个最大为150*150的缩略图并保存为thumb.jpg
\PhalApi\DI()->image->thumb(150, 150, IMAGE_THUMB_SCALING);
\PhalApi\DI()->image->save("thumb.jpg");

//将图片裁剪为400x400并保存为corp.jpg
\PhalApi\DI()->image->crop(400, 400)->save('./crop.jpg');

//将图片裁剪为400x400并保存为corp.jpg  从（100，30）开始裁剪
\PhalApi\DI()->image->crop(400, 400, 100, 30)->save('./crop.jpg');
```
4. 图片水印
```php
/**
 * water方法的第二个参数表示水印的位置，可以传入下列常量或者对应的数字：
 * IMAGE_WATER_NORTHWEST =   1 ; //左上角水印
 * IMAGE_WATER_NORTH     =   2 ; //上居中水印
 * IMAGE_WATER_NORTHEAST =   3 ; //右上角水印
 * IMAGE_WATER_WEST      =   4 ; //左居中水印
 * IMAGE_WATER_CENTER    =   5 ; //居中水印
 * IMAGE_WATER_EAST      =   6 ; //右居中水印
 * IMAGE_WATER_SOUTHWEST =   7 ; //左下角水印
 * IMAGE_WATER_SOUTH     =   8 ; //下居中水印
 * IMAGE_WATER_SOUTHEAST =   9 ; //右下角水印
 */
//添加图片水印
\PhalApi\DI()->image->open('./1.jpg');
//将图片裁剪为440x440并保存为corp.jpg
\PhalApi\DI()->image->crop(440, 440)->save('./crop.jpg');
// 给裁剪后的图片添加图片水印（水印文件位于./logo.png），位置为右下角，保存为water.gif
\PhalApi\DI()->image->water('./logo.png')->save("water.gif");
// 给原图添加水印并保存为water_o.gif（需要重新打开原图）
\PhalApi\DI()->image->open('./1.jpg')->water('./logo.png')->save("water_o.gif");

//还可以支持水印图片的透明度（0~100，默认值是80），例如：
// 在图片左上角添加水印（水印文件位于./logo.png） 水印图片的透明度为50 并保存为water.jpg
\PhalApi\DI()->image->open('./1.jpg')->water('./logo.png', IMAGE_WATER_NORTHWEST, 50)->save("water.jpg");
```