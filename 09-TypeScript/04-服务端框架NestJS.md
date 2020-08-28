## 一 Nest 简介

Nest 是使用 TypeScript 的 Node 服务端框架，其内核是 Express。

Nest 的中文文档：https://docs.nestjs.cn/

与 Express、Koa 不同，Nest 具备了大量企业级开发特性，这些特性往往与 Java 中的 Spring 框架开发思想相似：

- 基于 TypeScript 语言开发，具备类型校验功能，也有更便利的语法联想，适合大型工程项目
- 具备依赖注入（DI）、控制反转（IOC）功能，解耦了业务代码
- 提供了大量 cli 工具命令与第三方模块，开发起来相对方便
- 支持函数式、面向对象、函数式响应编程等范式

Express 与 Koa 适合小型项目，如个人博客等，但是其约定很差，项目格式会因为不同开发者的个人风格造成项目风格千奇百怪，阿里的 egg 虽然也做了约束，但是仍然只适合普通项目。Nest 是 NodeJS 真正意义上的第一个 Web 框架。

## 二 Nest7.0 初步使用

### 2.1 环境搭建

```
# 环境要求
node -v         # v12.10.0
npm -v          # 6.10.3

# 安装nest脚手架
npm i -g @nestjs/cli

# 创建nest项目
nest new demo

# 启动
cd demo
npm run start:dev           # :参数为启动形式，:dev支持热重启

# 访问
http://localhost:3000
```

### 2.2 目录分析

```
src
├── main.ts                 # 入口：使用NestFactory 创建了实例
├── app.module.ts           # 应用程序的根模块
├── app.controller.ts       # 带有单个路由的基本控制器示例
├── app.controller.spec.ts  # 具备拦截功能
├── app.service.ts          # 服务层示例
```

nest 推荐将项目按照 业务模块进行划分（module），比如`user.module.ts`，`order.module.ts`，在每个模块下进行控制层、服务层的开发。

### 2.3 nest cli 命令使用

nest 无需手动创建目录，并为目录中的 controller、service、module 添加依赖关系，而是使用 cli 命令即可自动完成上述功能：

```
# 创建模块
nest g mo user          # g 即 generator，mo是 module 的缩写

# 在模块基础上创建控制层
nest g co user          # co 是 controller 的缩写

# 在模块基础上创建服务层
nest g s user           # s 是 service 的缩写
```

贴士：`nest --help` 可以查看缩写规范。

### 2.4 接口书写

## 三 一些工具的继承

### 2.1 swagger

nest 支持 swagger 接口文档：

```
npm i --save @nestjs/swagger swagger-ui-express
```

在 main.ts 中配置：

```ts
import { NestFactory } from "@nestjs/core";
import { SwaggerModule, DocumentBuilder } from "@nestjs/swagger";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // 配置 swagger
  const docOptions = new DocumentBuilder()
    .setTitle("Demo")
    .setDescription("demo...")
    .setVersion("1.0")
    .addTag("demo")
    .build();
  const document = SwaggerModule.createDocument(app, docOptions);
  SwaggerModule.setup("api-docs", app, document);

  await app.listen(3000);
}
bootstrap();
```

在控制器中书写接口信息：

```ts
@Controller("user")
@ApiTags("user")
export class UserController {
  @Get()
  @ApiOperation({ summary: "用户首页" })
  index() {
    return "user.html";
  }
}
```

访问接口信息：

```
http://localhost:3000/api-docs/
```
