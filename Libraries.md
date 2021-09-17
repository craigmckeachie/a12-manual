# Libraries

## Your First Library

1. Run the commands:

   ```
   ng new my-workspace --create-application false
   cd my-workspace
   ng generate application my-first-app
   ng generate library my-lib
   ng build my-lib
   ```

1. Import `MyLibModule` in the app.

   **`my-first-app\src\app\app.module.ts`**

   ```diff
   import { BrowserModule } from '@angular/platform-browser';
   import { NgModule } from '@angular/core';

   import { AppComponent } from './app.component';
   + import { MyLibModule } from 'my-lib';

   @NgModule({
     declarations: [AppComponent],
     imports: [BrowserModule
   +  ,MyLibModule
     ],
     providers: [],
     bootstrap: [AppComponent]
   })
   export class AppModule {}
   ```

1. Delete the html in the AppComponent template and add the component from `my-lib`.

   **`my-first-app\src\app\app.component.html`**

   ```html
   <lib-my-lib></lib-my-lib>
   ```

1. Start `my-lib`

   ```
    ng serve -o
   ```

1. You should see the `my-lib` component rendered in `my-first-app`.

   ```
   my-lib works!
   ```

1. Change the lib component.

   #### `projects\my-lib\src\my-lib.component.ts`

   ```
    @Component({
    selector: 'lib-my-lib',
    template: `
        <p>
        my-lib still works!
        </p>
    `,
    styles: [
    ]
    })
   ```

1. Notice the app doesn't update.
1. Rebuild the library.

   ```
   ng build my-lib
   ```

1. Restart `my-first-app`

   ```
   #  Ctrl+C
   ng serve -o
   ```

   > Sometimes there is no need to stop and start `ng serve` but many times I have to for it to work.

1. Alternatively, build the library in watch mode.

   - First, stop the app

   ```
   Ctrl+C in the ng serve terminal
   ```

   - Build the library in watch mode.

   ```
   ng build my-lib --watch
   ```

   - Restart the app

   ```
   ng serve -o
   ```

1. The library should now trigger rebuilds automatically when it's code is changed.
1. Update `my-lib` again.
1. See the changes reflected in your browser.

## Publishing your Library

1.  If you want to make your library public in the NPM repository, you can run the following commands:

    ```
    ng build my-lib
    cd dist/my-lib
    npm publish
    ```

    > If you’ve never published a package in npm before, you will need to create a user account and run `npm adduser` first (see steps below). You can read more about publishing on npm here: https://docs.npmjs.com/getting-started/publishing-npm-packages

    > To publish `my-lib` and get a feel for the process I recommend publishing the package as an [unscoped public package](https://docs.npmjs.com/creating-and-publishing-unscoped-public-packages#publishing-unscoped-public-packages) which just requires creating a free account.

1.  Visit https://www.npmjs.com/signup and creat an account.
    > Caution: The email you use to create that account becomes public on npm
1.  Add the account to npm client on the command-line by running:
    ```
    npm adduser
    ```
1.  Once you are logged just verify that you are in the `dist\my-lib` directory

    ```
    cd dist/my-lib # if not there from a previous step
    npm publish
    ```

1.  You will receive the following error.
    ```
    403 Forbidden - PUT https://registry.npmjs.org/my-lib - You do not have permission to publish "my-lib". Are you logged in as the correct user?
    ```
1.  Open the `my-lib` `package.json` file and change the package name to a unique name.

    #### `my-workspace\projects\my-lib\package.json`

    ```diff
    {
    -  "name": "my-lib",
    +  "name": "funny-ant-my-lib",
    "version": "0.0.1",
    "peerDependencies": {
        "@angular/common": "^12.2.0",
        "@angular/core": "^12.2.0"
    },
    "dependencies": {
        "tslib": "^2.3.0"
    }
    }
    ```

1.  To use this library in another application.

    ```
    cd ..
    ng new my-second-app
    cd my-second-app
    npm i funny-ant-my-lib
    ```

    #### `package.json`

    ```diff
    "dependencies": {
        "@angular/animations": "~12.2.0",
        "@angular/common": "~12.2.0",
        "@angular/compiler": "~12.2.0",
        "@angular/core": "~12.2.0",
        "@angular/forms": "~12.2.0",
        "@angular/platform-browser": "~12.2.0",
        "@angular/platform-browser-dynamic": "~12.2.0",
        "@angular/router": "~12.2.0",
    +    "funny-ant-my-lib": "~0.0.1",
        "rxjs": "~6.6.0",
        "tslib": "^2.3.0",
        "zone.js": "~0.11.4"
    },
    ```

    #### `src\app\app.module.ts`

    ```diff
    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    + import { MyLibModule } from 'funny-ant-my-lib';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';

    @NgModule({
    declarations: [AppComponent],
    imports: [BrowserModule, AppRoutingModule, 
    + MyLibModule
    ],
    providers: [],
    bootstrap: [AppComponent],
    })
    export class AppModule {}
    ```

    #### `src\app\app.component.html`

    ```
    <lib-my-lib></lib-my-lib>
    ```

### Reference

- [File Structure: Library Project Files](https://angular.io/guide/file-structure#library-project-files)
- [Creating Libraries Official Documentation](https://angular.io/guide/creating-libraries)
- [Using npm Link](https://medium.com/dailyjs/how-to-use-npm-link-7375b6219557)
- [Creating Your Own Libraries with the Angular CLI](https://blog.angulartraining.com/create-your-own-libraries-with-angular-cli-7b434600bbb7)
