---
title: 플랫 파일 인코딩 또는 디코딩
description: Enterprise 통합 팩을 사용하여 Azure Logic Apps 엔터프라이즈 통합을 위해 플랫 파일을 인코딩하거나 디코딩합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 5956f8fb7a1de3baaac0a69ef9e5b63d445416eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327123"
---
# <a name="encode-and-decode-flat-files-in-azure-logic-apps"></a>Azure Logic Apps 플랫 파일 인코딩 및 디코딩

B2B(business-to-business) 시나리오에서 비즈니스 파트너에게 XML 콘텐츠를 보내기 전에 먼저 해당 콘텐츠를 인코딩해야 할 수 있습니다. 논리 앱 워크플로를 빌드하면 [기본 제공](../connectors/built-in.md#integration-account-built-in-actions) 플랫 파일 작업을 사용하여 **플랫** 파일을 인코딩하고 디코딩할 수 있습니다.

플랫 **파일** 트리거는 사용할 수 없지만 다른 트리거 또는 작업을 사용하여 인코딩 또는 디코딩을 위해 다양한 소스의 XML 콘텐츠를 워크플로에 제공하거나 얻을 수 있습니다. 예를 들어 요청 트리거, 다른 앱 또는 [Azure Logic Apps 에서 지원하는](../connectors/apis-list.md)다른 커넥터를 사용할 수 있습니다. [ **논리 앱(소비) 및 논리 앱(표준)** 리소스  종류에서 워크플로와](single-tenant-overview-compare.md)함께 **플랫 파일** 작업을 사용할 수 있습니다.

> [!NOTE]
> **논리 앱(표준)의** 경우 **플랫 파일** 작업은 현재 미리 보기로 제공됩니다. 

이 문서에서는 플랫 파일 인코딩 및 디코딩 작업을 기존 논리 앱 워크플로에 추가하는 방법을 보여줍니다. 논리 앱을 처음 사용하는 경우 다음 문서를 참조하세요.

* [Azure Logic Apps란?](logic-apps-overview.md)
* [Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하는 B2B 엔터프라이즈 통합 워크플로](logic-apps-enterprise-integration-overview.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 다음 항목이 필요합니다.

    * XML 콘텐츠를 인코딩하거나 디코딩하는 데 사용할 플랫 파일 [스키마입니다.](logic-apps-enterprise-integration-schemas.md)

    * [논리 앱 리소스 에 대한 링크입니다.](logic-apps-enterprise-integration-create-integration-account.md#link-account)

  * [ **논리 앱(표준)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 스키마를 저장하지 않습니다. 대신 Azure Portal 또는 Visual Studio Code 사용하여 [논리 앱 리소스에 스키마를 직접 추가할](logic-apps-enterprise-integration-schemas.md) 수 있습니다. 그런 다음 *동일한 논리 앱 리소스* 내의 여러 워크플로에서 이러한 스키마를 사용할 수 있습니다.

    [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업을 사용하는 것과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장하려면 통합 계정이 여전히 필요합니다. 그러나 논리 앱 리소스를 통합 계정에 연결할 필요가 없으므로 연결 기능이 존재하지 않습니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

    > [!NOTE]
    > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

* **플랫 파일** 작업을 사용하려는 논리 앱 워크플로(비어 있거나 기존)입니다.

  빈 워크플로가 있는 경우 워크플로를 시작하려는 트리거를 사용합니다. 이 예제에서는 요청 트리거를 사용합니다.

## <a name="limits"></a>제한

생성하는 플랫 파일 스키마의 포함된 XML 그룹에 `max count` *1보다 큰* 값으로 설정된 속성의 수가 과도하지 않은지 확인합니다. 속성 값이 1보다 큰 XML 그룹을 `max count` 속성이 1보다 큰 다른 XML 그룹 내에 중첩하지 않도록 `max count` 합니다.

플랫 파일 스키마가 다음 조각을 선택할 수 있도록 할 때마다 스키마를 구문 분석하는 Azure Logic Apps 엔진은 해당 조각에 대한 *기호와* *예측을* 생성합니다. 스키마에서 이러한 구문을 너무 많이 허용하는 경우(예: 100,000개 초과) 스키마 확장이 너무 커져서 리소스와 시간이 너무 많이 소비됩니다.

## <a name="add-flat-file-encoding-action"></a>플랫 파일 인코딩 작업 추가

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 트리거가 없는 빈 워크플로가 있는 경우 원하는 트리거를 추가합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

   이 예제에서는 HTTP 요청을 받을 때 라는 Request **트리거를** 사용하고 논리 앱 워크플로 외부에서 인바운드 요청을 처리합니다. 요청 트리거를 추가하려면 다음 단계를 수행합니다.

   1. 디자이너 검색 상자에서 **기본 제공 을** 선택합니다. 디자이너 검색 상자에 를 `HTTP request` 입력합니다.

   1. 트리거 목록에서 HTTP 요청을 받을 때 라는 **요청 트리거를** 선택합니다.

   > [!TIP]
   > JSON 스키마를 제공하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용하여 스키마 생성** 을 선택하고 샘플 페이로드를 입력한 다음 **완료** 를 선택합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **플랫 파일 인코딩** 작업을 추가하려는 워크플로의 단계에서 옵션을 선택합니다.

   * 워크플로의 끝에 **플랫 파일 인코딩** 작업을 추가하려면 **새 단계** 를 선택합니다.

   * 기존 단계 사이에 **플랫 파일 인코딩** 작업을 추가하려면 더하기 기호( )가 표시되도록 해당 단계를 연결하는 화살표 위로 **+** 포인터를 이동합니다. 더하기 기호를 선택한 다음, **작업 추가** 를 선택합니다.

1. **작업 선택** 검색 상자에서 `flat file`를 입력합니다. 작업 목록에서 **플랫 파일 인코딩이라는** 작업을 선택합니다.

   ![검색 상자에 "플랫 파일"이 있고 "플랫 파일 인코딩" 작업이 선택된 Azure Portal 및 소비 디자이너를 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-consumption.png)

1. **콘텐츠** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **HTTP 요청이 수신되는 경우** 섹션에서 트리거의 요청 본문 출력과 인코딩할 콘텐츠를 포함하는 **본문** 속성을 선택합니다.

   ![동적 콘텐츠 목록 및 인코딩을 위해 선택한 콘텐츠가 있는 소비 디자이너 및 "콘텐츠" 속성을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-consumption.png)

   > [!TIP]
   > **본문** 속성이 동적 콘텐츠 목록에 표시되지 않으면 HTTP 요청을 **받은 경우** 섹션 레이블 옆에 있는 **자세히 보기를** 선택합니다.
   > **콘텐츠** 상자에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다.

1. **스키마 이름** 목록에서 인코딩에 사용할 연결된 통합 계정에 있는 스키마를 선택합니다. 예를 들면 다음과 같습니다.

   ![인코딩에 사용할 스키마가 선택된 소비 디자이너 및 열린 "스키마 이름" 목록을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-consumption.png)

   > [!NOTE]
   > 목록에 스키마가 표시되지 않으면 인코딩에 사용할 스키마 파일이 통합 계정에 없다는 뜻입니다. 사용하려는 스키마를 통합 계정에 업로드하세요.

1. 워크플로를 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 워크플로를 테스트하려면 요청 트리거의 HTTP **POST URL** 속성에 표시되는 HTTPS 엔드포인트에 요청을 보내고 요청 본문에 인코딩하려는 XML 콘텐츠를 포함합니다.

이제 플랫 파일 인코딩 작업 설정을 완료하였습니다. 실제 앱에서는 인코딩된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 또는 인코딩된 데이터를 거래 업체에 보낼 수도 있습니다. 인코딩 작업의 출력을 Salesforce 또는 거래 업체에 보내려면 [Azure Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)를 사용합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 트리거가 없는 빈 워크플로가 있는 경우 원하는 트리거를 추가합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

   이 예제에서는 HTTP 요청을 받을 때 라는 Request **트리거를** 사용하고 논리 앱 워크플로 외부에서 인바운드 요청을 처리합니다. 요청 트리거를 추가하려면 다음 단계를 수행합니다.

   1. 디자이너에서 작업 **선택을 선택합니다.** 열리는 **작업 선택** 창의 검색 상자에서 기본 **제공** 을 선택합니다.

   1. 검색 상자에 `HTTP request`를 입력합니다. 트리거 목록에서 HTTP 요청을 받을 때 라는 **요청 트리거를** 선택합니다.

     > [!TIP]
     > JSON 스키마를 제공하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용하여 스키마 생성** 을 선택하고 샘플 페이로드를 입력한 다음 **완료** 를 선택합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **플랫 파일 인코딩** 작업을 추가하려는 워크플로의 단계에서 옵션을 선택합니다.

   * 워크플로의 끝에 **플랫 파일 인코딩** 작업을 추가하려면 더하기 기호()를 **+** 선택한 다음, 작업 **추가를** 선택합니다.

   * 기존 단계 사이에 **플랫 파일 인코딩** 작업을 추가하려면 해당 단계 사이에 나타나는 더하기 기호( **+** )를 선택한 다음 새 단계 **삽입을** 선택합니다.

1. 열리는 **작업 선택** 창의 검색 상자에서 기본 **제공** 을 선택합니다.

1. 검색 상자에 `flat file`를 입력합니다. 작업 목록에서 **플랫 파일 인코딩이라는** 작업을 선택합니다.

   ![검색 상자에 "플랫 파일"이 있고 "플랫 파일 인코딩" 작업이 선택된 Azure Portal 및 표준 워크플로 디자이너를 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/flat-file-encoding-standard.png)

1. **콘텐츠** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **HTTP 요청이 수신되는 경우** 섹션에서 트리거의 요청 본문 출력과 인코딩할 콘텐츠를 포함하는 **본문** 속성을 선택합니다.

   ![인코딩을 위해 동적 콘텐츠 목록 및 콘텐츠가 선택된 표준 워크플로 디자이너 및 "콘텐츠" 속성을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-encode-standard.png)

   > [!TIP]
   > **본문** 속성이 동적 콘텐츠 목록에 표시되지 않으면 HTTP 요청을 **받은 경우** 섹션 레이블 옆에 있는 **자세히 보기를** 선택합니다.
   > 콘텐츠 **상자에** 인코딩할 콘텐츠를 직접 입력할 수도 있습니다.

1. **이름** 목록에서 인코딩을 위해 이전에 논리 앱 리소스에 업로드한 스키마를 선택합니다. 예를 들면 다음과 같습니다.

   ![인코딩에 사용할 스키마가 선택된 표준 워크플로 디자이너 및 열린 "이름" 목록을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-encoding-schema-standard.png)

   > [!NOTE]
   > 목록에 스키마가 표시되지 않으면 표준 논리 앱 리소스에 인코딩에 사용할 스키마 파일이 포함되지 않습니다. [표준 논리 앱 리소스 에 사용하려는 스키마를 업로드하는](logic-apps-enterprise-integration-schemas.md)방법을 알아봅니다.

1. 워크플로를 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 워크플로를 테스트하려면 요청 트리거의 HTTP **POST URL** 속성에 표시되는 HTTPS 엔드포인트에 요청을 보내고 요청 본문에 인코딩하려는 XML 콘텐츠를 포함합니다.

이제 플랫 파일 인코딩 작업 설정을 완료하였습니다. 실제 앱에서는 인코딩된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 또는 인코딩된 데이터를 거래 업체에 보낼 수도 있습니다. 인코딩 작업의 출력을 Salesforce 또는 거래 업체에 보내려면 [Azure Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)를 사용합니다.

---

## <a name="add-flat-file-decoding-action"></a>플랫 파일 디코딩 작업 추가

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 트리거가 없는 빈 워크플로가 있는 경우 원하는 트리거를 추가합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

   이 예제에서는 HTTP 요청을 받을 때 라는 Request **트리거를** 사용하고 논리 앱 워크플로 외부에서 인바운드 요청을 처리합니다. 요청 트리거를 추가하려면 다음 단계를 수행합니다.

   1. 디자이너 검색 상자에서 **기본 제공 을** 선택합니다. 디자이너 검색 상자에 를 `HTTP request` 입력합니다.

   1. 트리거 목록에서 HTTP 요청을 받을 때 라는 **요청 트리거를** 선택합니다.

   > [!TIP]
   > JSON 스키마를 제공하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용하여 스키마 생성** 을 선택하고 샘플 페이로드를 입력한 다음 **완료** 를 선택합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **플랫 파일 디코딩** 작업을 추가하려는 워크플로의 단계에서 옵션을 선택합니다.

   * 워크플로의 끝에 **플랫 파일 디코딩** 작업을 추가하려면 **새 단계** 를 선택합니다.

   * 기존 단계 사이에 **플랫 파일 디코딩** 작업을 추가하려면 더하기 기호( )가 표시되도록 해당 단계를 연결하는 화살표 위로 **+** 포인터를 이동합니다. 더하기 기호를 선택한 다음, **작업 추가** 를 선택합니다.

1. **작업 선택** 검색 상자에서 `flat file`를 입력합니다. 작업 목록에서 **플랫 파일 디코딩이라는** 작업을 선택합니다.

   ![검색 상자에 "플랫 파일"이 있고 "플랫 파일 디코딩" 작업이 선택된 Azure Portal 및 소비 디자이너를 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-consumption.png)

1. **콘텐츠** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **HTTP 요청이 수신되는 경우** 섹션에서 트리거의 요청 본문 출력과 디코딩할 콘텐츠를 포함하는 **본문** 속성을 선택합니다.

   ![동적 콘텐츠 목록과 디코딩을 위해 선택한 콘텐츠가 있는 소비 디자이너 및 "콘텐츠" 속성을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-consumption.png)

   > [!TIP]
   > **본문** 속성이 동적 콘텐츠 목록에 표시되지 않으면 HTTP 요청을 **받은 경우** 섹션 레이블 옆에 있는 **자세히 보기를** 선택합니다. **콘텐츠** 상자에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다.

1. **스키마 이름** 목록에서 디코딩에 사용할 연결된 통합 계정에 있는 스키마를 선택합니다. 예를 들면 다음과 같습니다.

   ![디코딩에 사용할 스키마가 선택된 소비 디자이너 및 열린 "스키마 이름" 목록을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-consumption.png)

   > [!NOTE]
   > 목록에 스키마가 표시되지 않으면 디코딩에 사용할 스키마 파일이 통합 계정에 없다는 뜻입니다. 사용하려는 스키마를 통합 계정에 업로드하세요.

1. 워크플로를 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 워크플로를 테스트하려면 요청 트리거의 HTTP **POST URL** 속성에 표시되는 HTTPS 엔드포인트에 요청을 보내고 요청 본문에 디코딩하려는 XML 콘텐츠를 포함합니다.

이제 플랫 파일 디코딩 작업 설정을 완료하였습니다. 실제 앱에서는 디코딩된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 또는 디코딩된 데이터를 거래 업체에 보낼 수도 있습니다. 디코딩 작업의 출력을 Salesforce 또는 거래 업체에 보내려면 [Azure Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)를 사용합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 트리거가 없는 빈 워크플로가 있는 경우 원하는 트리거를 추가합니다. 그렇지 않은 경우 다음 단계를 계속 진행합니다.

   이 예제에서는 HTTP 요청을 받을 때 라는 Request **트리거를** 사용하고 논리 앱 워크플로 외부에서 인바운드 요청을 처리합니다. 요청 트리거를 추가하려면 다음 단계를 수행합니다.

   1. 디자이너에서 작업 **선택을 선택합니다.** 열리는 **작업 선택** 창의 검색 상자에서 기본 **제공** 을 선택합니다.

   1. 검색 상자에 `HTTP request`를 입력합니다. 트리거 목록에서 HTTP 요청을 받을 때 라는 **요청 트리거를** 선택합니다.

     > [!TIP]
     > JSON 스키마를 제공하는 것은 선택 사항입니다. 인바운드 요청의 샘플 페이로드가 있으면 **샘플 페이로드를 사용하여 스키마 생성** 을 선택하고 샘플 페이로드를 입력한 다음 **완료** 를 선택합니다. 스키마가 **요청 본문 JSON 스키마** 상자에 나타납니다.

1. **플랫 파일 디코딩** 작업을 추가하려는 워크플로의 단계에서 옵션을 선택합니다.

   * 워크플로의 끝에 **플랫 파일 디코딩** 작업을 추가하려면 더하기 **+** 기호()를 선택한 다음, **작업 추가를** 선택합니다.

   * 기존 단계 사이에 **플랫 파일 디코딩** 작업을 추가하려면 해당 단계 사이에 나타나는 더하기 기호( **+** )를 선택한 다음 새 단계 **삽입을** 선택합니다.

1. 열리는 **작업 선택** 창의 검색 상자에서 기본 **제공** 을 선택합니다.

1. 검색 상자에 `flat file`를 입력합니다. 작업 목록에서 **플랫 파일 디코딩이라는** 작업을 선택합니다.

   ![검색 상자에 "플랫 파일"이 있고 "플랫 파일 디코딩" 작업이 선택된 Azure Portal 및 표준 워크플로 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-flatfile/flat-file-decoding-standard.png)

1. **콘텐츠** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다. 목록의 **HTTP 요청이 수신되는 경우** 섹션에서 트리거의 요청 본문 출력과 디코딩할 콘텐츠를 포함하는 **본문** 속성을 선택합니다.

   ![디코딩을 위해 동적 콘텐츠 목록 및 콘텐츠가 선택된 표준 워크플로 디자이너 및 "콘텐츠" 속성을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-content-to-decode-standard.png)

   > [!TIP]
   > **본문** 속성이 동적 콘텐츠 목록에 표시되지 않으면 HTTP 요청을 **받은 경우** 섹션 레이블 옆에 있는 **자세히 보기를** 선택합니다.
   > **콘텐츠** 상자에 직접 디코딩할 콘텐츠를 입력할 수도 있습니다.

1. **이름** 목록에서 디코딩을 위해 이전에 논리 앱 리소스에 업로드한 스키마를 선택합니다. 예를 들면 다음과 같습니다.

   ![디코딩에 사용할 스키마가 선택된 표준 워크플로 디자이너 및 열린 "이름" 목록을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-flatfile/select-decoding-schema-standard.png)

   > [!NOTE]
   > 목록에 스키마가 표시되지 않으면 표준 논리 앱 리소스에 디코딩에 사용할 스키마 파일이 포함되지 않습니다. [사용하려는 스키마를 표준 논리 앱 리소스 에 업로드하는](logic-apps-enterprise-integration-schemas.md)방법을 알아봅니다.

1. 워크플로를 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 워크플로를 테스트하려면 요청 트리거의 HTTP **POST URL** 속성에 표시되는 HTTPS 엔드포인트에 요청을 보내고 요청 본문에 디코딩하려는 XML 콘텐츠를 포함합니다.

이제 플랫 파일 디코딩 작업 설정을 완료하였습니다. 실제 앱에서는 디코딩된 데이터를 SalesForce와 같은 LOB(기간 업무) 앱에 저장하려고 할 수도 있습니다. 또는 디코딩된 데이터를 거래 업체에 보낼 수도 있습니다. 디코딩 작업의 출력을 Salesforce 또는 거래 업체에 보내려면 [Azure Logic Apps에서 사용할 수 있는 다른 커넥터](../connectors/apis-list.md)를 사용합니다.

---

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩](logic-apps-enterprise-integration-overview.md)에 대해 자세히 알아보기
