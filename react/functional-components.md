
### Simple example

Declaring type of props

``` TypeScript
type AppProps = {
  message: string;
}; /* use `interface` if exporting so that consumers can extend */
```

Easiest way to declare a Function Component; return type is inferred.
``` TypeScript
const App = ({ message }: AppProps) => <div>{message}</div>;
```

you can choose to annotate the return type so an error is raised if you accidentally return some other type
``` TypeScript
const App = ({ message }: AppProps): JSX.Element => <div>{message}</div>;
```

you can also inline the type declaration; eliminates naming the prop types, but looks repetitive
``` TypeScript
const App = ({ message }: { message: string }) => <div>{message}</div>;
```

### Example of functional component declaration
With `FunctionComponent`
``` TypeScript
import React, { FC, useState } from 'react';

export interface ISuppliersTreeViewProps {
  onSupplierSelected: (documentNumber: number) => void;
}

const SuppliersTreeView: FC<ISuppliersTreeViewProps> = ({ onSupplierSelected }: ISuppliersTreeViewProps) => {
  const classes = useStyles();
  const { isLoading, isFetching, data } = useFetchSuppliers();
  const [expanded, setExpanded] = useState([]);
  const [selected, setSelected] = useState<string>();
```

Or with `type`definition:
``` TypeScript
import React, { FC, useState } from 'react';

type SuppliersTreeViewProps = {
  onSupplierSelected: (documentNumber: number) => void;
};

const SuppliersTreeView: FC<SuppliersTreeViewProps> = ({ onSupplierSelected }: SuppliersTreeViewProps) => {
  const classes = useStyles();
  const { isLoading, isFetching, data } = useFetchSuppliers();
  const [expanded, setExpanded] = useState([]);
  const [selected, setSelected] = useState<string>();

```

### Hooks related links

https://overreacted.io/making-setinterval-declarative-with-react-hooks/
