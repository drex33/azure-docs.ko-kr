---
title: Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용
description: Azure Pipelines 빌드 및 릴리스 파이프라인에서 Azure DevTest Labs를 사용하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: df9e496aa930c328832d59901aa8165e61cd076f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552626"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs 사용
이 문서에서는 Azure Pipelines 빌드 및 릴리스 파이프라인에서 DevTest Labs를 사용하는 방법에 대한 정보를 제공합니다. 

## <a name="overall-flow"></a>전체 흐름
기본적인 흐름은 다음 작업을 수행하는 **빌드 파이프라인** 을 갖추는 것입니다.

1. 애플리케이션 코드를 빌드합니다.
1. DevTest Labs에서 기준 환경을 만듭니다.
1. 사용자 지정 정보로 환경을 업데이트합니다.
1. DevTest Labs 환경에 애플리케이션을 배포합니다.
1. 코드를 테스트합니다. 

빌드가 성공적으로 완료되면 **릴리스 파이프라인** 은 빌드 아티팩트를 사용하여 스테이징 또는 프로덕션을 배포합니다. 

필수 전제 중 하나는 Azure 리소스 구성을 포함하여 테스트된 에코시스템을 다시 만드는 데 필요한 모든 정보를 빌드 아티팩트 내에서 사용할 수 있어야 한다는 것입니다. Azure 리소스를 사용하면 비용이 발생하므로 회사는 해당 리소스의 사용을 제어하거나 추적하려고 합니다. 상황에 따라 리소스를 만들고 구성하는 데 사용되는 Azure Resource Manager 템플릿을 IT 같은 다른 부서에서 관리할 수 있습니다. 해당 템플릿이 다른 리포지토리에 저장될 수도 있습니다. 빌드를 만들고 테스트하거나 코드 및 구성을 모두 빌드 아티팩트 내에 저장하여 프로덕션에서 시스템을 제대로 다시 만드는 흥미로운 상황이 이어지기도 합니다. 

빌드/테스트 단계에서 DevTest Labs를 사용하여 빌드 소스에 Azure Resource Manager 템플릿 및 지원 파일을 추가할 수 있습니다. 그러면 릴리스 단계에서 테스트에 사용된 정확한 구성이 프로덕션에 배포됩니다. 적절한 구성으로 **Azure DevTest Labs 환경 만들기** 작업을 수행하면 빌드 아티팩트 내에 Resource Manager 템플릿이 저장됩니다. 이 예제에서는 [자습서: Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드](../app-service/tutorial-dotnetcore-sqldb-app.md)의 코드를 사용하여 Azure에서 웹앱을 배포하고 테스트합니다.

