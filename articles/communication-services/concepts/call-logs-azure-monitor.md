---
title: Azure Communication Services - 통화 요약 및 통화 진단 로그
titleSuffix: An Azure Communication Services concept document
description: Azure Monitor에서 통화 요약 및 통화 진단 로그에 대해 알아보기
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 3844fcab3f9830bbaca60042aedbfb75bf7f09e4
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676147"
---
# <a name="call-summary-and-call-diagnostic-logs"></a>통화 요약 및 통화 진단 로그

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

## <a name="data-concepts"></a>데이터 개념

### <a name="entities-and-ids"></a>엔터티 및 ID

데이터에 표시되는 엔터티와 관련해서 ‘통화’는 `correlationId`로 표현되는 추상화입니다. `CorrelationId`는 통화마다 고유하며 `callStartTime` 및 `callDuration`으로 시간 범위가 지정됩니다. 모든 통화는 통화의 다양한 사람, 봇 또는 서버 참가자를 나타내는 둘 이상의 ‘엔드포인트’에 있는 데이터를 포함하는 이벤트입니다.

‘참가자’(`participantId`)는 엔드포인트와 서버 간의 연결을 나타내므로 통화가 ‘그룹’ 통화일 때만 있습니다. 

‘엔드포인트’는 `endpointId`로 나타내는 가장 고유한 엔터티입니다. `EndpointType`은 엔드포인트가 인간 사용자(PSTN, VoIP), 봇(봇) 또는 통화 내의 여러 참가자를 관리하는 서버 중 어떤 것을 나타내는지를 알려 줍니다. `endpointType`이 `"Server"`이면 엔드포인트에는 고유 ID가 할당되지 않습니다. `endpointType` 및 `endpointId`의 수를 확인하면 항상 통화의 사용자 수 및 기타 비사용자 참가자(봇, 서버)의 수를 확인할 수 있습니다. 네이티브 SDK(예: Android 통화 SDK)는 여러 통화에서 사용자에 대해 동일한 `endpointId`를 다시 사용하므로 세션 간 환경을 이해할 수 있습니다. 이것은 항상 각각의 새 통화에 대해 새로운 `endpointId`를 생성하는 웹 기반 엔드포인트와는 다릅니다.

‘Stream’은 방향(인바운드/아웃바운드) 및 `mediaType`(예: 오디오, 비디오) 기준으로 Stream이 하나만 있으므로 가장 세분화된 엔터티입니다.  

### <a name="p2p-vs-group-calls"></a>P2P와 그룹 통화 비교

`callType`으로 나타내는 통화 유형에는 P2P와 그룹의 두 가지가 있습니다. 

**P2P** 통화는 서버 엔드포인트가 없는 두 엔드포인트 간에만 이루어지는 연결입니다. P2P 통화는 이러한 엔드포인트 간의 통화로 시작되며 연결 전에 그룹 통화 이벤트로 생성되지 않습니다.

:::image type="content" source="media\call-logs-images\call-logs-concepts-p2p.png" alt-text="p2p 통화":::

**그룹** 통화에는 모임/일정 이벤트로 미리 생성된 모든 통화와 엔드포인트가 2개 이상 연결된 통화가 포함됩니다. 그룹 통화는 서버 엔드포인트를 포함하며 각 엔드포인트와 서버 간의 연결은 참가자를 구성합니다. 통화 동안 추가 엔드포인트를 추가하는 P2P 통화는 더 이상 P2P가 아니며 그룹 통화가 됩니다. `participantStartTime` 및 `participantDuration`을 보면 각 엔드포인트가 통화에 참가한 타임라인을 확인할 수 있습니다.

:::image type="content" source="media\call-logs-images\call-logs-concepts-group.png" alt-text="그룹 통화":::

## <a name="log-structure"></a>로그 구조
**통화 요약** 로그와 **통화 진단** 로그의 두 가지 로그 유형이 생성됩니다. 

통화 요약 로그에는 관련된 모든 ID, 타임스탬프, 엔드포인트 및 SDK 정보를 비롯하여 통화에 대한 기본 정보가 포함됩니다. 서버를 제외하고 통화 내의 각 엔드포인트에 대해 고유한 통화 요약 로그가 생성됩니다.

