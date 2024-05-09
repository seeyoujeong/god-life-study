# Next.js

## 폴더 구조 - app

- `app` : 모든 라우트, 컴포넌트, 로직을 포함
- `public` : 모든 정적 리소스를 포함

## 중첩 라우팅

Next.js는 파일 시스템 라우팅을 사용해서 중첩 라우팅을 구현했다. 각 폴더는 URL segment에 매핑되는 route segment를 나타낸다.

### page.tsx

라우트에 필수적으로 요구되는 파일로 리액트 컴포넌트를 export한다.

선택적으로 `params`와 `searchParams`를 prop으로 받을 수 있다. (서버용)

```tsx
export default function Page({
  params,
  searchParams,
}: {
  params: { slug: string };
  searchParams: { [key: string]: string | string[] | undefined };
}) {
  return <h1>My Page</h1>;
}
```

### layout.tsx

여러 페이지가 공유할 UI를 생성할 수 있는 파일이다.

하위 폴더에 속한 페이지들이 UI를 공유받는다.

페이지 컴포넌트가 업데이트 될때 레이아웃은 리렌더링이 일어나지 않는다.

파일 내의 컴포넌트는 각 페이지 혹은 레이아웃이 될 `children` prop을 받는다.

```tsx
export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div>
      <div>
        <SideNav />
      </div>
      <div>{children}</div>
    </div>
  );
}
```

루트 폴더(app)에는 필수적으로 요구되며 모든 페이지에서 공유받는다.

루트 레이아웃에서 `<html>`, `<body>` 태그를 수정할 수 있으며 메타데이터를 추가할 수 있다.

### loading.tsx

페이지의 콘텐츠가 로드될 동안 대신 보여줄 UI를 생성할 수 있는 파일이다.

하위 폴더에 있는 page.tsx에도 영향을 주기때문에 소괄호로 감싼 이름을 가진 폴더를 생성하여 loading.tsx와 page.tsx를 옮기면 된다.

### error.tsx

route segment에서 일어나는 모든 에러를 잡아서 fallback UI를 표시하는 파일이다.

“use client”가 필요하다.

`error`와 `reset` props를 받는다.

- `error` : JS의 `Error` 객체 인스턴스
- `reset` : route segment를 리렌더링하는 함수

### not-found.tsx

리소스가 존재하지 않을 경우에서 에러를 보여주고 싶을때 사용하는 파일이다.

`next/navigation`의 `notFound`를 호출하여 해당 파일의 UI를 표시할 수 있다.

`error.tsx` 보다 우선적으로 가져온다.

## navigation

`<a>` 를 사용하여 페이지를 이동할 경우 모든 페이지가 새로고침된다. 그러므로 `Link` 컴포넌트를 사용해야 한다. 예. `<Link href={…}>…</Link>`

`Link` 컴포넌트는 `next/link`에서 불러와 사용하면 된다.

Next.js는 route segment 별로 애플리케이션을 자동으로 코드 분할한다. 이는 초기 로드시 전체 애플리케이션을 로드하는 전통적인 SPA와는 다르다.

라우트별로 코드 분할을 할 경우, 특정 페이지에서 에러가 나도 나머지 부분은 동작한다.

Next.js는 링크된 route를 자동으로 미리 패치한다. 이를 통해 페이지가 바로 보여줄 수 있다.

next/cache의 `revalidatePath`함수를 통해 route의 캐시를 제거할 수 있다.

next/navigation의 `redirect`함수로 리다이렉트할 수 있다.

## Hook

이벤트 리스너 혹은 훅을 사용하기 위해서는 파일 상단에 `“use client”`를 추가해야 한다.

### next/navigation

`usePathname()`

- 현재 경로를 확인할 수 있다.
- 파라미터를 제외한 경로를 가져온다.
- 사용법
  ```tsx
  const pathname = usePathname();
  ```

`useSearchParams()`

- 현재 URL의 파라미터에 접근할 수 있다.
- 읽기 전용 객체인 `ReadonlyURLSearchParams`를 반환한다.
- 사용법

  ```tsx
  const searchParams = useSearchParams();
  const params = new URLSearchParams(searchParams);

  params.set("page", "1"); // 추가
  params.set("query", "aaa");

  params.toString(); // page=1&query=aaa

  params.delete("query"); // 삭제
  ```

`useRouter()`

- 새로고침 없이 URL 업데이트를 할 수 있다.
  - `push(href: string, { scroll: boolean })`: client-side navigation을 수행(히스토리에 추가)
  - `replace(href: string, { scroll: boolean })`: client-side navigation을 수행
  - `refresh()`: 현재 경로 새로고침(React 상태나 브라우저 상태에 영향 없음)
- 사용법

  ```tsx
  const { replace } = useRouter();

  replace(`${pathname}?${params.toString()}`);
  ```

