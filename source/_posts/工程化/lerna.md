---
title: lerna 
categories: 工程化
---
### 原理
1. 找出变更(当前分支commit对比最近tag)
2. 生成新版本号并更新packag.json
3. 打tag，推到仓库


#### 版本控制	
1. 固定模式(默认)，以全局 lerna.json 版本号为准，所有包共用一个版本号。lerna publish时，会自动将所有 package 版本号同步到最新。vue release有点像这个模式。
2. 独立模式。lerna init --independent 各子包独立版本控制。

 
#### 注意事项
  learn不识别自己打的tag, 影响对比变更, 所以不自己打tag
  多分支会可能造成版本冲突，只在master分支上publish
  publish失败后，
  lerna publish from-git能再次发布当前提交中标记的包；
  lerna publish from-package 对比本地包，远端不存在会再次发布

#### 处理依赖
  lerna bootstrap --hoist lerna把检测到package中相同的依赖--安装在根目录node_modules中--减少依赖安装次数，提升速度。
  npmClient: "yarn"时，该命令不可用，
    解决：yarn的workspace特性： useWorkspaces true + workspaces packages级配置 yarn本身提供了较lerna更好的依赖分析和yarn hoisting功能
    yarn workspace：自包独立package.json，公共依赖可，yarn hoisting；yarn可一次性安装所有依赖
  npmClient: "yarn" lerna bootstrap命令由yarn install代理
  1本地包之间的依赖通过软链接实现,lerna bootstrap时会自动在根目录node_modules下创建该包对应软链。2更新版本号是会自动更新依赖包的版本号

#### 常用命令
  lerna exec -- <command> [..args] # 在所有包中运行该命令
  lerna add <package> 为所有包安装依赖
  lerna ls: 等同于 lerna list本身，输出项目下所有的 package
  lerna ll: 输出项目下所有 package 名称、当前版本、所在位置
  lerna la: 输出项目下所有 package 名称、当前版本、所在位置，包括 private package








