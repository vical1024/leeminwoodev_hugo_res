---
title: "Yocto 공부"
date: 2021-01-23T21:36:54+09:00
Description: ""
Categories: []
DisableComments: false
---
일반적인 PC 환경에서는 기성 배포판을 커스터마이징하는 Top-Down방식으로 원하는 형태의 Linux를 얻을 수 있다. 개인이 운영하는 Linux는 물론 크고 작은 Enterprise 서버 환경에서 동작하는 Linux등은 모두 이러한 방식으로 생성되고 관리되고 있다. 하지만 여기에는 큰 제약이 따르는데 대부분의 배포판은 X86 하드웨어 타겟으로 이뤄져 있기 때문에 다양한 CPU 아키텍쳐에 걸쳐 일어나는 임베디드 개발 환경에 기민하게 대응하기 어렵다. 때문에 임베디드 Linux의 개발은 Bottom-Up으로 이뤄지는데 Bootloader, Kernel의 선택부터 필수적인 Package, User Application의 추가, 그리고 이를 Build, Install, Packaging하는 과정까지 사용자가 세세하게 관리하며 원하는 최적의 Customized Linux를 만들어 내게 된다. Yocto는 이런 과정을 효율적으로 통제할 수 있는 도구 모음이다. 보드, 칩 업체에서 제공하는 BSP가 있지 않냐는 질문이 있을 수 있을 것 같은데, 요새는 그 BSP를 Yocto를 통해 만든다. 이런 이유로 최근의 임베디드 Linux 개발자에게 Yocto는 피해갈 수 없는 Tech stack이 되었고... 그런 이유로 나도 공부를 조금씩 해보고 있다.

내가 Yocto 공부를 하면서 겪은 어려움은 크게 두 가지였다. 첫 번째 한글로 적힌 Yocto 관련 글들이 많지 않다는 것, 두 번째 그나마 있는 한글 Blog 자료의 설명은 Yocto를 처음 시작하는 사람이 보기에 지나치게 크고 추상적인 부분의 설명에 주로 할애되어 있다는 것이다. 그래서 아래는 최대한 처음 시작하는 사람이 Getting start하며 이 도구를 이해하는 데 도움을 줄 수 있도록 적어보았다.

일단 `Yocto project`는 는 하나의 Customized Linux 빌드를 할 수 있게 해주는 여러 도구의 모음, 그리고 이 도구를 사용하는 방법 자체이다. 이를 구성하는 도구들는  

Open source 상의 여러 package들이 있으며,  
```
$ sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm
```

`Yocto project` 자체의 것도 있다.
```
$ git clone git://git.yoctoproject.org/poky
```

`poky` repository가 포함하는 것들이 사실 `Yocto project`의 핵심인데, Linux를 만들 수 있는 중요한 핵심 Meta(몇몇 자주 쓰이는 Kernel과 Bootloader, 그리고 중요 package 등)와 이 Meta를 읽고 처리할 수 있는 핵심 도구(Script 등)이 있다.

`poky` repository를 베이스로 도구를 사용할 수 있는 기본 환경을 만들고
```
$ source oe-init-build-env
```

이 때 만들어지는 `bblayers.conf`, `local.conf`, `templateconf.cfg`파일 등을 원하는 대로 바꿔준다. 실제 내부에서 사용할 수 있는 keyword와 그에 대한 설명은 두꺼운 책 한 권으로도 모자랄 정도이니 이 부분은 Case by Case로 공부해야 한다. 중요한 포인트는 다음과 같다.  

- `bblayers.conf`: 여러 추가 Meta(Recipe가 담긴 Layer)들의 위치 정보를 담고 있다.
- `local.conf`: Device와 기본 Build 환경를 담고 있다.
- `templateconf.cfg`: 추가 설정에 대한 정보를 담고 있다.

수정 이후 bitbake 명령어를 통해 특정 Meta안에 있는 Recipe를 수행하는데, 이는 내부의 핵심 도구를 통해 Fetch, Build, Install 등의 Step을 "Project 기본 설정 + Recipe 설정"에 기반해 수행한다. 이 때 하나의 Recipe가 다른 Recipes의 Dependance를 가지고 있으면 추가 Dependance recipe 수행이 연쇄적으로 일어난다.
   
   
   
(흠 써놓고 보니... 나도 썩 잘 설명은 못한 것 같네. 나중에 조금씩 수정해봐야지.)
