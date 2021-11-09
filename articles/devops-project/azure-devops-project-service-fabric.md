---
title: '자습서: Azure DevOps Starter를 사용하여 Azure Service Fabric에 ASP.NET Core 앱 배포'
description: Azure DevOps Starter를 사용하면 Azure를 쉽게 시작할 수 있습니다. DevOps Projects를 사용하면 몇 가지 빠른 단계로 ASP.NET Core 앱을 Azure Service Fabric으로 쉽게 배포할 수 있습니다.
ms.author: gwallace
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.openlocfilehash: fc2a86e9bf8105851f4fb9e5cfe444cd44f850d5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061554"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>자습서: Azure DevOps Starter를 사용하여 Azure Service Fabric에 ASP.NET Core 앱 배포

Azure DevOps Starter는 기존 코드와 Git 리포지토리를 가져오거나 샘플 애플리케이션을 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만드는 간소화된 환경을 제공합니다. 

DevOps Starter는 다음 작업도 수행합니다.

* Azure Service Fabric과 같은 Azure 리소스를 자동으로 만듭니다.
* Azure DevOps에서 CI/CD 파이프라인을 설정하는 릴리스 파이프라인을 만들고 구성합니다.
* 모니터링을 위해 Azure Application Insights 리소스를 만듭니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * DevOps Starter를 사용하여 ASP.NET Core 앱을 만든 후 Service Fabric에 배포
> * Azure DevOps 및 Azure 구독 구성 
> * CI 파이프라인 검토
> * CD 파이프라인 검토
> * Git에 변경 내용 커밋 및 자동으로 Azure에 배포
> * 리소스 정리

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>DevOps Starter를 사용하여 ASP.NET Core 앱을 만든 후 Service Fabric에 배포

