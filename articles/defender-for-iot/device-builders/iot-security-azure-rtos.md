---
title: Azure RTOS용 Defender-IoT-micro-agent 개요
description: Azure Defender for IoT의 일부로 Azure RTOS 지원 및 구현을 위한 Defender-IoT-마이크로 에이전트에 대해 자세히 알아보세요.
ms.topic: conceptual
ms.date: 07/01/2021
ms.openlocfilehash: cf905eb9cff7840eeaac70e731421ba2252193e6
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303655"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos"></a>개요: Azure RTOS 용 Defender-IoT-마이크로 에이전트

Azure Defender for IoT 마이크로 모듈은 Azure RTOS를 사용하는 디바이스에 포괄적이고 간단한 보안 솔루션을 제공합니다. Azure Defender for IoT 마이크로 에이전트는 실시간 운영 체제(RTOS) 디바이스에서 일반적인 위협에 대한 범위와 잠재적 악성에 대한 보장을 제공합니다. Azure RTOS는 이제 기본 제공되는 Azure IoT Defender-마이크로 에이전트와 함께 제공됩니다.

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="IoT Azure RTOS 용 Defender의 시각화.":::


Azure RTOS의 마이크로 모듈은 다음과 같은 기능을 제공합니다.

- 악의적인 네트워크 활동 감지
- 사용자 지정 경고 기반 디바이스 동작 기준선 지정
- 강화된 디바이스 보안 예방

## <a name="detect-malicious-network-activities"></a>악의적인 네트워크 활동 감지

각 디바이스의 인바운드 및 아웃바운드 네트워크 활동이 모니터링됩니다. IPv4 및 IPv6에서 지원되는 프로토콜은 TCP, UDP 및 ICMP입니다. Defender for IoT는 Microsoft 위협 인텔리전스 피드에 대해 이러한 각 네트워크 활동을 검사합니다. 피드는 전 세계에서 수집된 수백만 개의 고유한 위협 지표로 실시간으로 업데이트됩니다.

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>사용자 지정 경고를 기반으로 하는 디바이스 동작 기준 지정

기준선을 통해 디바이스를 보안 그룹으로 클러스터링하고 각 그룹의 예상되는 동작을 정의할 수 있습니다. IoT 디바이스는 일반적으로 잘 정의되고 제한된 시나리오에서 작동하도록 설계되었으므로, 매개 변수 집합을 사용하여 예상되는 동작을 정의하는 기준선을 쉽게 만들 수 있습니다. 기준선의 모든 편차는 경고를 트리거합니다.

## <a name="improve-your-device-security-hygiene"></a>강화된 디바이스 보안 예방

권장되는 인프라 Defender for IoT를 사용하면 디바이스의 보안 상태에 영향을 줄 수 있는 환경에서 발생하는 문제에 대한 정보와 이해를 얻을 수 있습니다. 취약한 IoT 디바이스 보안 상태에서 변경되지 않은 상태로 유지되는 경우 잠재적인 공격이 성공하도록 허용할 수 있습니다. 보안은 항상 모든 조직 내의 가장 약한 링크로 측정됩니다.

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS 디바이스 보호 시작하기

Azure RTOS용 Defender-IoT-마이크로 에이전트는 디바이스에 무료 다운로드로 제공됩니다. Defender for IoT 클라우드 서비스는 Azure 구독당 30일 평가판으로 사용 가능합니다. 시작하려면 [Azure RTOS 용 Defender-IoT-마이크로 에이전트](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/device-builders/iot-security-azure-rtos.md)를 다운로드합니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure RTOS 용 Defender-IoT-마이크로 에이전트에 대해 알아보았습니다. Defender-IoT-마이크로 에이전트에 대해 자세히 알아보고 시작하려면 다음 문서를 참조하세요.

- [Azure RTOS IoT Defender-IoT-micro-agent 개념](concept-rtos-security-module.md)
- [빠른 시작: Azure RTOS IoT Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
