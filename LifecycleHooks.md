## Lifecycle Hooks

A component has a lifecycle managed by Angular.

Angular **creates** and renders components along with their children, checks when their data-bound _properties_ **change**, and **destroys** them before removing them from the DOM.

Angular offers **lifecycle hooks** that provide visibility into these key life moments and the ability to act when they occur.

### Starting Point

Open `demos\start\` directory.

```
git checkout input-property -f
git clean -df
```

### Init & Changes

**`src\app\app.component.ts`**

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
  `,
  styles: [],
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
}
```

**`src\app\fruit-list\fruit-list.component.ts`**

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: [],
})
export class FruitListComponent implements OnInit, OnChanges {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }

  ngOnInit() {
    console.log('OnInit');
  }
}
```

**Results**

Refresh Page:

```
Constructor
OnChanges
Previous Values: undefined
Current Values: ["Apple", "Orange", "Plum"]
OnInit
```

Click Change List:

```
OnChanges
Previous Values: ["Apple", "Orange", "Plum"]
Current Values: ["Banana", "Kiwi", "Grape"]
```

### Destroy

**`src\app\app.component.ts`**

```diff
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data"
+   *ngIf="showList"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
+    <br />
+    <button (click)="onClickRemove()">Remove</button>
  `,
  styles: []
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
  showList = true;
  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
+  onClickRemove() {
+    this.showList = !this.showList;
+  }
}
```

**`src\app\fruit-list\fruit-list.component.ts`**

```diff
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
+  OnDestroy
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: []
})
export class FruitListComponent implements OnInit, OnChanges,
+ OnDestroy {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }
  ngOnInit() {
    console.log('OnInit');
  }
+  ngOnDestroy(): void {
+    console.log('Destroyed');
+  }
}
```

**Results**

Click Remove

```
Destroyed
```

### Final Code

**`src\app\app.component.ts`**

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data" *ngIf="showList"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
    <br />
    <button (click)="onClickRemove()">Remove</button>
  `,
  styles: [],
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
  showList = true;
  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
  onClickRemove() {
    this.showList = !this.showList;
  }
}
```

**`src\app\fruit-list\fruit-list.component.ts`**

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
  OnDestroy,
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: [],
})
export class FruitListComponent implements OnInit, OnChanges, OnDestroy {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }
  ngOnInit() {
    console.log('OnInit');
  }
  ngOnDestroy(): void {
    console.log('Destroyed');
  }
}
```

### ViewChild(ren) & AfterViewInit

```
ng g c hello-world
```

**`src\app\app.component.ts`**

```ts
import { Component, ViewChild, OnInit, AfterViewInit } from '@angular/core';
import { FruitListComponent } from './fruit-list/fruit-list.component';
import { HelloWorldComponent } from './hello-world/hello-world.component';

@Component({
  selector: 'app-root',
  template: `
    <app-hello-world></app-hello-world>
    <app-fruit-list [fruits]="data" *ngIf="showList"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
    <br />
    <button (click)="onClickRemove()">Remove</button>
  `,
  styles: [],
})
export class AppComponent implements OnInit, AfterViewInit {
  @ViewChild(HelloWorldComponent, { static: true }) helloWorldComponent;
  @ViewChild(FruitListComponent, { static: false }) fruitListComponent;
  data: string[] = ['Apple', 'Orange', 'Plum'];
  showList = true;

  ngOnInit(): void {
    console.log('App OnInit: ');
    console.log('ViewChild (hello):', this.helloWorldComponent);
    console.log('ViewChild: (fruit list)', this.fruitListComponent);
  }

  ngAfterViewInit(): void {
    console.log('App AfterViewInit: ');
    console.log('ViewChild (hello):', this.helloWorldComponent);
    console.log('ViewChild: (fruit list)', this.fruitListComponent);
  }

  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
  onClickRemove() {
    this.showList = !this.showList;
  }
}
```

