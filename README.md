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