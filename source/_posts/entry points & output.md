---
layout: webpack
title: webpack --- entry points & output
date: 2022-02-24 11:04:23
tags:
---

### 1、什么是Webpack
Webpack 是一个打包模块化 JavaScript 的工具，在 Webpack 里一切文件皆模块，通过 Loader 转换文件，通过 Plugin 注入钩子，最后输出由多个模块组合成的文件。Webpack 专注于构建模块化项目。
<img src="http://webpack.wuhaolin.cn/1%E5%85%A5%E9%97%A8/img/1-2webpack.png">

一切文件：JavaScript、CSS、SCSS、图片、模板，在 Webpack 眼中都是一个个模块，这样的好处是能清晰的描述出各个模块之间的依赖关系，以方便 Webpack 对模块进行组合和打包。 经过 Webpack 的处理，最终会输出浏览器能使用的静态资源。

Webpack 具有很大的灵活性，能配置如何处理文件，大致使用如下：

``` bash
module.exports = {
  // 所有模块的入口，Webpack 从入口开始递归解析出所有依赖的模块
  entry: './app.js',
  output: {
    // 把入口所依赖的所有模块打包成一个文件 bundle.js 输出 
    filename: 'bundle.js'
  }
}
```

### 2、Entry
entry是配置模块的入口，可抽象成输入，Webpack 执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块。

entry 配置是必填的，若不填则将导致 Webpack 报错退出。

#### 2.1 Entry 类型
Entry 类型可以是以下三种中的一种或者相互组合：

<table>
<thead>
<tr>
<th>类型</th>
<th>例子</th>
<th>含义</th>
</tr>
</thead>
<tbody>
<tr>
<td>string</td>
<td><code>'./app/entry'</code></td>
<td>入口模块的文件路径，可以是相对路径。</td>
</tr>
<tr>
<td>array</td>
<td><code>['./app/entry1', './app/entry2']</code></td>
<td>入口模块的文件路径，可以是相对路径。</td>
</tr>
<tr>
<td>object</td>
<td><code>{ a: './app/entry-a', b: ['./app/entry-b1', './app/entry-b2']}</code></td>
<td>配置多个入口，每个入口生成一个 Chunk</td>
</tr>
</tbody>
</table>
如果是 array 类型，则搭配 output.library 配置项使用时，只有数组里的最后一个入口文件的模块会被导出。


### 3、Output
output 配置如何输出最终想要的代码。output 是一个 object，里面包含一系列配置项，下面分别介绍它们。

#### 3.1 filename
 配置输出文件的名称
 ```
 filename: 'bundle.js' //一个Chunk
 filename: '[name].js' //多个Chunk
 ```
#### *什么是Chunk？*
Webpack 会为每个生成的 Chunk 取一个名称，Chunk 的名称和 Entry 的配置有关：
如果 entry 是一个 string 或 array，就只会生成一个 Chunk，这时 Chunk 的名称是 main；
如果 entry 是一个 object，就可能会出现多个 Chunk，这时 Chunk 的名称是 object 键值对里键的名称。

代码里的 [name] 代表用内置的 name 变量去替换[name]，这时你可以把它看作一个字符串模块函数， 每个要输出的 Chunk 都会通过这个函数去拼接出输出的文件名称。
 ` 
id	Chunk 的唯一标识，从0开始
`
 ` 
name	Chunk 的名称
`
 ` 
chunkhash	Chunk 内容的 Hash 值
 `
`
contenthash  模块内容的哈希
`
#### 3.2 path
output.path 配置输出文件存放在本地的目录，必须是 string 类型的绝对路径。通常通过 Node.js 的 path 模块去获取绝对路径：
```
path: path.resolve(__dirname, 'dist_[hash]')
```

#### 3.3 publicPath
output.publicPath 配置发布到线上资源的 URL 前缀，为string 类型。 默认值是空字符串 ''，即使用相对路径。
```
filename:'[name]_[chunkhash:8].js'
publicPath: 'https://cdn.example.com/assets/'
```
时发布到线上的 HTML 在引入 JavaScript 文件时就需要：
```
<script src='https://cdn.example.com/assets/a_12345678.js'></script>
```

#### 3.4 chunkFilename
output.chunkFilename 配置无入口的 Chunk 在输出时的文件名称。 chunkFilename 和上面的 filename 非常类似，但 chunkFilename 只用于指定在运行过程中生成的 Chunk 在输出时的文件名称。 
常见的会在运行时生成 Chunk 场景有在使用 CommonChunkPlugin、使用 import('path/to/module') 动态加载等时。 chunkFilename 支持和 filename 一致的内置变量。

#### 3.5 crossOriginLoading
Webpack 输出的部分代码块可能需要异步加载，而异步加载是通过 JSONP 方式实现的。 JSONP 的原理是动态地向 HTML 中插入一个 <script src="url"></script> 标签去加载异步资源。 output.crossOriginLoading 则是用于配置这个异步插入的标签的 crossorigin 值。

script 标签的 crossorigin 属性可以取以下值：

anonymous(默认) 在加载此脚本资源时不会带上用户的 Cookies；
use-credentials 在加载此脚本资源时会带上用户的 Cookies。
通常用设置 crossorigin 来获取异步加载的脚本执行时的详细错误信息。

#### 3.6 libraryTarget 和 library
当用 Webpack 去构建一个可以被其他模块导入使用的库时需要用到它们。

output.libraryTarget 配置以何种方式导出库。
output.library 配置导出库的名称。
它们通常搭配在一起使用。

output.libraryTarget 是字符串的枚举类型，支持以下配置。
<code>var (默认)
commonjs
commonjs2
this
window
global
</code>

####  *libraryExport*
output.libraryExport 配置要导出的模块中哪些子模块需要被导出。 它只有在 output.libraryTarget 被设置成 commonjs 或者 commonjs2 时使用才有意义。

#### 3.7 clean
清除dist文件夹

<blockquote>
<p>以上只是 <code>output</code> 里常用的配置项，还有部分几乎用不上的配置项没有一一列举，你可以在 <a href="https://webpack.js.org/configuration/output/" target="_blank">Webpack 官方文档</a> 上查阅它们。</p>
</blockquote>

### webpack常见面试问题
1.module、chunk 和 bundle
2.sourceMap
3.模块热更新
4.模块懒加载
5.长缓存、长缓存优化
6.Tree-sharking