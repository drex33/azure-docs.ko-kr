---
title: B2B 엔터프라이즈 통합에 대한 XML 유효성 검사
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 스키마를 사용하여 XML 유효성을 검사합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 87650a1ab950f8e88fe08a1c4555c98652776730
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099306"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로에 대한 XML 유효성 검사

종종 엔터프라이즈 통합 B2B(business-to-business) 시나리오에서 계약을 체결한 거래 파트너는 데이터 처리를 시작하기 전에 먼저 교환하는 메시지가 유효한지 확인해야 합니다. Azure Logic Apps에서 **XML 유효성 검사** 작업을 사용하여 미리 정의된 스키마에 대해 문서의 유효성을 검사할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* **XML 유효성 검사** 작업을 사용하려는 이 논리 앱 워크플로(빈 논리 앱 또는 기존 논리 앱)입니다.

  빈 워크플로가 있는 경우 원하는 트리거를 사용합니다. 이 예에서는 요청 트리거를 사용합니다.

  논리 앱을 처음 사용하는 경우 다음 문서를 참조하세요.

  * [Azure Logic Apps란?](logic-apps-overview.md)

  * [빠른 시작: 첫 번째 논리 앱 워크플로 만들기](quickstart-create-first-logic-app-workflow.md)

  * [단일 테넌트 논리 앱 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)

  * [Azure Logic Apps의 사용량 계량, 청구, 가격 책정 모델](logic-apps-pricing.md)

* **Logic App(소비)** 리소스 종류를 사용하는 경우 다음 요구 사항을 충족하는 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 있어야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결됩니다.

  * **XML 유효성 검사** 작업을 사용하려는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * 논리 앱 리소스에 [연결](logic-apps-enterprise-integration-create-integration-account.md#link-account)됩니다.

  * XML 콘텐츠의 유효성을 검사하는 데 사용할 [스키마](logic-apps-enterprise-integration-schemas.md)를 포함합니다.

  **Logic App(표준)** 리소스 종류를 사용하는 경우 통합 계정을 연결할 필요가 없습니다. 그러나 XML 콘텐츠의 유효성을 검사하는 데 사용할 [스키마](logic-apps-enterprise-integration-schemas.md)를 논리 앱 리소스에 여전히 추가해야 합니다. 논리 앱 리소스 메뉴의 **설정** 아래 **스키마** 섹션에서 이 작업을 완료할 수 있습니다.

## <a name="add-xml-validation-action"></a>XML 유효성 검사 작업 추가

1. [Azure Portal](https://portal.azure.com)의 디자이너 보기에서 논리 앱과 워크플로를 엽니다.

1. 트리거가 없는 빈 논리 앱이 있는 경우 원하는 트리거를 추가합니다. 이 예에서는 요청 트리거를 사용합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

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

   동적 콘텐츠 목록에는 워크플로에서 이전 단계의 출력을 나타내는 속성 토큰이 표시됩니다. 목록에 예상한 속성이 표시되지 않으면 목록의 트리거 또는 작업 제목에서 **자세히 보기** 를 선택할 수 있는지 확인합니다.

   소비 또는 ISE 플랜 기반 논리 앱의 경우 디자이너는 다음 예와 같습니다.

   ![동적 콘텐츠 목록이 열려 있고 "콘텐츠" 상자에 커서가 있고 동적 콘텐츠 목록이 열려 있는 다중 테넌트 디자이너를 보여 주는 스크린샷.](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   표준 플랜 기반 논리 앱의 경우 디자이너는 다음 예와 같습니다.

   ![동적 콘텐츠 목록이 열려 있고 "콘텐츠" 상자에 커서가 있고 동적 콘텐츠 목록이 열려 있는 단일 테넌트 디자이너를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. 동적 콘텐츠 목록에서 유효성을 검사할 콘텐츠에 대한 속성 토큰을 선택합니다.

   이 예에서는 트리거에서 **본문** 토큰을 선택합니다.

1. 유효성 검사에 사용할 스키마를 지정하려면 **스키마 이름** 목록을 열고 이전에 추가한 스키마를 선택합니다.

1. 완료되면 논리 앱 워크플로를 저장해야 합니다.

   이제 **XML 유효성 검사** 작업 설정이 완료되었습니다. 실제 앱에서는 유효성 검사된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 유효성을 검사한 출력을 Salesforce에 보내려면 Salesforce 작업을 추가합니다.

1. 유효성 검사 작업을 테스트하려면 워크플로를 트리거하고 실행합니다. 예를 들어 요청 트리거의 경우 트리거의 엔드포인트 URL로 요청을 보냅니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에서 XML 유효성 검사를 위한 스키마 추가](logic-apps-enterprise-integration-schemas.md)
* [Azure Logic Apps의 워크플로용 XML 변환](logic-apps-enterprise-integration-transform.md)