## Server Components

서버 컴포넌트는 데이터 패칭에 사용된다.

프로미스를 지원하며 비동기 작업을 수행한다.

`useEffect`, `useState`, 데이터 패칭 라이브러리 없이 `async/await`를 사용할 수 있다.

서버 컴포넌트는 서버에서 실행되며 비용이 높은 데이터 패칭이나 로직을 서버에서 수행하여 클라이언트에 결과만 보낼 수 있다.

서버 컴포넌트는 추가적인 API layer 없이 직접 데이터베이스에서 쿼리할 수 있다.

```tsx
export default async function Page() {
  const data = await fetchData();
  // ...
}
```

> **request waterfalls**
> 이전 네트워크 요청이 완료돼야 이후 요청을 시작할 수 있는 과정
>
> ```tsx
> const data1 = await fetchData1();
> const data2 = await fetchData2(); // fetchData1()을 기다린다.
> const data3 = await fetchData3(); // fetchData2()를 기다린다.
> ```

## Static Rendering

빌드 또는 유효성 검사 중에 데이터 패칭 및 렌더링이 서버에서 일어난다.

결과물은 CDN에 캐싱된다.

웹사이트에 빠른 접속, 서버 로드 단축, SEO에 도움이 된다.

## Dynamic Rendering

사용자가 요청할때마다 서버에서 렌더링이 일어난다.

실시간 데이터와 사용자별 콘텐츠를 볼 수 있다.

## Streaming

준비가 된 리소스부터 서버에서 클라이언트로 점진적으로 보내는 데이터 전송 기술이다.

느린 데이터 요청으로 인한 전체 페이지가 안보이는 현상을 예방할 수 있다.

### Streaming a component

컴포넌트별로 스트리밍을 하기위해서는 데이터 패칭 함수를 상위 컴포넌트에서 해당 컴포넌트로 옮기고 Suspense로 감싸주는 작업을 하면 된다.

```tsx
<Suspense fallback={<RevenueChartSkeleton />}>
  <RevenueChart />
</Suspense>
```

과도하게 컴포넌트별로 Suspense를 감싸면 popping effect가 일어날 수 있기 때문에 wrapper component를 사용하면 좋다.

Suspense boundaries를 정하는 기준은 아래와 같다.

- 유저 경험을 어떻게 줄지
- 콘텐츠에 우선순위를 정해서
- 컴포넌트가 데이터 페칭에 의존할때

## Server Actions

React Server Actions은 서버에서 바로 비동기 코드를 돌릴 수 있다.

API 엔드포인트를 수동으로 생성할 필요가 없어진다.

보안을 위해 도입됐다.

`“use sever”`를 추가하여 서버 함수로 만들 수 있다. 서버와 클라이언트 컴포넌트에서 사용할 수 있다.

서버 컴포넌트안에 `“use server”`를 추가해서 Server Action을 직접 추가할 수 있다.

React에서 Server Actions을 `form`의 `action`속성에 할당해서 사용할 수 있다.

만약 특정한 값을 인수로 넘기고 싶다면 `bind` 메서드를 사용해야 한다.

```tsx
const updateInvoiceWithId = updateInvoice.bind(null, invoice.id);

return (
  <form action={updateInvoiceWithId}>
    <input type="hidden" name="id" value={invoice.id} />
  </form>
);
```

## Dynamic Route Segments

Dynamic Route Segments를 만들기위해선 폴더명을 대괄호로 감싸야한다. 예. `[id]`, `[post]`

## Metadata

Next.js는 자동으로 app 폴더에 있는 favicon.ico와 opengraph-image.jpg 파일을 인식하여 적용해준다.

페이지 타이틀과 설명을 설정하려면 page.tsx나 layout.tsx에 아래와 같은 코드를 추가하면 된다.

```tsx
import { Metadata } from "next";

export const metadata: Metadata = {
  title: "Acme Dashboard",
  description: "The official Next.js Course Dashboard, built with App Router.",
  metadataBase: new URL("https://next-learn-dashboard.vercel.sh"),
};
```

만약 반복적인 작업을 줄이고 타이틀을 페이지별로 다르게 주고싶다면 루트 layout.tsx에 아래와 같은 코드를 추가한다.

```tsx
import { Metadata } from "next";

export const metadata: Metadata = {
  title: {
    template: "%s | Acme Dashboard",
    default: "Acme Dashboard",
  },
  description: "The official Next.js Learn Dashboard built with App Router.",
  metadataBase: new URL("https://next-learn-dashboard.vercel.sh"),
};
```

그리고 하위 page.tsx에 아래와 같은 코드를 추가한다.

```tsx
export const metadata: Metadata = {
  title: "Invoices",
};
```
