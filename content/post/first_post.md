---
title: "블로그 시작"
date: 2020-10-11T12:44:00+09:00
Description: ""
Tags: []
Categories: []
DisableComments: false
---

예전부터 개인 도메인 위에 개인 블로그를 하나 운영하고 싶은 바램이 있었지만 게으른 천성 때문에 실천에 옮기지 못했다. 그런데 최근 회사에서 경험했던 힘든 시간들이 나에게 여러모로 생각할 거리를 던져주었다. 짧지 않은 시간을 거치며 몇 가지 결론을 스스로 냈는데 그 중 하나가 "나는 좀 더 적극적으로 노력해야 한다"였다. 나의 영원한 숙적인 게으름을 타파하고, 변화를 두려워하지 말고, 집중력 있게 연습과 실전을 계속 경험할 것. 흠, 어린 시절 '착한 사람이 되자'처럼 애매모호한 목표를 거창하게 말하는 것 같아 부끄럽긴 하지만. - 이 경험과 생각의 흐름에 대해서는 다음에 자세히 이야기 할 기회를 두고 - 이 결론으로부터 몇가지 숙원 사업 진행이 이어졌고 개인도메인 구매와 블로그의 운영도 비로소 시작되었다.  

### 도메인 구입  
구글 도메인에서 내 이름 영문을 한글 순서 대로 검색을 하니 사용 가능한 Top level domain에 'dev'가 있어 망설임 없이 구매했다. 가격도 1년에 12달러 정도로 커피 3~4잔 가격 뿐이 되지 않는다. Sub domain을 여럿 두고 사용하면 충분히 제 값을 하지 않을까 싶다. (사실 지금와서 드는 생각인데 'leeminwoo'가 아닌 'minwoolee'로 해야 했는데...그리고 이름을 너무 드러내는 것 자체도 부담이긴 하다)  

### 블로그 구축  
서비스 형 블로그(tumblur, tistory, branch등)들은 처음부터 고려하지 않았고, 구글 클라우드 가상 머신 위에 서버를 두고 설치형 블로그를 운영해 보려고 했다. 단순한 블로그 설치/운영을 위해서는 굳이 높은 사양이 필요 없어서 가장 낮은 사양(Daul CPU, 1G RAM, 10G HDD, Ubuntu)으로 구성해 며칠 두고 보았는데 생각보다 요금이 저렴하지 않았다. 외부 트래픽이 없이 가상 머신을 4일 동안 운영한 것만으로 약 5달러 정도의 비용이 청구되었는데, 한 달을 어림잡아 계산하면 가상 머신 운용에만 한 달에 약 5만원의 비용이 드는셈이고, 추가 기능 요금에 외부 트래픽에 대한 요금도 추가된다면 한 달에 어림잡아 5~7만원의 고정비용이 예상되었다. (가격 정책에 대해서 자세히 살펴보진 않았고, 4일간의 경험과 추측만으로 작성) 생각했던 것보다 너무 높은 비용에 바로 다른 대안을 찾아서 github.io를 살펴보았다. 정적 사이트 호스팅만 가능하다는 제약사항이 있지만 내가 엄청 요란스러운 웹페이지를 만들진 않을테고 요즘 이런 류의 블로그가 개발자 사이에서 꽤 유행하니 괜찮은 대안이라고 생각이 들었다. 정적 사이트 생성 도구는 jeykll으로 진행하려 했다. github.io에서 기본으로 제공하는 기능이고 많은 Reference가 장점이지만 속도가 느리다는 피드백들이 눈에 들어와 주섬주섬 다른 도구를 살펴보고(https://jamstack.org/generators) 심사숙고 끝에 Hugo로 노선을 바꾸었다. 일단 속도가 빠르다는 장점, 그리고 Golang으로 개발 되었다는게 크게 맘에 든다. 한글 레퍼런스가 부족한게 걱정되지만 문제가 생기면 영어 공부 겸사겸사 하면서 해결하자 생각하고 시작.  

### Hugo를 통해 Github.io에 blog 설치 (Windows10, WSL2, Ubuntu18.04)


Homebrew를 /home/linuxbrew/.linuxbrew 위치에 설치
```
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

아래 4줄의 스크립트를 모두 수행해 Homebrew의 PATH 환경 설정을 수행
```
$ test -d ~/.linuxbrew && eval $(~/.linuxbrew/bin/brew shellenv)
$ test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
$ test -r ~/.bash_profile && echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.bash_profile
$ echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.profile
```

Homebrew를 통해 Hugo를 설치
```
$ brew install hugo
```

Hugo Version 체크
```
$ hugo version
```

프로젝트 폴더를 생성
```
$ hugo new site leeminwoodev_hugo_res
$ cd leeminwoodev_hugo_res
$ ll
drwxr-xr-x 10 minwoo minwoo 4096 Oct 11 01:23 ./
drwxr-xr-x  3 minwoo minwoo 4096 Oct 11 01:22 ../
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 archetypes/
-rw-r--r--  1 minwoo minwoo   82 Oct 11 01:22 config.toml
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 content/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 data/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 layouts/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 static/
drwxr-xr-x  2 minwoo minwoo 4096 Oct 11 01:22 themes/
```

anatole테마를 submodule로 theme위치 아래에 받아오기
```
$git submodule add https://github.com/lxndrblz/anatole.git themes/anatole 
```

미리 생성한 leeminwoodev_hugo.github.io 저장소를 submodule로 public위치에 받아오기
```
$git submodule add -b master git@github.com:vical1024/leeminwoodev_hugo.github.io.git public
```

config.toml 수정
```
$vim config.toml
```

새 Post 생성
```
$hugo new post/first_post.md
```

Post 내용 작성
```
$vim ./post/first_post.md
```

로컬서버 Start
```
$hugo server -D
```

이상이 없다면 최종 빌드
```
$hugo -D
```

Public 폴더 안에 정적 사이트 파일들이 생성되었는지 확인
```
$cd public
$ll
-rwxrwxrwx 1 minwoo minwoo 5.7K Dec 31 23:26 404.html
-rwxrwxrwx 1 minwoo minwoo   18 Dec 31 23:02 CNAME
drwxrwxrwx 1 minwoo minwoo 4.0K Dec 31 23:02 categories
drwxrwxrwx 1 minwoo minwoo 4.0K Dec 31 23:03 css
-rwxrwxrwx 1 minwoo minwoo 7.1K Dec 31 23:26 index.html
-rwxrwxrwx 1 minwoo minwoo 1.6K Dec 31 23:26 index.xml
drwxrwxrwx 1 minwoo minwoo 4.0K Dec 31 23:02 js
drwxrwxrwx 1 minwoo minwoo 4.0K Dec 31 23:02 page
drwxrwxrwx 1 minwoo minwoo 4.0K Dec 31 23:02 post
-rwxrwxrwx 1 minwoo minwoo  674 Dec 31 23:26 sitemap.xml
drwxrwxrwx 1 minwoo minwoo 4.0K Dec 31 23:02 tags
```

변경사항들을 모두 git commit & push

### Deploy 자동 수행 Script 생성

```
#!/bin/sh

set -e

printf "Deploying updates to GitHub\n"
hugo
cd public
git add .
msg="rebuilding site $(date)"
if [ -n "$*" ]; then
	msg="$*"
fi
git commit -m "$msg"
git push origin master
```