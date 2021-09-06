## Redux (NgRx)

### Redux (NgRx) Counter

Documentation available at:
https://ngrx.io/

#### Setup

1. Open: `demos\start`
1. Run `npm install` if you haven't earlier in the class.

#### Installation

1. Install @ngrx/schematics from npm:

   ```
   ng add @ngrx/schematics
   ```

1. This will ask you if you want to make the @ngrx/schematics the default collection in your Angular CLI project. Choose `n` for no in this demo.

   > NgRx Schematics helps you avoid writing common boilerplate and instead focus on building your application
   > The @ngrx/schematics command prefix is only needed when the default collection isn't set. For example, the command `ng g @ngrx/schematics:action Counter` could just be `ng g action Counter`

1. After installing @ngrx/schematics, install the NgRx dependencies.

   ```
   ng add @ngrx/store
   ng add @ngrx/store-devtools
   ```

   In more complex applications, you may also want to install the follow NgRx libraries but there is no need to run the below commands in this example.

   ```
   # DO NOT RUN THESE COMMANDS
   npm add @ngrx/effects
   npm add @ngrx/entity
   ```

<div style="page-break-after: always;"></div>

#### Store

Generate the initial state management and register it within the app.module.ts

```
ng generate @ngrx/schematics:store State --root --module app.module.ts
```

> If this command doesn't work try adding `ng add @ngrx/schematics` again.

> By adding the StoreModule.forRoot function in the imports array of your AppModule. The StoreModule.forRoot() method registers the global providers needed to access the Store throughout your application.

#### Actions

Create actions.

1. Create the directories `src\app\counter\` and `src\app\counter\shared`.
2. Create a file named `src\app\counter\shared\counter.actions.ts`

3. Describe the counter actions to increment, decrement, and reset its value.

```ts
// src\app\counter\shared\counter.actions.ts
import { createAction } from '@ngrx/store';

export const increment = createAction('[Counter] Increment');
export const decrement = createAction('[Counter] Decrement');
export const reset = createAction('[Counter] Reset');
```

<div style="page-break-after: always;"></div>

#### Reducer

Create a reducer.

1. Create a file named `src\app\counter\shared\counter.reducer.ts`

   Define a reducer function to handle changes in the counter value based on the provided actions.

   ```ts
   // src\app\counter\shared\counter.reducer.ts
   import { createReducer, on } from '@ngrx/store';
   import { increment, decrement, reset } from './counter.actions';

   export const initialState = 0;

   const _counterReducer = createReducer(
     initialState,
     on(increment, (state) => state + 1),
     on(decrement, (state) => state - 1),
     on(reset, (state) => 0)
   );

   export function counterReducer(state, action) {
     return _counterReducer(state, action);
   }
   ```

    <div style="page-break-after: always;"></div>

1. Add the count to the state interface and the reducers object and set the counterReducer to manage the state of the counter.

   ```diff
   // src/app/reducers/index.ts

   import {
   ActionReducer,
   ActionReducerMap,
   createFeatureSelector,
   createSelector,
   MetaReducer
   } from '@ngrx/store';
   import { environment } from '../../environments/environment';
   + import { counterReducer } from '../counter/shared/counter.reducer';

   export interface State {
   +  count: number;
   }

   export const reducers: ActionReducerMap<State> = {
   +   count: counterReducer
   };

   export const metaReducers: MetaReducer<State>[] = !environment.production
   ? []
   : [];
   ```

    <div style="page-break-after: always;"></div>

#### User Interface

1. Generate a counter module.

   ```
   ng g m counter --module=app
   ```

1. Generate a component to display the counter.

   ```shell
   ng generate component counter/my-counter
   ```

1. Update the `MyCounterComponent` class with a selector for the `count`, and methods to dispatch the Increment, Decrement, and Reset actions.
1. Then update the `MyCounterComponent` template with buttons to call the increment, decrement, and reset methods. Use the async pipe to subscribe to the count\$ observable.

   ```diff
   // src/app/counter/my-counter/my-counter.component.ts
   import { Component, OnInit
   + , ChangeDetectionStrategy
    } from '@angular/core';
   import { Store,
   + select
   } from '@ngrx/store';
   + import { Observable } from 'rxjs';
   + import { increment, decrement, reset } from '../shared/counter.actions';

   @Component({
   selector: 'app-my-counter',
   template: `
   +    <button (click)="increment()">Increment</button>
   +
   +    <div>Current Count: {{ count$ | async }}</div>
   +
   +    <button (click)="decrement()">Decrement</button>
   +
   +   <button (click)="reset()">Reset Counter</button>
   `,
   +  changeDetection: ChangeDetectionStrategy.OnPush,
   styles: []
   })
   export class MyCounterComponent {
   +  count$: Observable<number>;

   +  constructor(private store: Store<{ count: number }>) {
   +    this.count$ = store.pipe(select('count'));
   +  }

   +  increment() {
   +    this.store.dispatch(increment());
   +  }

   +  decrement() {
   +    this.store.dispatch(decrement());
   +  }

   +  reset() {
   +    this.store.dispatch(reset());
   +  }
   }

   ```

1. Export the `MyCounterComponent` from the `CounterModule`.

   ```diff
   import { NgModule } from '@angular/core';
   import { CommonModule } from '@angular/common';
   import { MyCounterComponent } from './my-counter/my-counter.component';

   @NgModule({
   declarations: [MyCounterComponent],
   imports: [CommonModule],
   +  exports: [MyCounterComponent]
   })
   export class CounterModule {}
   ```

1. Add the `MyCounterComponent` to your `AppComponent` template.

   ```diff
   import { Component } from '@angular/core';

   @Component({
   selector: 'app-root',
   template: `
   +    <app-my-counter></app-my-counter>
   `,
   styles: []
   })
   export class AppComponent {}
   ```

1. If not already running start the application with the following command:

   ```shell
   ng serve -o
   ```

1. Open `Chrome DevTools` and demonstrate the time traveling, record replay, and logging features of the `Redux DevTools` extension.

   > Directions on installing this extension included as part of the setup document for the class.

Finished code available in `demos\ngrx-counter1`.
