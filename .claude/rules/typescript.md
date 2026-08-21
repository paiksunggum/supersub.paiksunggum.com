---
paths:
  - "**/*.ts"
  - "**/*.tsx"
---

## TypeScript 규칙

> 기준 코드베이스: `www/` (Next.js 16 · React 19 · TypeScript 5.7.3)
> 아래 규칙은 새로 만든 것이 아니라 **`www/`에 이미 자리잡은 패턴을 문서화**한 것이다.
> 기존 코드와 충돌하는 규칙을 발견하면 임의로 코드를 고치지 말고 먼저 알린다.

---

### 1. strict mode 필수

`www/tsconfig.json`에 `"strict": true`가 이미 켜져 있다. **끄거나 개별 옵션으로 완화하지 않는다.**

- `// @ts-ignore` · `// @ts-expect-error` · `// eslint-disable`로 타입 에러를 덮지 않는다.
- 타입 에러는 타입을 고쳐서 해결한다. 우회가 불가피하면 그 사실을 사용자에게 보고한다.

### 2. any 타입 사용 금지

현재 `www/` 전체에 `any`가 **0건**이다. 이 상태를 유지한다.

외부에서 들어온 값(fetch 응답, `JSON.parse`, `localStorage`)은 **`any`가 아니라 `unknown`**으로 받고 좁혀서 쓴다.

```ts
// lib/sport-favorites-storage.ts — 실제 패턴
const parsed = JSON.parse(raw) as unknown;
if (!Array.isArray(parsed)) return [];
return parsed.filter((slug): slug is string => typeof slug === "string");
```

```ts
// app/chat/page.tsx — 실제 패턴
function parseApiError(raw: unknown, status: number): string {
  if ("error" in raw && typeof (raw as { error: unknown }).error === "string") {
    // ...
  }
}
```

- 배열 필터링으로 `undefined`를 걷어낼 때는 **타입 서술어(type predicate)** 를 붙인다.
  `.filter((sport): sport is SportItem => sport !== undefined)`
- 응답 형태를 아는 경우에만 단언한다: `(await res.json()) as CurrentUser`
- 알 수 없는 구조는 `Record<string, unknown>`으로 둔다.

### 3. 인터페이스보다 타입 별칭 선호

`www/` 실측: **타입 별칭 67건 vs 인터페이스 6건.** 신규 코드는 `type`으로 쓴다.

```ts
// ✅ hooks/use-current-user.ts
export type CurrentUser = {
  id: number;
  email: string;
  name: string;
  role: string;
};
```

`interface`는 선언 병합(declaration merging)이 필요한 경우에만 쓴다. 남아 있는 6건은 과도기 코드이므로 **이번 작업과 무관하면 건드리지 않는다.**

---

### 4. 컴포넌트 Props 타입

- 이름은 **`{컴포넌트명}Props`** 형태의 `type` 별칭.
- `React.FC`는 쓰지 않는다 (현재 사용 0건). 함수 선언에 직접 구조 분해로 받는다.

```tsx
// components/sport-page-header.tsx — 실제 패턴
type SportPageHeaderProps = {
  slug: string;
  league: string;
  brandColor: string;
};

export default function SportPageHeader({
  slug,
  league,
  brandColor,
}: SportPageHeaderProps) { /* ... */ }
```

### 5. 유니온 리터럴 (enum 금지)

`enum`은 현재 0건이다. 고정된 선택지는 **문자열 유니온 리터럴**로 표현한다.

```ts
type UploadKind = "csv" | "video";
type Gender = "male" | "female" | "none";
export type NavVariant = "home" | "warm" | "default";
```

리터럴 타입이 `string`으로 넓어지는 자리에서는 `as const`를 쓴다.

```ts
const thread = [...messages, { role: "user" as const, text }];
```

### 6. React import 스타일

기본은 **`import * as React from "react";`** (26건으로 지배적). 훅 몇 개만 쓰는 파일은 named import도 허용하되, 한 파일 안에서 두 방식을 섞지 않는다.

이벤트 핸들러 인자는 구체 타입으로 명시한다.

```tsx
const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => { ... };
const onFileChange = (e: React.ChangeEvent<HTMLInputElement>) => { ... };
```

### 7. Next.js App Router 타입

Next 16 기준으로 **`params`는 `Promise`** 다. 반드시 `await`한다.

```tsx
// app/sports/[slug]/page.tsx — 실제 패턴
type SportPageProps = {
  params: Promise<{ slug: string }>;
};

export default async function SportPage({ params }: SportPageProps) {
  const { slug } = await params;
}
```

Route Handler는 요청 바디 타입을 선언하고 **선택 필드(`?`)로 방어**한다.

```ts
type ChatRequestBody = {
  message?: string;
  messages?: ChatMessage[];
};

const body = (await request.json()) as ChatRequestBody;
```

### 8. 경로 별칭

`@/*` 별칭을 쓴다 (`tsconfig.json`의 `paths`). 상대경로 `../../`를 새로 만들지 않는다.

```ts
import { Button } from "@/components/ui/button";
import { useSportFavorites } from "@/hooks/use-sport-favorites";
import { cn } from "@/lib/utils";
```

### 9. 데이터 모듈

정적 데이터는 타입을 먼저 선언하고 명시적으로 주석(annotation)을 단다.

```ts
// lib/sports-data.ts
export type SportItem = {
  slug: string;
  /** League brand color for circular icon backgrounds */
  brandColor: string;
};

export const sports: SportItem[] = [ /* ... */ ];
```

---

### 10. 작성 후 검증 (필수)

```bash
cd www
pnpm lint          # ESLint
npx tsc --noEmit   # 타입 체크
```

린트·타입 에러는 무시하지 않는다. 반드시 수정한 뒤 완료 보고한다.

---

### 예외 구역

`www/components/ui/**`는 **shadcn/ui 생성 코드**다. 이 규칙(특히 3·6번)과 다른 스타일을 쓰더라도 정상이며, 해당 컴포넌트를 직접 수정할 이유가 없으면 그대로 둔다.