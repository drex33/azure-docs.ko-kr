---
title: 자습서 - Azure Linux 가상 머신에 대한 롤링 배포 구성
description: 이 자습서에서는 CD(지속적인 배포) 파이프라인을 설정하는 방법을 알아봅니다. 이 파이프라인은 롤링 배포 전략을 사용하여 Azure Linux 가상 머신 그룹을 점진적으로 업데이트합니다.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: 5212357fe123d722b7dbb853c874190a3ef9dee0
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697724"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>자습서 - Azure Linux 가상 머신에 대한 롤링 배포 전략 구성

**적용 대상:** :heavy_check_mark: Linux VM

Azure DevOps는 모든 Azure 리소스에 대한 DevOps 프로세스의 각 부분을 자동화하는 기본 제공 Azure 서비스입니다. 앱에서 가상 머신, 웹앱, Kubernetes 또는 기타 리소스를 사용하는지 여부에 관계없이 Azure와 Azure DevOps를 사용하여 IaC(Infrastructure as Code), 연속 통합, 지속적인 테스트, 지속적인 업데이트 및 지속적인 모니터링을 구현할 수 있습니다.

![[서비스]에서 Azure DevOps를 선택한 Azure Portal](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS(Infrastructure as a Service) - CI/CD 구성

Azure Pipelines는 가상 머신에 배포하는 데 사용할 수 있는 완전한 기능을 갖춘 CI/CD 자동화 도구 세트를 제공합니다. Azure Portal에서 Azure VM에 대한 지속적인 배달 파이프라인을 구성할 수 있습니다.

이 문서에서는 Azure Portal에서 다중 머신 배포를 롤링하는 CI/CD 파이프라인을 설정하는 방법을 보여줍니다. Azure Portal에서는 [카나리아](./tutorial-azure-devops-canary-strategy.md), [파란색-녹색](./tutorial-azure-devops-blue-green-strategy.md) 등의 다른 전략도 지원합니다.

### <a name="configure-cicd-on-virtual-machines"></a>가상 머신에서 CI/CD 구성

가상 머신을 [배포 그룹](/azure/devops/pipelines/release/deployment-groups)에 대상으로 추가할 수 있습니다. 그런 다음, 다중 머신 업데이트의 대상으로 지정할 수 있습니다. 머신에 배포한 후, 배포 그룹 내에서 **배포 기록** 을 확인합니다. 이 보기를 사용하면 VM에서 파이프라인을 지나 커밋까지 추적할 수 있습니다.

### <a name="rolling-deployments"></a>롤링 배포

각 반복에서 롤링 배포는 애플리케이션의 이전 버전 인스턴스를 대체합니다. 고정된 머신 세트(롤링 세트)에서 이전 버전 인스턴스를 새 버전의 인스턴스로 대체합니다. 다음 연습에서는 가상 머신에 대한 롤링 업데이트를 구성하는 방법을 보여줍니다.

지속적인 업데이트 옵션을 사용하여 Azure Portal 내에서 가상 머신에 대한 롤링 업데이트를 구성할 수 있습니다. 단계별 연습은 다음과 같습니다.

1. Azure Portal에 로그인하고, 가상 머신으로 이동합니다.
1. VM 설정의 맨 왼쪽 창에서 **지속적인 업데이트** 를 선택합니다. 그런 다음, **구성** 을 선택합니다.

   ![[구성] 단추가 있는 [지속적인 업데이트] 창](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 구성 패널에서 **Azure DevOps 조직** 을 선택하여 기존 계정을 선택하거나 새 계정을 만듭니다. 그런 다음, 파이프라인을 구성하려는 프로젝트를 선택합니다.  

   ![[지속적인 업데이트] 패널](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 배포 그룹은 물리적 환경을 나타내는 배포 대상 머신의 논리적 세트입니다. 예를 들어 Dev, Test, UAT 및 Production입니다. 새 배포 그룹을 만들거나 기존 배포 그룹을 선택할 수 있습니다.
1. 가상 머신에 배포할 패키지를 게시하는 빌드 파이프라인을 선택합니다. 게시된 패키지에는 패키지 루트 폴더의 deployscripts 폴더에 deploy.ps1 또는 deploy.sh라는 배포 스크립트가 있어야 합니다. 파이프라인은 이 배포 스크립트를 실행합니다.
1. **배포 전략** 에서 **롤링** 을 선택합니다.
1. 필요에 따라 각 머신에 역할을 태그로 지정할 수 있습니다. 예를 들어 "웹"이나 "db"를 태그로 지정할 수 있습니다. 이러한 태그는 특정 역할이 있는 VM만을 대상으로 지정할 수 있습니다.
1. **확인** 을 선택하여 지속적인 업데이트 파이프라인을 구성합니다.
1. 구성을 마치면 가상 머신에 배포하도록 지속적인 업데이트 파이프라인이 구성됩니다.  

   ![배포 기록을 보여주는 [지속적인 업데이트] 패널](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. 가상 머신에 대한 배포 세부 정보가 표시됩니다. **Release-1** 파이프라인으로 이동하는 이 링크를 선택하여 배포를 살펴볼 수도 있고, **편집** 을 선택하여 릴리스 파이프라인 정의를 수정할 수도 있습니다.

1. 여러 VM을 구성하는 경우 다른 VM에 대해 2~4단계를 반복하여 배포 그룹에 추가합니다. 이미 파이프라인이 실행되는 배포 그룹을 선택하면 VM이 배포 그룹에 바로 추가됩니다. 새 파이프라인이 생성되지 않습니다.
1. 구성이 완료되면 파이프라인 정의를 선택하고 Azure DevOps 조직으로 이동한 다음, 릴리스 파이프라인에 대한 **편집** 을 클릭합니다.

   ![롤링 파이프라인 편집](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. **개발** 스테이지에서 **1작업, 1태스크** 를 선택합니다. **배포** 단계를 선택합니다.

   ![배포 태스크가 선택된 롤링 파이프라인 태스크](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. 맨 오른쪽의 구성 창에서 반복마다 병렬로 배포할 머신 수를 지정할 수 있습니다. 한 번에 여러 머신에 배포하려면 슬라이더를 사용하여 백분율로 지정하면 됩니다.  

1. 배포 스크립트 실행 태스크는 기본적으로 deploy.ps1 또는 deploy.sh 배포 스크립트를 실행합니다. 이 스크립트는 게시된 패키지의 루트 폴더에 있는 deployscripts 폴더에 있습니다.

   ![deployscripts 폴더의 deploy.sh를 표시하는 [아티팩트] 창](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>기타 배포 전략

- [카나리아 배포 전략 구성](./tutorial-azure-devops-canary-strategy.md)
- [파란색-녹색 배포 전략 구성](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Azure를 더 쉽게 시작할 수 있습니다. Azure DevOps Projects를 사용하면 다음 항목을 선택하여 세 단계만으로 모든 Azure 서비스에서 애플리케이션 실행을 시작할 수 있습니다.

- 애플리케이션 언어
- 런타임
- Azure 서비스
 
[자세히 알아보기](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>추가 리소스

- [Azure DevOps Projects를 사용하여 Azure 가상 머신에 배포](../../devops-project/azure-devops-project-vms.md)
- [Azure 가상 머신 확장 집합에 대한 앱의 지속적인 배포 구현](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)