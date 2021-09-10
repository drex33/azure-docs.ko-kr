---
title: Azure Linux Virtual Machines에서 카나리아 배포 구성
description: CD(지속적인 배포) 파이프라인을 설정하는 방법을 알아봅니다. 이 파이프라인은 카나리아 배포 전략을 사용하여 Azure Linux 가상 머신 그룹을 업데이트합니다.
author: moala
tags: azure-devops-pipelines
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: f9ab4712e6419303f010d7cf1a417a8a907f9a05
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690951"
---
# <a name="configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Azure Linux Virtual Machines에서 카나리아 배포 전략 구성

**적용 대상:** :heavy_check_mark: Linux VM 

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>IaaS(Infrastructure as a Service) - CI/CD 구성

Azure Pipelines는 가상 머신에 배포하는 데 사용할 수 있는 완전한 기능을 갖춘 CI/CD 자동화 도구 세트를 제공합니다. Azure Portal에서 Azure VM에 대한 지속적인 배달 파이프라인을 구성할 수 있습니다.

이 문서에서는 카나리아 전략을 다중 머신 배포에 사용하는 CI/CD 파이프라인을 설정하는 방법을 보여 줍니다. Azure Portal에서는 [롤링](./tutorial-devops-azure-pipelines-classic.md) 및 [청록색](./tutorial-azure-devops-blue-green-strategy.md)과 같은 다른 전략도 지원합니다.

### <a name="configure-cicd-on-virtual-machines"></a>가상 머신에서 CI/CD 구성

가상 머신을 [배포 그룹](/azure/devops/pipelines/release/deployment-groups)에 대상으로 추가할 수 있습니다. 그런 다음, 다중 머신 업데이트의 대상으로 지정할 수 있습니다. 머신에 배포한 후, 배포 그룹 내에서 **배포 기록** 을 확인합니다. 이 보기를 사용하면 VM에서 파이프라인으로 추적한 다음, 커밋으로 추적할 수 있습니다.

### <a name="canary-deployments"></a>카나리아 배포

카나리아 배포는 소규모 사용자 하위 집합에 대한 변경 내용을 천천히 롤아웃하여 위험을 줄입니다. 새 버전에 자신감이 생기면 인프라의 더 많은 서버로 릴리스하고 더 많은 사용자를 해당 버전으로 라우팅할 수 있습니다.

지속적인 배달 옵션을 사용하면 Azure Portal에서 가상 머신에 대한 카나리아 배포를 구성할 수 있습니다. 단계별 연습은 다음과 같습니다.

1. Azure Portal에 로그인하고, 가상 머신으로 이동합니다.
1. VM 설정의 맨 왼쪽 창에서 **지속적인 업데이트** 를 선택합니다. 그런 다음, **구성** 을 선택합니다.

   ![[구성] 단추가 있는 [지속적인 업데이트] 창](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. 구성 패널에서 **Azure DevOps 조직** 을 선택하여 기존 계정을 선택하거나 새 계정을 만듭니다. 그런 다음, 파이프라인을 구성하려는 프로젝트를 선택합니다.  

   ![[지속적인 업데이트] 패널](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. 배포 그룹은 물리적 환경을 나타내는 배포 대상 머신의 논리적 세트입니다. 예를 들어 Dev, Test, UAT 및 Production입니다. 새 배포 그룹을 만들거나 기존 배포 그룹을 선택할 수 있습니다.
1. 가상 머신에 배포할 패키지를 게시하는 빌드 파이프라인을 선택합니다. 게시된 패키지에는 패키지 루트 폴더의 deployscripts 폴더에 deploy.ps1 또는 deploy.sh라는 배포 스크립트가 있어야 합니다. 파이프라인은 이 배포 스크립트를 실행합니다.
1. **배포 전략** 에서 **카나리아** 를 선택합니다.
1. “카나리아” 태그를 카나리아 배포의 일부가 될 VM에 추가합니다. 카나리아 배포가 성공한 후에 생성된 배포의 일부인 VM에 “prod” 태그를 추가합니다. 태그는 특정 역할이 있는 VM만을 대상으로 지정할 수 있습니다.

   ![배포 전략 값이 카나리아로 선택된 지속적인 배달 패널](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. **확인** 을 선택하여 가상 머신에 배포할 지속적인 배달 파이프라인을 구성합니다.

   ![카나리아 파이프라인](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. 가상 머신에 대한 배포 세부 정보가 표시됩니다. Azure DevOps의 릴리스 파이프라인으로 이동하는 링크를 선택할 수 있습니다. 릴리스 파이프라인에서 **편집** 을 선택하여 파이프라인 구성을 확인합니다. 파이프라인에는 다음 세 가지 단계가 있습니다.

   1. 이 단계는 배포 그룹 단계입니다. 애플리케이션은 “카나리아”로 태그가 지정된 VM에 배포됩니다.
   1. 이 단계에서는 파이프라인이 일시 중지되고 수동 개입을 통해 실행이 다시 시작될 때까지 대기합니다.
   1. 이는 다시 배포 그룹 단계입니다. 이제 업데이트는 “prod”로 태그가 지정된 VM에 배포됩니다.

      ![카나리아 배포 작업에 대한 배포 그룹 창](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. 파이프라인 실행을 다시 시작하기 전에 하나 이상의 VM에 “prod”로 태그가 지정되어 있는지 확인합니다. 파이프라인의 세 번째 단계에서는 “prod” 태그가 있는 VM에만 애플리케이션이 배포됩니다.

1. 실행 배포 스크립트 작업은 기본적으로 deploy.ps1 또는 deploy.sh 배포 스크립트를 실행합니다. 스크립트는 게시된 패키지의 루트 폴더에 있는 deployscripts 폴더에 있습니다. 선택한 빌드 파이프라인에서 배포를 패키지의 루트 폴더에 게시하는지 확인합니다.

   ![deployscripts 폴더의 deploy.sh를 보여 주는 아티팩트 창](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>기타 배포 전략
- [롤링 배포 전략 구성](./tutorial-devops-azure-pipelines-classic.md)
- [청록색 배포 전략 구성](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

Azure를 더 쉽게 시작할 수 있습니다. Azure DevOps Projects를 사용하면 다음 항목을 선택하여 세 단계만으로 모든 Azure 서비스에서 애플리케이션 실행을 시작할 수 있습니다.

- 애플리케이션 언어
- 런타임
- Azure 서비스

[자세히 알아보기](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>추가 리소스

- [Azure DevOps Projects를 사용하여 Azure 가상 머신에 배포](../../devops-project/azure-devops-project-vms.md)
- [Azure 가상 머신 확장 집합에 대한 앱의 지속적인 배포 구현](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)