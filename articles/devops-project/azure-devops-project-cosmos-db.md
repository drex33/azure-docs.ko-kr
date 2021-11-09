---
title: '자습서: Azure DevOps Starter를 사용하여 Azure Cosmos DB에서 제공되는 Node.js 앱 배포'
description: Azure DevOps Starter를 사용하면 Azure를 쉽게 시작할 수 있습니다. DevOps Starter를 사용하면 몇 가지 빠른 단계를 통해 Azure Cosmos DB로 구동되는 Node.js 앱을 Windows 웹앱에 배포할 수 있습니다.
ms.author: gwallace
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: georgewallace
ms.custom: devx-track-js
ms.openlocfilehash: ff811be8f4651bde7059fe1d575c0ac703570830
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132060456"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>DevOps Starter를 사용하여 Azure Cosmos DB에서 제공되는 Node.js 앱 배포

Azure DevOps Starter는 CI(연속 통합) 및 CD(지속적인 배포) 파이프라인을 Azure에 만들 수 있는 간소화된 환경을 제공합니다. 이 작업은 기존 코드와 Git 리포지토리(repo)를 사용하거나 샘플 애플리케이션을 선택하여 수행합니다.

DevOps Starter는 다음 작업도 수행합니다.

* 자동으로 Azure 리소스(예: Azure Cosmos DB, Azure Application Insights, Azure App Service 및 App Service 계획) 만들기

* Azure DevOps에서 CI/CD 릴리스 파이프라인 만들기 및 구성

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * DevOps Starter를 사용하여 Azure Cosmos DB에서 제공되는 Node.js 앱 배포
> * Azure DevOps 및 Azure 구독 구성
> * Azure Cosmos DB 검토
> * CI 파이프라인 검토
> * CD 파이프라인 검토
> * Git에 변경 내용 커밋 및 자동으로 Azure에 배포
> * 리소스 정리

## <a name="prerequisites"></a>필수 구성 요소

[Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)를 체험할 수 있는 Azure 구독이 필요합니다.

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>DevOps Starter를 사용하여 Node.js 앱 배포

DevOps Starter는 Azure Pipelines에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 DevOps Starter는 선택한 Azure 구독에서 Azure Cosmos DB, Application Insights, App Service 및 App Service 계획과 같은 Azure 리소스를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 상자에 **DevOps Starter** 를 입력한 다음, 선택합니다. **추가** 를 클릭하여 새 항목을 만듭니다.

    ![DevOps Starter 대시보드](_img/azure-devops-starter-aks/search-devops-starter.png)

1. 런타임으로 **Node.js** 를 선택하고 **다음** 을 선택합니다. **애플리케이션 프레임워크 선택** 에서 **Express.js** 를 선택합니다.

