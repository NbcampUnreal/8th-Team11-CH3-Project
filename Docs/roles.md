# Ch3 팀프로젝트 — 역할 분담

*작성일: 2026-04-23 / 최종 갱신: 2026-05-04 (WBS 기준)*
*기준 문서: `ch3-wbs.md` · `ch3-feature-prep.md` · `ch3-team-interfaces.md` · `ch3-role-c-implementation.md`*

---

## 난이도 기준

| 등급 | 기준 |
|---|---|
| **상** | UE5 핵심 서브시스템 직접 다룸 — AI, 애니메이션, 캐릭터 컨트롤러 |
| **중** | Blueprint 로직 + UMG 중심 — 시스템 간 연결이 핵심 |
| **하** | 에셋 배치 + HUD 위젯 중심 — 엔진 숙련도보다 꼼꼼함이 중요 |

---

## 역할 요약

| 역할 | 핵심 시스템 | 난이도 | 담당자 | 비고 |
|---|---|---|---|---|
| **A — 플레이어** | TPS 컨트롤러, 무기, 과열 | **상** | 오성현 | — |
| **B — AI** | 좀비 AI, 웨이브 매니저, 보스 | **상** | 장재봉 | — |
| **C — 전투** | 처형 시스템, 애니메이션 연동 | **중** (처형 구현 시 상) | 신장식 | **팀장 / 제출 총괄 / 발표 준비** |
| **D — 시스템** | 카드/업그레이드, 게임 스테이트 | **중** | 문창욱 | — |
| **E — 환경/UI** | 아레나 레벨, HUD, 에셋 셋업 | **하~중** | 김하승 | 발제 갭 UI 4건 추가 |

---

## 마일스톤 캘린더 (WBS §1)

| 마일스톤 | 시작 | 종료 | 핵심 산출물 |
|---|---|---|---|
| 준비 (PR) | 2026-05-01 | 2026-05-03 | 레포·플러그인·페이즈 Enum |
| **Tracking (TR)** | 2026-05-04 (월) | 2026-05-07 (목) | 이동·사격·추적·히트 통합 빌드 |
| **Alpha (AL)** | 2026-05-08 (금) | 2026-05-14 (목) | 게임 필 + 카드/페이즈 + HUD |
| **Beta (BT)** | 2026-05-15 (금) | 2026-05-21 (목) | 폴리싱 + 보스전 + COULD |
| 🔴 코드 프리즈 | **2026-05-21 (목)** | — | 변경 동결 |
| 발표 준비 (RL) | 2026-05-22 (금) | 2026-05-26 (화) | 빌드 / PPT / 영상 / 리허설 |
| **제출 마감** | **2026-05-27 (수) 12:00** | — | GitHub + PPT + YouTube |
| **발표회** | **2026-05-27 (수) 14:00–18:00** | — | 10분 발표 |

### 트래킹 회의

| 일시 | 종류 | 핵심 안건 |
|---|---|---|
| **2026-05-06 (수) 20:00–21:30** | 🔴 Tracking 중간 점검 | WBS §6.4 P0 합의 5개 결정 / 블로커 식별 |
| 2026-05-07 (목) 저녁 | Tracking 종료 데모 | 통합 빌드 — 이동·사격·추적·히트 |
| 2026-05-08 (금) | Alpha 킥오프 | Tracking 회고 / Alpha 작업 분배 |
| 매주 목·금 (AL~BT) | 정기 회의 | 합의 / 진척 / 블로커 |
| 2026-05-21 (목) | 코드 프리즈 점검 | 빌드 통과 / 미완성 — 보류 처리 |

---

## 역할 A — 플레이어 `난이도: 상` `담당: 오성현`

### 담당 시스템
- TPS 캐릭터 컨트롤러 (이동, 조준, 카메라)
- 무기 시스템 (근접 고정 + 원거리 카드 획득) — Projectile 발사 확정
- 과열 게이지 (원거리 과열 → 근접 강제 전환 → 냉각)
- 무기 스왑 로직 (수치 유지)
- **달리기 / 점프 / 상태별 속도·콜리전** ✦ (발제 갭)
- **재장전** ✦ (발제 갭)

