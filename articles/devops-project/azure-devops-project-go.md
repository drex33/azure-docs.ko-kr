---
title: '빠른 시작: Azure DevOps Starter를 사용하여 Go 프로그래밍 언어용 CI/CD 파이프라인 만들기'
description: DevOps Starter를 사용하면 Azure를 쉽게 시작할 수 있습니다. 빠른 몇 단계로 Azure 서비스에서 Go 프로그래밍 언어 웹앱을 시작할 수 있습니다.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: georgewallace
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: db1d72c953e5f344d4e202774983ab134ff6afe8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054147"
---
# <a name="create-a-cicd-pipeline-for-go-using-azure-devops-starter"></a>Azure DevOps Starter를 사용하여 Go용 CI/CD 파이프라인 만들기

Azure DevOps Starter를 사용하여 Go 앱에 대한 CI(연속 통합) 및 CD(지속적인 업데이트)를 구성합니다. DevOps Starter는 Azure DevOps 빌드 및 릴리스 파이프라인의 초기 구성을 간소화합니다.

Azure 구독이 없으면 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)을 통해 무료로 구독을 구할 수 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

DevOps Starter는 Azure Pipelines에 CI/CD 파이프라인을 만듭니다. 새 Azure DevOps 조직을 만들거나 기존 조직을 사용할 수 있습니다. 또한 DevOps Starter는 선택한 Azure 구독에서 Azure 리소스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 검색 상자에 **DevOps Starter** 를 입력한 다음, 선택합니다. **추가** 를 클릭하여 새 항목을 만듭니다.

    ![DevOps Starter 대시보드](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-app-and-azure-service"></a>샘플 앱 및 Azure 서비스 선택

1. **Go** 샘플 앱을 선택하고 **다음** 을 선택합니다.  
    
1. **간단한 Go 앱** 은 기본 프레임워크입니다. **다음** 을 선택합니다.  이전에 선택한 앱 프레임워크는 배포에 대해 사용 가능한 Azure 서비스 배포 대상의 유형을 나타냅니다. 
    
1. 기본 Azure 서비스에서 나가고 **다음** 을 선택합니다.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps 및 Azure 구독 구성 

1. 새 무료 Azure DevOps 조직을 만들거나 기존 조직을 선택합니다. 

1. Azure DevOps 프로젝트의 이름을 입력합니다. 

1. Azure 구독 및 위치를 선택하고 앱의 이름을 입력한 후 **완료** 를 선택합니다. 잠시 후에 DevOps Starter 대시보드가 Azure Portal에 표시됩니다. 샘플 앱이 Azure DevOps 조직의 리포지토리에서 설정되고, 빌드가 실행되고, 앱이 Azure에 배포됩니다. 

    이 대시보드에서는 코드 리포지토리, CI/CD 파이프라인 및 Azure의 애플리케이션에 가시성을 제공합니다. 오른쪽에서 **찾아보기** 를 선택하여 실행 중인 앱을 확인합니다.

    ![대시보드 보기](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>코드 변경 내용 커밋 및 CI/CD 실행

DevOps Starter는 Azure Repos 또는 GitHub에서 Git 리포지토리를 만듭니다. 리포지토리를 살펴보고 앱의 코드를 변경하려면 다음 단계를 수행합니다.

1. DevOps Starter 왼쪽에서 기본 분기에 대한 링크를 선택합니다. 이 링크는 새로 생성된 Git 리포지토리 보기를 엽니다.

1. 리포지토리 복제 URL을 보려면 오른쪽 위에 있는 **복제** 를 선택합니다. 즐겨찾는 IDE에서 Git 리포지토리를 복제할 수 있습니다. 다음 몇 단계에서는 웹 브라우저를 사용하여 코드 변경을 직접 기본 분기에 만들고 커밋할 수 있습니다.

1. 왼쪽의 *views/index.html* 파일로 이동한 후 **편집** 을 선택합니다.

1. 파일을 변경합니다. 예를 들어 한 div 태그 내부에서 텍스트를 수정합니다.

1. **커밋** 을 선택하고 변경 내용을 저장합니다.

1. 브라우저에서 DevOps Projects 대시보드로 이동합니다. 빌드가 진행 중이어야 합니다. 변경한 내용은 자동으로 빌드되며 CI/CD 파이프라인을 통해 배포됩니다.

## <a name="examine-the-cicd-pipeline"></a>CD 파이프라인 검토

DevOps Starter는 Azure Repos에서 전체 CI/CD 파이프라인을 자동으로 구성합니다. 필요에 따라 파이프라인을 탐색하고 사용자 지정합니다. Azure DevOps 빌드 및 릴리스 파이프라인을 숙지하려면 다음을 수행합니다.

1. DevOps Starter 대시보드로 이동합니다.

1. 맨 위에서 **빌드 파이프라인** 을 선택합니다. 브라우저 탭에 새 프로젝트에 대한 빌드 파이프라인이 표시됩니다.

1. **상태** 필드를 가리킨 후 줄임표(...)를 선택합니다. 메뉴에 새 빌드 쿼리, 빌드 일시 중지 및 빌드 파이프라인 편집과 같은 몇 가지 옵션이 표시됩니다.

1. **편집** 을 선택합니다.

1. 이 창에서 빌드 파이프라인의 다양한 작업을 검사할 수 있습니다. 빌드는 Git 리포지토리에서 원본 가져오기, 종속성 복원 및 배포에 사용된 출력 게시 등 다양한 작업을 수행합니다.

1. 빌드 파이프라인의 맨 위에서 빌드 파이프라인 이름을 선택합니다.

1. 빌드 파이프라인의 이름을 좀 더 구체적인 것으로 변경하고 **저장 및 큐에 넣기**, **저장** 을 차례로 선택합니다.

1. 빌드 파이프라인 이름에서 **기록** 을 선택합니다. 이 창에 대한 최근 변경 내용의 감사 내역이 표시됩니다. Azure DevOps는 빌드 파이프라인에 대한 모든 변경 내용을 계속 추적하고 버전을 비교할 수 있습니다.

1. **트리거** 를 선택합니다. DevOps Starter는 CI 트리거를 자동으로 생성하며, 리포지토리에 대한 모든 커밋이 새 빌드를 시작합니다. 필요에 따라 CI 프로세스에서 분기를 포함할지를 선택할 수 있습니다.

1. **보존** 을 선택합니다. 시나리오에 따라 특정 수의 빌드를 유지하거나 제거하는 정책을 지정할 수 있습니다.

1. **빌드 및 릴리스** 를 선택한 다음, **릴리스** 를 선택합니다.  DevOps Starter는 Azure에 대한 배포를 관리하는 릴리스 파이프라인을 만듭니다.

1. 릴리스 파이프라인 옆의 줄임표(...)를 선택하고 **편집** 을 선택합니다. 릴리스 파이프라인에는 릴리스 프로세스를 정의하는 *파이프라인* 이 포함됩니다.

1. **아티팩트** 아래에서 **드롭** 을 선택합니다. 이전에 검사한 빌드 파이프라인이 아티팩트에 사용된 출력을 생성합니다. 

1. **드롭** 아이콘의 오른쪽에서 **지속적인 배포 트리거** 를 선택합니다. 이 릴리스 파이프라인은 새 빌드 아티팩트를 사용할 수 있을 때마다 배포를 실행하는 CD 트리거를 사용하도록 설정했습니다. 필요에 따라 트리거를 비활성화할 수 있으므로 배포는 수동 실행이 필수적입니다. 

1. 왼쪽에서 **작업** 을 선택합니다. 작업은 배포 프로세스가 수행하는 활동입니다. 이 예제에서는 Azure App Service에 배포하기 위해 작업을 만들었습니다.

1. 오른쪽에서 **릴리스 보기** 를 선택하여 릴리스의 기록을 표시합니다.

1. 릴리스 옆에 있는 줄임표(...)를 선택하고 **열기** 를 선택합니다. 릴리스 요약, 연결된 작업 항목 및 테스트 등 몇 가지 메뉴를 확인할 수 있습니다.

1. **커밋** 을 선택합니다. 이 보기에는 이 배포와 연결된 코드 커밋이 표시됩니다. 

1. **로그** 를 선택합니다. 로그에는 배포 프로세스에 대한 유용한 정보가 포함됩니다. 배포 도중 및 이후 모두에서 로그를 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 빠른 시작에서 만든 Azure App Service 인스턴스와 관련 리소스를 삭제할 수 있습니다. 이렇게 하려면 DevOps Starter 대시보드의 **삭제** 기능을 사용합니다.

## <a name="next-steps"></a>다음 단계

팀의 요구에 맞게 빌드 및 릴리스 파이프라인 수정 방법에 대해 자세히 알려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [다단계 CD(연속 배포) 파이프라인 정의](/azure/devops/pipelines/release/define-multistage-release-process)