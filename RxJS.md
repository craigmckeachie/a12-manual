# RxJS

- [RxJS](#rxjs)
  - [Code Examples](#code-examples)
  - [Observables](#observables)
    - [First Observable](#first-observable)
    - [Creating a Stream of DOM Events](#creating-a-stream-of-dom-events)
    - [Listening for keyup events](#listening-for-keyup-events)
  - [Observers](#observers)
  - [Subcriptions](#subcriptions)
  - [Operators](#operators)
      - [map](#map)
    - [filter](#filter)
    - [Fluent Syntax](#fluent-syntax)
    - [switchMap](#switchmap)
  - [Subjects](#subjects)
      - [Unicast (Observables)](#unicast-observables)
      - [Multicast (Subjects)](#multicast-subjects)
      - [BehaviorSubject](#behaviorsubject)
  - [Practical Example](#practical-example)
      - [debounceTime](#debouncetime)
      - [distinctUntilChanged](#distinctuntilchanged)
      - [switchMap](#switchmap-1)
  - [Additional Reading](#additional-reading)

## Code Examples

```
git checkout rxjs-observables-1
git checkout branch rxjs-observables-2
git checkout rxjs-observables-3
git checkout rxjs-observer
git checkout rxjs-operators-map
git checkout rxjs-operators-filter
git checkout rxjs-operators-filter-1
git checkout rxjs-operators-switchMap
git checkout rxjs-subject-observable-unicast
git checkout rxjs-subject-multicast
git checkout rxjs-behaviorsubject
git checkout rxjs-practical
```

## Observables

**Observable**: represents the idea of an invokable collection of future values or events.

### First Observable

```
git checkout rxjs-observables-1
```

```ts
import { OnInit } from '@angular/core';
import { Component } from '@angular/core';
import { of } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ` See console for output. `,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable$ = of(1, 2, 3);
    observable$.subscribe((x) => console.log(x));
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
1
2
3
```

### Creating a Stream of DOM Events

```
git checkout branch rxjs-observables-2
```

```ts
import { OnInit, ViewChild } from '@angular/core';
import { ElementRef } from '@angular/core';
import { AfterViewInit } from '@angular/core';
import { Component } from '@angular/core';
import { fromEvent } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ` <button #myButton>Click Me</button> `,
  styles: [],
})
export class AppComponent implements OnInit, AfterViewInit {
  @ViewChild('myButton') button: ElementRef | undefined = undefined;

  ngOnInit(): void {
    console.log(this.button); //undefined
  }
  ngAfterViewInit(): void {
    console.log(this.button); //ElementRef
    console.log(this.button?.nativeElement); //button
    const clickEvents$ = fromEvent<Event>(this.button?.nativeElement, 'click');
    clickEvents$.subscribe((event: Event) => console.log(event));
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
undefined
ElementRef {nativeElement: button}
button
...
```

### Listening for keyup events

```
git checkout rxjs-observables-3
```

```ts
import { OnInit, ViewChild } from '@angular/core';
import { ElementRef } from '@angular/core';
import { AfterViewInit } from '@angular/core';
import { Component } from '@angular/core';
import { fromEvent } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ` <input type="text" #myInput /> `,
  styles: [],
})
export class AppComponent implements OnInit, AfterViewInit {
  @ViewChild('myInput') input: ElementRef | undefined = undefined;

  ngOnInit(): void {}
  ngAfterViewInit(): void {
    const inputChangeEvents$ = fromEvent<InputEvent>(
      this.input?.nativeElement,
      'input'
    ).subscribe((event: InputEvent) => console.log(event));
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser
- Enter the letters `abcdef` into the input

```
a
ab
abc
abcd
...
```

## Observers

**Observer**: is a collection of callbacks that knows how to listen to values delivered by the Observable.

```
git checkout rxjs-observer
```

```ts
import { OnInit } from '@angular/core';
import { Component } from '@angular/core';
import { of, Observer } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ` See console for output. `,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable$ = of<number>(1, 2, 3);
    const observer: Observer<number> = {
      next: (x) => console.log(x),
      complete: () => console.log('completed'),
      error: (x) => console.log(x),
    };
    observable$.subscribe(observer);
    observable$.subscribe(observer.next, observer.error, observer.complete);
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
1
2
3
completed
1
2
3
completed
```

## Subcriptions

**Subscription**: represents the execution of an Observable, is primarily useful for cancelling the execution.

```ts
import { OnInit } from '@angular/core';
import { Component } from '@angular/core';
import { interval, Observer } from 'rxjs';

@Component({
  selector: 'app-root',
  template: `See console for output.`,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable$ = interval(1000);
    const observer: Observer<number> = {
      next: (x) => console.log(x),
      complete: () => console.log('completed'),
      error: (x) => console.log(x),
    };
    const subscription = observable$.subscribe(observer);
    setTimeout(() => subscription.unsubscribe(), 5000);
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
0
1
2
3
4
...
```

## Operators

#### map

```shell
git checkout rxjs-operators-map
```

```ts
import { OnInit } from '@angular/core';
import { Component } from '@angular/core';
import { interval, Observer } from 'rxjs';
import { map, tap } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `See console for output.`,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable$ = interval(1000);
    const observer: Observer<number> = {
      next: (x) => console.log(x),
      complete: () => console.log('completed'),
      error: (x) => console.log(x),
    };

    const observableCommingOutOfThePipe$ = observable$.pipe(
      tap((x) => console.log(x)),
      map((x) => x * 10)
    );
    const subscription = observableCommingOutOfThePipe$.subscribe(observer);
  }
}
```

Result

```
0
10
20
30
...
```

Result (with tap)

```
0
0
1
10
2
20
3
30
...
```

### filter

```shell
git checkout rxjs-operators-filter
```

```ts
import { OnInit } from '@angular/core';
import { Component } from '@angular/core';
import { interval, Observer } from 'rxjs';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `See console for output.`,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable$ = interval(1000);
    const observer: Observer<number> = {
      next: (x) => console.log(x),
      complete: () => console.log('completed'),
      error: (x) => console.log(x),
    };

    const observableCommingOutOfThePipe$ = observable$.pipe(
      filter((x) => x % 2 === 0)
    );
    const subscription = observableCommingOutOfThePipe$.subscribe(observer);
  }
}
```

Result

```
0
2
4
6
8
10
...
```

### Fluent Syntax

RxJS code commonly uses the fluent syntax and chains functions.

```shell
git checkout rxjs-operators-filter-1
```

```ts
ngOnInit(): void {
    // Emits ascending numbers, one every second (1000ms)
    // const observable$ = interval(1000);
    // const observer: Observer<any> = {
    //   next: x => console.log(x),
    //   complete: () => console.log('completed'),
    //   error: x => console.log(x)
    // };

    // const observableCommingOutOfThePipe$ = observable$.pipe(
    //   filter(x => x % 2 === 0)
    // );

    // observableCommingOutOfThePipe$.subscribe(observer);

    interval(1000)
      .pipe(filter(x => x % 2 === 0))
      .subscribe(x => console.log(x));
  }
```

### switchMap

```
git checkout rxjs-operators-switchMap
```

```ts
import { OnInit, ViewChild } from '@angular/core';
import { ElementRef } from '@angular/core';
import { AfterViewInit } from '@angular/core';
import { Component } from '@angular/core';
import { fromEvent, interval } from 'rxjs';
import { switchMap } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: ` <button #myButton>Click Me</button> `,
  styles: [],
})
export class AppComponent implements OnInit, AfterViewInit {
  @ViewChild('myButton') button: ElementRef | undefined = undefined;

  ngOnInit(): void {}
  ngAfterViewInit(): void {
    fromEvent<Event>(this.button?.nativeElement, 'click')
      .pipe(
        //restart the counter on every click
        switchMap(() => interval(1000))
      )
      .subscribe((x) => console.log(x));
  }
}
```

## Subjects

- Read & Write

  - A Subject is like an Observable. It can be subscribed to, just like you normally would with Observables. It also has methods like next(), error() and complete() just like the observer you normally pass to your Observable creation function.

- Multicast

  - The main reason to use Subjects is to multicast. An Observable by default is unicast. Unicasting means that each subscribed observer owns an independent execution of the Observable. To demonstrate this:

#### Unicast (Observables)

```
git checkout rxjs-subject-observable-unicast
```

```ts
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable = new Observable((subscriber) => {
      subscriber.next(Math.random());
    });

    // subscription 1
    observable.subscribe((data) => {
      console.log(data); // 0.24957144215097515 (random number)
    });

    // subscription 2
    observable.subscribe((data) => {
      console.log(data); // 0.004617340049055896 (random number)
    });
  }
}
```

#### Multicast (Subjects)

Subjects can multicast. Multicasting basically means that one Observable execution is shared among multiple subscribers.

_Subjects are like EventEmitters, they maintain a registry of many listeners._ When calling subscribe on a Subject it does not invoke a new execution that delivers data. It simply registers the given Observer in a list of Observers.

```
git checkout rxjs-subject-multicast
```

```ts
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const subject = new Subject();

    // subscription 1
    subject.subscribe((data) => {
      console.log(data); // 0.24957144215097515 (random number)
    });

    // subscription 2
    subject.subscribe((data) => {
      console.log(data); // 0.24957144215097515(same andom number)
    });

    subject.next(Math.random());
  }
}
```

#### BehaviorSubject

Requires an initial value and emits its current value (last emitted item) to new subscribers.

```
git checkout rxjs-behaviorsubject
```

```ts
import { OnInit } from '@angular/core';
import { Component } from '@angular/core';
import { BehaviorSubject } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ` start `,
  styles: [],
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const subject = new BehaviorSubject(123);

    // two new subscribers will get initial value => output: 123, 123
    subject.subscribe(console.log);
    subject.subscribe(console.log);

    // two subscribers will get new value => output: 456, 456
    subject.next(456);

    // new subscriber will get latest value (456) => output: 456
    subject.subscribe(console.log);

    // all three subscribers will get new value => output: 789, 789, 789
    subject.next(789);
  }
}
```

- Review

  - Observables = data producers
  - Subjects = data producer and a data consumer

    > By using Subjects as a data consumer you can use them to convert Observables from unicast to multicast.

## Practical Example

```
git checkout rxjs-practical
```

Search Box

```ts
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';

@Component({
  selector: 'app-root',
  template: `
    <input
      type="text"
      #term
      (input)="search(term.value)"
      placeholder="search"
    />
    <br />
    <p *ngFor="let message of messages">{{ message }}</p>
  `,
  styles: [],
})
export class AppComponent implements OnInit {
  messages: string[] = [];
  private searchTermStream$ = new Subject<string>();

  ngOnInit(): void {
    this.searchTermStream$.subscribe((term) =>
      this.messages.push(`http call for: ${term}`)
    );
  }

  search(term: string) {
    this.searchTermStream$.next(term);
  }
}
```

Result

- Type `angular` quickly in the searchbox
- Ouput is shown below the input on the page

```
http call for: an

http call for: ang

http call for: ang

http call for: angu

http call for: angular

http call for: angular

http call for: angular
...
```

#### debounceTime

```diff
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';
+ import { debounceTime } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `
    <input
      type="text"
      #term
      (keyup)="search(term.value)"
      placeholder="search"
    />
    <br />
    <p *ngFor="let message of messages">{{ message }}</p>
  `,
  styles: []
})
export class AppComponent implements OnInit {
  messages: string[] = [];
  private searchTermStream$ = new Subject<string>();

  ngOnInit(): void {
    this.searchTermStream$
+     .pipe(debounceTime(300))
      .subscribe(term => this.messages.push(`http call for: ${term}`));
  }

  search(term: string) {
    this.searchTermStream$.next(term);
  }
}
```

Result

- Type `angular` quickly in the searchbox
- Ouput is shown below the input on the page

```
http call for: angular
```

#### distinctUntilChanged

Try

- Type `angular` quickly in the searchbox
- Delete the last letter `r` then retype the `r`
- Ouput is shown below the input on the page

Result

```
http call for: angular

http call for: angula

http call for: angular
```

```diff
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';
import { debounceTime,
+ distinctUntilChanged } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `
    <input
      type="text"
      #term
      (keyup)="search(term.value)"
      placeholder="search"
    />
    <br />
    <p *ngFor="let message of messages">{{ message }}</p>
  `,
  styles: []
})
export class AppComponent implements OnInit {
  messages: string[] = [];
  private searchTermStream$ = new Subject<string>();

  ngOnInit(): void {
    this.searchTermStream$
      .pipe(
        debounceTime(1000),
+       distinctUntilChanged()
      )
      .subscribe(term => this.messages.push(`http call for: ${term}`));
  }

  search(term: string) {
    this.searchTermStream$.next(term);
  }
}
```

Try

- Type `angular` quickly in the searchbox
- Delete the last letter `r` then retype the `r`
- Ouput is shown below the input on the page

Result

```
http call for: angular
```

Notice that I increased the debounceTime so that it's easier to retype the letters you removed.

#### switchMap

Cancels the orginal obervable and returns a new one

```diff
this.searchTermStream$
      .pipe(
        debounceTime(1000),
        distinctUntilChanged(),
+        switchMap((term: string) => {
+          return of(`new observable: ${term}`);
+        })
      )
      .subscribe(term => this.messages.push(` ${term}`));
```

## Additional Reading

- [Understanding RxJS BehaviorSubject, ReplaySubject and AsyncSubject](https://medium.com/@luukgruijs/understanding-rxjs-behaviorsubject-replaysubject-and-asyncsubject-8cc061f1cfc0)
- [Persist Login Status with Behavior Subject](https://netbasal.com/angular-2-persist-your-login-status-with-behaviorsubject-45da9ec43243)
