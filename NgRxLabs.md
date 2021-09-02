# NgRx

- [NgRx](#ngrx)
  - [Labs](#labs)
    - [Setup](#setup)
    - [Installation](#installation)
    - [Actions](#actions)
    - [State](#state)
    - [Reducer](#reducer)
    - [Effects](#effects)
    - [Configure](#configure)
    - [Connect the Container & Store](#connect-the-container--store)
  - [Resources](#resources)
    - [Online courses](#online-courses)
    - [Redux & GraphQL Resources](#redux--graphql-resources)

## Labs

### Setup

1. This lab is designed to start immediately after **Lab 31: Search Using RxJS**. If your code is not at the completion of that lab.

   - Please **run** the following command in your solutions directory.

     ```
     git checkout lab31
     ```

   - Then copy the `project-manage` directory to the `_working` directory where you will complete this lab exercise.

   - In the `_working\project-manage` directory run the command
     ```
     npm install
     ```

### Installation

1. **Install** `@ngrx/schematics` from npm:

   ```
   ng add @ngrx/schematics
   ```

1. You will be prompted with the following message, reply `y` for yes.

   ```
   The package @ngrx/schematics@12.4.0 will be installed and executed.
   Would you like to proceed? (Y/n)
   ```

   > You may be prompted for a more recent version. As long it is a `12.x` version you can proceed.

   Next, it will ask you if you want to make the `@ngrx/schematics` the **default** **collection** in your Angular CLI project. **Choose** `n` for **no** in this lab.

1. After installing @ngrx/schematics, **install** the NgRx **following** **library** dependencies.

   ```
   ng add @ngrx/store
   ng add @ngrx/store-devtools
   ng add @ngrx/effects
   ```

   After each command you will be receive the following message.

   ```
   The package @ngrx/... will be installed and executed.
    Would you like to proceed?
   ```

   Reply `y` for yes each time.

1. Generate the initial state management and register it within the app.module.ts

   ```
   ng generate @ngrx/schematics:store State --root --module app.module.ts
   ```

1. Generate the root effects and register it with the app.module.ts
   ```
   ng generate @ngrx/schematics:effect App --root --module app.module.ts
   ```
   - Should we wire up success and failure actions? No
   - Do you want to use the create function? Yes
1. **Open** `src\app\app.module.ts` and notice that the `StoreModule` was automatically imported by the `ng add` commands and the Redux Dev Tools are configured.

<div style="page-break-after: always;"></div>

### Actions

1. **Create** the `src\app\projects\shared\state` **directory**.
2. **Create** the **file** `src\app\projects\shared\state\project.actions.ts`.
3. **Add** the following **actions** using the `createAction` helper function.

   `src\app\projects\shared\state\project.actions.ts`

   ```ts
   import { createAction, props } from '@ngrx/store';
   import { Project } from '../project.model';

   export const load = createAction('[Project] Load');
   export const loadSuccess = createAction(
     '[Project] Load Success',
     props<{ projects: Project[] }>()
   );
   export const loadFail = createAction(
     '[Project] Load Fail',
     props<{ error: any }>()
   );

   export const save = createAction(
     '[Project] Save',
     props<{ project: Project }>()
   );
   export const saveSuccess = createAction(
     '[Project] Save Success',
     props<{ project: Project }>()
   );
   export const saveFail = createAction(
     '[Project] Save Fail',
     props<{ error: any }>()
   );
   ```

<div style="page-break-after: always;"></div>

### State

1. **Create** the **file** `src\app\projects\shared\state\project.reducer.ts`.
2. Define the state for the slice of the state related to projects including:

   1. an interface
   2. initial or default values
   3. selectors to select different parts of state

   **`src\app\projects\shared\state\project.reducer.ts`**

   ```ts
   import { Project } from '../project.model';
   import { State } from 'src/app/reducers';

   export interface ProjectState {
     loading: boolean;
     saving: boolean;
     error: string;
     projects: Array<Project>;
   }

   export const initialState = {
     loading: false,
     saving: false,
     error: '',
     projects: new Array<Project>(),
   };

   export const getProjects = (state: State) => state.projectState.projects;
   export const getLoading = (state: State) => state.projectState.loading;
   export const getSaving = (state: State) => state.projectState.saving;
   export const getError = (state: State) => state.projectState.error;
   ```

   > ! At this point, you will see the `state.projectState...` lines display the following error:

   ```
   Property 'projectState' does not exist on type 'State'.
   ```

   > You can safely ignore as we will resolve it in a later step.

<div style="page-break-after: always;"></div>

### Reducer

1. Create the `projectReducer` using the `createReducer` helper function by adding this code to the code in the last step.

   **`src\app\projects\shared\state\project.reducer.ts`**

   ```ts
   //...
   // add this code to the code in the last step
   import { createReducer, on } from '@ngrx/store';
   import {
     load,
     loadSuccess,
     loadFail,
     save,
     saveSuccess,
     saveFail,
   } from './project.actions';

   export const projectReducer = createReducer(
     initialState,
     on(load, (state) => ({ ...state, loading: true })),
     on(loadSuccess, (state, { projects }) => ({
       ...state,
       projects,
       loading: false,
       saving: false,
     })),
     on(loadFail, (state, { error }) => ({ ...state, error, loading: false })),
     on(save, (state) => ({ ...state, saving: true })),
     on(saveSuccess, (state, { project }) => {
       const updatedProjects = state.projects.map((item) =>
         project.id === item.id ? project : item
       );
       return {
         ...state,
         projects: updatedProjects,
         saving: false,
       };
     }),
     on(saveFail, (state, { error }) => ({ ...state, error, saving: false }))
   );
   ```

<div style="page-break-after: always;"></div>

### Effects

1. Create the `ProjectEffects` class and the `load$` and `save$` effects using the `createEffect` helper function.

   **`src\app\projects\shared\state\project.effects.ts`.**

   ```ts
   import { Injectable } from '@angular/core';
   import { of } from 'rxjs';
   import { Actions, ofType, createEffect } from '@ngrx/effects';

   import { switchMap, catchError, map, mergeMap } from 'rxjs/operators';
   import { ProjectService } from '../project.service';
   import {
     load,
     loadSuccess,
     loadFail,
     save,
     saveSuccess,
     saveFail,
   } from './project.actions';

   @Injectable()
   export class ProjectEffects {
     load$ = createEffect(() => {
       return this.actions$.pipe(
         ofType(load),
         switchMap(() => {
           return this.projectService.list().pipe(
             map((data) => loadSuccess({ projects: data })),
             catchError((error) => of(loadFail({ error: error })))
           );
         })
       );
     });

     save$ = createEffect(() => {
       return this.actions$.pipe(
         ofType(save),
         mergeMap(({ project }) => {
           return this.projectService.put(project).pipe(
             map(() => saveSuccess({ project })),
             catchError((error) => of(saveFail({ error: error })))
           );
         })
       );
     });

     constructor(
       private actions$: Actions,
       private projectService: ProjectService
     ) {}
   }
   ```

<div style="page-break-after: always;"></div>

### Configure

1. Add the project related state and reducer to the root state and reducer.

   **`src\app\reducers\index.ts`.**

   ```diff
   import {
   ActionReducer,
   ActionReducerMap,
   createFeatureSelector,
   createSelector,
   MetaReducer
   } from '@ngrx/store';
   import { environment } from '../../environments/environment';
   + import {
   +   ProjectState,
   +   projectReducer
   + } from '../projects/shared/state/project.reducer';

   export interface State {
   +  projectState: ProjectState;
   }

   export const reducers: ActionReducerMap<State> = {
   +  projectState: projectReducer
   };

   export const metaReducers: MetaReducer<State>[] = !environment.production
   ? []
   : [];
   ```

<div style="page-break-after: always;"></div>

### Connect the Container & Store

1. Refactor the `ProjectsContainerComponent` to use the `Store` instead of the `ProjectService` to access data. More specifically:

   1. Change all class members to be Observables.
   2. Inject the `Store` instead of the `ProjectService` in the component's constructor.
   3. Dispatch the `load` action in `ngOnInit` instead of calling the service directly.
   4. Dispatch the `save` action in the `onSaveListItem` event handler method instead of calling the service directly.

   **`src\app\projects\projects-container\projects-container.component.ts`**

   ```diff
   import {
   Component,
   OnInit,
   OnDestroy,
   +  ChangeDetectionStrategy,
   } from '@angular/core';
   import { Project } from '../shared/project.model';
   import { Subject,
   -        Observable
           Subscription,
   +        of }
           from 'rxjs';
   import { debounceTime, distinctUntilChanged, switchMap } from 'rxjs/operators';
   + import { select, Store } from '@ngrx/store';
   + import { State } from 'src/app/reducers';
   + import {
   + getError,
   + getLoading,
   + getProjects,
   + getSaving,
   + } from '../shared/state/project.reducer';
   + import { load, save } from '../shared/state/project.actions';

   @Component({
   selector: 'app-projects-container',
   templateUrl: './projects-container.component.html',
   styleUrls: ['./projects-container.component.css'],
   +  changeDetection: ChangeDetectionStrategy.OnPush,
   })
   export class ProjectsContainerComponent implements OnInit, OnDestroy {
   - projects: Project[] = [];
   - errorMessage: string = '';
   - loading: boolean = false;
   + projects$ = this.store.pipe(select(getProjects));
   + errorMessage$ = this.store.pipe(select(getError));
   + loading$ = this.store.pipe(select(getLoading));
   + saving$ = this.store.pipe(select(getSaving));
   private searchTerms = new Subject<string>();
   private subscription!: Subscription;


   -  constructor(private projectService: ProjectService) {}
   +  constructor(private store: Store<State>) {}

   ngOnInit() {
       this.observeSearchTerms();
   -   this.searchTerms.next('');
   +   this.store.dispatch(load({ name: '' }));
   }

   onSearch(term: string) {
       this.searchTerms.next(term);
   }

   observeSearchTerms() {
   -    this.subscription = this.searchTerms
   -      .pipe(
   -        // wait 300ms after each keystroke before considering the term
   -        debounceTime(300),
   -
   -        // ignore new term if same as previous term
   -        distinctUntilChanged(),
   -
   -        // switch to new search observable each time the term changes
   -        switchMap((term: string): Observable<Project[]> => {
   -          this.loading = true;
   -          return this.projectService.listByName(term);
   -        })
   -      )
   -      .subscribe(
   -        (data) => {
   -          this.loading = false;
   -          this.projects = data;
   -        },
   -        (error) => {
   -          this.loading = false;
   -          this.errorMessage = error;
   -        }
   -      );
   +    this.subscription = this.searchTerms
   +      .pipe(
   +        // wait 300ms after each keystroke before considering the term
   +        debounceTime(300),
   +
   +        // ignore new term if same as previous term
   +        distinctUntilChanged(),
   +
   +        // switch to new search observable each time the term changes
   +        switchMap((term: string) => {
   +          this.store.dispatch(load({ name: term }));
   +          return of(term);
   +        })
   +      )
   +      .subscribe();

   }

   onSaveListItem(event: any) {
       const project: Project = event.item;
   -    this.projectService.put(project).subscribe(
   -      (updatedProject) => {
   -        const index = this.projects.findIndex(
   -          (element) => element.id === project.id
   -        );
   -        this.projects[index] = project;
   -      },
   -      (error) => (this.errorMessage = error)
   -    );

       this.store.dispatch(save({ project }));
   }

   ngOnDestroy(): void {
       this.subscription.unsubscribe();
   }
   }
   ```

1. Update the html template to use the `async` pipe which will automatically subscribe and unsubscribe from the NgRx observables.

   **`src\app\projects\projects-container\projects-container.component.html`**

   ```diff
   <h1>Projects</h1>

   <div class="row">
   <div class="col-sm-12">
       <div class="input-group fluid">
       <input
           #searchBox
           type="text"
           name="searchBox"
           placeholder="Search"
           (keyup)="onSearch(searchBox.value)"
       />
       </div>
   </div>
   </div>
   -<div *ngIf="loading" class="center-page">
   +<div *ngIf="loading$ | async" class="center-page">
   <span class="spinner primary"></span>
   <p>Loading...</p>
   </div>
   +<span *ngIf="saving$ | async" class="toast"> Saving... </span>
   <div class="row">
   -  <div *ngIf="errorMessage" class="card large error">
   +  <div *ngIf="errorMessage$ | async as errorMessage" class="card large error">
       <section>
       <p><span class="icon-alert inverse"></span> {{ errorMessage }}</p>
       </section>
   </div>
   </div>
   -<app-project-list
   -  [projects]="projects"
   -  (saveListItem)="onSaveListItem($event)"
   -></app-project-list>
   +<ng-container *ngIf="projects$ | async as projects">
   +  <app-project-list
   +    [projects]="projects"
   +    (saveListItem)="onSaveListItem($event)"
   +  >
   +  </app-project-list>
   +</ng-container>
   ```

1. Register the `ProjectEffects`.

   **`src\app\app.module.ts`**

   ```diff
   + import { ProjectEffects } from './projects/shared/state/project.effects';

   @NgModule({
   declarations: [AppComponent],
   imports: [
       BrowserModule,
       AppRoutingModule,
       ProjectsModule,
       HttpClientModule,
       HomeModule,
       StoreModule.forRoot(reducers, {
       metaReducers,
       runtimeChecks: {
           strictStateImmutability: true,
           strictActionImmutability: true
       }
       }),
       StoreDevtoolsModule.instrument({
       maxAge: 25,
       logOnly: environment.production
       }),
   EffectsModule.forRoot([AppEffects,
   +   ProjectEffects])
   ],
   providers: [],
   bootstrap: [AppComponent]
   })
   export class AppModule {}
   ```

1. Verify the application functionality works as it did previously including:
   1. Loading and saving data.
   2. Handles errors appropriately when the backend is down.
   3. Displays loading and saving messages.
1. Lastly, verify you can now time travel.
   1. Open Chrome DevTools (F12 or fn+F12 on Windows)
   1. Install the [Redux DevTools Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en) if you haven't already.
   1. Use the application and then travel back in time and replay your actions.

<div style="page-break-after: always;"></div>

## Resources

- [Official Documentation](https://ngrx.io/)
- [Example Application](https://github.com/ngrx/platform/tree/master/projects/example-app)
- [New Features in NgRx 8](https://medium.com/ngrx/announcing-ngrx-version-8-ngrx-data-create-functions-runtime-checks-and-mock-selectors-a44fac112627)
- [NgRx Resources](https://ngrx.io/resources)

  ### Online courses

  Pluralsight (Deborah Kurata and Duncan Hunter)
  [Angular NgRx: Getting Started](https://www.pluralsight.com/courses/angular-ngrx-getting-started)

  [Ultimate Angular (Todd Motto)
  NGRX Store + Effects](https://ultimatecourses.com/learn/ngrx-store-effects)

  ### Redux & GraphQL Resources

  [GraphQL](https://graphql.org/)

  [How GraphQL Replaces Redux](https://hackernoon.com/how-graphql-replaces-redux-3fff8289221d)

  [Free Video Course on Redux by Creator](https://egghead.io/courses/getting-started-with-redux)

  [Future: Less State Management](https://medium.com/@amcdnl/the-future-of-javascript-state-management-is-less-state-management-ba1d97b99308)

  [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

  [Presentational vs Container components divide](https://twitter.com/dan_abramov/status/802569801906475008)
