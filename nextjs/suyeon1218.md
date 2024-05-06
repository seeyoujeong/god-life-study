# 라우팅

## App Routing

Next JS 13 버전부터는 React Server 컴포넌트 상에서 빌드되는 App Router 가 새로 생겼다.

App Router 는 공유 레이아웃, 네스팅 라우팅, 로딩 상태, 에러 핸들링 등을 제공한다.

App Router 는 `app` 디렉터리 내에서 동작한다. `app` 디렉터리는 `pages` 디렉터리와 함께 작동한다. 이를 통해 어떤 경로는 새 동작을 하면서 다른 경로에 대해선 `pages` 디렉토리 내부에 이전 동작으로 유지할 수 있다. (? 이게 뭔 소리지)

`app` 디렉터리 내부에 있는 컴포넌트들을 서버 컴포넌트다.

### 폴더와 파일의 역할

- 폴더는 라우트들을 정의한다. 라우트는 중첩된 폴더들의 유일한 경로로, 루트 폴더에서 `page.js` 파일이 포함된 리프 폴더까지를 의미한다.
- 파일은 라우트 세그먼트에 따라 보여지는 UI 를 만든다.

### 라우트 세그먼트

라우트 세그먼트는 라우트 내부에 있는 각각의 폴더를 의미한다. 각각의 라우트 세그먼트는 URL 경로에 매핑된다.

### 중첩 라우트

중첩 라우트는 폴더를 중첩시켜 만들 수 있다. 예를 들어 `/dashboard/settings` 라는 라우트를 만들고 싶다면 다음과 같이 폴더를 생성한다.

- `/` root segment
- `dashboard` segment
- `settings` Leaf segment

### 파일 컨벤션

---

`layout`

세그먼트와 자식 컴포넌트 사이 공유되는 UI

---

`page`

라우트의 고유한 UI 또는 공개적으로 접근 가능한 경로 만들기(?)

---

`loading`

세그먼트나 자식 컴포넌트를 위한 Loading UI

---

`not-found`

세그먼트나 자식 컴포넌트를 위한 Not Found UI

---

`error`

세그먼트나 자식 컴포넌트를 위한 Error UI

---

`global-error`

전역적인 Error UI

---

`route`

서버사이드 API 엔드 포인트

---

`template`

리렌더링 되는 레이아웃 UI

---

`default`

다중 라우트에 대한 폴백 UI

### 컴포넌트 계층 구조

라우트 세그먼트의 특정 리액트 컴포넌트 파일은 계층 구조에 따라 렌더링된다.

- `layout.js`
- `template.js`
- `error.js`
- `loading.js`
- `not-found.js`
- `page.js` 또는 중첩된 `layout.js`

중첩된 라우트에선 세그먼트의 컴포넌트들은 부모 컴포넌트 내부에 위치하게 된다.

### 공동 배치(colocation)

특수 파일 외에도 컴포넌트, 스타일, 테스트 등의 파일을 `app` 디렉터리 폴더에 작성할 수도 있다.

폴더가 경로를 정의할 수도 있지만 `page.js` 또는 `route.js` 를 반환하는 콘텐츠만 주소가 지정되기 때문에 가능하다.

## Page Routing

페이지 라우터는 페이지 개념에 기반한 파일 시스템을 가지고 있다. 페이지가 `pages` 디렉터리에 추가되면, 이를 자동으로 route 에 추가한다.

# 렌더링

## Server Side Rendering(SSR)

페이지가 서버 사이드 렌더링을 사용하고 있다면 HTML 은 각 요청에 따라 생성된다.

각 페이지르 루이해 서버 사이르 렌더링을 사용하기 위해선 `export` 한 `async` 함수를 `getServerSideProps` 로 호출해야 한다. 해당 함수는 매 요청마다 서버에 의해 호출된다.

예를 들어 페이지에서 자주 업데이트되는 데이터(외부 API에서 가져온 데이터)를 미리 렌더링해야 한다고 가정하자. 아래와 같이 데이터를 패치해서 전달해주는 `getServerSideProps` 를 작성할 수 있다.

