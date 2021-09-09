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

### Creating the HTTP branches

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
