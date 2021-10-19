---
title: '개념: fhir 용 Azure API의 fhir (미리 보기) 기능에 대 한 Azure IoT 커넥터의 데이터 흐름'
description: fhir (미리 보기)의 데이터 흐름에 대 한 Azure IoT 커넥터를 이해 합니다. Azure IoT FHIR (미리 보기)에 대 한 커넥터는 데이터를 수집, 정규화, 그룹, 변환 하 고 IoMT 데이터를 FHIR 용 Azure API로 유지 합니다.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: rabhaiya
ms.openlocfilehash: db46af2dffbecb6301af13bbabd871c1c5392354
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436388"
---
# <a name="azure-iot-connector-for-fhir-preview-data-flow"></a>Azure IoT Connector for FHIR(미리 보기) 데이터 흐름

이 문서에서는 신속한 의료 상호 운용성 리소스 (fhir&#174;) *에 대 한 Azure IoT 커넥터의 데이터 흐름에 대 한 개요를 제공 합니다. 장치 데이터를 fhir 기반 [관찰](https://www.hl7.org/fhir/observation.html) 리소스로 변환 하는 fhir 용 Azure IoT 커넥터 내에서 다양 한 데이터 처리 단계에 대해 알아봅니다.

![Azure IoT Connector for FHIR 데이터 흐름](media/concepts-iot-data-flow/iot-connector-data-flow.png)

위의 다이어그램에서는 fhir 용 Azure IoT Connector를 사용 하는 일반적인 데이터 흐름을 보여 줍니다. 

다음은 fhir에 대 한 Azure IoT 커넥터에서 데이터를 받은 후에 발생 하는 다양 한 단계입니다.

## <a name="ingest"></a>수집
수집은 장치 데이터를 fhir 용 Azure IoT 커넥터로 수신 하는 첫 번째 단계입니다. 장치 데이터에 대 한 수집 끝점은 [Azure 이벤트 허브](../../event-hubs/index.yml)에서 호스팅됩니다. Azure 이벤트 허브 플랫폼은 초당 수백만 개의 메시지를 받고 처리 하는 기능을 통해 높은 확장성과 처리량을 지원 합니다. 또한 fhir 용 Azure IoT 커넥터에서 메시지를 비동기적으로 사용 하 여 장치 데이터를 처리 하는 동안 장치가 대기할 필요가 없습니다.

> [!NOTE]
> 이번에는 장치 데이터에 대해 JSON이 유일 하 게 지원 되는 형식입니다.

## <a name="normalize"></a>Normalize
정규화는 위의 Azure 이벤트 허브에서 장치 데이터를 검색 하 고 장치 매핑 템플릿을 사용 하 여 처리 하는 다음 단계입니다. 이 매핑 프로세스는 장치 데이터를 정규화 된 스키마로 변환 합니다. 

정규화 프로세스는 이후 단계에서 데이터 처리를 간소화할 뿐만 아니라 하나의 입력 메시지를 여러 정규화 된 메시지로 프로젝션 하는 기능도 제공 합니다. 예를 들어 장치는 단일 메시지에서 본문 온도, 펄스 율, 혈압 및 respiration 요금에 대 한 여러 가지 중요 한 기호를 보낼 수 있습니다. 이 입력 메시지는 4 개의 별도의 FHIR 리소스를 만듭니다. 각 리소스는 서로 다른 중요 한 기호를 나타내며, 입력 메시지는 서로 다른 4 개의 정규화 된 메시지에 프로젝션 됩니다.

## <a name="group"></a>그룹
그룹은 이전 단계에서 사용할 수 있는 정규화 된 메시지가 장치 id, 측정 유형 및 기간 이라는 세 가지 매개 변수를 사용 하 여 그룹화 되는 다음 단계입니다.

장치 id 및 측정 유형 그룹화에서는 [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData) 측정 유형을 사용할 수 있습니다. 이 형식은 FHIR에서 장치에 대 한 시간 기반 일련의 측정을 나타내는 간결한 방법을 제공 합니다. 및 기간은 fhir에 대 한 Azure IoT 커넥터에서 생성 한 관찰 리소스가 fhir 용 Azure API에 기록 되는 대기 시간을 제어 합니다.

> [!NOTE]
> 기간 값은 기본적으로 15 분 이며 미리 보기용으로 구성할 수 없습니다.

## <a name="transform"></a>변환
변환 단계에서 그룹화 정규화 된 메시지는 FHIR 매핑 템플릿을 통해 처리 됩니다. 템플릿 유형과 일치 하는 메시지는 매핑을 통해 지정 된 대로 FHIR 기반 관찰 리소스로 변환 됩니다.

이 시점에서 연결 된 [환자](https://www.hl7.org/fhir/patient.html) 리소스와 함께 [장치](https://www.hl7.org/fhir/device.html) 리소스는 메시지에 있는 장치 식별자를 사용 하 여 fhir 서버 에서도 검색 됩니다. 이러한 리소스는 생성 되는 관찰 리소스에 대 한 참조로 추가 됩니다.

> [!NOTE]
> 모든 id 조회는 FHIR 서버에 대 한 부하를 줄이기 위해 해결 된 후에 캐시 됩니다. 여러 환자를 사용 하 여 장치를 다시 사용 하려는 경우에는 환자와 관련 된 가상 장치 리소스를 만들고 메시지 페이로드에 가상 장치 식별자를 전송 하는 것이 좋습니다. 가상 장치를 실제 장치 리소스에 부모로 연결할 수 있습니다.

지정 된 장치 식별자에 대 한 장치 리소스가 FHIR 서버에 없으면 생성 시 집합 값에 따라 결과가 달라 집니다 `Resolution Type` . 로 설정 되 면 `Lookup` 특정 메시지는 무시 되 고 파이프라인은 다른 들어오는 메시지를 계속 처리 합니다. 로 설정 된 경우 `Create` fhir에 대 한 Azure IoT Connector는 fhir 서버에서 운영 뼈 장치 및 환자 리소스를 만듭니다.  

## <a name="persist"></a>Persist
변환 단계에서 관찰 FHIR 리소스가 생성 되 면 리소스는 FHIR 용 Azure API에 저장 됩니다. FHIR 리소스가 새 리소스인 경우 서버에 생성 됩니다. FHIR 리소스가 이미 있으면 업데이트 됩니다.

## <a name="next-steps"></a>다음 단계

다음 단계를 클릭 하 여 장치 및 FHIR 매핑 템플릿을 만드는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 매핑 템플릿](iot-mapping-templates.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다. FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다. 