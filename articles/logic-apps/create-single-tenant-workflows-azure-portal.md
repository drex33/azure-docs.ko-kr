---
title: Azure Portal에서 단일 테넌트 Azure Logic Apps(표준)를 사용하여 워크플로 만들기
description: Azure Portal에서 단일 테넌트 Azure Logic Apps(표준)를 사용하여 앱, 데이터, 서비스 및 시스템을 통합하는 자동 워크플로를 만듭니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 507e7a97c0ec884580b0a29fd8a8691035221751
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085983"
---
# <a name="create-an-integration-workflow-with-single-tenant-azure-logic-apps-standard-in-the-azure-portal"></a>Azure Portal에서 단일 테넌트 Azure Logic Apps(표준)를 사용하여 통합 워크플로 만들기

이 문서에서는 **논리 앱(표준)** 리소스 종류 및 Azure Portal을 사용하여 *단일 테넌트* Azure Logic Apps 환경에서 실행되는 자동 통합 워크플로 예제를 만드는 방법을 설명합니다. 이 리소스 종류는 여러 [상태 저장 및 상태 비저장 워크플로](single-tenant-overview-compare.md#stateful-stateless)를 호스트할 수 있습니다. 또한 동일한 논리 앱과 테넌트에 있는 워크플로는 다시 설계된 Azure Logic Apps 런타임과 동일한 프로세스에서 실행되므로 같은 리소스를 공유하고 더 우수한 성능을 제공합니다. 단일 테넌트 Azure Logic Apps 제품에 대한 자세한 내용은 [단일 테넌트 및 다중 테넌트 비교와 통합 서비스 환경](single-tenant-overview-compare.md)을 검토하세요.

이 예제 워크플로는 클라우드 기반이며 두 단계로만 구성되어 있지만, 클라우드, 온-프레미스 및 하이브리드 환경에서 다양한 앱, 데이터, 서비스 및 시스템을 연결할 수 있는 수백 개 작업에서 워크플로를 만들 수 있습니다. 예제 워크플로는 기본 제공 요청 트리거로 시작되며 Office 365 Outlook 작업을 따릅니다. 트리거는 호출 가능한 워크플로 엔드포인트를 만들고 호출자의 인바운드 HTTPS 요청을 기다립니다. 트리거가 요청을 수신하고 작동하면, 다음 작업이 트리거에서 선택한 출력과 함께 특정 이메일 주소로 이메일을 전송하여 실행됩니다.

> [!TIP]
> Office 365 계정이 없으면 이메일 계정(예: Outlook.com)에서 메시지를 보낼 수 있는 사용 가능한 작업을 사용하면 됩니다.
>
> Visual Studio Code에서 이 예제 워크플로를 만들려면 [단일 테넌트 Azure Logic Apps와 Visual Studio Code를 사용하여 통합 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md)의 단계를 수행하세요. 
> 두 옵션 모두 동일한 종류의 환경에서 논리 앱 워크플로를 개발, 실행 및 배포하는 기능을 제공합니다. 
> 하지만 Visual Studio Code를 사용하면 개발 환경에서 로컬로 워크플로를 개발, 테스트 및 실행할 수 있습니다.

!["논리 앱(표준)" 리소스의 워크플로 디자이너가 표시된 Azure Portal을 보여주는 스크린샷.](./media/create-single-tenant-workflows-azure-portal/azure-portal-logic-apps-overview.png)

진행하면서 다음과 같은 상위 수준 작업을 완료하게 됩니다.

* 새 논리 앱 리소스를 만들고 빈 [상태 저장](single-tenant-overview-compare.md#stateful-stateless) 워크플로를 추가합니다.
* 트리거 및 작업을 추가합니다.
* 워크플로 실행을 트리거합니다.
* 워크플로의 실행 및 트리거 기록을 봅니다.
* 배포 후 Application Insights를 사용하도록 설정하거나 엽니다.
* 상태 비저장 워크플로에 실행 기록을 사용하도록 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* [Azure Storage 계정](../storage/common/storage-account-overview.md). 스토리지 계정이 없는 경우, 논리 앱을 만들 때 또는 그 전에 만들 수 있습니다.

  > [!NOTE]
  > **논리 앱(표준)** 리소스 종류는 Azure Functions에서 제공하며, [함수 앱과 유사한 스토리지 요구사항](../azure-functions/storage-considerations.md)이 있습니다. 
  > [상태 저장 워크플로](single-tenant-overview-compare.md#stateful-stateless)는 큐를 사용하여 워크플로 상태를 예약하고 테이블 및 Blob에 저장하는 등의 스토리지 트랜잭션을 수행합니다. 이러한 트랜잭션은 [스토리지 요금](https://azure.microsoft.com/pricing/details/storage/)을 발생시킵니다. 상태 저장 워크플로가 외부 스토리지에 데이터를 저장하는 방법에 대한 자세한 내용은 [상태 저장 및 상태 비저장 워크플로](single-tenant-overview-compare.md#stateful-stateless)를 참조하세요.

* 이 문서와 동일한 예제 워크플로를 만들려면 Microsoft 회사 또는 학교 계정을 사용하여 로그인하는 Office 365 Outlook 이메일 계정이 필요합니다.

  Outlook.com과 같은 [다른 이메일 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors)를 선택하면 계속 예제를 수행할 수 있으며 일반적인 전체 단계는 동일합니다. 그러나 몇 가지 방식으로 서로 다를 수 있습니다. 예를 들어 Outlook.com 커넥터를 사용하는 경우 개인 Microsoft 계정을 대신 사용하여 로그인합니다.

* 이 문서의 예제 워크플로를 테스트하려면 요청 트리거에서 만든 엔드포인트에 호출을 보낼 수 있는 도구가 필요합니다. 해당 도구가 없는 경우 [Postman](https://www.postman.com/downloads/)을 다운로드하여 설치하고 사용할 수 있습니다.

* [Application Insights](../azure-monitor/app/app-insights-overview.md) 사용을 지원하는 설정으로 논리 앱 리소스를 만드는 경우, 필요에 따라 논리 앱에 진단 로깅 및 추적을 사용하도록 설정할 수 있습니다. 논리 앱을 만들 때 또는 배포 후에 설정할 수 있습니다. Application Insights 인스턴스가 필요하지만, 논리 앱을 만들 때 [미리](../azure-monitor/app/create-workspace-resource.md) 또는 배포 후에 이 리소스를 만들 수 있습니다.

* **논리 앱 (표준)** 리소스를 [App Service Environment v3 (ASEv3)](../app-service/environment/overview.md)에 배포 하려면 먼저이 환경 리소스를 만들어야 합니다. 그런 다음 논리 앱 리소스를 만들 때이 환경을 배포 위치로 선택할 수 있습니다. 자세한 내용은 [리소스 유형 및 환경](single-tenant-overview-compare.md#resource-environment-differences) 을 검토 하 고 [App Service Environment를 만듭니다](../app-service/environment/creation.md).

<a name="create-logic-app-resource"></a>

## <a name="create-the-logic-app-resource"></a>논리 앱 리소스 만들기

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정 자격 증명을 사용하여 로그인합니다.

1. Azure Portal 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

   !["논리 앱" 검색 용어와 "논리 앱" 그룹이 선택 된 Azure Portal 검색 상자를 보여 주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/find-logic-app-resource-template.png)

1. **Logic Apps** 페이지에서 **추가** 를 선택합니다.

1. **논리 앱 만들기** 페이지의 **기본** 탭에서 논리 앱 리소스에 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | 논리 앱에 사용할 Azure 구독입니다. |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 논리 앱 및 관련 리소스를 만드는 Azure 리소스 그룹입니다. 이 리소스 이름은 모든 Azure 지역에서 고유해야 하고 문자, 숫자, 하이픈( **-** ), 밑줄( **_** ), 괄호( **()** ) 및 마침표( **.** )만 포함할 수 있습니다. <p><p>이 예제에서는 `Fabrikam-Workflows-RG`라는 리소스 그룹을 만듭니다. |
   | **형식** | 예 | **Standard** | 이 논리 앱 리소스 종류는 단일 테 넌 트 Azure Logic Apps 환경에서 실행 되며 [표준 사용량, 청구 및 가격 책정 모델](logic-apps-pricing.md#standard-pricing)을 사용 합니다. |
   | **논리 앱 이름** | 예 | <*logic-app-name*> | 논리 앱에 사용할 이름입니다. 이 리소스 이름은 모든 Azure 지역에서 고유해야 하고 문자, 숫자, 하이픈( **-** ), 밑줄( **_** ), 괄호( **()** ) 및 마침표( **.** )만 포함할 수 있습니다. <p><p>이 예제에서는 `Fabrikam-Workflows`라는 논리 앱을 만듭니다. <p><p>**참고**: 논리 앱 `.azurewebsites.net` **(표준)** 리소스는 Azure Functions 확장성 모델을 사용 하 고 Azure Functions 런타임에서 확장으로 호스트 되는 단일 테 넌 트 Azure Logic Apps 런타임으로 구동 되기 때문에 논리 앱의 이름은 자동으로 접미사를 가져옵니다. Azure Functions는 동일한 앱 명명 규칙을 사용 합니다. |
   | **게시** | 예 | <*deployment-environment*> | 논리 앱의 배포 대상입니다. 기본적으로 **워크플로** 는 단일 테넌트 Azure Logic Apps에 배포하도록 선택됩니다. Azure에서 첫 번째 워크플로를 추가해야 하는 빈 논리 앱 리소스를 만듭니다. <p><p>**참고**: 현재 **Docker 컨테이너** 옵션을 사용하려면 Azure Arc 지원 Kubernetes 클러스터에서 [*사용자 지정 위치*](../azure-arc/kubernetes/conceptual-custom-locations.md)가 필요하며 [Azure Arc 지원 Logic Apps(미리 보기)](azure-arc-enabled-logic-apps-overview.md)와 함께 사용할 수 있습니다. 논리 앱, 사용자 지정 위치 및 클러스터의 리소스 위치는 모두 동일해야 합니다. |
   | **지역** | 예 | <*Azure-region*> | 리소스 그룹 및 리소스를 만들 때 사용할 위치입니다. 이 예제에서는 샘플 논리 앱을 Azure에 배포하고 **미국 서부** 를 사용합니다. <p>- **Docker 컨테이너** 를 선택한 경우 사용자 지정 위치를 선택 합니다. <p>-먼저 존재 해야 하는 [ASEv3](../app-service/environment/overview.md) 리소스에 배포 하려면 **지역** 목록에서 해당 환경 리소스를 선택 합니다. |
   |||||

   다음 예제에서는 **논리 앱 만들기(표준)** 페이지를 보여 줍니다.

   ![Azure Portal 및 "논리 앱 만들기" 페이지를 보여주는 스크린샷.](./media/create-single-tenant-workflows-azure-portal/create-logic-app-resource-portal.png)

1. **호스팅** 탭에서 논리 앱에 사용할 수 있도록 스토리지 솔루션 및 호스팅 계획에 관한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | 설명 |
   |----------|----------|-------|-------------|
   | **스토리지 유형** | Yes | - **SQL 및 Azure Storage** <br>- **Azure Storage** | 워크플로 관련 아티팩트 및 데이터에 사용 하려는 저장소 유형입니다. <p><p>-Azure에만 배포 하려면 **Azure Storage** 을 선택 합니다. <p><p>-SQL를 기본 저장소로 사용 하 고 보조 저장소로 Azure Storage을 사용 하려면 **SQL 및 Azure Storage** 를 선택 하 고 [단일 테 넌 트 SQL에서 표준 논리 앱에 대 한 Azure Logic Apps 데이터베이스 저장소 설정](set-up-sql-db-storage-single-tenant-standard-workflows.md)을 검토 합니다. <p><p>**참고**: azure 지역에 배포 하는 경우에도 azure storage 계정이 필요 합니다 .이 계정은 Azure Logic Apps 플랫폼에서 논리 앱의 구성에 대 한 일회성 호스팅을 완료 하는 데 사용 됩니다. 진행 중인 워크플로 상태, 실행 기록 및 기타 런타임 아티팩트가 SQL 데이터베이스에 저장 됩니다. <p><p>Azure Arc 클러스터에서 호스트 되는 사용자 지정 위치에 대 한 배포의 경우 저장소 공급자로 SQL 필요 합니다. |
   | **스토리지 계정** | 예 | <*Azure-storage-account-name*> | 스토리지 트랜잭션에 사용할 [Azure Storage 계정](../storage/common/storage-account-overview.md)입니다. <p><p>이 리소스 이름은 모든 지역에서 고유해야 하며 숫자와 소문자만 포함된 3-24자여야 합니다. 기존 계정을 선택하거나 새 계정을 만듭니다. <p><p>이 예제에서는 `fabrikamstorageacct`라는 스토리지 계정을 만듭니다. |
   | **플랜 유형** | 예 | <호스팅 계획> | 논리 앱을 배포하는 데 사용할 호스팅 계획입니다. <p><p>자세한 내용은 [호스팅 계획과 가격 책정 계층](logic-apps-pricing.md#standard-pricing)을 참조하세요. |
   | **Windows 플랜** | 예 | <*plan-name*> | 사용할 계획 이름입니다. 기존 계획 이름을 선택하거나 새 계획의 이름을 입력합니다. <p><p>이 예제에서는 `Fabrikam-Service-Plan`이름을 사용합니다. |
   | **SKU 및 크기** | 예 | <*pricing-tier*> | 논리 앱에 사용할 [가격 책정 계층](../app-service/overview-hosting-plans.md)입니다. 선택한 내용에 따라 논리 앱과 워크플로에서 사용하는 가격 책정, 컴퓨팅, 메모리 및 스토리지에 영향이 있습니다. <p><p>기본 가격 책정 계층을 변경하려면 **크기 변경** 을 선택합니다. 그런 다음, 필요한 워크로드에 따라 다른 가격 책정 계층을 선택할 수 있습니다. <p><p>자세한 내용은 [호스팅 계획과 가격 책정 계층](logic-apps-pricing.md#standard-pricing)을 참조하세요. |
   |||||

1. 다음으로, 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md) 사용을 지원하는 경우 필요에 따라 논리 앱에 진단 로깅 및 추적을 사용하도록 설정할 수 있습니다.

   1. **모니터링** 탭의 **Application Insights** 에서 **Application Insights 사용** 을 **예** 로 설정합니다(아직 설정하지 않은 경우).

   1. **Application Insights** 설정에서 기존 Application Insights 인스턴스를 선택하거나, 새 인스턴스를 만들려는 경우 **새로 만들기** 를 선택하고 사용할 이름을 입력합니다.

1. Azure에서 논리 앱의 설정에 대 한 유효성을 검사 한 후 **검토 + 만들기** 탭에서 **만들기** 를 선택 합니다. 예를 들면 다음과 같습니다.

   ![Azure Portal 및 새 논리 앱 리소스 설정을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/check-logic-app-resource-settings.png)

   > [!TIP]
   > 이 단계 후에 유효성 검사 오류가 발생 하면를 열고 오류 정보를 검토 합니다. 예를 들어 선택한 지역이 만들려는 리소스의 할당량에 도달하는 경우 다른 지역을 시도해야 할 수도 있습니다.

   Azure에서 배포를 완료하면 논리 앱이 자동으로 활성화되어 실행되지만, 리소스가 비어 있고 아직 워크플로를 추가하지 않았기 때문에 아직 아무런 작업도 수행하지 않습니다.

1. 빈 워크플로를 추가할 수 있도록 배포 완료 페이지에서 **리소스로 이동** 을 선택합니다.

   ![Azure Portal 및 완료된 배포를 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/logic-app-completed-deployment.png)

<a name="add-workflow"></a>

## <a name="add-a-blank-workflow"></a>빈 워크플로 추가

빈 논리 앱 리소스를 만든 후 첫 번째 워크플로를 추가해야 합니다.

1. Azure가 리소스를 연 후, 논리 앱의 메뉴에서 **워크플로** 를 선택합니다. **워크플로** 도구 모음에서 **추가** 를 선택합니다.

   ![논리 앱 리소스 메뉴에서 "워크플로"를 선택한 다음, 도구 모음에서 "추가"를 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/logic-app-add-blank-workflow.png)

1. **새 워크플로** 창이 열리면 워크플로의 이름을 입력하고, [**상태 저장** 또는 **상태 비저장**](single-tenant-overview-compare.md#stateful-stateless) 중에서 상태 유형을 선택합니다. 완료되면 **만들기** 를 선택합니다.

   이 예제에서는 `Fabrikam-Stateful-Workflow`라는 빈 상태 저장 워크플로를 추가합니다. 기본적으로 워크플로는 사용하도록 설정되지만 사용자가 트리거 및 작업을 추가하기 전에는 아무 것도 수행하지 않습니다.

   ![새로 추가된 빈 상태 저장 워크플로 "Fabrikam-Stateful-Workflow"를 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/logic-app-blank-workflow-created.png)

1. 다음으로, 트리거 및 작업을 추가할 수 있도록 디자이너에서 빈 워크플로를 엽니다.

   1. 워크플로 목록에서 빈 워크플로를 선택합니다.

   1. 워크플로 메뉴의 **개발자** 에서 **디자이너** 를 선택합니다.

      **트리거 추가** 창을 열린 상태로 표시하기 위해 디자이너 화면에서 **작업 선택** 프롬프트가 이미 표시되고 기본적으로 선택되어 있습니다.

      ![디자이너 화면에서 워크플로 디자이너가 열려 있고 "작업 선택"이 선택된 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/opened-logic-app-designer-blank-workflow.png)

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-an-action"></a>트리거 및 작업 추가

이 예제에서는 다음 단계로 구성되는 워크플로를 빌드합니다.

* 기본 제공 [요청 트리거](../connectors/connectors-native-reqres.md) - **HTTP 요청** 이 수신될 때 인바운드 호출 또는 요청을 수신하고 다른 서비스 또는 논리 앱이 호출할 수 있는 엔드포인트를 만듭니다.

* [Office 365 Outlook 작업](../connectors/connectors-create-api-office365-outlook.md), **이메일 보내기**.

### <a name="add-the-request-trigger"></a>요청 트리거 추가

빈 워크플로에 트리거를 추가하려면 워크플로 디자이너를 열고 디자이너 화면에서 **작업 선택** 프롬프트를 선택해야 합니다.

1. 디자이너 화면 옆에 있는 **트리거 추가** 창의 **작업 선택** 검색 상자 아래에서 **기본 제공** 탭을 선택합니다. 이 탭은 Azure Logic Apps에서 기본적으로 실행되는 트리거를 표시합니다.

1. **작업 선택** 검색 상자에 `when a http request`을(를) 입력하고 **HTTP 요청을 수신하는 경우** 라는 이름의 기본 제공 요청 트리거를 선택합니다.

   ![디자이너와 "HTTP 요청을 수신하는 경우" 트리거가 선택된 **트리거 추가** 창을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/find-request-trigger.png)

   트리거가 디자이너에 표시되면 트리거의 세부 정보 창이 열리고 트리거의 속성, 설정 및 기타 작업이 표시됩니다.

   !["HTTP 요청을 수신하는 경우" 트리거가 선택되고 트리거 세부 정보 창이 열려 있는 디자이너를 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/request-trigger-added-to-designer.png)

   > [!TIP]
   > 세부 정보 창이 표시되지 않으면 디자이너에서 트리거를 선택했는지 확인합니다.

1. 디자이너에서 항목을 삭제해야 하는 경우 [디자이너에서 항목을 삭제하는 다음 단계를 수행](#delete-from-designer)합니다.

1. 작업을 저장하려면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   워크플로를 처음 저장하고 워크플로가 요청 트리거로 시작하는 경우 Logic Apps 서비스는 요청 트리거에서 만든 엔드포인트의 URL을 자동으로 생성합니다. 나중에 워크플로를 테스트할 때 이 URL로 요청을 보냅니다. 그러면 트리거가 실행되고 워크플로가 실행되기 시작합니다.

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook 작업 추가

1. 디자이너의 추가한 트리거에서 더하기 기호( **+** ) > **작업 선택** 을 선택합니다.

   **작업 선택** 프롬프트가 디자이너에 표시되고 다음 작업을 선택할 수 있도록 **작업 추가** 창이 다시 열립니다.

   > [!NOTE]
   > **작업 추가** 창에 '정의되지 않은 속성 '필터'를 읽을 수 없습니다'라는 오류 메시지가 표시되면 워크플로를 저장하고, 페이지를 다시 로드하고, 워크플로를 다시 열고, 다시 시도하세요.

1. **작업 추가** 창의 **작업 선택** 검색 상자에서 **Azure** 를 선택합니다. 이 탭은 Azure에서 사용 가능한 호스트된 관리형 커넥터를 표시합니다.

   > [!NOTE]
   > **작업 추가** 창에 `The access token expiry UTC time '{token-expiration-date-time}' is earlier than current UTC time '{current-date-time}'` 오류 메시지가 표시되면 워크플로를 저장하고, 페이지를 다시 로드하고, 워크플로를 다시 열고, 작업을 다시 추가해 봅니다.

   이 예제에서는 **이메일 보내기(V2)** 라는 Office 365 Outlook 작업을 사용합니다.

   ![디자이너의 **작업 추가** 창에서 Office 365 Outlook "이메일 보내기" 작업을 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/find-send-email-action.png)

1. 작업 세부 정보 창의 **연결 만들기** 탭에서 **로그인** 을 선택합니다. 그러면 이메일 계정에 연결할 수 있습니다.

   ![디자이너의 "이메일 보내기(V2)" 세부 정보 창에서 "로그인"을 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/send-email-action-sign-in.png)

1. 이메일 계정에 액세스할지를 묻는 메시지가 표시되면 계정 자격 증명을 사용하여 로그인합니다.

   > [!NOTE]
   > `Failed with error: 'The browser is closed.'. Please sign in again` 오류 메시지가 표시되면 브라우저에서 타사 쿠키를 차단하는지 확인합니다. 쿠키가 차단된 경우 쿠키를 사용할 수 있는 사이트 목록에 `https://portal.azure.com`을 추가해 봅니다. 시크릿 모드를 사용하는 경우 이 모드에서 작업하는 동안 타사 쿠키가 차단되지 않도록 합니다.
   > 
   > 필요한 경우 페이지를 다시 로드하고, 워크플로를 열고, 이메일 작업을 다시 추가하고, 연결을 시도합니다.

   Azure가 연결에 성공하면 **이메일 보내기** 작업이 디자이너에 표시되고 기본적으로 선택됩니다. 이 작업이 선택되지 않으면 이 작업을 선택하여 세부 정보 창을 엽니다.

1. 작업 세부 정보 창의 **매개 변수** 탭에서 작업에 필요한 정보를 입력합니다. 예를 들면 다음과 같습니다.

   ![디자이너의 "작업 보내기" 세부 정보 창에서 "매개 변수" 탭을 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/send-email-action-details.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **수행할 작업** | 예 | <*your-email-address*> | 이메일을 받는 사람. 테스트 목적의 이메일 주소를 사용할 수 있습니다. 이 예제에서는 가상의 이메일 주소 `sophiaowen@fabrikam.com`을 사용합니다. |
   | **Subject** | 예 | `An email from your example workflow` | 이메일 제목 |
   | **본문** | 예 | `Hello from your example workflow!` | 이메일 본문 내용 |
   ||||

   > [!NOTE]
   > **설정**, **정적 결과** 또는 **다음 시간 이후에 실행** 의 세부 정보 창에서 변경 작업을 수행한 경우 탭을 전환하거나 포커스를 디자이너로 이동하기 전에 **완료** 를 선택하여 변경 내용을 커밋해야 합니다. 그렇지 않으면 디자이너가 변경 내용을 유지하지 않습니다.

1. 작업을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 현재 환경에 트래픽을 제한하는 엄격한 네트워크 요구 사항 또는 방화벽이 있는 경우 정규화된 도메인 이름을 찾으려면 워크플로에 있는 트리거 또는 작업 연결에 대한 정규화된 도메인 이름을 검색하려면 [방화벽 액세스용 도메인 이름 찾기](#firewall-setup)를 확인하세요.

   그렇지 않고 워크플로를 테스트하려면 [실행을 수동으로 트리거](#trigger-workflow)합니다.

<a name="firewall-setup"></a>

## <a name="find-domain-names-for-firewall-access"></a>방화벽 액세스를 위한 도메인 이름 찾기

Azure Portal에서 논리 앱을 배포하고 워크플로를 실행하기 전에, 현재 환경에 트래픽을 제한하는 엄격한 네트워크 요구 사항 또는 방화벽이 있는 경우 논리 앱에 있는 트리거 또는 작업 연결에 대한 네트워크 또는 방화벽 권한을 워크플로에서 설정해야 합니다.

논리 앱 및 워크플로에서 사용하는 인바운드 및 아웃바운드 IP 주소를 찾으려면 다음 단계를 수행합니다.

1. 논리 앱 메뉴의 **설정** 에서 **네트워킹(미리 보기)** 을 선택합니다.

1. 네트워킹 창에서 **인바운드 트래픽** 및 **아웃바운드 트래픽** 섹션을 찾아 검토합니다.

연결에 대한 FQDN(정규화된 도메인 이름)을 찾으려면 다음 단계를 수행합니다.

1. 논리 앱 메뉴의 **워크플로** 에서 **연결** 을 선택합니다. **API 연결** 탭에서 연결의 리소스 이름을 선택합니다. 예를 들면 다음과 같습니다.

   ![Azure Portal 및 논리 앱 메뉴에서 "연결" 및 "office365" 연결 리소스 이름이 선택된 것을 보여주는 스크린샷.](./media/create-single-tenant-workflows-azure-portal/logic-app-connections.png)

1. 브라우저의 오른쪽 위 모서리에 **JSON 보기** 가 표시되도록 브라우저 너비를 충분히 확장하고 **JSON 보기** 를 선택합니다.

   ![Azure Portal 및 API 연결 창에서 "JSON 보기"를 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-view-json.png)

1. 방화벽을 설정할 때 이 정보를 사용할 수 있도록 `connectionRuntimeUrl` 속성 값을 복사하고 안전한 위치에 저장합니다.

   !["connectionRuntimeUrl" 속성 값을 선택한 스크린샷](./media/create-single-tenant-workflows-azure-portal/logic-app-connection-runtime-url.png)

1. 각 연결에 대해 관련 단계를 반복합니다.

<a name="trigger-workflow"></a>

## <a name="trigger-the-workflow"></a>워크플로 트리거

이 예제의 워크플로는 요청 트리거가 인바운드 요청을 수신할 때 실행되고, 인바운드 요청은 트리거에서 만든 엔드포인트의 URL로 전송됩니다. 워크플로를 처음으로 저장할 때 Logic Apps 서비스가 자동으로 이 URL을 생성합니다. 따라서 이 요청을 보내서 워크플로를 트리거하려면 먼저 이 URL을 찾아야 합니다.

1. 워크플로 디자이너에서 **HTTP 요청을 수신하는 경우** 요청 트리거를 선택합니다.

1. 세부 정보 창이 열리면 **매개 변수** 탭에서 **HTTP POST URL** 속성을 찾습니다. 생성된 URL을 복사하려면 **Rrl 복사**(파일 복사 아이콘)를 선택하고, 지금은 다른 위치에 URL을 저장합니다. URL은 다음 형식을 따릅니다.

   `http://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

   ![디자이너에서 요청 트리거와 "HTTP POST URL" 속성의 엔드포인트 URL을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/find-request-trigger-url.png)

   이 예제의 URL은 다음과 같습니다.

   `https://fabrikam-workflows.azurewebsites.net:443/api/Fabrikam-Stateful-Workflow/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=xxxxxXXXXxxxxxXXXXxxxXXXXxxxxXXXX`

   > [!TIP]
   > **워크플로 URL** 속성의 논리 앱 **개요** 창에서 엔드포인트 URL을 찾는 방법도 있습니다.
   >
   > 1. 리소스 메뉴에서 **개요** 를 선택합니다.
   > 1. **개요** 창에서 **워크플로 URL** 속성을 찾습니다.
   > 1. 엔드포인트 URL을 복사하려면 엔드포인트 URL 텍스트의 끝으로 포인터를 이동하고 **클립보드로 복사**(파일 복사 아이콘)를 선택합니다.

1. 요청을 전송하여 URL을 테스트하려면 요청을 만들고 보낼 수 있는 [Postman](https://www.postman.com/downloads/) 또는 선호하는 도구를 엽니다.

   이 예제에서는 Postman을 사용합니다. 자세한 내용은 [Postman 시작](https://learning.postman.com/docs/getting-started/introduction/)을 참조하세요.

   1. Postman 도구 모음에서 **새로 만들기** 를 선택합니다.

      ![Postman에서 새로 만들기 단추를 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/postman-create-request.png)

   1. **새로 만들기** 창의 **구성 요소** 에서 **요청** 을 선택합니다.

   1. **요청 저장** 창의 **요청 이름** 에서 요청의 이름을 입력합니다(예: `Test workflow trigger`).

   1. **저장할 컬렉션 또는 폴더 선택** 에서 **컬렉션 만들기** 를 선택합니다.

   1. **모든 컬렉션** 에서 요청을 구성하기 위해 만들 컬렉션의 이름을 입력하고 Enter 키를 누른 다음, **<*컬렉션 이름*>에 저장** 을 선택합니다. 이 예제에서는 컬렉션 이름으로 `Logic Apps requests`을(를) 사용합니다.

      Postman에서 요청 트리거의 엔드포인트 URL에 요청을 보낼 수 있도록 요청 창이 열립니다.

      ![요청 창이 열린 Postman을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/postman-request-pane.png)

   1. 요청 창의 메서드 목록 옆에 있는 주소 상자를 보면 현재 기본 요청 메서드가 **GET** 으로 표시됩니다. 앞에서 복사한 URL을 이 주소 상자에 붙여넣고 **보내기** 를 선택합니다.

      ![Postman의 주소 상자 엔드포인트 URL에서 [보내기] 단추를 선택한 것을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/postman-test-endpoint-url.png)

      트리거가 실행되면 이 예제 워크플로가 실행되고 다음과 비슷한 이메일을 보냅니다.

      ![예제에서 설명한 대로 Outlook 이메일을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/workflow-app-result-email.png)

<a name="view-run-history"></a>

## <a name="review-run-history"></a>실행 기록 검토

상태 저장 워크플로의 경우 각 워크플로가 실행된 후에는 입력 및 출력과 함께 전체적인 실행, 트리거 및 각 작업의 상태를 포함한 실행 기록을 볼 수 있습니다. Azure Portal에서 실행 기록 및 트리거 기록은 논리 앱 수준이 아닌 워크플로 수준에서 표시됩니다. 실행 기록 컨텍스트 외부에서 트리거 기록을 검토하려면 [트리거 기록 검토](#view-trigger-histories)를 참조하세요.

1. Azure Portal의 워크플로 메뉴에서 **개요** 를 선택합니다.

1. **개요** 창에서 **실행 기록** 을 선택하면 해당 워크플로의 실행 기록이 표시됩니다.

   ![워크플로의 “개요” 창에서 “실행 기록”이 선택된 것을 보여주는 스크린샷.](./media/create-single-tenant-workflows-azure-portal/find-run-history.png)

   > [!TIP]
   > 최신 실행 상태가 표시되지 않으면 **개요** 창 도구 모음에서 **새로 고침** 을 선택합니다. 조건이 충족되지 않거나 데이터를 찾을 수 없어 트리거를 건너뛴 경우에는 실행이 발생하지 않습니다.

   | 실행 상태 | 설명 |
   |------------|-------------|
   | **중단됨** | 시스템이 중단되거나 Azure 구독이 만료되는 등의 외부 문제로 인해 실행이 중지되었거나 완료되지 않았습니다. |
   | **취소** | 실행이 트리거되고 시작되었지만 취소 요청이 수신되었습니다. |
   | **실패** | 실행에서 하나 이상의 작업이 실패했습니다. 실패를 처리하는 워크플로의 후속 작업이 설정되지 않았습니다. |
   | **실행 중** | 실행이 트리거되어 현재 진행 중이지만, [작업 제한](logic-apps-limits-and-config.md) 또는 [현재 가격 책정 계획](https://azure.microsoft.com/pricing/details/logic-apps/)으로 인해 제한된 실행에도 이 상태가 표시될 수 있습니다. <p><p>**팁**: [진단 로깅](monitor-logic-apps-log-analytics.md)을 설정한 경우 발생하는 모든 제한된 이벤트에 대한 정보를 얻을 수 있습니다. |
   | **성공함** | 실행이 성공했습니다. 작업이 실패하면 워크플로의 후속 작업에서 실패를 처리합니다. |
   | **시간 초과됨** | 현재 기간이 [**실행 기록 보존 기간(일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)을 통해 제어되는 실행 기간 제한을 초과하여 실행 시간이 초과되었습니다. 실행의 기간은 실행의 시작 시간과 해당 시작 시간의 실행 기간 제한을 사용하여 계산됩니다. <p><p>**참고**: 실행 기간이 [**실행 기록 보존 기간(일)** 설정](logic-apps-limits-and-config.md#run-duration-retention-limits)을 통해 제어되는 현재 *실행 기록 보존 제한* 을 초과하는 경우에도 일간 정리 작업을 통해 실행 기록에서 실행이 지워집니다. 실행 시간이 초과되건 실행이 완료되건 보존 기간은 항상 실행의 시작 시간과 *현재* 보존 제한을 사용하여 계산됩니다. 따라서 진행 중인 실행의 기간 제한을 줄이면 실행 시간이 초과됩니다. 그러나 실행이 유지되는지 아니면 기록에서 실행이 지워지는지 여부는 실행 기간이 보존 한도를 초과했는지 여부에 따라 결정됩니다. |
   | **대기 중** | 예를 들어 아직 실행 중인 이전 워크플로 인스턴스 때문에 실행이 시작되지 않았거나 일시 중지되었습니다. |
   |||

1. 실행의 각 단계에 대한 상태를 검토하려면 검토하려는 실행을 선택합니다.

   실행 세부 정보 보기가 열리고 실행의 각 단계에 대한 상태가 표시됩니다.

   ![워크플로의 각 단계에 대한 상태와 함께 실행 세부 정보 보기를 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/review-run-details.png)

   워크플로의 각 단계에서 가능한 상태는 다음과 같습니다:

   | 작업 상태 | 설명 |
   |---------------|-------------|
   | **중단됨** | 시스템이 중단되거나 Azure 구독이 만료되는 등의 외부 문제로 인해 작업이 중지되었거나 완료되지 않았습니다. |
   | **취소** | 작업이 실행 중이지만 취소 요청을 받았습니다. |
   | **실패** | 작업이 실패했습니다. |
   | **실행 중** | 작업이 현재 실행 중입니다. |
   | **생략** | `runAfter` 조건이 충족되지 않았기 때문에(예: 이전 작업 실패) 작업을 건너뛰었습니다. 각 작업에는 `runAfter` 현재 작업을 실행하려면 충족해야 하는 조건을 설정할 수 있는 개체가 있습니다. |
   | **성공함** | 작업이 성공했습니다. |
   | **다시 시도하여 성공함** | 한 번 또는 여러 번 시도한 후에 작업에 성공했습니다. 다시 시도 기록을 검토하려면 실행 기록 세부 정보 보기에서 해당 작업을 선택합니다. 그러면 입력 및 출력을 볼 수 있습니다. |
   | **시간 초과됨** | 해당 작업의 설정에 지정된 제한 시간으로 인해 작업이 중지되었습니다. |
   | **대기 중** | 호출자의 인바운드 요청을 기다리는 웹후크 작업에 적용됩니다. |
   |||

   [aborted-icon]: ./media/create-single-tenant-workflows-azure-portal/aborted.png
   [cancelled-icon]: ./media/create-single-tenant-workflows-azure-portal/cancelled.png
   [failed-icon]: ./media/create-single-tenant-workflows-azure-portal/failed.png
   [running-icon]: ./media/create-single-tenant-workflows-azure-portal/running.png
   [skipped-icon]: ./media/create-single-tenant-workflows-azure-portal/skipped.png
   [succeeded-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-single-tenant-workflows-azure-portal/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-single-tenant-workflows-azure-portal/timed-out.png
   [waiting-icon]: ./media/create-single-tenant-workflows-azure-portal/waiting.png

1. 특정 단계에 대한 입력 및 출력을 검토하려면 해당 단계를 선택합니다.

   ![선택한 "이메일 보내기" 작업의 입력 및 출력을 보여주는 스크린샷](./media/create-single-tenant-workflows-azure-portal/review-step-inputs-outputs.png)

1. 해당 단계에 대한 원시 입력 및 출력을 추가로 검토하려면 **원시 입력 표시** 또는 **원시 출력 표시** 를 선택합니다.

<a name="view-trigger-histories"></a>

## <a name="review-trigger-histories"></a>트리거 기록 검토

상태 저장 워크플로의 경우 [실행 기록 컨텍스트](#view-run-history)와 별도로 입력 및 출력과 함께 트리거 상태를 포함하여 각 실행의 트리거 기록을 검토할 수 있습니다. Azure Portal에서 트리거 기록 및 실행 기록은 논리 앱 수준이 아닌 워크플로 수준에서 표시됩니다. 이 기록 데이터를 찾으려면 다음 단계를 수행합니다.

1. Azure Portal의 워크플로 메뉴에서 **개요** 를 선택합니다.

1. **개요** 페이지에서 **트리거 기록** 을 선택합니다.

   **트리거 기록** 창에는 워크플로의 실행에 대한 트리거 기록이 표시됩니다.

1. 특정 트리거 기록을 검토하려면 해당 실행의 ID를 선택합니다.

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>배포 후 Application Insights를 사용하도록 설정하거나 열기

워크플로가 실행되는 동안 논리 앱은 다른 이벤트와 함께 원격 분석 데이터를 내보냅니다. 이 원격 분석 데이터를 사용하여 워크플로가 얼마나 잘 실행되는지 그리고 Logic Apps 런타임이 얼마나 다양한 방식으로 작동하는지 보다 정확하게 파악할 수 있습니다. 거의 실시간 원격 분석 데이터(라이브 메트릭)를 제공하는 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 워크플로를 모니터링할 수 있습니다. 이 데이터를 사용하여 문제를 진단하고 경고를 설정하고 차트를 작성할 때 이 기능을 통해 오류 및 성능 문제를 보다 쉽게 조사할 수 있습니다.

논리 앱의 만들기 및 배포 설정에서 [Application Insights](../azure-monitor/app/app-insights-overview.md) 사용을 지원하는 경우 필요에 따라 논리 앱에 진단 로깅 및 추적을 사용하도록 설정할 수 있습니다. Azure Portal에서 논리 앱을 만들 때 또는 배포 후에 설정할 수 있습니다. Application Insights 인스턴스가 필요하지만, 논리 앱을 만들 때 [미리](../azure-monitor/app/create-workspace-resource.md) 또는 배포 후에 이 리소스를 만들 수 있습니다.

배포한 논리 앱에서 Application Insights를 사용하도록 설정하거나 이미 사용하도록 설정한 Application Insights 대시보드를 열려면 다음 단계를 수행합니다.

1. Azure Portal에서 배포된 논리 앱을 찾습니다.

1. 논리 앱 메뉴의 **설정** 에서 **Application Insights** 를 선택합니다.

1. Application Insights가 사용하도록 설정되지 않은 경우 **Application Insights** 창에서 **Application Insights 켜기** 를 선택합니다. 창이 업데이트되면 아래쪽에서 **적용** > **예** 를 선택합니다.

   Application Insights가 사용하도록 설정된 경우 **Application Insights** 창에서 **Application Insights 데이터 보기** 를 선택합니다.

Application Insights가 열리면 논리 앱에 대한 다양한 메트릭을 검토할 수 있습니다. 자세한 내용은 다음 항목을 검토하세요:

* [어디서나 실행되는 Azure Logic Apps - Application Insights로 모니터링 - 1부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [어디서나 실행되는 Azure Logic Apps - Application Insights로 모니터링 - 2부](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>상태 비저장 워크플로에 대한 실행 기록 사용 설정

상태 비저장 워크플로를 보다 쉽게 디버그하려면 해당 워크플로에 실행 기록을 사용하도록 설정한 다음, 완료되면 실행 기록을 사용하지 않도록 설정하면 됩니다. Azure Portal에서 다음 단계를 수행하거나, Visual Studio Code를 사용하는 경우 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-single-tenant-workflows-visual-studio-code.md#enable-run-history-stateless)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 **논리 앱(표준)** 리소스를 엽니다.

1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택합니다.

1. **애플리케이션 설정** 탭에서 **새 애플리케이션 설정** 을 선택합니다.

1. **애플리케이션 설정 추가/편집** 창의 **이름** 상자에 다음 작업 옵션 이름을 입력합니다. 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. **값** 상자에 `WithStatelessRunHistory` 값을 입력합니다.

   예를 들면 다음과 같습니다.

   !["구성" > "새 애플리케이션 설정" < "애플리케이션 추가/편집" 창이 열려 있고 "Workflows.{yourWorkflowName}.OperationOptions" 옵션이 "WithStatelessRunHistory"로 설정된 Azure Portal 및 논리 앱(표준) 리소스를 보여주는 스크린샷.](./media/create-single-tenant-workflows-azure-portal/stateless-operation-options-run-history.png)

1. 작업을 마치려면 **확인** 을 선택합니다. **구성** 창 도구 모음에서 **저장** 을 선택합니다.

1. 작업을 마친 후 실행 기록을 사용하지 않도록 설정하려면 `Workflows.{yourWorkflowName}.OperationOptions` 속성을 `None`로 설정하거나 이 속성과 해당 값을 삭제합니다.

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>디자이너에서 항목 삭제

디자이너에서 워크플로의 항목을 삭제하려면 다음 단계 중 하나를 수행합니다:

* 항목을 선택하고 항목의 바로 가기 메뉴(Shift+F10)를 열고 **삭제** 를 선택합니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택하고 삭제 키를 누릅니다. 확인하려면 **확인** 을 선택합니다.

* 항목을 선택하면 해당 항목에 대한 세부 정보 창이 열립니다. 이 창의 오른쪽 위 모서리에서 줄임표( **...** ) 메뉴를 열고 **삭제** 를 선택합니다. 확인하려면 **확인** 을 선택합니다.

  ![디자이너에서 항목을 선택하여 세부 정보 창이 열려 있고 줄임표 단추를 선택하여 "삭제" 명령이 표시된 것을 보여주는 스크린샷입니다.](./media/create-single-tenant-workflows-azure-portal/delete-item-from-designer.png)

  > [!TIP]
  > 줄임표 메뉴가 표시되지 않는 경우 세부 정보 창의 오른쪽 위 모서리에 줄임표( **...** ) 단추가 표시되도록 브라우저 창을 충분히 넓게 확장합니다.

<a name="restart-stop-start"></a>

## <a name="restart-stop-or-start-logic-apps"></a>논리 앱 다시 시작, 중지 또는 시작

[단일 논리 앱](#restart-stop-start-single-logic-app) 또는 [여러 논리 앱을 동시에](#stop-start-multiple-logic-apps) 중지하거나 시작할 수 있습니다. 먼저 중지하지 않고 단일 논리 앱을 다시 시작할 수도 있습니다. 단일 테넌트 기반 논리 앱에 여러 워크플로가 포함될 수 있으므로, 전체 논리 앱을 중지하거나 [워크플로만 사용하지 않도록 설정할](#disable-enable-workflows) 수 있습니다.

> [!NOTE]
> 논리 앱 중지와 워크플로 미사용 설정 작업의 결과는 서로 다릅니다. 자세한 내용은 [논리 앱 중지 고려 사항](#considerations-stop-logic-apps)과 [워크플로 미사용 설정 고려 사항](#disable-enable-workflows)을 참조하세요.

<a name="considerations-stop-logic-apps"></a>

### <a name="considerations-for-stopping-logic-apps"></a>논리 앱 중지 고려 사항

논리 앱을 중지하면 워크플로 인스턴스에 다음과 같은 영향이 있습니다.

* Azure Logic Apps가 진행 중인 실행과 보류 중인 실행을 즉시 취소합니다.

* Azure Logic Apps가 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 트리거가 다음번에 해당 조건이 충족되면 발생하지 않습니다. 하지만 트리거 상태는 논린 앱이 중지된 지점을 기억합니다. 따라서 논리 앱을 다시 시작하면 트리거가 마지막 실행 이후에 처리되지 않은 모든 항목에 대해 발생합니다.

  마지막 실행 후 처리되지 않은 항목에서 각 워크플로가 트리거되지 않게 하려면 다음 단계를 수행하여 트리거 상태를 지운 후에 논리 앱을 다시 시작합니다.

  1. Azure Portal에서 논리 앱을 엽니다.
  1. 논리 앱 메뉴의 **워크플로** 에서 **워크플로** 를 선택합니다.
  1. 워크플로를 열고 해당 워크플로 트리거의 모든 부분을 편집합니다.
  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 재설정합니다.
  1. 워크플로마다 반복합니다.
  1. 완료되면 [논리 앱을 다시 시작합니다](#restart-stop-start-single-logic-app).

<a name="restart-stop-start-single-logic-app"></a>

### <a name="restart-stop-or-start-a-single-logic-app"></a>단일 논리 앱 다시 시작, 중지 또는 시작

1. Azure Portal에서 논리 앱을 엽니다.

1. 논리 앱 메뉴에서 **개요** 를 선택합니다.

   * 중지하지 않고 논리 앱을 다시 시작하려면 개요 창 도구 모음에서 **다시 시작** 을 선택합니다.
   * 실행 중인 논리 앱을 중지하려면 개요 창 도구 모음에서 **중지** 를 선택합니다. 선택 사항을 확인합니다.
   * 중지한 논리 앱을 시작하려면 개요 창 도구 모음에서 **시작** 을 선택합니다.

   > [!NOTE]
   > 논리 앱이 이미 중지된 경우에는 **시작** 옵션만 표시됩니다. 논리 앱이 이미 실행 중인 경우에는 **중지** 옵션만 표시됩니다.
   > 언제든 논리 앱을 다시 시작할 수 있습니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="stop-start-multiple-logic-apps"></a>

### <a name="stop-or-start-multiple-logic-apps"></a>여러 논리 앱 중지 또는 시작

여러 논리 앱을 동시에 중지하거나 시작할 수 있지만 여러 논리 앱을 다시 시작하려면 먼저 중지해야 합니다.

1. Azure Portal 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

1. **논리 앱** 페이지에서 논리 앱의 **상태** 열을 검토합니다.

1. 확인란 열에서 중지 또는 시작할 논리 앱을 선택합니다.

   * 선택한 실행 중 논리 앱을 중지하려면 개요 창 도구 모음에서 **사용 안 함/중지** 를 선택합니다. 선택 사항을 확인합니다.
   * 선택한 중지된 논리 앱을 시작하려면 개요 창 도구 모음에서 **사용/시작** 을 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="disable-enable-workflows"></a>

## <a name="disable-or-enable-workflows"></a>워크플로 사용 안 함 또는 사용

다음 번에 트리거 조건이 충족되더라도 트리거가 발생하지 않게 하려면 워크플로를 사용하지 않도록 설정합니다. 단일 워크플로를 사용하지 않도록 설정하거나 사용하도록 설정할 수 있지만, 동시에 여러 워크플로를 사용하지 않도록 설정하거나 사용하도록 설정할 수는 없습니다. 워크플로를 사용하지 않도록 설정하면, 워크플로 인스턴스에 다음과 같은 영향이 미칩니다.

* Azure Logic Apps가 완료될 때까지 모든 진행 중인 실행 및 보류 중인 실행을 계속합니다. 볼륨 또는 백로그에 따라 이 프로세스를 완료하는 데 시간이 걸릴 수 있습니다.

* Azure Logic Apps가 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 트리거는 다음에 조건이 충족될 때 발생하지 않습니다. 하지만 트리거 상태는 워크플로 사용이 중지된 지점을 기억합니다. 따라서 워크플로를 다시 사용하도록 설정하면 마지막 실행 이후 처리되지 않은 모든 항목에 대해 트리거가 발생합니다.

  트리거가 마지막 실행 이후 처리되지 않은 항목에서 발생하지 않도록 하려면 워크플로를 다시 활성화하기 전에 트리거의 상태를 지워야 합니다.

  1. 워크플로에서 워크플로의 트리거 일부를 편집합니다.
  1. 변경 내용을 저장합니다. 이 단계는 트리거의 현재 상태를 다시 설정합니다.
  1. [워크플로를 다시 활성화합니다](#disable-enable-workflows).

> [!NOTE]
> 워크플로 미사용 설정 및 논리 앱 중지 작업의 결과는 서로 다릅니다. 자세한 내용은 [논리 앱 중지 시 고려 사항](#considerations-stop-logic-apps)을 읽어보세요.

<a name="disable-workflow"></a>

### <a name="disable-workflow"></a>워크플로 사용 안 함

1. 논리 앱 메뉴의 **워크플로** 에서 **워크플로** 를 선택합니다. 확인란 열에서 사용하지 않도록 설정할 워크플로를 선택합니다.

1. **워크플로** 창 도구 모음에서 **사용 안 함** 을 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="enable-workflow"></a>

### <a name="enable-workflow"></a>워크플로 사용

1. 논리 앱 메뉴의 **워크플로** 에서 **워크플로** 를 선택합니다. 확인란 열에서 사용하도록 설정할 워크플로를 선택합니다.

1. **워크플로** 창 도구 모음에서 **사용** 을 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="delete"></a>

## <a name="delete-logic-apps-or-workflows"></a>논리 앱 또는 워크플로 삭제

[단일 논리 앱 또는 여러 논리 앱을 동시에 삭제](#delete-logic-apps)할 수 있습니다. 단일 테넌트 기반 논리 앱에 여러 워크플로가 포함될 수 있으므로 전체 논리 앱을 삭제하거나 [워크플로만 삭제](#delete-workflows)할 수 있습니다.

<a name="delete-logic-apps"></a>

### <a name="delete-logic-apps"></a>논리 앱 삭제

논리 앱을 삭제하면 진행 중인 실행과 보류 중인 실행이 즉시 취소되지만 앱에서 사용하는 스토리지에서 정리 작업이 실행되지 않습니다.

1. Azure Portal 검색 상자에 `logic apps`를 입력하고, **논리 앱** 을 선택합니다.

1. **논리 앱** 목록의 확인란 열에서 삭제할 논리 앱을 하나 또는 여러 개 선택합니다. 도구 모음에서 **삭제** 를 선택합니다.

1. 확인 상자가 표시되면 `yes`를 입력하고 **삭제** 를 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="delete-workflows"></a>

### <a name="delete-workflows"></a>워크플로 삭제

워크플로를 삭제하면 다음과 같은 방식으로 워크플로 인스턴스에 영향을 줍니다.

* Azure Logic Apps가 진행 중인 실행과 보류 중인 실행을 즉시 취소하지만 워크플로에서 사용하는 스토리지에서 정리 작업을 실행하지 않습니다.

* Azure Logic Apps가 새 워크플로 인스턴스를 만들거나 실행하지 않습니다.

* 워크플로를 삭제한 다음, 동일한 워크플로를 다시 만들면 다시 생성된 워크플로에는 삭제된 워크플로와 동일한 메타데이터가 포함되지 않습니다. 메타데이터를 새로 고치려면 삭제된 워크플로를 호출한 모든 워크플로를 다시 저장해야 합니다. 이렇게 하면 호출자가 다시 생성된 워크플로에 대한 올바른 정보를 가져옵니다. 그렇지 않으면 다시 생성된 워크플로에 대한 호출이 `Unauthorized` 오류와 함께 실패합니다. 이 동작은 Azure 함수를 호출하는 워크플로 및 통합 계정에서 아티팩트를 사용하는 워크플로에도 적용됩니다.

1. Azure Portal에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **워크플로** 에서 **워크플로** 를 선택합니다. 확인란 열에서 삭제할 워크플로를 하나 또는 여러 개를 선택합니다.

1. 도구 모음에서 **삭제** 를 선택합니다.

1. 작업의 성공 여부를 확인하려면 주 Azure 도구 모음에서 **알림** 목록(종 모양 아이콘)을 엽니다.

<a name="recover-deleted"></a>

## <a name="recover-deleted-logic-apps"></a>삭제된 논리 앱 복구

소스 제어를 사용하는 경우 삭제된 **논리 앱(표준)** 리소스를 단일 테넌트 Azure Logic Apps로 원활하게 다시 배포할 수 있습니다. 그러나 소스 제어를 사용하지 않는 경우에는 다음 단계를 수행하여 삭제된 논리 앱을 복구합니다.

> [!NOTE]
> 삭제된 논리 앱을 복구하기 전에 다음 고려 사항을 검토하세요.
>
> * **워크플로 표준** 호스팅 계획을 사용하는 삭제된 **논리 앱(표준)** 리소스만 복구할 수 있습니다. 
> 삭제된 **논리 앱(사용)** 리소스는 복구할 수 없습니다.
>
> * 워크플로가 요청 트리거로 시작되는 경우, 복구된 논리 앱의 콜백 URL은 삭제된 논리 앱의 URL과 다릅니다.
>
> * 삭제된 논리 앱의 실행 기록은 복구된 논리 앱에서 사용할 수 없습니다.

1. 논리 앱의 스토리지 계정이 아직 있는지 확인합니다. 스토리지 계정이 삭제된 경우에는 [먼저 삭제된 스토리지 계정을 복구](../storage/common/storage-account-recover.md)해야 합니다.

1. 스토리지 계정의 **보안 + 네트워킹** 에서 **액세스 키** 를 선택합니다.

1. **액세스 키** 페이지에서 계정의 기본 연결 문자열을 복사하고 나중에 사용할 수 있도록 저장합니다. 예를 들면 다음과 같습니다.

   `DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accesskey>;EndpointSuffix=core.windows.net`

1. 스토리지 계정 메뉴의 **데이터 스토리지** 에서 **파일 공유** 를 선택하고, 논리 앱과 연결된 파일 공유의 이름을 복사해 나중에 사용할 수 있도록 저장합니다.

1. 동일한 호스팅 계획 및 가격 책정 계층을 사용하여 새 **논리 앱(표준)** 리소스를 만듭니다. 새 이름을 사용하거나 삭제된 논리 앱의 이름을 다시 사용할 수 있습니다.

1. 논리 앱을 중지한 후 계속 진행합니다. 논리 앱 메뉴에서 **개요** 를 선택합니다. 논리 앱 메뉴의 **설정** 페이지 도구 모음에서 **중지** 를 선택합니다.

1. 논리 앱 메뉴의 **설정** 에서 **구성** 을 선택합니다.

1. **구성** 페이지에서 다음 애플리케이션 설정 값을 업데이트하고, 완료되면 변경 내용을 저장해야 합니다.

   | 앱 설정 | 대체 값 |
   |-------------|-------------------|
   | `AzureWebJobsStorage` | 기존 값을 스토리지 계정에서 이전에 복사한 연결 문자열로 대체합니다. |
   | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | 기존 값을 스토리지 계정에서 이전에 복사한 문자열로 대체합니다. |
   | `WEBSITE_CONTENTSHARE` | 기존 값을 이전에 복사한 파일 공유 이름으로 대체합니다. |
   |||

1. 논리 앱 메뉴의 **워크플로** 에서 **연결** 을 선택합니다.

1. 각 연결을 열고 **설정** 에서 **액세스 정책** 을 선택합니다.

1. 삭제된 논리 앱의 액세스 정책을 삭제한 다음 대체 논리 앱의 새 액세스 정책을 추가합니다.

1. 논리 앱의 **구성** 페이지로 돌아가서 삭제된 논리 앱에 있는 사용자 지정 설정을 추가합니다.

1. 완료되면 논리 앱을 다시 시작합니다.

<a name="troubleshoot"></a>

## <a name="troubleshoot-problems-and-errors"></a>문제 및 오류 해결

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>이전에 만든 워크플로의 디자이너 선택기에 새 트리거 및 작업이 없음

단일 테넌트 Azure Logic Apps는 Azure Function 작업, Liquid 작업 및 XML 작업(예: **XML 유효성 검사** 및 **XML 변환**)의 기본 제공 작업을 지원합니다. 그러나 이전에 만든 논리 앱의 경우 논리 앱에서 오래된 버전의 확장 번들 `Microsoft.Azure.Functions.ExtensionBundle.Workflows`를 사용하기 때문에 이러한 작업이 디자이너에 표시되지 않을 수 있습니다.

이 문제를 해결하려면 확장 번들을 최신 버전으로 자동 업데이트하도록 다음 단계에 따라 오래된 버전을 삭제합니다.

> [!NOTE]
> 이 솔루션은 Azure Portal을 사용하여 만드는 **논리 앱(표준)** 리소스에만 적용되고, Visual Studio Code 및 Azure Logic Apps(표준) 확장을 사용하여 만들고 배포하는 논리 앱에는 적용되지 않습니다. [지원되는 트리거 및 작업이 Visual Studio Code 디자이너에 없음](create-single-tenant-workflows-visual-studio-code.md#missing-triggers-actions)을 참조하세요.

1. Azure Portal에서 논리 앱을 중지합니다.

   1. 논리 앱 메뉴에서 **개요** 를 선택합니다.

   1. **개요** 창의 도구 모음에서 **중지** 를 선택합니다.

1. 논리 앱 메뉴의 **개발 도구** 에서 **고급 도구** 를 선택합니다.

1. **고급 도구** 창에서 **Go** 를 선택합니다. 그러면 논리 앱에 대한 Kudu 환경이 열립니다.

1. Kudu 도구 모음에서 **디버그 콘솔** 메뉴를 열고 **CMD** 를 선택합니다.

   명령 프롬프트를 사용하여 번들 폴더로 이동할 수 있도록 콘솔 창이 열립니다. 또는 콘솔 창 위에 표시된 디렉터리 구조를 찾아볼 수도 있습니다.

1. 기존 번들의 버전 폴더가 들어 있는 다음 폴더로 이동합니다.

   `...\home\data\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 기존 번들의 버전 폴더를 삭제합니다. 콘솔 창에서 다음 명령을 실행하고 `{bundle-version}`을 기존 버전으로 바꿉니다.

   `rm -rf {bundle-version}`

   `rm -rf 1.1.3`

   > [!TIP]
   > "권한이 거부됨" 또는 "사용 중인 파일"과 같은 오류가 발생하는 경우 브라우저에서 페이지를 새로 고치고, 폴더가 삭제될 때까지 이전 단계를 다시 시도합니다.

1. Azure Portal에서 논리 앱의 **개요** 페이지로 돌아가서 **다시 시작** 을 선택합니다.

   포털이 자동으로 최신 번들을 가져와서 사용합니다.

## <a name="next-steps"></a>다음 단계

이 시나리오 경험에 대한 여러분의 의견을 듣고 싶습니다.

* 버그 또는 문제가 발견되는 경우 [GitHub에서 문제를 알려주세요](https://github.com/Azure/logicapps/issues).
* [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)하여 질문, 요청, 의견 및 기타 피드백을 보내주세요.
