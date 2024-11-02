#### react hook으로는 구현할 수 없는 error boundary

- react hook
- 클래스 컴포넌트로 직접 구현하시는지 라이브러리를 이용하는지.. 다른분들은 error boundary 어떤 방식으로 사용하고 있는지 궁금하네요 🤔

> 생명주기 메서드인 static getDerivedStateFromError() 와 componentDidCatch() 중 하나 (혹은 둘 다)를 정의하면 클래스 컴포넌트 자체가 에러 경계가 됩니다. 에러가 발생한 뒤에 폴백 UI를 렌더링하려면 static getDerivedStateFromError()를 사용하세요. 에러 정보를 기록하려면 componentDidCatch()를 사용하세요.

```
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // state를 갱신하여 다음 렌더링에서 대체 UI를 표시합니다.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // 에러 리포팅 서비스에 에러를 기록할 수도 있습니다.
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // 별도로 작성한 대체 UI를 렌더링할 수도 있습니다.
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

- static getDerivedStateFromError()

  > 이 생명주기 메서드는 하위의 자손 컴포넌트에서 오류가 발생했을 때 호출됩니다. 이 메서드는 매개변수로 오류를 전달받고, 갱신된 state 값을 반드시 반환해야 합니다.
  > getDerivedStateFromError()는 “render” 단계에서 호출되므로, 부수 효과를 발생시키면 안 됩니다.

- componentDidCatch()
  > 이 생명주기 메서드는 자손 컴포넌트에서 오류가 발생했을 때에 호출되며, 2개의 매개변수를 전달받습니다.
  > error - 발생한 오류
  > info - 어떤 컴포넌트가 오류를 발생시켰는지에 대한 정보를 포함한 componentStack 키를 갖고 있는 객체
  > componentDidCatch()는 “커밋” 단계에서 호출되므로, 부수 효과를 발생시켜도 됩니다. 아래와 같이 오류 로그 기록 등을 위하여 사용하면 됩니다.
