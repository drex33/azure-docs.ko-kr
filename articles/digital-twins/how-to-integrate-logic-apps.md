---
title: Logic Apps와 통합
titleSuffix: Azure Digital Twins
description: 사용자 지정 커넥터를 사용하여 Logic Apps를 Azure Digital Twins에 연결하는 방법 보기
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 7aca72097e1d6aee40e5e9ead0c60be34d3e65d5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437841"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>사용자 지정 커넥터를 사용하여 Logic Apps와 통합

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 앱과 서비스에서 워크플로를 자동화하는 데 사용되는 클라우드 서비스입니다. Logic Apps를 Azure Digital Twins API에 연결하여 Azure Digital Twins와 해당 데이터에 대한 이러한 자동화된 흐름을 만들 수 있습니다.

Azure Digital Twins에는 현재 Logic Apps용으로 인증(미리 빌드)된 커넥터가 없습니다. 대신 Azure Digital Twins에서 Logic Apps를 사용하는 현재 프로세스는 Logic Apps에서 작동하도록 수정된 사용자 지정 Azure Digital Twins Swagger를 사용하여 [[사용자 지정 Logic Apps 커넥터](../logic-apps/custom-connector-overview.md)](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)를 만드는 것입니다.

> [!NOTE]
> 위에 연결된 사용자 지정 Swagger 샘플에는 여러 버전의 Swagger가 포함되어 있습니다. 최신 버전은 가장 최근 날짜의 하위 폴더에 있지만 이 샘플에 포함된 이전 버전도 계속 지원됩니다.

이 문서에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Azure Digital Twins 인스턴스에 Logic Apps를 연결하는 데 사용할 수 있는 **사용자 지정 커넥터를 만듭니다**. 그런 다음, 타이머에 의해 트리거된 이벤트가 Azure Digital Twins 인스턴스에서 트윈을 자동으로 업데이트하는 예제 시나리오에 이 연결을 사용하는 **논리 앱을 만듭니다**. 

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
이 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

또한 필수 구성 요소를 설정하는 과정에서 다음 항목을 완료해야 합니다. 이 섹션의 나머지 부분에서는 다음 단계를 안내합니다.
- Azure Digital Twins 인스턴스 설정
- 디지털 트윈 추가
- Azure AD(Azure Active Directory) 앱 등록 설정

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>디지털 트윈 추가

이 문서에서는 Logic Apps를 사용하여 Azure Digital Twins 인스턴스에서 트윈을 업데이트합니다. 계속하려면 인스턴스에서 하나 이상의 트윈을 추가해야 합니다. 

