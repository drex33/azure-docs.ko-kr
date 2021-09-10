---
title: 기본 제공 트리거 및 작업
description: 기본 제공 트리거 및 동작을 사용하면 앱, 데이터, 서비스, 시스템을 통합하고, 워크플로를 제어하고, Azure Logic Apps를 사용하여 데이터를 관리하는 자동 워크플로를 만들 수 있습니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 83800e088599bca0023d734bba52b6ed3207f0a3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568375"
---
# <a name="built-in-triggers-and-actions-in-azure-logic-apps"></a>Azure Logic Apps용 기본 제공 트리거 및 작업

[기본 제공 트리거 및 동작](apis-list.md)을 통해 [워크플로의 일정 및 구조를 제어하고](#control-workflow), [고유의 코드를 실행하고](#run-code-from-workflows), [데이터를 관리 또는 조작하고](#manage-or-manipulate-data), 워크플로의 기타 작업을 완료할 수 있습니다. [관리형 커넥터](managed.md)와 달리, 대부분의 기본 제공 작업은 특정 서비스, 시스템, 프로토콜에 연결되어 있지 않습니다. 예를 들어 되풀이 트리거를 사용하여 일정에 따라 거의 모든 워크플로를 시작할 수 있습니다. 또는 요청 트리거를 사용하여 호출될 때까지 워크플로를 대기시킬 수 있습니다. 모든 기본 제공 작업은 기본적으로 Azure Logic Apps에서 실행되며, 대부분의 경우 사용 전에 연결을 생성할 필요가 없습니다.

더 적은 수의 서비스, 시스템, 프로토콜의 경우, Azure Logic Apps는 Azure App Services, Azure Functions, Azure API Management 및 다른 Azure Logic Apps 논리 앱 워크플로를 호출하는 등의 기본 제공 작업을 제공합니다. 사용 가능한 수와 범위는 다중 테넌트 Azure Logic Apps에서 실행되는 소비 계획 기반 논리 앱 리소스를 만드는지 또는 단일 테넌트 Azure Logic Apps에서 실행되는 표준 계획 기반 논리 앱 리소스를 만드는지에 따라 다릅니다. 자세한 내용은 [단일 테넌트와 다중 테넌트 및 통합 서비스 환경 비교](../logic-apps/single-tenant-overview-compare.md)를 참조하세요. 대부분의 경우 기본 제공 버전은 더 나은 성능, 기능, 가격 책정 등을 제공합니다.

예를 들어, 단일 테넌트 논리 앱을 생성하는 경우, 기본 제공 작업 및 [관리 커넥터 작업](managed.md)을 모두 몇 가지 서비스, 특히 Azure Service Bus, Azure Event Hubs, SQL Server, DB2, MQ에 사용할 수 있습니다. 경우에 따라, 기본 제공 작업은 한 환경에서만 사용할 수 있습니다. 예를 들어 플랫 파일은 현재 다중 테넌트에서 사용할 수 있는 반면, IBM 호스트 파일은 단일 테넌트에서만 사용할 수 있습니다. 대부분의 경우 기본 제공 버전은 더 나은 성능, 기능, 가격 책정 등을 제공합니다.

다음 목록에서는 [기본 제공 트리거 및 동작](#general-built-in-triggers-and-actions)으로 수행할 수 있는 작업 중 일부만 설명합니다.

- 사용자 지정 및 고급 일정을 사용하여 워크플로 실행. 예약에 대한 자세한 내용은 [Azure Logic Apps 커넥터 개요의 되풀이 동작 섹션](apis-list.md#recurrence-behavior)을 검토하세요.

- 워크플로의 구조를 구성 및 제어(예: 루프 및 조건 사용)

- 변수, 날짜, 데이터 작업, 콘텐츠 변환, 일괄 처리 작업 수행

- HTTP 트리거 및 동작을 사용하여 다른 엔드포인트와 통신

- 요청 받기 및 응답

- 자체 함수(Azure Functions), 웹앱(Azure App Services), API(Azure API Management), 요청을 받을 수 있는 기타 Logic Apps 워크플로 등을 호출합니다.

## <a name="general-built-in-triggers-and-actions"></a>일반 기본 제공 트리거 및 동작

Azure Logic Apps에서는 다음과 같은 기본 제공 트리거 및 동작을 제공합니다.

:::row:::
    :::column:::
        [![일정 아이콘][schedule-icon]][schedule-doc]
        \
        \
        [**일정**][schedule-doc]
        \
        \
        [**되풀이**][schedule-recurrence-doc]: 지정된 되풀이에 따라 워크플로를 트리거합니다.
        \
        \
        [**슬라이딩 윈도우**][schedule-sliding-window-doc]: 연속 청크로 데이터를 처리해야 하는 워크플로를 트리거합니다.
        \
        \
        [**지연**][schedule-delay-doc]: 지정된 기간 동안 워크플로를 일시 중지합니다.
        \
        \
        [**다음 기간까지 지연**][schedule-delay-until-doc]: 지정된 날짜 및 시간까지 워크플로를 일시 중지합니다.
    :::column-end:::
    :::column:::
        [![HTTP 트리거 및 작업 아이콘][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        HTTP 트리거 또는 동작을 사용하여 HTTP 또는 HTTPS 엔드포인트를 호출합니다.
        \
        \
        다음과 같은 기타 기본 제공 HTTP 트리거 및 동작도 사용할 수 있습니다.
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + 웹후크][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![요청 트리거 아이콘][http-request-icon]][http-request-doc]
        \
        \
        [**요청**][http-request-doc]
        \
        \
        [**HTTP 요청을 수신하는 경우**][http-request-doc]: 다른 워크플로, 앱, 서비스에서 요청을 수신할 때까지 기다립니다. 이 트리거를 사용하면 일정에 따라 확인하거나 폴링하지 않고도 워크플로를 호출할 수 있습니다.
        \
        \
        [**응답**][http-request-doc]: 같은 워크플로의 **HTTP 요청을 수신하는 경우** 트리거에서 받은 요청에 응답합니다.
    :::column-end:::
    :::column:::
        [![일괄 처리 아이콘][batch-icon]][batch-doc]
        \
        \
        [**일괄 처리**][batch-doc]
        \
        \
        [**메시지 일괄 처리**][batch-doc]: 일괄 처리로 메시지를 처리하는 워크플로를 트리거합니다.
        \
        \
        [**일괄 처리할 메시지 보내기**][batch-doc]: 현재 **메시지 일괄 처리** 트리거로 시작하는 기존 워크플로를 호출합니다.
    :::column-end:::
:::row-end:::

## <a name="service-based-built-in-trigger-and-actions"></a>서비스 기반 기본 제공 트리거 및 작업

Azure Logic Apps는 다음 서비스에 대해 다음과 같은 기본 제공 작업을 제공합니다.

:::row:::
    :::column:::
        [![Azure API Management 아이콘][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        [Azure API Management](../api-management/api-management-key-concepts.md)를 사용하여 정의, 관리, 게시하는 API에서 고유한 트리거 및 동작을 호출합니다. <p><p>**참고**: [API Management의 소비 계층](../api-management/api-management-features.md)을 사용할 경우 지원되지 않습니다.
    :::column-end:::
    :::column:::
        [![Azure App Services icon][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        [Azure App Service](../app-service/overview.md)(예: API Apps, Web Apps)에서 만들고 호스트하는 앱을 호출합니다.
        \
        \
        Swagger가 포함되는 경우 이러한 앱에서 정의된 트리거 및 동작은 Azure Logic Apps에서 다른 모든 고급 트리거 및 동작처럼 나타납니다.
    :::column-end:::
    :::column:::
        [![Azure Logic Apps 아이콘][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        **HTTP 요청을 수신하는 경우** 라는 요청 트리거로 시작하는 다른 워크플로를 호출합니다.
    :::column-end:::
    :::column:::
        [![SQL Server 아이콘][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc] <br>(*단일 테넌트 전용*)     \
        \
        온-프레미스의 SQL Server 또는 클라우드의 Azure SQL Database에 연결하여 레코드를 관리하고 저장 프로시저를 실행하거나 쿼리를 수행할 수 있습니다. <p>**참고**: 단일 테넌트 Azure Logic Apps는 SQL 기본 제공 및 관리 커넥터 작업을 제공하지만, 다중 테넌트 Azure Logic Apps는 관리 커넥터 작업만 제공합니다. <p>자세한 내용은 [Azure Logic Apps의 단일 테넌트와 다중 테넌트 및 통합 서비스 환경(ISE) 비교](../logic-apps/single-tenant-overview-compare.md)를 참조하세요.
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>워크플로에서 코드 실행

Azure Logic Apps에서는 워크플로에서 고유한 코드를 실행하기 위한 다음과 같은 기본 제공 작업을 제공합니다.

:::row:::
    :::column:::
        [![Azure Functions 아이콘][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure Functions**][azure-functions-doc]
        \
        \
        [Azure에서 호스트되는 함수](../azure-functions/functions-overview.md)를 호출하여 워크플로 내에서 고유한 ‘코드 조각’(C# 또는 Node.js)을 실행합니다.
    :::column-end:::
    :::column:::
        [![Inline Code 작업 아이콘][inline-code-icon]][inline-code-doc]
        \
        \
        [**인라인 코드**][inline-code-doc]
        \
        \
        [**JavaScript 코드 실행**][inline-code-doc]: 워크플로 내에서 고유한 인라인 JavaScript ‘코드 조각’을 추가하고 실행합니다.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>워크플로 제어

Azure Logic Apps에서는 워크플로에서 작업을 구성하고 제어하기 위한 다음과 같은 기본 제공 작업을 제공합니다.

:::row:::
    :::column:::
        [![Condition 작업 아이콘][condition-icon]][condition-doc]
        \
        \
        [**Condition**][condition-doc]
        \
        \
        조건을 평가하고, 조건이 true 또는 false인지에 따라 별도의 작업을 실행합니다.
    :::column-end:::
    :::column:::
        [![For Each 작업 아이콘][for-each-icon]][for-each-doc]
        \
        \
        [**For Each**][for-each-doc]
        \
        \
        배열의 모든 항목에 대해 동일한 작업을 수행합니다.
    :::column-end:::
    :::column:::
        [![Scope 작업 아이콘][scope-icon]][scope-doc]
        \
        \
        [**이름**][scope-doc]
        \
        \
        범위에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 *범위* 로 작업을 그룹화합니다.
    :::column-end:::
    :::column:::
        [![Switch 작업 아이콘][switch-icon]][switch-doc]
        \
        \
        [**Switch**][switch-doc]
        \
        \
        기본 사례를 제외하고는 고유한 값이 할당된 *사례* 로 작업을 그룹화합니다. 할당된 값이 식, 개체 또는 토큰의 결과와 일치하는 사례만 실행합니다. 일치하는 사례가 없는 경우 기본 사례를 실행합니다.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Terminate 작업 아이콘][terminate-icon]][terminate-doc]
        \
        \
        [**Terminate**][terminate-doc]
        \
        \
        활발히 실행 중인 논리 앱 워크플로를 중지합니다.
    :::column-end:::
    :::column:::
        [![Until 작업 아이콘][until-icon]][until-doc]
        \
        \
        [**Until**][until-doc]
        \
        \
        지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업을 반복합니다.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>데이터 관리 또는 조작

Azure Logic Apps에서는 데이터 출력 및 해당 형식으로 작업하기 위한 다음과 같은 기본제공 작업을 제공합니다.

:::row:::
    :::column:::
        [![Data Operations 아이콘][data-operations-icon]][data-operations-doc]
        \
        \
        [**데이터 작업**][data-operations-doc]
        \
        \
        데이터를 사용하여 작업을 수행합니다.
        \
        \
        **작성**: 다양한 형식의 여러 입력에서 하나의 출력을 만듭니다.
        \
        \
        **CSV 테이블 만들기**: JSON 개체를 사용하여 배열에서 CSV(쉼표로 구분된 값) 테이블을 만듭니다.
        \
        \
        **HTML 테이블 만들기**: JSON 개체를 사용하여 배열에서 HTML 테이블을 만듭니다.
        \
        \
        **배열 필터링**: 조건을 충족하는 다른 배열의 항목에서 배열을 만듭니다.
        \
        \
        **조인**: 배열의 모든 항목에서 문자열을 만들고, 지정한 구분 기호를 사용하여 해당 항목을 구분합니다.
        \
        \
        **JSON 구문 분석**: JSON 콘텐츠의 속성 및 해당 값에서 사용자에게 친숙한 토큰을 만들어 워크플로에서 이러한 속성을 사용할 수 있습니다.
        \
        \
        **선택**: 다른 배열의 항목이나 값을 변환하고 해당 항목을 지정한 속성에 매핑하여 JSON 개체가 포함된 배열을 만듭니다.
    :::column-end:::
    :::column:::
        ![Date Time 작업 아이콘][date-time-icon]
        \
        \
        **날짜/시간**
        \
        \
        타임스탬프를 사용하여 작업을 수행합니다.
        \
        \
        **시간에 추가**: 타임스탬프에 지정한 단위 수를 추가합니다.
        \
        \
        **표준 시간대 변환**: 타임스탬프를 원본 표준 시간대에서 대상 표준 시간대로 변환합니다.
        \
        \
        **현재 시간**: 현재 타임스탬프를 문자열로 반환합니다.
        \
        \
        **미래 시간 가져오기**: 현재 타임스탬프에 지정한 시간 단위를 더한 값을 반환합니다.
        \
        \
        **과거 시간 가져오기**: 현재 타임스탬프에서 지정한 시간 단위를 뺀 값을 반환합니다.
        \
        \
        **시간에서 빼기**: 타임스탬프에서 시간 단위 수를 뺍니다.
    :::column-end:::
    :::column:::
        [![Variables 작업 아이콘][variables-icon]][variables-doc]
        \
        \
        [**변수**][variables-doc]
        \
        \
        변수를 사용하여 작업을 수행합니다.
        \
        \
        **배열 변수에 추가**: 값을 변수로 저장되는 배열의 마지막 항목으로 삽입합니다.
        \
        \
        **문자열 변수에 추가**: 값을 변수로 저장되는 문자열의 마지막 문자로 삽입합니다.
        \
        \
        **변수 감소**: 변수를 상수 값만큼 줄입니다.
        \
        \
        **변수 증가**: 변수를 상수 값만큼 늘립니다.
        \
        \
        **변수 초기화**: 변수를 만들고 해당 데이터 형식과 초기 값을 선언합니다.
        \
        \
        **변수 설정**: 기존 변수에 다른 값을 할당합니다.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-built-in-actions"></a>통합 계정 기본 제공 작업

Azure Logic Apps는 다중 테넌트, 소비 계획 기반 Azure Logic Apps를 사용하는 경우, 통합 계정이 필요한 다음 기본 제공 작업을 제공하며, 단일 테넌트, 표준 계획 기반 Azure Logic Apps를 사용하는 경우에는 통합 계정이 필요하지 않은 기본 제공 작업을 제공합니다.

> [!NOTE]
> 다중 테넌트, 소비 계획 기반 Azure Logic Apps에서 통합 계정 작업을 사용하려면 먼저 [논리 앱 리소스를 통합 계정에 연결](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)해야 합니다. 그러나 단일 테넌트 표준 계획 기반 Azure Logic Apps에서 일부 통합 계정 작업에는 논리 앱 리소스를 통합 계정에 연결할 필요가 없습니다(예: Liquid 작업 및 XML 작업). 이러한 작업을 사용하려면 Liquid 맵, XML 맵 또는 XML 스키마가 필요하며, Azure Portal에서 각 작업을 통해 업로드하거나 각각 **Maps** 및 **Schemas** 폴더를 사용하여 Visual Studio Code 프로젝트의 **Artifacts** 폴더에 추가할 수 있습니다.

:::row:::
    :::column:::
        [![플랫 파일 디코딩 아이콘][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**플랫 파일 디코딩**<br>(*다중 테넌트 전용*)][flat-file-decode-doc]
        \
        \
        콘텐츠를 거래 업체에게 보내기 전에 XML을 인코딩합니다.
    :::column-end:::
    :::column:::
        [![플랫 파일 인코딩 아이콘][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**플랫 파일 인코딩**<br>(*다중 테넌트 전용*)][flat-file-encode-doc]
        \
        \
        거래 업체에서 콘텐츠를 받은 후 XML을 디코딩합니다.
    :::column-end:::
    :::column:::
        [![통합 계정 아이콘][integration-account-icon]][integration-account-doc]
        \
        \
        [**통합 계정 아티팩트 조회**<br>(*다중 테넌트 전용*)][integration-account-doc]
        \
        \
        통합 계정에서 거래 업체, 규약, 스키마 등과 같은 아티팩트에 대한 사용자 지정 메타데이터를 가져옵니다.
    :::column-end:::
    :::column:::
        [![Liquid 작업 아이콘][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Liquid 작업**][json-liquid-transform-doc]
        \
        \
        Liquid 템플릿을 사용하여 다음 형식을 변환합니다. <p><p>- JSON에서 JSON으로 <br>JSON에서 텍스트로 <br>XML에서 JSON으로 <br>XML에서 텍스트로
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![변환 XML 아이콘][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**반환 XML**][xml-transform-doc]
        \
        \
        원본 XML 형식을 다른 XML 형식으로 변환합니다.
    :::column-end:::
    :::column:::
        [![XML 유효성 검사 아이콘][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**XML 유효성 검사**][xml-validate-doc]
        \
        \
        지정된 스키마에 대해 XML 문서의 유효성을 검사합니다.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Logic Apps에서 호출할 수 있는 사용자 지정 API 생성](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-in icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[sql-server-icon]: ./media/apis-list/sql.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Built-in integration account connector icons -->
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[xml-transform-icon]: ./media/apis-list/xml-transform.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API 관리 및 게시를 위한 Azure API Management 서비스 인스턴스 만들기"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "App Service API Apps과 논리 앱 통합"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "논리 앱을 Azure Functions와 통합"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "그룹 또는 일괄 처리로 메시지 처리"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "조건 평가 및 조건이 true인지 false인지에 따라 별도의 작업 실행"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "배열 필터링이나 CSV 및 HTML 테이블 생성과 같은 데이터 작업 수행"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "배열의 모든 항목에 대해 동일한 작업 수행"
[http-doc]: ./connectors-native-http.md "Logic Apps에서 HTTP 또는 HTTPS 엔드포인트 호출"
[http-request-doc]: ./connectors-native-reqres.md "Logic Apps에서 HTTP 요청 수신"
[http-response-doc]: ./connectors-native-reqres.md "Logic Apps에서 HTTP 요청에 응답"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Logic Apps에서 REST 엔드포인트 호출"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP 또는 HTTPS 엔드포인트의 특정 이벤트 대기"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Logic Apps에서 JavaScript 코드 조각 추가 및 실행"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "중첩된 워크플로와 논리 앱 통합"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "쿼리 작업을 사용하여 배열 선택 및 필터링"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "일정에 따라 Logic Apps 실행"
[schedule-delay-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-delay-until-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "되풀이 일정에 따라 Logic Apps 실행"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "연속 청크로 데이터를 처리해야 하는 Logic Apps 실행"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "그룹에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 그룹으로 작업을 구성"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database 또는 SQL Server에 연결. SQL 데이터베이스 테이블에서 항목 생성, 업데이트, 가져오기 및 삭제"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "고유 값이 할당된 사례로 작업 구성. 값이 식, 개체, 토큰의 결과와 일치하는 사례만 실행. 일치하는 항목이 없으면 기본 사례 실행"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "논리 앱에 대해 활발히 실행되는 워크플로 중지 또는 취소"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업 반복"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "변수를 사용하여 작업 수행(예: 초기화, 설정, 증가, 감소 및 문자열 또는 배열 변수에 추가)"

<!--Built-in integration account doc links-->
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 아티팩트에 대한 메타데이터 관리"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid 템플릿으로 JSON 변환"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사"
