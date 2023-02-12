# big-react
从0 到1 做一个 react

# 一、搭架子
    架子包括：
        - 定义项目结构（monorepo）
        - 定义开发规范 （lint、commit、tsc、代码风格）
        - 选择打包工具

## 1.项目结构
    Multi-repo 和 Mono-repo 如何选择？
    Multi-repo 每个库都有自己独立的仓库，逻辑清晰，相对应的，协同管理会更繁琐。
    Mono-repo 可以很方便协同管理不同独立的库的生命周期，相对应的，会有更高操作的复杂度。
    参考资料： https://juejin.cn/post/6944877410827370504
    参考项目本身 本项目 使用多个库 react、react-dom、react-*、同时管理多个包 并且包之间相互依赖、所以使用Mono-repo
    Vue、babel、npm 都是这种结构

## Mono-repo
    Mono-repo技术选型
    简单工具：
    - npm workspace
    - yarn workspace
    - pnpm workspace
    专业工具：
    - nx
    - bit
    - turbo-repo
    - rush
    - lerna
    pnpm相比于其他打包工具的优势： 
    - 依赖安装快 1.为什么更快 处理不同的依赖是使用link链接
    - 更规范（处理幽灵依赖）2. 什么是 幽灵依赖 没有在依赖中显示安装的依赖

## pnpm-workspace.yaml
 定义了工作空间的根目录，并能够使您从工作空间中包含/排除目录。默认情况下包含所有子目录。
 即使使用了自定义目录位置通配符，根目录下的package目录也总是被包含的

 ## 2.定义开发规范
   ### 代码规范检查与修复
   - 代码规范：lint工具
   eslint
   安装
   pnpm i eslint -D -w 
    -D devDependencies -w mono-repo 项目 依赖制定安装 位置 -w 根目录
   初始化
   npx eslint --init
   选择语法和发现错误 代码风格 交给 prettier
   安装遇到的两个错误
    - 没有携带 -w 指定安装目录
    - pnpm不支持@latest
    - 缺少 peerDependencies 原因是我们使用了很多库 但是没有必要都安装 即依赖 但是没有必要都安装的库安装到 peerDependencies
   配置

   ### 安装ts eslint 插件
   pnpm i -D -w @typescript-eslint/eslint-plugin

### 代码风格 prettier
pnpm i prettier -D -w
新建.prettierrc.json配置文件，添加配置：
{
 "printWidth": 80,
 "tabWidth": 2,
 "useTabs": true,
 "singleQuote": true,
 "semi": true,
 "trailingComma": "none",
 "bracketSpacing": true
}
将prettier集成到eslint中，其中：

eslint-config-prettier：覆盖ESLint本身的规则配置

eslint-plugin-prettier：用Prettier来接管修复代码即eslint --fix

pnpm i eslint-config-prettier eslint-plugin-prettier -D -w

## commit 规范检查
### 提交代码时格式化 代码
 安装husky 用于拦截commit命令
 pnpm i husky -D -w
 初始化 husky 
 npx husky install
 将刚才实现的格式化命令pnpm lint 纳入 commit 时 huasky 将执行脚本
npx husky add .husky/pre-commit "pnpm lint"
-- TODO：pnpm lint会对代码全量检查，当项目复杂后执行速度可能比较慢，届时可以考虑使用lint-staged，实现只对暂存区代码进行检查

### 通过commitlint对git提交信息进行检查，首先安装必要的库：
pnpm i commitlint @commitlint/cli @commitlint/config-conventional -D -w

新建配置文件.commitlintrc.js：
module.exports = {
  extends: ["@commitlint/config-conventional"]
}; 
npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"
conventional规范集意义：

// 提交的类型: 摘要信息
<type>: <subject>

常用的type值包括如下:

feat: 添加新功能
fix: 修复 Bug
chore: 一些不影响功能的更改
docs: 专指文档的修改
perf: 性能方面的优化
refactor: 代码重构
test: 添加一些测试代码等等

## 选择打包工具
比较不同打包工具的区别 参考资料：Overview | Tooling.Report我们要开发的项目的特点：

是库，而不是业务项目
希望工具尽可能简洁、打包产物可读性高
原生支持ESM
所以选择rollup，安装：

pnpm i -D -w rollup