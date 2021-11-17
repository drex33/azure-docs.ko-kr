---
title: 에이전트 기반 솔루션 아키텍처란
description: Microsoft Defender for IoT 에이전트 기반 아키텍처 및 정보 흐름에 대해 알아봅니다.
ms.topic: overview
ms.date: 11/09/2021
ms.openlocfilehash: 4bedabced290c16e9172bee58d0f68a995bb5ebd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331607"
---
# <a name="what-is-agent-based-solution-for-device-builders"></a>디바이스 빌더를 위한 에이전트 기반 솔루션이란

이 문서에서는 Defender for IoT 에이전트 기반 솔루션의 기능적 시스템 아키텍처를 설명합니다. Microsoft Defender for IoT는 사용자 환경의 요구 사항에 맞는 두 가지 기능 세트, 조직을 위한 에이전트 없는 솔루션 및 디바이스 빌더를 위한 에이전트 기반 솔루션을 제공합니다.

## <a name="iot-hub-built-in-security"></a>IoT 허브 기본 제공 보안

Defender for IoT는 새로 생성되는 모든 IoT Hub에서 기본적으로 사용하도록 설정됩니다. Defender for IoT는 디바이스에 에이전트를 설치하지 않고도 실시간 모니터링, 권장 사항 및 경고를 제공하며, 기록된 IoT Hub 메타데이터에 대한 고급 분석을 사용하여 현장 디바이스와 IoT 허브를 분석하고 보호합니다. 

## <a name="defender-for-iot-micro-agent"></a>Defender for IoT 마이크로 에이전트 

Defender for IoT 마이크로 에이전트는 강력한 보안과 디바이스 동작에 대한 심도 있는 가시성을 제공합니다. 디바이스에서 원시 보안 이벤트를 수집, 집계 및 분석합니다. 원시 보안 이벤트에는 IP 연결, 프로세스 만들기, 사용자 로그인 및 기타 보안 관련 정보가 포함될 수 있습니다. 또한 Defender for IoT 디바이스 에이전트는 네트워크 처리량이 많아지지 않도록 이벤트 집계를 처리합니다. 이 에이전트는 고급 사용자 지정이 가능하므로 가장 빠른 SLA에서 중요한 정보만 보내거나 광범위한 보안 정보 및 컨텍스트를 더 큰 세그먼트로 집계하여 서비스 비용을 줄이는 등의 특정 작업에 사용할 수 있습니다.

디바이스 에이전트 및 기타 애플리케이션은 **Azure send security message SDK** 를 사용하여 Azure IoT 허브에 보안 정보를 보냅니다. IoT 허브는 이 정보를 받아서 Defender for IoT에 전달합니다.

Defender for IoT 서비스를 사용하도록 설정하면 IoT 허브는 이렇게 전달된 데이터 외에도 모든 내부 데이터를 Defender for IoT로 보내 분석합니다. 이 데이터에는 디바이스-클라우드 작업 로그, 디바이스 ID 및 허브 구성이 포함됩니다. 이 모든 정보는 Defender for IoT 분석 파이프라인을 만드는 데 도움이 됩니다.

또한 Defender for IoT 분석 파이프라인은 Microsoft 및 Microsoft 파트너의 다양한 소스에서 다른 위협 인텔리전스 스트림을 수신합니다. Defender for IoT 전체 분석 파이프라인은 서비스에서 만들어진 모든 고객 구성(예: 사용자 지정 경고 및 send security message SDK 사용)과 함께 작동합니다.

분석 파이프라인을 사용하면 Defender for IoT는 모든 정보 스트림을 결합하여 실행 가능한 권장 사항 및 경고를 생성합니다. 파이프라인에는 보안 연구원 및 전문가가 만든 사용자 지정 규칙과 표준 디바이스 동작 및 위험 분석의 편차를 검색하는 기계 학습 모델이 모두 포함됩니다.

Defender for IoT 권장 사항 및 경고(분석 파이프라인 출력)는 각 고객의 Log Analytics 작업 영역에 기록됩니다. 작업 영역과 경고 및 권장 사항에 원시 이벤트를 포함하면 탐지된 의심스러운 활동의 정확한 세부 정보를 사용하여 심층적인 조사와 쿼리를 수행할 수 있습니다.

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="마이크로 에이전트 아키텍처":::

## <a name="next-steps"></a>다음 단계

[Microsoft Defender for IoT 에이전트 질문과 대답](resources-agent-frequently-asked-questions.md)을 확인하세요.
