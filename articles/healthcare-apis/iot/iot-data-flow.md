---
title: IoT 커넥터의 데이터 흐름 - Azure Healthcare API
description: IoT 커넥터의 데이터 흐름을 이해합니다. IoT 커넥터는 IoMT 데이터를 수집, 정규화, 그룹화, 변환 및 FHIR 서비스에 유지합니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: 80de5c094175a4c2372befeaedddbe1ac56bba8a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308333"
---
# <a name="iot-connector-data-flow"></a>IoT 커넥터 데이터 흐름

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 IoT 커넥터 데이터 흐름에 대한 개요를 제공합니다. 디바이스 데이터를 전자 의료 기록 교환(FHIR&#174;) 기반 [관찰](https://www.hl7.org/fhir/observation.html) 리소스로 변환하는 IoT 커넥터 내의 다양한 데이터 처리 단계에 대해 알아봅니다.

다음은 IoT 커넥터에서 받은 데이터가 진행되는 여러 단계입니다.

## <a name="ingest"></a>수집
수집은 디바이스 데이터가 IoT 커넥터에 수신되는 첫 번째 단계입니다. 디바이스 데이터에 대한 수집 엔드포인트는 Azure [Event Hub](../../event-hubs/index.yml)에서 호스트됩니다. Azure Event Hub 플랫폼은 초당 수백만 개의 메시지를 수신하고 처리하는 기능을 갖춘 높은 규모와 처리량을 지원합니다. 또한 IoT 커넥터가 메시지를 비동기적으로 사용할 수 있도록 하여 디바이스 데이터가 처리되는 동안 디바이스가 대기할 필요가 없습니다.

> [!NOTE]
> JSON은 현재 디바이스 데이터에 대해 지원되는 유일한 형식입니다.

## <a name="normalize"></a>Normalize
정규화는 위의 Azure Event Hub에서 디바이스 데이터를 검색하고 디바이스 매핑을 사용하여 처리하는 다음 단계입니다. 이 매핑 프로세스는 디바이스 데이터를 정규화된 스키마로 변환합니다. 

정규화 프로세스는 이후 단계에서 데이터 처리를 간소화할 뿐만 아니라 하나의 입력 메시지를 여러 정규화된 메시지에 프로젝션하는 기능을 제공합니다. 예를 들어 디바이스는 단일 메시지로 신체 온도, 펄스 속도, 압력 및 대기 속도에 대한 여러 가지 바이탈 기호를 보낼 수 있습니다. 이 입력 메시지는 4개의 개별 FHIR 리소스를 만듭니다. 각 리소스는 입력 메시지가 4개의 서로 다른 정규화된 메시지에 프로젝션된 다른 중요한 기호를 나타냅니다.

## <a name="group"></a>그룹
그룹은 이전 단계에서 사용할 수 있는 정규화된 메시지가 세 가지 매개 변수를 사용하여 그룹화되는 다음 단계입니다.

* 디바이스 ID
* 측정 유형 
* 기간

디바이스 ID 및 측정 유형 그룹화는 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 측정 유형을 사용할 수 있습니다. 이 형식은 FHIR의 디바이스에서 시간 기반 일련의 측정값을 나타내는 간결한 방법을 제공합니다. 또한 기간은 IoT 커넥터에서 생성된 관찰 리소스가 FHIR 서비스에 기록되는 대기 시간을 제어합니다.

> [!NOTE]
> 기간 값은 기본적으로 15분으로 설정되며 미리 보기로 구성할 수 없습니다.

## <a name="transform"></a>변환
변환 단계에서 그룹화된 정규화된 메시지는 FHIR 대상 매핑 템플릿을 통해 처리됩니다. 템플릿 형식과 일치하는 메시지는 매핑을 통해 지정된 대로 FHIR 기반 관찰 리소스로 변환됩니다.

이 시점에서 [디바이스](https://www.hl7.org/fhir/device.html) 리소스는 연결된 [환자](https://www.hl7.org/fhir/patient.html) 리소스와 함께 메시지에 있는 디바이스 식별자를 사용하여 FHIR 서비스에서도 검색됩니다. 이러한 리소스는 생성되는 Observation 리소스에 대한 참조로 추가됩니다.

> [!NOTE]
> 모든 ID 조회는 FHIR 서비스의 부하를 줄이기 위해 확인되면 캐시됩니다. 여러 환자와 함께 디바이스를 다시 사용하려는 경우 환자와 관련된 가상 디바이스 리소스를 만들고 메시지 페이로드에 가상 디바이스 식별자를 보내는 것이 좋습니다. 가상 디바이스는 부모로서 실제 디바이스 리소스에 연결할 수 있습니다.

지정된 디바이스 식별자의 디바이스 리소스가 FHIR 서비스에 없는 경우 결과는 생성 시 설정된 값에 따라 `Resolution Type` 달라집니다. 로 설정하면 `Lookup` 특정 메시지가 무시되고 파이프라인이 다른 들어오는 메시지를 계속 처리합니다. 로 설정하면 `Create` IoT 커넥터가 FHIR 서비스에 미설치 디바이스 및 환자 리소스를 만듭니다.  

## <a name="persist"></a>Persist
관찰 FHIR 리소스가 변환 단계에서 생성되면 리소스가 FHIR 서비스에 저장됩니다. FHIR 리소스가 새 리소스인 경우 FHIR 서비스에 만들어집니다. FHIR 리소스가 이미 있는 경우 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

디바이스 및 FHIR 대상 매핑을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [디바이스 매핑](how-to-use-device-mapping-iot.md)

> [!div class="nextstepaction"]
> [FHIR 대상 매핑](how-to-use-fhir-mapping-iot.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
