# 1. IContainer

`interface of container which store singleton instance of service, controller => default of platform is "singletonContainer"`

```
export interface IContainer<ID extends any = any> {
  bind<T>(name: ID, instance: T): void;
  unbind(name: ID): void;
  get<T>(name: ID): T;
}
```
