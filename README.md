## 初始化
- ``` yarn init ```
- 新建 ``` pnpm-workspace.yaml ```

定义 工作空间 的根目录，并能够使您从工作空间中包含 / 排除目录 。 默认情况下，包含所有子目录

- ``` package.json ```
在项目中使用 pnpm 时，您不希望被其他人意外运行 npm install 或 yarn。 为了防止开发人员使用其他的包管理器，您可以将下面的这个 preinstall 脚本添加到您的 package.json

## 安装依赖
> 使用工作空间搭建的项目，可以将公共依赖安装在根目录下，子项目中存放各自的依赖

### 公共依赖全局安装

``` -w flag ( --workspace-root) ```
``` pnpm install @types/node -wD```

在子项目 package.json 文件中 声明使用公共依赖

```
    "peerDependencies": {
        "@types/node": "*"
    }
```

全局安装所有 工作空间依赖
``` pnpm install ```

### 指定 package 依赖安装

使用 --filter 参数，对指定的 package 进行操作。如下指定 foo 工程安装 包

``` pnpm install element-plus -D --filter foo ```

### 子项目之间互相引用
foo
```
...
{
    "name": "@foo/utils",
    "main": "index.js",
}
```

bar

``` import { add } from '@foo/utils' ```

```
// package.json
"dependencies": {
    "@foo/utils": "workspace:*"
}
```

## 发布 changesets
https://pnpm.io/zh/using-changesets

### 配置 ###
要在 pnpm 工作空间上配置 changesets，将 changesets 作为开发依赖项安装在工作空间的根目录中：

$ pnpm add -Dw @changesets/cli

$ pnpm changeset init

### 添加新的 changesets ###

要生成新的 changesets，请在仓库的根目录中执行 ```pnpm changeset```
```.changeset```目录中生成的 markdown 文件```red-cherries-build```需要被提交到仓库 

### 发布 ###
1、运行 ```pnpm changeset version``` 这将提高先前使用 ```pnpm changeset```（以及它们的任何依赖项）的版本，并更新变更日志文件

2、运行 ``` pnpm install ``` 这将更新锁文件并重新构建所有 workspace 的包
3、提交更改
4、运行``` pnpm publish -r ``` 此命令将发布所有包含被更新版本且尚未出现在包注册源中的包

