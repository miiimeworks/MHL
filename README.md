# MIIIME Hybrid Launcher (MHL™)

MIIIMELauncher · 미메런처 · ミメランチャー<br>

![OS](https://img.shields.io/badge/Platform-Windows-0078D4?logo=windows&style=flat-square)
[![Language](https://img.shields.io/badge/Language-AutoIt-orange?logo=autoit&style=flat-square)](https://www.autoitscript.com/site/)
![License](https://img.shields.io/badge/License-Freeware-lightgrey?style=flat-square)

<br>
<img width="559" height="136" alt="001" src="https://github.com/miiimeworks/M4T/blob/main/4bit_Enhanced/Id/Neon/4b_136_1_G.png?raw=true" style="margin-top: 20px; margin-bottom: 20px;">
<br>

MIIIMELauncher is not a one-click portable solution. It is a controlled execution environment.  
Not recommended unless you have a thorough understanding of file systems and registry structures.

미메런처는 원클릭 포터블 솔루션이 아닙니다. 제어된 실행 환경을 제공합니다.  
파일 시스템 및 레지스트리 구조에 대한 이해가 없는 경우 사용을 권장하지 않습니다.  
<br>

---

## Technical Stack

* **Core Engine** : AutoIt3, WinAPI (Kernel32, User32, Advapi32), GDI+
* **Process Management** : WMI (Windows Management Instrumentation) query-based monitoring.
* **FileSystem** : NTFS Junction Points (Reparse Points) & Physical Fallback.
* **Registry** : Native Hive Injection/Retrieval via Regedit binaries.

---

## Mechanism

### 1. Filesystem Virtualization

- **NTFS** : Junction Point (Symbolic Link) redirection.  
- **Non-NTFS** : Automatic fallback to Physical Swap mode.  
- **Robustness** : Smart retry logic for locked files during transition.

**[파일 시스템 가상화]**

- **NTFS** : Junction Point (심볼릭 링크) 리다이렉션 사용.  
- **Non-NTFS** : 물리적 Swap 모드로 자동 전환.
- **안전성** : 파일 잠김 발생 시 스마트 재시도 로직을 통해 데이터 무결성 보장.

### 2. Registry Management

- **Injection** : Seamless integration of HKCU keys.
- **Integrity** : Hashing (MD5) applied to long key paths during backup.
- **Cleanup** : Root key pruning and verified retrieval.

**[레지스트리 관리]**

- **주입** : HKCU 키의 무결절 통합.
- **무결성** : 백업 시 긴 키 경로에 대한 해싱(MD5) 적용.
- **정리** : 루트 키 가지치기 및 검증된 회수 수행.

### 3. Forensic Recovery

- **Session Tracking** : Validates session integrity via UUID and PID monitoring.
- **Rollback** : Transaction-based recovery using `*.reg.bakk` snapshots.
- **AppData Recovery** : On next launch, detects `*_bakk` folders left by abnormal termination, removes orphaned Junction Points, and restores the original AppData directories automatically. Controlled by `CrashRecovery=` in `[Launch]` (`1`=enabled by default, `0`=disabled).

**[포렌식 복구]**

- **세션 추적** : UUID 및 PID 모니터링을 통한 세션 무결성 검증.
- **롤백** : 트랜잭션 기반의 스냅샷(`*.reg.bakk`)을 이용한 정밀 복구.
- **AppData 복구** : 다음 실행 시 비정상 종료로 잔류한 `*_bakk` 폴더를 자동 탐지하여 고아 Junction을 제거하고 원본 AppData를 복원. `[Launch]` 섹션의 `CrashRecovery=`로 제어 (`1`=활성(기본값), `0`=비활성).

---

## Configuration

### 1. Quick Setup

- **Naming Convention** : The filenames `TargetApp_M.exe` and `TargetApp_M.ini` must match the name of the target executable file.  
  The suffix `_M` must be included at the end of the filename for management and identification purposes.
- **Binary Placement** : Place the target application folder inside the `App/` directory.

**[빠른 설정]**

- **네이밍 규칙** : `TargetApp_M.exe` 및 `TargetApp_M.ini` 파일명은 타겟 실행 파일과 일치해야 하며,  
  관리 및 식별을 위해 파일명 끝에 반드시 `_M` 접미사를 포함 해야 함.  
- **바이너리 배치** : 타겟 애플리케이션 폴더를 `App/` 디렉토리 내부에 배치.  

### 2. Directory Structure

```text
TargetApp_M/
     │
     ├─ TargetApp_M.exe             # Launcher Executable / 런처 실행 파일
     ├─ TargetApp_M.ini             # Configuration File / 설정 파일
     ├─ TargetApp_M.log             # Runtime Log / 실행 로그 (활성 시 생성)
     │
     ├─ App/                        # Core Files & Templates / 핵심 파일 및 템플릿
     │   │     
     │   ├─ TargetApp/              # Target Application / 원본 타겟 앱 바이너리
     │   │
     │   ├─ Ext/                    # Extra / 추가 파일
     │   │   ├─ Ast/                # Assets File Injection / 1회 주입용 어셋 파일
     │   │   ├─ Env/                # Environment File / 환경설정용 파일	
     │   │   ├─ Org/                # Factory Reset (Stub) / 초기화 설정 (기능 없음)
     │   │   └─ Res/                # UI Resources / 폴더아이콘, 스플래시 이미지
     │   │
     │   └─ RawDat/                 # Default Data Template / 데이터 초기 템플릿
     │       ├─ AppDat/             # System AppData Sandbox / 시스템 앱 데이터 샌드박스
     │       │   ├─ Local/          # %LocalAppData% 대체
     │       │   ├─ LocalLow/       # %LocalLow% 대체
     │       │   └─ Roaming/        # %AppData% 대체
     │       ├─ Reg/                # Registry Templates / 주입용 .reg 템플릿
     │       ├─ Set/                # Portable Settings / 앱 폴더 병합용 설정 파일 
     │       └─ Usr/                # User File / 사용자 파일 *    
     │
     └─ Dat/                        # Active User Data / 사용자 저장소 (수정 사항 저장)

            * Usr folder is for ADVANCED USERS ONLY.
            * Usr 폴더는 고급 사용자 전용입니다.
```

**[디렉토리 구조]**

* **런처 실행 파일** : 런처 본체 및 동작 환경 정의를 위한 설정 파일.
* **App 영역** : 원본 앱 바이너리 및 사용자 데이터 초기 상태를 위한 템플릿.
* **Dat 영역** : 사용자가 수정한 사항이 저장되는 실제 데이터 저장소.

### 3. Technical Specification (`ini`)

#### **[INI Parameters] (`TargetApp_M.ini`)**

Key configuration values for the launcher behavior.  
런처 동작을 제어하는 주요 설정 값. 

| Parameter              | Section  | Type | Description                                                                                                                 |
| ---------------------- | -------- | ---- | --------------------------------------------------------------------------------------------------------------------------- |
| `RunAsAdmin`           | Launch   | Bool | 1=Force Administrator privileges, 0=User mode                                                                               |
| `UseJunction`          | Options  | Bool | **1=Symbolic Link (Recommended)**, 0=Physical Copy mode                                                                     |
| `FreezeMode`           | Options  | Bool | 1=Non-persistent (Volatile / Read-Only), 0=Persistent. **Requires `UseJunction=0`.** Mutually exclusive with Junction mode. |
| `LogLevel`             | Options  | Int  | 0=Off (default), 1=INFO/WARN/ERROR, 2=All including DEBUG, 3=INFO+, 4=WARN+, 5=ERROR only                                   |
| `ProcessCheckInterval` | Advanced | Int  | Polling interval (ms) for child process monitoring                                                                          |

#### **[Registry] Sections**

| Section           | Description                                                                                                                                                                                          |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[Registry]`      | Registry keys to back up on launch, inject from `Dat\Reg\KeyNN.reg`, retrieve on exit, and delete from host. If `Dat\Reg\` does not exist, backup and delete only (no injection — clean state mode). |
| `[RegistryRoot]`  | Parent keys to prune if empty after `[Registry]` cleanup.                                                                                                                                            |
| `[RegistryShell]` | Shell context menu entries to register on launch and remove on exit. Supports `SmartSkip`.                                                                                                           |
| `[RegistryFix]`   | Key\|Value\|Data entries to force-write on every launch (path patching). Supports `SmartSkip`.                                                                                                       |


#### **[Filesystem] Sections**

| Section            | Description                                                                                                                                          |
| ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[CleanupExclude]` | Files/folders to exclude from the Dat retrieval sweep (preserve on host). Supports `SmartSkip`.                                                      |
| `[CleanupDelete]`  | Files/folders inside `Dat\` to delete before retrieval (discard volatile data). Supports `SmartSkip`.                                                |
| `[Assets]`         | Files to inject into `{Run}` once (or always if `AlwaysUpdate=1`).                                                                                   |
 `[UserFile]`   | Files to copy into host directories on launch and restore on exit. **Advanced users only.** Requires `RunAsAdmin=1`. |

#### **SmartSkip**

스마트스킵 컨트롤은 다음 섹션의 실행 여부를 지정.  
`SmartSkip` control specifies whether the next section execute.

`[RegistryFix]`, `[RegistryShell]`, `[CleanupExclude]`, `[CleanupDelete]`, `[Resources]`

| Value | Behavior                                                                          |
| ----- | --------------------------------------------------------------------------------- |
| `0`   | Always execute                                                                    |
| `1`   | Execute only when environment change is detected (drive path or hostname changed) |
| `2`   | Always skip — disabled (default for Cleanup sections)                             |

#### **[Environment] & Macros**

Supports a macro system for path flexibility.  
경로 유연성을 위해 매크로 시스템을 지원.

* **Launcher** : `{Base}`, `{Run}`, `{Dat}`, `{Ast}`, `{Env}`, `{Res}`, `{Ext}`, `{Usr}`, `{Exe}`, `{AppName}`, `{CommonFiles}`, `{_MIIIMEEnv}`
* **System** : `{Windows}`, `{System32}`, `{SysWOW64}`, `{ProgramFiles}`, `{ProgramData}`, `{UserProfile}`, `{Docs}`, `{Desktop}`, `{StartMenu}`, `{Programs}`, `{CommonStartMenu}`, `{CommonPrograms}`, `{Temp}`
* **AppData** : `{Local}`, `{LocalLow}`, `{Roaming}`

---

## CLI Arguments

Supported command-line arguments for debugging and maintenance.  
디버깅 및 유지보수를 위해 지원되는 명령줄 인수.

```bash
TargetApp_M.exe [Options]
```

| Argument  | Description                                                                                                                    |
| --------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `--clean` | **Force Cleanup**: Deletes the entire `Dat/` directory (including all user data) and resets the environment. **Irreversible.** |
| `--debug` | **Debug Mode**: Forces `LogLevel=2` (DEBUG) regardless of INI settings                                                         |

---

## Companion Tools

Standalone companion utilities are provided alongside MHL.  
미메런처의 운영을 보조하는 독립 도구를 제공함.  

> ### MIIIME Launcher Sweeper (MLS)
> 
> A forensic cleanup utility that detects and removes filesystem artifacts left by abnormal launcher termination.  
> 런처의 비정상 종료 후 호스트에 잔류한 파일시스템 아티팩트를 탐지 · 제거하는 포렌식 정리 도구.  

- **Repository** : [MIIIMELauncherSweeper](https://github.com/miiimeworks/MLS)

> ### MIIIME Tools Manager (MTM)
> 
> A live monitoring and management tool for launcher processes and their host-side traces.  
> 런처 프로세스와 호스트 흔적을 실시간 모니터링하고 정리하는 운영 도구.  

- **Repository** : [MIIIMEToolsManager](https://github.com/miiimeworks/MTM)

> ### MIIIME Universal Cleaner (MUC)
> 
> A tool that cleans up unnecessary files and folders according to rules defined in an INI configuration file.  
> INI 설정 파일에 정의된 규칙에 따라 불필요한 파일과 폴더를 정리하는 도구.  

- **Repository** : [MIIIMEUniversalCleaner](https://github.com/miiimeworks/MUC)

---

## 🛡️ Security & Anti-virus Info

### [✅ VirusTotal Analysis Report](https://www.virustotal.com/gui/file/0c8c91d69e9870de34e3f7a8c4dc7fbb16d79a63b639755cf37e69d713fa2407?nocache=1)

| Status             | Details                                                                        |
|:------------------ |:------------------------------------------------------------------------------ |
| **Major Vendors**  | **Clean** (Passed by AhnLab V3, Kaspersky, Microsoft, Avast, ESET, etc.)       |
| **Detection Rate** | **9 / 72** (Mostly Heuristic/Generic/Trojan-type flags)                        |
| **Integrity**      | The source code is transparently available for verification in this repository |

> This Program was created with AutoIt. Some antivirus programs may incorrectly detect it as a virus.  
> 본 프로그램은 AutoIt으로 제작되었습니다. 일부 백신이 바이러스로 오진 할 수 있습니다. 

**File Checksum (SHA-256) :** `0c8c91d69e9870de34e3f7a8c4dc7fbb16d79a63b639755cf37e69d713fa2407`

---

## Disclaimer

Provided **“AS IS”**, without warranty.  
This is a **private project**. No technical support is provided.

본 프로그램은 **“있는 그대로”** 제공되며, 사용 중 발생하는 문제에 대해 제작자는 책임을 지지 않습니다.  
기술 지원은 제공되지 않습니다.

---

## Project Information

**Developer** : MIIIME  
**Website** : https://www.miiime.com  
**GitHub** : [@miiime6248](https://github.com/miiime6248)  
**Last Update** : 2026-02-14  

<br>
<img width="64" height="64" alt="002" src="https://github.com/miiimeworks/M4T/blob/main/4bit_Brutal/Logo/Neon/4b_Mium_64_0_G.png?raw=true">  
<br>
<br>
<br>
