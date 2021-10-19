---
title: Azure Lab Services를 사용하여 빅 데이터 분석을 교육하도록 랩 설정 | Microsoft Docs
description: HDP(Hortonworks Data Platform)의 Docker 배포를 사용하여 빅 데이터 분석을 교육하도록 랩을 설정하는 방법을 알아봅니다.
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 280ed6f2804e7a3aef6da8da9512a59da6e8c5f4
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177045"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>HortonWorks Data Platform의 Docker 배포를 사용하여 빅 데이터 분석에 대한 랩 설정

이 문서에서는 빅 데이터 분석 클래스를 교육하기 위한 랩을 설정하는 방법을 보여 줍니다.  이러한 종류의 수업에서 학생들은 대량의 데이터를 처리하고, 머신 및 통계적 학습 알고리즘을 적용하여 데이터 인사이트를 도출하는 방법을 알아봅니다.  학생의 핵심 목표는 빅 데이터를 저장, 관리 및 처리하기 위한 도구를 제공하는 [Apache Hadoop의 오픈 소스 소프트웨어 패키지](https://hadoop.apache.org/)와 같은 데이터 분석 도구를 사용하는 방법을 배우는 것입니다.

이 랩에서 학생은 [HDP(Hortonworks Data Platform)](https://www.cloudera.com/products/hdp.html)라는, [Cloudera](https://www.cloudera.com/)에서 제공하는 인기 있는 상업용 버전의 Hadoop을 사용합니다.  특히 학생은 학습과 실험을 위해 무료로 제공되는 간단하고 사용하기 쉬운 버전의 플랫폼인 [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html)을 사용합니다.  이 클래스는 HDP Sandbox가 배포된 Windows 또는 Linux VM(가상 머신)을 사용할 수 있지만 이 문서에서는 Windows를 사용하는 방법을 보여 줍니다.

이 랩의 또 다른 흥미로운 측면은 [Docker](https://www.docker.com/) 컨테이너를 사용하여 랩 VM에 HDP Sandbox를 배포한다는 것입니다.  각 Docker 컨테이너는 내부에서 실행할 소프트웨어 애플리케이션의 자체 격리된 환경을 제공합니다.  개념적으로 Docker 컨테이너는 중첩된 VM과 비슷하며 [Docker Hub](https://www.docker.com/products/docker-hub)에서 제공하는 컨테이너 이미지에 따라 매우 다양한 소프트웨어 애플리케이션을 쉽게 배포하고 실행하는 데 사용될 수 있습니다.  HDP Sandbox의 Cloudera 배포 스크립트는 자동으로 Docker Hub에서 [HDP Sandbox 3.0.1 Docker 이미지](https://hub.docker.com/r/hortonworks/sandbox-hdp)를 끌어오고 두 개의 Docker 컨테이너를 실행합니다.
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 관한 자세한 내용은 [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)를 참조하세요.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용합니다. Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용하도록 설정합니다.|

### <a name="lab-settings"></a>랩 설정

클래스룸 랩을 설정하는 경우 아래 표의 설정을 사용합니다.  클래스룸 랩을 만드는 방법에 대한 자세한 내용은 [클래스룸 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 중간(중첩된 가상화) 이 VM 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다.  이 크기는 중첩된 가상화도 지원합니다.|  
|가상 머신 이미지| Windows 10 Pro|

> [!NOTE] 
> Docker를 사용하여 HDP Sandbox를 배포하려면 다음이 필요하므로 중간(중첩된 가상화)을 사용해야 합니다.
>   - 중첩된 가상화를 사용하는 Windows Hyper-V
>   - 최소 10GB RAM

## <a name="template-machine-configuration"></a>템플릿 머신 구성

템플릿 머신을 설정하려면 다음을 수행합니다.
- Docker 설치
- HDP Sandbox 배포
- PowerShell 및 Windows 작업 스케줄러를 사용하여 자동으로 Docker 컨테이너 시작

### <a name="install-docker"></a>Docker 설치

이 섹션의 단계는 [Docker 컨테이너를 사용하여 배포하기 위한 Cloudera 지침](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)을 기반으로 합니다. 

Docker 컨테이너를 사용하려면 먼저 템플릿 VM에 Docker Desktop을 설치해야 합니다.

1. [필수 조건 섹션](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)의 단계에 따라 [Windows용 Docker](https://docs.docker.com/docker-for-windows/install/)를 설치합니다. 

    > [!IMPORTANT] 
    > **Linux 컨테이너 대신 Windows 컨테이너 사용** 구성 옵션이 선택 취소되어 있는지 확인합니다.

1. **Windows 컨테이너 및 Hyper-V 기능** 이 켜져 있는지 확인합니다.
   ![Windows 기능 켜기 또는 끄기](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. [Windows용 메모리](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) 섹션의 단계에 따라 Docker 메모리 구성을 구성합니다.

    > [!WARNING]
    > Docker를 설치할 때 실수로 **Linux 컨테이너 대신 Windows 컨테이너 사용** 옵션을 선택하면 메모리 구성 설정이 표시되지 않습니다.  이 문제를 해결하기 위해 [Windows 시스템 트레이에서 Docker 아이콘을 클릭](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)하여 Linux 컨테이너 사용으로 전환할 수 있습니다. Docker Desktop 메뉴가 열리면 **Linux 컨테이너로 전환** 을 선택합니다.
 
### <a name="deploy-hdp-sandbox"></a>HDP Sandbox 배포

이 섹션에서는 HDP Sandbox를 배포한 다음, 브라우저를 사용하여 HDP Sandbox에 액세스합니다.

1. 다음 단계를 완료하기 위해 가이드의 [필수 조건 섹션](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites)에 나열된 대로 [Git Bash](https://gitforwindows.org/)를 설치했는지 확인하는 것이 좋습니다.

1. [Docker에 대한 Cloudera 배포 및 설치 가이드](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)를 사용하여 다음 섹션의 단계를 완료합니다.
   
   -    HDP Sandbox 배포
   -    HDP Sandbox 확인

    > [!WARNING] 
    > HDP에 대한 최신 .zip 파일을 다운로드하는 경우 공백이 포함된 디렉터리 경로에 .zip 파일을 저장하지 ‘않아야’ 합니다.

    > [!NOTE] 
    > 배포하는 동안 **드라이브가 공유되지 않았음** 이라는 예외가 표시되면 HDP의 Linux 컨테이너가 로컬 Windows 파일에 액세스할 수 있도록 Docker를 사용하여 C 드라이브를 공유해야 합니다.  이 문제를 해결하려면 [Windows 시스템 트레이에서 Docker 아이콘을 클릭](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)하여 Docker Desktop 메뉴를 열고 **설정** 을 선택합니다.  **Docker 설정** 대화 상자가 열리면 **리소스 > 파일 공유** 를 선택하고 **C** 드라이브를 선택합니다.  그런 다음, HDP Sandbox를 배포하는 단계를 반복할 수 있습니다.

1. HDP Sandbox의 Docker 컨테이너가 배포되고 실행되면 브라우저를 시작하고, Cloudera 지침에 따라 [Sandbox 시작 페이지](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page)를 열고, HDP 대시보드를 시작하여 환경에 액세스할 수 있습니다.

    > [!NOTE] 
    > 이 지침에서는 먼저 샌드박스 환경의 로컬 IP 주소를 템플릿 VM의 호스트 파일에 있는 sandbox-hdp.hortonworks.com에 매핑한 것으로 가정합니다.  이 매핑을 수행하지 **않은** 경우에는 `http://localhost:8080`으로 이동하여 Sandbox 시작 페이지에 액세스할 수 있습니다.

### <a name="automatically-start-docker-containers-when-students-log-in"></a>학생이 로그인할 때 자동으로 Docker 컨테이너 시작

학생에게 사용하기 쉬운 환경을 제공하기 위해 사용하는 PowerShell 스크립트는 자동으로 다음을 수행합니다.
  - 학생이 랩 VM을 시작하고 여기에 연결할 때 HDP Sandbox Docker 컨테이너를 시작합니다.
  - 브라우저를 시작하고 Sandbox 시작 페이지로 이동합니다.
또한 학생이 VM에 로그인할 때 Windows 작업 스케줄러를 사용하여 이 스크립트를 자동으로 실행합니다.
이를 설정하려면 [빅 데이터 분석 스크립팅](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/) 단계를 따릅니다.

## <a name="cost-estimate"></a>예상 비용

이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다.

학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은
  - 학생 25명 * (20 + 10)시간 * 55 랩 단위 * 0.01 USD/시간 = 412.50 USD

가격 책정에 대한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론

이 문서에서는 Docker를 사용하여 배포된 Hortonworks Data Platform을 사용하는 빅 데이터 분석 클래스의 랩을 만드는 데 필요한 단계를 안내했습니다.  이 클래스 형식의 설정은 비슷한 데이터 분석 클래스에 사용될 수 있습니다.  이 설정은 배포에 Docker를 사용하는 다른 형식의 클래스에도 적용될 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)