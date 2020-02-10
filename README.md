# NgrxFundamentals

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 8.3.9.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).

# TODO Aplication

Type the following in order to instantiate a new ANgular application:

`ng new <project-name>`

A Todo application could therefore look like the following:

```
   AppComponent
     TodoList
       TodoItem
       TodoItem
       TodoItem
       ...
```

Let's start to create this app, starting with the `AppComponent`. As this is the topmost component, it is also referred to as the root component. The `AppComponent` should render the `TodoListComponent` in its own template, like so:

```typescript
// mvc/MvcExample/src/app/app.component.ts
import { Component } from "@angular/core";
@Component({
  selector: "app-root",
  template: `
    <todo-list></todo-list>
  `,
  styleUrls: ["./app.component.css"]
})
export class AppComponent {
  title = "app";
}
```

The next step is defining the `TodoListComponent` and knowing that it should be able to render a number of `TodoItemComponent` instances within its template. The size of a list is usually unknown. This is exactly what the structural directive `*ngFor` is for. So that is what we will utilize in the following code as we define the `TodoListComponent`:

```typescript
// mvc/MvcExample/src/app/todo-list.component.ts
import { Component } from "@angular/core";

@Component({
  selector: "todo-list",
  template: `
    <h1>{{ title }}</h1>
    <custom></custom>
    <div *ngFor="let todo of todos">
      <todo-item [todo]="todo"></todo-item>
    </div>
  ` // the view
})
export class TodoListComponent {
  // the controller class
  title: string;
  todos = [
    {
      title: "todo1"
    },
    {
      title: "todo1"
    }
  ];
}
```

Here, we can see that we render out a list of todo items by looping out the `todos` array in the template.

We can see in the preceding code that we are rendering out the `todo-item` selector, which points to a `TodoItemComponent` that we are yet to define. Worth noting is how we pass it a todo object and assign it to an input property on the `TodoItemComponent`. The definition for said component is as follows:

```typescript
// mvc/MvcExample/src/app/todo-item.component.ts
import { Component, Input } from "@angular/core";

@Component({
  selector: "todo-item",
  template: `
    <h1>{{ todo.title }}</h1>
  ` // the view
})
export class TodoItemComponent {
  // the class
  @Input() todo;
}
```

Reasoning about which components should exist as part of which other components is something you are going to dedicate a lot of time to.

## Components from an architectural standpoint

Components are meant to be used to solve one thing well. That's why we created a `TodoItemComponent` which only job in life was to display a todo item. Same thing goes for the `TodoListComponent`. It should only care about displaying a list, nothing else. The more you split down your applications into small and focused areas the better.

### Angular Modules

An Angular module is responsible for knowing where all your constructs are located. This also means that were you to move files around, changing directories for certain constructs, the `app.module.ts` file is the only one where updating the paths to these constructs is needed.

An Angular module looks like the following:

```typescript
// mvc/MvcExample/src/app/my/my.module.ts
import { NgModule } from "@angular/core";
import { MyComponent } from "./my.component";
import { MyPipe } from "./my.pipe";

@NgModule({
  imports: [],
  exports: [MyComponent],
  declarations: [MyComponent, MyPipe],
  providers: []
})
export class MyModule {}
```

The effect of putting components into the declarations property of the module is so that these components can be freely used within the module. For example, you can use `MyPipe` within the `MyComponent` template. However, if you want to use `MyComponent` outside of this module, in a component belonging to another module, you will need to export it. We do that by placing it in the array belonging to the exports property.

Angular takes the concept of a module way beyond grouping. Some instructions in our `NgModule` are meant for the compiler so that it knows how to assemble the components. Some other instructions we give it are meant for the Dependency Injection tree. Think of the Angular module as a configuration point, but also as the place where you logically divide up your application in to cohesive blocks of code.

- The **declarations** property is an array that states what belongs to our module
- The **imports** property is an array that states what other Angular modules we are dependent on; it could be basic Angular directives or common functionality that we want to use inside of our module
- The **exports** property is an array stating what should be made available for any module importing this module; `MyComponent` is made public whereas `MyPipe` would become private for this module only
- The **providers** property is an array stating what services should be injectable into constructs belonging to this module, that is, to constructs that are listed in the declarations array

## Consuming a Module

In ES2015, we use the `import` and `from` keywords to import one or several constructs like so:

`import { SomeConstruct } from './module';`

The imported file looks like this:

`export let SomeConstruct = 5;`

In the example below, we see that we import the functionality we need and we end up exporting this class, thereby making it available for other constructs to consume.

```typescript
import { Component } from "@angular/core";
@Component({
  selector: "example"
})
export class ExampleComponent {}
```

