========================================================================
		MIIIME Hybrid Launcher (MHL™)
========================================================================

This is Portable Launcher.
Not recommended unless you have a thorough understanding of file systems and registry structures.

포터블런쳐입니다.
파일 시스템 및 레지스트리 구조에 대한 이해가 없는 경우 사용을 권장하지 않습니다.

========================================================================
[Mechanism]
========================================================================

1. Filesystem Virtualization
   - NTFS : Junction Point (Symbolic Link) redirection.
   - Non-NTFS : Automatic fallback to Physical Swap mode.
   - Robustness : Smart retry logic for locked files during transition.
   
   [파일 시스템 가상화]
   - NTFS : Junction Point (심볼릭 링크) 리다이렉션 사용.
   - Non-NTFS : 물리적 Swap 모드로 자동 전환.
   - 안전성 : 파일 잠김 발생 시 스마트 재시도 로직을 통해 데이터 무결성 보장.

2. Registry Management
   - Injection : Seamless integration of HKCU / HKLM keys (HKLM requires administrator rights).
   - Integrity : Hashing (MD5) applied to long key paths during backup.
   - Cleanup : Root key pruning and verified retrieval.
   - Trace : Optional deletion of execution traces (MUICache, UserAssist) on exit (enable with SmartSkip=0 in [TraceClean]; targets: {Exe} / {Launcher}).
   - Shared Area : Optional deletion, on exit, of values with matching names in registry areas shared by several programs (e.g. OpenWithProgids).
     Host-owned values are protected (enable with SmartSkip=0 in [SharedClean]).
   - Conflict Check : If another launcher is using the same registry key, the launch is stopped with a warning.

   [레지스트리 관리]
   - 주입 : HKCU / HKLM 키의 무결절 통합 (HKLM은 관리자 권한 필요).
   - 무결성 : 백업 시 긴 키 경로에 대한 해싱(MD5) 적용.
   - 정리 : 루트 키 가지치기 및 검증된 회수 수행.
   - 흔적 : 종료 시 실행 흔적(MUICache, UserAssist) 선택적 삭제 ([TraceClean]의 SmartSkip=0 으로 사용, 대상: {Exe} / {Launcher}).
   - 공용 영역 : 여러 프로그램이 함께 쓰는 레지스트리 영역(예: OpenWithProgids)에서 이름이 일치하는 값만 종료 시 선택적 삭제.
     호스트 소유 값은 보호 ([SharedClean]의 SmartSkip=0 으로 사용).
   - 충돌 검사 : 다른 런처가 같은 레지스트리 키를 사용 중이면 경고 후 실행 중단.

3. Forensic Recovery
   - Session Tracking : UUID and PID monitoring for session integrity validation.
   - Rollback : Restores previous state via `*.reg.bakk` snapshots on abnormal termination.
   - AppData Recovery : On next launch, detects `*_bakk` folders left by crash, 
     removes orphaned Junction Points, and restores original AppData automatically. 

   [포렌식 복구]
   - 세션 추적 : UUID 및 PID 모니터링을 통한 세션 무결성 검증.
   - 롤백 : 비정상 종료 시 `*.reg.bakk` 스냅샷을 통한 이전 상태 복원.
   - AppData 복구 : 다음 실행 시 크래시 잔류 `*_bakk` 폴더를 자동 탐지하여 고아 Junction 제거 및 원본 AppData 복원. 

4. Shell Integration
   - File Association : Registers extension / ProgID / icon / open command on launch and removes them on exit (HKCU by default, HKLM optional).
   - Shell Extension : Registers COM shell extension DLLs (context menu / drag-and-drop / property sheet handlers; HKLM, requires administrator rights).
     Apps with more than one server DLL are supported (a DLL can be set per CLSID).
   - Host Protection : Host keys are backed up before writing and restored on exit. HostSkip skips registration when the host already has its own installation.

   [셸 통합]
   - 파일 연결 : 실행 시 확장자 / ProgID / 아이콘 / 열기 명령을 등록하고 종료 시 제거 (기본 HKCU, HKLM 선택).
   - 셸 확장 : COM 셸 확장 DLL 등록 (우클릭 / 드래그 / 속성 시트 핸들러. HKLM, 관리자 권한 필요).
     서버 DLL 이 둘 이상인 앱도 지원 (CLSID 별로 DLL 지정 가능).
   - 호스트 보호 : 쓰기 전 호스트 키를 백업하고 종료 시 복원. HostSkip 으로 호스트 설치본이 있으면 등록을 건너뜀.

