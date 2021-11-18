---
title: IoT 커넥터용 이란? -Azure 의료 Api
description: 이 문서에서는 FHIR 서비스에 IoMT 데이터를 저장 하기 전에 IoT 커넥터에서 수행 하는 단계를 알아봅니다.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: b74b3c1aefe2c7dd05421385e672385196aab15e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719432"
---
# <a name="what-is-iot-connector"></a>IoT 커넥터용 이란?

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

IoT 커넥터는 IoMT (의료 사물 인터넷) 장치에서 데이터를 수집 하는 기능을 제공 하는 Azure 의료 Api의 선택적 기능입니다.

이 문서에서는 IoT 커넥터의 데이터 흐름에 대 한 개요를 제공 합니다. IoT connector 서비스 내의 여러 데이터 처리 단계에 대해 설명 하 고, 장치 데이터를 신속한 의료 상호 운용성 리소스 (FHIR&#174;) 기반 [관찰](https://www.hl7.org/fhir/observation.html) 리소스로 변환 합니다.

IoT 커넥터에서 수신 된 후 데이터를 이동 하는 다양 한 단계는 다음과 같습니다.

## <a name="ingest"></a>수집

수집은 IoT connector 서비스로 장치 데이터를 수신 하는 첫 번째 단계입니다. 장치 데이터에 대 한 수집 끝점은 Azure 이벤트 허브에서 호스팅됩니다. [Azure 이벤트 허브](../../event-hubs/index.yml) 플랫폼은 초당 수백만 개의 메시지를 받고 처리 하는 기능을 통해 높은 확장성과 처리량을 지원 합니다. 또한 장치 데이터를 처리 하는 동안 장치에서 대기할 필요가 없는 메시지를 IoT connector 서비스에서 비동기적으로 사용할 수 있습니다.

> [!NOTE]
> 이번에는 장치 데이터에 대해 JSON이 유일 하 게 지원 되는 형식입니다.

## <a name="normalize"></a>Normalize

정규화는 위의 Azure 이벤트 허브에서 장치 데이터를 검색 하 고 장치 매핑 템플릿을 사용 하 여 처리 하는 다음 단계입니다. 이 매핑 프로세스는 장치 데이터를 정규화 된 스키마로 변환 합니다. 정규화 프로세스는 이후 단계에서 데이터 처리를 간소화할 뿐만 아니라 하나의 입력 메시지를 여러 정규화 된 메시지로 프로젝션 하는 데도 제공 됩니다. 예를 들어 장치는 단일 메시지에서 본문 온도, 펄스 율, 혈압 및 respiration 요금에 대 한 여러 가지 중요 한 기호를 보낼 수 있습니다. 이 입력 메시지는 4 개의 별도의 FHIR 리소스를 만듭니다. 각 리소스는 서로 다른 중요 한 기호를 나타내며, 입력 메시지는 서로 다른 4 개의 정규화 된 메시지에 프로젝션 됩니다.

## <a name="group"></a>그룹

그룹은 이전 단계에서 사용할 수 있는 정규화 된 메시지를 세 가지 매개 변수를 사용 하 여 그룹화 하는 다음 단계입니다. 

* 디바이스 ID
* 측정 유형 
* 기간

장치 id 및 측정 유형 그룹화에서는 [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData) 측정 유형을 사용할 수 있습니다. 이 유형은 IoT 커넥터에서 생성 되는 관찰 리소스가 FHIR 서비스에 기록 되는 대기 시간을 제어 하는 반면, 기간 동안 FHIR에서 장치에 대 한 시간 기반 일련의 측정을 나타내는 간결한 방법을 제공 합니다.

> [!NOTE]
> 기간 값은 기본적으로 15 분 이며 미리 보기용으로 구성할 수 없습니다.

## <a name="transform"></a>변환

변환 단계에서 그룹화 정규화 된 메시지는 FHIR 대상 매핑 템플릿을 통해 처리 됩니다. 템플릿 유형과 일치 하는 메시지는 매핑을 통해 지정 된 대로 FHIR 기반 관찰 리소스로 변환 됩니다.

이 시점에서 연결 된 환자 리소스와 함께 장치 리소스는 메시지에 있는 장치 식별자를 사용 하 여 FHIR 서비스에서 검색 됩니다. 이러한 리소스는 생성 되는 관찰 리소스에 대 한 참조로 추가 됩니다.

> [!NOTE]
>FHIR 서비스에 대 한 부하를 줄이기 위해 모든 id 조회는 해결 된 후에 캐시 됩니다. 여러 환자를 사용 하 여 장치를 다시 사용 하려는 경우에는 메시지 페이로드에서 환자 및 송신 가상 장치 식별자에 해당 하는 가상 장치 리소스를 만드는 것이 좋습니다. 가상 장치를 실제 장치 리소스에 부모로 연결할 수 있습니다.

지정 된 장치 식별자에 대 한 장치 리소스가 FHIR 서비스에 없으면 생성 시 설정 된 확인 유형 값에 따라 결과가 달라 집니다. 조회로 설정 하면 특정 메시지는 무시 되 고 파이프라인은 다른 들어오는 메시지를 계속 처리 합니다. 만들기로 설정 된 경우 IoT connector 서비스는 FHIR 서비스에 대 한 장치 및 환자 리소스를 만듭니다.

## <a name="persist"></a>Persist

변환 단계에서 관찰 FHIR 리소스가 생성 되 면 리소스가 FHIR 서비스에 저장 됩니다. FHIR 리소스가 새 리소스인 경우 서비스에 생성 됩니다. FHIR 리소스가 이미 있으면 업데이트 됩니다.

## <a name="next-steps"></a>다음 단계

IoT 커넥터 매핑에 대 한 자세한 내용은 다음 가이드를 참조 하세요.

>[!div class="nextstepaction"]
>[장치 매핑을 사용 하는 방법](how-to-use-device-mappings.md)

>[!div class="nextstepaction"]
>[FHIR 대상 매핑을 사용 하는 방법](how-to-use-fhir-mappings.md)

(FHIR&#174;)는 [HL7](https://hl7.org/fhir/) 의 등록 상표 이며 HL7의 사용 권한과 함께 사용 됩니다.
