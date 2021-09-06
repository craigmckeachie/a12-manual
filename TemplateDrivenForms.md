## Template-driven Forms

### Template Forms Binding

```diff
// app.module.ts
+ import { FormsModule } from '@angular/forms';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AppRoutingModule,
+           FormsModule],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}
```

```ts
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <form #signinForm="ngForm" (submit)="onSubmit(signinForm)">
      <input type="text" ngModel name="username" placeholder="username" /><br />
      <input type="text" ngModel name="password" placeholder="password" /><br />
      <button>Sign In</button>
    </form>
    <pre>
      {{ signinForm.value | json }}
    </pre
    >
  `,
  styles: [],
})
export class AppComponent {
  onSubmit(form) {
    console.log(form.value);
  }
}
```

<div style="page-break-after: always;"></div>

### Template Forms Validation

```diff
// app.component.ts

import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
  <form #signinForm="ngForm" (submit)="onSubmit(signinForm)">
      <input
        type="text"
        placeholder="username"
        ngModel
+        #username="ngModel"
        name="username"
+        required
+        minlength="3"
      /><br />
+      <pre>
+        {{ username.errors | json }}
+      </pre
      >
+      <br />
+      Dirty: {{ username.dirty | json }} <br />
+      Touched: {{ username.touched | json }} <br />
      <input type="text" ngModel name="password" placeholder="password" /><br />
      <button>Sign In</button>
    </form>
  `,
  styles: []
})
export class AppComponent {
  ...
}
```

<div style="page-break-after: always;"></div>

#### Displaying Validation Messages

```diff
// app.component.ts

@Component({
  selector: 'app-root',
  template: `
    <form #signinForm="ngForm" (submit)="onSubmit(signinForm)">
      <input
        type="text"
        placeholder="username"
        ngModel
        #username="ngModel"
        name="username"
        required
        minlength="3"
      /><br />
+      <div *ngIf="username.hasError('required') && username.dirty">
+        Username is required.
+      </div>

-      <pre>
-        {{ username.errors | json }}
-      </pre>
-      <br />
-      Dirty: {{ username.dirty | json }} <br />
-      Touched: {{ username.touched | json }} <br />
      <input type="text" ngModel name="password" placeholder="password" /><br />

      <button>Sign In</button>
    </form>
  `,
  styles: []
})
export class AppComponent {
...
}
```

<div style="page-break-after: always;"></div>

### Two-way Binding

```ts
@Component({
  selector: 'app-root',
  template: `
    <input
      [value]="message"
      (input)="message = $event.target.value"
      type="text"
    />
    <p>{{ message }}</p>
  `,
  styles: [],
})
export class AppComponent {
  message = '';
}
```

```ts
import { FormsModule } from '@angular/forms';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, AppRoutingModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

```ts
@Component({
  selector: 'app-root',
  template: `
    <input [(ngModel)]="message" type="text" />
    <p>{{ message }}</p>
  `,
  styles: [],
})
export class AppComponent {
  message = '';
}
```
