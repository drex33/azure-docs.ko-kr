---
title: Logic Apps와 통합
titleSuffix: Azure Digital Twins
description: 사용자 지정 커넥터를 사용하여 Logic Apps를 Azure Digital Twins에 연결하는 방법 보기
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: aa8ea41233cad42bcad9fc387b8f984503c6e75e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797772"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>사용자 지정 커넥터를 사용하여 Logic Apps와 통합

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 앱과 서비스에서 워크플로를 자동화하는 데 사용되는 클라우드 서비스입니다. Logic Apps를 Azure Digital Twins API에 연결하여 Azure Digital Twins와 해당 데이터에 대한 이러한 자동화된 흐름을 만들 수 있습니다.

Azure Digital Twins는 현재 Logic Apps에 대해 인증 된 (미리 작성 된) 커넥터를 포함 하지 않습니다. 대신, Azure Digital Twins와 Logic Apps를 사용 하는 현재 프로세스는 Logic Apps 작업 하도록 수정 된 [사용자 지정 Azure Digital Twins Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) 정의 파일을 사용 하 여 [사용자 지정 Logic Apps 커넥터](../logic-apps/custom-connector-overview.md)를 만드는 것입니다.

> [!NOTE]
> 위에 연결 된 사용자 지정 Swagger 샘플에 포함 된 여러 버전의 Swagger 정의 파일이 있습니다. 최신 버전은 가장 최근 날짜의 하위 폴더에 있지만 이 샘플에 포함된 이전 버전도 계속 지원됩니다.

