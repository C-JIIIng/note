# Webpack



## 在网页中会引用那些常见的静态资源？

+ JS
  + .js .jsx .coffee .ts  (TypeScript  类 C# 语言) 
+ CSS
  + .css .less .sass .scss 
+ Images
  + .jpg .png .gif .bmp .svg
+ 字体文件（fonts）
  + .svg .ttf .eot .woff .woff2
+ 模板文件
  + .ejs .jade .vue(这是在webpack中定义组件的方式 推荐这么用)



## 网页中引入的静态资源多了以后有什么问题？

1. 网页加载速度慢 因为要发起很多二次请求；
2. 要处理错综复杂的依赖关系



## 如何解决上述两个问题

1. 合并、压缩、精灵图、图片的base64编码
2. 可以使用 requireJS、也可以使用 webpack可以解决各个包之间的复杂依赖关系



## 什么是Webpack

webpack是前端的一个项目构建工具，他是基于Node.js 开发出来的一个开发工具

Webpack这个前端自动化构造工具 可以完美实现资源的合并、打包、压缩、混淆等诸多功能



## 初步使用webpack打包构建列表隔行变色案例

























