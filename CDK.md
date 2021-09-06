## Angular Material: CDK

Angular Material components use the `Component Development Kit` (CDK) library for reusable behavior and combines it with the styles to create components that implement [Google's Material Design Specification](https://material.io/design/).

### CDK: Installation

#### Steps

1. Install

```bash
npm install @angular/cdk@10 faker
```

> Note: `faker` is a JavaScript library used for generating data that we will use for one of the examples

2. Import the module

```ts
//app.module.ts
import {DragDropModule} from '@angular/cdk/drag-drop';
import { ScrollingModule } from '@angular/cdk/scrolling';

@NgModule({
  imports: [ ...,DragDropModule, ScrollingModule ]
})
export class AppModule { }
```

<div style="page-break-after: always;"></div>

### CDK: Drag & Drop

(using the `DragDropModule`)

The `@angular/cdk/drag-drop` module provides you with a way to easily and declaratively create drag-and-drop interfaces, with support for free dragging, sorting within a list, transferring items between lists, animations, touch devices, custom drag handles, previews, and placeholders, in addition to horizontal lists and locking along an axis.

#### Steps

1. Add the following styles

```css
/* 
src/styles.css 
*/

.example-container {
  width: 400px;
  max-width: 100%;
  margin: 0 25px 25px 0;
  display: inline-block;
  vertical-align: top;
}

.example-list {
  border: solid 1px #ccc;
  min-height: 60px;
  background: white;
  border-radius: 4px;
  overflow: hidden;
  display: block;
}

.example-boundary {
  width: 400px;
  height: 400px;
  max-width: 100%;
  border: dotted #ccc 2px;
}

.example-box {
  width: 200px;
  height: 200px;
  border: solid 1px #ccc;
  color: rgba(0, 0, 0, 0.87);
  cursor: move;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  background: #fff;
  border-radius: 4px;
  position: relative;
  z-index: 1;
  transition: box-shadow 200ms cubic-bezier(0, 0, 0.2, 1);
  box-shadow: 0 3px 1px -2px rgba(0, 0, 0, 0.2), 0 2px 2px 0 rgba(0, 0, 0, 0.14),
    0 1px 5px 0 rgba(0, 0, 0, 0.12);
}

.example-box:active {
  box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2), 0 8px 10px 1px rgba(0, 0, 0, 0.14),
    0 3px 14px 2px rgba(0, 0, 0, 0.12);
}

.example-box {
  padding: 20px 10px;
  border-bottom: solid 1px #ccc;
  color: rgba(0, 0, 0, 0.87);
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  box-sizing: border-box;
  cursor: move;
  background: white;
  font-size: 14px;
}

.cdk-drag-preview {
  box-sizing: border-box;
  border-radius: 4px;
  box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2), 0 8px 10px 1px rgba(0, 0, 0, 0.14),
    0 3px 14px 2px rgba(0, 0, 0, 0.12);
}

.cdk-drag-placeholder {
  opacity: 0;
}

.cdk-drag-animating {
  transition: transform 250ms cubic-bezier(0, 0, 0.2, 1);
}

.example-box:last-child {
  border: none;
}

.example-list.cdk-drop-list-dragging .example-box:not(.cdk-drag-placeholder) {
  transition: transform 250ms cubic-bezier(0, 0, 0.2, 1);
}
```

2. Create a `div` with the `cdKDrag` directive

```diff
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
+    <div class="example-box" cdkDrag>
+      Drag me around
+    </div>
  `,
  styles: []
})
export class AppComponent {}
```

3. In the browser you can now drag the div around the page.
4. You can also drag the div off the page.

5. Wrap the another `div` around the draggable `div`. This wrapper element will be the boundary within which you can drag.

```html
<div class="example-boundary">
  <div class="example-box" cdkDragBoundary=".example-boundary" cdkDrag>
    Drag me around
  </div>
</div>
```

> Be sure to include the `.` before the class name `example-boundary` in the `cdkDragBoundary` directive.

6. Drag the element and notice that it stays within the bounds of the boundary box.

> You can stop here but if time permits you can type the code below to create a list that reorders itself.

<div style="page-break-after: always;"></div>

#### Reorder List

1. Update the code

```ts
// app.component.ts

