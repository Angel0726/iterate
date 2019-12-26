---
title: 01 Atom 设置
toc: true
date: 2019-12-13
---
# Atom 配置


## 字体配置

通过`File` -> `Settings` -> `Open Config Folder`，然后编辑`styles.less`这个文件，就可以改变Atom的字体设置。

为了让中文显示起来跟好看，我使用微软雅黑作为中文字体，Monaco作为英文字体，具体配置如下：

```css
.tree-view {
  font-size: 12px; /*配置文件树中的字体大小*/
}
atom-text-editor {
  font-family: Monaco, Microsoft YaHei UI; /*配置编辑器的字体*/
}
atom-text-editor .cursor {
  // border-color: red;
}
html, body, .tree-view, .tab-bar .tab, .bottom {
  font-family: Monaco, Microsoft YaHei UI; /*配置编辑器的各个部件的字体*/
}
.terminal {
  font-size: 12px; /*配置终端字体大小*/
  font-family: Monaco, Microsoft YaHei UI; /*配置终端字体*/
}
```

## 常用的Package

- `file-icons`: 显示文件图标
- `pigments`: 使用不同颜色显示文件和文件夹
- `platformio-ide-terminal`: 在Atom里面打开终端窗口

下面这些是和Markdown编辑相关的包：

- `language-markdown`: Markdown语法支持

- `markdown-image-paste`: 使用Ctrl-V将图片插入到Markdown文档中

- `markdown-preview-plus`：对`markdown-preview`的强化，更好的预览Markdown文档

- `markdown-scroll-sync`: 同步显示Markdown源文件和预览文件

- `markdown-table-editor`: 让Markdown编辑表格更轻松

- `markdown-themeable-pdf`: 为Markdown生成的PDF文件使用自定义主题。

  通过修改`~/.atom/markdown-themeable-pdf/`中的以下文件，可以让更好的自定义生成的PDF文件：

  - `~/.atom/markdown-themeable-pdf/header.js`: PDF文件页眉，我的设置如下(页眉中只显示文件名称)。

    ```js
    module.exports = function (info) {
      return {
          height: '2cm',
          contents: '<div style="text-align: right;"><strong>' + info.fileInfo.name + '</strong></div>'
      };
    };
    ```

  - `~/.atom/markdown-themeable-pdf/footer.js`: PDF文件页脚，我的设置如下(页脚中显示页码信息，Copyright，时间戳和作者名)。

    ```js
    module.exports = function (info) {
        var dateFormat = function () {
            var d = new Date();
            var mm = d.getMonth() + 1;
            var dd = d.getDate();
            var yy = d.getFullYear();
            var myDateString = yy + '-' + mm + '-' + dd;
            return  myDateString;
        };
        return {
            height: '1cm',
            contents: '<div style="float:left;">Page {{page}}/{{pages}}</div><div style="float:right;">&copy; Copyright ' + dateFormat() + ' by K.X</div>'
        };
    };
    ```

  - `~/.atom/markdown-themeable-pdf/styles`: PDF文本字体。和Atom一样，我使用微软雅黑作为中文字体，Monaco作为英文字体。

    ```css
    * {
      font-family: Monaco, Microsoft YaHei UI;
    }
    ```

  通过上面的设置，生成的PDF文件看起来如下：

  ![mark](http://images.iterate.site/blog/image/20191213/CJfjawPGMMRi.png?imageslim)atom-themable-pdf-sample.png

- `markdown-writer`: 更好的使用Markdown写作

- `pdf-view`: 基于PDF.js的PDF预览，和`markdown-themeable-pdf`结合起来，可以在Atom里面直接查看生成的PDF文件



# 相关

- [我的 Atom 配置](http://xintq.net/2017/09/30/my-atom-packages/)
