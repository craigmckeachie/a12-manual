## Lifecycle Hooks

A component has a lifecycle managed by Angular.

Angular **creates** and renders components along with their children, checks when their data-bound _properties_ **change**, and **destroys** them before removing them from the DOM.

Angular offers **lifecycle hooks** that provide visibility into these key life moments and the ability to act when they occur.

### Starting Point

Open `demos\` directory.

```
git checkout input-property -f
git clean -df
```

### Init & Changes

```
git checkout lifecycle-basics
```

**`src\app\app.component.ts`**

```diff
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data"></app-fruit-list>
+    <button (click)="onClickChange()">Change List</button>
  `,
  styles: [],
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
+  onClickChange() {
+    this.data = ['Banana', 'Kiwi', 'Grape'];
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
export class FruitListComponent implements OnInit,
+ OnChanges
{
  @Input()
  fruits: string[];
+  constructor() {
+    console.log('Constructor');
+  }
+  ngOnChanges(changes: SimpleChanges): void {
+    console.log('OnChanges');
+    console.log('Previous Values: ' + changes.+fruits.previousValue);
+    console.log('Current Values: ' + changes.fruits.+currentValue);
+  }
+
+  ngOnInit() {
+    console.log('OnInit');
+  }
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

### ViewChild(ren) & AfterViewInit

```
git checkout lifecycle-viewinit
```

```
ng g c hello-world
```

**`src\app\app.component.ts`**

```diff
import { Component, ViewChild, OnInit, AfterViewInit } from '@angular/core';
import { FruitListComponent } from './fruit-list/fruit-list.component';
+ import { HelloWorldComponent } from './hello-world/hello-world.component';

@Component({
  selector: 'app-root',
  template: `
+    <app-hello-world></app-hello-world>
    <app-fruit-list [fruits]="data" *ngIf="showList"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
    <br />
    <button (click)="onClickRemove()">Remove</button>
  `,
  styles: [],
})
export class AppComponent implements OnInit, AfterViewInit {
+  @ViewChild(HelloWorldComponent, { static: true })
+  helloWorldComponent!: HelloWorldComponent;
+  @ViewChild(FruitListComponent, { static: false })
+  fruitListComponent!: FruitListComponent;

  data: string[] = ['Apple', 'Orange', 'Plum'];
  showList = true;

+  ngOnInit(): void {
+    console.log('App OnInit: ');
+    console.log('ViewChild (hello):', this.helloWorldComponent);
+    console.log('ViewChild: (fruit list)', this.fruitListComponent);
+  }
+
+  ngAfterViewInit(): void {
+    console.log('App AfterViewInit: ');
+    console.log('ViewChild (hello):', this.helloWorldComponent);
+    console.log('ViewChild: (fruit list)', this.fruitListComponent);
+  }

  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
  onClickRemove() {
    this.showList = !this.showList;
  }
}
```

---

### ContentChild(ren) & AfterContentInit

- ViewChildren don’t include elements that exist within the ng-content tag.
- ContentChildren includes only elements that exists within the ng-content tag.

```
git checkout lifecycle-contentinit
```

**`src\hello-world\hello-world.component.ts`**

```diff
import {
  Component,
  OnInit,
  ContentChild,
  AfterViewInit,
  AfterContentInit,
} from '@angular/core';

@Component({
  selector: 'app-hello-world',
  template: ` <p>Hello World! My name is:
+  <ng-content></ng-content>
  </p> `,
  styles: [],
})
export class HelloWorldComponent
  implements OnInit, AfterViewInit, AfterContentInit
{
  @ContentChild('nameContent', { static: true }) nameContent: any;
  constructor() {}

+  ngOnInit() {
+    console.log(
+      'OnInit: nameContent available only if static is true. +',
+      this.nameContent
+    );
+  }
+  ngAfterContentInit() {
+    console.log('AfterContentInit: nameContent available. ', +this.nameContent);
+  }
+  ngAfterViewInit() {
+    console.log('AfterViewInit: nameContent available. ', +this.nameContent);
+  }
}
```

**`src\app\app.component.ts`**

```diff
import { Component, ViewChild, OnInit, AfterViewInit } from '@angular/core';
import { FruitListComponent } from './fruit-list/fruit-list.component';
import { HelloWorldComponent } from './hello-world/hello-world.component';

@Component({
  selector: 'app-root',
  template: `
    <app-hello-world>
+      <h2 #nameContent>Bond, James Bond</h2>
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
