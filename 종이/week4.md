#### 7.18 관찰자 패턴

> React의 useEffect 훅은 상태나 props의 변화를 감지하고, 이에 맞춰 부수적인 동작을 실행하는 면에서 관찰자 패턴과 매우 유사합니다.

```javascript
import React, { useState, useEffect } from "react";

function MyComponent() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // 이 코드는 count 상태가 변경될 때마다 실행됨
    console.log(`현재 count : ${count}`);

    // 선택적으로 클린업 함수를 리턴
    return () => {
      cleanSomething();
    };
  }, [count]); // count를 피관찰자로 설정

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

1. 상태 변화 감지: useEffect는 컴포넌트 내에서 관찰할 상태(state)나 props를 의존성 배열에 지정합니다. 지정된 값들이 변경되면 useEffect는 이를 감지하고 실행됩니다.
2. 자동 실행: 상태가 변경되면 React는 자동으로 관찰자(useEffect)를 다시 실행하여 부수적인 동작을 처리합니다.
3. 클린업 (Cleanup): 관찰자 패턴에서 자원을 해제하거나 특정 작업을 취소할 수 있는 방법을 제공하는데, useEffect는 클린업 함수를 통해 이를 제공합니다. 이는 React가 컴포넌트가 사라지거나 useEffect가 다시 실행될 때 불필요한 자원을 정리하는 데 유용합니다.

#### 7.18 커맨드 패턴

> React의 useImperativeHandle 훅은 커스텀 레퍼런스(forwarded ref) 제어를 위해 사용되며, 부모 컴포넌트가 자식 컴포넌트의 특정 메서드나 값을 직접 호출하고 제어할 수 있는 인터페이스를 제공할 수 있습니다.

```javascript

// App.js

import { useRef } from 'react';
import MyInput from './MyInput.js';

export default function Form() {
  const ref = useRef(null);

  function handleClick() {
    ref.current.focus();
    // 이 작업은 DOM 노드가 노출되지 않으므로 작동하지 않습니다.
    // ref.current.style.opacity = 0.5;
  }

  return (
    <form>
      <MyInput placeholder="Enter your name" ref={ref} />
      <button type="button" onClick={handleClick}>
        Edit
      </button>
    </form>
  );
}

// MyInput.js
import { forwardRef, useRef, useImperativeHandle } from 'react';

const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      scrollIntoView() {
        inputRef.current.scrollIntoView();
      },
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});

export default MyInput;
```

- 커맨드 패턴은 객체의 동작을 캡슐화하고 메서드를 외부에서 호출하여, 호출자(부모 컴포넌트)가 명령을 실행할 수 있도록 하는 패턴입니다.
- useImperativeHandle은 이러한 상황에서 자식 컴포넌트의 내부 메서드나 속성을 외부로 노출하여 부모 컴포넌트가 이를 직접 제어할 수 있도록 해줍니다.
- useImperativeHandle은 forwardRef와 함께 사용되며, 부모가 자식 컴포넌트에 ref를 전달하면, 이 훅을 통해 자식 컴포넌트는 부모에게 특정 메서드나 값을 노출할 수 있습니다.