import { Component } from "@angular/core";
import { CdkDragDrop, moveItemInArray } from "@angular/cdk/drag-drop";

@Component({
  selector: "app-root",
  template: `
    <div cdkDropList class="example-list" (cdkDropListDropped)="drop($event)">
      <div class="example-box" *ngFor="let movie of movies" cdkDrag>
        {{ movie }}
      </div>
    </div>
  `,
  styles: []
})
export class AppComponent {
  movies = [
    "Episode I - The Phantom Menace",
    "Episode II - Attack of the Clones",
    "Episode III - Revenge of the Sith",
    "Episode IV - A New Hope",
    "Episode V - The Empire Strikes Back",
    "Episode VI - Return of the Jedi",
    "Episode VII - The Force Awakens",
    "Episode VIII - The Last Jedi"
  ];

  drop(event: CdkDragDrop<string[]>) {
    moveItemInArray(this.movies, event.previousIndex, event.currentIndex);
  }
}
```

8. Comment out the first `example-box` style as shown below

```css
/* src/styles.css */

/* .example-box {
  width: 200px;
  height: 200px;
  border: solid 1px #ccc;
  color: rgba(0, 0, 0, 0.87);
  cursor: move;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  background: #fff;
  border-radius: 4px;
  position: relative;
  z-index: 1;
  transition: box-shadow 200ms cubic-bezier(0, 0, 0.2, 1);
  box-shadow: 0 3px 1px -2px rgba(0, 0, 0, 0.2), 0 2px 2px 0 rgba(0, 0, 0, 0.14),
    0 1px 5px 0 rgba(0, 0, 0, 0.12);
} */
```

9. You can now reorder the list by dragging and dropping the items.

> For additonal information see: [Drag & Drop Documentation](https://material.angular.io/cdk/drag-drop/overview)

<div style="page-break-after: always;"></div>

### CDK: Virtual Scrolling

(using the ScrollingModule)

The `<cdk-virtual-scroll-viewport>` displays large lists of elements performantly by only rendering the items that fit on-screen. Loading hundreds of elements can be slow in any browser; virtual scrolling enables a performant way to simulate all items being rendered by making the height of the container element the same as the height of total number of elements to be rendered, and then only rendering the items in view. Virtual scrolling is different from strategies like infinite scroll where it renders a set amount of elements and then when you hit the end renders the rest.

#### Steps

1. Add the following code:

```ts
//app.component.ts
import { Component } from "@angular/core";
import * as faker from "faker";

@Component({
  selector: "app-root",
  template: `
    <cdk-virtual-scroll-viewport itemSize="50" class="list">
      <div *cdkVirtualFor="let item of items" class="list-item">
        {{ item }}
      </div>
    </cdk-virtual-scroll-viewport>
  `,
  styles: [
    `
      .list {
        height: 200px;
        width: 200px;
        border: 1px solid black;
      }

      .list-item {
        height: 50px;
      }
    `
  ]
})
export class AppComponent {
  items = Array.from({ length: 100000 }).map(() => `${faker.name.findName()}`);
}
```

> The key to the above example is that the `itemSize` property on the `<cdk-virtual-scroll-viewport>` must match the `.list-item` css class height.

4. View in the Chrome browser and open Chrome DevTools to the Elements tab
5. Scroll through the list and watch the items being generated as they come into view

> For additonal information see: [Scrolling Module Documentation](https://material.angular.io/cdk/scrolling/overview)

<div style="page-break-after: always;"></div>

#### Resources

- [Live drag/drop demo video](https://youtu.be/4EXQKP-Sihw?t=240)

- [Article on Angular Material Setup](https://material.angular.io/guide/schematics)

- [Slides: The CDK is the Coolest Thing You are not Using](g.co/ng/conf19-components-slides)

- [Infinite Virtual Scroll using Angular CDK](https://angularfirebase.com/lessons/infinite-virtual-scroll-angular-cdk/)

<div style="page-break-after: always;"></div>


