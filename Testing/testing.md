# Testing

UI보다는 동작에 집중

버튼을 클릭했을때, 어떠한 동작이 발생하는가에 대한 테스트

state, effect, 와 같은 동작의 발생으로 인해 기대되는 결과가 나오는가?

Given - When - Then


1. test의 목적
2. 예상되는 값
3. 테스트하고 싶은 flow 작성
4. 이때는 어떤 가정이 필요한가?


## Jest ? testing library?

Jest와  testing library 는  interchnageable 한 관계가 아니다.

Jest 는  test-runner를 제공해서  command line 에서 테스팅이 가능하도록 해주고, 테스트에 관한 여러가지 함수들을 제공해준다.

Jest  자체만으로는  react 테스팅에 관한 어떤 것도 제공하지 않는다.

testing library는 react 컴포넌트를 테스트할 수 있는 라이브러리.




## getBy-  vs queryBy- vs findBy-


default로 `getBy` 사용

그런데 getBy로 element를 찾을 때, 해당 element를 못 찾으면 바로 throw error를 해버린다.

만약,  아래와 같이 `toBeNull()`을 check 하려고 할 때, getBy를 하면, assertion을 하기 전에 error 로인해서 test가 실패함.

queryBy는 에러를 내지 않기 때문에, 이런 상황에서 사용한다.
```javascript
test('renders App component', () => {
    render(<App />);
    expect(screen.queryByText(/Searches for JavaScript/)).toBeNull();
  });

``` 

`findBy` 는 비동기적인 elements에 대해 사용 가능. (will be there eventually)

예를 들어, 데이터 패칭 이후, 조건부 렌더링을 통해 생성되는 element가 이런 예.

```javascript
// App.js
function App() {
  const [user, setUser] = React.useState(null);
  React.useEffect(() => {
		// 데이터 패칭 
		// setUser(...);
  }, []);
  
  return (
    <div>
      { user ? <p>Signed in as {user.name}</p> : null}
    </div>
  );
}
// App.test.js
test('renders App component', async () => {
    render(<App />);
 
    expect(screen.queryByText(/Signed in as/)).toBeNull();
 
    expect(await screen.findByText(/Signed in as/)).toBeInTheDocument();
  });

```







#Testing