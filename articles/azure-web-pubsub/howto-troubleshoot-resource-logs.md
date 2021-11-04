---
title: Azure Web PubSub 서비스 리소스 로그를 사용하여 문제를 해결하는 방법
description: 리소스 로그를 사용하여 문제를 해결하는 방법 알아보기
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/22/2021
ms.openlocfilehash: 7e8d4c725c8e205f015774b8b5b01e26b5f89271
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482887"
---
# <a name="how-to-troubleshoot-with-resource-logs"></a>리소스 로그 문제 해결 방법

이 방법 가이드에서는 리소스 로그를 얻는 옵션과 문제 해결 방법을 보여줍니다.

## <a name="whats-the-resource-logs"></a>리소스 로그란?

리소스 로그는 Azure Web PubSub 서비스 인스턴스에 대한 연결, 메시징 및 HTTP 요청 정보를 보다 다양하게 볼 수 있습니다. 문제 식별, 연결 추적, 메시지 추적, HTTP 요청 추적 및 분석에 사용할 수 있습니다.

연결 로그, 메시징 로그 및 HTTP 요청 로그의 세 가지 로그 유형이 있습니다.

### <a name="connectivity-logs"></a>연결 로그

연결 로그는 Azure Web PubSub 허브 연결에 대한 자세한 정보를 제공합니다. 예를 들면 기본 정보(사용자 ID, 연결 ID 등)와 이벤트 정보(연결, 연결 끊기 및 중단 이벤트 등)가 있습니다. 연결 로그가 연결 관련 문제를 해결하는 데 도움이 되는 이유입니다.

### <a name="messaging-logs"></a>메시징 로그

메시징 로그는 Azure Web PubSub 서비스를 통해 수신 및 전송되는 Azure Web PubSub 허브 메시지에 대한 추적 정보를 제공합니다. 예를 들어 메시지의 추적 ID와 메시지 유형이 있습니다. 일반적으로 메시지는 서비스에 도착하거나 서비스에서 나갈 때 기록됩니다. 따라서 메시징 로그는 메시지 관련 문제를 해결하는 데 유용합니다.

### <a name="http-request-logs"></a>HTTP 요청 로그

Http 요청 로그는 Azure Web PubSub 서비스에 대한 HTTP 요청에 대한 추적 정보를 제공합니다. 예를 들어 HTTP 메서드 및 상태 코드입니다. 일반적으로 HTTP 요청은 서비스에 도착하거나 서비스에서 나갈 때 기록됩니다. 따라서 HTTP 요청 로그는 요청 관련 문제를 해결하는 데 유용합니다.

## <a name="capture-resource-logs-with-live-trace-tool"></a>라이브 추적 도구를 사용하여 리소스 로그 캡처 

Azure Web PubSub 서비스 라이브 추적 도구는 리소스 로그를 실시간으로 수집할 수 있으며 고객의 개발 환경을 추적하는 데 유용합니다. 라이브 추적 도구는 연결 로그, 메시징 로그 및 HTTP 요청 로그를 캡처할 수 있습니다.

> [!NOTE]
> 라이브 추적 도구에서 캡처한 실시간 리소스 로그는 메시지(아웃바운드 트래픽)로 청구됩니다.

> [!NOTE]
> 무료 계층으로 생성된 Azure Web PubSub 서비스 인스턴스에는 일일 메시지(아웃바운드 트래픽) 제한이 있습니다.

### <a name="launch-the-live-trace-tool"></a>라이브 추적 도구 시작

1. Azure Portal로 이동합니다. 
2. Azure Web PubSub 서비스 인스턴스의 **라이브 추적 설정** 페이지에서 라이브 **추적** 사용(사용 안 함)을 선택합니다.
3. 필요한 로그 범주를 확인합니다.
4. **저장** 단추를 클릭하고 설정이 적용될 때까지 기다립니다.
5. *라이브 추적 도구 열기를* 클릭합니다.

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="라이브 추적 도구 실행 스크린샷.":::

### <a name="capture-the-resource-logs"></a>리소스 로그 캡처

라이브 추적 도구는 문제 해결을 위해 리소스 로그를 캡처하는 데 도움이 되는 몇 가지 기본 기능을 제공합니다.

* **캡처:** 라이브 추적 도구를 사용하여 Azure Web PubSub 인스턴스에서 실시간 리소스 로그 캡처를 시작합니다.
* **지우기:** 캡처된 실시간 리소스 로그를 지웁니다.
* **로그 필터:** 라이브 추적 도구를 사용하면 캡처된 실시간 리소스 로그를 하나의 특정 키워드로 필터링할 수 있습니다. 일반적인 구분 기호(예: 공백, 쉼표, 세미콜론 등)는 키 단어의 일부로 처리됩니다. 
* **상태**: 상태는 라이브 추적 도구가 특정 인스턴스와 연결되어 있는지 또는 연결이 끊겼는지를 보여 줍니다.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="라이브 추적 도구를 사용하여 리소스 로그를 캡처하는 스크린샷":::

