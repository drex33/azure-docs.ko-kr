---
title: '자습서: Azure DevOps Starter를 사용하여 기존 코드용 CI/CD 파이프라인 만들기'
description: Azure DevOps Starter를 사용하면 Azure를 쉽게 시작할 수 있습니다. 몇 가지 빠른 단계에서 DevOps Projects를 사용하면 사용자 고유의 코드와 GitHub 리포지토리를 사용하여 Azure 서비스에서 앱을 시작할 수 있습니다.
services: vsts
documentationcenter: vs-devops-build
ms.author: gwallace
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.custom: mvc
ms.openlocfilehash: 9600dcc2c9cbd150783ed1a14afb2b785ba5b054
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056343"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Azure DevOps Starter를 사용하여 GitHub 리포지토리용 CI/CD 파이프라인 만들기

Azure DevOps Starter는 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 Azure에 만드는 간소화된 프로세스를 제공합니다. 기존 코드와 Git 리포지토리를 가져오거나 샘플 애플리케이션을 선택할 수 있습니다.

다음을 수행합니다.

> [!div class="checklist"]
> * DevOps Starter를 사용하여 CI/CD 파이프라인 만들기
> * GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택
> * Azure DevOps 및 Azure 구독 구성 
> * GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
> * Azure Pipelines CI/CD 파이프라인 검사
> * 리소스 정리

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료 구독을 구할 수 있습니다.
* .NET, Java, PHP, Node.js, Python 또는 정적 웹 코드가 포함된 GitHub 또는 외부 Git 리포지토리에 액세스합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

