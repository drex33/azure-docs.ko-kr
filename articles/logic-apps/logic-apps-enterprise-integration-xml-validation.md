---
title: 엔터프라이즈 통합 워크플로에서 XML 유효성 검사
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 스키마를 사용하여 XML 유효성을 검사합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 842b26502dcfa073bca21891eed44fe990037f06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660807"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로에 대한 XML 유효성 검사

엔터프라이즈 통합 B2B(기업 간) 시나리오에서 계약의 거래 파트너는 데이터 처리를 시작하기 전에 교환하는 메시지가 유효한지 확인해야 하는 경우가 많습니다. 논리 앱 워크플로는 XML 유효성 검사 작업 및 미리 정의된 [스키마](logic-apps-enterprise-integration-schemas.md)를 사용하여 XML 메시지 및 문서의 **유효성을 검사할** 수 있습니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps 및 Enterprise 통합 팩을 통해 B2B 엔터프라이즈 통합 워크플로를](logic-apps-enterprise-integration-overview.md)검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* **XML 유효성 검사** 작업을 사용하려는 이 논리 앱 워크플로(빈 논리 앱 또는 기존 논리 앱)입니다.

  빈 워크플로가 있는 경우 원하는 트리거를 사용합니다. 이 예에서는 요청 트리거를 사용합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * **XML 유효성 검사*** 작업을 사용하려는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 있습니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 다음 항목이 필요합니다.

    * XML 콘텐츠의 유효성을 검사하는 데 사용할 [스키마입니다.](logic-apps-enterprise-integration-schemas.md)

    * [논리 앱 리소스 에 대한 링크입니다.](logic-apps-enterprise-integration-create-integration-account.md#link-account)

  * [ **논리 앱(표준)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 스키마를 저장하지 않습니다. 대신 Azure Portal 또는 Visual Studio Code 사용하여 [논리 앱 리소스에 스키마를 직접 추가할](logic-apps-enterprise-integration-schemas.md) 수 있습니다. 그런 다음 *동일한 논리 앱 리소스* 내의 여러 워크플로에서 이러한 스키마를 사용할 수 있습니다.

    [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업을 사용하는 것과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장하려면 통합 계정이 여전히 필요합니다. 그러나 논리 앱 리소스를 통합 계정에 연결할 필요가 없으므로 연결 기능이 존재하지 않습니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

    > [!NOTE]
    > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

## <a name="add-xml-validation-action"></a>XML 유효성 검사 작업 추가

1. [Azure Portal](https://portal.azure.com)의 디자이너 보기에서 논리 앱과 워크플로를 엽니다.

1. 트리거가 없는 빈 논리 앱이 있는 경우 원하는 트리거를 추가 합니다. 이 예제에서는 요청 트리거를 사용합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

   요청 트리거를 추가하려면 디자이너 검색 상자에 `HTTP request`를 입력하고 **HTTP 요청이 수신될 때** 라는 요청 트리거를 선택합니다.

1. **XML 유효성 검사** 작업을 추가하려는 워크플로의 단계에서 다음 단계 중 하나를 선택합니다.

   소비 또는 ISE 플랜 기반 논리 앱의 경우 단계를 선택합니다.

   * 워크플로 끝에 **XML 유효성 검사** 작업을 추가하려면 **새 단계** 를 선택합니다.

   * 기존 단계 사이에 **XML 유효성 검사** 작업을 추가하려면 더하기 기호( **+** )가 나타날 때까지 해당 단계를 연결하는 화살표 위로 포인터를 이동합니다. 더하기 기호를 선택한 다음, **작업 추가** 를 선택합니다.

   표준 플랜 기반 논리 앱의 경우 단계를 선택합니다.

   * 워크플로 끝에 **XML 유효성 검사** 작업을 추가하려면 더하기 기호( **+** )를 선택한 다음 **작업 추가** 를 선택합니다.

   * 기존 단계 사이에 **XML 유효성 검사** 작업을 추가하려면 해당 단계 사이에 표시되는 더하기 기호( **+** )를 선택한 다음 **작업 추가** 를 선택합니다.

1. **작업 선택** 에서 **기본 제공** 을 선택합니다. 검색 상자에 `xml validation`를 입력합니다. 작업 목록에서 **XML 유효성 검사** 를 선택합니다.

1. 유효성을 검사할 XML 콘텐츠를 지정하려면 동적 콘텐츠 목록이 표시되도록 **콘텐츠** 상자 내부를 클릭합니다.

   동적 콘텐츠 목록에는 워크플로에서 이전 단계의 출력을 나타내는 속성 토큰이 표시됩니다. 목록에 예상한 속성이 표시되지 않으면 트리거 또는 작업 제목에서 **자세히 보기** 를 선택할 수 있는지 확인합니다.

   소비 또는 ISE 계획 기반 논리 앱의 경우 디자이너는 다음 예제와 같습니다.

   ![동적 콘텐츠 목록이 열려 있고 "콘텐츠" 상자에 커서가 있으며 동적 콘텐츠 목록이 열려 있는 다중 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   표준 계획 기반 논리 앱의 경우 디자이너는 다음 예제와 같습니다.

   ![동적 콘텐츠 목록이 열려 있고 "콘텐츠" 상자에 커서가 있으며 동적 콘텐츠 목록이 열려 있는 단일 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. 동적 콘텐츠 목록에서 유효성을 검사할 콘텐츠가 있는 속성 토큰을 선택합니다.

   이 예에서는 트리거에서 **본문** 토큰을 선택합니다.

1. 유효성 검사에 사용할 스키마를 지정하려면 **스키마 이름** 목록을 열고 이전에 추가한 스키마를 선택합니다.

1. 완료되면 논리 앱 워크플로를 저장해야 합니다.

   이제 **XML 유효성 검사** 작업 설정이 완료되었습니다. 실제 앱에서는 유효성 검사된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 유효성을 검사한 출력을 Salesforce에 보내려면 Salesforce 작업을 추가합니다.

1. 유효성 검사 작업을 테스트하려면 워크플로를 트리거하고 실행합니다. 예를 들어 요청 트리거의 경우 트리거의 엔드포인트 URL로 요청을 보냅니다.

   XML 유효성 검사 동작은 워크플로가 트리거된 후 유효성 **검사에** XML 콘텐츠를 사용할 수 있는 경우에 실행됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 XML 유효성 검사를 위한 스키마 추가](logic-apps-enterprise-integration-schemas.md)
* [Azure Logic Apps의 워크플로용 XML 변환](logic-apps-enterprise-integration-transform.md)