라이브 추적 도구에서 캡처한 실시간 리소스 로그에는 문제 해결을 위한 자세한 정보가 포함되어 있습니다. 

| Name | Description |
| ------------ |  ------------------------ | 
| Time | 로그 이벤트 시간 |
| 로그 수준 | 로그 이벤트 수준(추적/디버그/정보/경고/오류) |
| 이벤트 이름 | 이벤트의 작업 이름 |
| 메시지 | 로그 이벤트의 세부 메시지 |
| 예외 | Azure Web PubSub 서비스의 런타임 예외 |
| 허브 | 사용자 정의 허브 이름 |
| 연결 ID | 연결의 ID |
| 사용자 ID | 사용자의 ID |
| IP | 클라이언트의 IP 주소 |
| 경로 템플릿 | API의 경로 템플릿 |
| Http 메서드 | Http 메서드(POST/GET/PUT/DELETE) |
| URL | 균일한 리소스 로케이터 |
| 추적 ID | 호출에 대한 고유 식별자입니다. |
| 상태 코드 | Http 응답 코드 |
| Duration | 요청이 수신되고 처리되는 기간 |
| 헤더 | HTTP 요청 또는 응답을 사용하여 클라이언트와 서버에서 전달한 추가 정보 |

Azure Web PubSub 서비스가 일반 공급된 후로, 라이브 추적 도구는 로그를 특정 형식으로 내보낸 다음, 문제 해결을 위해 다른 사람과 공유할 수 있는 기능도 지원합니다. 

## <a name="capture-resource-logs-with-azure-monitor"></a>Azure Monitor 리소스 로그 캡처

### <a name="how-to-enable-resource-logs"></a>리소스 로그를 사용하도록 설정하는 방법

현재 Azure Web PubSub는 [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)와 통합을 지원합니다.

1. Azure 포털로 이동합니다.
2. Azure Web PubSub 서비스 인스턴스의 **진단 설정** 페이지에서 + 진단 **설정 추가** 링크를 클릭합니다.
3. **진단 설정 이름** 에서 설정 이름을 입력합니다.
4. **범주 세부 정보** 에서 필요한 로그 범주를 선택합니다.
5. **대상 세부 정보에서** **스토리지 계정에 보관을** 선택합니다.
6. **저장 단추를** 클릭하여 진단 설정을 저장합니다.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-list.png" alt-text="진단 설정을 보고 새 설정을 만드는 스크린샷":::

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-details.png" alt-text="진단 설정 세부 정보 구성 스크린샷":::

> [!NOTE]
> 스토리지 계정은 Azure Web PubSub 서비스와 동일한 지역이어야 합니다.

### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관

로그는 **진단 설정** 창에 구성된 스토리지 계정에 저장됩니다. `insights-logs-<CATEGORY_NAME>`이라는 컨테이너는 리소스 로그를 저장하기 위해 자동으로 만들어집니다. 컨테이너 내에서 로그는 `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` 파일에 저장됩니다. 기본적으로 경로는 `resource ID` 및 `Date Time`으로 결합됩니다. 로그 파일은 `hour`로 분할됩니다. 따라서 분은 항상 `m=00`입니다.

모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

보관 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

**Format**

이름 | Description
------- | -------
time | 로그 이벤트 시간
수준 | 로그 이벤트 수준
resourceId | Azure SignalR Service의 리소스 ID
위치 | Azure SignalR Service의 위치
category | 로그 이벤트 범주
operationName | 이벤트의 작업 이름
callerIpAddress | 서버/클라이언트의 IP 주소
properties | 해당 로그 이벤트와 관련된 자세한 속성입니다. 더 자세히 알아보려면 아래 속성 표를 참조하세요.

**속성 표**

