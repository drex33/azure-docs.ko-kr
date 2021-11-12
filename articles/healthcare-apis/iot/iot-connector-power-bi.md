---
title: IoT 커넥터 Microsoft Power BI - Azure Healthcare API
description: 이 문서에서는 IoT 커넥터 및 Power BI 사용하는 방법을 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: b099a085041664ed00788720eb3245dd9518c020
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315409"
---
# <a name="iot-connector-and-microsoft-power-bi"></a>IoT 커넥터 및 Microsoft Power BI

이 문서에서는 IoT 커넥터 및 Microsoft Power Business Intelligence(BI)를 사용하는 방법을 살펴보겠습니다.

## <a name="iot-connector-and-power-bi-reference-architecture"></a>IoT 커넥터 및 Power BI 참조 아키텍처

아래 참조 아키텍처는 Microsoft 클라우드 서비스를 사용하여 IoMT(의료 사물 인터넷) 및 전자 의료 기록 교환(FHIR&#174;) 데이터를 기반으로 Power BI 사용하도록 설정하는 기본 구성 요소를 보여 줍니다. 

Microsoft Teams 클라이언트 내에 Power BI 대시보드를 포함시켜 의료 팀의 조정을 더욱 강화할 수도 있습니다. Teams Power BI 포함에 대한 자세한 내용은 [여기를 방문하세요.](/power-bi/collaborate-share/service-embed-report-microsoft-teams)

:::image type="content" source="media/iot-concepts/iot-connector-power-bi.png" alt-text="IoT 커넥터 및 Power BI 스크린샷." lightbox="media/iot-concepts/iot-connector-power-bi.png":::

IoT 커넥터는 위치, 데이터 센터 또는 클라우드에 관계없이 대부분의 IoT 디바이스 또는 게이트웨이에서 IoT 데이터를 수집할 수 있습니다. 

디바이스/게이트웨이 연결을 지원하기 위해 Azure IoT 서비스를 사용하는 것이 좋습니다.

:::image type="content" source="media/iot-concepts/iot-connector-iot-hub-power-bi.png" alt-text="IoT 커넥터, IoT Hub 및 Power BI 스크린샷." lightbox="media/iot-concepts/iot-connector-iot-hub-power-bi.png":::

일부 솔루션의 경우 Azure IoT Hub 대신 Azure IoT Central을 사용할 수 있습니다.

Azure IoT Edge는 에서 IoT Hub 사용하여 디바이스 및/또는 디바이스 내 연결에 대한 온-프레미스 엔드포인트를 만들 수 있습니다.

:::image type="content" source="media/iot-concepts/iot-connector-iot-edge-power-bi.png" alt-text="IoT 커넥터, IoT Hub, IoT Edge 및 Power BI 스크린샷" lightbox="media/iot-concepts/iot-connector-iot-edge-power-bi.png":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 커넥터 및 Power BI 통합에 대해 배웠습니다. IoT 커넥터에 대한 개요는 다음을 참조하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
