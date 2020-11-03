+++
title = "WSL 2 X11 애플리케이션을 더 깔끔하게 실행하는 방법"
description = "WSL 2 X11 애플리케이션을 윈도우 환경에서 좀 더 깔끔하게 실행할 수 있는 방법을 소개합니다."
date = 2020-11-03T23:19:57+09:00
weight = 20
draft = false
[[author]]
    name = "남정현"
    email = "rkttu@rkttu.com"
    bio = "WSLHUB 운영진"
    linklabel = "GitHub"
    linkurl = "htts://github.com/rkttu"
+++

![VCXSRV와 Seamless Multi Window로 구현한 Windows + Linux 데스크톱](https://miro.medium.com/max/1400/1*pjvI3auVYN60R5BBGjWuhA.png)

WSL 2에 조만간 추가될 예정인 Windows 10용 Wayland Compositor를 기다리시는 분들이 많을 것 같습니다. 올해 겨울 즈음에 프리뷰 버전이 출시될 것으로 예상되는 이 기능도 좋겠지만, 지금 당장 사용할 수 있는 최적화된 GUI 환경을 찾으시는 분들을 위해 간단한 팁을 소개할까 합니다.

## First Things First: 한국어 입력기 추가하기

이번에 저는 WSL 2에서 한글 입력기 추가하기라는 아티클의 도움을 받아 이전보다 더 간결하게 한국어 입력기를 추가할 수 있었습니다. Felix Song님께 감사드립니다.

[WSL2에서 한글 입력 사용하기](https://sigmafelix.wordpress.com/2020/08/17/wsl2%ec%97%90%ec%84%9c-%ed%95%9c%ea%b8%80-%ec%9e%85%eb%a0%a5-%ec%82%ac%ec%9a%a9%ed%95%98%ea%b8%b0/)

이 아티클에서 소개하는대로 한국어 입력기 설정을 마친 후를 전제로 계속 이야기를 진행해볼까 합니다.

## VCXSRV 바로 가기 아이콘 만들기

기본적으로 VCXSRV는 다양한 설정을 편리하게 등록할 수 있도록 별도의 설정 파일을 작성하는 마법사를 제공함과 동시에 설정 파일을 VCXSRV와 연결하도록 셸 레지스트리 설정을 변경합니다.

개인적으로는 이런 방식보다 바로 가기 아이콘을 이용하여 명령줄을 직접 지정하는 것을 선호하는데요, VCXSRV를 Seamless 모드로 띄우는 바로 가기 아이콘을 아래 명령줄을 지정하여 만들어보려고 합니다.

```batch
"%PROGRAMFILES%\VcXsrv\vcxsrv.exe" :0 -multiwindow -clipboard -nowgl -ac
```

0번 디스플레이를 사용하도록 명시적으로 지정하고, Seamless 모드를 나타내는 Multiwindow 모드, 그리고 클립보드 입출력과 Native OpenGL을 끄도록 설정하며, -ac 스위치를 추가로 지정합니다.

이렇게 만든 바로 가기 아이콘이 자동 실행될 수 있도록, shell:startup 이라는 주소를 폴더 창 주소 입력 부분에 넣고 Enter 키를 누릅니다. 그러면 시작프로그램 폴더가 자동으로 열리며, 여기에 방금 만든 바로 가기 아이콘을 복사해서 넣습니다.

> 가끔 VCXSRV가 버그로 인해서 강제 종료가 될 수 있으므로, 이럴 때 편리하게 다시 실행할 수 있도록 바탕 화면 등의 적당한 위치에 바로가 아이콘은 남겨두는 것이 편리합니다.

## 한국어 입력이 가능하도록 Firefox 띄워보기

앞 단계에서 한 것과 같은 요령으로 바로 가기를 만들되 이번에는 다음과 같이 지정하여 바로 가기를 만듭니다.

```batch
%WINDIR%\System32\wsl.exe --distribution Ubuntu-20.04 -- export QT_IM_MODULE=fcitx; export GTK_IM_MODULE=fcitx; export XMODIFIERS=@im=fcitx; export DefaultIMModule=fcitx; fcitx-autostart; DISPLAY=$(ip route | awk '{print $3; exit}'):0 firefox
```

환경에 따라 distribution 스위치 다음에 오는 배포판 이름은 정확하게 다시 지정해야 하며, fcitx 및 한국어 입력기 설정과 Firefox 패키지가 미리 설치되어있어야 합니다.

위와 같이 바로 가기를 만든 후에는 속성으로 들어가서, 바로 가기 탭의 실행 드롭 다운을 최소화로 바꿉니다. 또한 원한다면 아이콘 변경 버튼을 눌러 적절한 아이콘을 직접 지정해도 됩니다.

![WSL 2 X11에서 Firefox를 실행하는 바로 가기 만들기](https://miro.medium.com/max/906/1*-AFzAv3UumWdBns6jOnhvQ.png)

위와 같이 설정을 바꾼 다음 바로 가기를 더블 클릭하여 테스트하면 아래 그림처럼 한국어 입력이 가능한 Firefox가 실행될 것입니다.

이렇게 만들면 콘솔 작업을 방해받는 일 없이, 그리고 콘솔에 띄워지는 복잡한 디버그 메시지를 보는 일 없이 깔끔하게 리눅스 GUI 애플리케이션을 실행할 수 있으므로 훨씬 쾌적할 것입니다.

![한국어 입출력이 자유로운 상태로 Firefox가 X11 환경에서 실행 중입니다.](https://miro.medium.com/max/1400/1*YeKXO1GmuXZyhUXG-xeY_w.png)

## 응용하기

같은 방법으로 Nautilus (파일 탐색기), File Roller (압축 파일 관리자), Inkscape (벡터 그래픽 편집기), LibreOffice (오피스 스위트), 그리고 JetBrains의 IDE를 설치하여 사용할 수 있습니다.

![VCXSRV와 Seamless Multi Window로 구현한 Windows + Linux 데스크톱](https://miro.medium.com/max/1400/1*pjvI3auVYN60R5BBGjWuhA.png)

다만 일부 애플리케이션 (글을 작성하는 현재 시점에서는 Gimp가 해당됩니다.)은 VCXSRV에 크래시를 일으켜서 사용할 수 없는 경우도 존재할 수 있습니다. 그리고 아직 Wayland Compositor가 완성된 후 기대할 수 있는 3D 가속 관련 부분이나, 오디오 지원 등의 부분은 WSL 2 환경에서 원활하지 않은 부분이 있습니다.