[DigitalTwins API](/rest/api/digital-twins/dataplane/twins), [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 또는 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 사용하여 트윈을 추가할 수 있습니다. 이러한 방법을 사용하여 트윈을 만드는 방법에 대한 자세한 단계는 [디지털 트윈 관리](how-to-manage-twin.md)를 참조하세요.

사용자가 만든 인스턴스에 트윈의 **트윈 ID** 가 필요합니다.

### <a name="set-up-app-registration"></a>앱 등록 설정

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

## <a name="create-custom-logic-apps-connector"></a>사용자 지정 Logic Apps 커넥터 만들기

이제 Azure Digital Twins API에 대한 [사용자 지정 Logic Apps 커넥터](../logic-apps/custom-connector-overview.md)를 만들 준비가 되었습니다. 이 작업을 수행한 후 다음 섹션에서 논리 앱을 만들 때 Azure Digital Twins에 연결할 수 있습니다.

Azure Portal의 [Logic Apps 사용자 지정 커넥터](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) 페이지로 이동합니다(이 링크를 사용하거나 포털 검색 창에서 검색할 수 있음). *+추가* 를 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure Portal의 'Logic Apps 사용자 지정 커넥터' 페이지의 스크린샷입니다. '추가' 단추가 강조 표시됩니다.":::

다음에 표시되는 **Create Logic Apps Custom Connector(Logic Apps 사용자 지정 커넥터 만들기)** 페이지에서 구독 및 리소스 그룹을 선택하고 새 커넥터의 이름과 배포 위치를 선택합니다. **검토 + 만들기** 를 선택합니다. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure Portal의 'Logic Apps 사용자 지정 커넥터 만들기' 페이지의 스크린샷":::

그러면 **검토 + 만들기** 탭으로 이동합니다. 여기서 맨 아래에 있는 **만들기** 를 선택하여 리소스를 만들 수 있습니다.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure Portal의 'Logic Apps 사용자 지정 커넥터 검토' 페이지의 '검토 + 만들기' 탭의 스크린샷":::

커넥터에 대한 배포 페이지로 이동합니다. 배포가 완료되면 **리소스로 이동** 단추를 선택하여 포털에서 커넥터의 세부 정보를 확인합니다.

### <a name="configure-connector-for-azure-digital-twins"></a>Azure Digital Twins에 관해 커넥터 구성

다음으로, Azure Digital Twins에 연결하기 위해 만든 커넥터를 구성합니다.

먼저 Logic Apps로 작업하도록 수정된 사용자 지정 Azure Digital Twins Swagger를 다운로드합니다. **ZIP 다운로드** 단추를 선택하여 [Azure Digital Twins 사용자 지정 Swagger(Logic Apps 커넥터) 샘플](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)을 다운로드합니다. 다운로드한 *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* 폴더로 이동하여 폴더 압축을 풉니다. 

이 자습서의 사용자 지정 Swagger는 *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps* 폴더에 있습니다. 이 폴더에는 *stable* 및 *preview* 라는 하위 폴더가 포함되어 있으며, 둘 다 날짜별로 구성된 서로 다른 버전의 Swagger를 포함합니다. 가장 최근 날짜가 있는 폴더에는 Swagger의 최신 복사본이 포함됩니다. 어떤 버전을 선택하든 Swagger 파일의 이름은 _digitaltwins.json_ 입니다.

> [!NOTE]
> 미리 보기 기능을 사용하지 않는 한 일반적으로 Swagger의 *안정적인* 최신 버전을 사용하는 것이 좋습니다. 그러나 Swagger의 이전 버전 및 미리 보기 버전도 계속 지원됩니다. 

그런 다음, [Azure Portal](https://portal.azure.com)의 커넥터 개요 페이지로 이동하고 **편집** 을 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="이전 단계에서 만든 커넥터에 대한 '개요' 페이지의 스크린샷입니다. '편집' 단추가 강조 표시됩니다.":::

다음에 표시되는 **Edit Logic Apps Custom Connector(Logic Apps 사용자 지정 커넥터 편집)** 페이지에서 다음 정보를 구성합니다.
* **사용자 지정 커넥터**
    - API 엔드포인트: REST(기본값 유지)
    - 가져오기 모드: OpenAPI 파일(기본값 유지)
    - 파일: 앞에서 다운로드한 사용자 지정 Swagger 파일입니다. **가져오기** 를 선택하고 머신에서 파일(*Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\...\digitaltwins.json*)을 찾아 **열기** 를 선택합니다.
* **일반 정보**
    - 아이콘: 원하는 아이콘 업로드
    - 아이콘 배경색: 색상에 관해 ‘#xxxxxx’ 형식으로 16진수 코드를 입력합니다.
    - 설명: 원하는 모든 값을 입력합니다.
    - 스키마: HTTPS(기본값 유지)
    - 호스트: Azure Digital Twins 인스턴스의 **호스트 이름**
    - 기준 URL: /(기본값 유지)

그런 다음, 창 아래쪽에 있는 **보안** 단추를 선택하여 다음 구성 단계로 진행합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="'Logic Apps 사용자 지정 커넥터 편집' 페이지 아래쪽의 스크린샷입니다. 보안으로 계속하는 단추가 강조 표시됩니다.":::

보안 단계에서 **편집** 을 선택하고 다음 정보를 구성합니다.
* **인증 유형**: OAuth 2.0
* **OAuth 2.0**:
    - ID 공급자: Azure Active Directory
    - 클라이언트 ID: [필수 구성 요소](#prerequisites)에서 만든 Azure AD 앱 등록에 대한 **애플리케이션(클라이언트) ID**
    - 클라이언트 암호: 앱 등록의 **클라이언트 암호** 
    - 로그인 URL: https://login.windows.net(기본값 유지)
    - 테넌트 ID: Azure AD 앱 등록에 대한 **디렉터리(테넌트) ID**
    - 리소스 URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - 범위: Directory.AccessAsUser.All
    - 리디렉션 URL: (지금은 기본값 유지)

리디렉션 URL 필드는 *사용자 지정 커넥터를 저장하여 리디렉션 URL을 생성* 하도록 지정합니다. 이제 창 위쪽에 있는 **커넥터 업데이트** 를 선택하여 커넥터 설정을 확인합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="'Logic Apps 사용자 지정 커넥터 편집' 페이지 위쪽의 스크린샷입니다. '커넥터 업데이트' 단추가 강조 표시됩니다.":::

리디렉션 URL 필드로 돌아가서 생성된 값을 복사합니다. 이 버전 번호는 다음 단계에서 사용합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="'Logic Apps 사용자 지정 커넥터 편집' 페이지의 리디렉션 URL 필드의 스크린샷입니다. 값을 복사하는 단추가 강조 표시됩니다.":::

커넥터를 만드는 데 필요한 모든 정보입니다(보안을 지나 정의 단계까지 계속할 필요 없음). **Edit Logic Apps Custom Connector(Logic Apps 사용자 지정 커넥터 편집)** 창을 닫을 수 있습니다.

>[!NOTE]
>처음 **편집** 을 선택한 커넥터의 개요 페이지로 돌아가서 편집을 다시 선택하면 구성 선택 사항을 입력하는 전체 프로세스가 다시 시작됩니다. 마지막으로 수행한 값은 채워지지 않으므로 변경된 값으로 업데이트된 구성을 저장하려면 기본값이 덮어쓰지 않도록 다른 모든 값도 다시 입력해야 합니다.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Azure AD 앱에서 커넥터 권한 부여

다음으로, 마지막 단계에서 복사한 사용자 지정 커넥터의 **리디렉션 URL** 값을 사용하여 Azure AD 앱 등록에서 커넥터 권한을 부여합니다.

Azure Portal의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 페이지로 이동하고 목록에서 등록을 선택합니다. 

등록 메뉴의 **인증** 에서 URI를 추가합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="'URI 추가' 단추와 '인증' 메뉴가 강조 표시된 Azure Portal의 앱 등록에 대한 인증 페이지의 스크린샷"::: 

사용자 지정 커넥터의 **리디렉션 URL** 을 새 필드에 입력하고 **저장** 아이콘을 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="새 리디렉션 URL 및 '저장' 단추가 강조 표시된 Azure Portal의 앱 등록을 위한 인증 페이지의 스크린샷":::

이제 Azure Digital Twins API에 액세스할 수 있는 사용자 지정 커넥터 설정이 완료되었습니다. 

## <a name="create-logic-app"></a>논리 앱 만들기

다음으로, 새 커넥터를 사용하여 Azure Digital Twins 업데이트를 자동화하는 논리 앱을 만듭니다.

[Azure Portal](https://portal.azure.com) 검색 창에서 **논리 앱** 을 검색합니다. 논리 앱을 선택하면 **논리 앱** 페이지로 이동합니다. **논리 앱 만들기** 단추를 선택하여 새 논리 앱을 만듭니다.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="'논리 앱 만들기' 단추가 강조 표시된 Azure Portal의 'Logic Apps' 페이지의 스크린샷":::

다음에 표시되는 **논리 앱** 페이지에서 구독 및 리소스 그룹을 입력합니다. 또한 논리 앱의 이름을 선택하고 배포 위치를 선택합니다.

_검토 + 만들기_ 단추를 선택합니다.

그러면 **검토 + 만들기** 탭으로 이동하여 세부 정보를 검토하고 아래쪽에서 **만들기** 를 선택하여 리소스를 만들 수 있습니다.

논리 앱에 대한 배포 페이지로 이동합니다. 배포가 완료되면 **리소스로 이동** 단추를 선택하여 워크플로의 논리를 채울 **Logic Apps 디자이너** 로 이동합니다.

### <a name="design-workflow"></a>디자인 워크플로

Logic Apps 디자이너에서 **일반적인 트리거로 시작** 아래에 있는 **되풀이** 를 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="'되풀이' 일반 트리거가 강조 표시된 Azure Portal의 'Logic Apps 디자이너' 페이지의 스크린샷":::

다음에 표시되는 Logic Apps 디자이너 페이지에서 3초마다 이벤트가 트리거되도록 **되풀이** 빈도를 **초** 로 변경합니다. 이렇게 하면 나중에 오래 기다리지 않고 결과를 쉽게 확인할 수 있습니다.

**+ 새 단계** 를 선택합니다.

그러면 작업 선택 상자가 열립니다. **사용자 지정** 탭으로 전환합니다. 이전의 사용자 지정 커넥터가 맨 위에 있는 상자에 표시됩니다.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure Portal의 Logic Apps 디자이너에서 흐름을 만드는 스크린샷입니다. 사용자 지정 커넥터가 강조 표시됩니다.":::

해당 커넥터에 포함된 API의 목록을 표시하려면 상자를 선택합니다. 검색 창을 사용하거나 목록을 스크롤하여 **DigitalTwins_Add** 를 선택합니다. 이 API가 본 문서에서 사용지만 Logic Apps 연결에 대한 유효한 API로 다른 API를 선택할 수도 있습니다.

커넥터에 연결하려면 Azure 자격 증명을 사용하여 로그인하라는 메시지가 나타날 수 있습니다. **요청된 권한** 대화 상자가 나타나면 메시지에 따라 앱에 대한 동의를 부여하고 수락합니다.

새 **DigitalTwinsAdd** 상자에서 다음과 같이 필드를 채웁니다.
* id: 논리 앱을 업데이트하려는 인스턴스에서 디지털 트윈의 **트윈 ID** 를 입력합니다.
* twin: 이 필드는 선택한 API 요청에 필요한 본문을 입력하는 필드입니다. **DigitalTwinsUpdate** 의 경우 이 본문은 JSON 패치 코드 형식입니다. 트윈 업데이트를 위한 JSON 패치 구조화에 대한 자세한 내용은 *방법: 디지털 트윈 관리* 의 [디지털 트윈 업데이트](how-to-manage-twin.md#update-a-digital-twin) 섹션을 참조하세요.
* api-version: 최신 API 버전입니다. 현재 이 값은 *2020-10-31* 입니다.

Logic Apps 디자이너에서 **저장** 을 선택합니다.

같은 창에서 _+ 새 단계_ 를 선택하여 다른 작업을 선택할 수 있습니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="논리 앱 커넥터에서 앱의 완료된 보기의 스크린샷입니다. DigitalTwinsAdd 상자는 아래 설명된 값으로 채워집니다.":::

## <a name="query-twin-to-see-the-update"></a>트윈을 쿼리하여 업데이트 확인

이제 논리 앱을 만들었으므로 Logic Apps 디자이너에서 정의한 트윈 업데이트 이벤트가 3초마다 되풀이되어 발생합니다. 즉, 3초 후에 트윈을 쿼리하고 새 패치 값이 반영되었음을 확인할 수 있어야 합니다.

선택하는 방법([사용자 지정 클라이언트 앱](tutorial-command-line-app.md), [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md), [SDK 및 API](concepts-apis-sdks.md) 또는 [CLI](concepts-cli.md) 등)으로 트윈을 쿼리할 수 있습니다. 

Azure Digital Twins 인스턴스 쿼리에 대한 자세한 내용은 [트윈 그래프 쿼리](how-to-query-graph.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자가 제공한 패치를 사용하여 Azure Digital Twins 인스턴스에서 트윈을 정기적으로 업데이트하는 논리 앱을 만들었습니다. 사용자 지정 커넥터에서 다른 API를 선택하여 다양한 인스턴스 작업을 위한 Logic Apps를 만들 수 있습니다.

사용 가능한 API 작업과 필요한 세부 정보에 대한 자세한 내용은 [Azure Digital Twins API 및 SDK](concepts-apis-sdks.md)를 참조하세요.
