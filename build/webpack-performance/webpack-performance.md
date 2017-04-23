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

![webpack-old](./webpack-old.png)


#### 二、项目优化
1、[happypack](https://github.com/amireh/happypack)

通过多进程模型并行转换多个文件，来加速代码构建。再次构建时将会使用生成的缓存文件，从而提升二次构建的速度。


2、[CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/)

webpack 自带插件, 通过抽取公用代码，减少公用文件的大小。

这些优化，让项目最开始的打包速度
