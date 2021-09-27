---
title: Communication Services 로그
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services의 로깅에 대해 알아보기
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 8c0b1f403508ddc8f704518b2caa784b9c8894de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671985"
---
# <a name="communication-services-logs"></a>Communication Services 로그

Azure Communication Services는 Communication Services 솔루션을 모니터링하고 디버깅하는 데 사용할 수 있는 로깅 기능을 제공합니다. 이러한 기능은 Azure Portal을 통해 구성할 수 있습니다.

## <a name="enable-diagnostic-logs-in-your-resource"></a>리소스에서 진단 로그 사용

리소스를 만들 때 로깅이 기본적으로 해제되어 있습니다. 로깅을 사용하려면 **모니터링** 섹션의 리소스 메뉴에서 **진단 설정** 블레이드로 이동합니다. 그리고 **진단 설정 추가** 를 클릭합니다.

그런 다음, 원하는 보관 대상을 선택합니다. 현재는 스토리지 계정 및 Log Analytics를 보관 대상으로 지원합니다. 캡처할 로그 유형을 선택한 후에는 진단 설정을 저장합니다.
 
새 설정은 약 10분 후에 적용됩니다. Communication Services 리소스의 [로그] 창 내부에 구성된 보관 대상에 로그가 표시되기 시작합니다.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="ACS 진단 설정 옵션":::

진단 구성에 대한 자세한 내용은 [Azure 리소스 로그](../../azure-monitor/essentials/platform-logs-overview.md) 개요를 참조하세요.

## <a name="resource-log-categories"></a>리소스 로그 범주

Communication Services는 사용할 수 있는 세 가지 유형의 로그를 제공합니다.

* **사용량 로그** - 요금이 청구된 각 서비스 제품과 연결된 사용량 데이터를 제공합니다.
* **채팅 작업 로그** - 채팅 서비스와 관련된 기본 정보를 제공합니다.
* **SMS 작업 로그** - SMS 서비스와 관련된 기본 정보를 제공합니다.
* **인증 작업 로그** - 인증 서비스와 관련된 기본 정보를 제공합니다.

### <a name="usage-logs-schema"></a>사용량 로그 스키마

| 속성 | Description |
| -------- | ---------------|
| 타임스탬프 | 로그가 생성된 시간의 타임스탬프(UTC)입니다. |
| 작업 이름 | 로그 레코드와 연결된 작업입니다. |
| 작업 버전 | operationName이 API를 사용하여 수행된 경우 작업과 연결된 `api-version`입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| Category | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. |
| 상관관계 ID | 상관 관계가 있는 이벤트의 ID입니다. 여러 테이블 간의 상관 관계가 있는 이벤트를 식별하는 데 사용할 수 있습니다. |
| 속성 | 다양한 Communication Services 모드에 적용 가능한 기타 데이터입니다. |
| 레코드 ID | 지정된 사용량 레코드의 고유 ID입니다. |
| 사용 유형 | 사용 모드입니다. (예: 채팅, PSTN, NAT 등) |
| 단위 형식 | 지정된 사용 모드의 사용량 기준이 되는 단위 유형입니다 (예: 분, 메가바이트, 메시지 등). |
| 수량 | 이 레코드에 사용된 단위 수입니다. |

### <a name="chat-operational-logs"></a>채팅 작업 로그

| 속성 | Description |
| -------- | ---------------|
| TimeGenerated | 로그가 생성된 시간의 타임스탬프(UTC)입니다. |
| OperationName | 로그 레코드와 연결된 작업입니다. |
| CorrelationID | 상관 관계가 있는 이벤트의 ID입니다. 여러 테이블 간의 상관 관계가 있는 이벤트를 식별하는 데 사용할 수 있습니다. |
| OperationVersion | operationName이 API를 사용하여 수행된 경우 작업과 연결된 api-version입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| Category | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. |
| ResultType | 작업의 상태입니다. |
| ResultSignature | 작업의 하위 상태입니다. 이 작업이 REST API 호출에 해당하는 경우 이 필드는 해당 REST 호출의 HTTP 상태 코드입니다. |
| ResultDescription | 이 작업에 대한 정적 텍스트 설명입니다. |
| DurationMs | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 작업이 공용 IP 주소를 사용하는 엔터티에서 시작되는 API 호출에 해당하는 경우 호출자 IP 주소입니다. |
| Level | 이벤트의 심각도 수준입니다. |
| URI | 요청의 URI입니다. |
| UserId | 요청을 보낸 사람의 사용자 ID입니다. |
| ChatThreadId | 요청과 연관된 채팅 스레드 ID입니다. |
| ChatMessageId | 요청과 연관된 채팅 메시지 ID입니다. |
| SdkType | 요청에 사용되는 Sdk 유형입니다. |
| PlatformType | 요청에 사용되는 플랫폼 유형입니다. |