통화 진단 로그에는 환경 측정의 품질을 나타내는 메트릭 집합 뿐만 아니라 스트림에 대한 정보가 포함됩니다. 통화 내의 각 엔드포인트(서버 포함)에 대해서는 엔드포인트 간의 각 데이터 스트림(오디오, 비디오 등)에 대해 고유한 통화 진단 로그가 생성됩니다. P2P 통화에서 각 로그에는 각 엔드포인트와 연결된 각 아웃바운드 스트림과 관련된 데이터가 포함됩니다. 그룹 통화에서 `endpointType`= `"Server"`에 연결된 각 스트림은 인바운드 스트림에 대한 데이터를 포함하는 로그를 만들며 다른 모든 스트림은 모든 비서버 엔드포인트에 대한 아웃바운드 스트림 데이터를 포함하는 로그를 만듭니다. 그룹 통화에서 `participantId`를 키로 사용하여 관련된 인바운드/아웃바운드 로그를 고유한 참가자 연결에 조인합니다.

### <a name="example-1-p2p-call"></a>예제 1: P2P 통화

아래 다이어그램은 P2P 통화에서 직접 연결된 두 개의 엔드포인트를 나타냅니다. 이 예제에서는 2개의 통화 요약 로그가 만들어지고(`endpointId`당 1개) 4개의 통화 진단 로그가 만들어집니다(미디어 스트림당 1개). 각 로그에는 `endpointId`의 아웃바운드 스트림과 관련된 데이터가 포함됩니다.

:::image type="content" source="media\call-logs-images\call-logs-p2p-streams.png" alt-text="p2p 통화 스트림":::


### <a name="example-2-group-call"></a>예제 2: 그룹 통화

아래 다이어그램은 3개의 `particpantIds`를 포함하는 그룹 통화 예제를 나타냅니다. 이것은 3개의 `endpointIds`(`endpointIds`는 동일한 디바이스에서 통화에 다시 참가하는 경우 등에 여러 참가자에게 표시될 수 있음) 및 서버 엔드포인트를 의미합니다. `participantId` 1에 대해 두 개의 통화 요약 로그가 생성됩니다. 하나는 `endpointId`에 대한 것이고 다른 하나는 서버에 대한 것입니다. 각 미디어 스트림에 대해 하나씩, `participantId` 1과 관련해서 4개의 통화 진단 로그가 생성됩니다. `endpointId` 1이 있는 3개의 로그에는 아웃바운드 미디어 스트림과 관련된 데이터가 포함되며, `endpointId = null, endpointType = "Server"`인 하나의 로그에는 인바운드 스트림과 관련된 데이터가 포함됩니다.

:::image type="content" source="media\call-logs-images\call-logs-concepts-group-call-endpoint-detail.png" alt-text="그룹 통화 정보":::

## <a name="data-definitions"></a>데이터 정의

### <a name="call-summary-log"></a>통화 요약 로그
통화 요약 로그에는 모든 통화의 키 속성을 식별하는 데 도움이 되는 데이터가 포함되어 있습니다. 통화의 각 `participantId`(P2P 호출의 경우 `endpointId`)에 대해 다른 통화 요약 로그가 생성됩니다.

