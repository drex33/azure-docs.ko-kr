---
title: 프라이빗 링크 서비스를 사용하여 이벤트 제공
description: 이 문서에서는 프라이빗 링크 서비스를 사용하여 이벤트를 전달할 수 없는 제한 사항을 해결하는 방법에 관해 설명합니다.
ms.topic: how-to
ms.date: 07/01/2021
ms.openlocfilehash: 0672b2b93cf7413ac9a3d46e8d824354276ba89f
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286117"
---
# <a name="deliver-events-using-private-link-service"></a>프라이빗 링크 서비스를 사용하여 이벤트 제공
현재 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 이벤트를 전달할 수 없습니다. 즉, 전달된 이벤트 트래픽이 개인 IP 공간을 벗어나지 않아야 하는 엄격한 네트워크 격리 요구 사항이 있는 경우 지원되지 않습니다. 

## <a name="use-managed-identity"></a>관리 ID 사용
그러나 요구 사항에 따라 공용 IP 공간을 사용하여 암호화된 채널과 보낸 사람의 알려진 ID(이 경우 Event Grid)를 사용하여 이벤트를 보내는 안전한 방법이 필요한 경우 Azure Event Grid 사용자 지정 토픽 또는 시스템 관리 ID가 있는 도메인을 사용하여 이벤트를 Event Hubs, Service Bus 또는 Azure Storage Service에 전송할 수 있습니다. 관리 ID를 사용하여 이벤트를 전송하는 데 관한 자세한 내용은 [관리 ID를 사용한 이벤트 전송](managed-service-identity.md)을 참조하세요. 

그런 다음 Azure Functions에 구성된 프라이빗 링크 또는 가상 네트워크에 배포된 웹후크를 사용하여 이벤트를 풀할 수 있습니다. 샘플: [Azure Functions를 사용하여 프라이빗 엔드포인트에 연결](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/)을 참조하세요.


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="프라이빗 링크 서비스를 통해 제공":::


이 구성에서 트래픽은 공용 IP/인터넷을 통해 Event Grid에서 Event Hubs, Service Bus 또는 Azure Storage로 이동하지만 채널을 암호화할 수 있으며 Event Grid의 관리 ID가 사용됩니다. 프라이빗 링크를 통해 Event Hubs, Service Bus 또는 Azure Storage를 사용하도록 가상 네트워크에 배포된 Azure Functions 또는 웹후크를 구성하는 경우 트래픽의 해당 섹션은 확실히 Azure 내에 유지됩니다.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>관리 ID를 사용하여 Event Hubs에 이벤트 제공
관리 ID를 사용하여 Event Hubs 네임스페이스의 이벤트 허브에 이벤트를 제공하려면 다음 단계를 따릅니다.

1. 시스템 할당 ID 사용: [시스템 토픽](enable-identity-system-topics.md), [사용자 지정 토픽 및 도메인](enable-identity-custom-topics-domains.md).  
1. [Event Hubs 네임스페이스에서 **Azure Event Hubs 데이터 보낸 사람** 역할에 ID를 추가](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal)합니다.
1. [Event Hubs 네임스페이스에서 **신뢰할 수 있는 Microsoft 서비스에서 이 방화벽을 우회하도록 허용** 설정을 사용으로 설정](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services)합니다. 
1. 시스템 할당 ID를 사용하도록 이벤트 허브를 엔드포인트로 사용하는 [이벤트 구독을 구성](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)합니다.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>관리 ID를 사용하여 Service Bus에 이벤트 제공
관리 ID를 사용하여 Service Bus 네임스페이스의 Service Bus 큐 또는 토픽에 이벤트를 제공하려면 다음 단계를 따릅니다.

1. 시스템 할당 ID 사용: [시스템 토픽](enable-identity-system-topics.md), [사용자 지정 토픽 및 도메인](enable-identity-custom-topics-domains.md). 
1. Service Bus 네임스페이스의 [**Azure Service Bus 데이터 보낸 사람**](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) 역할에 ID 추가
1. [Service Bus 네임스페이스에서 **신뢰할 수 있는 Microsoft 서비스에서 이 방화벽을 우회하도록 허용** 설정을 사용으로 설정](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services)합니다. 
1. 시스템 할당 ID를 사용하도록 Service Bus 큐 또는 토픽을 엔드포인트로 사용하는 [이벤트 구독을 구성](managed-service-identity.md)합니다.

## <a name="deliver-events-to-storage"></a>스토리지에 이벤트 제공 
관리 ID를 사용하여 스토리지 큐에 이벤트를 전달하려면 다음 단계를 따릅니다.

1. 시스템 할당 ID 사용: [시스템 토픽](enable-identity-system-topics.md), [사용자 지정 토픽 및 도메인](enable-identity-custom-topics-domains.md). 
1. Azure Storage 큐의 [**스토리지 큐 데이터 메시지 보낸 사람** 역할에 ID를 추가](../storage/blobs/assign-azure-role-data-access.md)합니다.
1. 시스템 할당 ID를 사용하도록 스토리지 큐를 엔드포인트로 사용하는 [이벤트 구독을 구성](managed-service-identity.md#create-event-subscriptions-that-use-an-identity)합니다.


## <a name="next-steps"></a>다음 단계
관리 ID를 사용하여 이벤트를 전송하는 데 관한 자세한 내용은 [관리 ID를 사용한 이벤트 전송](managed-service-identity.md)을 참조하세요.