```jsx
export default function Page({ data }) {
	// 데이터는 여기서 렌더링된다
}

// 매 요청마다 호출한다
export async function getServerSideProps() {
	const res = await fetch(`https://.../data`);
	const data = await res.json();

	// 데이터를 프롭을 통해 전달한다
	return { props: { data } };
}
```

여기서 볼 수 있듯 `getServerSideProps` 는 `getStaticProps` 와 유사하게 동작한다. 하지만 `getServerSideProps` 는 빌드가 아니라 매 요청마다 실행된다.

## Static Site Generation(SSG)

Static Generation을 사용하면 HTML은 빌드 시 생성된다. 프로덕선 환경에선 `next build` 시 페이지 HTML이 생성된다. 해당 HTML은 CDN에 의해 캐싱되어 재사용될 수 있다.

Next.js에선 데이터가 있든 없든 정적으로 페이지를 생성할 수 있다.

### 데이터가 없는 SSG

기본적으로 Next.js는 데이터 페칭 없이 SSG를 사용하여 미리 렌더링을 한다.

```jsx
function About() {
	return <div>About</div>;
}

export default About;
```

### 데이터가 있는 경우의 SSG

어떤 페이지가 pre-rendering 을 위해 외부의 페이를 요청하기 위해 2가지 방법을 사용할 수 있다.

1. `getStaticProps` : 페이지 콘텐츠가 외부 데이터에 의존하는 경우
2. `getStaticPaths` : 페이지 경로가 외부 데이터에 의존하는 경우

**시나리오 1. 페이지 콘텐츠가 외부 데이터에 의존하는 경우**

데이터를 pre-render 하기 위해 Next.js 는 `export` 한 `async` 함수를 `getStaticProps` 을 같은 파일 내부에서 호출할 수 있다. 해당 함수는 빌드 시 호출되어 프리렌더링 시 페이지의 props 로 전달된다.

```jsx
export default function Blog({ posts }) {
	// 외부의 posts 데이터를 받아와서 렌더링 해야함
	return (
		<ul>
			{posts.map((post) => (
				<li>{post.title}</li>
			))}
		</ul>
	);
}

// 빌드 시 getStaticProps 가 호출된다
export async function getStaticProps() {
	const res = await fetch('https://.../posts');
	const posts = await res.json();

	// props 의 posts 변수를 담아 Blog 로 전달한다
	return {
		props: {
			posts,
		},
	};
}
```

**시나리오2. 페이지 경로가 외부 데이터에 의존하는 경우**

Next.js 는 다이나믹 라우트를 지원한다. 예를 들어 `id` 에 기반한 블로그 포스트를 보여주기 위해 `pages/posts/[id].js` 로 호출되는 파일을 생성해야 할 수 있다.

이를 핸들링하기 위해 Next.js 는 `export` 한 `async` 함수인 `getStaticPaths` 를 다이나믹 페이지에서 호출할 수 있다. 해당 함수는 빌드타임에 호출되어 미리 렌더링할 경로를 지정할 수 있다.

```jsx
// 빌드 시 호출
export async function getStaticPaths() {
	const res = await fetch('https://.../posts');
	const posts = await res.json();

	// posts 를 기반으로 미리 렌더링할 경로 가져오기
	const paths = posts.map((post) => ({
		params: { id: post.id },
	}));

	// { fallback: false } 는 다른 경로에 대해선 404로 표시할 것을 의미한다
	return { paths, fallback: false };
}
```

또한 `pages/posts/[id].js` 에선 `id` 를 사용해 해당 글의 데이터를 가져와 미리 렌더링하는 데 사용하기 위해 `getStaicProps` 를 export 해야한다.

```jsx
export default function Post({ post }) {
	// 단일 포스트를 렌더링
}

export async function getStaticPaths() {
	// ...
}