|     속성                  |     설명                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     time                      |     로그가 생성된 시간의 타임스탬프(UTC)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                       |
|     operationName             |     로그 레코드와 연결된 작업입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     operationVersion          |     `operationName`이 API를 사용하여 수행된 경우 작업과 연결된 api-version입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다.                                                                                                                                                                           |
|     category                  |     이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 `properties` Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다.                                                                                                                                                                                                    |
|     correlationIdentifier     |     `correlationIdentifier`는 단일 통화 중에 연결되는 모든 참가자와 엔드포인트에서 상호 연결된 이벤트를 식별합니다. `correlationIdentifier`는 통화의 고유 ID입니다. Microsoft에서 지원 사례를 열어야 하는 경우 `correlationID`를 사용하여 문제를 해결하려는 통화를 쉽게 식별할 수 있습니다.                                                                                                                                                                      |
|     identifier                |     이 ID는 사용자의 고유 ID로, 인증 서비스(MRI)에서 할당한 ID와 일치합니다.                                                                                                                                                                                                                                                                                                                                                              |
|     callStartTime             |     엔드포인트에서 첫 번째 시도된 연결을 기준으로 하는 통화 시작에 대한 타임스탬프입니다.                                                                                                                                                                                                                                                                                                                                                                   |
|     callDuration              |     두 엔드포인트 간의 처음으로 시도된 연결과 마지막 연결의 끝을 기준으로 하는 통화 지속 기간(초)입니다.                                                                                                                                                                                                                                                                                                                         |
|     callType                  |     `"P2P"` 또는 `"Group"`을 포함합니다. `"P2P"` 통화는 서버가 아닌 두 엔드포인트 간의 직접 1:1 연결입니다. `"Group"` 통화는 엔드포인트가 3개 이상 있거나 연결 전에 `"Group"` 통화로 만들어진 통화입니다.                                                                                                                                                                                                                                 |
|     teamsThreadId             |     이 ID는 통화가 Microsoft Teams 회의로 구성된 경우에만 해당되며, Microsoft Teams – Azure Communication Services 상호 운용성 사용 사례를 나타냅니다. 이 ID는 작업 로그에 표시됩니다. 채팅 API를 통해 이 ID를 가져올 수도 있습니다.                                                                                                                                                                                              |
|     participantId             |     이 ID는 `"Participant"` 엔드포인트(`endpointType` = `"Server"`)와 서버 간의 양방향 연결을 나타내기 위해 생성됩니다. `callType` = `"P2P"`인 경우 두 엔드포인트 간에 직접 연결되고 `participantId`가 생성되지 않습니다.                                                                                                                                                                                                               |
|     participantStartTime      |     참가자의 첫 번째 연결 시도 시작의 타임스탬프입니다.                                                                                                                                                                                                                                                                                                                                                                                            |
|     participantDuration       |     `participantStartTime`부터 연결이 종료되는 타임스탬프까지의 각 참가자 연결 기간(초)입니다.                                                                                                                                                                                                                                                                                                                                             |
|     participantEndReason      |     각 `participantId`와 관련해서 SDK에서 내보낸 통화 SDK 오류 코드를 포함합니다. 아래에서 통화 SDK 오류 코드를 참조하세요.                                                                                                                                                                                                                                                                                                                          |
|     endpointId                |     엔드포인트 유형이 `endpointType`로 정의된 경우 통화에 연결된 각 엔드포인트를 나타내는 고유 ID입니다. 값이 `null`이면 연결된 엔터티가 Communication Services 서버(`endpointType`= `"Server"`)입니다. `EndpointId`는 경우에 따라 네이티브 클라이언트에 대한 여러 호출(`correlationIdentifier`)에서 동일한 사용자에 대해 지속될 수 있습니다. `endpointId`의 수에 따라 통화 요약 로그 수가 결정됩니다. 각 `endpointId`에 대해 고유한 요약 로그가 생성됩니다.    |
|     endpointType              |     이 값은 통화에 연결된 각 엔드포인트의 속성을 설명합니다. `"Server"`, `"VOIP"`, `"PSTN"`, `"BOT"` 또는 `"Unknown"`을 포함할 수 있습니다.                                                                                                                                                                                                                                                                                                                               |
|     sdkVersion                |     각 관련 엔드포인트에서 사용하는 Communication Services 통화 SDK 버전의 버전 문자열입니다. (예: `"1.1.00.20212500"`)                                                                                                                                                                                                                                                                                                                                                          |
|     osVersion                 |     각 엔드포인트 디바이스의 운영 체제 및 버전을 나타내는 문자열입니다.                                                                                                                                                                                                                                                                                                                                                                                       |

### <a name="call-diagnostic-log"></a>통화 진단 로그
통화 진단 로그는 각 참가자의 엔드포인트와 미디어 전송에 대한 중요한 정보와 품질 문제를 이해하는 데 도움이 되는 측정값을 제공합니다. 

