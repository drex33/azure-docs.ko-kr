---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 09/29/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c6c6a92848b91f32dec612abe7c16ab73b71cccd
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300257"
---
## <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스
**신뢰할 수 있는 Microsoft 서비스에서 이 방화벽을 무시하도록 허용** 설정을 사용하도록 설정하면 다음 서비스에 Service Bus 리소스에 대한 액세스 권한이 부여됩니다.

| 신뢰할 수 있는 서비스 | 지원되는 사용 시나리오 | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid에서 Service Bus 네임스페이스의 큐 또는 토픽에 이벤트를 보낼 수 있습니다. 다음 단계도 수행해야 합니다. <ul><li>항목 또는 도메인에 대해 시스템이 할당한 ID 사용</li><li>Service Bus 네임스페이스의 Azure Service Bus 데이터 보낸 사람 역할에 ID 추가</li><li>그런 다음, 시스템 할당 ID를 사용하도록 Service Bus 큐 또는 토픽을 엔드포인트로 사용하는 이벤트 구독을 구성합니다.</li></ul> <p>자세한 내용은 [관리 ID를 사용한 이벤트 전달](../../event-grid/managed-service-identity.md)을 참조하세요.</p>|
| Azure API Management | <p>API Management 서비스를 사용하면 Service Bus 네임스페이스의 Service Bus 큐/토픽에 메시지를 보낼 수 있습니다.</p><ul><li>[송신 요청 정책](../../api-management/api-management-sample-send-request.md)을 사용하여 API를 호출할 때 Service Bus 큐/토픽에 메시지를 전송하여 사용자 지정 워크플로를 트리거할 수 있습니다.</li><li>API에서 Service Bus 큐/토픽을 API의 백 엔드로 취급할 수도 있습니다. 샘플 정책은 [Service Bus 큐 또는 토픽에 액세스하기 위해 관리 ID를 사용하여 인증](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Service%20Bus.xml)을 참조하세요. 다음 단계도 수행해야 합니다.<ol><li>API Management 인스턴스에서 시스템 할당 ID를 사용하도록 설정합니다. 지침은 [Azure API Management에서 관리 ID 사용](../../api-management/api-management-howto-use-managed-service-identity.md)을 참조하세요.</li><li>Service Bus 네임스페이스의 **Azure Service Bus 데이터 보낸 사람** 역할에 ID 추가</li></ol></li></ul> | 
| Azure IoT Central | <p>IoT Central Service Bus 네임스페이스의 Service Bus 큐 또는 토픽으로 데이터를 내보낼 수 있습니다. 다음 단계도 수행해야 합니다.</p><ul><li>IoT Central 애플리케이션에 시스템 할당 ID 사용</li><li>Service Bus 네임스페이스의 **Azure Service Bus Data Sender** 역할에 ID를 추가합니다. </li><li>그런 다음 id 기반 인증을 사용하도록 [IoT Central 애플리케이션에서 Service Bus 내보내기 대상을](../../iot-central/core/howto-export-data.md) 구성합니다. </li>
