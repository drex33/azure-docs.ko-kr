---
title: Azure RTOS용 Defender-IoT-micro-agent 기본 제공/사용자 지정 가능한 경고 및 권장 사항
description: Azure IoT Defender-IoT-micro-agent -RTOS를 사용하는 보안 경고 및 권장 수정 사항에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: ad6cd708bfdb53068895abc72abb3092e82db818
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293557"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-security-alerts-and-recommendations-preview"></a>Azure RTOS용 Defender-IoT-micro-agent 보안 경고 및 권장 사항(미리 보기)

Azure RTOS용 Defender-IoT-micro-agent는 고급 분석 및 위협 인텔리전스를 사용하여 IoT 솔루션을 지속적으로 분석함으로써 잠재적인 악성 활동과 의심스러운 시스템 수정 사항을 경고해 줍니다. 또한 예상되는 디바이스 동작과 기준에 대한 지식을 바탕으로 사용자 지정 경고를 만들 수 있습니다.

Azure RTOS용 Defender-IoT-micro-agent는 잠재적 손상을 알려 주는 역할을 하므로, 조사하고 수정해야 합니다. Azure RTOS용 Defender-IoT-micro-agent 권장 사항은 수정 및 업데이트할 취약한 보안 상태를 식별합니다. 

이 문서에서는 기본 범위를 기반으로 트리거되고 예상 또는 기준 동작에 따라 고유한 값으로 사용자 지정할 수 있는, 기본 제공 경고 및 권장 사항의 목록을 찾을 수 있습니다. 

Defender for IoT 서비스에서 경고 사용자 지정 기능이 작동하는 방식에 대한 자세한 내용은 [사용자 지정 가능한 경고](concept-customizable-security-alerts.md)를 참조하세요. 다음 표에는 Azure RTOS용 Defender-IoT-micro-agent를 이용할 때 사용자 지정에서 사용 가능한 특정 경고와 권장 사항이 자세히 설명되어 있습니다. 

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-security-alerts"></a>Azure RTOS용 Defender-IoT-micro-agen에서 지원하는 보안 경고

### <a name="device-related-security-alerts"></a>디바이스 관련 보안 경고

|디바이스 관련 보안 경고 활동  |경고 이름  |
|---------|---------|
|IP 주소| 의심스러운 IP 주소와의 통신이 검색됨|
|X.509 디바이스 인증서 지문|X.509 디바이스 인증서 지문 불일치|
|X.509 인증서| X.509 인증서가 만료됨|
|SAS 토큰| 만료된 SAS 토큰|
|SAS 토큰| 잘못된 SAS 토큰 서명|

### <a name="iot-hub-related-security-alerts"></a>IoT Hub 관련 보안 경고

|IoT Hub 보안 경고 활동  |경고 이름  |
|---------|---------|
|인증서 추가    |  IoT Hub에 인증서를 추가하려다 실패한 시도가 검색됨       |
|진단 설정 추가 또는 편집    | IoT Hub의 진단 설정을 추가하거나 편집하려는 시도가 검색됨      |
|인증서 삭제    |  IoT Hub에서 인증서를 삭제하려다 실패한 시도가 검색됨       |
|진단 설정 삭제    |  IoT Hub에서 진단 설정을 삭제하려는 시도가 검색됨      |
|삭제된 인증서    | IoT Hub에서 인증서 삭제가 검색됨        |
|새 인증서     |  IoT Hub에 새 인증서 추가가 검색됨       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-customizable-alerts"></a>Azure RTOS용 Defender-IoT-micro-agent에서 지원하는 사용자 지정 경고

### <a name="device-related-customizable-alerts"></a>디바이스 관련 사용자 지정 가능 경고

|디바이스 관련 활동 |경고 이름  |
|---------|---------|
|활성 연결|활성 연결의 수가 허용 범위 내에 없음|
|**MQTT** 프로토콜의 클라우드-디바이스 메시지|**MQTT** 프로토콜의 클라우드-디바이스 메시지 수가 허용 범위 내에 없음|
|아웃바운드 연결| 허용되지 않은 IP에 대한 아웃바운드 연결|

### <a name="hub-related-customizable-alerts"></a>허브 관련 사용자 지정 가능 경고 

|허브 관련 활동  |경고 이름  |
|---------|---------|
|명령 큐 제거     |  명령 큐 제거의 수가 허용 범위를 벗어남       |
|**MQTT** 프로토콜의 클라우드-디바이스 메시지    |  **MQTT** 프로토콜의 클라우드-디바이스 메시지 수가 허용 범위를 벗어남       |
|**MQTT** 프로토콜의 디바이스-클라우드 메시지    | **MQTT** 프로토콜의 디바이스-클라우드 메시지 수가 허용 범위를 벗어남        |
|직접 메서드 호출     |  직접 메서드 호출의 수가 허용 범위를 벗어남       |
|**MQTT** 프로토콜에서 거부된 클라우드-디바이스 메시지     |   **MQTT** 프로토콜에서 거부된 클라우드-디바이스 메시지 수가 허용 범위를 벗어남      |
|쌍 모듈 업데이트     |  쌍 모듈 업데이트의 수가 허용 범위를 벗어남       |
|권한 없는 작업    |  권한 없는 작업의 수가 허용 범위를 벗어남       |

## <a name="defender-iot-micro-agent-for-azure-rtos-supported-recommendations"></a>Azure RTOS용 Defender-IoT-micro-agent에서 지원하는 권장 사항

### <a name="device-related-recommendations"></a>디바이스 관련 권장 사항

|디바이스 관련 활동  |권장 사항 이름 |
|---------|---------|
|인증 자격 증명    |  여러 디바이스에서 사용하는 동일한 인증 자격 증명       |

### <a name="hub-related-recommendations"></a>허브 관련 권장 사항

|IoT Hub 관련 활동  |권장 사항 이름 |
|---------|---------|
|IP 필터 정책   |  기본 IP 필터 정책을 **거부** 로 설정해야 함  |
|IP 필터 규칙| IP 필터 규칙에 넓은 IP 범위가 포함됨|
|진단 로그|IoT Hub에서 진단 로그를 사용하도록 설정하는 제안|

### <a name="all-defender-for-iot-alerts-and-recommendations"></a>모든 Defender for IoT 경고 및 권장 사항

모든 Defender for IoT 서비스 관련 경고 및 권장 사항의 전체 목록은 IoT [보안 경고](concept-security-alerts.md), IoT 보안 [권장 사항](concept-recommendations.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure RTOS용 Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
- [Azure RTOS용 Defender-IoT-micro-agent 구성 및 사용자 지정](how-to-azure-rtos-security-module.md)
- [Azure RTOS API용 Defender-IoT-micro-agent](azure-rtos-security-module-api.md) 참조