### WBS 작업 (요약)
| 마일스톤 | 작업 ID | 핵심 |
|---|---|---|
| PR | A-PR-01 | Enhanced Input 셋업 |
| TR | A-TR-01~05 | TPS 컨트롤러 / Projectile 사격 / 달리기·점프·상태 ✦ |
| AL | A-AL-01~06 | Weapon Actor / 과열 게이지 / 무기 스왑 / 재장전 ✦ / 과열 피드백 / HUD 바인딩(E) |
| BT | A-BT-01~02 | 카드 인터페이스 통합(D) / 입력·카메라 폴리싱 |

### 필요 지식
- UE5 `ACharacter` / `CharacterMovementComponent`
- Enhanced Input System (UE5.1+)
- Weapon Actor 클래스 설계
- Timeline 또는 Timer로 과열 게이지 관리

### 병목 포인트
과열 시스템 — 무기별 독립 게이지, 과열 시 강제 스왑, 냉각 중 원거리 잠금 로직이 가장 복잡.

### 협업 의존성
- **C (전투)** ← `OnZombieHit(FHitResult, Damage, EDamageType)` 인터페이스 ✅ 합의
- **D (시스템)** ← 아이템 효과 인터페이스 (⚠️ 05/09 마감)
- **E (환경/UI)** ← HUD 데이터 바인딩 — 폴링 vs 델리게이트 (⚠️ 05/08 마감)

### 소스 폴더
`Source/NBC_Ch3_TeamProject/Player/` — `Content/Player/`

---

## 역할 B — AI `난이도: 상` `담당: 장재봉`

### 담당 시스템
- 좀비 AI (Recast NavMesh + `CrowdAIController` + BT/Blackboard)
- 웨이브 스포너 (웨이브별 스폰 수/속도/위치 관리, Spawn Culling)
- 웨이브 클리어 판정 및 다음 웨이브 전환
- 보스 BT 별도 설계 (패턴 N개 + 페이즈 전환)
- **좀비 방어력 변수** ✦ (발제 갭)

### WBS 작업 (요약)
| 마일스톤 | 작업 ID | 핵심 |
|---|---|---|
| PR | B-PR-01~03 | NavMesh / `CrowdAIController` / 좀비 ABP DefaultSlot |
| TR | B-TR-01~03 | 1단계 추적 / 2단계 BT+BB / Decorator 분기 |
| AL | B-AL-01~05 | HitReact / OnDeath + IDamageable / 웨이브 매니저 / 클리어 이벤트(D) / 방어력 ✦ |
| BT | B-BT-01~04 | 보스 BT / 보스 패턴 N개 / Spawn Culling / 난이도 스케일링(D) |

### 필요 지식
- UE5 NavMesh + `AIController` / `CrowdAIController`
- Behavior Tree + Blackboard (`TargetActor` / `TargetLocation` / `IsTargetInAttackRange`)
- 출처 — https://tjdgus123.tistory.com/64
- Custom Spawner Actor 설계

### 병목 포인트
대규모 좀비 AI 성능 — `CrowdAIController` + Spawn Culling 병행 필수. 보스 BT는 일반 좀비 BT 안정화 후 별도 설계.

### 협업 의존성
- **C (전투)** ← `IDamageable` 인터페이스 ✅ 합의 / Physics Asset 본 이름 (⚠️ 05/15 마감)
- **D (시스템)** ← 웨이브 클리어 이벤트 포맷 (⚠️ 05/10 마감)
- **E (환경/UI)** ← 스폰 포인트 Actor 규격 (⚠️ 05/06 회의 마감) / 보스 체력바 인터페이스
- **C ↔ B** — 공격 애니 방식 SM vs Montage (⚠️ 05/06 회의 마감)

### 소스 폴더
`Source/NBC_Ch3_TeamProject/AI/` — `Content/AI/`

---

## 역할 C — 전투 / 게임 필 `난이도: 중` `처형 구현 시 상` `담당: 신장식` `팀장`

### 담당 시스템

**기반 시스템**
- Easy Combo Buffering 플러그인 통합
- AnimBP 근접 콤보 Montage + Anim Notify State (타격 판정 활성화 구간)

