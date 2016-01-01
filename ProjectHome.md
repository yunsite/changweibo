# 长微博生成器 #
长微博程序，即所谓的**[长微博生成器](http://changweibo.debake.com/code/)** ，这是一个PHP的脚本，可以实现基本的长微博功能。欢迎试用微博不止(weibo.bz)。

# [使用长微博](http://changweibo.debake.com/) #

# [weibo.bz](http://bz.debake.com/) #

[![](http://cwbgj.xti.me/demo.gif)](http://changweibo.debake.com/)

长微博生成器正在紧张激烈的开发中。。。
以下是一个文字转图片的脚本示例：

```
<?php

error_reporting(7);
define('ROOT', getcwd());
define('BASEDIR', parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH));
if ($_POST['text']) {
    $ret = array ();
    $text = $_POST['text'];
    try {
        $ret['imgurl'] = text2img($text, (array)$_REQUEST['config']);
    } catch (Exception $e) {
        $ret['imgurl'] = print_r($e, true);
    }
    echo str_replace('\\/', '/', json_encode($ret));
    exit (0);
}
function text2img($text, $options = array ()) {
    $text .= "\n-------------------------------\n";
    $text .= "http://iscup.com";
    $rows = substr_count($text, "\n") + 1;
    $font_path = $options['fontfile'] ? $options['fontfile'] : ROOT . '/SIMSUN.TTC'; 
    if (!file_exists($font_path))
        throw new Exception(" 找不到字体文件：$font_path  ");
    $font_size = $options['fontsize'] ? $options['fontsize'] : 12;
    $padding = $options['padding'] ? $options['padding'] : 30;
    $row_plus_height = $options['row_plus_height'] ? $options['row_plus_height'] : 4;
    $border = 1;
    $im_width = 430;
    $im_height = ($row_plus_height + ($font_size * 4) / 3) * $rows + ($padding + $border) * 2;
    $im = @ imagecreatetruecolor($im_width, $im_height);
    if (!$im)
        throw new Exception("初始化图片失败，请检查 GD 配置");
    imagefilledrectangle($im, $border, $border, ($im_width -2 * $border), ($im_height -2 * $border), imagecolorallocate($im, 255, 255, 255));
    imagettftext($im, $font_size, 0, ($border + $padding), (($font_size * 4) / 3 + $border + $padding), imagecolorallocate($im, 0, 0, 0), $font_path, $text);
    $base_path = '/files';
    $base_filename = date("Y-m-d,H-i-s") . '.png';
    $short_filename = $base_path . '/' . $base_filename;
    $short_url = rtrim(BASEDIR, '/') . $short_filename;
    @ mkdir(ROOT . $base_path, 0777, true);
    $filename = ROOT . $short_filename;
    if (!imagepng($im, $filename)) {
        throw new Exception("创建图片时出错。");
    }
    @ imagedestroy($im);
    return $short_url;
}

?>
```