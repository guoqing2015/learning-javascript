# compass自动生成雪碧图

## 环境配置

安装`ruby`, [安装指南](https://www.ruby-lang.org/en/documentation/installation/)

安装`sass`, ```gem install sass```

安装`compass`, ```gem install compass```

## compass项目

在项目根目录运行```compass init```

或者直接在一个目录下运行```compass create compass-test```

compass项目配置文件`config.rb`

```rb
require 'compass/import-once/activate'
# Require any additional compass plugins here.

# Set this to the root of your project when deployed:
http_path = "/"
css_dir = "stylesheets"
sass_dir = "sass"
images_dir = "images"
javascripts_dir = "javascripts"

# You can select your preferred output style here (can be overridden via the command line):
# output_style = :expanded or :nested or :compact or :compressed

# To enable relative paths to assets via compass helper functions. Uncomment:
# relative_assets = true

# To disable debugging comments that display the original location of your selectors. Uncomment:
# line_comments = false


# If you prefer the indented syntax, you might want to regenerate this
# project again passing --syntax sass, or you can uncomment this:
# preferred_syntax = :sass
# and then run:
# sass-convert -R --from scss --to sass sass scss && rm -rf sass && mv scss sass
```

- 准备要拼接的icon

根据config.rb中的设置，我们需要将图片放在images文件夹下
所以，我们先在项目根目录下新建一个images文件夹，
比如我们现在项目㓟多个模块，每个模块需要对应一个雪碧图，
那么我们在images中新建多个文件夹保存原始的图片；

- 现在假设我们有一个share模块，然后share模块有2张需要合并的背景图

- 针对share模块，我们在sass文件夹下新建一个叫share.scss，内容为：

```scss
@charset "utf-8";
@import "compass/utilities/sprites";    // 加载compass sprites模块
@import "share/*.png";                    // 导入share目录下所有png图片
@include all-share-sprites;                // 输出所有的雪碧图css
```

- 在根目录下，执行

```compass compile```
或
```compass watch```

如果命令执行成功，我们可以在images文件夹下生成一个以share开头的文件,如：share-s0876535d58.png。

- 同时，我们在stylesheets文件夹下，看到新生成的share.css文件


## 雪碧图配置

配置相关的语法：

```
$<map>-<property>: setting;                // 配置所有sprite
$<map>-<sprite>-<property>: setting;    // 配置单个sprite
```

`<map>`: 对应图标存放的文件夹名称，如上面例子中的：share
`<sprite>`: 对应单个图标的名称，如上面例子中的: weibo, qq, github等

[查看demo](../code/compass-demo/sass/share.scss)

参考资料：
- [使用compass自动合并css雪碧图](https://www.w3ctech.com/topic/620)