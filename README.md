# MIIIME Hybrid Launcher (MHL™)
MIIIMELauncher · 미메런처 · ミメランチャー<br>

![OS](https://img.shields.io/badge/Platform-Windows-0078D4?logo=windows&style=flat-square)
[![Language](https://img.shields.io/badge/Language-AutoIt-orange?logo=autoit&style=flat-square)](https://www.autoitscript.com/site/)
![License](https://img.shields.io/badge/License-Freeware-lightgrey?style=flat-square)

<br>
<img width="559" height="136" alt="001" src="https://github.com/miiimeworks/M4T/blob/main/4bit_Enhanced/Id/Neon/4b_136_0_G.png?raw=true" style="margin-top: 20px; margin-bottom: 20px;">
<br><br>

> MIIIMELauncher is not a one-click portable solution. It is a controlled execution environment.  
> 미메런처는 원클릭 포터블 솔루션이 아닙니다. 제어된 실행 환경을 제공합니다.

Instead of hiding system behavior, it exposes it.  
Instead of simplifying portability, it enforces consistency.  
Not recommended unless you have a thorough understanding of file systems and registry structures.
  
시스템 동작을 숨기는 대신 노출합니다.   
이식성을 단순화하는 대신 일관성을 강화합니다.  
파일 시스템 및 레지스트리 구조에 대한 이해가 없는 경우 사용을 권장하지 않습니다.  

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

**[포렌식 복구]**
- **세션 추적** : UUID 및 PID 모니터링을 통한 세션 무결성 검증.
- **롤백** : 트랜잭션 기반의 스냅샷(`*.reg.bakk`)을 이용한 정밀 복구.

### 4. Volatility Control (Freeze Mode)
- **Read-Only** : Forces volatile state; no write-back to storage.  
- **Auto-Redirection** : Relocates execution context to Host Temp on RO media.  
  Detection is based on drive type (`DriveGetType`) combined with an actual filesystem write test,  
  covering CD / ISO / Write-Protected USB and any other non-writable path.

**[휘발성 제어 (동결 모드)]**
- **읽기 전용** : 휘발성 상태 강제, 스토리지 쓰기 방지.  
- **자동 우회** : RO 미디어 감지 시 호스트 Temp로 실행 컨텍스트 자동 재배치.  
  드라이브 타입(`DriveGetType`) 확인 및 실제 파일시스템 쓰기 테스트를 통해 감지.  
  CD/ISO/쓰기 금지 USB 외 비쓰기 가능 경로도 포함.
  
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
	 ├─ TargetApp_M.exe             # Launcher Executable / 런쳐 실행 파일
	 ├─ TargetApp_M.ini             # Configuration File / 설정 파일
	 ├─ TargetApp_M.log             # Runtime Log / 실행 로그 (활성 시 생성)
	 │
	 ├─ App/                        # Core Files & Templates / 핵심 파일 및 템플릿
	 │   │	 
	 │   ├─ TargetApp/              # Target Application / 원본 타겟 앱 바이너리
	 │   │
	 │   ├─ Ext/                    # Extra / 추가 파일
	 │   │   ├─ Ast/                # Assets File Injection / 1회 주입용 어셋 파일 *
	 │   │   ├─ Sys/                # System File Injection / 호스트 주입용 파일
	 │   │   ├─ Org/        		# Factory Reset (Stub) / 초기화 설정 (기능 없음)
	 │   │   └─ Res/                # UI Resources / 폴더아이콘, 스플래시 이미지
	 │   │
	 │   └─ RawDat/                 # Default Data Template / 데이터 초기 템플릿
	 │       ├─ AppDat/             # System AppData Sandbox / 시스템 앱 데이터 샌드박스
	 │       │   ├─ Local/          # %LocalAppData% 대체
	 │       │   ├─ LocalLow/       # %LocalLow% 대체
	 │       │   └─ Roaming/        # %AppData% 대체
	 │       ├─ Reg/                # Registry Templates / 주입용 .reg 템플릿
	 │       ├─ Set/                # Portable Settings / 앱 폴더 병합용 설정 파일 
	 │       └─ Usr/                # User Profile / 사용자 프로파일 *	
	 │
	 └─ Dat/                        # Active User Data / 사용자 저장소 (수정 사항 저장)

			* Usr & Sys folders are for ADVANCED USERS ONLY.
			* Usr, Sys 폴더는 고급 사용자 전용입니다.
```

**[디렉토리 구조]**

* **런쳐 실행 파일** : 런처 본체 및 동작 환경 정의를 위한 설정 파일.
* **App 영역** : 원본 앱 바이너리 및 사용자 데이터 초기 상태를 위한 템플릿.
* **Dat 영역** : 사용자가 수정한 사항이 저장되는 실제 데이터 저장소.

### 3. Technical Specification (`ini`)

#### **[INI Parameters] (`TargetApp_M.ini`)**

Key configuration values for the launcher behavior.  
런처 동작을 제어하는 주요 설정 값. 

| Parameter | Section | Type | Description |
| --- | --- | --- | --- |
| `RunAsAdmin` | Launch | Bool | 1=Force Administrator privileges, 0=User mode |
| `UseJunction` | Options | Bool | **1=Symbolic Link (Recommended)**, 0=Physical Copy mode |
| `FreezeMode` | Options | Bool | 1=Non-persistent (Volatile / Read-Only), 0=Persistent. **Requires `UseJunction=0`.** Mutually exclusive with Junction mode. |
| `LogLevel` | Options | Int | 0=Off, 1=All, 2=Debug, 3=Info, 4=Warn, 5=Error |
| `ProcessCheckInterval` | Advanced | Int | Polling interval (ms) for child process monitoring |

#### **[Environment] & Macros**

**Macro System** : Supports a macro system for path flexibility.  
**매크로 시스템** : 경로 유연성을 위해 매크로 시스템을 지원.


* **Launcher** : `{Base}`, `{Run}`, `{Dat}`, `{Ext}`, `{Ast}`, `{Sys}`, `{Res}`, `{Exe}`, `{AppName}`, `{CommonFiles}`, `{_MIIIMEEnv}`
* **System** : `{Windows}`, `{System32}`, `{SysWOW64}`, `{ProgramFiles}`, `{ProgramData}`, `{UserProfile}`, `{Docs}`, `{Desktop}`, `{StartMenu}`, `{Programs}`, `{CommonStartMenu}`, `{CommonPrograms}`, `{Temp}`
* **AppData** : `{Local}`, `{LocalLow}`, `{Roaming}`

---

## CLI Arguments

Supported command-line arguments for debugging and maintenance.  
디버깅 및 유지보수를 위해 지원되는 명령줄 인수.

```bash
TargetApp_M.exe [Options]
```
| Argument | Description |
| --- | --- |
| `--clean` | **Force Cleanup**: Deletes the entire `Dat/` directory (including all user data) and resets the environment. **Irreversible.** |
| `--debug` | **Debug Mode**: Forces `LogLevel=2` (DEBUG) regardless of INI settings |

---

## Companion Tools

Two standalone companion utilities are provided alongside MHL.  
미메런처의 운영을 보조하는 두 가지 독립 도구를 제공함.  

> ### MIIIME Tools Manager (MTM)
A live monitoring and management tool for launcher processes and their host-side traces.  
런처 프로세스와 호스트 흔적을 실시간 모니터링하고 정리하는 운영 도구.  
**Repository** : [MIIIMEToolsManager](https://github.com/miiime6248)

> ### MIIIME Launcher Sweeper (MLS)
A forensic cleanup utility that detects and removes filesystem artifacts left by abnormal launcher termination.  
런처의 비정상 종료 후 호스트에 잔류한 파일시스템 아티팩트를 탐지 · 제거하는 포렌식 정리 도구.  
**Repository** : [MIIIMELauncherSweeper](https://github.com/miiime6248)

---

## 🛡️ Security & Anti-virus Info

### [✅ VirusTotal Analysis Report](https://www.virustotal.com/gui/file/fd04ad5a053c5f0f9f81446991b2883598f46272c7e122627364ec447501320c?nocache=1)
| Status | Details |
| :--- | :--- |
| **Major Vendors** | **Clean** (Passed by AhnLab V3, Kaspersky, Microsoft, Avast, ESET, etc.) |
| **Detection Rate** | **11 / 72** (Mostly Heuristic/Generic/Trojan-type flags) |
| **Integrity** | The source code is transparently available for verification in this repository |

> This launcher was created with AutoIt. Some antivirus programs may incorrectly detect it as a virus.  
> 본 런처는 AutoIt으로 제작되었습니다. 일부 백신이 바이러스로 오진 할 수 있습니다. 

**File Checksum (SHA-256) :** `fd04ad5a053c5f0f9f81446991b2883598f46272c7e122627364ec447501320c`

---

## Disclaimer

Provided **"AS IS"**, without warranty.  
This is a **private project**. No technical support is provided.

본 프로그램은 **"있는 그대로"** 제공되며, 사용 중 발생하는 문제에 대해 제작자는 책임을 지지 않습니다.  
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
