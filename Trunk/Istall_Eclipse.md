## Install Ecelipse
### 이클립스 다운로드

http://www.eclipse.org/

#### 압축 풀기

> tar xfv <download file>

#### 인스톨러 실행

> <압축 푼 폴더>/eclipse installer/ecelipse inst
>
> 관리자 권한으로 실행하면 사용자 계정으로 폴더 접근이 안돼 아래 오류가 발생할 수 있다. 일반 권한으로 설치하자.
>
> ``The Eclipse executable launcher was unable to  locate its companion shared library``

#### 바로가기 만들기

아래 파일을 만들고 

``sudo gedit ~/local/share/applications/eclipse.desktop``

<InstallDir>을 위에서 설치한 경로 변경해 저장한다.

```
[Desktop Entry]
Name=Eclipse
Type=Application
Exec=<InstallDir>/eclipse/eclipse
Terminal=false
Icon=<InstallDir>/eclipse/icon.xpm
Comment=Integrated Development Environment
NoDisplay=false
Categories=Development;IDE;
Name[en]=Eclipse
```





