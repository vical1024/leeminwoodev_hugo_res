---
title: "First_post"
date: 2020-10-11T12:44:00+09:00
---

예전부터 개인 도메인 위에 개인 블로그를 하나 운영하고 싶은 바램이 있었습니다. 물론 게으른 제 천성 때문에 실천에 옮기지 못했습니다. 그런데 최근 회사에서 경험하는 많은 어려움이 저에게 많은 시사점을 주었고 많은 생각 끝에 저는 제가 좀 더 적극적으로 노력해야 한다고 결론지었습니다. 게으름을 타파하고, 변화를 두려워하지 말고, 집중력 있게 연습과 실전을 계속 경험할 것. 흠, 초등학생의 '착한 사람이 되자'와 같은 애매모호하게 뜬구름 잡는 목표를 거창하게 말하는 것 같아 부끄럽네요. 제 경험과 생각의 흐름에 대해서는 다음에 자세히 이야기 할 기회를 두고 다시 한 번 정리해보죠. 어쨌든 이런 결론 도출로부터 여러 숙원 사업 진행이 이어졌고 앞서 말씀드린 개인도메인 구매, 개인 블로그의 운영도 이 중 일부분입니다.
제일 먼저 한 일 중 하나는 도메인을 사는 일이었습니다. 막상 시작하니 그리 어려운 일이 아니었습니다. 구글 도메인에서 제 이름 영문을 한글 순서로 'leeminwoo'로 두고 Second level 검색을 하니 사용 가능한 Top level domain에 'dev'가 있어 망설임 없이 구매했습니다. 가격도 1년에 12달러 정도로 커피 3~4잔 가격 뿐이 되지 않습니다. Sub domain을 여럿 두고 사용하면 충분히 제 값을 하겠죠. (사실 지금와서 드는 생각인데 'leeminwoo'가 아닌 'minwoolee'로 해야 했는데요...)
두 번째로는 본격적인 블로그 구축입니다. 처음에는 구글 클라우드 가상 머신 위에 서버를 두고 블로그를 운영해 볼까 했습니다. 아시다시피 구글 클라우드에서는 다양하게 가상 머신 구성을 할 수 있습니다. 단순 블로그 운영을 위해서라면 높은 사양이 필요 없을 듯해서 가장 낮은 사양(Daul CPU, 1G RAM, 10G HDD, Ubuntu)으로 구성해 며칠 두고 보았는데, 아뿔싸 이게 생각보다 저렴하지 않습니다. 외부 트래픽이 없이 가상 머신을 4일 동안 운영한 것만으로 약 5달러 정도의 비용이 청구. 한 달을 어림잡아 계산하면 가상 머신 운용에만 한 달에 약 5만원의 비용이 드는셈이고, 추가 기능 요금에 외부 트래픽에 대한 요금도 추가된다면 한 달에 어림잡아 5~7만원의 고정비용이 나갈 것 같네요.(가격 정책에 대해서 자세히 살펴보진 않았고, 4일간의 경험과 추측만으로 작성) 생각했던 것보다 너무 높은 비용에 바로 다른 대안을 찾아봅니다. 그렇다면 github.io는 어떨까요? Github에서는 github.io 도메인으로 정적 사이트 호스팅을 제공하고 있고, 이것은... 이것은... 무료입니다! 그렇다면 사용하지 않을 이유가 없죠. 정적 사이트 생성 도구는 두말없이 jeykll으로 진행하려고 했습니다. 하지만 잠깐의 웹서핑만으로도 jeykll이 가진 치명적인 단점, 느리다는 피드백들이 눈에 들어와 주섬주섬 다른 도구를 살펴보고(https://jamstack.org/generators) 심사숙고 끝에 Hugo를 선택했습니다. 일단 속도가 빠르다는 장점, 그리고 Golang으로 개발 되었다는게 크게 맘에 듭니다. 한글 레퍼런스가 부족한게 걱정되지만 문제가 생기면 영어 공부 겸사겸사 하면서 해결하자 생각하고 시작해봅니다.

제 데스크탑 Windows10, WSL2, Ubuntu18.04 환경 기준으로 아직 Homebrew가 설치 되어있지 않아 설치부터 필요합니다. 아래 스크립트를 실행해 Homebrew를 /home/linuxbrew/.linuxbrew 위치에 설치하고,
```
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

아래 4줄의 스크립트를 모두 수행해 Homebrew의 PATH 환경 설정을 마칩니다. 
```
$ test -d ~/.linuxbrew && eval $(~/.linuxbrew/bin/brew shellenv)
$ test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
$ test -r ~/.bash_profile && echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.bash_profile
$ echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.profile
```

Homebrew 설치가 완료되었으니 아래 아래와 같이 Hugo를 설치합니다.
```
$ brew install hugo
```

설치 완료가 되면 설치 완료 기념으로 Version정도 한 번 체크해주고요,
```
$ hugo version
Hugo Static Site Generator v0.76.3/extended linux/amd64 BuildDate: unknown
```

바로 사이트 생성을 위한 프로젝트 폴더를 만들어준다.
```
$ hugo new site gitio_leeminwoodev
$ cd gitio_leeminwoodev
$ ll
drwxr-xr-x 10 minwoo minwoo 4096 Oct 11 01:23 ./
drwxr-xr-x  3 minwoo minwoo 4096 Oct 11 01:22 ../
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 archetypes/
-rw-r--r--  1 minwoo minwoo   82 Oct 11 01:22 config.toml
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 content/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 data/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 layouts/
drwxr-xr-x  4 minwoo minwoo 4096 Aug 31  1754 public/
drwxr-xr-x  3 minwoo minwoo 4096 Oct 11 01:23 resources/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 static/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 themes/
```