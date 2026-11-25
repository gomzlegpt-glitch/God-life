# 우리집 갓생살기

부부가 각자 자기 공부·운동 스케줄을 보고 기록하고, 자기 시간표대로 휴대폰 알림을 받는 앱(PWA)입니다.
상대방 스케줄은 '함께 보기' 탭에서만 봅니다. 나중에 사복직 합격 도우미, 전기기사 도우미를 같은 앱 안에 붙입니다.

- 화면·호스팅: GitHub Pages (무료)
- 로그인·데이터·알림 서버: Supabase (무료)
- `config.js` 가 비어 있으면 로그인 없이 **미리보기 모드**로 열립니다.

## 파일

| 파일 | 내용 |
|---|---|
| index.html | 앱 화면 전체 (오늘 / 이번 주 / 함께 보기 / 내 스케줄 / 알림·설정) |
| config.js | Supabase 주소와 공개(anon) 키 |
| manifest.webmanifest, sw.js, icon-*.png | 홈 화면 설치·오프라인·알림 표시 |
| supabase/schema.sql | 테이블, 권한, 실시간, 1분마다 알림 예약 |
| supabase/functions/notify/index.ts | 알림 서버 (블록 시작 · 저녁 기록 · 일요일 점검) |

## 설치 순서

### 1. Supabase
1. supabase.com 가입 → New project (지역: Northeast Asia (Seoul)).
2. Project Settings → API 에서 **Project URL** 과 **anon public key** 를 `config.js` 에 넣습니다. (service_role 키는 절대 넣지 않습니다)
3. SQL Editor → `schema.sql` 전체 붙여 넣기 → 맨 아래 `<프로젝트-ref>` 를 Project URL 의 앞부분(예: `abcd1234`)으로 바꾸고 Run.
4. Edge Functions → Deploy a new function → 이름 `notify` → `index.ts` 내용 붙여 넣기 → **Verify JWT 끄기** → Deploy.
5. Authentication → Sign In / Providers → Email: **Confirm email 끄기** (가입 즉시 로그인되도록).
6. 두 분이 앱에서 각자 가입을 마치면 → **Allow new users to sign up 끄기** (다른 사람이 가입 못 하게).

### 2. GitHub Pages
1. GitHub 저장소 `godsaeng` 생성 (Public).
2. 이 폴더의 파일을 그대로 올립니다 (`supabase` 폴더는 올려도 되고 빼도 됩니다).
3. Settings → Pages → Branch `main` / root → Save. 1~2분 뒤 `https://<아이디>.github.io/godsaeng/` 로 열립니다.

### 3. 휴대폰
- 안드로이드: 크롬으로 열기 → 로그인 → '알림·설정' → 앱 설치 → 설치된 앱에서 '이 기기에서 알림 켜기' → '테스트 알림 보내기'.
- 아이폰(iOS 16.4 이상): Safari로 열기 → 공유 → '홈 화면에 추가' → 홈 화면 아이콘으로 열어 로그인 → 알림 켜기 → 테스트.

## 알림 동작
- 1분마다 서버가 각자의 시간표를 보고 보냅니다. 남편 기기에는 남편 시간표, 아내 기기에는 아내 시간표 알림만 갑니다.
- 블록 시작: 공부·운동·집안일 블록 시작 N분 전 (근무·출퇴근 같은 고정 일정은 제외). 야근·회식·약속이 있는 날은 최소 분량으로 안내.
- 저녁 기록: 정한 시각에, 기록 안 한 블록이 남아 있을 때만.
- 일요일 점검: 이번 주 공부 시간 / 운동 횟수 / 밀린 칸.
- 시각은 한국 시간(KST) 기준입니다.

## 공부 도우미 묶는 방법 (예정)
한 Supabase 프로젝트, 한 번 로그인으로 세 기능을 씁니다.

```
godsaeng/            ← 이 앱 (허브 + 스케줄)
godsaeng/civil/      ← 사복직 합격 도우미 (아내)
godsaeng/elec/       ← 전기기사 도우미 (남편)
```

- 같은 주소 아래 하위 폴더라 로그인이 그대로 이어지고, 홈 화면 아이콘도 하나입니다.
- 각자 '내 스케줄 → 공부 도우미 연결'에서 고른 도우미가 오늘 화면에 바로가기로 뜹니다 (아내=사복직, 남편=전기기사).
- 데이터는 접두어로 나눕니다: 스케줄 `sch_*`, 사복직 `civil_*`, 전기기사 `elec_*`.
- 도우미에서 문제를 풀면 `study_log` 에 공부 시간이 남고, 스케줄의 공부 기록에 합산합니다.