**MUST — 게임 필**
- 히트 판정 & 데미지 시스템
- 좀비 HitReact Montage
- 히트스톱 — `CustomTimeDilation` 0.05~0.1초 (피격 대상 + 플레이어), **`SetGlobalTimeDilation` 금지**
- 카메라 셰이크 — 근접/원거리 각 1종 (`UCameraShakeBase` 서브클래스)
- 피격 VFX — `UGameplayStatics::SpawnEmitterAtLocation` (Niagara)

**SHOULD — 처형 시스템**
- 처형 트리거 (체력 30% 이하 → bExecutable + E에 UI 요청)
- 처형 Anim Montage (재생 중 이동 잠금 → BlendOut Notify로 복귀)
- 처형 연쇄 — `SphereOverlapActors(N미터)` → 좀비 경직, **N값 UPROPERTY 노출(하드코딩 금지)**

**원거리 탄착 처리**
- 인터페이스 통일: `OnZombieHit(FHitResult, float Damage, EDamageType)`
- 원거리 HitReact: `FHitResult.ImpactNormal` 방향 판별 → 앞/뒤/옆 플린치 분기
- 원거리 히트스톱: `CustomTimeDilation 0.03~0.05`초 (근접보다 짧게)

**COULD**
- 피격 부위 파괴 (`BreakConstraint` + 라그돌, **Chaos 금지**)
- 파쿠르 Vault / Mantle (Motion Warping)

**발제 갭 ✦** — 데미지량 → E 송신

### WBS 작업 (요약)
| 마일스톤 | 작업 ID | 핵심 |
|---|---|---|
| PR | C-PR-01~02 | Easy Combo Buffering / AnimBP Montage + Notify State |
| TR | C-TR-01~02 | 히트 판정·데미지 / HitReact 연동 |
| AL | C-AL-01~08 | 히트스톱 / 셰이크 2종 / VFX / 처형 트리거+Montage+연쇄 / 원거리 탄착 / 데미지량 → E ✦ |
| BT | C-BT-01~03 | 처형 연쇄 VFX / [COULD] 부위 파괴 / [COULD] Vault·Mantle |
| RL | C-RL-01~02 | **팀장 제출 총괄** / 발제 해석 발표 노트 ✦ |

### 필요 지식
- UE5 `Animation Blueprint` + `Anim Montage`
- `UGameplayStatics::SpawnEmitterAtLocation` (VFX 호출)
- `UCameraShakeBase` (카메라 셰이크)
- Anim Notify State (히트스톱 타이밍 제어)
- Easy Combo Buffering 플러그인 API

### 병목 포인트
히트스톱 — 전체 게임을 멈추는 게 아니라 **피격 대상과 플레이어만** 멈춰야 자연스러움. 타이밍 값 튜닝이 까다로움.

### 협업 의존성 (팀장 추가 책임)
- **A (플레이어)** ← `OnZombieHit` 인터페이스 ✅ 합의
- **B (AI)** ← `IDamageable` ✅ / 공격 애니 방식 (⚠️ 05/06 회의) / Physics Asset 본 (⚠️ 05/15)
- **E (환경/UI)** ← 처형 UI 포맷 (⚠️ 05/09) / 데미지량 표시 인터페이스
- **A ↔ C** — CMC 양도 (Mantle, ⚠️ 05/17)
- **팀장 책임** — 매주 목·금 회의 진행 / WBS·인터페이스 문서 갱신 / 발표 준비 총괄(PPT·영상·리허설) / 제출 총괄 (X-RL-05) — 코드 프리즈(05/21) 후 RL 구간에 집중

### 소스 폴더
`Source/NBC_Ch3_TeamProject/Combat/` — `Content/Combat/`

---

## 역할 D — 시스템 `난이도: 중` `담당: 문창욱`

### 담당 시스템

**페이즈 관리**
- 페이즈: **전투(Combat) / 보상(Reward)** — Enum 정의
- 전환 주체: **GameMode**
- 보상 페이즈 → **포털 이동** (UI 즉시 표시 안 함)

**난이도 스케일링**
- 웨이브 진행에 따라 적 강화
- **웨이브 체류 시간이 길수록 적 강화**
- 웨이브에 따른 몹 스폰 수 조절

**클리어 / 오버**
- 웨이브 — 보스 격파 OR 몹 kill 수
- 게임 — 웨이브 N개 클리어 → 보스 스폰 → 격파 시 클리어
- 게임 오버 — 캐릭터 체력 0

