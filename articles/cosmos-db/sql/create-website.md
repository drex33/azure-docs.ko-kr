---
title: 템플릿을 사용하여 웹앱 배포 - Azure Cosmos DB
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos 계정, Azure App Service Web Apps 및 샘플 웹 애플리케이션을 배포하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: bd5b9762b72264161e18289c378cd447806e14aa
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117046"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 및 Azure App Service에 대한 GitHub의 웹앱 배포
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 자습서에서는 Azure Cosmos DB의 연결 정보를 잘라내어 `appsettings.json` Azure Portal의 Azure 앱 Services 애플리케이션 설정으로 붙여넣지 않고도 처음 실행 시 Azure Cosmos DB에 연결하는 웹 애플리케이션의 웹 배포를 수행하는 방법을 보여줍니다. 이러한 모든 작업은 단일 작업에서 Azure Resource Manager 템플릿을 사용하여 수행됩니다. 이 예제에서는 [Web app 자습서](sql-api-dotnet-application.md)에서 [Azure Cosmos DB ToDo 샘플](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)을 배포합니다.

Resource Manager 템플릿은 매우 유연하며 Azure의 모든 서비스에서 복잡한 배포를 작성할 수 있도록 합니다. 여기에는 GitHub에서 애플리케이션을 배포하고 Azure Portal에서 Azure App Service의 애플리케이션 설정에 연결 정보를 삽입하는 등의 고급 작업이 포함됩니다. 이 자습서에서는 단일 Resource Manager 템플릿을 사용하여 다음과 같은 작업을 수행하는 방법을 보여줍니다.

* Azure Cosmos 계정 배포.
* Azure App Service Hosting Plan 배포.
* Azure App Service 배포.
* Azure Cosmos 계정에서 엔드포인트와 키를 Azure Portal의 App Service 애플리케이션 설정에 삽입합니다.
* App Service로 GitHub 리포지토리의 웹 애플리케이션 배포.

결과 배포에는 Azure Portal에서 Azure Cosmos DB의 엔드포인트 URL 또는 인증 키를 잘라내어 붙여넣지 않고도 Azure Cosmos DB에 연결할 수 있는 완전히 작동하는 웹 애플리케이션이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

> [!TIP]
> 이 자습서에서는 Azure Resource Manager 템플릿 또는 JSON을 이전에 사용해 본 경험이 있다고 가정하지는 않지만, 참조된 템플릿 또는 배포 옵션을 수정하려면 이러한 각 영역에 대한 지식이 필요합니다.

## <a name="step-1-deploy-the-template"></a>1단계: 템플릿 배포

먼저 아래의 **Azure에 배포** 단추를 선택하여 Azure Portal을 열고 사용자 지정 배포를 만듭니다. [Azure 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.documentdb/cosmosdb-webapp)에서 Azure Resource Manager 템플릿을 볼 수도 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-webapp%2Fazuredeploy.json)

Azure Portal에 들어왔으면 배포할 구독을 선택하고 새 리소스 그룹을 선택하거나 만듭니다. 그 후, 다음 값을 채웁니다.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="템플릿 배포 UI의 스크린샷":::

* **지역** - 리소스 관리자에게 필요한 값입니다. 리소스가 있는 위치 매개 변수에서 사용하는 것과 동일한 지역을 입력합니다.
* **애플리케이션 이름** - 이 이름은 배포에 대한 모든 리소스에서 사용됩니다. 기존 Azure Cosmos DB와 App Service 계정과의 충돌을 피하기 위해 고유한 이름을 선택해야 합니다.
* **위치** - 리소스를 배포하는 지역입니다.
* **App Service 계획 계층** - App Service 계획의 가격 책정 계층입니다.
* **App Service 계획 인스턴스** - App Service 계획의 작업자 수입니다.
* **리포지토리 URL** - GitHub의 웹 애플리케이션 리포지토리입니다.
* **분기** - GitHub 리포지토리의 분기입니다.
* **데이터베이스 이름** - Azure Cosmos 데이터베이스 이름입니다.
* **컨테이너 이름** - Azure Cosmos 컨테이너 이름입니다.

값을 채운 다음 **만들기** 단추를 선택하여 배포를 시작합니다. 이 단계는 완료하는 데 5분에서 10분 정도가 걸립니다.

> [!TIP]
> 템플릿은 템플릿에 입력된 Azure App Service 이름과 Azure Cosmos 계정 이름이 유효한지, 사용 가능한지를 확인하지 않습니다. 배포를 제출하기 전에 지정하려는 이름의 가용성을 확인하는 것이 좋습니다.


## <a name="step-2-explore-the-resources"></a>단계 2: 리소스 살펴보기

### <a name="view-the-deployed-resources"></a>배포된 리소스 검토

템플릿이 리소스를 배포한 후 리소스 그룹에서 각 리소스를 볼 수 있습니다.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="리소스 그룹":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Cosmos DB 엔드포인트 및 키 보기

