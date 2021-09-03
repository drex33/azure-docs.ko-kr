---
title: SharePoint에 연결
description: Azure Logic Apps를 사용하여 온-프레미스에서 SharePoint Online 또는 SharePoint Server의 리소스 모니터링 및 관리
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 08/11/2021
tags: connectors
ms.openlocfilehash: e53fca8b0e9f8dbca3dba8bd684e71ef25c88738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528113"
---
# <a name="connect-to-sharepoint-resources-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 SharePoint 리소스에 연결

SharePoint Online 또는 온-프레미스 SharePoint Server에서 파일, 폴더, 목록, 항목 등의 리소스를 모니터링하고 관리하는 작업을 자동화하려면 Azure Logic Apps 및 SharePoint 커넥터를 사용하여 자동화된 통합 워크플로를 만듭니다.

다음 목록에서는 자동화할 수 있는 예제 작업을 설명합니다.

* 파일 또는 항목이 생성, 변경 또는 삭제되는 경우를 모니터링합니다.
* 항목을 만들고, 가져오고 업데이트하거나 삭제합니다.
* 첨부 파일을 추가하고, 가져오거나 삭제합니다. 첨부 파일에서 콘텐츠를 가져옵니다.
* 파일을 만들고, 복사하고, 업데이트하거나 삭제합니다. 
* 파일 속성을 업데이트합니다. 파일에 대한 콘텐츠, 메타데이터 또는 속성을 가져옵니다.
* 폴더를 나열하거나 추출합니다.
* 목록을 가져오거나 보기를 나열합니다.
* 콘텐츠 승인 상태를 설정합니다.
* 사람을 확인합니다.
* SharePoint에 HTTP 요청을 보냅니다.
* 엔터티 값을 가져옵니다.

논리 앱 워크플로에서 SharePoint의 이벤트를 모니터링하고 다른 동작에 출력을 사용할 수 있게 하는 트리거를 사용할 수 있습니다. 그런 다음, 동작을 사용하여 SharePoint에서 다양한 작업을 수행할 수 있습니다. 또한 SharePoint 동작의 출력을 사용하는 다른 동작을 포함할 수도 있습니다. 예를 들어 정기적으로 SharePoint에서 파일을 검색하는 경우 Office 365 Outlook 커넥터 또는 Outlook.com 커넥터를 사용하여 해당 파일 및 해당 콘텐츠에 대한 이메일 경고를 보낼 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다. 또는 이 [ 빠른 시작을 시도하여 첫 번째 예제 논리 앱 워크플로 ](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* [회사 또는 학교 계정](https://support.microsoft.com/office/what-account-to-use-with-office-and-you-need-one-914e6610-2763-47ac-ab36-602a81068235#bkmk_msavsworkschool)으로 로그인하는 SharePoint에서 사용하는 Microsoft Office 365 계정 자격 증명.

  이 자격 증명은 SharePoint 계정에 액세스할 수 있는 권한을 워크플로에 부여하는 데 필요합니다.

  > [!NOTE]
  > [21Vianet에서 운영하는 Microsoft Azure](https://portal.azure.cn)를 사용하는 경우 Azure AD(Azure Active Directory) 인증은 .com 계정이 아닌 21Vianet(.cn)에서 운영하는 Microsoft Office 365 계정에서만 작동합니다.

* SharePoint 사이트 주소

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 온-프레미스 SharePoint 서버에 연결하려면 로컬 컴퓨터의 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md)와 [Azure에 이미 생성된 데이터 게이트웨이 리소스를 설치하고 설정](../logic-apps/logic-apps-gateway-connection.md)해야 합니다.

  그런 다음, 워크플로에서 SharePoint 서버 연결을 만들 때 사용할 게이트웨이 리소스를 선택할 수 있습니다.

* SharePoint 사이트 또는 서버에 액세스해야 하는 논리 앱 워크플로.

  * SharePoint 트리거로 워크플로를 시작하려면 빈 논리 앱 워크플로가 필요합니다.
  * SharePoint 동작을 추가하려면 워크플로에 트리거가 이미 있어야 합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 트리거, 동작, 제한 등 이 커넥터에 대한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/sharepoint/)에서 검토할 수 있습니다.

## <a name="connect-to-sharepoint"></a>SharePoint에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

## <a name="add-a-trigger"></a>트리거 추가

1. 아직 열려 있지 않은 경우 Azure Portal, Visual Studio Code 또는 Visual Studio의 비주얼 디자이너에서 논리 앱 워크플로를 엽니다.

1. 디자이너의 검색 상자에서 검색어로 `sharepoint`를 입력합니다. **SharePoint** 커넥터를 선택합니다.

1. **트리거** 목록에서 사용하려는 트리거를 선택합니다.

1. 로그인하고 연결을 만들라는 메시지가 나타나면 다음 옵션 중 하나를 선택합니다.

   * SharePoint Online의 경우 **로그인** 을 선택하고 사용자 자격 증명을 인증합니다.
   * SharePoint Server의 경우 **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택합니다. 사용할 게이트웨이 리소스, 인증 형식 및 기타 필요한 세부 정보에 대한 요청 정보를 제공합니다.

1. 완료되면 **만들기** 를 선택합니다.

   워크플로에서 연결을 성공적으로 만든 후 선택한 트리거가 나타납니다.

1. 트리거를 설정하고 워크플로 빌드를 계속하기 위한 정보를 제공합니다.

## <a name="add-an-action"></a>작업 추가

1. 아직 열려 있지 않은 경우 Azure Portal, Visual Studio Code 또는 Visual Studio의 비주얼 디자이너에서 논리 앱 워크플로를 엽니다.

1. 다음 옵션 중 하나를 선택합니다.

   * 현재 마지막 단계로 동작을 추가하려면 **새 단계** 를 선택합니다.
   * 단계 사이에 작업을 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 더하기 기호( **+** )를 선택하고 **작업 추가** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 검색어로 `sharepoint`를 입력합니다. **SharePoint** 커넥터를 선택합니다.

1. **작업** 목록에서 사용하려는 작업을 선택합니다.

1. 로그인하고 연결을 만들라는 메시지가 나타나면 다음 옵션 중 하나를 선택합니다.

   * SharePoint Online의 경우 **로그인** 을 선택하고 사용자 자격 증명을 인증합니다.
   * SharePoint Server의 경우 **온-프레미스 데이터 게이트웨이를 통해 연결** 을 선택합니다. 사용할 게이트웨이 리소스, 인증 형식 및 기타 필요한 세부 정보에 대한 요청 정보를 제공합니다.

1. 완료되면 **만들기** 를 선택합니다.

   워크플로에서 연결을 성공적으로 만든 후 선택한 작업이 나타납니다.

1. 작업을 설정하고 워크플로 빌드를 계속하기 위한 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.