**`src\app\fruit-list\fruit-list.component.ts`**

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
  OnDestroy,
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: [],
})
export class FruitListComponent implements OnInit, OnChanges, OnDestroy {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('FruitList OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }
  ngOnInit() {
    console.log('FruitList OnInit');
  }
  ngOnDestroy(): void {
    console.log('FruitList Destroyed');
  }
}
```

---

### ContentChild(ren) & AfterContentInit

- ViewChildren don’t include elements that exist within the ng-content tag.
- ContentChildren includes only elements that exists within the ng-content tag.

**`src\hello-world\hello-world.component.ts`**

```ts
import {
  Component,
  OnInit,
  ContentChild,
  AfterViewInit,
  AfterContentInit,
} from '@angular/core';

@Component({
  selector: 'app-hello-world',
  template: ` <p>Hello World! My name is: <ng-content></ng-content></p> `,
  styles: [],
})
export class HelloWorldComponent
  implements OnInit, AfterViewInit, AfterContentInit
{
  @ContentChild('nameContent', { static: true }) nameContent;
  constructor() {}

  ngOnInit() {
    console.log(
      'OnInit: nameContent available only if static is true. ',
      this.nameContent
    );
  }
  ngAfterContentInit() {
    console.log('AfterContentInit: nameContent available. ', this.nameContent);
  }
  ngAfterViewInit() {
    console.log('AfterViewInit: nameContent available. ', this.nameContent);
  }
}
```

**`src\app\fruit-list\fruit-list.component.ts`**

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
  OnDestroy,
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: [],
})
export class FruitListComponent implements OnInit, OnChanges, OnDestroy {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('FruitList OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }
  ngOnInit() {
    console.log('FruitList OnInit');
  }
  ngOnDestroy(): void {
    console.log('FruitList Destroyed');
  }
}
```

**`src\app\app.component.ts`**

```ts
import { Component, ViewChild, OnInit, AfterViewInit } from '@angular/core';
import { FruitListComponent } from './fruit-list/fruit-list.component';
import { HelloWorldComponent } from './hello-world/hello-world.component';

@Component({
  selector: 'app-root',
  template: `
    <app-hello-world>
      <h2 #nameContent>Bond, James Bond</h2>
    </app-hello-world>
  `,
  styles: [],
})
export class AppComponent implements OnInit, AfterViewInit {
  @ViewChild(HelloWorldComponent, { static: true }) helloWorldComponent;

  ngOnInit(): void {
    console.log('App OnInit: ');
    console.log('ViewChild (hello):', this.helloWorldComponent);
  }

  ngAfterViewInit(): void {
    console.log('App AfterViewInit: ');
    console.log('ViewChild (hello):', this.helloWorldComponent);
  }
}
```

### Reference

- [Lifecycle Hooks Documentation](https://angular.io/guide/lifecycle-hooks)
- [Understanding View Children and ContentChildren](https://netbasal.com/understanding-viewchildren-contentchildren-and-querylist-in-angular-896b0c689f6e)

<div style="page-break-after: always;"></div>

<div style="page-break-after: always;"></div>

## IVY

Ivy is the code name for Angular's [next-generation compilation and rendering pipeline](https://blog.angular.io/a-plan-for-version-8-0-and-ivy-b3318dfc19f7). Starting with Angular version 8, you can choose to opt in to start using a preview version of Ivy and help in its continuing development and tuning.

It is the default rendering engine in an Angular version 9 project.

### Using Ivy in an existing project

To update an existing project:

1. Update to Angular version 11 using the directions here:
   https://update.angular.io/
   > Choose From 8.0 to 9.x
   > Then choose from 9.x to 10.x
   > Then choose from 10.x to 11.x
   > Note: You cannot skip versions when upgrading

OR

2. Set the `enableIvy` option in the `angularCompilerOptions` in your project's `tsconfig.app.json`.

```json
{
  "compilerOptions": { ... },
  "angularCompilerOptions": {
    "enableIvy": true
  }
}
```

AOT compilation with Ivy is faster and should be used by default. In the `angular.json` workspace configuration file, set the default build options for your project to always use AOT compilation.

```json
{
  "projects": {
    "my-existing-project": {
      "architect": {
        "build": {
          "options": {
            ...
            "aot": true,
          }
        }
      }
    }
  }
}

```

To stop using the Ivy compiler, set `enableIvy` to `false` in `tsconfig.app.json`, or remove it completely. Also remove `"aot": true` from your default build options if you didn't have it there before.

## Resources

- [A Plan for Version 8 & Ivy](https://blog.angular.io/a-plan-for-version-8-0-and-ivy-b3318dfc19f7)
- [Ivy Official Documentation](https://angular.io/guide/ivy)

- [Ivy Architecture](https://github.com/angular/angular/blob/master/packages/compiler/design/architecture.md)

- [The New Ivy Compiler Finally Works on Windows](https://levelup.gitconnected.com/the-new-angular-ivy-compiler-finally-works-on-windows-9042378cede0)

- [What is Angular Ivy](https://blog.ninja-squad.com/2019/05/07/what-is-angular-ivy/)

- [Exploring the new Ivy Compiler (slightly outdated)](https://blog.angularindepth.com/inside-ivy-exploring-the-new-angular-compiler-ebf85141cee1)

<div style="page-break-after: always;"></div>

## Setup

### Creating this project

Not done in class just checkout start branch.

```
ng new demos --routing --inline-style --inline-template --skip-tests --skip-install
```

> Notice the flags --inline-style --inline-template so separate html and css files are not generated for each component.

1. Open `package.json` and remove `devDependencies` related to testing.
2. Delete e2e folder.
3. npm install

<div style="page-break-after: always;"></div>

### Creating the http-start branch

Not done in class just checkout http-start branch.

```shell
git checkout start
npm install json-server
mkdir api
```

Copy db.json from here: https://jsonplaceholder.typicode.com/db
into the api folder.

Add script to `package.json`

```diff
"scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e",
+   "api": "json-server ./api/db.json"
  },
```