|     속성              |     Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     operationName         |     로그 레코드와 연결된 작업입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     operationVersion      |     `operationName`이 API를 사용하여 수행된 경우 작업과 연결된 `api-version`입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다.                                                                                                                                                                                                                                                                                                                                                                  |
|     category              |     이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 `properties` Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다.                                                                                                                                                                                                                                                                                                                                                                                         |
|     correlationIdentifier     |     `correlationIdentifier`는 단일 통화 중에 연결되는 모든 참가자와 엔드포인트에서 상호 연결된 이벤트를 식별합니다. `correlationIdentifier`는 통화의 고유 ID입니다. Microsoft에서 지원 사례를 열어야 하는 경우 `correlationID`를 사용하여 문제를 해결하려는 통화를 쉽게 식별할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                         |
|     participantId         |     이 ID는 "참가자" 엔드포인트(`endpointType` =  `“Server”`)와 서버 간의 양방향 연결을 나타내기 위해 생성됩니다. `callType`   = `"P2P"`인 경우 두 엔드포인트 간에 직접 연결되고 `participantId`가 생성되지 않습니다.                                                                                                                                                                                                                                                                                                                                                                                                |
|     identifier            |     이 ID는 인증 서비스에 정의된 사용자 ID를 나타냅니다. 이 ID를 사용하여 여러 통화 및 서비스에서 여러 이벤트 간 상관 관계를 지정합니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     endpointId            |     `endpointType`로 정의된 엔드포인트 유형을 사용하여 통화에 연결된 각 엔드포인트를 나타내는 고유 ID입니다.   값이 `null`이면 연결된 엔터티가 Communication Services 서버임을 의미합니다.  `EndpointId`는 네이티브 클라이언트에 대한 여러 통화(`correlationIdentifier`)에서 동일한 사용자에 대해 유지될 수 있지만 클라이언트가 웹 브라우저인 경우에는 모든 통화에 대해 고유합니다.                                                                                                                                                                                                                                                                                  |
|     endpointType          |     이 값은 각 `endpointId`의 속성을 설명합니다. `“Server”`, `“VOIP”`, `“PSTN”`, `“BOT”` 또는 `“Unknown”`을 포함할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     mediaType             |     이 문자열 값은 각 스트림 내의 엔드포인트 간에 전송되는 미디어의 유형을 설명합니다. 가능한 값에는 `“Audio”`, `“Video”`, `“VBSS”`(비디오 기반 화면 공유) 및 `“AppSharing”`이 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     streamId              |     `mediaType`과 함께 동일한 `participantId`의 스트림을 고유하게 식별하는 데 사용할 수 있는 고유하지 않은 정수입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     transportType         |     `participantId`를 기준으로 네트워크 전송 프로토콜을 설명하는 문자열 값입니다. `"UDP”`, `“TCP”` 또는 `“Unrecognized”`를 포함할 수 있습니다. `"Unrecognized"`는 `transportType`이 TCP인지 또는 UDP인지를 시스템에서 확인할 수 없음을 나타냅니다.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     roundTripTimeAvg      |     `participantDuration` 내에서 한 엔드포인트에서 다른 엔드포인트로 IP 패킷을 가져오는 데 걸리는 평균 시간입니다. 이 네트워크 전파 지연은 기본적으로 두 지점과 광원 속도 사이의 실제 거리(둘 간의 다양한 라우터로 인해 추가적으로 발생하는 오버헤드 포함)와 관련됩니다. 대기 시간은 단방향 또는 RTT(왕복 시간)로 측정됩니다.  해당 값은 밀리초 단위로 표시되며, RTT가 500ms보다 크면 통화 품질에 부정적인 영향을 주는 것으로 간주해야 합니다.                                                                                                                                                                      |
|     roundTripTimeMax      |     그룹 통화의 `participantDuration` 또는 P2P 통화의 `callDuration` 동안 미디어 스트림당 측정된 최대 RTT(ms)입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     jitterAvg             |     연속 패킷 간의 지연 변화에 대한 평균입니다. Azure Communication Services는 버퍼링을 통해 특정 수준의 지터에 맞게 조정할 수 있습니다. 이러한 조정은 지터가 버퍼링(약 `jitterAvg` >30ms)을 초과하는 경우에만 발생하며 품질에 부정적인 영향을 줄 수 있습니다. 서로 다른 속도로 도착하는 패킷은 스피커의 음성 소리를 로봇처럼 들리게 합니다. 이 값은 그룹 통화에서는 `participantDuration`, P2P 통화에서는 `callDuration` 동안 미디어 스트림별로 측정됩니다.                                                                                                                                              |
|     jitterMax             |     미디어 스트림별로 패킷 간에 측정된 최대 지터 값입니다. 네트워크 상태에서 버스트가 발생하면 오디오/비디오 트래픽 흐름에서 문제가 발생할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|     packetLossRateAvg     |     손실된 패킷의 평균 백분율입니다. 패킷 손실은 거의 영향을 주지 않는 작은 개별 손실 패킷에서 오디오를 완전히 차단하는 연속 버스트 손실에 이르기까지 오디오 품질에 직접적인 영향을 줍니다. 패킷이 삭제되어 의도한 대상에 도달하지 않은 경우 미디어에서 간격이 발생하므로 음절과 단어가 누락되고 비디오 및 공유가 고르지 못할 수 있습니다. 패킷 손실율이 10%(0.1)보다 큰 경우 부정적인 품질에 영향을 미칠 수 있는 비율로 간주되어야 합니다. 이 값은 그룹 통화에서는 `participantDuration`, P2P 통화에서는 `callDuration` 동안 미디어 스트림별로 측정됩니다.    |
|     packetLossRateMax     |     이 값은 그룹 통화에서는 `participantDuration`, P2P 통화에서는 `callDuration` 동안 미디어 스트림별 최대 패킷 손실률(%)을 나타냅니다. 네트워크 상태에서 버스트가 발생하면 오디오/비디오 트래픽 흐름에서 문제가 발생할 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                  |
|                           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### <a name="error-codes"></a>오류 코드
`participantEndReason`에는 통화 SDK 오류 코드 집합의 값이 포함됩니다. 이러한 코드를 참조하여 통화 중에 발생하는 문제를 엔드포인트별로 해결할 수 있습니다.

