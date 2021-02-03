---
title: vue
---
### 配置文件
```
.env                # 所有的环境被载入
.env.local          # 所有的环境被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式下被载入
.env.[mode].local   # 只在指定的模式下被载入，但会被 git 忽略
.env.[mode].local > .env.[mode] > .env.local > .env    //相同会覆盖，不同进行合并
```
使用 `process.env.[name]` 进行访问，对象可以包含多个键值对，vue-cli仅支持VUE_APP_开头的变量[`NODE_ENV` 和 `BASE_URL`]除外，webpack的DefinePlugin没有命名限制
vue-cli-service serve --mode development
修改webpack 4中mode选项为dev，同时会读取.env.[mode]中的配置

### 注入
webpack 通过 DefinePlugin 内置插件将 process.env 注入到客户端代码中
``` 
{
    plugins: [
        new webpack.DefinePlugin({
            'process.env': {
                NODE_ENV: JSON.stringify(process.env.NODE_ENV)
            }
        }),
    ]
}
//vue-cli 3.x 已封装好
```

 