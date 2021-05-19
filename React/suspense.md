# Suspense

`Suspense`는 컴포넌트가 읽어들이고 있는 데이터가 아직 준비되지 않았다고 React에 알려줄 수 있는, 
데이터 불러오기 라이브러리에서 사용할 수 있는 메커니즘입니다.
```javascript
const resource = fetchProfileData();

// ProfilePage.js
function ProfilePage() {
  return (
    <Suspense fallback={<h1>Loading profile...</h1>}>
      <ProfileDetails />
      <Suspense fallback={<h1>Loading posts...</h1>}>
        <ProfileTimeline />
      </Suspense>
    </Suspense>
  );
}

// ProfileDetail.js
function ProfileDetails() {
  // 비록 아직 불러오기가 완료되지 않았겠지만, 사용자 정보 읽기를 시도합니다
  const user = resource.user.read();
  return <h1>{user.name}</h1>;
}

// ProfileTimeline.js
function ProfileTimeline() {
  // 비록 아직 불러오기가 완료되지 않았겠지만, 게시글 읽기를 시도합니다
  const posts = resource.posts.read();
  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.text}</li>
      ))}
    </ul>
  );
}
```

Suspense는 위의 문제에 대한 기존의 접근 방식과는 상당히 다르기 때문에, 처음 접할 때 종종 오해를 만들어냅니다. 가장 흔한 오해들을 명확히 짚어보겠습니다.

Suspense는 데이터 불러오기에 대한 구현이 아닙니다. GraphQL, REST 또는 특정한 데이터 형식, 라이브러리, 전송 또는 프로토콜을 사용한다고 가정하지 않습니다.
Suspense는 바로 사용할 수 있는 클라이언트가 아닙니다. fetch 또는 Relay를 Suspense로 “대체”할 수 없습니다. 다만, Suspense로 통합된 라이브러리를 사용할 수는 있습니다(예를 들어, 새로운 Relay API와 같은 것이 있습니다).
Suspense는 데이터 불러오기 작업과 뷰 레이어를 결합해주지 않습니다. UI 상에 로딩 상태를 표시할 수 있도록 조정하는 것을 돕지만, 이는 네트워크 로직을 React 컴포넌트에 종속시키는 것은 아닙니다.