5. Multiple Instances
   - Primary / Secondary : With AllowMultipleInstances=1, the first launcher is the primary instance and later launchers are secondary instances.
     Secondary instances reuse the environment prepared by the primary and skip recovery, injection, registration and cleanup.
   - Cleanup : The primary instance waits until every target exe in its folder has exited, then performs the cleanup once.

   [다중 인스턴스]
   - 주 / 보조 : AllowMultipleInstances=1 이면 먼저 실행된 런처가 주 인스턴스, 이후 런처는 보조 인스턴스.
     보조 인스턴스는 주 인스턴스가 만든 환경을 그대로 쓰며 복구 · 주입 · 등록 · 정리를 하지 않음.
   - 정리 : 주 인스턴스가 폴더 안의 타겟 exe 가 모두 종료될 때까지 기다린 뒤 정리를 한 번만 수행.

========================================================================
[Configuration]
========================================================================

1. Setup
   - Naming Convention : The launcher executable and its INI file must share the same base name (e.g. TargetApp_M.exe and TargetApp_M.ini).
     The suffix _M must be included in the filename (placing it at the end is recommended) for management and identification purposes.
     The target executable is specified separately by ExeName= in [Launch].
   - Binary Placement : Place the target application folder inside the App/ directory.
   - Bitness : Build the launcher per bitness (x86 portable = x86 launcher, x64 portable = x64 launcher).
   - INI Lookup : For TargetApp_x64_M.exe / TargetApp_x86_M.exe, the first existing INI is used: own bitness -> TargetApp_M.ini -> other bitness.

   [설치]
   - 네이밍 규칙 : 런처 실행 파일과 INI 파일은 같은 이름이어야 하며 (예: TargetApp_M.exe 및 TargetApp_M.ini),
     관리 및 식별을 위해 파일명에 _M 접미사를 반드시 포함 해야 함 (끝에 두는 것을 권장).
     타겟 실행 파일은 [Launch]의 ExeName=으로 별도 지정.
   - 바이너리 배치 : 타겟 애플리케이션 폴더를 App/ 디렉토리 내부에 배치.
   - 비트 : 런처를 비트별로 따로 빌드 (x86 포터블 = x86 런처, x64 포터블 = x64 런처).
   - INI 탐색 : TargetApp_x64_M.exe / TargetApp_x86_M.exe 이면 자기 비트 -> TargetApp_M.ini -> 다른 비트 순으로 처음 존재하는 INI 사용.

2. Directory Structure (상세 폴더 구조) 

  TargetApp_M/
 	│  
	├─ TargetApp_M.exe           	# Launcher Executable / 런처 실행 파일
 	├─ TargetApp_M.ini           	# Configuration File / 설정 파일
 	├─ TargetApp_M.log           	# Runtime Log / 실행 로그 (활성 시 생성)
 	│
 	├─ App/                   		# Core Files & Templates / 핵심 파일 및 템플릿
 	│	│
	│	├─ TargetApp/            	# Target Application / 원본 타겟 앱 바이너리
	│	│
	│	├─ Ext/					# Extra / 추가 파일
	│ 	│	├─ Ast/        		# Assets Injection / 주입용 어셋 파일 
	│	│	├─ Env/        		# Environment File / 환경설정용 파일	
	│	│	└─ Res/      			# UI Resources / 폴더아이콘, 스플래시 이미지	 
 	│	│
 	│	└─	RawDat/            		# Default Data Template / 데이터 초기 템플릿
	│ 		├─ AppDat/        	# System AppData Sandbox / 시스템 앱 데이터 샌드박스
	│          │	├─ Local/      	# %LocalAppData% 대체
	│          │	├─ LocalLow/    	# %LocalLow% 대체
	│          │	└─ Roaming/     	# %AppData% 대체
	│		├─ Reg/             	# Registry Templates / 주입용 .reg 템플릿	
	│		├─ Set/      			# Portable Settings / 앱 폴더 병합용 설정 파일     
	│		└─ Usr/             		# User File / 사용자 파일 *
	│    	   
 	└─ Dat/                   			# Active User Data / 사용자 저장소 (수정 사항 저장)
     
			* Usr folder is for ADVANCED USERS ONLY.
			* Usr 폴더는 고급 사용자 전용.	

3. Customization
   - Core configuration via TargetApp_M.ini.

   [사용자 정의]
   - TargetApp_M.ini를 통한 코어 설정.

______________________________________________________________________________________________________________________

This program was created with AutoIt. Some antivirus programs may incorrectly detect it as a virus.
본 프로그램은 AutoIt으로 제작되었습니다. 일부 백신이 바이러스로 오진 할 수 있습니다.

========================================================================
[Disclaimer]
========================================================================

Provided **"AS IS"**, without warranty.
This is a **private project**. No technical support is provided.
본 프로그램은 **"있는 그대로"** 제공되며, 사용 중 발생하는 문제에 대해 제작자는 책임을 지지 않습니다.
기술 지원은 제공되지 않습니다.

Developer	: MIIIME 
Website		: https://www.miiime.com 
GitHub		: @miiimeworks 
Update		: 2026.11.01