The pipe, directive, and filter all follow the same pattern of importing what they need and exporting themselves to be included as part of an `NgModule`.

## Multiple exports

So far, we have only shown how to export one construct. It is possible to export multiple things from one module by adding an `export` keyword next to all constructs that you wish to export, like so:

```typescript
export class Math {
  add() {}
  subtract() {}
}
export const PI = 3.14;
```

Essentially, for everything you want to make public you need to add an `export` keyword at the start of it. There is an alternate syntax, where instead of adding an `export` keyword to every construct, we can instead define within curly brackets what constructs should be exported. It looks like this:

```typescript
class Math {
  add() {}
  subtract() {}
}
const PI = 3.14;
export { Math, PI };
```

Whether you put export in front of every construct or you place them all in an `export {}`, then end result is the same, it's just a matter of taste which one to use. To consume constructs from this module, we would type:

`import { Math, PI } from './module';`

Here, we have the option of specifying what we want to `import`. In the previous example, we have opted to export both `Math` and `PI`, but we could be content with only exporting `Math`, for example; it is up to us.

## The default import/export

So far, we have been very explicit with what we import and what we export. We can, however, create a so-called default export, which looks somewhat different to consume:

```typescript
export default class Player {
  attack() {}
  move() {}
}
export const PI = 3.13;
```

To consume this, we can write the following:

```typescript
import Player from "./module";
import { PI } from "./module";
```

Note especially the first row where we no longer use the curly brackets, `{}`, to import a specific construct. We just use a name that we make up. In the second row, we have to name it correctly as `PI`, but in the first row we can choose the name. The player points to what we exported as default, that is, the `Player` class. As you can see, we can still use the normal curly brackets, `{}`, to import specific constructs if we want to.

## Renaming Imports

Sometimes we may get a collision, with constructs being named the same. We could have this happening:

```typescript
import { productService } from "./module1/service";
import { productService } from "./module2/service"; // name collision
```

This is a situation we need to resolve. We can resolve it using the `as` keyword, like so:

```typescript
import { productService as m1_productService }
import { productService as m2_productService }
```

Thanks to the `as` keyword, the compiler now has no problem differentiating what is what.

## Services

Services can be of two types:

- Services without dependencies
- Services with dependencies

### Service without dependencies

A service without dependencies is a service whose constructor is empty:

```typescript
export Service {
  constructor(){}
  getData() {}
}
```

To use it, you simply type:

```typescript
import { Service } from "./service";
let service = new Service();
service.getData();
```

Any module that consumes this service will get their own copy of the code, with this kind of code. If you, however, want consumers to share a common instance, you change the service module definition slightly to this:

```typescript
class Service {
  constructor() {}
  getData() {}
}
const service = new Service();
export default service;
```

Here, we export an instance of the service rather than the service declaration.

### Service With Dependencies

A service with dependencies has dependencies in the constructor that we need help resolving. Without this resolution process, we can't create the service. Such a service may look like this:

```typescript
export class Service {
  constructor(
    Logger logger: Logger,
    repository:Repository
  ) {}
}
```

In this code, our service has two dependencies. Upon constructing a service, we need one `Logger` instance and one `Repository` instance. It would be entirely possible for us to find the `Logger` instance and `Repository` instance by typing something like this:

```typescript
import { Service } from "./service";
import logger from "./logger";
import { Repository } from "./repository";
// create the service
let service = new Service(logger, new Repository());
```

This is absolutely possible to do. However, the code is a bit tedious to write every time I want a service instance. When you start to have 100s of classes with deep object dependencies, a DI system quickly pays off.

This is one thing a Dependency Injection library helps you with, even if it is not the main motivator behind its existence. The main motivator for a DI system is to create loose coupling between different parts of the system and rely on contracts rather than concrete implementations. Take our example with the service. There are two things a DI can help us with:

- Switch out one concrete implementation for another
- Easily test our construct

To show what I mean, let's first assume `Logger` and `Repository` are interfaces. Interfaces may be implemented differently by different concrete classes, like so:

```typescript
import { Service } from "./service";
import logger from "./logger";
import { Repository } from "./repository";
class FileLogger implements Logger {
  log(message: string) {
    // write to a file
  }
}
class ConsoleLogger implements Logger {
  log(message: string) {
    console.log("message", message);
  }
}
// create the service
let service = new Service(new FileLogger(), new Repository());
```

This code shows how easy it is to switch out the implementation of `Logger` by just choosing `FileLogger` over `ConsoleLogger` or vice versa. The test case is also made a lot easier if you only rely on dependencies coming from the outside, so that everything can therefore be mocked.

