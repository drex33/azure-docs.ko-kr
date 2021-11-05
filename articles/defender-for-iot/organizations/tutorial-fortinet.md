---
title: Azure Defender for IoT와 Fortinet 통합
description: 이 자습서에서는 Fortinet을 Azure Defender for IoT와 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 10/25/2021
ms.custom: template-tutorial
ms.openlocfilehash: 81bbedf86c3559b8f668dcfcf26cb0d9dab21af0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070910"
---
# <a name="tutorial-integrate-fortinet-with-azure-defender-for-iot"></a>자습서: Azure Defender for IoT와 Fortinet 통합

이 자습서를 사용하면 Fortinet을 Azure Defender for IoT와 통합하고 사용하는 방법을 알아볼 수 있습니다.

Azure Defender for IoT는 ICS 디바이스, 취약성, 위협에 대한 즉각적인 인사이트를 제공하는 ICS 인식 자체 학습 엔진을 통해 IIoT, ICS 및 SCADA 위험을 완화합니다.  Defender for IoT는 에이전트, 규칙, 서명, 특수 기술 또는 환경에 대한 사전 지식에 의존하지 않고 위험을 완화합니다.

Defender for IoT 및 Fortinet은 IoT 및 ICS 네트워크에 대한 공격을 감지하여 차단하는 기술 파트너 관계를 구축했습니다.

Fortinet 및 Azure Defender for IoT는 다음을 방지합니다.

- PLC(프로그래밍 가능한 논리 컨트롤러)에 대한 권한 없는 변경

- 기본 프로토콜을 통해 ICS 및 IoT 디바이스를 조작하는 맬웨어

- 데이터 수집의 정찰 도구.

- 잘못된 또는 악의적인 공격자에 의해 발생하는 프로토콜 위반

Defender for IoT는 IoT 및 ICS 네트워크에서 비정상적인 동작을 감지하고 다음과 같이 FortiGate 및 FortiSIEM에 해당 정보를 제공합니다.

- **가시성:** Defender for IoT에서 제공하는 정보는 FortiSIEM 관리자에게 이전에 볼 수 없었던 IoT 및 ICS 네트워크에 대한 가시성을 제공합니다.

- **악의적인 공격 차단:** FortiGate 관리자는 Defender for IoT에서 검색한 정보를 사용하여 악의적인 동작이 무차별 행위자 또는 잘못 구성된 디바이스에 의해 발생하는지와 관계없이 프로덕션, 수익 또는 사용자에게 손상이 발생하기 전에 비정상 동작을 중지하는 규칙을 만들 수 있습니다.

FortiSIEM 및 Fortinet의 멀티벤더 보안 인시던트 및 이벤트 관리 솔루션은 스케일링 가능한 단일 솔루션에 가시성, 상관 관계, 자동 대응 및 수정 기능을 제공합니다.

비즈니스 서비스 보기를 사용하면 네트워크 및 보안 작업 관리의 복잡성이 감소하여 리소스를 확보하고 위반 감지가 향상됩니다. FortiSIEM은 위반이 발생하기 전에 이를 중단시킬 수 있도록 기계 학습을 적용하는 동안 교차 상관 관계를 제공하고, 대응을 개선하기 위해 UEBA를 제공합니다.

이 자습서에서는 다음 방법에 관해 알아봅니다.

> [!div class="checklist"]
> - Fortinet에서 API 키 만들기
> - 맬웨어 관련 경고를 차단하도록 전달 규칙 설정
> - 의심스러운 경고 소스 차단
> - FortiSIEM에 Defender for IoT 경고 전송
> - Fortigate 방화벽을 사용하여 악의적인 소스 차단