이 문서에서는 [Azure Portal](https://portal.azure.com) 를 사용 하 여 Azure Digital twins 인스턴스에 Logic Apps를 연결 하는 데 사용할 수 있는 **사용자 지정 커넥터를 만듭니다** . 그런 다음이 연결을 사용 하는 **논리 앱을 만듭니다** . 예를 들어 타이머에서 트리거되는 이벤트는 Azure Digital twins 인스턴스의 쌍을 자동으로 업데이트 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
이 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

또한 필수 구성 요소를 설정하는 과정에서 다음 항목을 완료해야 합니다. 이 섹션의 나머지 부분에서는 다음 단계를 안내 합니다.
- Azure Digital Twins 인스턴스 설정
- 디지털 트윈 추가
- Azure AD(Azure Active Directory) 앱 등록 설정

### <a name="set-up-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 설정

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>디지털 트윈 추가

이 문서에서는 Logic Apps를 사용하여 Azure Digital Twins 인스턴스에서 트윈을 업데이트합니다. 계속 하려면 인스턴스에 하나 이상의 쌍을 추가 해야 합니다. 

[DigitalTwins API](/rest/api/digital-twins/dataplane/twins), [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 또는 [Azure Digital Twins CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)를 사용하여 트윈을 추가할 수 있습니다. 이러한 방법을 사용하여 트윈을 만드는 방법에 대한 자세한 단계는 [디지털 트윈 관리](how-to-manage-twin.md)를 참조하세요.

사용자가 만든 인스턴스에 쌍의 쌍 **ID** 가 필요 합니다.

### <a name="set-up-app-registration"></a>앱 등록 설정

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

## <a name="create-custom-logic-apps-connector"></a>사용자 지정 Logic Apps 커넥터 만들기

이제 Azure Digital Twins API에 대한 [사용자 지정 Logic Apps 커넥터](../logic-apps/custom-connector-overview.md)를 만들 준비가 되었습니다. 이렇게 하면 다음 섹션에서 논리 앱을 만들 때 Azure Digital Twins에 연결할 수 있습니다.

Azure Portal의 [Logic Apps 사용자 지정 커넥터](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) 페이지로 이동합니다(이 링크를 사용하거나 포털 검색 창에서 검색할 수 있음). *+ 만들기* 를 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure Portal의 'Logic Apps 사용자 지정 커넥터' 페이지의 스크린샷입니다. '추가' 단추가 강조 표시됩니다.":::

다음에 나오는 **논리 앱 사용자 지정 커넥터 만들기** 페이지에서 구독 및 리소스 그룹을 선택 하 고 새 커넥터에 대 한 이름 및 배포 위치를 선택 합니다. **검토 + 만들기** 를 선택합니다. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure Portal에서 ' 논리 앱 사용자 지정 커넥터 만들기 ' 페이지의 스크린샷":::

이렇게 하면 아래에서 **만들기** 를 선택 하 여 리소스를 만들 수 있는 **검토 + 만들기** 탭으로 이동 됩니다.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure Portal의 'Logic Apps 사용자 지정 커넥터 검토' 페이지의 '검토 + 만들기' 탭의 스크린샷":::

커넥터에 대한 배포 페이지로 이동합니다. 배포가 완료 되 면 **리소스로 이동** 단추를 선택 하 여 포털에서 커넥터의 세부 정보를 확인 합니다.

### <a name="configure-connector-for-azure-digital-twins"></a>Azure Digital Twins에 관해 커넥터 구성

다음으로, Azure Digital Twins에 연결하기 위해 만든 커넥터를 구성합니다.

먼저 Logic Apps로 작업하도록 수정된 사용자 지정 Azure Digital Twins Swagger를 다운로드합니다. **ZIP 다운로드** 단추를 선택하여 [Azure Digital Twins 사용자 지정 Swagger(Logic Apps 커넥터) 샘플](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)을 다운로드합니다. 다운로드한 *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* 폴더로 이동하여 폴더 압축을 풉니다. 

이 자습서에 대 한 사용자 지정 Swagger는 *digital-twins-custom-swaggers-main\LogicApps* 폴더에 있습니다. 이 폴더에는 *stable* 및 *preview* 라는 하위 폴더가 포함되어 있으며, 둘 다 날짜별로 구성된 서로 다른 버전의 Swagger를 포함합니다. 가장 최근 날짜의 폴더에는 Swagger 정의 파일의 최신 복사본이 포함 됩니다. 어떤 버전을 선택하든 Swagger 파일의 이름은 _digitaltwins.json_ 입니다.

> [!NOTE]
> 미리 보기 기능을 사용 하 여 작업 하지 않는 한 일반적으로 가장 *안정적인* Swagger 파일 버전을 사용 하는 것이 좋습니다. 그러나 Swagger 파일의 이전 버전 및 미리 보기 버전도 계속 지원 됩니다. 

그런 다음, [Azure Portal](https://portal.azure.com)의 커넥터 개요 페이지로 이동하고 **편집** 을 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="이전 단계에서 만든 커넥터에 대한 '개요' 페이지의 스크린샷입니다. '편집' 단추가 강조 표시됩니다.":::

다음에 표시되는 **Edit Logic Apps Custom Connector(Logic Apps 사용자 지정 커넥터 편집)** 페이지에서 다음 정보를 구성합니다.
* **사용자 지정 커넥터**
    - API 엔드포인트: REST(기본값 유지)
    - 가져오기 모드: OpenAPI 파일(기본값 유지)
    - 파일:이 구성은 앞에서 다운로드 한 사용자 지정 Swagger 파일입니다. **가져오기** 를 선택하고 머신에서 파일(*Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\...\digitaltwins.json*)을 찾아 **열기** 를 선택합니다.
* **일반 정보**
    - 아이콘: 원하는 아이콘을 업로드 합니다.
    - 아이콘 배경색: 색상에 관해 ‘#xxxxxx’ 형식으로 16진수 코드를 입력합니다.
    - 설명: 원하는 모든 값을 입력합니다.
    - 온-프레미스 데이터 게이트웨이를 통한 커넥트: 해제 (기본값 유지)
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

리디렉션 URL 필드는 *사용자 지정 커넥터를 저장 하 여 리디렉션 url을 생성 하* 는 것을 말합니다. 이제 창의 위쪽에서 **커넥터 업데이트** 를 선택 하 여 커넥터 설정을 확인 하 여 생성 합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="'Logic Apps 사용자 지정 커넥터 편집' 페이지 위쪽의 스크린샷입니다. '커넥터 업데이트' 단추가 강조 표시됩니다.":::

리디렉션 URL 필드로 돌아가서 생성된 값을 복사합니다. 다음 단계에서 사용합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="'Logic Apps 사용자 지정 커넥터 편집' 페이지의 리디렉션 URL 필드의 스크린샷입니다. 값을 복사하는 단추가 강조 표시됩니다.":::

이제 커넥터를 만드는 데 필요한 모든 정보를 입력 했습니다. (정의 단계에 대 한 이전 보안을 계속할 필요가 없습니다.) **Edit Logic Apps Custom Connector(Logic Apps 사용자 지정 커넥터 편집)** 창을 닫을 수 있습니다.

>[!NOTE]
>처음 **편집** 을 선택한 커넥터의 개요 페이지로 돌아가서 편집을 다시 선택하면 구성 선택 사항을 입력하는 전체 프로세스가 다시 시작됩니다. 마지막으로 수행한 값은 채워지지 않으므로 변경된 값으로 업데이트된 구성을 저장하려면 기본값이 덮어쓰지 않도록 다른 모든 값도 다시 입력해야 합니다.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Azure AD 앱에서 커넥터 권한 부여

다음으로, 마지막 단계에서 복사한 사용자 지정 커넥터의 **리디렉션 URL** 값을 사용하여 Azure AD 앱 등록에서 커넥터 권한을 부여합니다.

Azure Portal의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 페이지로 이동하고 목록에서 등록을 선택합니다. 

등록 메뉴의 **인증** 에서 URI를 추가합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="'URI 추가' 단추와 '인증' 메뉴가 강조 표시된 Azure Portal의 앱 등록에 대한 인증 페이지의 스크린샷"::: 

사용자 지정 커넥터의 **리디렉션 URL** 을 새 필드에 입력하고 **저장** 아이콘을 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="새 리디렉션 URL 및 '저장' 단추가 강조 표시된 Azure Portal의 앱 등록을 위한 인증 페이지의 스크린샷":::

이제 Azure Digital Twins Api에 액세스할 수 있는 사용자 지정 커넥터를 설정 하는 작업이 완료 되었습니다. 

## <a name="create-logic-app"></a>논리 앱 만들기

다음으로, 새 커넥터를 사용하여 Azure Digital Twins 업데이트를 자동화하는 논리 앱을 만듭니다.

[Azure Portal](https://portal.azure.com) 검색 창에서 **논리 앱** 을 검색합니다. 논리 앱을 선택하면 **논리 앱** 페이지로 이동합니다. **+ 추가를** 선택하여 새 논리 앱을 만듭니다.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="'논리 앱 만들기' 단추가 강조 표시된 Azure Portal의 'Logic Apps' 페이지의 스크린샷":::

다음에 표시되는 **논리 앱** 페이지에서 구독 및 리소스 그룹을 입력합니다. **인스턴스 세부 정보에서** **소비** 인스턴스 유형을 선택하고 논리 앱의 이름을 선택한 다음 배포 위치를 선택합니다. 로그 분석을 사용하거나 사용하지 않도록 설정할지 여부를 선택합니다.

_검토 + 만들기_ 단추를 선택합니다.

이렇게 하면 **검토 + 만들기** 탭으로 가서 세부 정보를 검토하고 맨 아래에서 **만들기를** 선택하여 리소스를 만들 수 있습니다.

논리 앱에 대한 배포 페이지로 이동합니다. 배포가 완료되면 **리소스로 이동** 단추를 선택하여 워크플로의 논리를 채울 **Logic Apps 디자이너로** 계속 진행합니다.

### <a name="design-workflow"></a>디자인 워크플로

Logic Apps 디자이너에서 **일반적인 트리거로 시작** 아래에 있는 **되풀이** 를 선택합니다.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="'되풀이' 일반 트리거가 강조 표시된 Azure Portal의 'Logic Apps 디자이너' 페이지의 스크린샷":::

다음에 표시되는 Logic Apps 디자이너 페이지에서 3초마다 이벤트가 트리거되도록 **되풀이** 빈도를 **초** 로 변경합니다. 이 빈도를 선택하면 오래 기다리지 않고도 나중에 결과를 쉽게 볼 수 있습니다.

**+ 새 단계** 를 선택합니다.

이렇게 하면 작업 선택 상자가 열립니다. **사용자 지정** 탭으로 전환합니다. 이전의 사용자 지정 커넥터가 맨 위에 있는 상자에 표시됩니다.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Azure Portal의 Logic Apps 디자이너에서 흐름을 만드는 스크린샷입니다. 사용자 지정 커넥터가 강조 표시됩니다.":::

해당 커넥터에 포함된 API의 목록을 표시하려면 상자를 선택합니다. 검색 창을 사용하거나 목록을 스크롤하여 **DigitalTwins_Add** 를 선택합니다. **(DigitalTwins_Add** 작업은 이 문서에서 사용되는 API 호출이지만 다른 API를 Logic Apps 연결에 대한 유효한 선택으로 선택할 수도 있습니다.)

커넥터에 연결하려면 Azure 자격 증명을 사용하여 로그인하라는 메시지가 나타날 수 있습니다. **요청된 권한** 대화 상자가 나타나면 메시지에 따라 앱에 대한 동의를 부여하고 수락합니다.

새 **DigitalTwinsAdd** 상자에서 다음과 같이 필드를 채웁니다.
* id: 논리 앱을 업데이트하려는 인스턴스에서 디지털 트윈의 **트윈 ID** 를 입력합니다.
* twin: 이 필드는 선택한 API 요청에 필요한 본문을 입력하는 필드입니다. **DigitalTwinsUpdate** 의 경우 이 본문은 JSON 패치 코드 형식입니다. 트윈 업데이트를 위한 JSON 패치 구조화에 대한 자세한 내용은 *방법: 디지털 트윈 관리* 의 [디지털 트윈 업데이트](how-to-manage-twin.md#update-a-digital-twin) 섹션을 참조하세요.
* api-version: 최신 API 버전입니다. 현재 이 값은 *2020-10-31* 입니다.

Logic Apps 디자이너에서 **저장** 을 선택합니다.

같은 창에서 _+ 새 단계_ 를 선택하여 다른 작업을 선택할 수 있습니다.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="논리 앱 커넥터에서 앱의 완료된 보기의 스크린샷입니다. DigitalTwinsAdd 상자는 아래 설명된 값으로 채워집니다.":::

## <a name="query-twin-to-see-the-update"></a>트윈을 쿼리하여 업데이트 확인

이제 논리 앱을 만들었으므로 Logic Apps 디자이너에서 정의한 트윈 업데이트 이벤트가 3초마다 되풀이되어 발생합니다. 이 구성된 빈도는 3초 후에 트윈을 쿼리하고 새 패치된 값이 반영되는 것을 볼 수 있음을 의미합니다.

선택하는 방법([사용자 지정 클라이언트 앱](tutorial-command-line-app.md), [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md), [SDK 및 API](concepts-apis-sdks.md) 또는 [CLI](concepts-cli.md) 등)으로 트윈을 쿼리할 수 있습니다. 

Azure Digital Twins 인스턴스 쿼리에 대한 자세한 내용은 [트윈 그래프 쿼리](how-to-query-graph.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자가 제공한 패치를 사용하여 Azure Digital Twins 인스턴스에서 트윈을 정기적으로 업데이트하는 논리 앱을 만들었습니다. 사용자 지정 커넥터에서 다른 API를 선택하여 인스턴스의 다양한 작업에 대한 Logic Apps 만들 수 있습니다.

사용 가능한 API 작업과 필요한 세부 정보에 대한 자세한 내용은 [Azure Digital Twins API 및 SDK](concepts-apis-sdks.md)를 참조하세요.