## Dependency Injection

Essentially, when we ask for a construct instance, we want help constructing it. A DI system can act in one of two ways when asked to resolve an instance:

- **Transient mode**: The dependency is always created anew
- **Singleton mode**: The dependency is reused

Angular only creates singletons though which means every time we ask for a dependency it will only be created once and we will be given an already existing dependency if we are not the first construct to ask for that dependency.

The default behavior of any DI framework is to use the default constructor on a class and create an instance from a class. If that class has dependencies, then it has to resolve those first. Imagine we have the following case:

```typescript
export class Logger {}
export class Service {
  constructor(logger: Logger) {}
}
```

The DI framework would crawl the chain of dependencies, find the construct that does not have any dependencies, and instantiate that first. Then it would crawl upwards and finally resolve the construct you asked for. So with this code:

```typescript
import { Service } from "./service";
export class ExampleComponent {
  constructor(srv: Service) {}
}
```

The DI framework would:

- Instantiate the logger first
- Instantiate the service second
- Instantiate the component third

## Dependency Injection in Angular using providers

So far we have only discussed Dependency Injection in general, but Angular has some constructs,
or decorators, to ensure that Dependency Injection does its job. First imagine a simple scenario,
a service with no dependencies:

```typescript
export class SimpleService {}
```

If a component exists that requires an instance of the service, like so:

```typescript
@Component({
  selector: "component"
})
export class ExampleComponent {
  constructor(srv: Service) {}
}
```

The Angular Dependency Injection system comes in and attempts to resolve it.
Because the service has no dependencies, the solution is as simple as instantiating `Service`,
and Angular does this for us. However, we need to tell Angular about this construct for the DI machinery
to work. The thing that needs to know this is called a provider. Both Angular modules and components have
access to a providers array that we can add the `Service` construct to. A word on this though.
Since the arrival of Angular modules, the recommendation is to **not use the providers array for components**.

This will ensure that a Service instance is being created and injected at the right place, when asked for. Let's tell an Angular module about a service construct:

```typescript
import { Service } from "./Service";
@NgModule({
  providers: [Service]
})
export class FeatureModule {}
```

This is usually enough to make it work. You can, however, register the `Service` construct with the
component class instead. It looks identical:

```typescript
@Component({
  providers: [Service]
})
export ExampleComponent {}
```

This has a different effect though. You will tell the DI machinery about this construct and it will
be able to resolve it. There is a limitation, however. It will only be able to resolve it for this
component and all its view child components. Some may see this as a way of limiting what components
can see what services and therefore see it as a feature. Let me explain that by showing when the
DI machinery can figure out our provided service:

**Everybody's parent – it works:** Here, we can see that as long as the component highest up declares
`Service` as a provider, all the following components are able to inject `Service`:

```
AppComponent // Service added here, Can resolve Service
  TodosComponent // Can resolve Service
    TodoComponent // Can resolve Service
```

Let's exemplify this with some code:

```typescript
// example code on how DI works for Component providers
// app.component.ts
@Component({
  providers: [Service] // < - provided,
  template : `<todos></todos>`
})
export class AppComponent {}

// todos.component.tsx§
@Component({
  template : `<todo></todo>`,
  selector: 'todos'
})
export class TodosComponent {
  // this works
  constructor(private service: Service) {}
}
// todo.component.ts
@Component({
  selector: 'todo',
  template: `todo component `
})
export class TodoComponent {
  // this works
  constructor(private service: Service) {}
}
```

**TodosComponent – will work for its children but not higher up:** Here, we provide Service one level
down, to `TodosComponent`. This makes `Service` available to the child components of `TodosComponent`
but `AppComponent`, its parent, misses out:

```
AppComponent // Does not know about Service
  TodosComponent // Service added here, Can resolve Service
    TodoComponent // Can resolve Service
```

Let's try to show this in code:

```typescript
// this is example code on how it works
// app.component.ts
@Component({
  selector: 'app',
  template: `<todos></todos>`
})
export class AppComponent {
  // does NOT work, only TodosComponent and below knows about Service
  constructor(private service: Service) {}
}

// todos.component.ts
@Component({
  selector: 'todos', template: `<todo></todo>` providers: [Service]
})
export class TodosComponent {
  // this works
  constructor(private service: Service) {}
}

// todo.component.ts
@Component({
  selector: 'todo',
  template: `a todo`
})
export class TodoComponent {
  // this works
  constructor(private service: Service) {}
}
```

We can see here that adding our `Service` to a component's providers array has limitations.
Adding it to an Angular module is the sure way to ensure it can be resolved by all constructs
residing inside of that array. This is not all though.
Adding our `Service` to an Angular module's providers array ensures it is accessible throughout our
entire application. How is that possible, you ask? It has to do with the module system itself.
Imagine we have the following Angular modules in our application:

