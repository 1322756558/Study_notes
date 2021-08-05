1. 初始化npm

> npm init

2. yarn 装包

> yarn add koa koa-router  
>
> yarn add @types/koa @types/koa-router  --dev

安装热编译环境

> yarn add typescript ts-node nodemon --dev

如果无法启动说明未全局安装 ts-node 和 nodemon

> yarn global add ts-node nodemon

3. 配置package.json

> "scripts": {
>
> ​    "start": "nodemon --watch src -e ts,tsx --exec ts-node src/server.ts"
>
>  }

4. 添加tsconfig.json

```js
{
  "compilerOptions": {
    // 编译目标
    "target": "ES2017",
    // 模板标准
    "module": "commonjs",
    // 包含在编译中的库文件
    "lib": ["es6"],
    // 编译时是否生成.map文件
    "sourceMap": true,
    // 输出文件夹
    "outDir": "dist",
    // 未指定类型是否报错 false会默认赋值any
    "noImplicitAny": true,
    "strictNullChecks": true,
    "moduleResolution": "node",
    "baseUrl": ".",
    "paths": {
      "*": ["node_modules/*"]
    },
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"]
}

```

5.