그 다음 포털에서 Azure Cosmos 계정을 엽니다. 다음 스크린샷은 Azure Cosmos 계정의 엔드포인트와 키를 보여줍니다.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos 키":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>애플리케이션 설정에서 Azure Cosmos DB 키 보기

그 다음 리소스 그룹에서 Azure App Service로 이동합니다. 구성 탭을 클릭하여 App Service의 애플리케이션 설정을 확인합니다. 애플리케이션 설정에는 Cosmos DB에 연결하는 데 필요한 Cosmos DB 계정 및 기본 키 값과 템플릿 배포에서 전달된 데이터베이스 및 컨테이너 이름이 포함되어 있습니다.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="애플리케이션 설정":::

### <a name="view-web-app-in-deployment-center"></a>배포 센터에서 웹앱 보기

다음으로 App Service 배포 센터로 이동합니다. 여기에 템플릿으로 전달된 GitHub 리포지토리의 리포지토리 지점이 표시됩니다. 또한 아래 상태는 성공(활성), 즉 애플리케이션이 성공적으로 배포되고 시작되었음을 나타냅니다.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="배포 센터":::

### <a name="run-the-web-application"></a>웹 애플리케이션 실행

배포 센터 위쪽에서 **찾아보기** 를 클릭하여 웹 애플리케이션을 엽니다. 홈 화면에 웹 애플리케이션이 열립니다. **새로 만들기** 를 클릭하고 필드에 데이터를 입력한 다음 저장을 클릭합니다. Cosmos DB에 저장된 데이터가 결과 화면으로 표시됩니다.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="홈 화면":::

## <a name="step-3-how-does-it-work"></a>3단계: 작동 방식

이 작업을 수행하는 데 필요한 세 가지 요소가 있습니다.

### <a name="reading-app-settings-at-runtime"></a>런타임에 앱 설정 읽기

먼저 애플리케이션은 `Startup` ASP.NET MVC 웹 애플리케이션의 클래스에서 Cosmos DB 엔드포인트 및 키를 요청해야 합니다. [Cosmos DB 할 일 샘플](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)은 연결 정보를 appsettings.json에 입력하는 로컬에서 실행할 수 있습니다. 그러나 이 파일을 배포하면 파일이 앱을 배포합니다. 이러한 줄이 appsettings.json의 설정에 액세스할 수 없는 경우 Azure App Service의 애플리케이션 설정에서 시도합니다.

:::image type="content" source="./media/create-website/startup.png" alt-text="스크린샷에는 databaseName, containerName, account 및 key를 포함하여 빨간색으로 표시된 여러 문자열 변수가 있는 메서드가 표시됩니다.":::

### <a name="using-special-azure-resource-management-functions"></a>특수 Azure 리소스 관리 기능 사용

이러한 값을 배포할 때 애플리케이션에서 사용할 수 있도록 Azure Resource Manager 템플릿에서는 '{section: key}' 형식의 위에 있는 애플리케이션에서 사용되는 것과 일치하는 키 이름을 가진 애플리케이션 설정 값에 Cosmos DB 계정의 값을 가져오는 [reference](../../azure-resource-manager/templates/template-functions-resource.md#reference)와 [listKeys](../../azure-resource-manager/templates/template-functions-resource.md#listkeys)를 비롯한 특수 Azure 리소스 관리 기능을 사용하여 Cosmos DB 계정에서 해당 값을 요청할 수 있습니다. 예들 들어 `CosmosDb:Account`입니다.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="템플릿 키":::

### <a name="deploying-web-apps-from-github"></a>GitHub에서 웹 앱 배포

마지막으로 GitHub에서 App Service로 웹 애플리케이션을 배포해야 합니다. 이는 아래 JSON을 실행하여 구현할 수 있습니다. 여기에서 주의해야 할 점 두 가지는 리소스의 형식과 이름입니다. `"type": "sourcecontrols"` 및 `"name": "web"` 속성 값은 모두 하드코드되어 있으며 변경해서는 안 됩니다.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="GitHub에서 배포":::

## <a name="next-steps"></a>다음 단계

축하합니다! Cosmos DB에 연결하는 데 필요한 연결 정보를 자동으로 포함하는 Azure Cosmos DB, Azure App Service 및 샘플 웹 애플리케이션을 단일 작업으로 사용하고 중요한 정보를 잘라내어 붙여넣지 않아도 됩니다. 이 템플릿을 시작점으로 사용하여 동일한 방식으로 웹 애플리케이션을 배포하도록 수정할 수 있습니다.

* 이 샘플의 Azure Resource Manager 템플릿을 확인하려면 [Azure 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.documentdb/cosmosdb-webapp)로 이동합니다.
* 샘플 앱의 원본 코드를 확인하려면 [GitHub Cosmos DB 할 일 앱](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app)으로 이동합니다.
