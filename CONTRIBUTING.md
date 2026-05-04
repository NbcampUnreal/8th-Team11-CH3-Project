# Contributing Guide — NBC Ch3 Team Project

5인 팀, 4주 일정(2026-05-01 ~ 2026-05-27) 기준. 모든 팀원이 동일한 규칙으로 일해서 머지 충돌·코드 스타일 충돌을 최소화하는 것이 목표.

---

## 1. 브랜치 전략 — GitHub Flow

```
main ────●────●────●────●────●   (항상 배포 가능한 상태)
   │     │    │    │    │
   │  feat/*  feat/* feat/* ...  (짧은 생명주기, PR로 병합)
```

- **`main`** — 항상 빌드 성공 + 기능 검증 완료된 상태 유지. **직접 커밋 금지**
- **`feat/*`** — 새 기능. 하루~3일 내 완료 후 PR
- **`fix/*`** — 버그 수정
- **`refactor/*`** — 동작 변경 없는 구조 개선
- **`chore/*`** — 빌드·설정·의존성

### 브랜치 네이밍

```
<type>/<scope>-<short-description>
```

**예시**:
```
feat/player-ads-aiming
feat/ai-zombie-perception
feat/wave-manager-round-5
fix/combat-hitbox-not-tracking
refactor/card-data-asset-split
chore/gitignore-asset-packs
```

규칙:
- 영문 소문자 + 하이픈
- scope는 담당 시스템 (`player` / `ai` / `wave` / `card` / `combat` / `hud` / `level` / `vfx` / `sfx` / `anim`)
- 한글 금지 (터미널·CI 깨짐)

---

## 2. 커밋 메시지 — Conventional Commits

```
<type>(<scope>): <subject>

[optional body]
```

### type

| type | 의미 |
|---|---|
| `feat` | 새 기능 |
| `fix` | 버그 수정 |
| `refactor` | 구조만 변경 (동작 동일) |
| `chore` | 빌드·설정·의존성·에셋팩 |
| `docs` | 문서 |
| `style` | 포맷팅만 (로직 변경 없음) |
| `test` | 테스트 |

### scope

`player` / `ai` / `wave` / `card` / `combat` / `hud` / `level` / `vfx` / `sfx` / `anim`

### 예시

```
feat(player): 과열 게이지 누적 + 무기 스왑 강제 전환 구현
feat(ai): 좀비 BT에 AIPerception Sight Stimulus 연결
feat(wave): WaveManager 5라운드 스폰 수·속도 증가 적용
fix(combat): 근접 공격 판정 박스가 적 위치 추적 안 되던 문제 수정
refactor(card): 카드 효과를 DataAsset으로 분리
chore(gitignore): Content/ 화이트리스트 패턴 추가
docs(readme): MVP 범위에 처형 연쇄 추가
```

### 규칙

- **subject는 한국어 OK** (내부 팀 프로젝트이므로)
- 명령형으로 작성 ("추가함" 아닌 "추가")
- 50자 이내 권장
- 상세 설명 필요 시 한 줄 띄우고 본문 작성

---

## 3. Pull Request 규칙

### PR 만들 때

1. **로컬에서 빌드 성공 확인** 후 push
2. GitHub에서 PR 생성 → 템플릿 채우기 (자동)
3. **리뷰어 1명 이상 지정** (담당 시스템에 가까운 팀원)
4. 관련 Issue 있으면 `Closes #N` 본문에 명시

### PR 제목

커밋 메시지와 동일 형식:
```
feat(ai): 돌진형 좀비 AI 구현
```

### 병합 전 체크

- [ ] 로컬 빌드 성공
- [ ] PIE 검증 완료
- [ ] 리뷰어 1명 이상 Approve
- [ ] 머지 충돌 해결
- [ ] 브랜치·커밋 컨벤션 준수

---

## 4. 병합 전략

| 상황 | 전략 |
|---|---|
| `feat/*` → `main` | **Squash Merge** (여러 커밋을 1개로 압축, main 히스토리 깔끔) |
| `fix/*` → `main` | Squash Merge |
| 큰 기능 (3일 이상 작업) | **Merge Commit** 가능 (병합 시점 명시) |