1. **Cosmos DB** 에 대해 **데이터베이스 추가** 섹션을 사용하도록 설정하고, **다음** 을 선택합니다.

    ![데이터베이스 추가](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter는 **Express.js**, **Sample Node.js 앱** 및 **Sail.js** 와 같은 다양한 애플리케이션 프레임워크를 지원합니다. 이 자습서에서는 **Express.js** 를 사용합니다.

1. 애플리케이션을 배포할 Azure 서비스를 선택하고, **다음** 을 선택합니다. 옵션으로 Windows 웹앱, Azure Kubernetes Service 및 Azure Web App for Containers가 있습니다. 이 자습서에서는 **Windows 웹앱** 을 사용합니다.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps 및 Azure 구독 구성

1. Azure DevOps 프로젝트의 이름을 입력합니다.

1. 새 Azure DevOps 조직을 만들거나 기존 조직을 선택합니다.

1. Azure 구독을 선택합니다.

1. 추가 Azure 구성 설정을 보거나 가격 책정 계층과 위치를 확인하려면 **추가 설정** 을 선택합니다. 이 창에는 Azure 서비스의 가격 책정 계층과 위치를 구성하는 다양한 옵션이 표시됩니다.

1. Azure 구성 영역을 종료하고 **완료** 를 선택합니다.

1. 몇 분 후에 프로세스가 완료됩니다. Node.js 앱 샘플은 Azure DevOps 조직의 Git 리포지토리에 설정됩니다. 다음으로, CI/CD 파이프라인뿐만 아니라 Azure Cosmos DB, App Service, App Service 계획 및 Application Insights 리소스도 만들어집니다. 그런 다음, 앱이 Azure에 배포됩니다.

   모든 프로세스가 완료되면 Azure DevOps Starter 대시보드가 Azure Portal에 표시됩니다. Azure Portal의 **모든 리소스** 에서 직접 DevOps Starter 대시보드로 이동할 수도 있습니다.

   이 대시보드는 Azure DevOps 코드 리포지토리, CI/CD 파이프라인 및 Azure Cosmos DB 데이터베이스에 대한 가시성을 제공합니다. Azure DevOps 파이프라인에서 추가 CI/CD 옵션을 구성할 수 있습니다. 대시보드의 오른쪽에서 **Cosmos DB** 를 선택하여 이러한 옵션을 확인합니다.

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB 검토

DevOps Starter는 검색하고 사용자 지정할 수 있는 Azure Cosmos DB를 자동으로 구성합니다. Azure Cosmos DB에 익숙해지려면 다음을 수행합니다.

1. DevOps Starter 대시보드로 이동합니다.

    ![DevOps Projects 대시보드](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. 오른쪽에서 Azure Cosmos DB를 선택합니다. Azure Cosmos DB에 대한 창이 열립니다. 이 보기에서 작업 모니터링 및 로그 검색과 같은 다양한 작업을 수행할 수 있습니다.

    ![Azure Cosmos DB 창](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>CI 파이프라인 검토

DevOps Starter는 Azure DevOps 조직에서 CI/CD 파이프라인을 자동으로 구성합니다. 파이프라인을 탐색하고 사용자 지정할 수 있습니다. 이 작업에 익숙해지려면 다음을 수행합니다.

1. DevOps Starter 대시보드로 이동합니다.

1. **빌드** 아래의 하이퍼링크를 선택합니다. 브라우저 탭에 새 프로젝트에 대한 빌드 파이프라인이 표시됩니다.

    ![빌드 창](_img/azure-devops-project-cosmos-db/build.png)

1. **편집** 을 선택합니다. 이 창에서 빌드 파이프라인의 다양한 작업을 검사할 수 있습니다. 빌드는 Git 리포지토리에서 소스 코드 가져오기, 애플리케이션 빌드, 단위 테스트 실행 및 배포에 사용된 출력 게시 등 다양한 작업을 수행합니다.

1. **트리거** 를 선택합니다. DevOps Starter는 CI 트리거를 자동으로 생성하며, 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다. CI 프로세스에서 분기를 포함하거나 제외하도록 선택할 수 있습니다.

1. **보존** 을 선택합니다. 시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

1. 빌드 파이프라인의 맨 위에서 빌드 파이프라인 이름을 선택합니다.

1. 빌드 파이프라인의 이름을 좀 더 구체적인 것으로 변경하고 **저장 및 큐에 넣기** 드롭다운에서 **저장** 을 선택합니다.

1. 빌드 파이프라인 이름에서 **기록** 을 선택합니다. 이 창에 대한 최근 변경 내용의 감사 내역이 표시됩니다. Azure DevOps는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

## <a name="examine-the-cd-release-pipeline"></a>CD 릴리스 파이프라인 검토

DevOps Starter는 Azure DevOps 조직에서 Azure 구독에 배포하는 데 필요한 단계를 자동으로 만들고 구성합니다. 이러한 단계에는 Azure 구독에 Azure DevOps를 인증하기 위한 Azure 서비스 연결 구성이 포함됩니다. 또한 자동화 기능은 Azure에 CD를 제공하는 릴리스 파이프라인도 만듭니다. 릴리스 파이프라인에 대한 자세한 내용을 보려면 다음을 수행하세요.

1. **파이프라인** 으로 이동하여 **릴리스** 를 선택합니다.

1. **편집** 을 선택합니다.

1. **아티팩트** 아래에서 **드롭** 을 선택합니다. 이전 단계에서 검사한 빌드 파이프라인이 아티팩트에 사용된 출력을 생성합니다.

1. **놓기** 아이콘의 오른쪽에서 **지속적인 배포 트리거** 를 선택합니다. 이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 지속적인 배포 트리거를 사용하도록 설정했습니다. 배포가 수동으로 실행되도록 트리거를 사용하지 않도록 설정할 수 있습니다.

1. 오른쪽에서 **릴리스 보기** 섹션을 선택하여 릴리스의 기록을 표시합니다.

1. 파이프라인을 표시할 릴리스를 선택합니다. 릴리스 요약, 커밋 또는 연결된 작업 항목을 확인할 환경을 선택합니다.

1. **커밋** 을 선택합니다. 이 보기에는 이 배포와 연결된 코드 커밋이 표시됩니다. 배포 간 커밋 차이를 보기 위해 릴리스를 비교합니다.

1. **로그 보기** 를 선택합니다. 로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다. 배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>코드 변경 내용 커밋 및 CI/CD 파이프라인 실행

> [!NOTE]
> 다음 절차에서는 텍스트를 간단히 변경하여 CI/CD 파이프라인을 테스트합니다.

이제 최신 작업을 App Service에 배포하는 CI/CD 프로세스를 사용하여 앱에서 팀과 협업할 준비가 되었습니다. Git 리포지토리에 대한 각 변경은 Azure DevOps에서 빌드를 시작하고, CD 파이프라인은 Azure에 배포를 실행합니다. 이 섹션의 절차를 수행하거나 다른 기술을 사용하여 리포지토리에 변경 내용을 커밋합니다. 예를 들어 즐겨찾는 도구 또는 IDE에서 Git 리포지토리를 복제한 다음, 이 리포지토리에 변경 내용을 푸시할 수 있습니다.

1. Azure DevOps 메뉴에서 **리포지토리**, **파일** 을 차례로 선택합니다. 그런 다음, 리포지토리로 이동합니다.

1. 리포지토리에는 만들기 프로세스에서 선택한 애플리케이션 언어를 기반으로 하는 코드가 이미 포함되어 있습니다. **Application/views/index.pug** 파일을 엽니다.

1. **편집** 을 선택한 다음, 줄 번호를 **15** 로 변경합니다. 예를 들어 "Azure Cosmos DB로 구동되는 Azure App Service에 대한 첫 번째 배포"로 변경할 수 있습니다.

1. 오른쪽 위 모서리에서 **커밋** 을 선택한 다음, **커밋** 을 다시 선택하여 변경 내용을 푸시합니다.

     몇 초 후에 Azure DevOps에서 빌드가 시작되고, 릴리스가 실행되어 변경 내용을 배포합니다. Azure DevOps 조직이 있는 브라우저에서 또는 DevOps Starter 대시보드를 사용하여 빌드 상태를 모니터링합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 만든 관련 리소스를 삭제합니다. DevOps Starter 대시보드의 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구를 충족하려면 이러한 빌드 및 릴리스 파이프라인을 수정할 수 있습니다. 또한 다른 파이프라인에 대한 템플릿으로 이 CI/CD 패턴을 사용할 수 있습니다. 이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * DevOps Starter를 사용하여 Azure Cosmos DB에서 제공되는 Node.js 앱 배포
> * Azure DevOps 및 Azure 구독 구성 
> * Azure Cosmos DB 검토
> * CI 파이프라인 검토
> * CD 파이프라인 검토
> * Git에 변경 내용 커밋 및 자동으로 Azure에 배포
> * 리소스 정리

자세한 내용과 다음 단계는 [다단계 CD(지속적인 배포) 파이프라인 정의](/azure/devops/pipelines/release/define-multistage-release-process)를 참조하세요.