**카드 / 아이템**
- 카드 데이터 → **DataTable / DataAsset**으로 관리
- 카드 선택창 → 랜덤 N개 추출
- 카드 획득 → **TMap 중첩** 누적
- 변경 사항 — **카드 → 아이템 드롭**, **포털 이동** 후 보상 페이즈

**발제 갭 ✦**
- 점수 / 킬 카운트 시스템 채택 결정 (05/06 회의)
- "특수공격" 발제 해석 — 처형 대체 / 별도 스킬
- "제한 시간 / 미션 실패" — 체류시간 강화로 대체 (발표 시 설명)

### WBS 작업 (요약)
| 마일스톤 | 작업 ID | 핵심 |
|---|---|---|
| PR | D-PR-01~02 | 페이즈 Enum / DataTable·DataAsset 스키마 |
| TR | D-TR-01~03 | 게임 스테이트 / 페이즈 전환 / 점수 채택 결정 ✦ |
| AL | D-AL-01~06 | 보상 포털 / 카드 UI / 효과(TMap 중첩) / 아이템 효과 IF(A) / 스케일링 / 클리어 이벤트(B) |
| BT | D-BT-01~02 | 보스 보상 / 밸런싱 |

### 필요 지식
- UE5 `UMG` Widget Blueprint
- `DataTable` + `FTableRowBase` / `DataAsset`
- Blueprint Interface (카드 효과를 다른 Actor에 적용)
- `GameInstance` / `GameMode` / `GameState` 데이터 유지

### 병목 포인트
카드 효과 적용 — 화력 카드가 A의 대미지를 올리고, 무기 획득 카드가 A의 무기 슬롯을 변경하는 인터페이스 설계. A와 사전 합의 필요.

### 협업 의존성
- **B (AI)** ← 웨이브 클리어 이벤트 (⚠️ 05/10)
- **A (플레이어)** → 아이템 효과 인터페이스 (⚠️ 05/09)
- **C (전투)** ↔ 보상 페이즈 히트 판정 (⚠️ 05/12)
- **E (환경/UI)** ← 점수/킬 카운트 채택 결정 동기화 (05/06 회의)

### 소스 폴더
`Source/NBC_Ch3_TeamProject/System/` — `Content/System/`

---

## 역할 E — 환경/UI `난이도: 하~중` `담당: 김하승`

### 담당 시스템
- 아레나 레벨 (Post Apocalyptic Urban + Metro Subway 에셋 배치)
- 스폰 포인트 Actor 배치 (B와 위치 협의)
- HUD (체력 / 탄약 / 과열 게이지 / 웨이브 카운트)
- 에셋 임포트 및 머티리얼 셋업
- 처형 연쇄 VFX 연동 (Niagara 기초)
- 게임 스테이트 UI (클리어 / 오버)

**발제 갭 ✦**
- **히트마커** (필수)
- **데미지량 표시** (필수, C와 인터페이스)
- **킬 확정 표시** (필수, B `OnDeath` 연동)
- **크로스헤어** (필수, 담당 결정 — 05/06 회의)
- **점수 / 킬 카운트 HUD** (D 채택 시)

**보스전 UI (도전 기능)**
- 보스 체력바
- 특수 공격 경고
- 페이즈 전환 알림

### WBS 작업 (요약)
| 마일스톤 | 작업 ID | 핵심 |
|---|---|---|
| PR | E-PR-01~03 | 에셋 임포트 / 박스 아레나 / Figma → UMG 변환 계획 |
| TR | E-TR-01~02 | 스폰 포인트 배치(B) / 머티리얼 셋업 |
| AL | E-AL-01~08 | HUD 4종 / 히트마커 ✦ / 데미지량 ✦ / 킬 확정 ✦ / 크로스헤어 ✦ / 처형 UI / 바인딩(A) / 점수 HUD |
| BT | E-BT-01~05 | 처형 연쇄 VFX / 보스 체력바 / 특공 경고 / 페이즈 전환 알림 / 게임 스테이트 UI |

### 필요 지식
- UE5 Level Editor / World Outliner
- UMG HUD Widget (데이터 바인딩 — 폴링 / 델리게이트)
- 에셋 임포트 (FBX, 텍스처, Skeletal Mesh)
- Niagara 파티클 기초 (VFX 연동만)

