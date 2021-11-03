---
title: Azure Portal을 사용하여 WebSocket API 가져오기 | Microsoft Docs
titleSuffix: ''
description: API Management WebSocket을 지원하는 방법, WebSocket API 추가 및 WebSocket 제한에 대해 알아봅니다.
ms.service: api-management
author: dlepow
ms.author: danlep
ms.topic: how-to
ms.date: 11/2/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 027a87a7502f551b7fb97d52a732a90bc0b8aa45
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065687"
---
# <a name="import-a-websocket-api"></a>WebSocket API 가져오기

API Management의 WebSocket API 솔루션을 사용하면, API Management를 사용하여 WebSocket 및 REST API를 모두 관리, 보호, 관찰, 공개하고, 모든 API를 검색하고 사용할 수 있는 중앙 허브를 제공할 수 있습니다. API 게시자는 다음을 통해 API Management에 WebSocket API를 신속하게 추가할 수 있습니다.
* Azure Portal에 간단한 제스처 
* Management API 및 Azure Resource Manager. 

[JWT 유효성 검사](./api-management-access-restriction-policies.md#ValidateJWT)와 같은 기존 액세스 제어 정책을 적용하여 WebSocket API를 보호할 수 있습니다. Azure Portal 및 개발자 포털 모두에서 API 테스트 콘솔을 사용하여 WebSocket API를 테스트할 수도 있습니다. 기존 가시성 기능을 기반으로 하는 API Management는 WebSocket API 모니터링과 문제 해결을 위한 메트릭 및 로그를 제공합니다. 

이 문서에서는 다음을 수행합니다.
> [!div class="checklist"]
> * Websocket 통과 흐름을 이해합니다.
> * API Management 인스턴스에 WebSocket API를 추가합니다.
> * WebSocket API를 테스트합니다.
> * WebSocket API에 대한 메트릭 및 로그를 봅니다.
> * WebSocket API의 제한에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 기존 API Management 인스턴스. [없다면 하나 생성합니다](get-started-create-service-instance.md).
- WebSocket API. 

## <a name="websocket-passthrough"></a>WebSocket 통과

API Management는 WebSocket 통과를 지원합니다. 

:::image type="content" source="./media/websocket-api/websocket-api-passthrough.png" alt-text="WebSocket 통과 흐름의 시각 자료":::

WebSocket 통과 중에 클라이언트 애플리케이션은 API Management 게이트웨이와 WebSocket 연결을 설정합니다. 그러면 해당 백 엔드 서비스와의 연결이 설정됩니다. 그런 다음 API Management는 WebSocket 클라이언트-서버 메시지를 프록시합니다.

1. 클라이언트 애플리케이션은 OnHandshake 작업을 호출하여 APIM 게이트웨이에 WebSocket 핸드셰이크 요청을 보냅니다.
1. APIM 게이트웨이는 해당 백 엔드 서비스에 WebSocket 핸드셰이크 요청을 보냅니다.
1. 백 엔드 서비스는 WebSocket에 대한 연결을 업그레이드합니다.
1. APIM 게이트웨이는 해당하는 연결을 WebSocket으로 업그레이드합니다.
1. 연결 쌍이 설정되면, APIM은 클라이언트 애플리케이션과 백 엔드 서비스 간에 메시지를 중개합니다.
1. 클라이언트 애플리케이션이 APIM 게이트웨이로 메시지를 보냅니다.
1. APIM 게이트웨이가 메시지를 백 엔드 서비스로 전달합니다.
1. 백 엔드 서비스에서 APIM 게이트웨이로 메시지를 보냅니다.
1. APIM 게이트웨이가 메시지를 클라이언트 애플리케이션으로 전달합니다.
1. 양쪽 연결이 끊어지면 APIM은 해당 연결을 종료합니다.

> [!NOTE]
> 클라이언트 쪽 및 백 엔드 쪽 연결은 일 대 일 매핑으로 구성됩니다. 

## <a name="onhandshake-operation"></a>onHandshake 작업

[Websocket 프로토콜](https://tools.ietf.org/html/rfc6455)마다, 클라이언트 애플리케이션은 백 엔드 서비스와 WebSocket 연결을 설정하려고 할 때 먼저 [핸드셰이크 요청 열기](https://tools.ietf.org/html/rfc6455#page-6)를 보냅니다. API Management의 각 WebSocket API에는 onHandshake 작업이 있습니다. onHandshake는 변경할 수 없고 제거할 수 없는, 자동으로 생성된 시스템 작업입니다. onHandshake 작업을 통해 API 게시자는 이러한 핸드셰이크 요청을 가로채 API Management 정책을 적용할 수 있습니다.

:::image type="content" source="./media/websocket-api/onhandshake-screen.png" alt-text="onHandshake 화면 예시":::

## <a name="add-a-websocket-api"></a>WebSocket API 추가

1. API Management 인스턴스로 이동합니다.
1. 측면 탐색 메뉴의 **API** 섹션 아래에서 **API** 를 선택합니다.
1. **새 API 정의** 에서 **WebSocket** 아이콘을 선택합니다.
1. 대화 상자에서 **전체** 를 선택하고 필요한 양식 필드를 작성합니다.

    | 필드 | 설명 |
    |----------------|-------|
    | 표시 이름 | WebSocket API가 표시되는 이름입니다. |
    | 이름 | WebSocket API의 원시 이름입니다. 표시 이름을 입력하면 자동으로 채워집니다. |
    | WebSocket URL | websocket 이름을 가진 기본 URL입니다. 예: *ws://example.com/your-socket-name* |
    | URL 구성표 | 기본값 적용 |
    | API URL 접미사| URL 접미사를 추가하여 이 API Management 인스턴스에서 이 API를 식별합니다. 이 APIM 인스턴스 내에서 고유해야 합니다. |
    | 제품 | WebSocket API를 제품과 연결하여 게시합니다. |
    | 게이트웨이 | WebSocket API를 기존 게이트웨이와 연결합니다. |
 
1. **만들기** 를 클릭합니다.

## <a name="test-your-websocket-api"></a>WebSocket API 테스트

1. WebSocket API로 이동합니다.
1. WebSocket API 내에서 onHandshake 작업을 선택합니다.
1. **테스트** 탭을 선택하여 테스트 콘솔에 액세스합니다. 
1. 필요에 따라 WebSocket 핸드셰이크에 필요한 쿼리 문자열 매개 변수를 제공합니다.

    :::image type="content" source="./media/websocket-api/test-websocket-api.png" alt-text="테스트 API 예제":::

1. **연결** 을 클릭합니다.
1. **출력** 에서 연결 상태를 봅니다.
1. **페이로드** 에 값을 입력합니다. 
1. **보내기** 를 클릭합니다.
1. **출력** 에서 수신된 메시지를 봅니다.
1. 이전 단계를 반복하여 다른 페이로드를 테스트합니다.
1. 테스트가 완료되면 **연결 끊기** 를 선택합니다.

## <a name="view-metrics-and-logs"></a>메트릭 및 로그 보기

표준 API Management 및 Azure Monitor 기능을 사용하여 WebSocket API를 [모니터링](api-management-howto-use-azure-monitor.md)합니다.

* Azure Monitor의 API 메트릭 보기
* 선택 사항으로, 진단 설정을 사용하도록 설정하여 WebSocket API 작업을 포함하는 API Management 게이트웨이 로그를 수집 및 확인

예를 들어 다음 스크린샷에서는 **ApiManagementGatewayLogs** 테이블의 코드 `101`을 사용하는 최신 WebSocket API 리소스를 확인할 수 있습니다. 이러한 결과는 요청이 TCP에서 WebSocket 프로토콜로 성공적으로 전환되었음을 나타냅니다.

:::image type="content" source="./media/websocket-api/query-gateway-logs.png" alt-text="WebSocket API 요청에 대한 쿼리 로그":::

## <a name="limitations"></a>제한 사항

다음은 API Management의 WebSocket 지원의 현재 제한 사항입니다.

* WebSocket API는 소비 계층에서 아직 지원되지 않습니다.
* WebSocket API는 [자체 호스팅 게이트웨이](./self-hosted-gateway-overview.md)에서 아직 지원되지 않습니다.
* Azure CLI, PowerShell 및 SDK는 현재 WebSocket API의 관리 작업을 지원하지 않습니다.

### <a name="unsupported-policies"></a>지원되지 않는 정책

다음 정책은 지원되지 않으며 onHandshake 작업에 적용할 수 없습니다.
*  모의 응답
* 캐시에서 가져오기
* 캐시에 저장
* 도메인 간 호출 허용
* CORS
* JSONP
*  요청 메서드 설정
* 본문 설정
* XML에서 JSON으로 변환
* JSON을 XML로 변환
* XSLT를 사용하여 XML 변환
* 콘텐츠 유효성 검사
* 매개 변수 유효성 검사
* 헤더 유효성 검사
* 상태 코드 유효성 검사

> [!NOTE]
> 더 높은 범위(즉, 전체 또는 제품)에서 정책을 적용하고 정책을 통해 WebSocket API에 상속된 경우, 런타임에서 건너뜁니다.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
