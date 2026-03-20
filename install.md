
# 로컬 환경 구성

## Git Client 설치
### 설치
`git -v` 명령으로 설치여부 검사하여 미설치 시   
[Git Client 설치하기](https://git-scm.com/downloads)에 접속하여 설치파일을 다운로드 받아 설치합니다.   
  
### 기존 인증정보 삭제  
다른 사람의 PC를 사용한다면 기존 사용자의 Git 인증정보가 있을 수 있습니다.    
처음 Git Client를 설치했거나 본인 PC를 사용하고 있다면 수행할 필요 없습니다.    
```
git credential-cache exit
git credential reject  
```

Window 사용자는 자격증명 관리자에서 삭제하는게 제일 확실합니다.   
```
Windows 자격 증명 관리자에서 Git credential 삭제
1. 자격 증명 관리자 열기
Win + R → control /name Microsoft.CredentialManager
또는 제어판 → 사용자 계정 → 자격 증명 관리자

1. Windows 자격 증명 탭에서 찾기
Git 관련 항목들을 찾아보세요:

git:https://github.com
git:https://gitlab.com
LegacyGeneric:target=git:https://github.com

3. 삭제
해당 항목 클릭 → 제거 버튼
```

### Git 사용법
https://happycloud-lee.tistory.com/93

| [Top](#로컬-환경-구성) |

---

## Window Terminal의 Git Bash 설정(Windows Only)
최근 제품/서비스들은 Linux 위주로 가이드하는 것들이 많습니다.        
Git Bash 터미널에선 Linux의 명령을 사용할 수 있어 매우 유용합니다.        

- Window Terminal 실행
  하단 검색바에서 '터미널'을 입력하고 '터미널'앱을 클릭    

- Window Terminal 설정 클릭    
  
- Git Bash 프로필 추가   
  좌측에서 '새 프로필 추가'를 클릭하고 아래와 같이 입력한 후 저장합니다.   
  명령줄의 '찾아보기'를 클릭하여 `C:\Program Files\Git\bin\bash.exe`가 없으면 Git Client를 먼저 설치해야 합니다.   
  
- 기본 프로필 지정    
  좌측에서 '시작' 메뉴 클릭 후, 기본 프로필을 'Git Bash'로 변경합니다.   

- 확인  
  설정 후 Window Terminal에서 새창열기에 'Git Bash'가 보여야 합니다.  

  새 창을 열면 기본이 Git Bash로 나오면 됩니다.  


| [Top](#로컬-환경-구성) |

---

## Microsoft Visual Studio Code 설치 
### 설치   
Microsoft Visual Studio Code(vscode라고 많이 부름)는 주로 Javascript, Python과 같은   
Interpreter 언어를 개발할 때 사용하는 IDE(Integrated Development Environment)입니다.  
> **Interpreter 언어**: 통역가라는 직역처럼 별도의 실행파일을 만들지 않고 소스를 바로 실행하는 언어   

다운로드 페이지에 접속하여 설치파일을 다운로드하여 설치: [vscode설치](https://code.visualstudio.com/download) 


| [Top](#로컬-환경-구성) |

---

## PATH 설정

~/.local/bin 디렉토리를 PATH에 추가함:

# Mac 사용자
```
code ~/.zshrc
```

# Linux/Windows 사용자 (Windows는 Git Bash 터미널 사용)
```
code ~/.bashrc
```
아래 내용을 파일 끝에 추가:
```
export PATH=~/.local/bin:$PATH
```
(중요) 경로 추가 후 반드시 source ~/.bashrc 또는 source ~/.zshrc 실행 또는 새 터미널 열기  

---

## Claude Code 설치  
**Linux/Mac**     
```bash
# macOS/Linux
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows**     
PowerShell에서 수행합니다.   
```
irm https://claude.ai/install.ps1 | iex
```

설치 후 초기 구성:

```bash
claude 
```

> 2026년 2월부터 Claude Code는 npm을 이용하지 않고 독립적인 런타임 엔진을 사용
> 기존 설치한 사람은 아래 명령으로 기존 claude를 삭제하고 재설치 바람 
> **삭제**   
> ```
> # 강제 언인스톨
> npm uninstall -g @anthropic-ai/claude-code --force
> # 확인
> where claude
> 만약, 위 명령 결과가 나오면 아래 수행하여 삭제   
> rm -rf {위 결과 파일 경로}
> # 캐시 정리
> npm cache clean --force
> ```
> **설치**
> ```
> claude install 
> ```

| [Top](#로컬-환경-구성) |

---


## Claude Code 편의 명령어 설정    
Claude Code의 CLI인 'claude'의 단축어를 등록합니다.   
이때 '--dangerously-skip-permissions'라는 옵션을 지정한 단축어 'cy'를 등록하면 매우 편합니다.    
이 옵션은 로컬의 파일 변경 등 중요 작업 시 매번 사용자에게 묻지 않게 하는 옵션입니다. 
위험하기 때문에 로컬에서만 사용하셔야 합니다. VM과 같은 곳에 Claude Code 설치하여 사용할 땐 하지 마십시오.     
Linux/Mac사용자는 기본 터미널에서 수행하고, Window사용자는 Window Terminal의 Git Bash에서 수행합니다.   
 
**1.시작 스크립트 파일 열기**      
Linux/Window   
```
code ~/.bashrc
```

Mac   
```
code ~/.zshrc
```

**2.Alias 등록**  
맨 아래에 아래 Alias를 등록합니다.    
```
alias cc-yolo='claude --dangerously-skip-permissions --verbose'
alias cc-safe='claude'
alias cy='cc-yolo'
```

Window 사용자는 Powershell에서도 사용할 수 있도록 아래 작업을 더 합니다.    
Window Terminal에서 Powershell창을 열고 아래를 수행하세요.   

```
code $PROFILE
```

아래와 같이 Alias를 등록합니다.   
```
function cc-yolo { claude --dangerously-skip-permissions --verbose @args }
function cc-safe { claude @args }
function cy { cc-yolo @args }
```

---

**3.사용방법**     
- cc-yolo: YOLO Mode로 Claude Code 실행. Think과정도 표시.   
- cc-safe: Safe Mode로 Claude Code 실행
- cy: cc-yolo와 동일함. 기본값을 바꾸고 싶으면 alias설정을 변경하면 됨      

편의 명령을 설정한 터미널을 모두 닫고 새 터미널을 열어 명령이 동작하는지 확인합니다.

---

## Oh My ClaudeCode (OMC) 설치
OMC는 Claude Code를 더 잘 사용하기 위한 플러그인입니다.    
  
Claude Code 실행 후 프롬프트에서 순차 수행:
```
cy
```

아래 명령 수행  
```
/plugin marketplace add https://github.com/Yeachan-Heo/oh-my-claudecode
```

```
/plugin install oh-my-claudecode
```

아래 명령으로 셋업 수행. Setup 시 MCP는 context7만 설치  
```
/omc-setup
```

| [Top](#로컬-환경-구성) |

---

## Python 설치
최신 버전을 설치하세요.  
https://www.python.org/ftp/python/3.14.3/python-3.14.3-amd64.exe