아직 Azure 계정이 없는 경우 [지금 Azure 체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 수행하기 위한 필수 구성 요소는 없습니다.

## <a name="create-an-api-key-in-fortinet"></a>Fortinet에서 API 키 만들기

API(애플리케이션 프로그래밍 인터페이스) 키는 고유하게 생성된 코드로써, API에서 애플리케이션 또는 애플리케이션에 대한 액세스 권한을 요청하는 사용자를 식별할 수 있습니다. Azure Defender for IoT와 Fortinet이 올바르게 통신하려면 API 키가 필요합니다.

**Fortinet에서 API 키를 만들려면** 다음을 수행합니다.

1. FortiGate에서 **시스템** > **관리자 프로필** 로 이동합니다.

1. 다음 권한을 사용하여 새 프로필을 만듭니다.

    | 매개 변수 | 선택 영역 |
    |--|--|
    | **보안 패브릭** | 없음 |
    | **Fortiview** | 없음 |
    | **사용자 및 디바이스** | 없음 |
    | **방화벽** | 사용자 지정 |
    | **정책** | 읽기/쓰기 |
    | **주소** | 읽기/쓰기  |
    | **서비스** | 없음 |
    | **일정** | 없음 |
    | **로그 및 보고서** | 없음 |
    | **Network** | 없음 |
    | **System** | 없음 |
    | **보안 프로필** | 없음 |
    | **VPN** | 없음 |
    | **WAN 옵션 및 캐시** | 없음 |
    | **WiFi 및 스위치** | 없음 |

1. **시스템** > **관리자** 로 이동하고, 다음 필드를 사용하여 새 **REST API 관리자** 를 만듭니다.

    | 매개 변수 | 설명 |
    | --------- | ----------- |
    | **사용자 이름** | 전달 규칙 이름을 입력합니다. |
    | **설명** | 전달하려는 최소 보안 수준 인시던트를 입력합니다. 예를 들어 **Minor** 를 선택하면 사소한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. |
    | **관리자 프로필** | 드롭다운 목록에서 이전 단계에서 정의한 프로필 이름을 선택합니다. |
    | **PKI 그룹** | 스위치를 **사용 안 함** 으로 전환합니다. |
    | **CORS 허용 원본** | 스위치를 **사용** 으로 전환합니다. |
    | **신뢰할 수 있는 호스트로 로그인 제한** | 센서의 IP 주소와 FortiGate에 연결할 관리 콘솔을 추가합니다. |

API 키가 생성되면 저장해 둡니다. 다시 제공되지 않습니다.

:::image type="content" source="media/tutorial-fortinet/api-key.png" alt-text="새 API 키를 자동으로 생성하는 설명의 스크린샷":::

## <a name="set-a-forwarding-rule-to-block-malware-related-alerts"></a>맬웨어 관련 경고를 차단하도록 전달 규칙 설정

FortiGate 방화벽을 사용하여 의심스러운 트래픽을 차단할 수 있습니다.

**맬웨어 관련 경고를 차단하도록 전달 규칙을 설정하려면** 다음을 수행합니다.

1. Azure Defender for IoT 관리 콘솔에 로그인합니다.

1. 왼쪽 창에서 **전달** 을 선택합니다.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="센서의 [전달] 창 옵션을 보여주는 스크린샷":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

1. **전달 규칙 만들기** 를 선택하고 다음 규칙 매개 변수를 정의합니다.

    | 매개 변수 | 설명 |
    | --------- | ----------- |
    | **이름** | 의미 있는 전달 규칙 이름을 입력합니다. |
    | **심각도 선택** | 드롭다운 메뉴에서 전달할 최소 보안 수준 인시던트를 선택합니다. 예를 들어 **Minor** 를 선택하면 사소한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. |
    | **프로토콜** | 특정 프로토콜을 선택하려면 **특정** 을 선택하고 이 규칙이 적용되는 프로토콜을 선택합니다. 기본적으로 모든 프로토콜이 선택되어 있습니다. |
    | **엔진** | 이 규칙이 적용되는 특정 보안 엔진을 선택하려면 **특정** 을 선택하고 엔진을 선택합니다. 기본적으로 모든 보안 엔진이 포함됩니다. |
    | **시스템 알림** | 센서의 *온라인* 및 *오프라인* 상태를 전달합니다. 이 옵션은 온-프레미스 관리 콘솔에 로그인한 경우에만 사용할 수 있습니다. |

1. [작업] 섹션에서 **추가** 를 선택한 다음, 드롭다운 메뉴에서 **FortiGate로 보내기** 를 선택합니다.

    :::image type="content" source="media/tutorial-fortinet/fortigate.png" alt-text="[전달 규칙 만들기] 창의 [작업 추가] 섹션을 보여주는 스크린샷":::

1. FortiGate 전달 규칙을 구성하려면 다음 매개 변수를 설정합니다.

    :::image type="content" source="media/tutorial-fortinet/configure.png" alt-text="[전달 규칙 만들기] 창의 구성을 보여주는 스크린샷":::

    | 매개 변수 | Description |
    |--|--|
    | **Host** | FortiGate 서버 IP 주소를 입력합니다. |
    | **API 키** | FortiGate에서 만든 [API 키](#create-an-api-key-in-fortinet)를 입력합니다. |
    | **들어오는 인터페이스** | 들어오는 방화벽 인터페이스 포트를 입력합니다. |
    | **나가는 인터페이스** | 나가는 방화벽 인터페이스 포트를 입력합니다. |
    | **구성**| FortiGate 방화벽에서 의심스러운 소스를 차단하도록 다음 옵션에 **√** 표시가 있는지 확인합니다. <br> - **잘못된 함수 코드 차단**: 프로토콜 위반 - ICS 프로토콜 사양을 위반하는 잘못된 필드 값(잠재적 악용) <br /> - **권한이 없는 PLC 프로그래밍/펌웨어 업데이트 차단**: 권한 없는 PLC 변경 <br /> - **권한 없는 PLC 중지 차단**: PLC 중단(가동 중지 시간) <br> - **맬웨어 관련 경고 차단**: 산업용 맬웨어 시도(TRITON, NotPetya 등) 차단 <br> -  **(선택 사항)** **자동 차단** 옵션을 선택할 수 있습니다. [자동 차단]을 선택하면 차단이 자동으로, 즉시 실행됩니다. <br /> - **권한 없는 검색 차단**: 권한 없는 검색(잠재적 정찰) |

1. **제출** 을 선택합니다.

## <a name="block-the-source-of-suspicious-alerts"></a>의심스러운 경고 소스 차단

추가 발생을 방지하기 위해 의심스러운 경고의 소스를 차단할 수 있습니다.

**의심스러운 경고의 소스를 차단하려면** 다음을 수행합니다.

1. 관리 콘솔에 로그인하고, 왼쪽 메뉴에서 **경고** 를 선택합니다.

1. Fortinet 통합과 관련된 경고를 선택합니다.

1. 의심스러운 소스를 자동으로 차단하려면 **소스 차단** 을 선택합니다.

    :::image type="content" source="media/tutorial-fortinet/block-source.png" alt-text="[경고] 창의 스크린샷":::

1. 확인 대화 상자에서 **확인** 을 선택합니다.

## <a name="send-defender-for-iot-alerts-to-fortisiem"></a>FortiSIEM에 Defender for IoT 경고 전송

Defenders for IoT 경고는 다음을 비롯한 광범위한 보안 이벤트에 대한 정보를 제공합니다.

- 학습된 기준 네트워크 활동의 편차

- 맬웨어 검색

- 의심스러운 작업 변경 내용에 따른 검색

- 네트워크 이상

- 프로토콜 사양의 프로토콜 편차

Defender for IoT에서 경고 정보를 Analytics 창에 표시하는 FortiSIEM 서버에 경고를 보내도록 구성할 수 있습니다.

:::image type="content" source="media/tutorial-fortinet/analytics.png" alt-text="Analytics 창의 스크린샷.":::

각 Defender for IoT 경고는 FortiSIEM 쪽에서 다른 구성 없이 구문 분석되고, 보안 이벤트로 FortiSIEM에 표시됩니다. 기본적으로 다음과 같은 이벤트 정보가 표시됩니다.

:::image type="content" source="media/tutorial-fortinet/event-detail.png" alt-text="[이벤트 세부 정보] 창에서 이벤트 세부 정보를 보는 스크린샷":::

그런 다음, Defender for IoT의 전달 규칙을 사용하여 FortiSIEM에 경고 정보를 보낼 수 있습니다.

**Defender for IoT의 전달 규칙을 사용하여 FortiSIEM에 경고 정보를 보내려면** 다음을 수행합니다.

1. 센서 또는 관리 콘솔 왼쪽 창에서 **전달** 을 선택합니다.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="[전달] 창에서 전달 규칙을 보는 스크린샷":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

2. **전달 규칙 만들기** 를 선택하고 규칙의 매개 변수를 정의합니다.

    | 매개 변수 | 설명 |
    |--|--|
    | **이름** | 의미 있는 전달 규칙 이름을 입력합니다. |
    | **심각도 선택** | 전달할 최소 보안 수준 인시던트를 선택합니다. 예를 들어 **Minor** 를 선택하면 사소한 경고 및 이 심각도 수준 이상의 모든 경고가 전달됩니다. |
    | **프로토콜** | 특정 프로토콜을 선택하려면 **특정** 을 선택하고 이 규칙이 적용되는 프로토콜을 선택합니다. 기본적으로 모든 프로토콜이 선택되어 있습니다. |
    | **엔진** | 이 규칙이 적용되는 특정 보안 엔진을 선택하려면 **특정** 을 선택하고 엔진을 선택합니다. 기본적으로 모든 보안 엔진이 포함됩니다. |
    | **시스템 알림** | 센서의 *온라인* 및 *오프라인* 상태를 전달합니다. 이 옵션은 온-프레미스 관리 콘솔에 로그인한 경우에만 사용할 수 있습니다. |

3. [작업] 섹션에서 **FortiSIEM으로 보내기** 를 선택합니다.

    :::image type="content" source="media/tutorial-fortinet/forward-rule.png" alt-text="전달 규칙을 만들고 Fortinet으로 보내도록 선택하는 스크린샷":::

4. FortiSIEM 서버 정보를 입력합니다.

    :::image type="content" source="media/tutorial-fortinet/details.png" alt-text="전달 규칙에 FortiSIEm 세부 정보를 추가하는 스크린샷":::

    | 매개 변수 | Description |
    | --------- | ----------- |
    | **Host** | FortiSIEM 서버 IP 주소를 입력합니다. |
    | **포트** | FortiSIEM 서버 포트를 입력합니다. |
    | **표준 시간대** | 경고 검색에 대한 타임스탬프입니다. |

5. **제출** 을 선택합니다.

## <a name="block-a-malicious-source-using-the-fortigate-firewall"></a>Fortigate 방화벽을 사용하여 악의적인 소스 차단

Defender for IoT의 경고를 사용하여 FortiGate 방화벽에서 악성 소스를 자동으로 차단하도록 정책을 설정할 수 있습니다.

:::image type="content" source="media/tutorial-fortinet/firewall.png" alt-text="FortiGate 방화벽 창 보기의 스크린샷":::

예를 들어 다음 경고는 악성 소스를 차단할 수 있습니다.

:::image type="content" source="media/tutorial-fortinet/suspicion.png" alt-text="NotPetya 맬웨어 의심 창의 스크린샷":::

**악성 소스를 차단하도록 FortiGate 방화벽 규칙을 설정하려면** 다음을 수행합니다.

1. FortiGate에서 [API 키를 만듭니다](#create-an-api-key-in-fortinet).

1. Defender for IoT 센서 또는 관리 콘솔에 로그인하고, **전달** 을 선택하고, [맬웨어 관련 경고를 차단하는 전달 규칙을 설정](#set-a-forwarding-rule-to-block-malware-related-alerts)합니다.

1. Defender for IoT 센서 또는 관리 콘솔에서 **경고** 를 선택하고 [악성 소스를 차단](#block-a-malicious-source-using-the-fortigate-firewall)합니다.

1. FortiGage **관리자** 창으로 이동한 다음, 차단한 악성 소스 주소를 찾습니다.

   :::image type="content" source="media/tutorial-fortinet/administrator.png" alt-text="FortiGate 관리자 창 보기의 스크린샷":::

   차단 정책이 자동으로 만들어지고 FortiGate IPv4 정책 창에 표시됩니다.

   :::image type="content" source="media/tutorial-fortinet/policy.png" alt-text="FortiGate IPv4 정책 창 보기의 스크린샷":::

1. 정책을 선택하고 [이 정책 사용]을 켜는 위치로 전환합니다.

   :::image type="content" source="media/tutorial-fortinet/edit.png" alt-text="FortiGate IPv4 정책 편집 보기의 스크린샷":::

    | 매개 변수 | 설명|
    |--|--|
    | **이름** | 정책 이름입니다. |
    | **들어오는 인터페이스** | 트래픽에 대한 인바운드 방화벽 인터페이스입니다. |
    | **나가는 인터페이스** | 트래픽에 대한 아웃바운드 방화벽 인터페이스입니다. |
    | **원본** | 트래픽의 원본 주소입니다. |
    | **대상** | 트래픽의 대상 주소입니다. |
    | **일정** | 새로 정의된 규칙의 발생입니다. 예들 들어 `always`입니다. |
    | **서비스** | 프로토콜 또는 트래픽에 대한 특정 포트입니다. |
    | **동작** | 방화벽에서 수행할 작업입니다. |

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Fortinet 통합을 시작하는 방법을 알아보았습니다. 계속하여 Palo Alto 통합에 대해 알아보세요.

> [!div class="nextstepaction"]
> [다음 단계 단추](./tutorial-palo-alto.md)