![전체 흐름](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Azure 리소스 설정
미리 만들어야 할 몇 가지 항목이 있습니다.

- 두 개의 리포지토리가 있어야 합니다. 첫 번째 리포지토리에는 자습서의 코드와 두 개의 추가 VM을 사용하는 Resource Manager 템플릿이 포함됩니다. 두 번째 리포지토리에는 기준 Azure Resource Manager 템플릿(기존 구성)이 포함됩니다.
- 프로덕션 코드 및 구성을 배포하기 위한 리소스 그룹이 있어야 합니다.
- 빌드 파이프라인을 위해 [구성 리포지토리에 대한 연결](devtest-lab-create-environment-from-arm.md)을 사용하여 랩을 설정해야 합니다. DevTest Labs에서 템플릿을 인식하고 배포할 수 있도록 하려면 Resource Manager 템플릿이 metadata.json을 통해 azuredeploy.json으로 구성 리포지토리에 체크 인되어야 합니다.

빌드 파이프라인은 DevTest Labs 환경을 만들고 테스트용 코드를 배포합니다.

## <a name="set-up-a-build-pipeline"></a>빌드 파이프라인 설정
Azure Pipelines에서 [자습서: Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드](../app-service/tutorial-dotnetcore-sqldb-app.md)의 코드를 사용하여 빌드 파이프라인을 만듭니다. **ASP.NET Core** 템플릿을 사용하면 코드를 빌드, 테스트, 게시하는 데 필요한 작업을 채울 수 있습니다.

![ASP.NET 템플릿 선택](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

DevTest Labs에서 환경을 만들고 환경에 배포하기 위해서는 세 개의 작업이 더 추가되어야 합니다.

![세 개의 작업이 포함된 파이프라인](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>환경 만들기 작업
환경 만들기 작업(**Azure DevTest Labs 환경 만들기** 작업)에서 드롭다운 목록을 사용하여 다음 값을 선택합니다.

- Azure 구독
- 랩 이름
- 리포지토리 이름
- 템플릿 이름(환경이 저장된 폴더 표시) 

정보를 수동으로 입력하는 대신 페이지에서 드롭다운 목록을 사용하는 것이 좋습니다. 정보를 수동으로 입력하는 경우 정규화된 Azure 리소스 ID를 입력합니다. 작업에서는 리소스 ID 대신 식별 이름을 보여 줍니다. 

환경 이름은 DevTest Labs 내에 표시되는 이름입니다. 이 이름은 빌드마다 고유해야 합니다. 예를 들어 **TestEnv$(Build.BuildId)** 가 있습니다. 

매개 변수 파일 또는 매개 변수를 지정하여 Resource Manager 템플릿에 정보를 전달할 수 있습니다. 

**환경 템플릿 출력을 기반으로 출력 변수 만들기** 옵션을 선택하고 참조 이름을 입력합니다. 이 예제에서는 참조 이름으로 **BaseEnv** 를 입력합니다. 이 BaseEnv는 다음 작업을 구성할 때 사용합니다. 

![Azure DevTest Labs 환경 만들기 작업](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>환경 채우기 작업
두 번째 작업(**Azure DevTest Labs 환경 채우기** 작업)은 기존 DevTest Labs 환경을 업데이트하는 것입니다. 환경 만들기 작업에서는 이 작업의 환경 이름을 구성하는 데 사용되는 **BaseEnv.environmentResourceId** 를 출력합니다. 이 예제의 Resource Manager 템플릿에는 두 매개 변수 **adminUserName** 및 **adminPassword** 가 있습니다. 

![Azure DevTest Labs 환경 채우기 작업](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service 배포 작업
세 번째 작업은 **Azure App Service 배포** 작업입니다. 앱 유형은 **웹앱** 으로 설정되었고 App Service 이름은 **$(WebSite)** 로 설정되었습니다.

![App Service 배포 작업](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>릴리스 파이프라인 설정
작업이 **Azure 배포: 리소스 그룹 만들기 또는 업데이트** 및 **Azure App Service 배포** 두 개인 릴리스 파이프라인을 만듭니다. 

첫 번째 작업의 경우 리소스 그룹의 이름과 위치를 지정합니다. 템플릿 위치는 연결된 아티팩트입니다. Resource Manager 템플릿에 연결된 템플릿이 포함된 경우 사용자 지정 리소스 그룹 배포를 구현해야 합니다. 템플릿은 게시된 삭제 아티팩트에 있습니다. Resource Manager 템플릿에 대한 템플릿 매개 변수를 재정의하세요. 나머지 설정은 기본값을 그대로 유지할 수 있습니다. 

두 번째 작업 **Azure App Service 배포** 에서 Azure 구독을 지정하고 **앱 유형** 에 **웹앱**, **App Service 이름** 에 **$(WebSite)** 를 선택합니다. 나머지 설정은 기본값을 그대로 유지할 수 있습니다. 

## <a name="test-run"></a>테스트 실행
이제 파이프라인이 둘 다 설정되었으므로 수동으로 빌드를 큐에 넣고 작동하는지 확인합니다. 다음 단계는 빌드에 적합한 트리거를 설정하고 빌드를 릴리스 파이프라인에 연결하는 것입니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [Azure Pipelines 연속 통합 및 지속적인 업데이트 파이프라인에 Azure DevTest Labs 통합](devtest-lab-integrate-ci-cd.md)
- [Azure Pipelines CI/CD 파이프라인에 환경 통합](integrate-environments-devops-pipeline.md)