export async function getStaticProps({ params }) {
	// params 로 부터 id 를 받아옴
	const res = await fetch(`https://.../posts/${params.id}`);
	const post = await res.json();

	// 포스트를 패치해서 Post 컴포넌트의 props 으로 전송
	return { props: { post } };
}
```

## Client-side Rendering (CSR)

CSR에선 브라우저가 페이지에 필요한 최소한의 HTML과 Javascript파일을 다운로드 한다. Javascript는 DOM을 업데이트하고 페이지를 렌더링하기 위해 사용한다.

페이지가 처음 로드되면 사용자는 최종 페이지를 보기까지 지연을 느낄 수 있다. 이는 Javscript가 다운로드되어 파싱 및 실행까지 페이지가 완전히 보여지지 않기 때문이다.

첫 로딩 이후 페이지는 필요한 데이터만 가져오면 되고, 페이지 일부만 렌더링할 수 있어 일반적으로 동일한 웹사이트의 다른 페이지로 이동할 때 속도가 빨라진다.

Next.js 에서 CSR을 향상시키는 두가지 방법이 있다.

1. SSR메서드 대신 React 의 `useEffect` 훅을 사용한다.
2. SWR 이나 Tanstack Query 같은 데이터 페칭 라이브러리를 사용한다. (추천)

```jsx
import React, { useState, useEffect } from 'react';

export function Page() {
	const [data, setData] = useState(null);

	useEffect(() => {
		const fetchData = async () => {
			const response = await fetch('https://api.example.com/data');
			if (!response.ok) {
				throw new Error(`HTTP error! status: ${response.status}`);
			}
			const result = await response.json();
			setData(result);
		};

		fetchData().catch((e) => {
			// 에러 핸들링
			console.error('An error occurred while fetching the data: ', e);
		});
	}, []);

	return <p>{data ? `Your data: ${data}` : 'Loading...'}</p>;
}
```

위 예제에서 컴포넌트는 `Loading…` 과 함께 렌더링된다. 그 뒤 데이터가 패치되면 데이터를 화면에 표시한다.

비록 `useEffect` 에서 데이터 패치를 하는 패턴을 React 에서 봤다고 하더라도, 성능 향상과 캐싱, 최적화 면에서 데이터 패칭 라이브러리를 사용할 것을 권장한다.

```jsx
import useSWR from 'swr';

export function Page() {
	const { data, error, isLoading } = useSWR(
		'https://api.example.com/data',
		fetcher
	);

	if (error) return <p>Failed to load.</p>;
	if (isLoading) return <p>Loading...</p>;

	return <p>Your Data: {data}</p>;
}
```

# 데이터 패칭

## getStaticProps

`getStaticProps` 함수를 페이지에서 export 하는 경우 Next.js 는 `getStaticProps` 에서 반환하는 프로퍼티를 사용하기 위해 페이지를 프리 렌더링한다.

```jsx
import type { InferGetStaticPropsType, GetStaticProps } from 'next'

type Repo = {
  name: string
  stargazers_count: number
}

export const getStaticProps = (async (context) => {
  const res = await fetch('https://api.github.com/repos/vercel/next.js')
  const repo = await res.json()
  return { props: { repo } }
}) satisfies GetStaticProps<{
  repo: Repo
}>

export default function Page({
  repo,
}: InferGetStaticPropsType<typeof getStaticProps>) {
  return repo.stargazers_count
}
```

### getStaticProps 를 언제 사용하면 좋을까?

- 페이지를 렌더링하기 위한 데이터를 사용자 요청 전, 빌드할 때 사용해야 하는 경우
- 데이터가 headless CMS 로부터 오는 경우 (프론트엔드가 백엔드로부터 API 요청을 통해 데이터를 불러오는 경우)
- (SEO 등을 위해) 페이지가 프리 렌더링되어야 하거나 매우 빨라야 하는 경우 - `getStaticProps` 는 HTML 과 JSON 파일을 생성하고 두 파일은 CDN 에 의해 캐싱될 수 있다.
- 데이터가 사용자 별로 캐싱되지 않고, 공개적으로 캐싱되어야 하는 경우

### getStaticProps 는 언제 실행되나?

- `getStaicProps` 는 서버 컴포넌트에서 동작하고, 클라이언트 컴포넌트에선 동작하지 않는다.
- `next build` 가 실행되면 동작한다.
- 백그라운드에서 `fallback: true` 를 사용하면 실행된다
- 초기 렌더링 전에 `fallback: blocking` 을 사용하면 호출된다.
- 백그라운드에서 `revalidate` 를 사용하면 실행된다.
- 백그라운드에서 `revalidate()` 를 실행하면 온디맨드 방식으로 실행된다.

## getStaticPaths

다이나믹 라우트에 쓰이는 페이지거나 `getStaticProps` 를 사용하면, 정적으로 생성되는 경로들의 리스트가 정의되어야 한다.

다이나믹 라우트를 사용하는 페이지에서 `getStaticPaths` 를 호출할 때, Next.js는 정적으로 `getStaticPaths` 에 의해 명시된 경로를 프리렌더링 한다.

### 언제 getStaticPaths 를 써야할까?

다이나믹 라우트를 사용하여 정적으로 프리 렌더링을 하는 경우 getStaticPaths 를 사용해야하며 다음 경우에도 사용할 수 있다.

- headless CMS로부터 데이터를 전달받는 경우
- 데이터베이스로부터 데이터를 전달받는 경우
- 파일 시스템으로부터 데이터를 전달받는 경우
- 공공 데이터로서 사용될 데이터를 캐싱하는 경우
- SEO 를 위해 프리 렌더링 되어야 하거나 매우 빨라야 하는 경우

### getStaticPaths 는 언제 실행될까?

`getStaticPaths` 는 오직 프로덕션 환경에서 빌드할 때만 일어난다. 런타임 환경에선 호출되지 않는다.

## getServerSideProps

`getServerSideProps` 는 요청 시간에 데이터를 패치하고 컨텐츠를 렌더링하기 위해 사용하는 함수이다.

### 예시

`getServerSideProps` 를 Page 컴포넌트에서 exporting 하는 방식으로 사용할 수 있다.

```jsx
import type { InferGetServerSidePropsType, GetServerSideProps } from 'next'

