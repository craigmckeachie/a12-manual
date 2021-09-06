### Change Detection

#### Checkout

```shell
git checkout change-detection -f
```

The following components are already created.

```shell
parent
child-a
child-b
grandchild-a
```

<div style="page-break-after: always;"></div>

#### ChangeDetectionStrategy.Default

Click on each of the buttons to see what components are checked by change detection.
Refreshing the page after each button click makes this easier to see.

- If an input "changes" (clicking the parent button which sets the nickname) then the entire tree is checked.
- If an event triggered then the entire tree is checked.

#### ChangeDetectionStrategy.OnPush

Go into each of the following child components.

- child-a
- child-b
- grandchild-a

Uncomment the line to modify the change detection strategy from:

- the default aptly named `ChangeDetectionStrategy.Default`
- to `ChangeDetectionStrategy.OnPush`

Click on each of the buttons to see what components are checked by change detection.
Refreshing the page after each button click makes this easier to see.

- If a component has a changed input or raises an event, causes check on itself (component) and all ancestors
  - If an input "changes" then checks itself (component with input) and all ancestors
  - If an event happens then checks itself (component where event was raised, Ex. click) and all ancestors