Azure DevOps Starter는 Azure Pipelines에서 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 Azure DevOps Starter는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 상자에 **DevOps Starter** 를 입력한 다음, 선택합니다. **추가** 를 클릭하여 새 항목을 만듭니다.

    ![DevOps Starter 대시보드](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. **사용자 코드 가져오기** 를 선택한 후 **다음** 을 선택합니다.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>GitHub 리포지토리에 대한 액세스 구성 및 프레임워크 선택

1. **GitHub** 또는 외부 **Git**  코드 리포지토리 중 하나를 선택합니다. 이 자습서에서는 **GitHub** 를 선택합니다. Azure에서 GitHub 리포지토리에 액세스할 수 있도록 허용하려면 처음에 GitHub를 인증해야 할 수도 있습니다.

1. **리포지토리** 및 **분기** 를 선택하고, **다음** 을 선택합니다.

1. Docker 컨테이너를 사용하는 경우 **앱 Docker화** 를 **예** 로 변경합니다. 이 자습서에서는 **아니요** 를 선택한 상태로 두고, **다음** 을 선택합니다. Docker 컨테이너를 사용하는 방법을 자세히 알아보려면 마우스로 **i** 아이콘 위를 가리키세요.

   ![드롭다운 메뉴에서 애플리케이션 프레임워크 선택](_img/azure-devops-project-github/appframework.png)

1. 드롭다운 메뉴에서 **애플리케이션 런타임** 및 **애플리케이션 프레임워크** 를 선택하고, **다음** 을 선택합니다. 애플리케이션 프레임워크는 사용할 수 있는 Azure 서비스 배포 대상의 유형을 나타냅니다.

1. 애플리케이션을 배포할 **Azure 서비스** 를 선택하고, **다음** 을 선택합니다.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps 및 Azure 구독 구성

1. **프로젝트 이름** 의 이름을 입력합니다.

1. **Azure DevOps 조직** 에서 사용 가능한 조직을 새로 만들거나 드롭다운 메뉴에서 기존 조직을 선택합니다.

1. **Azure 구독** 에서 구독을 선택하고, **웹앱** 에 이름을 입력하거나 기본값을 사용합니다. **위치** 를 선택한 다음, **완료** 를 선택합니다. 잠시 후에 DevOps Starter 배포 개요가 Azure Portal에 표시됩니다.

1. **리소스로 이동** 을 선택하여 DevOps Starter 대시보드를 확인합니다. 오른쪽 위 모서리에서 빠른 액세스를 위해 **프로젝트** 를 대시보드에 고정시킵니다. Azure DevOps Starter는 CI 빌드 및 릴리스 트리거를 자동으로 구성합니다. 코드는 GitHub 리포지토리 또는 다른 외부 리포지토리에 유지되며, 샘플 앱은 **Azure DevOps 조직** 의 리포지토리에 설정됩니다. Azure DevOps Starter는 빌드를 실행하고 앱을 Azure에 배포합니다.

   ![Azure DevOps Projects 대시보드 보기](_img/azure-devops-project-github/projectsdashboard.png)

1. 대시보드에는 코드 리포지토리, CI/CD 파이프라인 및 Azure의 앱이 표시됩니다. Azure 리소스 아래의 오른쪽에서 **찾아보기** 를 선택하여 실행 중인 앱을 확인합니다.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포

이제 앱에서 팀과 협업할 준비가 되었습니다. CI/CD 프로세스는 최신 작업을 웹 사이트에 자동으로 배포합니다. GitHub 리포지토리를 변경할 때마다 Azure DevOps에서 빌드를 시작하고, CD 파이프라인에서 Azure에 대한 배포를 실행합니다.

1. DevOps Starter 대시보드에서 **리포지토리** 를 선택합니다. GitHub 리포지토리가 새 브라우저 탭에서 열립니다. 애플리케이션을 변경한 다음, **변경 내용 적용** 을 선택합니다.

1. 잠시 후 Azure Pipelines에서 빌드가 시작됩니다. DevOps Starter 대시보드에서 빌드 상태를 모니터링할 수 있습니다. DevOps Starter 대시보드에서 **빌드 파이프라인** 탭을 선택하여 Azure DevOps 조직에서 이를 모니터링할 수도 있습니다.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Azure Pipelines CI/CD 파이프라인 검사

Azure DevOps Starter는 Azure Pipelines에서 CI/CD 파이프라인을 자동으로 구성합니다. 필요에 따라 파이프라인을 탐색하고 사용자 지정합니다. 빌드 및 릴리스 파이프라인에 익숙해지려면 다음을 수행합니다.

1. DevOps Starter 대시보드에서 **빌드 파이프라인** 을 선택합니다.

1. **Azure Pipelines** 페이지가 열리면 최근 빌드의 기록과 각 빌드에 대한 상태가 표시됩니다.

   ![Azure Pipelines 빌드 페이지](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. **빌드** 페이지의 오른쪽 위 모서리에서 **편집** 을 선택하여 현재 빌드를 변경하거나, **큐** 를 선택하여 새 빌드를 추가하거나, 세로 줄임표 단추( **&#8942;** )를 선택하여 더 많은 옵션이 있는 메뉴를 열 수 있습니다. **편집** 을 선택합니다.

1. 빌드는 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 대한 출력 게시와 같은 다양한 작업을 수행합니다. 오른쪽의 **이름** 아래에서 빌드 파이프라인 이름을 더 설명적인 이름으로 변경합니다. **저장 및 큐** 를 선택한 다음, **저장** 을 선택합니다. 설명을 입력한 다음, **저장** 을 다시 선택합니다.

   ![Azure DevOps 빌드 페이지](_img/azure-devops-project-github/buildpage.png)

1. 빌드에 대한 최근 변경 내용의 감사 내역을 보려면 **기록** 탭을 선택합니다.  Azure DevOps는 빌드 파이프라인에 대한 모든 변경을 모니터링하고 버전을 비교할 수 있도록 합니다.

1. **트리거** 탭을 선택합니다. Azure DevOps Projects는 일부 기본 설정을 사용하여 CI 트리거를 자동으로 만듭니다. 코드 변경 내용을 적용할 때마다 빌드를 실행하도록 **지속적인 통합 사용** 과 같은 트리거를 설정할 수 있습니다. 또한 특정 시간에 빌드가 실행되도록 예약하기 위한 트리거를 설정할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 Azure App Service 및 관련 리소스가 더 이상 필요하지 않은 경우 이를 삭제할 수 있습니다. DevOps Projects 대시보드에서 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서 CI/CD 프로세스를 구성하면 Azure DevOps Projects에서 빌드 및 릴리스 파이프라인이 자동으로 만들어집니다. 팀의 요구를 충족하려면 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다.

CI/CD 파이프라인에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [다단계 CD(연속 배포) 파이프라인 정의](/azure/devops/pipelines/release/define-multistage-release-process)

애플리케이션 모니터링에 대해 자세히 알아보려면 다음을 참조하세요.
  
 > [!div class="nextstepaction"]
 > [Azure Monitor란?](../azure-monitor/overview.md)