```
AppModule
SharedModule
```

For it to be possible to use our `SharedModule`, we need to import it into `AppModule` by adding
it to the imports array of `AppModule`, like so:

```typescript
//app.module.ts
@NgModule({
  imports: [SharedModule],
  providers: [AppService]
})
export class AppModule {}
```

We know this has the effect of pulling all constructs from the exports array in `SharedModule`,
but this will also concatenate the providers array from `SharedModule` to that of `AppModule`.
Imagine `SharedModule` looking something like this:

```typescript
//shared.module.ts
@NgModule({
  providers: [SharedService]
})
export class SharedModule {}
```

After the import has taken place, the combined providers array now contains:

```
AppService
SharedService
```

So the rule of thumb here is if you want to expose a service to your application, then put it in the
Angular module's providers array. If you want to limit access to the service, then place it into a
component's providers array. Then, you will ensure it can only be reached by that component and its
view children.

## Overriding an existing construct

There are cases when you want to override the default resolution of your construct. You can do so at the module level, but also at the component level. What you do is simply express which construct you are overriding and with which other construct. It looks like this:

```typescript
@Component({
  providers: [
    { provide: Service, useClass : FakeService }
  ]
})
```

The `provide` is our known construct and `useClass` is what it should point to instead. Let's
imagine we implemented our `Service` like so:

```typescript
export class Service {
  no: number = 0;
  constructor() {}
}
```

And we added the following override to a component:

```typescript
@Component({
  providers: [{ provide : Service, useClass: FakeService }]
})
```

The `FakeService` class has the following implementation:

```typescript
export class FakeService {
  set no(value) {
    // do nothing
  }
  get no() {
    return 99;
  }
}
```

Now the component and all its view child components will always get FakeService when asking for the Service construct.

## Overriding at runtime

There is a way to decide what to inject for/into a construct at runtime.
So far, we have been very explicit about when to override, but we can do this with a bit of logic added to it by using the
`useFactory` keyword. It works like the following:

```typescript
let factory = () => { if(condition) {
       return new FakeService();
     } else {
       return new Service();
     }
}
   @Component({
    providers : [
{ provide : Service, useFactory : factory } ]
})
```

This factory can in itself have dependencies; we specify those dependencies with the `deps` keyword like so:

```typescript
let factory = (auth:AuthService, logger: Logger) => {
  if(condition) {
    return new FakeService();
  } else {
    return new Service();
  }
}

@Component({
  providers : [
    { provide : Service, useFactory : factory,
      deps: [AuthService, Logger] }
  ]
})
```

Here, we highlighted the `condition` variable, which is a Boolean. There can be a ton of reasons why we would want to be able to
switch the implementation. One good case is when the endpoint don't exist yet and we want to ensure it calls our `FakeService`
instead. Another reason could be that we are in testing mode and by just changing this one variable we can make all our services
rely on a fake version of themselves.

## Overriding constants

Not everything, though, is a class that needs to be resolved; sometimes it is a constant.
For those cases, instead of using `useClass`, we can use `useValue`, like so:

`providers: [ { provide: 'a-string-token', useValue: 12345678 } ]`

This is not really a class type, so you can't write this in a constructor:

`constructor(a-string-token) . // will not compile`

That wouldn't compile. What we can do instead is to use the `@Inject` decorator in the following way:

```typescript
constructor( @Inject('a-string-token') token) // token will have value
12345678
```

The `useValue` is no different from `useClass` when it comes to how to override it.
The difference is of course that we need to type `useValue` in our instruction to override rather than `useClass`.

## Resolving your dependencies with @Injectable

`@Injectable` is not strictly mandatory to use for services in general. However, if that service has dependencies, then it needs to be used. Failure to decorate a service with `@Injectable` that has dependencies leads to an error where the compiler complains that it doesn't know how to construct the mentioned service. Let's look at a case where we need to use the `@Injectable` decorator:

```typescript
import { Injectable } from "@angular/core";
@Injectable()
export class Service {
  constructor(logger: Logger) {}
}
```

In this case, Angular's DI machinery will look up `Logger` and inject it into the `Service` constructor. So, providing we have done this:

`providers: [Service, Logger]`

In a component or module, it should work. Remember, when in doubt, add `@Injectable` to your service if it has dependencies in the constructor or will have in the near future. If your service lacks the `@Injectable` keyword and you try to inject it into a component's constructor, then it will throw an error and your component will not be created.

# Fetching and persisting data with HTTP – Introducing services with Observables


