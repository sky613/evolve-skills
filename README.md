# evolve

LLM의 작업 경험을 바탕으로 실수, 반복 작업, 사용자 규칙을 분석하고 재사용 가능한 Skill을 제안하는 Meta-Skill 모음.

## 원칙

LLM에 판단을 위임하고 시스템은 지침, 승인, 기록, 검증에 집중한다. Python, DB, 외부 API 및 별도 서버가 필요하지 않다. 각 Skill은 독립 설치 가능하며 공통 규칙을 내장한다. references와 templates는 유지보수용으로 제공한다.

## 명령어

| Claude Code | Codex | 기능 |
|---|---|---|
| `/evolve` | `$evolve` | 전체 분석 및 개선 제안 |
| `/evolve-errors` | `$evolve-errors` | 실수 및 실패 원인 분석 |
| `/evolve-patterns` | `$evolve-patterns` | 반복 작업 및 패턴 감지 |
| `/evolve-rules` | `$evolve-rules` | 사용자 규칙 및 선호도 분석 |
| `/evolve-create` | `$evolve-create` | 신규 Skill 생성 |
| `/evolve-improve` | `$evolve-improve` | 기존 Skill 개선 |
| `/evolve-optimize` | `$evolve-optimize` | 지침 및 토큰 사용량 최적화 |
| `/evolve-validate` | `$evolve-validate` | 중복, 충돌 및 품질 검증 |
| `/evolve-self` | `$evolve-self` | evolve 자체 개선 |
| `/evolve-history` | `$evolve-history` | 분석 및 개선 이력 확인 |

Claude Code에서는 slash 호출, Codex에서는 `$` Skill 호출을 사용한다. 환경 버전과 Skill 검색 설정에 따라 표시 방식이 다를 수 있다. 아래 설치는 프로젝트 단위 기준이며 Plugin Marketplace 등록은 포함하지 않는다.

## 설치 (macOS/Linux)

압축을 풀고 설치하려는 프로젝트 루트에서 실행한다. `/absolute/path/to/evolve`를 실제 압축 해제 경로로 변경한다. 기존 같은 이름의 Skill은 건너뛴다.

```bash
SRC="/absolute/path/to/evolve/skills"
DEST=".claude/skills" # Codex: .agents/skills
mkdir -p "$DEST"
for dir in "$SRC"/evolve*; do
  [ -f "$dir/SKILL.md" ] || continue
  name="$(basename "$dir")"
  if [ -e "$DEST/$name" ] || [ -L "$DEST/$name" ]; then
    printf 'Skipped existing: %s\n' "$name"
  else
    cp -R "$dir" "$DEST/$name"
  fi
done
```

새 세션에서 Skill 목록을 확인하고 `/evolve` 또는 `$evolve`를 호출한다. 목록에 없으면 설치 위치와 해당 환경의 Skill 검색 지원 여부를 확인한다. Windows에서도 같은 폴더 구조로 복사할 수 있다.

## 사용 예시

```text
/evolve
/evolve-errors
/evolve-create rag
/evolve-improve code-review
/evolve-optimize all
/evolve-validate all
/evolve-self
/evolve-history
```

Codex에서는 `/` 대신 `$`를 사용한다. 대상 이름과 `all`은 자연어 인수이며 별도 CLI 파서가 없다. `all`은 접근 가능한 Skill 범위를 뜻한다.

## 저장 및 승인

기본은 읽기 전용 분석과 초안 제안이다. 실제 적용 전에 변경 내용 및 경로를 확인하고 승인한다. 기록을 남기려면 예를 들어 프로젝트 내 `.evolve/observations/`를 승인된 경로로 지정한다. 각 관찰은 `templates/observation.md` 구조를 참고해 저장할 수 있다. 경로가 없으면 응답으로만 보고하며 history는 기록 없음으로 표시한다. 세션 간 자동 추적은 제공하지 않는다.

## 선택적 자동 검토

AGENTS.md 또는 CLAUDE.md에 아래 지침을 직접 추가할 수 있다.

```markdown
작업 완료 시 의미 있는 실수나 반복 작업이 있으면 evolve Skill을 활용해 개선안을 제안한다. 개선 사항이 없다면 추가 분석을 생략한다. 승인 없이 파일을 변경하거나 기록하지 않는다.
```

이는 LLM 지침이며 강제 실행 Hook은 아니다. 항상 로드되는 지침을 늘리면 토큰 사용량도 증가할 수 있다.

## 검증 예시

- 동일한 오류를 두 번 수정한 사례: errors 및 patterns는 실제 근거를 구분해야 한다.
- 일회성 사용자 요청: rules는 영구 규칙으로 자동 승격하지 않아야 한다.
- 이미 동일한 Skill이 있는 경우: create는 중복 생성 대신 개선을 제안해야 한다.
- 승인되지 않은 변경: improve와 self는 diff 제안에서 멈춰야 한다.
- 기록이 없는 경우: history는 이력을 만들어내지 않아야 한다.

v0.1은 파일 구조 및 메타데이터 정적 검사를 수행했다. 실제 Claude Code/Codex 실행 및 행동 테스트는 설치 환경에서 수행해야 한다.
# evolve-skills
