# MIIIME Hybrid Launcher (MHL™)

MIIIMELauncher · 미메런처 · ミメランチャー<br>

![OS](https://img.shields.io/badge/Platform-Windows-0078D4?logo=windows&style=flat-square)
[![Language](https://img.shields.io/badge/Language-AutoIt-orange?logo=autoit&style=flat-square)](https://www.autoitscript.com/site/)
![License](https://img.shields.io/badge/License-Freeware-lightgrey?style=flat-square)

<br>
<img width="559" height="136" alt="001" src="https://github.com/miiimeworks/M4T/blob/main/4bit_Enhanced/Id/Neon/4b_136_1_G.png?raw=true" style="margin-top: 20px; margin-bottom: 20px;">
<br>

This is Portable Launcher.  
Not recommended unless you have a thorough understanding of file systems and registry structures.

포터블런쳐입니다. 
파일 시스템 및 레지스트리 구조에 대한 이해가 없는 경우 사용을 권장하지 않습니다.  

<br>
<img width="402" height="197" alt="001" src="https://github.com/miiimeworks/MHL/blob/main/Preview/001.png?raw=true" style="margin-top: 20px; margin-bottom: 20px;">
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

- **Injection** : Seamless integration of HKCU / HKLM keys.
- **Integrity** : Hashing (MD5) applied to long key paths during backup.
- **Cleanup** : Root key pruning and verified retrieval.
- **Trace** : Optional deletion of execution traces (MUICache, UserAssist) on exit.

**[레지스트리 관리]**

- **주입** : HKCU / HKLM 키의 무결절 통합.
- **무결성** : 백업 시 긴 키 경로에 대한 해싱(MD5) 적용.
- **정리** : 루트 키 가지치기 및 검증된 회수 수행.
- **흔적** : 종료 시 실행 흔적(MUICache, UserAssist) 선택적 삭제.

### 3. Forensic Recovery

- **Session Tracking** : Validates session integrity via UUID and PID monitoring.
- **Rollback** : Transaction-based recovery using snapshots.
- **AppData Recovery** : On next launch, detects folders left by abnormal termination, removes orphaned Junction Points, and restores the original AppData directories automatically. 

**[포렌식 복구]**

- **세션 추적** : UUID 및 PID 모니터링을 통한 세션 무결성 검증.
- **롤백** : 트랜잭션 기반의 스냅샷을 이용한 복구.
- **AppData 복구** : 다음 실행 시 비정상 종료로 잔류한 폴더를 자동 탐지, 고아 Junction을 제거하고 원본 AppData를 복원. 

---

## Configuration

### 1. Quick Setup

- **Naming Convention** : The launcher executable and INI file must have the same name (e.g., `TargetApp_M.exe` and `TargetApp_M.ini`). For management and identification, filenames must include the `_M` suffix.

**[빠른 설정]**

- **네이밍 규칙** : 런처 실행 파일과 INI 파일은 같은 이름이어야 함 (예: `TargetApp_M.exe` 및 `TargetApp_M.ini`). 
  관리 및 식별을 위해 파일명에 `_M` 접미사를 반드시 포함해야 함.  

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
     │   │   ├─ Ast/                # Assets File Injection / 주입용 어셋 파일
     │   │   ├─ Env/                # Environment File / 환경설정용 파일    
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
            * Usr 폴더는 고급 사용자 전용.
```

**[디렉토리 구조]**

* **런처 실행 파일** : 런처 본체 및 동작 환경 정의를 위한 설정 파일.
* **App 영역** : 원본 앱 바이너리 및 사용자 데이터 초기 상태를 위한 템플릿.
* **Dat 영역** : 사용자가 수정한 사항이 저장되는 실제 데이터 저장소.

### 3. Technical Specification (`ini`)

#### **[INI Parameters] (`TargetApp_M.ini`)**

Key configuration values for the launcher behavior.  
런처 동작을 제어하는 주요 설정 값. 

| Parameter                | Section  | Type | Description                                                                                                                    |
| ------------------------ | -------- | ---- | ------------------------------------------------------------------------------------------------------------------------------ |
| `RunAsAdmin`             | Launch   | Bool | 1=Force Administrator privileges, 0=User mode                                                                                  |
| `UseJunction`            | Options  | Bool | **1=Symbolic Link (Recommended)**, 0=Physical Copy mode                                                                        |
| `FreezeMode`             | Options  | Bool | 1=Non-persistent (Volatile / Read-Only), 0=Persistent. **Requires `UseJunction=0`.** Mutually exclusive with Junction mode.    |
| `LogLevel`               | Options  | Int  | 0=Off (default), 1=All including DEBUG, 2=INFO and above, 3=WARN and above, 4=ERROR only. Values outside 0–4 are treated as 0. |
| `ProcessCheckInterval`   | Advanced | Int  | Polling interval (ms) for child process monitoring (default: `200`)                                                            |
| `ExitThreshold`          | Advanced | Int  | Consecutive missed polls before process exit is declared (default: `2`)                                                        |
| `CrashRecovery`          | Launch   | Bool | 1=Auto-recover orphaned `_bakk` folders on next launch (default), 0=Disabled                                                   |
| `AllowMultipleInstances` | Launch   | Bool | 1=Allow multiple concurrent instances, 0=Block (default)                                                                       |
| `DetectReadOnly`         | Options  | Bool | 1=Check for read-only media and exit with a message if detected (default), 0=Skip the check |
| `AddCommand`             | Options  | Str  | Additional command-line arguments appended to the target executable on launch                                                  |
| `WorkDir`                | Launch   | Str  | Working directory for the target executable (default: same directory as `{Run}`)                                               |

#### **[Path Override] Sections**

| Section         | Description                                                                                                                                                                                 |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[Directories]` | Override default directory layout. Keys: `RunPath` (default: `App\TargetApp`), `RawDataPath` (`App\RawDat`), `ExtPath` (`App\Ext`), `DataPath` (`Dat`). All paths relative to the launcher. |
| `[Environment]` | Environment variables and shared paths. Keys `CommonFilesDir` (default: `..\CommonFiles`) and `_MIIIMEEnvDir` (default: `..\_MIIIMEEnv`) override the `{CommonFiles}` / `{_MIIIMEEnv}` macro paths. Any other `NAME=value` sets an environment variable for the target; `PATH01`–`PATH99` extend `PATH`. Macros can be used in values. |

#### **[Process] Sections**

| Section          | Description                                                                                                                                    |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `[ProcessWatch]` | Additional processes to monitor alongside the main executable (for multi-process apps). Cleanup begins when all watched processes have exited. |
| `[ProcessKill]`  | Processes to forcefully terminate before cleanup. Restricted to executables within `{Run}`.                                                    |

#### **[Registry] Sections**

| Section           | Description                                                                                                                                                                                                                               |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[Registry]`      | Registry keys (HKCU / HKLM `\SOFTWARE`) to back up on launch, inject from `Dat\Reg\<KeyName>.reg` (named after the INI key, e.g. `01=` → `01.reg`), retrieve on exit, and delete from host. If `Dat\Reg\` does not exist, backup and delete only (no injection — clean state mode). Supports `SmartSkip` (default: `0`). |
| `[RegistryRoot]`  | Parent keys to prune if empty after `[Registry]` cleanup. Supports `SmartSkip` (default: `0`).                                                                                                                                            |
| `[RegistryShell]` | Shell context menu entries to register on launch and remove on exit. Supports `SmartSkip` (default: `0`).                                                                                                                                 |
| `[RegistryFix]`   | Key\|Value\|Data entries to force-write on every launch (path patching). Supports `SmartSkip` (default: `1`).                                                                                                                             |

#### **[Filesystem] Sections**

| Section            | Description                                                                                                                                                                             |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[FileWrite]`      | In-place file patching on launch: string replace/append, or key-value line update (Type A/B/C). Wildcard path supported. Supports `SmartSkip` (default: `1`).                           |
| `[CleanupExclude]` | Keep only the listed items in a folder and delete the rest (wildcards supported). Targets local absolute paths allowed by the built-in safety guard; runs on exit. Supports `SmartSkip` (default: `2`). |
| `[CleanupDelete]`  | Files/folders to delete on exit (wildcards supported). Targets local absolute paths allowed by the built-in safety guard. Supports `SmartSkip` (default: `2`). |
| `[Assets]`         | Files to inject into `{Run}` or `{Dat}`. Supports `SmartSkip` (default: `1` = on environment change). `SmartSkip=0` forces overwrite on every launch; `SmartSkip=2` disables injection. |
| `[UserFile]`       | Files to copy into host directories on launch and restore on exit. **Advanced users only.** Admin privileges may be required depending on target paths.                                 |

#### **[Post-App] Sections**

| Section           | Description                                                                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `[ShortcutGuard]` | Protects same-named shortcuts/folders (`_bakk` backup before launch, restore after exit) and deletes ones the app re-creates after exit. Parameters: `Name` (list of folder or `.lnk` names, `\|Name1\|Name2\|` format), `Wait` (post-exit watch in sec, default `10`), `Interval` (poll interval in ms, default `500`). |
| `[TraceClean]`    | Deletes execution traces (MUICache, UserAssist) of the target exe and/or launcher on exit. Parameters: `SmartSkip` (`0`/`1`=enable, `2`=disable, default: `2`), `Name` (`\|{Exe}\|`, `\|{Launcher}\|` or `\|{Exe}\|{Launcher}\|`), and timing options `TraceCleanWait` / `TraceCleanIdleWait` / `TraceCleanPollMs` / `TraceCleanStepDelay`. |
| `[Resources]`     | Applies folder icon and splash image from `Ext\Res\`. Supports `SmartSkip` (default: `1`).                                                                          |

#### **[SmartSkip] Control**
 
`SmartSkip` control specifies whether the section executes.  
스마트스킵 컨트롤은 다음 섹션의 실행 여부를 지정. 

`[Registry]`, `[RegistryRoot]`, `[RegistryShell]`, `[RegistryFix]`, `[FileWrite]`, `[CleanupExclude]`, `[CleanupDelete]`, `[Assets]`, `[UserFile]`, `[Resources]`

| Value | Behavior                                                                          |
| ----- | --------------------------------------------------------------------------------- |
| `0`   | Always execute                                                                    |
| `1`   | Execute only when environment change is detected (drive path, hostname, or user name changed) |
| `2`   | Always skip — disabled                                                            |

#### **[Environment] & Macros**

Supports a macro system for path flexibility.  
경로 유연성을 위해 매크로 시스템을 지원.

* **Launcher** : `{Base}`, `{Run}`, `{Raw}`, `{Dat}`, `{Ast}`, `{Env}`, `{Res}`, `{Ext}`, `{Usr}`, `{Exe}`, `{Launcher}`, `{ExeROT13}`, `{LauncherROT13}`, `{AppName}`, `{CommonFiles}`, `{_MIIIMEEnv}`
* **System** : `{Windows}`, `{System32}`, `{SysWOW64}`, `{ProgramFiles}`, `{ProgramFiles(x86)}`, `{ProgramData}`, `{UserProfile}`, `{Docs}`, `{Desktop}`, `{StartMenu}`, `{Programs}`, `{CommonStartMenu}`, `{CommonPrograms}`, `{Temp}`
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
| `--clean` | **Force Cleanup**: Deletes the entire `Dat/` directory (including all user data) and resets the environment. **Irreversible.** Ignored in `FreezeMode`. |
| `--debug` | **Debug Mode**: Forces `LogLevel=1` (all logs including DEBUG) regardless of INI settings.                                     |

Any other argument is passed through to the target application.  
그 밖의 인수는 타겟 앱에 그대로 전달됨.

---

## Companion Tools

Standalone companion utilities are provided alongside MHL.  
미메런처의 운영을 보조하는 독립 도구를 제공함.  

> ### MIIIME Icon Changer (MIC)
> 
> Change the icon of the launcher executable file after portable wrapping.  
> 포터블 래핑 후 런쳐실행파일의 아이콘을 변경.

- **Repository** : [MIIIMEIconChanger](https://github.com/miiimeworks/MIC)

> ### MIIIME Universal Cleaner (MUC)
> 
> A tool that cleans up unnecessary files and folders according to rules defined in an INI configuration file.  
> INI 설정 파일에 정의된 규칙에 따라 불필요한 파일과 폴더를 청소.  

- **Repository** : [MIIIMEUniversalCleaner](https://github.com/miiimeworks/MUC)

> ### MIIIME Launcher Sweeper (MLS)
> 
> A forensic cleanup utility that detects and removes filesystem artifacts left by abnormal launcher termination.  
> 런처의 비정상 종료 후 호스트에 잔류한 파일시스템 아티팩트를 포렌식 탐지 · 제거.  

- **Repository** : [MIIIMELauncherSweeper](https://github.com/miiimeworks/MLS)

> ### MIIIME Tools Manager (MTM)
> 
> A live monitoring and management tool for launcher processes and their host-side traces.  
> 런처 프로세스와 호스트 흔적을 실시간 모니터링하고 정리.  

- **Repository** : [MIIIMEToolsManager](https://github.com/miiimeworks/MTM)

---

## 🛡️ Security & Anti-virus Info

### [✅ VirusTotal Analysis Report](https://www.virustotal.com/gui/file/42ca020bfce3714cf06b023e4257327229256c7a10d8f2b10774d1aff53ba85d?nocache=1)

| Status             | Details                                                                        |
|:------------------ |:------------------------------------------------------------------------------ |
| **Major Vendors**  | **Clean** (Passed by AhnLab V3, Kaspersky, Microsoft, Avast, ESET, etc.)       |
| **Detection Rate** | **3 / 57** (Mostly Heuristic/Generic/Trojan-type flags)                        |
| **Integrity**      | Private project; verified via hashes and VT results. 						  |

> This Program was created with AutoIt. Some antivirus programs may incorrectly detect it as a virus.  
> 본 프로그램은 AutoIt으로 제작되었습니다. 일부 백신이 바이러스로 오진 할 수 있습니다. 

**File Checksum (SHA-256) :** `42ca020bfce3714cf06b023e4257327229256c7a10d8f2b10774d1aff53ba85d`

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
**GitHub** : [@miiimeworks](https://github.com/miiimeworks)  
**Last Update** : 2026-10-24   

<br>
<img width="64" height="64" alt="002" src="https://github.com/miiimeworks/M4T/blob/main/4bit_Brutal/Logo/Neon/4b_Mium_64_0_G.png?raw=true">  
<br>
<br>
<br>