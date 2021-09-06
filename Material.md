## Angular Material: Introduction

### Installation

1. In a command-prompt (Windows) or terminal (Mac) run the following commands.

   ```shell
   ng new my-mat-proj
   cd my-mat-proj
   ng add @angular/material
   code .
   ```

   Choose:

   - indigo-pink.css
   - HammerJS: Yes
   - Browser animations: Y

1. Open `my-mat-proj` in your editor.

   ```shell
   code .
   ```

1. Look at the changes using a Git diff.

> What the command does:

        - Ensure project dependencies are placed in package.json
        - Enable the BrowserAnimationsModule your app module
        - Add either a prebuilt theme or a custom theme
        - Add Roboto fonts to your index.html
        - Add the Material Icon font to your index.html
        - Add global styles to
        - Remove margins from body
        - Set height: 100% on html and body
        - Make Roboto the default font of your app
        - Install and import hammerjs for gesture support in your project

<div style="page-break-after: always;"></div>

3. Add a slider.

   `src\app\app.module.ts`

   ```ts
   import { MatSliderModule } from '@angular/material/slider';
   …
   @NgModule ({....
     imports: [...,
     MatSliderModule,
   …]
   })
   ```

   `src\app\app.component.html`

   ```html
   // delete contents
   <mat-slider min="1" max="100" step="1" value="1"></mat-slider>
   ```

4. Build and run the application.

   ```shell
   ng serve
   ```

<div style="page-break-after: always;"></div>

### Navigation

1. Generate a navigation component.

   ```shell
   ng generate @angular/material:nav mainnav
   ```

2. Add the component to the main content area.

   `src/app/mainnav/mainnav.component.html`

   ```diff
   </mat-toolbar>
       <!-- Add Content Here -->
   +    <h1>Main Content</h1>
   +    <p>This is the main content</p>
     </mat-sidenav-content>
   ```

   > Resize browser smaller to see hamburger menu

3. Make the changes below to show the menu by default.

   `src/app/mainnav/mainnav.component.html`

   ```diff
   <mat-sidenav-container class="sidenav-container">
     <mat-sidenav
       #drawer
       class="sidenav"
       fixedInViewport
       [attr.role]="(isHandset$ | async) ? 'dialog' : 'navigation'"
       [mode]="(isHandset$ | async) ? 'over' : 'side'"
   -    [opened]="(isHandset$ | async) === false"
     >

   ...
   <mat-sidenav-content>
       <mat-toolbar color="primary">
         <button
           type="button"
           aria-label="Toggle sidenav"
           mat-icon-button
           (click)="drawer.toggle()"
   -        *ngIf="isHandset$ | async"
         >
           <mat-icon aria-label="Side nav toggle icon">menu</mat-icon>
         </button>
         <span>my-mat-proj</span>
       </mat-toolbar>
   ```

<div style="page-break-after: always;"></div>

### Dashboard

1. Generate a dashboard component.

   ```
   ng generate @angular/material:dashboard dashboard
   ```

1. Add the component to the main content area.

   `src/app/mainnav/mainnav.component.html`

   ```diff
   ...
   -    <h1>Test</h1>
   +    <app-dashboard></app-dashboard>
     </mat-sidenav-content>
   </mat-sidenav-container>

   ```

### Table

1. Generate a table component.

   ```
   ng generate @angular/material:table my-table
   ```

1. Add the component to the main content area.

   `src/app/mainnav/mainnav.component.html`

   ```diff
   ...
       <app-dashboard></app-dashboard>
   +    <app-my-table></app-my-table>
     </mat-sidenav-content>
   </mat-sidenav-container>

   ```

<div style="page-break-after: always;"></div>

### Forms

1. Generate an address form component.

   ```
   ng generate @angular/material:address-form address-form
   ```

1. Add the component to the main content area.

   `src/app/mainnav/mainnav.component.html`

   ```diff
   ...
       <app-dashboard></app-dashboard>
       <app-my-table></app-my-table>
   +    <app-address-form></app-address-form>
     </mat-sidenav-content>
   </mat-sidenav-container>

   ```

#### Reference

- [Angular Material: Getting Started](https://material.angular.io/guide/getting-started)
- [Angular Material: Schematics](https://material.angular.io/guide/schematics)
