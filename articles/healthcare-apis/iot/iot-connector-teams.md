---
title: IoT 커넥터 및 Teams 알림-Azure 의료 api
description: 이 문서에서는 IoT 커넥터 및 Teams 알림을 사용 하는 방법에 대해 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 5f2cb9c9a4e4ef20e489c36a92285a6f96f74282
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894813"
---
# <a name="iot-connector-and-microsoft-teams-notifications"></a>IoT 커넥터 및 Microsoft Teams 알림

이 문서에서는 IoT 커넥터 및 알림에 대 한 Microsoft Teams 사용 하는 방법을 살펴봅니다.

## <a name="iot-connector-and-teams-notifications-reference-architecture"></a>IoT 커넥터 및 Teams 알림 참조 아키텍처

IoT 커넥터를 결합 하는 경우 fhir&#174; (신속한 의료 상호 운용성 리소스) 서비스 및 Teams를 통해 여러 주의 솔루션을 사용할 수 있습니다. 

다음은 iot 커넥터, fhir 및 Teams 환자 앱을 사용 하도록 설정 하는 Teams 알림 개념 아키텍처에 대 한 iot 커넥터입니다. 

Power BI 대시보드를 Microsoft Teams 클라이언트 내에 포함할 수도 있습니다. Microsoft 팀에서 Power BI를 포함 하는 방법에 대 한 자세한 내용은 [여기](/power-bi/collaborate-share/service-embed-report-microsoft-teams)를 참조 하세요.

:::image type="content" source="media/iot-concepts/iot-connector-teams.png" alt-text="IoT 커넥터 및 Teams의 스크린샷" lightbox="media/iot-concepts/iot-connector-teams.png":::

용 IoT 커넥터는 위치, 데이터 센터 또는 클라우드에 상관 없이 대부분의 IoT 장치 또는 게이트웨이에서 IoT 데이터를 수집할 수 있습니다. 

장치/게이트웨이 연결을 지원 하기 위해 Azure IoT 서비스를 사용 하는 것이 좋습니다.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-teams.png" alt-text="IoT 커넥터 및 IoT Hub의 스크린샷" lightbox="media/iot-concepts/iot-connector-iot-hub-teams.png":::

일부 솔루션의 경우 Azure IoT 허브 대신 Azure IoT Central을 사용할 수 있습니다.

Azure IoT Edge는 IoT Hub와 함께 사용 하 여 장치 및/또는 장치 연결에 대 한 온-프레미스 끝점을 만들 수 있습니다.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-teams.png" alt-text="IoT 커넥터 및 IoT Edge의 스크린샷" lightbox="media/iot-concepts/iot-connector-iot-edge-teams.png":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 커넥터 및 Teams 알림 통합에 대해 알아보았습니다. IoT 커넥터에 대 한 개요는를 참조 하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
