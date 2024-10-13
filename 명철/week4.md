# 이야깃거리


## 01
178p 커맨드 패턴 예제에서

1. 왜 apply를 통해서 호출하는가?
   - 어짜피 this는 `carManager`일텐데?
2. `arguments`는 유사배열객체이므로, slice 메소드를 사용하기 위해 빈 배열에 call을 사용했다.

> arguments는 함수 호출 시점에 실행 컨텍스트가 생성되며, 이 때 arguments 객체에 함수 호출과 함께 전달된 인자들이 담긴다.

```js
CarManager.execute = function(name) {
  return CarManager[name] && CarManager[name].apply(CarManager, [].slice.call(arguments, 1))
}

// 옛날 문법이여서 그런가?
// 똑같은걸 이렇게도 가능한데...
CarManager.execute = function(name, ...args) {
  return CarManager[name]?.(...args)
}
```

3. 메서드의 호출부가 느슨하게 결합된게 맞는지 모르겠음
```js
carManger.buy('ford', '1234')
// 'buy'의 API가 바뀌면 다 대응해줘야하는거 아닌가?
carManager.execute('buy', 'ford', '1234')
```

## 02
선언형이 길어지면 명령형이 되는건가..?

명령형 프로그래밍
- 어떤 방법으로 해야하는지 설명하는 경우

```js
function Example() {
  const [state, setState] = useState()
  const [error, setError] = useState()
  const [isLoading, setIsLoading] = useState(false)
  
  useEffect(() => {
    setIsLoading(true)

    fetch('http://example.com')
      .then(res => setState(res))
      .catch(err => setError(err))
      .finally(() => setIsLoading(false))
  }, [])

  if (isLoading) return '로딩'

  if (error) return '에러'

  return <p>{state}</p>
}
```


선언형 프로그래밍
- 무엇과 같은지를 설명하는 경우

```js
function Example() {
  const [state, setState] = useState()
  const [error, setError] = useState()
  
  useEffect(() => {
    fetch('http://example.com')
      .then(res => setState(res))
      .catch(err => setError(err))
  }, [])

  if (error) throw error
  
  return <p>{state}</p>
}

function Parent() {
  return (
    <ErrorBoundary fallback="에러">
      <Suspense fallback="로딩">
        <Example />
      </Suspense>
    </ErrorBoundary>
  )
}
```