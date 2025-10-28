---
title: type命令
---
`type`命令用来定义一个类型的别名。

```ts
type Age = number;
let age:Age = 55;
```

上面示例中，`type`命令为`number`类型定义了一个别名`Age`。这样就能像使用`number`一样，使用`Age`作为类型。

别名可以让类型的名字变得更有意义，也能增加代码的可读性，还可以使复杂类型用起来更方便，便于以后修改变量的类型。

别名不允许重名。

```ts
type Color = 'red';
type Color = 'blue'; // 报错
```

上面示例中，同一个别名`Color`声明了两次，就报错了。

别名的作用域是块级作用域。这意味着，代码块内部定义的别名，影响不到外部。

```ts
type Color = 'red';
if (Math.random() < 0.5) {
	type Color = 'blue';
}
```

上面示例中，`if`代码块内部的类型别名`Color`，跟外部的`Color`是不一样的。

别名支持使用表达式，也可以在定义一个别名时，使用另一个别名，即别名允许嵌套。

```ts
type World = "world";
type Greeting = `hello ${World}`;
```

上面示例中，别名`Greeting`使用了模板字符串，读取另一个别名`World`。

`type`命令属于类型相关的代码，编译成 JavaScript 的时候，会被全部删除。


# 类型定义示例

```ts
export declare type MenuRouteObject = {
    icon?: React.ReactNode;
    label?: string;
    children?: MenuRouteObject[] | null;
} & RouteObject;
```

这是一个 **类型定义（Type Alias）**，表示一个路由对象，它继承了 `RouteObject` 类型，并扩展了几个用于菜单显示的字段：

- `icon?: React.ReactNode`：菜单图标，可以是任意 React 元素，比如 `<PieChartOutlined />`
    
- `label?: string`：菜单的显示名称
    
- `children?: MenuRouteObject[] | null`：子菜单或子路由，递归地使用 `MenuRouteObject` 类型
    

它通过 `& RouteObject` 组合了 React Router 的标准路由配置类型 `RouteObject`，所以它既是一个路由配置，也可以用于构建菜单。

```ts
const portalRouters: MenuRouteObject[] = [
    {...welcome},
    {...user},
    {...system},
    {...test},
];
```

这是一个 **数组类型**，表示由多个 `MenuRouteObject` 组成的列表
这表示 `portalRouters` 是一个数组，里面每个元素都符合 `MenuRouteObject` 的结构。

```ts
type Age = number;
const ages: Age[] = [16, 20, 30];
```