### <a name="sms-operational-logs"></a>SMS 작업 로그

| 속성 | Description |
| -------- | ---------------|
| TimeGenerated | 로그가 생성된 시간의 타임스탬프(UTC)입니다. |
| OperationName | 로그 레코드와 연결된 작업입니다. |
| CorrelationID | 상관 관계가 있는 이벤트의 ID입니다. 여러 테이블 간의 상관 관계가 있는 이벤트를 식별하는 데 사용할 수 있습니다. |
| OperationVersion | operationName이 API를 사용하여 수행된 경우 작업과 연결된 api-version입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| Category | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. |
| ResultType | 작업의 상태입니다. |
| ResultSignature | 작업의 하위 상태입니다. 이 작업이 REST API 호출에 해당하는 경우 이 필드는 해당 REST 호출의 HTTP 상태 코드입니다. |
| ResultDescription | 이 작업에 대한 정적 텍스트 설명입니다. |
| DurationMs | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 작업이 공용 IP 주소를 사용하는 엔터티에서 시작되는 API 호출에 해당하는 경우 호출자 IP 주소입니다. |
| Level | 이벤트의 심각도 수준입니다. |
| URI | 요청의 URI입니다. |
| OutgoingMessageLength | 보내는 메시지의 문자 수입니다. |
| IncomingMessageLength | 들어오는 메시지의 문자 수입니다. |
| DeliveryAttempts | 이 메시지를 보내려고 시도한 횟수입니다. |
| PhoneNumber | SMS 메시지를 전송하는 전화 번호입니다. |
| SdkType | 요청에 사용되는 SDK 유형입니다. |
| PlatformType | 요청에 사용되는 플랫폼 유형입니다. |
| 메서드 | 요청에 사용되는 메서드입니다. |

### <a name="authentication-operational-logs"></a>인증 작업 로그

| 속성 | Description |
| -------- | ---------------|
| TimeGenerated | 로그가 생성된 시간의 타임스탬프(UTC)입니다. |
| OperationName | 로그 레코드와 연결된 작업입니다. |
| CorrelationID | 상관 관계가 있는 이벤트의 ID입니다. 여러 테이블 간의 상관 관계가 있는 이벤트를 식별하는 데 사용할 수 있습니다. |
| OperationVersion | `operationName`이 API를 사용하여 수행된 경우 작업과 연결된 `api-version`입니다. 이 작업에 해당하는 API가 없으면, 버전은 작업과 연결된 속성이 나중에 변경될 경우, 해당 작업의 버전을 나타냅니다. |
| Category | 이벤트의 로그 범주입니다. 범주는 특정 리소스에 대해 로그를 사용하거나 사용하지 않도록 설정할 수 있는 세분성입니다. 이벤트의 속성 Blob에 표시되는 속성은 특정 로그 범주 및 리소스 종류 내에서 동일합니다. |
| ResultType | 작업의 상태입니다. |
| ResultSignature | 작업의 하위 상태입니다. 이 작업이 REST API 호출에 해당하는 경우 이 필드는 해당 REST 호출의 HTTP 상태 코드입니다. |
| DurationMs | 밀리초 단위의 작업 기간입니다. |
| callerIpAddress | 작업이 공용 IP 주소를 사용하는 엔터티에서 시작되는 API 호출에 해당하는 경우 호출자 IP 주소입니다. |
| Level | 이벤트의 심각도 수준입니다. |
| URI | 요청의 URI입니다. |
| SdkType | 요청에 사용되는 SDK 유형입니다. |
| PlatformType | 요청에 사용되는 플랫폼 유형입니다. |
| ID | 작업과 관련된 Communication Services ID입니다. |
| 범위 | 액세스 토큰에 있는 Communication Services 범위입니다. |