type Repo = {
  name: string
  stargazers_count: number
}

export const getServerSideProps = (async () => {
  const res = await fetch('https://api.github.com/repos/vercel/next.js');
  const repo: Repo = await res.json();

  return { props: { repo } }
}) satisfies GetServerSideProps<{ repo: Repo }>

export default function Page({
  repo,
}: InferGetServerSidePropsType<typeof getServerSideProps>) {
  return (
    <main>
      <p>{repo.stargazers_count}</p>
    </main>
  )
}
```

### 언제 getServerSideProps 를 사용할까?

개인적인 사용자 데이터 또는 요청 시에만 알 수 있는 데이터에 의존하는 페이지를 렌더링해야 하는 경우 사용해야 한다. 예를 들어 인증 헤더 등이 해당한다.

요청 시 데이터를 가져올 필요가 없거나 미리 렌더링된 HTML을 캐싱하려는 경우엔 getStaticProps 를 사용하는 게 좋다.

## Client-side Fetching

클라이언트 측에서 데이터를 가져오는 것은 페이지에서 SEO 를 신경쓸 필요가 없는 경우, 데이터를 미리 렌더링할 필요가 없는 경우, 페이지의 콘텐츠를 자주 업데이트 해야하는 경우 유용하다. 서버 측에서 데이터를 가져올 때와 달리 클라이언트 측에선 컴포넌트 수준에서 데이터를 가져올 수 있다.

페이지 수준에서 패칭을 하게 되면 데이터를 가져오고 데이터가 변경되면 페이지의 콘텐츠가 업데이트된다. 페이지 수준에서 데이터를 패칭하면 컴포넌트가 마운트되는 시점에 데이터를 가져오고, 데이터가 변경되면 컴포넌트의 콘텐츠가 업데이트된다.

클라이언트 측에서 데이터 패칭을 하려면 어플리케이션의 성능과 페이지 속도에 영향을 줄 수 있다는 점을 유의해야 한다. 클라이언트 측에선 데이터가 캐싱되지 않기 때문이다.

아래는 useEffect 훅을 사용해 클라이언트 측에서 데이터 패칭을 하는 예시이다.

```jsx
import { useState, useEffect } from 'react';

function Profile() {
	const [data, setData] = useState(null);
	const [isLoading, setLoading] = useState(true);

	useEffect(() => {
		fetch('/api/profile-data')
			.then((res) => res.json())
			.then((data) => {
				setData(data);
				setLoading(false);
			});
	}, []);

	if (isLoading) return <p>Loading...</p>;
	if (!data) return <p>No profile data</p>;

	return (
		<div>
			<h1>{data.name}</h1>
			<p>{data.bio}</p>
		</div>
	);
}
```

하지만 클라이언트 측에서 데이터를 불러올 경우 SWR 이나 Tanstack Query 등의 데이터 패칭 라이브러리를 사용하는 것을 강력하게 권장한다.
