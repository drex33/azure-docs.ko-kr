---
title: Azure Lab Services를 사용하여 Linux에서 R 및 RStudio로 랩 설정
description: Linux에서 RStudio를 사용하여 R을 가르치기 위해 랩을 설정하는 방법 알아보기
author: emaher
ms.topic: how-to
ms.date: 08/25/2021
ms.author: enewman
ms.openlocfilehash: cc05b682a91deab2d67e9d5ddbe5d4dc85740f6a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176646"
---
# <a name="set-up-a-lab-to-teach-r-on-linux"></a>Linux에서 R을 가르치기 위한 랩 설정

[R](https://www.r-project.org/about.html)은 통계 컴퓨팅 및 그래픽에 사용되는 오픈 소스 언어입니다.  유전학부터 자연어 처리, 금융 데이터 분석까지의 통계 분석에 사용됩니다.  R은 [대화형 명령줄](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line) 환경을 제공합니다.  [RStudio](https://www.rstudio.com/products/rstudio/)는 R 언어에 사용할 수 있는 IDE(대화형 개발 환경)입니다.  무료 버전은 코드 편집 도구, 통합 디버깅 환경 및 패키지 개발 도구를 제공합니다.

이 문서에서는 통계 컴퓨팅을 사용해야 하는 클래스의 구성 요소로서 RStudio와 R에만 중점을 둡니다.  [딥 러닝](class-type-deep-learning-natural-language-processing.md)과 [Python 및 Jupyter Notebooks](class-type-jupyter-notebook.md) 클래스 유형은 RStudio를 다르게 설정합니다.  각 문서에서는 RStudio를 비롯한 많은 [데이터 과학 관련 도구](../machine-learning/data-science-virtual-machine/tools-included.md)가 사전 설치된 [Linux(Ubuntu)용 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) 마켓플레이스 이미지를 사용하는 방법을 설명합니다.  

## <a name="lab-account-configuration"></a>랩 계정 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 방법](./tutorial-setup-lab-account.md)의 자습서를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

다음 표에서 설명하는 것과 같이 랩 계정 설정을 사용합니다. Azure Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| -------------------- | ----- |
| Marketplace 이미지 | Ubuntu Server 18.04 LTS |
| [피어 가상 네트워크 사용](how-to-connect-peer-virtual-network.md) | 다음과 같은 경우 사용하도록 설정합니다.<ul><li>클래스에는 공유 R 서버가 필요합니다.</li><li>클래스에는 학생 VM이 아닌 외부에 저장하려는 대용량 데이터 파일이 필요합니다.</li></ul> |

> [!IMPORTANT]
> 피어 가상 네트워크를 사용하도록 선택하는 경우 랩을 만들기 전에 이 작업을 수행해야 합니다.

## <a name="lab-configuration"></a>랩 구성

새 랩을 만들고 필요한 설정을 적용하는 방법에 대한 지침은 [자습서: 클래스룸 랩 설정](tutorial-setup-classroom-lab.md)을 참조하세요.  랩을 만들 때 다음 설정을 적용합니다.

| 랩 설정 | 값 및 설명 |
| ------------ | ------------------ |
| Virtual Machine 크기 | 소형 GPU(컴퓨팅)|
| VM 이미지 | Ubuntu Server 18.04 LTS |
| 원격 데스크톱 연결을 사용하도록 설정 | RDP를 사용하기로 선택한 경우 이 설정을 사용하도록 설정해야 합니다.  이 설정은 [X2Go를 선택하여 랩 컴퓨터에 연결](how-to-use-remote-desktop-linux-student.md)하는 경우 필요하지 않습니다.  처음으로 SSH를 사용하여 Linux VM에 연결하고 RDP/X2Go 및 GUI 패키지를 설치해야 합니다.  자세한 내용은 [Linux VM에 대해 그래픽 원격 데스크톱 사용](how-to-enable-remote-desktop-linux.md)을 참조하세요. |

## <a name="external-resource-configuration"></a>외부 리소스 구성

일부 클래스에서는 대용량 데이터 파일과 같은 파일을 외부에 저장해야 합니다.  옵션 및 설정 지침은 [Azure Lab Services에서 외부 파일 스토리지 사용](how-to-attach-external-storage.md)을 참조하세요.

학생을 위한 공유 R 서버를 선택하는 경우 랩이 만들어지기 전에 서버를 설정해야 합니다.  공유 서버를 설정하는 방법에 대한 자세한 내용은 [Azure Lab Services에서 공유 리소스로 랩을 만드는 방법](how-to-create-a-lab-with-shared-resource.md)을 참조하세요.  RStudio Server를 만드는 방법에 대한 지침은 [Debian & Ubuntu에 대한 RStudio Server 다운로드](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/) 및 [RStudio Server 오픈 소스 액세스](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)를 참조하세요.

## <a name="template-configuration"></a>템플릿 구성

템플릿 컴퓨터가 만들어지면 컴퓨터를 시작하고 연결하여 [R](https://docs.rstudio.com/resources/install-r/), [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) 및 선택적으로 [X2Go Server](https://wiki.x2go.org/doku.php/doc:installation:x2goserver)를 설치합니다.  

먼저 apt를 업데이트하고 컴퓨터의 기존 패키지를 업그레이드할 수 있습니다.

```bash
sudo apt update 
sudo apt upgrade
```

### <a name="install-x2go-server"></a>X2Go 서버 설치

X2Go를 사용하기로 선택한 경우 [서버를 설치](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/X2GoRemoteDesktop#install-x2go-server)합니다.  서버 구성 요소를 설치하려면 먼저 [ssh를 사용하여 연결](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh)해야 합니다.  이 작업이 완료되면 [X2Go 클라이언트를 사용하여 연결](how-to-use-remote-desktop-linux-student.md)한 후 나머지 설정을 완료할 수 있습니다.

X2Go의 기본 설치는 RStudio와 호환되지 않습니다.  이 문제를 해결하려면 x2goagent 옵션 파일을 업데이트합니다.

1. `/etc/x2go/x2goagent.options` 파일을 편집합니다.  파일을 sudo로 편집하는 것을 잊지 마세요.
    1. 다음과 같은 줄의 주석 처리를 제거합니다. `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
    1. 다음과 같은 줄을 주석 처리합니다. `X2GO_NXAGENT_DEFAULT_OPTIONS+=" -extension GLX"`
2. 새 옵션이 사용되도록 X2Go 서버를 다시 시작합니다.

    ```bash
    sudo systemctl restart x2goserver
    ```

또는 [X2Go용 Glx Xlib 해결 방법](https://wiki.x2go.org/doku.php/wiki:development:glx-xlib-workaround)의 지침에 따라 필요한 라이브러리를 빌드할 수 있습니다.

### <a name="install-r"></a>R 설치

VM에 R을 설치할 수 있는 몇 가지 방법이 있습니다.  CRAN(광범위한 R 보관 네트워크) 리포지토리에서 R을 설치합니다.  최신 버전의 R을 제공합니다. 이 리포지토리가 컴퓨터에 추가되면 R 및 기타 여러 관련 패키지를 설치할 수 있습니다.

CRAN 리포지토리를 추가해야 합니다. 명령은 [R용 Ubuntu 패키지 간략한 지침](https://cran.rstudio.com/bin/linux/ubuntu/)의 지침을 바탕으로 수정되었습니다.

```bash
#download helper packages
sudo apt install --no-install-recommends software-properties-common dirmngr
# download and add the signing key (by Michael Rutter) for these repos
sudo wget -q "https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc" -O /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
#add repository
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu bionic-cran40/"
```

이제 다음 명령을 실행하여 R을 설치할 수 있습니다.

```bash
sudo apt install r-base
```

### <a name="install-rstudio"></a>RStudio 설치

R이 로컬에 설치되었으므로 RStudio IDE를 설치할 수 있습니다.  RStudio Desktop의 무료 버전을 설치하겠습니다.  사용 가능한 모든 버전은 [RStudio 다운로드](https://www.rstudio.com/products/rstudio/download/)를 참조하세요.

1. RStudio용 [코드 서명 키를 가져옵니다](https://www.rstudio.com/code-signing/).

    ```bash
    sudo gpg --keyserver keyserver.ubuntu.com  --recv-keys 3F32EE77E331692F
    ```

2. Ubuntu용 [R Studio용 Debian Linux 패키지 파일(.deb)](https://www.rstudio.com/products/rstudio/download/#download)을 다운로드합니다.  파일은 `rstudio-{version}-amd64.deb` 형식이 됩니다.  예를 들면 다음과 같습니다.

    ```bash
    export rstudiover="1.4.1717"
    wget --quiet -O rstudio.deb https://download1.rstudio.org/desktop/bionic/amd64/rstudio-$rstudiover-amd64.deb
    ```

3. gdebi를 사용하여 RStudio를 설치합니다.   파일 경로를 사용하여 로컬 파일을 설치하고 있는 apt를 나타냅니다.

    ```bash
    sudo apt install gdebi-core 
    echo "y" | gdebi rstudio.deb –quiet
    ```

### <a name="cran-packages"></a>CRAN 패키지

이제 원하는 [CRAN 패키지](https://cloud.r-project.org/web/packages/available_packages_by_name.html)를 설치할 차례입니다.  먼저 [현재 R 4.0 이상 c2d4u 리포지토리](https://cran.rstudio.com/bin/linux/ubuntu/#get-5000-cran-packages)를 추가합니다.

```bash
sudo add-apt-repository ppa:c2d4u.team/c2d4u4.0+
```

[유용한 R 패키지의 빠른 목록](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages) 문서에 표시된 대로 R 대화형 세션에서 `install.packages(“package name”)` 명령을 사용합니다.  또는 도구 -> RStudio의 패키지 메뉴 항목 설치를 활용합니다.

패키지를 찾는 데 도움이 필요하면 [작업별 패키지 목록](https://cran.r-project.org/web/views/) 또는 [알파벳순 패키지 목록](https://cloud.r-project.org/web/packages/available_packages_by_name.html)을 참조하세요.

## <a name="cost"></a>Cost

이 수업의 예상 비용 예시를 살펴보겠습니다.  25명의 학생으로 구성된 클래스가 있다고 가정합니다. 각 학생에게는 20시간 일정의 클래스 시간이 있습니다.  학생에게는 일정에 있는 클래스 시간 외에도 과제를 위해 추가적으로 10시간이 할당됩니다.  선택한 가상 머신 크기는 **소형 GPU(컴퓨팅)** 로, 랩 유닛 139개입니다.

학생 25명 &times;(예약 시간 20시간 + 할당 시간 10시간) &times; 랩 단위 139개 &times; 시간당 0.01 USD = 1042.5 USD

> [!IMPORTANT]
> 예상 비용은 예제 용도로만 사용됩니다.  최신 가격 책정 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 템플릿 이미지를 랩에 게시할 수 있습니다. 추가 지침은 [템플릿 VM 게시](how-to-create-manage-template.md#publish-the-template-vm)를 참조하세요.

랩을 설정할 때 다음 문서를 참조하세요.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)