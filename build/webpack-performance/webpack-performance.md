### webpack 性能优化之路
#### 一、本地环境
##### 1、硬件环境

    8 GB 1600 MHz DDR3

    2.6 GHz Intel Core i5

##### 2、运行环境

    node v7.3.0

    npm v3.10.10

    webpack v2.2.1

##### 3、项目未优化时

    大小: 3.4M

    打包情况:

        install时间为: 138s

        打包时间为: 137222ms

        大文件:  app.js 为1.94M、pass.js为1.21M、vender.js为0.9M


![webpack-old](./webpack-old.png)


#### 二、项目插件优化
##### Step 1、[happypack](https://github.com/amireh/happypack)

通过多进程模型并行转换多个文件，来加速代码构建。再次构建时将会使用生成的缓存文件，从而提升二次构建的速度。

首次打包时间为：107430ms, 速度提升约22%

二次打包时间为：95070ms, 速度提升约31%

![happy-cache](./happy-cache.png)

##### Step 2、[CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/)

webpack 自带插件, 通过抽取公用代码，减少公用文件的大小。

到Step 1优化之后，我们发现，打包的速度有了极为明显的提升，这个时候我们会想到，那些大文件是否可以继续优化一把，比如我们是否可以把他们的公用文件抽取出来(注: 先前项目中引入的第三方插件配置在entry中的vender)

![vender](./vender.png)

优化之后，我们发现

二次打包时间为: 62179ms, 速度提升约55%

大文件:  app.js 为0.72M、pass.js为0.35M、vender.js为0.9M，大小相对减少 52%

![webpack-vender](./webpack-vender.png)

##### Step 3、[Manifest File](https://webpack.js.org/guides/code-splitting-libraries/#manifest-file) 缓存优化

Step 2结束之后，我们发现每次生成的vender.js的hash码都不一样，既然作为公用的文件，那么每次生成都应该一样，这样项目再次发布时，浏览器就不用重复性的下载新文件了

    PS:官网原文

    But, if we change application code and run webpack again, we see that the hash for the vendor file changes. Even though we achieved separate bundles for vendor and main bundles, we see that the vendor bundle changes when the application code changes. This means that we still don't reap the benefits of browser caching because the hash for vendor file changes on every build and the browser will have to reload the file.

初始打包信息

![manifest-before](./manifest-before.png)

业务代码改动后代码信息

![manifest-after](./manifest-after.png)

如果想让替代webpack自带的manifest，可以试一下这个 [chunk-manifest-webpack-plugin](https://github.com/soundcloud/chunk-manifest-webpack-plugin)

##### Step 4、[Yarn](https://webpack.js.org/guides/code-splitting-libraries/#manifest-file) install优化
yarn，在使用上和npm没有什么明显区别, 初始目的是解决的npm安装过程中包一致的问题

和npm相比特别之处在于

1、默认生成的 yarn.lock 文件 用于保证包一致

2、并行安装， 并有缓存，因而二次安装速度快，并能够离线安装

3、简洁的输出，对外的api

有一个经验公式

T总 = T下载依赖 + Twebpack

Twebpack = Tloaders + Tplugins

yarn首次安装耗时较长,耗时233s, 增加耗时68%

![yarn-first](./yarn-first.png)

yarn二次安装耗时较长,耗时16.6s, 速度提升约88%

![yarn-second](./yarn-second.png)

##### Step 5

#### 三、代码优化