|     오류 코드                 |     Description                                                                                                       |     수행할 작업                                                                                                                                                                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     0                          |     성공                                                                                                           |     통화(P2P) 또는 참가자(그룹)가 올바르게 종료되었습니다.                                                                                                                                                                                                                    |
|     403                        |     금지 / 인증 오류.                                                                               |     Communication Services 토큰이 유효하고 만료되지 않았는지 확인합니다. Teams 상호 운용성을 사용하는 경우 Teams 테넌트가 미리 보기 액세스 허용 목록에 추가되었는지 확인합니다. Teams 테넌트 상호 운용성을 사용/사용하지 않도록 설정하려면 이 양식을 작성합니다.    |
|     404                        |     호출을 찾을 수 없습니다.                                                                                                   |     통화하는 번호(또는 조인하는 통화)가 있는지 확인합니다.                                                                                                                                                                                                   |
|     408                        |     통화 컨트롤러 시간이 초과되었습니다.                                                                                        |     사용자 엔드포인트에서 프로토콜 메시지를 기다리는 동안 통화 컨트롤러 시간이 초과되었습니다. 클라이언트가 연결되어 있고 사용 가능한지 확인합니다.                                                                                                                                                 |
|     410                        |     로컬 미디어 스택 또는 미디어 인프라 오류입니다.                                                                  |     지원되는 환경에서 최신 SDK를 사용하고 있는지 확인합니다.                                                                                                                                                                                                      |
|     430                        |     클라이언트 애플리케이션에 메시지를 전달할 수 없습니다.                                                                  |     클라이언트 애플리케이션이 실행 중이고 사용 가능한지 확인합니다.                                                                                                                                                                                                             |
|     480                        |     원격 클라이언트 엔드포인트가 등록되지 않았습니다.                                                                            |     원격 엔드포인트를 사용할 수 있는지 확인합니다.                                                                                                                                                                                                                              |
|     481                        |     들어오는 호출을 처리하지 못했습니다.                                                                                   |     Azure Portal을 통해 지원 요청을 제출합니다.                                                                                                                                                                                                                           |
|     487                        |     엔드포인트 불일치 문제로 인해 통화가 취소되거나, 로컬에서 거부되거나, 종료되었거나 미디어 제품을 생성하지 못했습니다.    |     예상 동작.                                                                                                                                                                                                                                                         |
|     490, 491, 496, 487, 498    |     로컬 엔드포인트 네트워크 문제                                                                                    |     네트워크를 확인합니다.                                                                                                                                                                                                                                                        |
|     500, 503, 504              |     Communication Services 인프라 오류입니다.                                                                      |     Azure Portal을 통해 지원 요청을 제출합니다.                                                                                                                                                                                                                           |
|     603                        |     원격 Communication Services 참가자가 전역적으로 호출을 거부했습니다.                                             |     예상 동작.                                                                                                                                                                                                                                                         |
|     Unknown                    |     비표준 종료 이유(표준 SIP 코드의 일부가 아님)                                                      |                                                                                                                                                                                                                                                                                |

## <a name="call-examples-and-sample-data"></a>통화 예제 및 샘플 데이터

### <a name="p2p-call"></a>P2P 통화

:::image type="content" source="media\call-logs-images\call-logs-concepts-p2p-sample.png" alt-text="P2P 샘플 로그 예제":::

통화의 모든 로그에 대한 공유 필드:

```
"time":                     "2021-07-19T18:46:50.188Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "8d1a8374-344d-4502-b54b-ba2d6daaf0ae",
```

#### <a name="call-summary-logs"></a>통화 요약 로그
통화 요약 로그에는 공유 작업 및 범주 정보가 있습니다.

