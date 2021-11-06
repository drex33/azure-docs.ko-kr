---
title: IoT connector Microsoft Power BI-Azure 의료 api
description: 이 문서에서는 IoT 커넥터 및 Power BI를 사용 하는 방법을 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 014bf6fe316252536a14c15bad8255285226ce10
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894797"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>IoT 커넥터 및 Microsoft Power BI

이 문서에서는 IoT 커넥터 및 Microsoft BI (Power Business Intelligence)를 사용 하는 방법을 살펴봅니다.

## <a name="iot-connector-and-power-bi-reference-architecture"></a>IoT 커넥터 및 Power BI 참조 아키텍처

아래 참조 아키텍처는 Microsoft 클라우드 서비스를 사용 하 여 IoMT (의료 사물 인터넷) 및 신속한&#174; 의료 () 데이터 Power BI를 기반으로 하는 기본 구성 요소를 보여 줍니다. 

Microsoft Teams 클라이언트 내에 Power BI 대시보드를 포함 하 여 팀의 조정에 대 한 책임을 높일 수도 있습니다. Teams에서 Power BI를 포함 하는 방법에 대 한 자세한 내용은 [여기](/power-bi/collaborate-share/service-embed-report-microsoft-teams)를 참조 하세요.

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="IoT 커넥터 및 Power BI의 스크린샷" lightbox="media/iot-concepts/iot-connector-power-bi.png":::

IoT 커넥터는 위치, 데이터 센터 또는 클라우드로 모든 IoT 장치 또는 게이트웨이에서 IoT 데이터를 수집할 수 있습니다. 

장치/게이트웨이 연결을 지원 하기 위해 Azure IoT 서비스를 사용 하는 것이 좋습니다.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="IoT 커넥터, IoT Hub 및 Power BI의 스크린샷" lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

일부 솔루션의 경우 Azure IoT 허브 대신 Azure IoT Central을 사용할 수 있습니다.

Azure IoT Edge는 IoT Hub와 함께 사용 하 여 장치 및/또는 장치 연결에 대 한 온-프레미스 끝점을 만들 수 있습니다.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="IoT 커넥터, IoT Hub, IoT Edge 및 Power BI의 스크린샷" lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 커넥터 및 Power BI 통합에 대해 알아보았습니다. IoT 커넥터에 대 한 개요는를 참조 하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
