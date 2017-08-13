* [CSS](#css)
    * [vertical-align: top](#vertical-align-top)
    * [min-height](#min-height)
    * [切图](#切图)
* [JavaScript](#javascript)
    * [对象无序，数组解决](#对象无序数组解决)
* [Django](#django)
    * [本地化包](#本地化包)
* [node](#node)
    * [nvm](#nvm)
* [Git](#git)
    * [git reset --hard xxxxxx](#git-reset---hard-xxxxxx)
* [React](#react)
* [Linux](#linux)
    * [auto-jump](#auto-jump)

## CSS

### vertical-align: top

在调整一个简单的布局时候，遇到的小问题。布局是这样的: 所有的框都左浮动，但是框的高度是不一样的，这样就导致浮动的时候会挤着导致布局并不是想要的那种效果。

想到，可以使用`inline-block`来避免高度不一致的, 但是用了`inline-block`后，才发现它的默认对齐方式是底部对齐，一点都不好看，我一下就蒙圈了。由于自己好久都没有写CSS了，在别人的提醒下才想到`vertical-align: top`这个东东。。

### min-height

还有，一个就是，因为高度不一致，只有使用一种很hack的办法，当后台传过来的数据是空格的时候，仍然显示高度。

这个时候要用到一个CSS属性`min-height`, 可以直接这样`min-height: 14px`， 就可以搞定了。开始我还以为`min-height`只支持IE10+， 后来才发现是自己打开`caniuse`的方式不对。

### 切图

在切图的时候，比如我测量出来高度50px，然后测量出来字体大小14px，我会给剩下的margin或者padding的值设置为36px, 但是发现并不是我想象的那样。

因为字体大小为14px并不代表字体所占据的高度并不只有14px，当我发现我犯下了这样的一个错的时候已经晚了。我只好设置`line-height: 1;`让它的行高和字体大小一样来解决这个问题。

## JavaScript

### 对象无序，数组解决

有一个页面是展示数据的，但是由于数据量并没有太多，而且如果需要建表的话，会非常的麻烦。因此想了一个解决办法，后台直接编辑JSON数据，然后传到前端来，前端进行数据解析。

一开始，全部用的对象，但是当数据展示出来的时候才意识到遍历对象出来的数据并不是有序的，也就是说并不是按照填写的顺序展示的。于是想到用数组来存，那么问题就很好的解决了。

## Django

### 本地化包

如果我们不想将包安装在virtualenv或者全局环境下，也就是我们想要将包装在项目下面，那么如何去配置引用的路径呢？ 可以这样配置:

```
// settings.py
import sys
lib_path = os.path.join(ROOT_DIR, 'libs/')
sys.path.append(lib_path)
```

## node

### nvm

在跑一个项目的时候，后端是用的是node，`yarn start`的时候发现报错:`app crashed - waiting for files changes before start...`, 在检查了各种原因后才发现是我的node版本太高了。于是装了一个nvm(node version manager), 然后`nvm install 6.9.5`安装了后, 使用`nvm use 6.9.5`切换node的版本。

有的时候，要考虑是环境或者是版本的问题，仔细思考到底是代码出了问题还是环境出了问题，这很重要。

## Git

### git reset --hard xxxxxx

在使用`git reset --hard commit_id`后，才发现commit_id之前的commit也没有了，这让我百思不解，后来才反应过来commit线并不是一条直线，而是存在分叉的。

## React

有一个需求是需要挂标，别的网站提供一个script, 然后在React里去引用这段script，就将标(一个图片)挂上去了。

通常图标都是别的网站script里js写入的，而不是我们提供一个div之类的，然后让它来操作DOM。并不是这样的。

所以，这就很尴尬了，不能直接在组件里将script引入，因为React并不会执行Script标签。也不能直接将script引入在`index.html`的模板里，因为script插入在哪个位置，图片就会插入在哪个位置(因为由script提供方控制)。

所以，最后还是用了这个标签：`iframe`, 不知道还是否有其他的方案。

## Linux

### auto-jump