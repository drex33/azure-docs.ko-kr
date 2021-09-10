---
title: 이벤트 처리기에 대한 이벤트 전달 인증(Azure Event Grid)
description: 이 문서에서는 Azure Event Grid의 이벤트 처리기에 대한 전달을 인증하는 다양한 방법을 설명합니다.
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 01383809e6aab895ff4ed42763c57004a6ee02a8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003236"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>이벤트 처리기에 대한 이벤트 전달 인증(Azure Event Grid)
이 문서에서는 이벤트 처리기에 이벤트 전달 인증에 대한 정보를 제공합니다. 

## <a name="overview"></a>개요
Azure Event Grid는 다양한 인증 방법을 사용하여 이벤트 처리기에 이벤트를 전달합니다. `

| 인증 방법 | 지원되는 처리기 | 설명  |
|--|--|--|
액세스 키 | <p>Event Hubs</p><p>Service Bus</p><p>Storage 큐</p><p>릴레이 하이브리드 연결</p><p>Azure 기능</p><p>Storage Blob(Deadletter)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p> | 액세스 키는 Event Grid 서비스 주체의 자격 증명을 사용하여 가져옵니다. Azure 구독에 Event Grid 리소스 공급자를 등록하면 Event Grid에 권한이 부여됩니다. |  
관리형 시스템 ID <br/>&<br/> 역할 기반 액세스 제어 | <p>Event Hubs</p><p>Service Bus</p><p>Storage 큐</p><p>Storage Blob(Deadletter)</p></li></ul> | 토픽에 대해 관리형 시스템 ID를 사용하도록 설정하고 대상의 적절한 역할에 추가합니다. 자세한 내용은 [이벤트 전달에 시스템 할당 ID 사용](#use-system-assigned-identities-for-event-delivery)을 참조하세요.  |
|Azure AD로 보호된 웹후크를 사용하는 전달자 토큰 인증 | 웹후크 | 자세한 내용은 [웹후크 엔드포인트에 대한 이벤트 전달 인증](#authenticate-event-delivery-to-webhook-endpoints) 섹션을 참조하세요. |
클라이언트 암호를 쿼리 매개 변수로 | 웹후크 | 자세한 내용은 [클라이언트 암호를 쿼리 매개 변수로 사용](#using-client-secret-as-a-query-parameter) 섹션을 참조하세요. |

## <a name="use-system-assigned-identities-for-event-delivery"></a>이벤트 전달에 시스템 할당 ID 사용
항목 또는 도메인에 대해 시스템 할당 관리 ID를 사용하도록 설정하고 이 ID를 사용하여 Service Bus 큐 및 항목, 이벤트 허브, 스토리지 계정과 같은 지원되는 대상으로 이벤트를 전달할 수 있습니다.

실행할 단계는 다음과 같습니다. 

1. 시스템 할당 ID를 사용하여 항목 또는 도메인을 만들거나, 기존 항목 또는 도메인을 업데이트하여 ID를 사용하도록 설정합니다. 자세한 내용은 [시스템 토픽에 관리 ID 사용](enable-identity-system-topics.md) 또는 [사용자 지정 토픽 또는 도메인에 관리 ID 사용](enable-identity-custom-topics-domains.md)을 참조하세요.
1. 대상(예: Service Bus 큐)의 적절한 역할(예: Service Bus 데이터 보낸 사람)에 ID를 추가합니다. 자세한 내용은 [ID에 Event Grid 대상에 대한 액세스 권한 부여](add-identity-roles.md)를 참조하세요.
1. 이벤트 구독을 만들 때 ID를 사용하여 대상에 이벤트를 전달하도록 설정합니다. 자세한 내용은 [ID를 사용하는 이벤트 구독 만들기](managed-service-identity.md)를 참조하세요. 

자세한 단계별 지침은 [관리 ID를 사용하여 이벤트 전달](managed-service-identity.md)을 참조하세요.


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>웹후크 엔드포인트에 대한 이벤트 전달 인증
다음 섹션에서는 웹후크 엔드포인트에 대한 이벤트 전달을 인증하는 방법을 설명합니다. 사용하는 방법에 관계 없이 유효성 검사 핸드셰이크 메커니즘을 사용합니다. 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요. 


### <a name="using-azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory) 사용
Azure AD를 사용하여 Event Grid에서 이벤트를 수신하는 데 사용되는 Webhook 엔드포인트를 보호할 수 있습니다. Azure AD 애플리케이션을 만들고 애플리케이션에서 Event Grid 권한을 부여하는 역할 및 서비스 주체를 만들고 Azure AD 애플리케이션을 사용하도록 이벤트 구독을 구성해야 합니다. [Azure Active Directory를 Event Grid로 구성하는 방법에 대해 알아봅니다](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>클라이언트 암호를 쿼리 매개 변수로 사용
또한 이벤트 구독을 만들 때 지정된 Webhook 대상 URL에 쿼리 매개 변수를 추가하여 Webhook 엔드포인트를 보호할 수 있습니다. 쿼리 매개 변수 중 하나를 [액세스 토큰](https://en.wikipedia.org/wiki/Access_token)과 같은 클라이언트 암호 또는 공유 비밀로 설정합니다. Event Grid 서비스는 Webhook에 대한 모든 이벤트 전달 요청에 모든 쿼리 매개 변수를 포함합니다. Webhook 서비스는 암호를 검색하고 유효성을 검사할 수 있습니다. 클라이언트 암호를 업데이트하는 경우에는 이벤트 구독도 업데이트해야 합니다. 이 비밀 순환 동안 전달 오류가 발생하지 않도록 하려면 Webhook가 새 암호로 이벤트 구독을 업데이트하기 전에 제한된 기간 동안 이전 및 새 비밀을 모두 수락하도록 설정합니다. 

쿼리 매개 변수는 클라이언트 암호를 포함할 수 있으므로 신중하게 처리됩니다. 암호화된 상태로 저장되며 서비스 운영자가 액세스할 수 없습니다. 또한 서비스 로그/추적의 일부로 기록되지 않습니다. 이벤트 구독 속성을 검색할 때 기본적으로 대상 쿼리 매개 변수가 반환되지 않습니다. 예를 들어 [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) 매개 변수는 Azure [CLI](/cli/azure)에서 사용해야 합니다.

웹후크에 이벤트를 전달하는 방법에 대한 자세한 내용은 [웹후크 이벤트 전달](webhook-event-delivery.md)을 참조하세요.

> [!IMPORTANT]
> Azure Event Grid는 **HTTPS** 웹후크 엔드포인트만 지원합니다. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>CloudEvents v1.0을 사용한 엔드포인트 유효성 검사
Event Grid에 대해 잘 알고 있다면 악용 방지를 위한 엔드포인트 유효성 검사 핸드셰이크에 대해 알고 있을 것입니다. CloudEvents v1.0은 **HTTP OPTIONS** 메서드를 사용하여 자체 [악용 방지 의미 체계](webhook-event-delivery.md)를 구현합니다. 자세한 내용은 [이벤트 전달을 위한 HTTP 1.1 웹후크 - 버전 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)을 참조하세요. 출력에 CloudEvents 스키마를 사용하는 경우, Event Grid는 Event Grid 유효성 검사 이벤트 메커니즘 대신 CloudEvents v1.0 악용 방지를 사용합니다. 자세한 내용은 [Event Grid에서 CloudEvents v1.0 스키마 사용](cloudevents-schema.md)을 참조하세요. 


## <a name="next-steps"></a>다음 단계
항목 또는 도메인에 이벤트를 게시하는 클라이언트 인증에 대해 알아보려면 [게시 클라이언트 인증](security-authenticate-publishing-clients.md)을 참조하세요. 
