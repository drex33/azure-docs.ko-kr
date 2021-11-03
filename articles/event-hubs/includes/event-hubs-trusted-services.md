---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/20/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1e9d61eca35cc9ea9fe20731fa093354c0eac5f8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131254084"
---
## <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스
**신뢰할 수 있는 Microsoft 서비스에서 이 방화벽을 무시하도록 허용** 설정을 사용하도록 설정하면 다음 서비스에 Event Hubs 리소스에 대한 액세스 권한이 부여됩니다.

| 신뢰할 수 있는 서비스 | 지원되는 사용 시나리오 | 
| --------------- | ------------------------- | 
| Azure Event Grid | Azure Event Grid에서 Event Hubs 네임스페이스의 이벤트 허브로 이벤트를 보낼 수 있습니다. 다음 단계도 수행해야 합니다. <ul><li>항목 또는 도메인에 대한 시스템 할당 ID 사용</li><li>Event Hubs 네임스페이스에서 Azure Event Hubs 데이터 보낸 사람 역할에 ID를 추가합니다.</li><li>그런 다음, 시스템 할당 ID를 사용하도록 이벤트 허브를 엔드포인트로 사용하는 이벤트 구독을 구성합니다.</li></ul> <p>자세한 내용은 [관리 ID를 사용한 이벤트 전달](../../event-grid/managed-service-identity.md)을 참조하세요.</p>|
| Azure Monitor (진단 설정 및 작업 그룹) | Azure Monitor Event Hubs 네임 스페이스의 event hubs에 진단 정보 및 경고 알림을 보낼 수 있습니다. Azure Monitor는 이벤트 허브에서 읽고 이벤트 허브에 데이터를 쓸 수도 있습니다. |
| Azure Stream Analytics | Azure Stream Analytics 작업이 Event Hubs 네임스페이스의 이벤트 허브에서 데이터를 읽거나([입력](../../stream-analytics/stream-analytics-add-inputs.md)) 데이터를 작성([출력](../../stream-analytics/event-hubs-output.md))할 수 있습니다. <p>**중요**: **관리 ID** 를 사용하여 이벤트 허브에 액세스하도록 Stream Analytics 작업을 구성해야 합니다. 자세한 내용은 [관리 ID를 사용하여 Azure Stream Analytics 작업에서 Event Hub에 액세스(미리 보기)](../../stream-analytics/event-hubs-managed-identity.md)를 참조하세요. </p>|
| Azure IoT Hub | IoT Hub를 사용하면 Event Hub 네임스페이스의 이벤트 허브로 메시지를 보낼 수 있습니다. 다음 단계도 수행해야 합니다. <ul><li>IoT 허브에 대해 시스템 할당 ID 사용</li><li>Event Hubs 네임스페이스에서 Azure Event Hubs 데이터 보낸 사람 역할에 ID를 추가합니다.</li><li>그런 다음, ID 기반 인증을 사용하도록 이벤트 허브를 사용자 지정 엔드포인트로 사용하는 IoT Hub를 구성합니다.</li></ul>
| Azure API Management | <p>API Management 서비스를 사용하면 Event Hubs 네임스페이스의 이벤트 허브로 이벤트를 보낼 수 있습니다.</p> <ul><li>[송신 요청 정책](../../api-management/api-management-sample-send-request.md)을 사용하여 API를 호출할 때 이벤트 허브에 이벤트를 전송하여 사용자 지정 워크플로를 트리거할 수 있습니다.</li><li>API에서 이벤트 허브를 백 엔드로 처리할 수도 있습니다. 샘플 정책은 [이벤트 허브에 액세스하기 위해 관리 ID를 사용하여 인증](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Authenticate%20using%20Managed%20Identity%20to%20access%20Event%20Hub.xml)을 참조하세요. 다음 단계도 수행해야 합니다.<ol><li>API Management 인스턴스에서 시스템 할당 ID를 사용하도록 설정합니다. 지침은 [Azure API Management에서 관리 ID 사용](../../api-management/api-management-howto-use-managed-service-identity.md)을 참조하세요.</li><li>Event Hubs 네임스페이스에서 **Azure Event Hubs 데이터 보낸 사람** 역할에 ID를 추가합니다.</li></ol></li></ul> | 
| Azure IoT Central | <p>IoT Central를 사용 하 여 이벤트 허브 네임 스페이스의 event hubs로 데이터를 내보낼 수 있습니다. 다음 단계도 수행해야 합니다.</p><ul><li>IoT Central 응용 프로그램에 대해 시스템이 할당 한 id를 사용 하도록 설정 합니다.</li><li>Event Hubs 네임 스페이스의 **Azure Event Hubs 데이터 발신자** 역할에 id를 추가 합니다.</li><li>그런 다음 id 기반 인증을 사용 하도록 [IoT Central 응용 프로그램에서 Event Hubs 내보내기 대상을](../../iot-central/core/howto-export-data.md) 구성 합니다.</li>