```
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",

```
VoIP 사용자 1에 대한 통화 요약
```
"properties": {
    "identifier":               "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "callStartTime":            "2021-07-19T17:54:05.113Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",    
    "participantStartTime":     "2021-07-19T17:54:06.758Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.1.0",
    "osVersion":                "Windows 10.0.17763 Arch: x64"
}
```

VoIP 사용자 2에 대한 통화 요약
```
"properties": {
    "identifier":               "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "callStartTime":            "2021-07-19T17:54:05.335Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",
    "participantStartTime":     "2021-07-19T17:54:06.335Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.1.0.0",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>통화 진단 로그
통화 진단 로그는 다음과 같은 작업 정보를 공유합니다.
```
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
VoIP 엔드포인트 1에서 VoIP 엔드포인트 2로의 오디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "82",
    "roundTripTimeMax":     "88",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
VoIP 엔드포인트 2에서 VoIP 엔드포인트 1으로의 오디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "participantId":        "null",
    "endpointId":           "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1363841599",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "78",
    "roundTripTimeMax":     "84",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
VoIP 엔드포인트 1에서 VoIP 엔드포인트 2로의 비디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Video",
    "streamId":             "2804",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "103",
    "roundTripTimeMax":     "143",
    "jitterAvg":            "0",
    "jitterMax":            "4",
    "packetLossRateAvg":    "3.146336E-05",
    "packetLossRateMax":    "0.001769911"
}
```
### <a name="group-call"></a>그룹 통화
다음 예제에는 그룹 통화에 참가하는 세 명의 사용자, VOIP를 통해 연결된 두 명의 사용자, PSTN을 통해 연결된 1명의 사용자가 있습니다. 모두 오디오만 사용합니다. 

:::image type="content" source="media\call-logs-images\call-logs-concepts-group-sample.png" alt-text="그룹 통화 샘플 로그 예제":::

데이터는 세 개의 통화 요약 로그와 6개의 통화 진단 로그에 생성됩니다.

통화의 모든 로그에 대한 공유 필드:
```
"time":                     "2021-07-05T06:30:06.402Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "341acde7-8aa5-445b-a3da-2ddadca47d22",
```

#### <a name="call-summary-logs"></a>통화 요약 로그
통화 요약 로그에는 공유 작업 및 범주 정보가 있습니다.
```
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",
```

VoIP 엔드포인트 1에 대한 통화 요약:
```
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "participantStartTime":     "2021-07-05T06:16:44.235Z",
    "participantDuration":      "82",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Darwin Kernel Version 18.7.0: Mon Nov 9 15:07:15 PST 2020; root:xnu-4903.272.3~3/RELEASE_ARM64_S5L8960X"
}
```
VoIP 엔드포인트 3에 대한 통화 요약:
```
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "participantStartTime":     "2021-07-05T06:16:40.240Z",
    "participantDuration":      "87",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Android 11.0; Manufacturer: Google; Product: redfin; Model: Pixel 5; Hardware: redfin"
}
```
PSTN 엔드포인트 2에 대한 통화 요약:
```
"properties": {
    "identifier":               "null",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "515650f7-8204-4079-ac9d-d8f4bf07b04c",
    "participantStartTime":     "2021-07-05T06:17:10.447Z",
    "participantDuration":      "52",
    "participantEndReason":     "0",
    "endpointId":               "46387150-692a-47be-8c9d-1237efe6c48b",
    "endpointType":             "PSTN",
    "sdkVersion":               "null",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>통화 진단 로그
통화 진단 로그는 다음과 같은 작업 정보를 공유합니다.
```
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
VoIP 엔드포인트 1에서 서버 엔드포인트로의 오디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "14884",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "46",
    "roundTripTimeMax":     "48",
    "jitterAvg":            "0",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
서버 엔드포인트에서 VoIP 엔드포인트 1으로의 오디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           null,
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           null,
    "endpointType":         "Server",
    "mediaType":            "Audio",
    "streamId":             "2001",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "42",
    "roundTripTimeMax":     "44",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
VoIP 엔드포인트 3에서 서버 엔드포인트로의 오디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "13783",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "2",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
서버 엔드포인트에서 VoIP 엔드포인트 3으로의 오디오 스트림에 대한 진단 로그:
```
"properties": {
    "identifier":           "null",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           null,
    "endpointType":         "Server"    
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "4",
    "packetLossRateAvg":    "0",
```

## <a name="next-steps"></a>다음 단계

- [로깅 및 진단](./logging-and-diagnostics.md)에 대해 자세히 알아보세요.
