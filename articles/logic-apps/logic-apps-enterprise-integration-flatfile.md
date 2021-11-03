---
title: 플랫 파일 인코딩 또는 디코딩
description: 엔터프라이즈 통합 팩를 사용 하 여 Azure Logic Apps에서 엔터프라이즈 통합을 위한 플랫 파일을 인코딩 또는 디코딩합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 05/01/2021
ms.openlocfilehash: 73b7538c41f1a560d07a702660a28f41b2c0e1de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131072449"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Azure Logic Apps에서 플랫 파일 인코딩 및 디코딩

B2B(business-to-business) 시나리오에서 비즈니스 파트너에게 XML 콘텐츠를 보내기 전에 먼저 해당 콘텐츠를 인코딩해야 할 수 있습니다. 논리 앱 워크플로를 빌드하여 **플랫 파일** 커넥터를 사용 하 여 플랫 파일을 인코딩 및 디코딩할 수 있습니다. 논리 앱 워크플로는 요청 트리거, 다른 앱 또는 [Azure Logic Apps에서 지 원하는 다른 커넥터](../connectors/apis-list.md)와 같은 다양 한 소스에서이 XML 콘텐츠를 가져올 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* Azure 구독과 연결되고 **플랫 파일** 커넥터를 사용하려는 [논리 앱에 연결된](./logic-apps-enterprise-integration-create-integration-account.md#link-account) [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)입니다. 논리 앱과 통합 계정이 동일한 위치 또는 Azure 지역에 있어야 합니다.

* 통합 계정에 이미 정의된 둘 이상의 [거래 업체](logic-apps-enterprise-integration-partners.md)

* XML 콘텐츠를 인코딩 또는 디코딩하는 통합 계정에 업로드한 플랫 파일 [스키마](logic-apps-enterprise-integration-schemas.md)

* **플랫 파일** 커넥터를 사용하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거입니다. **플랫 파일** 커넥터는 작업만 제공하고 트리거를 제공하지 않습니다. 트리거 또는 다른 작업을 사용 하 여 워크플로 인코딩 또는 디코딩을 위해 XML 콘텐츠를 논리 앱에 제공할 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="limits"></a>제한

생성 하는 플랫 파일 스키마의 포함 된 XML 그룹에 너무 많은 `max count` 속성을 *1 보다 큰* 값으로 설정 하지 않았는지 확인 합니다. 1 보다 `max count` 큰 속성을 가진 다른 xml 그룹 내에서 속성 값이 1 보다 큰 xml 그룹을 중첩 하지 마십시오 `max count` .

플랫 파일 스키마에서 다음 조각 선택이 허용 될 때마다 스키마를 구문 분석 하는 Azure Logic Apps 엔진은 *기호* 와 해당 조각에 대 한 *예측* 을 생성 합니다. 스키마에서 이러한 구문을 너무 많이 허용 하는 경우 (예: 10만 개 이상) 스키마 확장이 과도 하 게 증가 하 여 리소스와 시간이 너무 많이 소비 됩니다.

## <a name="add-flat-file-encode-action"></a>플랫 파일 인코딩 작업 추가

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱 워크플로를 엽니다.

1. 논리 앱의 트리거 또는 작업에서 **새 단계** > **작업 추가** 를 선택합니다. 이 예제에서는 **HTTP 요청을 받을 때** 명명된 요청 트리거를 사용하고 논리 앱 외부에서 인바운드 요청을 처리합니다.

   > [!TIP]
   > JSON 스키마를 제공하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용하여 스키마 생성** 을 선택하고 샘플 페이로드를 입력한 다음 **완료** 를 선택합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **작업 선택** 에서 `flat file`을 입력합니다. 작업 목록에서 **플랫 파일 인코딩** 작업을 선택합니다.

   !["플랫 파일 인코딩" 작업을 선택합니다.](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-encoding.png)

1. **콘텐츠** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **HTTP 요청이 수신되는 경우** 섹션에서 트리거의 요청 본문 출력과 인코딩할 콘텐츠를 포함하는 **본문** 속성을 선택합니다.

   ![동적 콘텐츠 목록에서 인코딩할 콘텐츠 선택](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode.png)

   > [!TIP]
   > 동적 콘텐츠 목록에 **본문** 속성이 표시되지 않으면 **HTTP 요청을 수신하는 경우** 섹션 레이블 옆에 있는 **자세히 보기** 를 선택합니다.
   > **콘텐츠** 상자에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다.

1. **스키마 이름** 목록에서 인코딩에 사용할 연결된 통합 계정에 있는 스키마를 선택합니다. 예를 들면 다음과 같습니다.

   ![인코딩에 사용할 스키마 선택](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-encoding.png)

   > [!NOTE]
   > 목록에 스키마가 표시되지 않으면 인코딩에 사용할 스키마 파일이 통합 계정에 없다는 뜻입니다. 사용하려는 스키마를 통합 계정에 업로드하세요.

1. 논리 앱을 저장합니다. 커넥터를 테스트하려면 요청 트리거의 **HTTP POST URL** 속성에 표시되는 HTTPS 엔드포인트에 요청하고 요청 본문에 인코딩할 XML 콘텐츠를 포함합니다.

이제 플랫 파일 인코딩 작업 설정을 완료하였습니다. 실제 앱에서는 인코딩된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 또는 인코딩된 데이터를 거래 업체에 보낼 수도 있습니다. 인코딩 작업의 출력을 Salesforce 또는 거래 업체에 보내려면 [Azure Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)를 사용합니다.

## <a name="add-flat-file-decode-action"></a>플랫 파일 디코딩 작업 추가

1. [Azure Portal](https://portal.azure.com)의 디자이너에서 논리 앱 워크플로를 엽니다.

1. 논리 앱의 트리거 또는 작업에서 **새 단계** > **작업 추가** 를 선택합니다. 이 예제에서는 **HTTP 요청을 받을 때** 명명된 요청 트리거를 사용하고 논리 앱 외부에서 인바운드 요청을 처리합니다.

   > [!TIP]
   > JSON 스키마를 제공하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용하여 스키마 생성** 을 선택하고 샘플 페이로드를 입력한 다음 **완료** 를 선택합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **작업 선택** 에서 `flat file`을 입력합니다. 작업 목록에서 **플랫 파일 디코딩** 작업을 선택합니다.

   !["플랫 파일 디코딩" 작업을 선택합니다.](./media/logic-apps-enterprise-integration-flatfile/select-flat-file-decoding.png)

1. **콘텐츠** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **HTTP 요청이 수신되는 경우** 섹션에서 트리거의 요청 본문 출력과 디코딩할 콘텐츠를 포함하는 **본문** 속성을 선택합니다.

   ![동적 콘텐츠 목록에서 디코딩할 콘텐츠를 선택합니다.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode.png)

   > [!TIP]
   > 동적 콘텐츠 목록에 **본문** 속성이 표시되지 않으면 **HTTP 요청을 수신하는 경우** 섹션 레이블 옆에 있는 **자세히 보기** 를 선택합니다. **콘텐츠** 상자에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다.

1. **스키마 이름** 목록에서 디코딩에 사용할 연결된 통합 계정에 있는 스키마를 선택합니다. 예를 들면 다음과 같습니다.

   ![디코딩에 사용할 스키마 선택](./media/logic-apps-enterprise-integration-flatfile/select-schema-for-decoding.png)

   > [!NOTE]
   > 목록에 스키마가 표시되지 않으면 디코딩에 사용할 스키마 파일이 통합 계정에 없다는 뜻입니다. 사용하려는 스키마를 통합 계정에 업로드하세요.

1. 논리 앱을 저장합니다. 커넥터를 테스트하려면 요청 트리거의 **HTTP POST URL** 속성에 표시되는 HTTPS 엔드포인트에 요청하고 요청 본문에 디코딩할 XML 콘텐츠를 포함합니다.

이제 플랫 파일 디코딩 작업 설정을 완료하였습니다. 실제 앱에서는 디코딩된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 또는 디코딩된 데이터를 거래 업체에 보낼 수도 있습니다. 디코딩 작업의 출력을 Salesforce 또는 거래 업체에 보내려면 [Azure Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md)에 대해 자세히 알아보기