**금지**:
- `main` 직접 커밋·push
- Force push (특히 main)
- 자기 PR 리뷰 없이 merge

---

## 5. 담당 영역 (파일/폴더 소유)

충돌 회피를 위해 **각자 주로 작업하는 영역**을 분명히 정합니다.

| 담당 | 역할 | 주 작업 영역 |
|---|---|---|
| A — 오성현 | 플레이어 | `Source/.../Player/`, `Source/.../Weapon/`, `Content/Player/`, `Content/Weapon/` |
| B — 장재봉 | AI | `Source/.../AI/`, `Content/AI/` |
| C — 신장식 | 전투 / 게임 필 | `Source/.../Combat/`, `Content/Combat/`, `Content/VFX/Hit/` |
| D — 문창욱 | 시스템 | `Source/.../Card/`, `Source/.../GameFlow/`, `Content/UI/Card/` |
| E — 김하승 | 환경 / UI | `Source/.../HUD/`, `Content/Level/`, `Content/UI/HUD/` |

> 교차 작업이 필요하면 **사전 공유** (디스코드·카톡). 같은 파일에 2명 이상 동시 수정 금지.

---

## 6. 코드 스타일

### C++ (UE5)

- 클래스 접두사: `A`(Actor), `U`(UObject/Component), `F`(Struct), `I`(Interface) — UE 표준
- 프로젝트 접두사: **회의 확정 필요** (예: `NBC`, `Ch3`, 또는 게임 제목 기반)
- `TObjectPtr<T>` 우선 (UE 5.4+ 권장)
- `UPROPERTY()` / `UFUNCTION()` 리플렉션 매크로 정확히 사용
- 헤더 전방 선언 선호, CPP에서 include

### Blueprint

- 파일명: `BP_<Class>`, `WBP_<Widget>`, `ABP_<AnimBP>`, `BS_<BlendSpace>`
- 노드 정리: 선 꼬임 금지, Reroute 적극 활용
- 100노드 이상이 되면 C++ 분리 고려

### 에셋 네이밍

| 종류 | 규칙 |
|---|---|
| Static Mesh | `SM_<Name>` |
| Skeletal Mesh | `SK_<Name>` |
| Material | `M_<Name>` |
| Material Instance | `MI_<Name>` |
| Texture | `T_<Name>_<Type>` (예: `T_Zombie_D`, `T_Zombie_N`) |
| Anim Montage | `AM_<Name>` |
| Anim Sequence | `AS_<Name>` |
| Sound Wave | `SW_<Name>` |
| Sound Cue | `SC_<Name>` |
| DataAsset | `DA_<Name>` |
| DataTable | `DT_<Name>` |
| Niagara System | `NS_<Name>` |

---

## 7. Git 세부 규칙

### `.gitignore` 정책

- `Binaries/`, `Intermediate/`, `DerivedDataCache/`, `Saved/` 금지
- IDE 찌꺼기 (`.vs/`, `.idea/`, `*.DotSettings.user`) 금지
- 외부 마켓팩은 `.gitignore` 제외 — 각자 로컬에서 FAB 다운로드 후 `Content/<프로젝트명>/` 네임스페이스로 필요한 것만 마이그레이트

### LFS
- 현재 **미사용**. 단, 개별 `.uasset`이 100MB 초과하면 LFS 도입 검토 필요

### 금지 행동
- `main`에 직접 push
- Force push (특히 main, 리뷰 중인 feat 브랜치)
- `Binaries/` / `Intermediate/` / `DerivedDataCache/` / `Saved/` 커밋
- 라이선스 불명 에셋 커밋

---

## 8. 리뷰 문화

- **작게 자주** PR — 500줄 이상 PR은 지양
- 리뷰어는 **하루 내 응답** — 못 하면 미리 공유
- 의견 충돌 시 **디스코드 `#dev-rules` 또는 `#code-review` 채널**에서 토론 후 결론

---

## 문의

규칙 변경 제안은 디스코드 `#dev-rules` 채널에서 논의 후 PR로 반영.
