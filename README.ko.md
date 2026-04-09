> [English](README.md) | 한국어

# PowerShell Profile

Windows 우선 개발 환경을 위한 재사용 가능한 `pwsh` (PowerShell 7) 프로필 설정입니다. 초보자도 쉽게 사용할 수 있도록 설계되었으며,_power user_를 위한 심층적인 커스터마이징도 가능합니다.

## 목차

1. [빠른 시작](#1-빠른-시작)
2. [스크린샷 / 출력 예시](#2-스크린샷--출력-예시)
3. [요구사항](#3-요구사항)
4. [설치](#4-설치)
5. [기능](#5-기능)
6. [설정](#6-설정)
7. [내장 명령어](#7-내장-명령어)
8. [워크플로 예시](#8-워크플로-예시)
9. [Oh My Posh 테마](#9-oh-my-posh-테마)
10. [VS Code 연동](#10-vs-code-연동)
11. [문제 해결](#11-문제-해결)
12. [저장소 구조](#12-저장소-구조)
13. [Git / 공개 모델](#13-git--공개-모델)
14. [참고](#14-참고)

---

## 1. 빠른 시작

30초 만에 작동하는 설정을 완료합니다. 각 줄을 복사하여 붙여넣으세요.

### Step 1: PowerShell 7 설치

Microsoft에서 다운로드하거나 winget을 사용하세요:

```powershell
winget install Microsoft.PowerShell
```

### Step 2: 저장소를 프로필 디렉토리에 클론

```powershell
# 프로필 디렉토리 경로 확인
Split-Path $PROFILE -Parent
# 클론 실행 (본인 경로로 교체)
git clone https://github.com/YOUR_USERNAME/PowerShell "C:\Users\YOUR_NAME\Documents\PowerShell"
```

### Step 3: 로컬 설정 파일 생성

```powershell
# 클론한 디렉토리 내에서
Copy-Item .\config.template.psd1 .\config.local.psd1
```

### Step 4: 새 PowerShell 세션 시작

```powershell
pwsh
```

### Step 5: 작동 확인

```powershell
phelp
```

도움말 메뉴가 표시되어야 합니다. 프롬프트에 상자나 물음표가 보이면 [문제 해결](#11-문제-해결)에서 Nerd Font 수정 방법을 확인하세요.

---

## 2. 스크린샷 / 출력 예시

모든 것이 정상적으로 작동할 때 보여지는 화면입니다.

### 시작 배너

새 터미널을 열면 프로필이 자동으로 상태 배너를 표시합니다:

```
======================================================================
[PowerShell profile] 2026-04-09 18:30:00
----------------------------------------------------------------------
[Python] python  : C:\Users\user\dev\_global-py\.venv\Scripts\python.exe (v3.12.4)
[Python] venv    : _global-py (C:\Users\user\dev\_global-py\.venv)
[uv]     managed : ALLOWED
[uv]     python  : C:\Users\user\dev\_global-py\.venv\Scripts\python.exe (v3.12.4)
[MSYS2]  status  : preferred=UCRT64 | active=UCRT64 | ucrt64[2] mingw64[not-in-path]
[Help]   phelp   : phelp (use -a for all; -d/-s/-c for sections)
======================================================================
```

### 도움말 메뉴 (`phelp`)

```
=== PowerShell profile help ===
Usage:
  phelp [ -d | -s | -c | -a ]
  phelp [ --devtools | --shortcuts | --profilecommands | --all ]
```

### 개발 도구 확인 (`Test-DevTools`)

```
=== Dev tool status ===
[oh-my-posh] C:\Users\user\AppData\Local\Programs\OhMyPosh\bin\oh-my-posh.exe (v3.0.0)
[git]        C:\Program Files\Git\cmd\git.exe (git version 2.45.0.windows.1)
[python]     Python 3.12.4
[pip]        pip 24.0 from C:\Users\user\dev\_global-py\.venv\Scripts\pip.exe
[uv]         uv 0.4.0
[gcc]        gcc (x86_64-posix-seh, built by MSYS2) 14.2.0
[g++]        g++ (x86_64-posix-seh, built by MSYS2) 14.2.0
[cmake]      C:\msys64\ucrt64\bin\cmake.exe (cmake version 3.29.2)
[ninja]      C:\msys64\ucrt64\bin\ninja.exe (v1.12.1)
```

---

## 3. 요구사항

### PowerShell 7+ (`pwsh`) — 필수

PowerShell 7은 핵심 런타임입니다. 이 프로필은 레거시 Windows PowerShell 5.1이 아닌 `pwsh` (버전 7 이상)를 대상으로 합니다.

**필요한 이유**: 이 프로필은 PowerShell 5.1에서 사용할 수 없는 PowerShell 7 기능과 문법을 사용합니다. 많은 헬퍼 함수도 크로스 플랫폼 호환성 (`$IsWindows`, `$HOME` 등)에 의존합니다.

**winget으로 설치**:

```powershell
winget install Microsoft.PowerShell
```

**확인**:

```powershell
pwsh --version
```

### Oh My Posh — 필수

Oh My Posh는 아이콘, git 상태, 경로 인식 색상을 포함한 스타일링된 프롬프트를 렌더링합니다. 프로필에는 하드코딩된 테마 (`theme/clean-detailed_custom.omp.json`)가 포함되어 있으며, 동적 경로 아이콘 매핑을 위해 `POSH_*` 환경변수를 내보냅니다. Oh My Posh가 없으면 프로필은 계속 로드되지만 핵심 프롬프트 환경이 손실됩니다.

**기능**: 일반 PowerShell 프롬프트를 디렉토리 컨텍스트, git 브랜치, 오류 상태, 실행 시간을 표시하는 테마 가능하고 기능이 풍부한 프롬프트로 대체합니다.

**winget으로 설치**:

```powershell
winget install JanDeDobbeleer.OhMyPosh
```

**확인**:

```powershell
oh-my-posh version
```

[Oh My Posh 문서](https://ohmyposh.dev/docs/installation/windows)

### Nerd Font — 필수 (Oh My Posh 사용 시)

포함된 Oh My Posh 테마는 Nerd Fonts의 글리프와 아이콘을 사용합니다 (폴더 아이콘, git 기호, 언어 로고, 경로 아이콘). Nerd Font가 설치되어 있고 터미널 폰트로 선택되지 않으면 이러한 글리프가 `□` 또는 `?` 상자로 표시됩니다.

**권장 폰트**: `CascadiaCode Nerd Font` 또는 `FiraCode Nerd Font` — 둘 다 이 프로필과 잘 작동합니다.

**설치**:

```powershell
# 옵션 1: Oh My Posh 내장 폰트 설치 도구 (권장)
oh-my-posh font install CascadiaCode

# 옵션 2: nerdfonts.com에서 수동 다운로드
```

**터미널에서 설정**: 설치 후 터미널 설정으로 이동하여 Nerd Font 변형을 활성 폰트로 선택하세요 (예: `CascadiaCode Nerd Font`).

[Nerd Fonts 다운로드](https://www.nerdfonts.com/font-downloads)

### `uv` — 선택 사항

`uv`는 Astral의 빠른 Python 패키지 관리자 및 환경 도구입니다. 프로필은 글로벌 venv 관리 및 관리 Python 정책을 위해 이를 사용합니다.

**설명**: `pip` + `virtualenv`를 10-100배 빠르게 대체합니다. 글로벌 Python 환경 활성화/비활성화 워크플로에 사용됩니다.

**winget으로 설치**:

```powershell
winget install Astral.uv
```

[uv 문서](https://docs.astral.sh/uv/)

### MSYS2 — 선택 사항

MSYS2는 Windows에서 GCC, G++, CMake, Ninja 툴체인을 제공합니다. 프로필은 `UCRT64`와 `MINGW64` 툴체인 사이를 전환할 수 있습니다.

**설명**: GCC/MinGW-w64 툴체인을 갖춘 Windows를 위한 Linux 스타일 빌드 환경입니다. C/C++ 프로젝트 컴파일 또는 CMake로 소프트웨어 빌드에 필수적입니다.

**설치**: [msys2.org](https://www.msys2.org/)에서 다운로드하여 설치 프로그램을 실행하세요.

**참고**: 설치 후 MSYS2 경로를 `config.local.psd1`에 추가하세요:

```powershell
@{ Msys2Root = "C:\msys64" }
```

### VS Code — 선택 사항

Visual Studio Code를 사용한다면, VS Code 터미널에서 동일한 프로필을 로드하는 전용 래퍼가 포함되어 있습니다.

**기능**: `Microsoft.VSCode_profile.ps1`가 메인 프로필을_dot-source_하므로, VS Code 터미널에서 동일한 프롬프트, 단축 변수, 명령어를 사용할 수 있습니다.

[VS Code](https://code.visualstudio.com/)

---

## 4. 설치

### 옵션 1 — 프로필 디렉토리에 직접 클론

가장 간단한 설정입니다. 저장소가 PowerShell 프로필 디렉토리가 됩니다.

```powershell
# Step 1: 프로필 디렉토리 경로 확인
Split-Path $PROFILE -Parent
# 결과: C:\Users\YOUR_NAME\Documents\PowerShell

# Step 2: 클론 실행 (아래 경로 교체)
git clone https://github.com/YOUR_USERNAME/PowerShell "C:\Users\YOUR_NAME\Documents\PowerShell"

# Step 3: 로컬 설정 파일 생성
Copy-Item .\config.template.psd1 .\config.local.psd1

# Step 4: 새 터미널 열기
pwsh
```

저장소가 PowerShell 프로필 디렉토리에 직접 있다면, `pwsh`를 시작할 때 `Microsoft.PowerShell_profile.ps1`와 `Microsoft.VSCode_profile.ps1`가 자동으로 로드됩니다.

### 옵션 2 — 다른 위치에서 심볼릭 링크

저장소를 원하는 위치에 두고 프로필 디렉토리에 심볼릭 링크를 생성합니다.

**관리자 터미널 필요**:

```powershell
# 심볼릭 링크 생성 (관리자로 실행)
New-Item -ItemType SymbolicLink -Path "C:\Users\YOUR_NAME\Documents\PowerShell\Microsoft.PowerShell_profile.ps1" -Target "C:\path\to\repo\Microsoft.PowerShell_profile.ps1"
New-Item -ItemType SymbolicLink -Path "C:\Users\YOUR_NAME\Documents\PowerShell\Microsoft.VSCode_profile.ps1" -Target "C:\path\to\repo\Microsoft.VSCode_profile.ps1"
```

프로필은 스크립트 위치를 기준으로 설정과 테마 경로를 해결하므로, 심볼릭 링크된 진입점도 올바르게 작동합니다.

### 설치 확인

```powershell
phelp          # 도움말 메뉴가 표시되어야 함
Test-DevTools  # 감지된 도구와 버전이 나열되어야 함
```

---

## 5. 기능

### 프롬프트 및 셸 UX

- `theme/clean-detailed_custom.omp.json`에서 로드되는 커스텀 테마를 사용하는 Oh My Posh 프롬프트
- 내보내진 `POSH_*` 환경변수 (예: `POSH_DESKTOP`, `POSH_DEV`, `POSH_WORKSPACE`)로 구동되는 경로 아이콘 매핑
- Python 버전, venv 상태, uv 관리 Python 정책, MSYS2 툴체인 상태를 표시하는 시작 배너
- 좁은 터미널에 적응하는 콘솔 너비 인식 레이아웃

### 폴더 단축 변수

프로필이 빠른 내비게이션을 위해 PowerShell 변수를 생성합니다:

| 변수 | 별칭 | 경로 |
|----------|---------|------|
| `$DESKTOP` | — | 시스템 데스크탑 폴더 |
| `$DEV` | — | 기본값 `~/dev` |
| `$WORKSPACE` | `$WS` | 기본값 `~/Desktop/Workspace` |
| `$OPENCODE` | `$OC` | 기본값 `~/.config/opencode` |
| `$GOOGLEDRIVE` | `$GDRIVE`, `$GD` | `config.local.psd1`에서 설정된 경우에만 |

이러한 단축 변수를 사용하면 전체 경로를 입력하지 않고 프로젝트 폴더로 쉽게 이동할 수 있습니다.

### Python 및 `uv` 헬퍼

- **글로벌 venv 활성화/비활성화**: `Activate-UvGlobal` (`ga`) 및 `Deactivate-UvGlobal` (`gde`)가 글로벌 Python 환경을 전환합니다
- **Python 경로 검사**: `Get-PythonExecutable` (`pywhere`, `pyw`)가 활성 python 경로를 표시합니다
- **uv 관리 Python 정책**: `Enable-UvManagedPythonBlock` (`uvblock`) 및 `Disable-UvManagedPythonBlock` (`uvallow`)가 `uv`가 자체 관리 Python을 설치할 수 있는지 제어합니다
- **환경 정보**: `Get-PythonEnvInfo` (`pyinfo`, `pyi`)가 prefix, base prefix, venv 감지를 표시합니다

### MSYS2 툴체인 전환

Windows에서 UCRT64와 MINGW64 툴체인 사이를 전환합니다:

| 명령어 | 설명 |
|---------|-------------|
| `Use-UCRT64` | UCRT64 툴체인을 우선 (현대적, 권장) |
| `Use-MINGW64` | MINGW64 툴체인을 우선 (레거시 프로젝트) |
| `Show-Msys2Toolchain` | 현재 툴체인 상태, PATH 순서, 감지된 컴파일러를 표시합니다 |
| `Reset-Msys2Toolchain` | 기본값으로 UCRT64로 재설정 |

### 내장 도움말 및 진단

- `phelp` (`ph`) — `-d`, `-s`, `-c`, `-a` 옵션으로 대상 뷰가 있는 도움말 메뉴
- `Test-DevTools` — 외부 도구 경로 및 버전 표시 (git, python, pip, uv, gcc, g++, cmake, ninja)
- `Show-ShortcutPaths` — 모든 단축 변수 값을 표시합니다
- `Show-ProfileCommands` — 카테고리로 구성된 모든 프로필 명령어를 나열합니다
- `Show-StartupBanner -Force` — 배너를 다시 그립니다 (변경 후 유용)

### PATH 유틸리티

프로필은 PATH의 세션 기준선을 유지하며 안전하게 수정하기 위한 유틸리티를 제공합니다:

- `Get-PathEntries` — 모든 PATH 항목 나열
- `Remove-PathEntry "path"` — PATH에서 특정 경로 제거
- `Prepend-PathEntry "path"` — 경로를 PATH 맨 앞으로 이동
- `Reset-Path` — 세션 시작 시 PATH로 복원
- `Rebaseline-Path` — 현재 PATH로 저장된 기준선 업데이트

---

## 6. 설정

프로필은 두 가지 설정 파일을 사용합니다:

- `config.template.psd1` — 추적되는 공유 기본값 (직접 편집하지 마세요)
- `config.local.psd1` — 머신별 재정의, Git에서 무시됨

로컬 설정을 생성하세요:

```powershell
Copy-Item .\config.template.psd1 .\config.local.psd1
```

### 설정 키

| 키 | 용도 | 기본값 | 기준 경로 |
|-----|---------|---------|-----------------|
| `OhMyPoshTheme` | Oh My Posh 테마 파일 경로 | `theme/clean-detailed_custom.omp.json` | 프로필 디렉토리 |
| `UvGlobalVenv` | 글로벌 uv venv 위치 | `dev/_global-py/.venv` | `$HOME` |
| `DevFolder` | 개발 폴더 이름 | `dev` | `$HOME` |
| `DesktopFolder` | 데스크탑 폴더 이름 | `Desktop` | 시스템 API (`$HOME` 폴백) |
| `WorkspaceFolder` | 워크스페이스 하위 폴더 | `Desktop\Workspace` | `$HOME` |
| `OpenCodeConfigFolder` | OpenCode 설정 폴더 | `.config\opencode` | `$HOME` |
| `GoogleDrive` | Google Drive 루트 경로 | (설정되지 않음) | `$HOME` (로컬만) |
| `Msys2Root` | MSYS2 설치 경로 | (설정되지 않음) | 절대 경로 (로컬만) |

### `config.local.psd1` 예시

```powershell
@{
    GoogleDrive = "D:\YourDrive"
    Msys2Root   = "C:\msys64"
}
```

실제로 필요한 키만 포함하세요. 키를 생략하면 템플릿의 기본값이 사용됩니다.

---

## 7. 내장 명령어

### 도움말 및 진단

| 명령어 | 별칭 | 설명 |
|---------|---------|-------------|
| `Show-ProfileHelp` | `phelp`, `ph`, `profilehelp` | 옵션이 있는 도움말 메뉴 표시 |
| `Test-DevTools` | — | 외부 도구 경로 및 버전 표시 |
| `Show-ShortcutPaths` | — | 단축 변수 경로 표시 |
| `Show-ProfileCommands` | — | 카테고리별 모든 프로필 명령어 나열 |
| `Show-StartupBanner` | — | 시작 배너 다시 렌더링 |

### PATH 관리

| 명령어 | 별칭 | 설명 |
|---------|---------|-------------|
| `Get-PathEntries` | — | 모든 PATH 항목 나열 (내부 사용) |
| `Remove-PathEntry` | — | 현재 세션 PATH에서 경로 제거 |
| `Prepend-PathEntry` | — | 경로를 PATH 맨 앞으로 이동 |
| `Reset-Path` | — | 세션 시작 시 PATH로 복원 |
| `Rebaseline-Path` | — | 현재 상태로 PATH 기준선 업데이트 |

### Python 및 `uv`

| 명령어 | 별칭 | 설명 |
|---------|---------|-------------|
| `Activate-UvGlobal` | `ga` | 설정된 글로벌 uv venv 활성화 |
| `Deactivate-UvGlobal` | `gde` | 글로벌 uv venv 비활성화 |
| `Get-PythonExecutable` | `pywhere`, `pyw` | 활성 python 실행 파일 경로 표시 |
| `Get-PythonEnvInfo` | `pyinfo`, `pyi` | 상세 Python 환경 정보 표시 |
| `Get-UvPythonExecutable` | `pyuvinfo`, `pyuv`, `uvi` | uv 관리 python 경로 표시 |
| `Get-PythonPath` | `pypath`, `pyp` | PATH에서 python 해결 (빠름, Get-Command 기반) |
| `Get-PipExecutable` | `pipwhere`, `pipw` | 활성 pip 실행 파일 경로 표시 |
| `Get-UvPipExecutable` | `pipuv`, `uvpip` | uv 관리 pip 경로 표시 |
| `Get-PipPath` | `pippath`, `pipp` | PATH에서 pip 해결 (빠름, Get-Command 기반) |

### `uv` 관리 Python 정책

| 명령어 | 별칭 | 설명 |
|---------|---------|-------------|
| `Get-UvManagedPythonPolicy` | `uvpolicy`, `uvp` | 현재 관리 Python 정책 표시 (ALLOWED/BLOCKED) |
| `Enable-UvManagedPythonBlock` | `uvblock`, `uvb` | 이 세션에서 uv 관리 Python 차단 |
| `Disable-UvManagedPythonBlock` | `uvallow`, `uva` | 이 세션에서 uv 관리 Python 허용 |
| `Invoke-UvWithManagedPython` | `uvm` | 관리 Python이 일시적으로 활성화된 상태로 uv 실행 |

### MSYS2 툴체인 (Windows만)

| 명령어 | 별칭 | 설명 |
|---------|---------|-------------|
| `Show-Msys2Toolchain` | — | 상세 MSYS2 툴체인 상태, PATH 순서, 감지된 도구 표시 |
| `Use-UCRT64` | — | UCRT64 툴체인으로 전환 (기본값, 현대적) |
| `Use-MINGW64` | — | MINGW64 툴체인으로 전환 (레거시 프로젝트) |
| `Reset-Msys2Toolchain` | — | 툴체인 기본 설정 UCRT64로 재설정 |

---

## 8. 워크플로 예시

### `uv`를 사용한 Python 워크플로

```powershell
# 작업 전에 글로벌 venv 활성화
ga

# ... Python 작업 수행 ...

# 완료되면 비활성화
gde
```

글로벌 venv는 모든 프로젝트에서 공유됩니다. 격리된 프로젝트 환경에는 `uv venv`로 프로젝트별 venv를 사용하세요.

### MSYS2 툴체인 전환

```powershell
# 최신 C++ 프로젝트용 (권장)
Use-UCRT64

# 레거시 프로젝트용 (MINGW64 필요)
Use-MINGW64

# 현재 상태 확인
Show-Msys2Toolchain

# 기본값으로 재설정 (UCRT64)
Reset-Msys2Toolchain
```

### PATH 문제 해결

```powershell
# 모든 PATH 항목 확인 (번호순)
Get-PathEntries

# 잘못된 항목 제거
Remove-PathEntry "C:\OldPython39"

# 복잡해지면 PATH를 세션 시작 상태로 재설정
Reset-Path

# 현재 상태를 새 기준선으로 저장
Rebaseline-Path
```

### 최초 설정 확인

```powershell
# 한 번에 모든 항목 확인
phelp -a

# 설치된 개발 도구 확인
Test-DevTools

# 단축 변수 경로 확인
Show-ShortcutPaths
```

---

## 9. Oh My Posh 테마

활성 테마의 위치:

```
theme/clean-detailed_custom.omp.json
```

Oh My Posh가 초기화되기 전에, 프로필은 경로 인식 아이콘 매핑을 위해 `POSH_*` 환경변수를 내보냅니다:

- `POSH_DESKTOP`
- `POSH_DOCUMENTS`
- `POSH_DOWNLOADS`
- `POSH_DEV`
- `POSH_WORKSPACE`
- `POSH_OPENCODE`
- `POSH_GOOGLEDRIVE`

테마는 `mapped_locations`에서 이를 사용하여 머신별 경로 하드코딩 없이 폴더 레이아웃에 따라 아이콘이 동적으로 업데이트됩니다.

### 테마 커스터마이징

**옵션 1 — JSON을 직접 편집**:

`theme/clean-detailed_custom.omp.json`을 열고 세그먼트, 색상, 글리프를 수정하세요.

**옵션 2 — 다른 테마로 전환**:

1. 새 `.omp.json` 테마를 다운로드하거나 생성
2. `theme/` 디렉토리에 배치
3. `config.local.psd1`에서 `OhMyPoshTheme` 업데이트:

```powershell
@{ OhMyPoshTheme = "theme/my-custom-theme.omp.json" }
```

이전 테마 스냅샷은 참조를 위해 `theme/archive/`에 보존되어 있습니다.

---

## 10. VS Code 연동

`Microsoft.VSCode_profile.ps1`는 메인 프로필을_dot-source_하는 얇은 래퍼입니다. VS Code에서 터미널을 열면 이 래퍼가 메인 프로필 대신 로드됩니다.

즉, 다음과 같은 의미입니다:
- VS Code 터미널에서 동일한 프롬프트, 단축 변수, 명령어 사용 가능
- 셸 통합이 활성화된 상태로 VS Code 내에서 Oh My Posh 로드
- VS Code 터미널에서 폴더 단축 변수 (`$DEV`, `$WORKSPACE` 등) 사용 가능

### VS Code 설정

워크스페이스 로컬 설정의 참조로 `.vscode/settings.template.json`을 사용하세요. 실제 `.vscode/settings.json`은 Git에서 무시됩니다 (`.gitignore`에 목록에 있음)이므로 개인 설정은 로컬에 유지됩니다.

---

## 11. 문제 해결

### 프롬프트에 `□` 또는 `?` 상자가 표시됨

**원인**: Nerd Font 누락.

**해결 방법**: [nerdfonts.com](https://www.nerdfonts.com/font-downloads)에서 Nerd Font (예: `CascadiaCode Nerd Font`)를 설치하고 터미널 설정에서 선택하세요.

### 시작 시 배너가 표시되지 않음

**원인**: 배너는 기본적으로 세션마다 한 번만 표시됩니다.

**해결 방법**: `Show-StartupBanner -Force`를 실행하여 배너를 다시 그립니다.

### 설치 후 `phelp`를 찾을 수 없음

**원인**: 프로필이 로드되지 않음, 잘못된 `$PROFILE` 경로일 수 있음.

**해결 방법**:

```powershell
# 프로필 경로 확인
$PROFILE

# 파일이 존재하는지 확인
Test-Path $PROFILE

# 없으면 터미널을 닫고 다시 열기
```

### Python이 감지되지 않음

**원인**: Python이 PATH에 없거나, Python이나 `uv`가 설치되지 않음.

**해결 방법**: Python 또는 `uv` 설치:

```powershell
winget install Python.Python.3.12
# 또는
winget install Astral.uv
```

그런 다음 터미널을 다시 시작하세요.

### MSYS2 명령어가 작동하지 않음

**원인**: `config.local.psd1`에서 `Msys2Root`가 설정되지 않음.

**해결 방법**: MSYS2 경로 추가:

```powershell
@{ Msys2Root = "C:\msys64" }
```

그런 다음 터미널을 다시 시작하세요.

### Oh My Posh가 로드되지 않음

**원인**: Oh My Posh가 설치되지 않음.

**해결 방법**:

```powershell
winget install JanDeDobbeleer.OhMyPosh
```

그런 다음 터미널을 다시 시작하세요.

### 실험 후 PATH가 잘못된 느낌

**원인**: 직접적인 PATH 조작으로 중복 항목이나 잘못된 순서가 생길 수 있음.

**해결 방법**:

```powershell
Reset-Path  # PATH를 세션 시작 상태로 복원
```

현재 상태를 기준선으로 저장하려면:

```powershell
Rebaseline-Path
```

---

## 12. 저장소 구조

```
.
├─ Microsoft.PowerShell_profile.ps1   # 메인 프로필 진입점
├─ Microsoft.VSCode_profile.ps1       # VS Code 터미널 래퍼
├─ README.md
├─ config.template.psd1              # 추적되는 공유 기본값
├─ config.local.psd1                  # 로컬 재정의 (gitignore됨)
├─ .gitignore
├─ theme/
│  ├─ clean-detailed_custom.omp.json  # 활성 Oh My Posh 테마
│  └─ archive/                        # 이전 테마 스냅샷
├─ docs/
│  └─ PowerShell_Profile_Analysis.md  # 디자인 노트 및 분석
└─ .vscode/
   ├─ settings.template.json          # VS Code 워크스페이스 템플릿
   └─ settings.json                  # 로컬 VS Code 설정 (gitignore됨)
```

---

## 13. Git / 공개 모델

이 저장소의 설계 의도:

- 공유 프로필 로직은 Git에서 추적
- 로컬 머신 경로는 `config.local.psd1`에 유지
- 로컬 편집기 상태는 `.vscode/settings.json`에 유지
- 테마 기록은 `theme/archive/` 아래에 보존

자체 포크를 공개적으로 게시할 계획이라면, 푸시하기 전에 로컬 재정의 및 Git 기록을 검토하세요.

기존 로컬 git 기록에 개인 경로, 이전 로컬 설정 또는 개인 작성자 메타데이터가 이미 포함되어 있다면, 작업 트리를 정화한 후 새 저장소 기록으로 시작하는 것이 공개 릴리스에 가장 깔끔한 방법입니다.

---

## 14. 참고

- 이 프로필은 Windows 우선이지만, 여러 섹션은 의도적으로 크로스 플랫폼입니다 (PATH 유틸리티, Python 헬퍼, Oh My Posh init).
- `Oh My Posh`, `uv` 또는 MSYS2가 없으면 프로필이 계속 로드됩니다. 관련 명령어는 부분적이거나 정보 제공 전용이 됩니다.
- 로컬 폴더 경로를 변경하면 내보내진 `POSH_*` 변수와 프롬프트 매핑이 함께 업데이트되도록 셸을 다시 시작하세요.
- 수동 테스트의 경우, 자동화 중심 세션 중에 프로필을 반복적으로_dot-source_하는 대신 새 터미널을 여세요.
- 소스 코드 주석은 한국어로 작성되어 있습니다. 이는 한국어 사용 개발자가 프로필 동작을 이해하고 수정할 수 있도록 의도적인 것입니다.