### 병목 포인트
HUD 과열 게이지 + 발제 갭 UI 4종 — A의 데이터 바인딩 인터페이스 합의가 모든 HUD 작업의 선행 조건.

### 협업 의존성
- **A (플레이어)** ← 체력/탄약/과열 데이터 바인딩 (⚠️ 05/08 마감)
- **B (AI)** → 스폰 포인트 위치 (⚠️ 05/06 회의) / ← OnDeath / 보스 체력바 인터페이스
- **C (전투)** ← 처형 UI 포맷 (⚠️ 05/09) / 데미지량 송신
- **D (시스템)** ← 게임 스테이트 / 점수 채택 결정

### 소스 폴더
`Source/NBC_Ch3_TeamProject/UI/` — `Content/Environment/` — `Content/UI/`

---

## 역할 간 의존 관계

```
A (플레이어)
├── → C (전투): OnZombieHit 인터페이스 제공 ✅
├── → D (시스템): 카드 효과 수신 인터페이스 (⚠️ 05/09)
└── → E (환경/UI): HUD 데이터 바인딩 (⚠️ 05/08)

B (AI)
├── → C (전투): IDamageable 좀비 구현 ✅
├── → D (시스템): 웨이브 클리어 이벤트 (⚠️ 05/10)
├── → E (환경/UI): OnDeath / 보스 체력바
└── ← E (환경/UI): 스폰 포인트 위치 (⚠️ 05/06)

C (전투)
├── ← A (플레이어): OnZombieHit 호출
├── ↔ B (AI): IDamageable / Physics Asset 본 (⚠️ 05/15)
└── → E (환경/UI): 처형 UI / 데미지량 (⚠️ 05/09)

D (시스템)
├── ← B (AI): 웨이브 클리어 이벤트
├── → A (플레이어): 카드 효과 전달
└── → E (환경/UI): 게임 스테이트 / 점수 채택 결정

E (환경/UI)
├── ← A: HUD 데이터
├── ↔ B: 스폰 포인트 / OnDeath
├── ← C: 처형 UI / 데미지량
└── ← D: 게임 스테이트
```

### 마일스톤별 병렬 작업 가능성

| | A | B | C | D | E |
|---|---|---|---|---|---|
| **PR (05/01-03)** | ✅ 독립 | ✅ 독립 | ✅ 독립 | ✅ 독립 | ✅ 독립 |
| **TR (05/04-07)** | ✅ | ✅ | ⏳ A 사격 이후 | ⏳ B 스폰 이후 | ⏳ B 스폰 협의 |
| **AL (05/08-14)** | ✅ | ✅ | ✅ | ✅ | ⏳ A 바인딩 합의 후 |
| **BT (05/15-21)** | 폴리싱 | 보스 | COULD | 밸런싱 | 보스 UI |

---

## 합의 사전 마감 (WBS §9)

| 합의 항목 | 관련 인원 | 마감일 |
|---|---|---|
| 점수 / 킬 카운트 채택 | D ↔ E | **05/06 회의** |
| "특수공격" 발제 해석 | D / 전원 | **05/06 회의** |
| 크로스헤어 담당 | A vs E | **05/06 회의** |
| 스폰 포인트 Actor 규격 | E ↔ B | **05/06 회의** |
| 공격 애니 방식 SM/Montage | B ↔ C | **05/06 회의** |
| 달리기·점프·상태별 속도/콜리전 | A | 05/07 |
| HUD 데이터 바인딩 | A ↔ E | 05/08 |
| 근접 투척 스킬 발제 해석 | D / 전원 | 05/08 |
| 아이템 효과 인터페이스 | D ↔ A | 05/09 |
| 처형 UI 포맷 | C ↔ E | 05/09 |
| 웨이브 클리어 이벤트 포맷 | B ↔ D | 05/10 |
| 보상 페이즈 히트 판정 | C ↔ D | 05/12 |
| 보스 패턴 수 / 페이즈 전환 조건 | B | 05/14 |
| Physics Asset 본 이름 | B ↔ C | 05/15 |
| CMC 양도 (Mantle) | A ↔ C | 05/17 |

*문서 정합성: 본 문서는 `ch3-wbs.md`에서 파생된 역할 요약. WBS와 충돌 시 WBS가 우선.*
