# react-multi-ref

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/Macil/react-multi-ref/blob/master/LICENSE.txt) [![npm version](https://img.shields.io/npm/v/react-multi-ref.svg?style=flat)](https://www.npmjs.com/package/react-multi-ref) [![CircleCI Status](https://circleci.com/gh/Macil/react-multi-ref.svg?style=shield)](https://circleci.com/gh/Macil/react-multi-ref) [![Greenkeeper badge](https://badges.greenkeeper.io/Macil/react-multi-ref.svg)](https://greenkeeper.io/)

This is a small utility to make it easy for React components to deal with refs
on multiple dynamically created elements.

The following example code assumes that you're using either Babel with the
"babel/plugin-proposal-class-properties" plugin active or TypeScript.

```js
import React from 'react';
import MultiRef from 'react-multi-ref';

class Foo extends React.Component {
  _itemRefs = new MultiRef();

  render() {
    // Make a 5-item array of divs with keys 0,1,2,3,4
    const items = new Array(5).fill(null).map((n, i) =>
      <div key={i}>
        <input type="text" ref={this._itemRefs.ref(i)} />
      </div>
    );
    return (
      <div>
        <button onClick={this._onClick}>Alert</button>
        { items }
      </div>
    );
  }

  _onClick = () => {
    const parts = [];
    this._itemRefs.map.forEach(input => {
      parts.push(input.value)
    });
    alert('all inputs: ' + parts.join(', '));
  };
}
```

The `multiRef.map` property is a Map object containing entries where the key is
the parameter passed to `multiRef.ref(key)` and the value is the ref element
given by React. You can retrieve a specific element by key from the map by using
`multiRef.map.get(key)`.

Multiple calls to `multiRef.ref(key)` with the same key return the same value
so that React knows that it doesn't need to update the ref.

This relies on Map being available globally. A global polyfill such as
[Babel's polyfill](https://babeljs.io/docs/en/babel-polyfill/) is required to
support older browsers that don't implement these.

## Hooks Example

MultiRef is usable as long as you can create an instance of it and persist the
instance for the lifetime of a component. The easiest way to do that in a
function component is to put it in state with `useState`.

It is *not* recommended to use React's `useMemo` to store the MultiRef instance
because the documentation specifies that React is allowed to purge the memory
of `useMemo` at any time. You should either use `useState` as below or use
[useMemoOne](https://github.com/alexreardon/use-memo-one).

```js
import React, { useState } from 'react';
import MultiRef from 'react-multi-ref';

function Foo(props) {
  const [itemRefs] = useState(() => new MultiRef());

  function onClick() {
    const parts = [];
    itemRefs.map.forEach(input => {
      parts.push(input.value)
    });
    alert('all inputs: ' + parts.join(', '));
  }

  // Make a 5-item array of divs with keys 0,1,2,3,4
  const items = new Array(5).fill(null).map((n, i) =>
    <div key={i}>
      <input type="text" ref={itemRefs.ref(i)} />
    </div>
  );

  return (
    <div>
      <button onClick={onClick}>Alert</button>
      { items }
    </div>
  );
}
```

## Types

Both [TypeScript](https://www.typescriptlang.org/) and
[Flow](https://flowtype.org/) type definitions for this module are included!
The type definitions won't require any configuration to use.
