---
title: fhir (미리 보기) 메트릭에 대 한 IoT Connector 표시 및 구성
description: 이 문서에서는 fhir (미리 보기) 메트릭에 대 한 Azure IoT 커넥터를 표시 하 고 구성 하는 방법을 설명 합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 7c8e7a32f6e62685a11d772924db24d15d91a286
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434763"
---
# <a name="display-and-configure-iot-connector-for-fhir-preview-metrics"></a>fhir (미리 보기) 메트릭에 대 한 IoT Connector 표시 및 구성 

이 문서에서는 신속한 의료 상호 운용성 리소스 (fhir&#174;) * 메트릭에 대 한 Azure IoT 커넥터를 표시 하 고 구성 하는 방법을 알아봅니다.

> [!TIP]
> 메트릭 데이터의 내보내기를 설정 하는 방법을 알아보려면 [진단 설정을 통해 fhir (미리 보기) 메트릭에 대 한 Azure IoT 커넥터 내보내기](iot-metrics-diagnostics-export.md)의 지침을 따르세요.

## <a name="display-metrics-for-azure-iot-connector-for-fhir-preview"></a>fhir 용 Azure IoT 커넥터 (미리 보기)에 대 한 메트릭 표시

1. Azure Portal에 로그인 하 고 FHIR 서비스에 대 한 Azure API를 선택 합니다. 

2. 왼쪽 창에서 **메트릭** 을 선택 합니다. 

3. **IoT Connector** 탭을 선택 합니다.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-main.png" alt-text="들어오는 메시지와 정규화 된 메시지 수를 표시 하는 꺾은선형 그래프가 포함 된 ' IoT Connector ' 창의 스크린샷" lightbox="media/iot-metrics-display/iot-metrics-main.png"::: 

4. 메트릭을 보려면 IoT Connector를 선택 합니다. 예를 들어이 Azure API for FHIR 서비스와 연결 된 4 개의 IoT 커넥터 (*커넥터 1*, *커넥터 2* 등)가 있습니다.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-connector.png" alt-text="IoT Connector 탭 1, 2, 3 및 4를 표시 하는 ' IoT Connector ' 창의 스크린샷" lightbox="media/iot-metrics-display/iot-metrics-select-connector.png"::: 

5. 표시 하려는 IoT Connector 메트릭의 기간 (예: **1 시간**, **24 시간**, **7 일** 또는 **사용자 지정**)을 선택 합니다. **사용자 지정** 탭을 선택 하 여 IoT Connector 메트릭을 표시 하기 위한 특정 시간/날짜 조합을 만들 수 있습니다.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-select-time.png" alt-text="' 커넥터 1 '에 대 한 ' 1 시간 ' 시간 구분선 그래프를 표시 하는 ' IoT Connector ' 창의 스크린샷" lightbox="media/iot-metrics-display/iot-metrics-select-time.png"::: 
 
## <a name="metric-types-for-azure-iot-connector-for-fhir-preview"></a>fhir의 Azure IoT 커넥터에 대 한 메트릭 유형 (미리 보기) 

> [!TIP]
> fhir 용 Azure IoT 커넥터에서 데이터 흐름에 대 한 자세한 내용은 fhir [(미리 보기) 데이터 흐름에 대 한 Azure IoT 커넥터](iot-data-flow.md) 및 오류 메시지 및 수정에 대 한 자세한 내용은 [fhir (미리 보기) 문제 해결 가이드에 대 한 Azure IoT 커넥터](iot-troubleshoot-guide.md) 를 참조 하세요.

표시할 수 있는 IoT Connector 메트릭은 다음 표에 나와 있습니다.

|메트릭 유형|메트릭 용도| 
|-----------|--------------|
|들어오는 메시지 수|받은 원시 수신 메시지 수 (예: 장치 이벤트)를 표시 합니다.|
|정규화된 메시지 수|정규화 된 메시지 수를 표시 합니다.|
|메시지 그룹의 수|지정 된 기간에 메시지가 집계 된 그룹 수를 표시 합니다.|
|평균 정규화 된 단계 대기 시간|정규화 된 단계의 평균 대기 시간을 표시 합니다. 정규화 된 단계는 원시 들어오는 메시지에 대해 정규화를 수행 합니다.|
|평균 그룹 단계 대기 시간|그룹 단계의 평균 대기 시간을 표시 합니다. 그룹 단계는 정규화 된 메시지에서 버퍼링, 집계 및 그룹화를 수행 합니다.| 
|총 오류 수|총 오류 수를 표시 합니다.| 

## <a name="focus-on-and-configure-azure-iot-connector-for-fhir-preview-metrics"></a>fhir (미리 보기) 메트릭에 대 한 Azure IoT 커넥터에 집중 및 구성

이 예제에서는 **들어오는 메시지 메트릭의 수** 를 집중적으로 살펴보겠습니다.

1. 초점을 맞출 특정 시점을 선택 합니다.

   :::image type="content" source="media/iot-metrics-display/iot-metrics-focus.png" alt-text="꺾은선형 그래프에서 단일 지정 시간을 강조 표시 하는 ' 들어오는 메시지 수 ' 메트릭 창의 스크린샷" lightbox="media/iot-metrics-display/iot-metrics-focus.png"::: 

2. **들어오는 메시지 수** 창에서 **메트릭 추가**, **필터 추가** 또는 **분할 적용** 을 선택 하 여 메트릭을 추가로 사용자 지정할 수 있습니다. 

   :::image type="content" source="media/iot-metrics-display/iot-metrics-add-options.png" alt-text="' 메트릭 추가 &quot;, ' 필터 추가 ' 및 ' 분할 적용 ' 단추를 강조 표시 하는 ' 들어오는 메시지 수 ' 메트릭 창의 스크린샷" lightbox="media/iot-metrics-display/iot-metrics-add-options.png"::: 

## <a name="conclusion"></a>결론 
데이터 평면 메트릭에 대 한 액세스는 모니터링 및 문제 해결에 필수적입니다. Azure IoT FHIR 용 커넥터는 메트릭을 통해 이러한 작업을 지원 합니다. 

## <a name="next-steps"></a>다음 단계

fhir 용 Azure IoT 커넥터에 대 한 질문과 대답을 확인 하세요.

>[!div class="nextstepaction"]
>[Azure IoT FHIR FAQ 용 커넥터](fhir-faq.yml)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다. FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다. 
