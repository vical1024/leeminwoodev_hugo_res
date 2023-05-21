---
title: "Yocto를 이용해 나만의 rasberrypi image를 만들어보기 (1)"
date: 2021-01-24T20:12:43+09:00
Description: ""
Categories: []
DisableComments: false
---
이전 포스트에서 Yocto의 장점에서만 나열했던 것 같은데, 아래와 같은 점들도 감안을 해야 할 것 같아 간단하게 정리한다. (단점이라고 하기는 애매하고...) 실제 프로젝트를 진행할 때는 필수로 감안되어야 할 것이다.

- 프로젝트의 방대함으로 오는 관리의 어려움이 있다. Yocto는 결국 도구일 뿐이며 이를 운영하는 조직이나 사람에 따라 천차만별의 효율로 쓰일 수 있는데 레시피의 관리, 개발 프로세스의 관리 등을 상환에 따라 잘 고려해야 한다. (여기서 말하는 "잘"에 대해서는 다음에 한 번 더 고민해보자.)
- Recipe에서 각각의 개별 저장소를 다운받는 것으로부터 빌드가 시작된다. 이는 다시 말하면 각 개별 컴포넌트의 소스코드 혹은 배포 산출물을 저장할 수 있는 충분한 공간이 필요하다는 말이다. 간단한 시스템이면 모르겠지만, 다양한 컴포넌트를 다루는 빌드를 진행할 경우 어마무시한 용량을 사용하게 된다. 내가 지금 회사에서 개발하는 Yocto base 프로젝트는 50G를 훌쩍 넘는 공간 가용량을 필요로 한다.
- 먼저 말했듯 Recipe에서 각각의 개별 저장소를 다운받아야 하니 다운받는 시간이 필요하고, 컴포넌트 빌드가 필요한 경우 이를 진행되어야 한다. 이 때문에 몇 십개, 몇 백개의 Recipe에 대해 이런 과정들을 모두 진행되어야 하니 전체 빌드 시간이 오래 걸리게 된다. 개선할 수 있는 방법은 있지만 Yocto의 구조적인 부분 때문에 개선할 수 있는 부분은 한계가 있다.
- (다른 분들은 잘나서 그런지 이런 코멘트를 본 적이 없는데, 그렇다면... 나 자신 한정) **빌드에 문제가 있는 경우 가끔씩 Bitbake python코드를 디버깅 할 일이 있었는데 변수나 함수의 이름들이 너무 간략하게 정의되어 있고 너무 중복적으로 나타나서 코드를 분석하는데 어려움을 겪었다.**

나는 Yocto 없는 리눅스 제품 개발을 해본적이 없어 정확한 비교를 하긴 힘들지만, **위와 같은 점들을 감안하더라고 임베디드 리눅스를 개발하는 어렵지만 가장 쉬운 길은 Yocto라고 생각한다**. Yocto없이 이에 해당하는 작업들을 일일히 직접 관리하고 포팅한다고 가정하면 생각만으로 머리가 지끈하다.

## 나만의 rasberrypi image를 만들어보기 - 환경

Docker 이미지를 이용해 보겠다. Docker 이미지를 사용하면 호스트 시스템에 Yocto Project의 의존성을 설치하지 않고도 Yocto 빌드 환경을 쉽게 설정할 수 있다. Raspberrypi 재단에서 관리하는 https://github.com/agherzan/meta-raspberrypi에는 해당 저장소를 관리하기 위한 github action 설정이 존재하는데, 이들 github action들은 Docker환경에서 수행된다. 그리고 이 Docker환경을 구성하고 관리하기 위한 Dockerfile이 존재한다. 아쉽게도 One-step은 아니지만 해당 항목을 분석하면 좀 더 용이하게 rasberrypi image를 만들기 위한 Yocto 빌드 환경을 만들 수 있을 것이다.

yocto-dockerfiles 저장소를 클론하고

```bash
$ git clone https://github.com/agherzan/meta-raspberrypi.git
$ cd meta-raspberrypi/.github/workflows/docker-images
```

.github/workflows/docker-images/yocto-builder/Dockerfile를 살펴보면 ubuntu:20.04에 Yocto에 필요한 패키지를 다운받는 걸 확인할 수 있다. 하지만 이 스크립트에서 Poky 저장소를 가져오진 않는다. 좀 더 살펴볼 필요가 있다.

```file
FROM ubuntu:20.04
...
RUN eatmydata apt-get install -qq -y \
    gawk wget git diffstat unzip texinfo gcc build-essential chrpath \
    socat cpio python3 python3-pip python3-pexpect xz-utils debianutils \
    iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev \
    pylint3 xterm python3-subunit mesa-common-dev zstd liblz4-tool
```

이 Dockerfile마지막에 .github/workflows/docker-images/yocto-builder/entrypoint-build.sh을 복사해두는데, 여기에서 poky를 가져온다. entrypoint-build.sh를 잘 살펴보며 스크립트 전반에 많은 매개변수를 필요로 하는데

```file
REPOS=" \
    git://git.yoctoproject.org/poky.git \
"
for repo in $REPOS; do
    log "Cloning $repo on branch $BASE_REF..."
    git clone --depth 1 --branch "$BASE_REF" "$repo"
done

# shellcheck disable=SC1091,SC2240
. ./poky/oe-init-build-env build
```

.github/workflows/yocto-builds.yml에 볼 수 있듯이 workflow를 수행할 때 매개변수와 함께 entrypoint-build.sh를 수행해 주는 것을 볼 수 있다.

```file
          docker run --rm \
            -v "$GITHUB_WORKSPACE:/work:ro" \
            -v "$DL_DIR:$DL_DIR:rw" \
            -v "$SSTATE_DIR:$SSTATE_DIR:rw" \
            --env "BASE_REF=$GITHUB_BASE_REF" \
            --env "MACHINE=${{ matrix.machine }}" \
            --env "DISTRO=${{ matrix.distro }}" \
            --env "IMAGE=${{ matrix.image }}" \
            --env "DL_DIR=$DL_DIR" \
            --env "SSTATE_DIR=$SSTATE_DIR" \
            "yocto-builder-${{ github.event.number }}" \
            /entrypoint-build.sh
```

다시 한 번 entrypoint-build.sh을 보니까 meta layer추가와 bitbake까지 모두 수행해준다.

```file
# Add the BSP layer
bitbake-layers add-layer "$META_RASPBERRYPI_PATH"
...
# Fire!
MACHINE="$MACHINE" bitbake "$IMAGE"
```

그럼 나는 clone해두었던 meta-raspberrypi 저장소에서 entrypoint-build.sh의 마지막 두 줄만 삭제해 entrypoint-build.sh에서 bitbake build가 시작되는 걸 막고, 이 상태에서 docker image를 빌드한 후에 matrix.machine, matrix.distro, matrix.image을 적당하게 채워주고 docker run entrypoint-build.sh을 수행한 후에 다시 bash 쉘로 접근해 보려고 한다.
