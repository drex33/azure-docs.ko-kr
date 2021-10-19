---
title: Azure Lab Services로 Linux 셸 스크립팅 랩 설정 | Microsoft Docs
description: Linux에서 셸 스크립팅을 학습하도록 랩을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0faf5c9a11ad4aed3bf1157bc18dbeb84f60030b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176551"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>Linux에서 셸 스크립팅을 학습하도록 랩 설정
이 문서에서는 Linux에서 셸 스크립팅을 학습하도록 랩을 설정하는 방법을 알아봅니다. 스크립팅은 관리자가 반복적인 작업을 방지할 수 있도록 하는 시스템 관리의 유용한 부분입니다. 이 샘플 시나리오에서 클래스는 기존 bash 스크립트와 향상된 스크립트를 포함합니다. 향상된 스크립트는 bash 명령과 Ruby를 결합하는 스크립트입니다. 이 접근 방식을 사용하면 Ruby에서 데이터를 전달하고, bash 명령이 셸과 상호 작용할 수 있습니다. 

이러한 스크립팅 클래스를 수강하는 학생은 Linux 가상 머신을 통해 Linux의 기본 사항을 알아보고 bash 셸 스크립팅에 익숙해질 수 있습니다. Linux 가상 머신은 원격 데스크톱 액세스를 사용하도록 설정되어 있고 [gedit](https://help.gnome.org/users/gedit/stable/) 및 [Visual Studio Code](https://code.visualstudio.com/) 텍스트 편집기가 설치 되어 있습니다.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들거나 기존 랩 계정을 사용할 수 있습니다. 새 랩 계정 만들기에 대한 다음 자습서를 참조하세요. [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)

랩 계정을 만든 후 랩 계정에서 다음 설정을 사용하도록 설정합니다. 

| 랩 계정 설정 | Instructions |
| ----------- | ------------ |  
| Marketplace 이미지 | 랩 계정 내에서 사용할 Ubuntu Server 18.04 LTS 이미지를 사용하도록 설정합니다. 자세한 내용은 [랩 작성자에 사용할 수 있는 Marketplace 이미지 지정](specify-marketplace-images.md)을 참조하세요. | 

[이 자습서](tutorial-setup-classroom-lab.md)에 따라 새 랩을 만들고 다음 설정을 적용합니다.

| 랩 설정 | 값/지침 | 
| ------------ | ------------------ |
| VM(가상 머신) 크기 | 작음  |
| VM 이미지 | Ubuntu Server 18.04 LTS|
| 원격 데스크톱 연결을 사용하도록 설정 | 사용하도록 설정합니다. <p>이 설정을 사용하도록 설정하면 교사와 학생이 RDP(원격 데스크톱)를 사용하여 해당 VM에 연결할 수 있습니다. 자세한 내용은 [Azure Lab Services의 랩에서 Linux 가상 머신을 위한 원격 데스크톱을 사용하도록 설정](how-to-enable-remote-desktop-linux.md)을 참조하세요. </p>|

## <a name="install-desktop-and-rdp"></a>데스크톱 및 RDP 설치
Ubuntu Server 18.04 LTS 이미지에는 기본적으로 RDP 원격 데스크톱 서버가 설치되어 있지 않습니다. [Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결](../virtual-machines/linux/use-remote-desktop.md) 문서의 지침에 따라 템플릿 머신에서 RDP(원격 데스크톱 프로토콜)를 통해 연결하는 데 필요한 패키지를 설치합니다.

## <a name="install-ruby"></a>Ruby 설치
Ruby는 bash 스크립트와 함께 사용할 수 있는 오픈 소스 동적 언어입니다. 이 섹션에서는 `apt-get`을 사용하여 [Ruby](https://www.ruby-lang.org/)의 최신 버전을 설치하는 방법을 보여 줍니다.

1. 다음 명령을 실행하여 업데이트를 설치합니다.

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  [Ruby](https://www.ruby-lang.org/)를 설치합니다.  Ruby는 bash 스크립트와 함께 사용할 수 있는 오픈 소스 동적 언어입니다. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>개발 도구 설치
이 섹션에서는 몇 가지 텍스트 편집기를 설치하는 방법을 보여 줍니다. Gedit은 Gnome 데스크톱 환경의 기본 텍스트 편집기입니다. 범용 텍스트 편집기로 설계되었습니다. Visual Studio Code는 디버깅 및 소스 제어 통합에 대한 지원을 포함하는 텍스트 편집기입니다.

> [!NOTE]
> 여러 가지 텍스트 편집기를 사용할 수 있습니다. Visual Studio Code와 Gedit은 두 가지 예제일 뿐입니다.

1. [Gedit](https://help.gnome.org/users/gedit/stable/)을 설치합니다.

    ```bash
    sudo apt-get install gedit
    ```
1. [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.  Visual Studio Code는 Snap Store를 사용하여 설치할 수 있습니다.  대체 설치 옵션은 [Visual Studio Code 대체 다운로드](https://code.visualstudio.com/#alt-downloads)를 참조하세요.

    ```bash
    sudo snap install vscode --classic 
    ```

    이제 템플릿이 업데이트되며 랩을 완료하는 데 필요한 프로그래밍 언어 및 개발 도구가 모두 있습니다. 이제 템플릿 이미지를 랩에 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 랩에 템플릿을 게시합니다.  

## <a name="cost"></a>비용 
이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다.
 
학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은 

학생 25명 * (20+10)시간 * 20 랩 단위 * 0.01USD/시간 = 150USD입니다.

가격 책정에 대한 자세한 내용은 다음 문서에서 확인할 수 있습니다. [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>결론
이 문서에서는 스크립팅 클래스에 대한 랩을 만드는 단계를 안내합니다. 이 문서에서는 Linux 컴퓨터에서 Ruby 스크립팅 도구를 설정하는 데 중점을 두었습니다. Linux에서 Python과 같은 다른 스크립팅 클래스에도 동일한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users) 