이름 | Description
------- | -------
collection | 로그 이벤트의 컬렉션입니다. 허용되는 값은 `Connection`, `Authorization` 및 `Throttling`입니다.
connectionId | 연결 ID
userId | 사용자 ID
message | 로그 이벤트의 세부 메시지
hub | 사용자 정의 허브 이름 |
routeTemplate | API의 경로 템플릿 |
httpMethod | Http 메서드 (POST/GET/PUT/DELETE) |
url | Uniform resource locator |
traceId | 호출에 대 한 고유 식별자입니다. |
statusCode | Http 응답 코드 |
duration | 요청 간의 기간이 수신 및 처리 됩니다. |
headers | HTTP 요청 또는 응답을 사용 하 여 클라이언트와 서버에서 전달 하는 추가 정보 |

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
  "properties": {
    "message": "Connection started",
    "collection": "Connection",
    "connectionId": "LW61bMG2VQLIMYIVBMmyXgb3c418200",
    "userId": null
  },
  "operationName": "ConnectionStarted",
  "category": "ConnectivityLogs",
  "level": "Informational",
  "callerIpAddress": "167.220.255.79",
  "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYGROUP/PROVIDERS/MICROSOFT.SIGNALRSERVICE/WEBPUBSUB/MYWEBPUBSUB",
  "time": "2021-09-17T05:25:05Z",
  "location": "westus"
}
```

## <a name="troubleshoot-with-the-resource-logs"></a>리소스 로그 문제 해결

예기치 않게 증가 하거나 삭제 한 연결을 찾을 때 리소스 로그를 활용 하 여 문제를 해결할 수 있습니다. 일반적인 문제는 연결의 예기치 않은 수량 변경, 연결의 연결 제한 도달 및 권한 부여 오류에 대한 경우가 많습니다.

### <a name="unexpected-connection-number-changes"></a>예기치 않은 연결 수 변경

#### <a name="unexpected-connection-dropping"></a>예기치 않은 연결 끊김

연결의 연결이 끊어지면 리소스 로그에서 또는를 사용 하 여이 연결 끊기 이벤트를 기록 합니다 `ConnectionAborted` `ConnectionEnded` `operationName` .

`ConnectionAborted`와 `ConnectionEnded`의 차이점은 `ConnectionEnded`가 클라이언트 또는 서버 쪽에서 트리거되는 예상한 연결 끊김이라는 것입니다. `ConnectionAborted`는 일반적으로 예기치 않은 연결 끊김 이벤트이며, `message`에 중단 이유가 제공됩니다.

중단 이유는 다음 표에 나열되어 있습니다.

| 이유 | Description |
| ------- | ------- |
| 연결 수가 제한에 도달 | 연결 수가 현재 가격 계층의 제한에 도달했습니다. 서비스 단위를 스케일 업하는 것이 좋습니다.
| 서비스 다시 로드, 다시 연결 | Azure Web PubSub 서비스를 다시 로드하는 중입니다. 고유한 다시 연결 메커니즘을 구현하거나 Azure Web PubSub 서비스에 수동으로 다시 연결해야 합니다. |
| 내부 서버의 일시적인 오류 | Azure Web PubSub 서비스에서 일시적인 오류가 발생하며, 자동으로 복구되어야 함

#### <a name="unexpected-connection-growing"></a>예기치 않은 연결 증가

예기치 않은 연결 증가 문제를 해결하려면 먼저 추가 연결을 필터링해야 합니다. 테스트 클라이언트 연결에 고유한 테스트 사용자 ID를 추가할 수 있습니다. 그런 다음 리소스 로그를 사용하여 이를 확인합니다. 두 개 이상의 클라이언트 연결이 동일한 테스트 사용자 ID 또는 IP를 사용하는 경우 클라이언트 쪽에서 예상보다 많은 연결을 만들고 설정할 가능성이 높습니다. 클라이언트 쪽을 확인합니다.

### <a name="authorization-failure"></a>권한 부여 실패

클라이언트 요청에 대해 인증되지 않은 401이 반환되는 경우 리소스 로그를 확인합니다. `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`가 발견될 경우, 액세스 토큰의 모든 대상 그룹이 잘못된 것입니다. 로그에 제안된 유효한 대상 그룹을 사용하세요.

### <a name="throttling"></a>제한

Azure 웹 PubSub 서비스에 대 한 클라이언트 연결을 설정할 수 없는 경우 리소스 로그를 확인 합니다. `Connection count reaches limit`리소스 로그에서 충족 하는 경우 연결 수 제한에 도달 하는 Azure 웹 PubSub 서비스에 너무 많은 연결을 설정할 수 있습니다. Azure Web PubSub 서비스 인스턴스를 스케일 업하는 것이 좋습니다. 리소스 로그에서 충족 하는 경우 `Message count reaches limit` 무료 계층을 사용 하 고 메시지 할당량을 사용 합니다. 더 많은 메시지를 보내려면 Azure Web PubSub 서비스 인스턴스를 표준 계층으로 변경하여 추가 메시지를 전송하는 것이 좋습니다. 자세한 내용은 [Azure Web PubSub 서비스 가격 책정](https://azure.microsoft.com/pricing/details/web-pubsub/)을 참조하세요.
