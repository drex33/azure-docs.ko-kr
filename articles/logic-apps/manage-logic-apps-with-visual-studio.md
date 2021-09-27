---
title: 클라우드 탐색기와 함께 Visual Studio를 사용하여 논리 앱 편집 및 관리
description: 클라우드 탐색기와 함께 Visual Studio를 사용하여 소스 제어 편집, 업데이트, 관리, 추가, 논리 앱 배포
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.custom: mvc
ms.date: 04/23/2021
ms.openlocfilehash: fff316c328ac61f48693e635745ca42a9c773c4d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646628"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Visual Studio로 논리 앱 관리

[Azure Portal](https://portal.azure.com)에서 논리 앱을 만들고 편집하고 관리하고 배포할 수 있지만, 소스 제어에 논리 앱을 추가하고 다양한 버전을 게시하고 다양한 배포 환경에 대한 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 템플릿을 만들려는 경우에도 Visual Studio를 사용할 수 있습니다. Visual Studio 클라우드 탐색기로 다른 Azure 리소스와 함께 논리 앱을 찾고 관리할 수 있습니다. 예를 들어 Azure Portal에서 이미 배포된 논리 앱을 열고, 다운로드하고, 편집하고, 실행하고, 실행 기록을 보고, 해제하고, 설정할 수 있습니다. Visual Studio에서 Azure Logic Apps를 작업하는 데 익숙하지 않다면 [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)를 참조하세요.

[Azure Portal에서 논리 앱을 관리](manage-logic-apps-with-azure-portal.md)할 수도 있습니다.

> [!IMPORTANT]
> Visual Studio에서 논리 앱을 배포하거나 게시하면 Azure Portal에서 해당 앱의 버전을 덮어씁니다. 따라서 Azure Portal에서 변경한 내용을 계속 유지하려면 다음에 Visual Studio에서 배포 또는 게시하기 전에 Azure Portal에서 [Visual Studio를 사용하여 논리 앱을 새로 고침](#refresh)해야 합니다.

<a name="requirements"></a>

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 다음 도구가 없으면 다운로드하여 설치합니다.

  * [Visual Studio 2019, 2017 또는 2015 - Community Edition 이상](https://aka.ms/download-visual-studio). 이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2017을 사용합니다.

    > [!IMPORTANT]
    > Visual Studio 2019 또는 2017을 설치할 때는 **Azure 개발** 워크로드를 선택해야 합니다.
    > 자세한 내용은 [Visual Studio 클라우드 탐색기에서 Azure 계정과 연결된 리소스 관리](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer)를 참조하세요.

    Visual Studio 2015용 클라우드 탐색기를 설치하려면 [Visual Studio Marketplace에서 클라우드 탐색기를 다운로드](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)합니다. 자세한 내용은 [Visual Studio 클라우드 탐색기(2015)에서 Azure 계정과 연결된 리소스 관리](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true)를 참조하세요.

  * [Azure SDK(2.9.1 이상)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * 원하는 버전에 대한 Visual Studio 확장용 최신 Azure Logic Apps 도구는 다음과 같습니다.

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Visual Studio Marketplace에서 직접 Azure Logic Apps 도구를 다운로드해 설치하거나 [Visual Studio 내에서 이 확장을 설치하는 방법](/visualstudio/ide/finding-and-using-visual-studio-extensions)을 알아볼 수 있습니다. 설치를 완료하면 Visual Studio를 다시 시작하도록 합니다.

  * Visual Studio에서 Azure Government 구독을 사용하려면 추가 설치에 대한 다음 항목을 참조하세요.

    * Visual Studio 2019: [빠른 시작: Visual Studio를 사용하여 Azure Government에 연결](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector)에서 다운로드하여 설치할 수 있는 [Azure 환경 선택기 Visual Studio 확장 소개](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/).

* 포함된 Logic Apps 디자이너를 사용하는 동안 웹에 액세스

  디자이너가 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해서는 인터넷 연결이 필요합니다.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Logic Apps 찾기

Visual Studio에서 클라우드 탐색기를 사용하여 Azure 구독에 연결되고 Azure Portal에서 배포된 모든 논리 앱을 찾을 수 있습니다.

1. Visual Studio를 엽니다. **보기** 메뉴에서 **클라우드 탐색기** 를 선택합니다.

1. 클라우드 탐색기에서 **계정 관리** 아이콘을 선택합니다. 논리 앱과 연결된 Azure 구독을 선택하고 **적용** 을 선택합니다. 예를 들면 다음과 같습니다.

   ![“계정 관리”를 선택합니다.](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. **계정 관리** 아이콘 옆에 있는 **리소스 종류** 를 선택합니다. Azure 구독에서 **Logic Apps** 를 확장하여 구독과 연결된 모든 배포된 논리 앱을 볼 수 있습니다.

그런 다음 논리 앱 편집기에서 논리 앱을 엽니다.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Visual Studio에서 논리 앱 열기

Visual Studio에서 Azure Portal을 통해 직접 또는 Visual Studio에서 Azure Resource Group 프로젝트로 만들고 배포한 논리 앱을 열 수 있습니다.

1. [클라우드 탐색기를 열고 논리 앱을 찾습니다](#find-logic-apps-vs).

1. 논리 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기** 를 선택합니다.

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

   ![Azure Portal에서 배포된 논리 앱 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Logic Apps 디자이너에서 논리 앱이 열린 후 디자이너의 맨 아래에서 **코드 보기** 를 선택하면 기본 논리 앱 정의 구조를 검토할 수 있습니다. 논리 앱에 대한 배포 템플릿을 만들려면 해당 논리 앱에 대한 [Azure Resource Manager 템플릿을 다운로드하는 방법](#download-logic-app)을 살펴보세요. [Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)에 대해 자세히 알아보세요.

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Azure에서 다운로드

[Azure Portal](https://portal.azure.com)에서 논리 앱을 [다운로드](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource)하여 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 템플릿으로 저장할 수 있습니다. 그런 다음, Visual Studio를 사용하여 로컬로 템플릿을 편집하고 다른 배포 환경에 적합하도록 사용자 지정할 수 있습니다.  논리 앱을 자동으로 다운로드하면 해당 정의가 [Resource Manager 템플릿](../azure-resource-manager/templates/overview.md) 내에서 *매개 변수화* 되며, 템플릿도 JSON(JavaScript Object Notation)을 사용합니다.

1. Visual Studio에서 클라우드 탐색기를 사용하여 [Azure에서 다운로드하려는 논리 앱을 엽니다](#open-designer).

1. 논리 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기** 를 선택합니다.

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트가 있는지 확인합니다.

   Logic Apps 디자이너에서 논리 앱이 열립니다.

1. 디자이너 도구 모음에서 **다운로드** 를 선택합니다.

   ![Azure Portal에서 논리 앱 다운로드](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. 위치를 묻는 메시지가 표시되면 해당 위치로 이동하여 논리 앱 정의에 대한 Resource Manager 템플릿을 JSON(.json) 파일 형식으로 저장합니다.

   논리 앱 정의는 Resource Manager 템플릿 내부의 `resources` 하위 섹션에 표시됩니다. 이제 Visual Studio를 사용하여 논리 앱 정의 및 Resource Manager 템플릿을 편집할 수 있습니다. 또한 템플릿을 Visual Studio 솔루션에 [Azure Resource Group 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)로 추가할 수 있습니다. [Visual Studio의 논리 앱에 대한 Azure Resource Group 프로젝트](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)에 대해 알아보세요.

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>통합 계정에 연결

B2B(Business-to-Business) 엔터프라이즈 통합 시나리오에 대한 논리 앱을 구축하기 위해 논리 앱과 동일한 지역에 있는 이전에 만든 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)에 논리 앱을 연결할 수 있습니다. 통합 계정은 거래 업체, 규약, 스키마, 맵과 같은 B2B 아티팩트를 포함하고 있으므로 논리 앱에서 XML 유효성 검사 및 플랫 파일 인코딩 또는 디코딩에 B2B 커넥터를 사용할 수 있습니다. [Azure Portal을 사용하여 이 링크를 만들 수](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) 있지만, [필수 구성 요소](#requirements)를 충족하고 논리 앱이 [Azure Resource Group 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)내에 JSON(json) 파일로 존재하면 Visual Studio를 사용할 수도 있습니다. [Visual Studio의 논리 앱에 대한 Azure Resource Group 프로젝트](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)에 대해 알아보세요.

1. Visual Studio에서 솔루션 또는 논리 앱이 포함된 Azure Resource Group 프로젝트를 엽니다.

1. 솔루션 탐색기 **\<logic-app-name\> .json** 파일의 바로 가기 메뉴를 열고 논리 **앱 디자이너로 열기를** 선택합니다. (키보드: Ctrl + L)

   ![Logic Apps 디자이너로 논리 앱 .json 파일 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트 및 Azure Logic Apps 도구 확장이 있는지 확인합니다.

1. 속성 창에 논리 앱에 대한 **통합 계정** 속성이 표시되도록 디자이너의 탭 또는 화면을 선택하여 Logic Apps 디자이너에 포커스가 있는지 확인합니다.

   ![속성 창 - “통합 계정” 속성](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > 속성 창 아직 열려 있지 않으면 **보기** 메뉴에서 **속성 창** 을 선택합니다. (키보드: F4 키 누르기)

1. **통합 계정** 속성 목록을 열고 논리 앱에 연결하려는 통합 계정을 선택합니다. 예를 들면 다음과 같습니다.

   ![“통합 계정” 속성 목록 열기](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. 완료되면 Visual Studio 솔루션을 저장해야 합니다.

Visual Studio에서 **통합 계정** 속성을 설정하고 논리 앱을 Azure Resource Manager 템플릿으로 저장하는 경우 해당 템플릿에 선택한 통합 계정에 대한 매개 변수 선언도 포함됩니다. 템플릿 매개 변수 및 논리 앱에 대한 자세한 내용은 [개요: 논리 앱 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)를 참조하세요.

<a name="change-location"></a>

## <a name="change-deployment-location"></a>배포 위치 변경

Visual Studio에서 논리 앱이 배포를 자동화하는 데 사용하는 [Azure Resource Group 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) 내에 JSON(.json) 파일로 있는 경우 해당 논리 앱은 위치 유형 및 특정 위치로 설정됩니다. 이 위치는 Azure 지역이거나 기존 [ISE(통합 서비스 환경)](connect-virtual-network-vnet-isolated-environment.md)입니다.

논리 앱의 위치 유형 또는 위치를 변경하려면 Logic Apps 디자이너를 사용하여 솔루션 탐색기에서 논리 앱의 워크플로 정의(json) 파일을 열어야 합니다. 클라우드 탐색기를 사용하여 이러한 속성을 변경할 수 없습니다.

> [!IMPORTANT]
> 위치 유형을 **지역** 에서 [**통합 서비스 환경**](connect-virtual-network-vnet-isolated-environment-overview.md)으로 변경하면 청구, [제한](logic-apps-limits-and-config.md#integration-account-limits), [통합 계정 지원](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus) 등에 사용되는 논리 앱의 [가격 책정 모델](logic-apps-pricing.md#ise-pricing)에 영향을 줍니다. 다른 위치 유형을 선택하기 전에 논리 앱에 미치는 영향을 이해해야 합니다.

1. Visual Studio에서 솔루션 또는 논리 앱이 포함된 Azure Resource Group 프로젝트를 엽니다.

1. 솔루션 탐색기에서 `<logic-app-name>.json` 파일의 바로 가기 메뉴를 연 다음, **Logic Apps 디자이너로 열기** 를 선택합니다. (키보드: Ctrl + L)

   ![Logic Apps 디자이너로 논리 앱 .json 파일 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio에 대한 최신 업데이트 및 Azure Logic Apps 도구 확장이 있는지 확인합니다.

1. 속성 창에 논리 앱에 대한 **위치 유형 선택** 및 **위치** 속성이 표시되도록 디자이너의 탭 또는 화면을 선택하여 Logic Apps 디자이너에 포커스가 있는지 확인합니다. 프로젝트의 위치 유형은 **지역** 또는 **통합 서비스 환경** 으로 설정됩니다.

   ![속성 창 - “위치 유형 선택” 및 “위치” 속성](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > 속성 창 아직 열려 있지 않으면 **보기** 메뉴에서 **속성 창** 을 선택합니다. (키보드: F4 키 누르기)

1. 위치 유형을 변경하려면 **위치 유형 선택** 속성 목록을 열고 원하는 위치 유형을 선택합니다.

   예를 들어 위치 유형이 **통합 서비스 환경** 인 경우 **지역** 을 선택할 수 있습니다.

   ![“위치 유형 선택” 속성 - 위치 유형 변경](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. 특정 위치를 변경하려면 **위치** 속성 목록을 엽니다. 위치 유형에 따라 원하는 위치를 선택합니다. 예를 들면 다음과 같습니다.

   * 다른 Azure 지역 선택:

     ![“위치” 속성 목록을 열고 다른 Azure 지역을 선택합니다.](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * 다른 ISE 선택:

     ![“위치” 속성 목록을 열고 다른 ISE를 선택합니다.](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. 완료되면 Visual Studio 솔루션을 저장해야 합니다.

Visual Studio에서 위치 유형 또는 위치를 변경하고 논리 앱을 Azure Resource Manager 템플릿으로 저장하는 경우 해당 템플릿에는 해당 위치 유형 및 위치에 대한 매개 변수 선언도 포함됩니다. 템플릿 매개 변수 및 논리 앱에 대한 자세한 내용은 [개요: 논리 앱 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)를 참조하세요.

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Azure에서 새로 고침

Azure Portal에서 논리 앱을 편집하고 변경 내용을 유지하려면 Visual Studio에서 해당 변경 내용으로 앱 버전을 새로 고침해야 합니다.

* Visual Studio의 Logic Apps 디자이너 도구 모음에서 **새로 고침** 을 선택합니다.

  또는

* Visual Studio 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **새로 고침** 을 선택합니다.

![업데이트로 논리 앱 새로 고침](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>논리 앱 업데이트 게시

Visual Studio에서 Azure로 논리 앱 업데이트를 배포할 준비가 완료되면 Logic Apps 디자이너 도구 모음에서 **게시** 를 선택합니다.

![Azure Portal에 업데이트된 논리 앱 게시](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>수동으로 논리 앱 실행

Azure에 배포된 논리 앱을 Visual Studio에서 수동으로 트리거할 수 있습니다. Logic Apps 디자이너 도구 모음에서 **트리거 실행** 을 선택합니다.

![논리 앱에 대한 트리거를 수동으로 실행](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>실행 기록 검토

논리 앱 실행 상태를 확인하고 문제를 진단하려면 Visual Studio에서 해당 실행에 대한 입력 및 출력 같은 세부 정보를 검토하면 됩니다.

1. 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **실행 기록 열기** 를 선택합니다.

   ![논리 앱에 대한 실행 기록 열기](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. 특정 실행에 대한 세부 정보를 보려면 실행을 두 번 클릭합니다. 예를 들면 다음과 같습니다.

   ![특정 실행에 대한 정보 보기](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > 속성별로 테이블을 정렬하려면 해당 속성의 열 헤더를 선택합니다.

1. 입력 및 출력을 검토할 단계를 확장합니다. 예를 들면 다음과 같습니다.

   ![각 단계에 대한 입력 및 출력 보기](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

<a name="disable-enable-logic-apps"></a>

## <a name="disable-or-enable-logic-apps"></a>논리 앱 사용 또는 사용하지 않도록 설정

다음번에 트리거 조건이 충족되더라도 트리거가 발생하지 않게 하려면 논리 앱을 사용하지 않도록 설정합니다. 논리 앱을 사용하지 않도록 설정하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Logic Apps 서비스는 완료될 때까지 모든 진행 중인 실행 및 보류 중인 실행을 계속합니다. 볼륨 또는 백로그에 따라 이 프로세스를 완료하는 데 시간이 걸릴 수 있습니다.

* Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 트리거는 다음에 조건이 충족될 때 발생하지 않습니다.

* 트리거 상태는 논리 앱이 중지된 지점을 기억합니다. 따라서 논리 앱을 다시 활성화하면 마지막 실행 이후 처리되지 않은 모든 항목에 대해 트리거가 발생합니다.

  트리거가 마지막 실행 이후 처리되지 않은 항목에서 발생하지 않도록 하려면 논리 앱을 다시 활성화하기 전에 트리거의 상태를 지우세요.

  1. 논리 앱에서 워크플로의 트리거 일부를 편집합니다.
  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 다시 설정합니다.
  1. [논리 앱을 다시 활성화](#enable-logic-apps)합니다.

<a name="disable-logic-apps"></a>

### <a name="disable-logic-apps"></a>논리 앱을 사용하지 않도록 설정

클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **사용 안 함** 을 선택합니다.

![클라우드 탐색기에서 논리 앱을 사용하지 않도록 설정](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

<a name="enable-logic-apps"></a>

### <a name="enable-logic-apps"></a>논리 앱을 사용하도록 설정

클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **사용** 을 선택합니다.

![클라우드 탐색기에서 논리 앱을 사용하도록 설정](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

<a name="delete-logic-apps"></a>

## <a name="delete-logic-apps"></a>논리 앱 삭제

논리 앱을 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Logic Apps 서비스가 진행 중이거나 보류 중인 실행을 취소하기 위해 전력을 다합니다.

  볼륨이나 백로그가 큰 경우에도 대부분의 실행은 완료 또는 시작 전에 취소됩니다. 그러나 취소 프로세스를 완료하는 데 시간이 걸릴 수 있습니다. 한편, 런타임이 취소 프로세스를 진행하는 동안 일부 실행을 선택하여 진행할 수 있습니다.

* Logic Apps 서비스는 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 워크플로를 삭제한 다음, 동일한 워크플로를 다시 만들면 다시 생성된 워크플로에는 삭제된 워크플로와 동일한 메타데이터가 포함되지 않습니다. 삭제된 워크플로를 호출한 모든 워크플로를 다시 저장해야 합니다. 이렇게 하면 호출자가 다시 생성된 워크플로에 대한 올바른 정보를 가져옵니다. 그렇지 않으면 다시 생성된 워크플로에 대한 호출이 `Unauthorized` 오류와 함께 실패합니다. 이 동작은 Azure 함수를 호출하는 워크플로 및 통합 계정에서 아티팩트를 사용하는 워크플로에도 적용됩니다.

Azure Portal에서 논리 앱을 삭제하려면 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **삭제** 를 선택합니다.

![Azure Portal에서 논리 앱 삭제](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

## <a name="troubleshooting"></a>문제 해결

Logic Apps 디자이너에서 논리 앱 프로젝트를 열 때 Azure 구독을 선택하는 옵션이 제공되지 않을 수 있습니다. 그 대신, 사용하려는 Azure 구독이 아닌 다른 구독으로 논리 앱이 열립니다. 사용자가 논리 앱의 .json 파일을 연 후 Visual Studio가 처음에 선택된 구독을 나중에 사용하기 위해 캐시하기 때문에 이 동작이 발생합니다. 이 문제를 해결하려면 다음 단계 중 하나를 수행합니다.

* 논리 앱의 .json 파일 이름을 바꿉니다. 구독 캐시는 파일 이름에 따라 달라집니다.

* 솔루션의 ‘모든’ 논리 앱에 대해 이전에 선택한 구독을 제거하려면 솔루션의 디렉터리에서 숨겨진 Visual Studio 설정 폴더(.vs)를 삭제합니다. 이 위치에 구독 정보가 저장됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Visual Studio를 사용하여 배포된 논리 앱을 관리하는 방법을 배웠습니다. 다음으로 배포에 대한 논리 앱 정의 사용자 지정에 대해 알아보겠습니다.

> [!div class="nextstepaction"]
> [JSON에서 논리 앱 정의를 작성](../logic-apps/logic-apps-author-definitions.md)
