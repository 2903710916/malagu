# Malagu

Malagu 是基于 TypeScript 的 Serverless First、可扩展和组件化的应用框架。

**主要特点：**

1. 基于 TypeScript
1. 零配置
1. NodeJs 版 Spring Boot
1. Serverless First
1. 组件化
1. 前后端一体化
1. 面向切面编程（AOP）
1. 集成了 ORM 框架
1. 命令工具插件化

Malagu 名字由来：在我的家乡，谐音“吗啦咕”是小石头的意思，小石头堆砌起来可以建成高楼大厦、道路桥梁，而 Malagu 组件编排可以实现千变万化的应用。

## 依赖注入

```typescript
@Component()
export class A {

}

@Component()
export class B {
    @Autowired()
    protected a: A;
}
```

## 属性注入

```typescript
@Component()
export class A {
    @Value('foo') // 支持 EL 表达式语法，如 @Value('obj.xxx')、@Value('arr[1]') 等等
    protected foo: string;
}
```

## MVC

```typescript
@Controller('users')
export class UserController {
    
    @Get()
    list(): Promise<User[]> {
        ...
    }

    @Get(':id')
    get(@Param('id') id: number): Promise<User | undefined> {
        ...
    }

    @Delete(':id')
    async reomve(@Param('id') id: number): Promise<void> {
        ...
    }

    @Put()
    async modify(@Body() user: User): Promise<void> {
        ...
    }

    @Post()
    create(@Body() user: User): Promise<User> {
        ...
    }

}
```

## 数据库操作

```typescript
import { Controller, Get, Param, Delete, Put, Post, Body } from '@malagu/mvc/lib/node';
import { Transactional, OrmContext } from '@malagu/typeorm/lib/node';
import { User } from './entity';
@Controller('users')
export class UserController {
    
    @Get()
    @Transactional({ readOnly: true })
    list(): Promise<User[]> {
        const repo = OrmContext.getRepository(User);
        return repo.find();
    }
    @Get(':id')
    @Transactional({ readOnly: true })
    get(@Param('id') id: number): Promise<User | undefined> {
        const repo = OrmContext.getRepository(User);
        return repo.findOne(id);
    }
    @Delete(':id')
    @Transactional()
    async reomve(@Param('id') id: number): Promise<void> {
        const repo = OrmContext.getRepository(User);
        await repo.delete(id);
    }
    @Put()
    @Transactional()
    async modify(@Body() user: User): Promise<void> {
        const repo = OrmContext.getRepository(User);
        await repo.update(user.id, user);
    }
    @Post()
    @Transactional()
    create(@Body() user: User): Promise<User> {
        const repo = OrmContext.getRepository(User);
        return repo.save(user);
    }
}
```
