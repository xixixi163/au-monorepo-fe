## 什么是 pnpm？
pnpm 是新一代的包管理工具，号称是最先进的包管理器。按照官网说法，可以实现节约磁盘空间并提升安装速度和创建非扁平化的 node_modules 文件夹两大目标，具体原理可以参考 pnpm 官网。

pnpm 提出了 workspace 的概念，内置了对 monorepo 的支持，那么为什么要用 pnpm 取代之前的 lerna 呢？

- lerna 已经不再维护，后续有任何问题社区无法及时响应
- pnpm装包效率更高，并且可以节约更多磁盘空间
- pnpm本身就预置了对monorepo的支持，不需要再额外第三方包的支持

## 初始化
- ``` yarn init ```
- 新建 ``` pnpm-workspace.yaml ```

定义 工作空间 的根目录，并能够使您从工作空间中包含 / 排除目录 。 默认情况下，包含所有子目录

- ``` package.json ```
在项目中使用 pnpm 时，您不希望被其他人意外运行 npm install 或 yarn。 为了防止开发人员使用其他的包管理器，您可以将下面的这个 preinstall 脚本添加到您的 package.json

- 为了防止根目录被发布出去，###不想被意外发布到公共 npm 仓库时###，需要设置工程根目录下 package.json配置文件的 private 字段为 true。

当我们的项目需要发布到私有的 npm 仓库时(比如公司内网的仓库)，需要设置 ```publishConfig``` 对象。

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

使用 --filter 参数，对指定的 package 进行操作。如下指定 foo 工程安装 包。

```--filter``` 参数跟着的是package下的 package.json 的 name 字段，并不是目录名

``` pnpm install element-plus -D --filter foo ```

### 执行 pkg1 下的 scripts 脚本 ###
```$ pnpm build --filter @qftjs/monorepo1```

### ```filter``` 后面除了可以指定具体的包名，还可以跟着匹配规则来指定对匹配上规则的包进行操作，比如：###
```$ pnpm build --filter "./packages/**"```

### 安装对应工程的包 ###
```pnpm i --filter demo1```

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

### 命令 ###
packages 内部进行互相引用。比如在 pkg1 中引用 pkg2
```$ pnpm install @qftjs/monorepo2 -r --filter @qftjs/monorepo1```

在设置依赖版本的时候推荐用 ```workspace:*```，这样就可以保持依赖的版本是工作空间里最新版本，不需要每次手动更新依赖版本。

当 `pnpm publish` 的时候，会自动将 `package.json` 中的 `workspace` 修正为对应的版本号。

- #所以有个问题# ，仓库是`private`情况，只能提交代码，提交的是`workspace`。碰到非组件库不需要发包的项目则提交直接是workspace？那是否会影响打包


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

### 版本号更改 ###
- 命令选择不了 patch major minor 时，进入 `.changeset` 文件修改字段
```
{
  "summary": "Fix a bug",
  "releaseType": "patch"
}
```

### 问题 
##### 对单独workspace 发布 ```pnpm publish --access public``` 报错404 发布不上
如果项目设置了 private，要发布到 npm，需要加发布配置：
```
"publishConfig": {
    "access": "public"
}
```

包命名：如果命名与npm上有重合或相似会导致发布失败，包名类似，拒绝发布，包名就是package.json 中的name，需要重新命名
使用作用域命名：
- 如果因为包名与现有的包名太相近而被阻止发布这个包，那么找到一个独一无二包名最简单方法就是使用自己的作用域。
- 使用@+npm用户名加在包名前面将包划到你的npm账户作用域下
- 被划了作用域的包默认是私有的，发布配置加上public可公开发布，或通过```npm publish --access=public```让它变为公有的包 

执行 pnpm publish

删除npm包

npm unpublish --force //强制删除

npm unpublish guitest@1.0.1 //指定版本号



