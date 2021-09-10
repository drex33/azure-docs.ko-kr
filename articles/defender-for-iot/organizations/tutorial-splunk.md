---
title: Azure Defender for IoT와 Splunk 통합
description: 이 자습서에서는 Splunk를 Azure Defender for IoT와 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 08/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: 00d4f5c5c432a4b5cccdfbfb83fe81c2844d68e8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786404"
---
# <a name="tutorial-integrate-splunk-with-azure-defender-for-iot"></a>자습서: Azure Defender for IoT와 Splunk 통합

이 자습서를 사용하면 Splunk를 Azure Defender for IoT와 통합하고 사용하는 방법을 알아볼 수 있습니다.

Defender for IoT는 에이전트, 규칙 또는 서명, 전문 기술 또는 환경에 대한 사전 지식 없이 1시간 이내에 ICS 디바이스, 취약성 및 위협에 대한 즉각적인 통찰력을 제공하는 특허 받은 ICS 인식 자체 학습 엔진을 통해 IIoT, ICS 및 SCADA 위험을 완화합니다.

OT 네트워크의 보안 및 복원력에 대한 가시성 부족을 해결하기 위해 Defender for IoT는 Defender for IoT와 Splunk 간 기본 통합인 Splunk용 Defender for IoT, IIoT 및 ICS 위협 모니터링 애플리케이션을 개발하여 IT 및 OT 보안에 대한 통합 접근 방식을 구현했습니다.

이 애플리케이션은 SOC 분석가에게 산업 환경에 배포된 특수한 OT 프로토콜 및 IIoT 디바이스에 대한 다차원 가시성을 제공하며 ICS 인식 동작 분석으로 의심스러운 동작 또는 변칙적인 동작을 신속하게 검색합니다. 또한 이 애플리케이션을 통해 하나의 회사 SOC 내에서 IT 및 OT 인시던트에 모두 대응할 수 있습니다. 이는 스마트 머신, 실시간 인텔리전스 같은 새로운 IIoT 이니셔티브를 지원하기 위해 IT와 OT가 지속적으로 통합되고 있다는 점을 고려하면 중요한 발전입니다.

Splunk 애플리케이션을 로컬로 설치하거나 클라우드에서 실행할 수 있습니다. Defender for IoT와 Splunk 통합은 두 배포를 모두 지원합니다.

> [!Note]
> CyberX에 대한 참조는 Azure Defender for IoT를 확인하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Splunk에서 Defender for IoT 애플리케이션 다운로드
> * Splunk에 Defender for IoT 경고 전송

## <a name="prerequisites"></a>필수 구성 요소

### <a name="version-requirements"></a>버전 요구 사항

애플리케이션을 실행하려면 다음 버전이 필요합니다.

- Defender for IoT 버전 2.4 이상.

- Splunkbase 버전 11 이상.

- Splunk Enterprise 버전 7.2 이상.

### <a name="splunk-permission-requirements"></a>Splunk 사용 권한 요구 사항

다음 Splunk 사용 권한이 필요합니다.

- ‘관리자’ 수준 사용자 역할이 있는 모든 사용자

## <a name="download-the-defender-for-iot-application-in-splunk"></a>Splunk에서 Defender for IoT 애플리케이션 다운로드

Splunk 내에서 Defender for IoT 애플리케이션에 액세스하려면 Splunk 애플리케이션 스토어에서 애플리케이션을 다운로드해야 합니다.

**Splunk에서 Defender for IoT 애플리케이션에 액세스하려면** 다음을 수행합니다.

1. [Splunkbase](https://splunkbase.splunk.com/) 애플리케이션 스토어로 이동합니다.

1. `CyberX ICS Threat Monitoring for Splunk`를 검색합니다.

1. Splunk 애플리케이션용 CyberX ICS 위협 모니터링을 선택합니다.

1. **다운로드하려면 로그인 단추** 를 선택합니다.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Splunk에 Defender for IoT 경고 전송

Defender for IoT 경고는 광범위한 보안 이벤트에 대한 정보를 제공합니다. 이러한 이벤트는 다음과 같습니다.

- 학습된 기준 네트워크 활동의 편차.

- 맬웨어 검색.

- 의심스러운 작업 변경 내용에 따른 검색.

- 네트워크 이상.

- 프로토콜 사양의 프로토콜 편차.

    :::image type="content" source="media/tutorial-splunk/address-scan.png" alt-text="검색 화면.":::

Splunk 서버로 경고를 전송하도록 Defender for IoT를 구성할 수도 있습니다. 경고 정보는 Splunk Enterprise 대시보드에 표시됩니다.

:::image type="content" source="media/tutorial-splunk/alerts-and-details.png" alt-text="모든 경고 및 세부 정보를 봅니다." lightbox="media/tutorial-splunk/alerts-and-details-expanded.png":::

Defender for IoT에서 Splunk 서버로 경고 정보를 보내려면 전달 규칙을 만들어야 합니다.

**전달 규칙을 만들려면** 다음을 수행합니다.

1. 센서에 로그인하고 왼쪽 창에서 **전달** 을 선택합니다.

    :::image type="content" source="media/tutorial-splunk/forwarding.png" alt-text="파란색 단추 경고 전달 만들기를 선택합니다.":::

1. **전달 규칙 만들기** 를 선택합니다.

1. **전달 규칙 만들기** 창에서 규칙 매개 변수를 정의합니다.

    :::image type="content" source="media/tutorial-splunk/forwarding-rule.png" alt-text="전달 규칙을 만듭니다." lightbox="media/tutorial-splunk/forwarding-rule-expanded.png":::

    | 매개 변수 | Description |
    |--|--|
    | **이름** | 전달 규칙 이름입니다. |
    | **심각도 선택** | 전달하려는 최소 보안 수준 인시던트입니다. 예를 들어 경미를 선택하면 경미한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. |
    | **프로토콜** | 기본적으로 모든 프로토콜이 선택되어 있습니다. 특정 프로토콜을 선택하려면 **특정** 을 선택하고 이 규칙이 적용되는 프로토콜을 선택합니다. |
    | **엔진** | 기본적으로 모든 보안 엔진이 포함됩니다. 이 규칙이 적용되는 특정 보안 엔진을 선택하려면 **특정** 을 선택하고 엔진을 선택합니다. |
    | **시스템 알림** | 센서 온라인/오프라인 상태를 전달합니다. 이 옵션은 중앙 관리자에 로그인한 경우에만 사용할 수 있습니다. |

1. **작업** 을 선택한 다음, **Send to Splunk Server**(Splunk 서버로 보내기)를 선택합니다.

1. 다음 Splunk 매개 변수를 입력합니다.

    :::image type="content" source="media/tutorial-splunk/parameters.png" alt-text="이 화면에 입력해야 하는 Splunk 매개 변수입니다." lightbox="media/tutorial-splunk/parameters-expanded.png":::

    | 매개 변수 | Description |
    |--|--|
    | **Host** | Splunk 서버 주소 |
    | **포트** | 8089 |
    | **사용자 이름** | Splunk 서버 사용자 이름 |
    | **암호** | Splunk 서버 암호 |

1. **제출** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Splunk 통합을 시작하는 방법을 알아보았습니다. [Azure Defender for IoT와 ServiceNow를 통합](tutorial-servicenow.md)하는 방법을 계속 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Defender for IoT와 ServiceNow 통합](tutorial-servicenow.md)