Input props take in the **generic item T**, renderer prop <https://reactjs.org/docs/render-props.html> and function to filter items vefore they are displayed.
```
interface PropsType<T> {
  items: T[];
  renderer: (item: T) => React.ReactNode;
  filterFn?: (item: T) => boolean;
}
```

Interface indicates the input data must have a **key**
```
interface AbstractItem {
  key: string;
}
```

Below code uses render prop pattern and utilizes the ability to have access to the raw item data by **filtering** the data before it is displayed.

```
import * as React from "react";

interface PropsType<T> {
  items: T[];
  renderer: (item: T) => React.ReactNode;
  filterFn?: (item: T) => boolean;
}

interface AbstractItem {
  key: string;
}

export default function FilterableListView<T extends AbstractItem>(
  props: PropsType<T>
) {
  const items = props.filterFn
    ? props.items.filter(props.filterFn)
    : props.items;

  return (
    <ul>
      {items.map((item) => {
        return <li key={item.key}>{props.renderer(item)}</li>;
      })}
    </ul>
  );
}
```

And the list can used in a following manner:
```
<FilterableListView
  items={[
    { key: "foo", name: "foo", value: 11 },
    { key: "bar", name: "bar", value: 12 },
    { key: "baz", name: "baz", value: 8 }
  ]}
  filterFn={(item) => item.value > 10}
  renderer={(item) => <div>{item.name}</div>}
/>
```
