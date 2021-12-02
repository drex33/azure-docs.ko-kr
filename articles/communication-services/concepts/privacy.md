---
title: Azure Communication Services에 대한 지역 가용성 및 데이터 보존
description: Azure Communication Services에 대한 데이터 보존 및 개인 정보 관련 문제에 대해 알아봅니다.
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b2a09c1cb4185bffc8891f91d37572afcaca9146
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133434561"
---
# <a name="region-availability-and-data-residency"></a>지역 가용성 및 데이터 상주

Azure Communication Services는 개인 정보 및 개인 데이터 요구 사항을 충족할 수 있도록 고객을 지원하기 위해 노력하고 있습니다. 애플리케이션을 사용하는 사용자와 직접적인 관계가 있는 Communication Services를 사용하는 개발자는 잠재적으로 해당 사용자의 데이터를 제어할 수 있습니다. Azure Communication Services는 사용자를 대신하여 이 미사용 데이터를 저장하고 암호화하므로 이 데이터의 프로세서가 될 가능성이 높습니다. 이 페이지에는 서비스에서 데이터를 보존하는 방법과 이 데이터를 식별하고, 내보내고, 삭제하는 방법이 요약되어 있습니다.

## <a name="data-residency"></a>데이터 상주

Azure Communication Services 리소스를 [만들](../quickstarts/create-communication-resource.md) 때 **지리(Azure** 데이터 센터가 아닌)를 지정합니다. Communication Services에서 저장한 휴지 상태의 모든 채팅 메시지 및 리소스 데이터는 Communication Services에서 내부적으로 선택한 데이터 센터의 해당 지역에 보존됩니다. 데이터는 다른 지역에서 전송되거나 처리될 수 있습니다. 이러한 글로벌 엔드포인트는 위치에 관계없이 최종 사용자에게 대기 시간이 짧은 고성능 환경을 제공하는 데 필요합니다.

선택할 수 있는 지역 목록에는 다음이 포함됩니다.
- 아프리카
- 아시아 태평양
- 오스트레일리아
- 브라질
- 캐나다
- 유럽
- 프랑스
- 독일
- 인도
- 일본
- 한국
- 영국
- 미국

> [!NOTE]
> PSTN 및 SMS의 경우 서비스의 운영 및 요금 청구에 필요한 통화 및 메시지 데이터 레코드를 미국에 저장할 수 있습니다.

## <a name="data-collection"></a>데이터 수집

Azure Communication Services는 서비스를 전달하는 데 필요한 진단 데이터만 수집합니다. 

## <a name="data-residency-and-events"></a>데이터 보존 및 이벤트

Azure Communication Services로 구성된 모든 Event Grid 시스템 토픽은 글로벌 위치에 만들어집니다. 안정적으로 제공하기 위해 글로벌 Event Grid 시스템 토픽은 모든 Microsoft 데이터 센터에 이벤트 데이터를 저장할 수 있습니다. Azure Communication Services를 사용하여 Event Grid를 구성하면 사용자가 제어하는 Azure 리소스인 Event Grid에 이벤트 데이터를 제공합니다. Azure Communication Services는 Azure Event Grid를 활용하도록 구성될 수 있지만, Event Grid 리소스와 여기에 저장된 데이터를 관리해야 합니다.

## <a name="relating-humans-to-azure-communication-services-identities"></a>사용자와 Azure Communication Services ID의 관계

애플리케이션은 인간 사용자와 Communication Service ID 간의 관계를 관리합니다. 인간 사용자에 대한 데이터를 삭제하려면 해당 사용자와 상호 관련된 모든 Communication Service ID와 관련된 데이터를 삭제해야 합니다.

Communication Service 데이터에는 다음과 같은 두 가지 범주가 있습니다.
- **API 데이터** 이 데이터는 Communication Service API를 통해 만들어지고 관리되며, 일반적인 예로 채팅 API를 통해 관리되는 채팅 메시지가 있습니다.
- **Azure Monitor 로그** 이 데이터는 서비스를 통해 만들어지고, Azure Monitor 데이터 플랫폼을 통해 관리됩니다. 이 데이터에는 Communication Services 사용량을 이해하는 데 도움이 되는 원격 분석 및 메트릭이 포함됩니다. 이는 Communication Service API를 통해 관리되지 않습니다.

## <a name="api-data"></a>API 데이터

### <a name="identities"></a>Identities

Azure Communication Services는 ID의 디렉터리를 유지 관리하고 [DeleteIdentity](/rest/api/communication/communicationidentity/communication-identity/delete) API를 사용하여 ID를 제거합니다. ID를 삭제하면 연결된 모든 액세스 토큰이 취소되고 해당 채팅 메시지가 삭제됩니다. ID를 제거하는 방법에 대한 자세한 내용은 [이 페이지를 참조하세요](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Communication Services에서 Azure Portal 또는 Azure Resource Manager API를 사용하여 개인 데이터를 만들 수 있습니다. [이 페이지를 사용하여 Azure Resource Manager 시스템에서 개인 데이터를 관리하는 방법을 알아보세요.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>전화 번호 관리

Azure Communication Services는 Communication Services 리소스와 연결된 전화 번호의 디렉터리를 유지 관리합니다. [전화 번호 관리 API](/rest/api/communication/phonenumbers)를 사용하여 전화 번호를 검색하고 삭제합니다.

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>채팅

채팅 스레드 및 메시지는 명시적으로 삭제될 때까지 유지됩니다. [채팅 API](/rest/api/communication/chat/chatthread)를 사용하여 메시지를 가져오고, 나열하고, 업데이트하고, 삭제합니다.

- `Get Thread`
- `Get Message`
- `List Messages`
- `Update Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>sms

보내고 받은 SMS 메시지는 서비스에서 임시로 처리되며 보존되지 않습니다.

### <a name="pstn-voice-calling"></a>PSTN 음성 통화

오디오 및 비디오 통신은 서비스에서 임시로 처리되며 Azure Monitor 로그 이외의 리소스에는 통화 처리 데이터가 보존되지 않습니다.

### <a name="internet-voice-and-video-calling"></a>인터넷 음성 및 비디오 통화

오디오 및 비디오 통신은 서비스에서 임시로 처리되며 Azure Monitor 로그 이외의 리소스에는 통화 처리 데이터가 보존되지 않습니다.

### <a name="call-recording"></a>통화 기록

통화 기록은 리소스를 만드는 48시간 동안 ```Data Location```에 대해 선택한 것과 동일한 지역에 일시적으로 저장됩니다. 그 후에는 녹음이 삭제되므로 녹음을 안전한 규정 위치에 저장할 책임은 사용자에게 있습니다.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor 및 Log Analytics

Azure Communication Services는 서비스 운영 상태 및 사용률을 이해하기 위해 Azure Monitor 로깅 데이터에 제공됩니다. 이러한 로그 중 일부에는 Communication Service ID와 전화 번호가 필드 데이터로 포함됩니다. 잠재적으로 개인 데이터를 삭제하려면 [이러한 절차를 Azure Monitor에 사용](../../azure-monitor/logs/personal-data-mgmt.md)합니다. [Azure Monitor에 대한 기본 보존 기간](../../azure-monitor/logs/manage-cost-storage.md)을 구성해야 할 수도 있습니다.

## <a name="additional-resources"></a>추가 리소스

- [GDPR 및 CCPA에 대한 Azure 데이터 주체 요청](/microsoft-365/compliance/gdpr-dsr-azure)
- [Microsoft 보안 센터](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure 대화형 지도 - 내 고객 데이터는 어디에 있나요?](https://azuredatacentermap.azurewebsites.net/)