DevOps Starter는 Azure Pipelines에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. DevOps Starter는 선택한 Azure 구독에서 Service Fabric 클러스터 같은 Azure 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 상자에 **DevOps Starter** 를 입력한 다음, 선택합니다. **추가** 를 클릭하여 새 항목을 만듭니다.

    ![DevOps Starter 대시보드](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. **.NET** 을 선택하고 **다음** 을 선택합니다.

1. **애플리케이션 프레임워크 선택** 의 경우 **ASP.NET Core** 및 **다음** 을 차례로 선택합니다.

1. **Service Fabric 클러스터** 를 선택한 후 **다음** 을 선택합니다. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps 및 Azure 구독 구성

1. 새 Azure DevOps 조직을 만들거나 기존 조직을 선택합니다. 

1. Azure DevOps 프로젝트의 이름을 입력합니다. 

1. Azure 구독을 선택합니다.

1. 추가 Azure 구성 설정을 보고, Service Fabric 클러스터에 대한 노드 가상 머신 크기 및 운영 체제를 식별하려면 **변경** 을 선택합니다. 이 창에는 Azure 서비스의 유형 및 위치를 구성하기 위한 다양한 옵션이 표시됩니다.
 
1. Azure 구성 영역을 종료하고 **완료** 를 선택합니다.  
    몇 분 후 프로세스가 완료됩니다. 샘플 ASP.NET Core 앱이 Azure DevOps Services 조직의 Git 리포지토리에서 설정되고 Service Fabric 클러스터가 만들어지고, CI/CD 파이프라인이 실행되고, 앱이 Azure에 배포됩니다. 

    모든 작업이 완료된 후 DevOps Starter 대시보드가 Azure Portal에 표시됩니다. Azure Portal의 **모든 리소스** 에서 직접 DevOps Starter 대시보드로 이동할 수도 있습니다. 

    이 대시보드에서는 Azure DevOps 코드 리포지토리, CI/CD 파이프라인 및 Service Fabric 클러스터에 가시성을 제공합니다. Azure 리포지토리에서 CI/CD 파이프라인에 대한 추가 옵션을 구성할 수 있습니다. 오른쪽에서 **찾아보기** 를 선택하여 실행 중인 앱을 확인합니다.

## <a name="examine-the-ci-pipeline"></a>CI 파이프라인 검토

DevOps Starter는 Azure Pipelines에서 CI/CD 파이프라인을 자동으로 구성합니다. 파이프라인을 탐색하고 사용자 지정할 수 있습니다. 이 작업에 익숙해지려면 다음을 수행합니다.

1. DevOps Starter 대시보드로 이동합니다.

1. DevOps Starter 대시보드 맨 위에서 **빌드 파이프라인** 을 선택합니다. 브라우저 탭에 새 프로젝트에 대한 빌드 파이프라인이 표시됩니다.

1. **상태** 필드를 가리킨 후 줄임표(...)를 선택합니다. 메뉴에 새 빌드 쿼리, 빌드 일시 중지 및 빌드 파이프라인 편집과 같은 몇 가지 옵션이 표시됩니다.

1. **편집** 을 선택합니다.

1. 이 창에서 빌드 파이프라인의 다양한 작업을 검사할 수 있습니다. 빌드는 Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 수행합니다.

1. 빌드 파이프라인의 맨 위에서 빌드 파이프라인 이름을 선택합니다. 

1. 빌드 파이프라인 이름에서 **기록** 을 선택합니다. 이 창에 대한 최근 변경 내용의 감사 내역이 표시됩니다. Azure DevOps는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거** 를 선택합니다. DevOps Starter는 CI 트리거를 자동으로 생성하며, 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다. 필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존** 을 선택합니다. 시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

## <a name="examine-the-cd-pipeline"></a>CD 파이프라인 검토

DevOps Starter는 Azure DevOps 조직에서 Azure 구독에 배포하는 데 필요한 단계를 자동으로 만들고 구성합니다. 이러한 단계에는 Azure 구독에 Azure DevOps를 인증하기 위한 Azure 서비스 연결 구성이 포함됩니다. 또한 자동화 기능은 Azure에 CD를 제공하는 릴리스 파이프라인도 만듭니다. 릴리스 파이프라인에 대한 자세한 내용을 보려면 다음을 수행하세요.

1. **빌드 및 릴리스** 를 선택한 다음, **릴리스** 를 선택합니다. DevOps Starter는 Azure에 대한 배포를 관리하는 릴리스 파이프라인을 만듭니다.

1. 릴리스 파이프라인 옆의 줄임표(...)를 선택하고 **편집** 을 선택합니다. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 *파이프라인* 이 포함됩니다.

1. **아티팩트** 아래에서 **드롭** 을 선택합니다. 이전에 검사한 빌드 파이프라인이 아티팩트에 사용된 출력을 생성합니다. 

1. **드롭** 아이콘의 오른쪽에서 **지속적인 배포 트리거** 를 선택합니다. 이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 CD 트리거를 사용하도록 설정했습니다. 필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다. 

1. 오른쪽에서 **릴리스 보기** 를 선택하여 릴리스의 기록을 표시합니다.

1. 릴리스 옆에 있는 줄임표(...)를 선택하고 **열기** 를 선택합니다. 릴리스 요약, 연결된 작업 항목 및 테스트 등 몇 가지 메뉴를 확인할 수 있습니다.

1. **커밋** 을 선택합니다. 이 보기에는 이 배포와 연결된 코드 커밋이 표시됩니다. 배포 간 커밋 차이를 보기 위해 릴리스를 비교합니다.

1. **로그** 를 선택합니다. 로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다. 배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Git에 변경 내용 커밋 및 자동으로 Azure에 배포 

 > [!NOTE]
 > 다음 절차에서는 텍스트를 간단히 변경하여 CI/CD 파이프라인을 테스트합니다.

이제 웹 사이트에 최신 작업을 자동으로 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다. Git 리포지토리를 변경할 때마다 빌드가 시작되고 릴리스는 변경 내용을 Azure에 배포합니다. 이 섹션의 절차를 수행하거나 다른 기술을 사용하여 리포지토리에 변경 내용을 커밋합니다. 예를 들어 즐겨찾는 도구 또는 IDE에서 Git 리포지토리를 복제한 다음, 이 리포지토리에 변경 내용을 푸시할 수 있습니다.

1. Azure DevOps 메뉴에서 **코드** > **파일** 을 선택한 후 리포지토리로 이동합니다.

1. *Views\Home* 으로 이동한 다음, *Index.cshtml* 파일 옆의 줄임표(...)를 선택하고 **편집** 을 선택합니다.

1. 한 div 태그 내부에 텍스트를 추가하는 경우처럼 파일을 변경합니다. 

1. 오른쪽 위에 있는 **커밋** 을 선택한 후 **커밋** 을 다시 선택하여 변경 내용을 적용합니다.  
    곧 빌드가 시작된 다음, 변경 내용을 배포하기 위해 릴리스가 실행됩니다. Azure DevOps 실시간 로깅이 있는 브라우저에서 또는 DevOps Starter 대시보드를 사용하여 빌드 상태를 모니터링할 수 있습니다.

1. 릴리스가 완료된 후 앱을 새로 고쳐 변경 내용을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

테스트하는 경우 리소스를 정리하여 요금이 청구되지 않도록 할 수 있습니다. 더 이상 필요하지 않은 경우 이 자습서에서 만든 Azure Service Fabric 클러스터와 관련 리소스를 삭제할 수 있습니다. 이렇게 하려면 DevOps Starter 대시보드의 **삭제** 기능을 사용합니다.

> [!IMPORTANT]
> 다음 절차에서는 리소스를 영구적으로 삭제합니다. *삭제* 기능은 Azure 및 Azure DevOps에서 DevOps Starter의 프로젝트에서 만든 데이터를 제거하므로 이 데이터를 검색할 수 없게 됩니다. 표시되는 메시지를 신중하게 읽은 후 이 절차를 따릅니다.

1. Azure Portal에서 DevOps Starter 대시보드로 이동합니다.
1. 오른쪽 위에서 **삭제** 를 선택합니다.합니다. 
1. 메시지에 따라 **예** 를 선택하여 리소스를 *영구적으로 삭제* 합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구를 충족하려면 필요에 따라 Azure CI/CD 파이프라인을 수정할 수 있습니다. 또한 다른 파이프라인에 대한 템플릿으로 이 CI/CD 패턴을 사용할 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * DevOps Starter를 사용하여 ASP.NET Core 앱을 만든 후 Service Fabric에 배포
> * Azure DevOps 및 Azure 구독 구성 
> * CI 파이프라인 검토
> * CD 파이프라인 검토
> * Git에 변경 내용 커밋 및 자동으로 Azure에 배포
> * 리소스 정리

Service Fabric 및 마이크로 서비스에 대해 자세히 알려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [애플리케이션 빌드에 마이크로 서비스 접근 방식 사용](/azure/devops/pipelines/release/define-multistage-release-process)