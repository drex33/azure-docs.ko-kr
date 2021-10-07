---
title: Azure Defender for IoT와 Forescout 통합
description: 이 자습서에서는 Forescout를 Azure Defender for IoT와 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a930671e501940ce7f6d0d22036225e7539eb7f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701990"
---
# <a name="tutorial-integrate-forescout-with-azure-defender-for-iot"></a>자습서: Azure Defender for IoT와 Forescout 통합

> [!Note]
> CyberX에 대한 참조는 Azure Defender for IoT를 확인하세요.

이 자습서를 사용하면 Forescout를 Azure Defender for IoT와 통합하는 방법을 알아볼 수 있습니다.

Azure Defender for IoT는 ICS 및 IoT 사이버 보안 플랫폼을 제공합니다. Defender for IoT는 ICS 인식 위협 분석 및 기계 학습을 사용하는 유일한 플랫폼입니다. Defender for IoT는 다음을 제공합니다.

- 특성에 대한 광범위한 세부 정보와 함께 디바이스 환경인 ICS에 대한 즉각적인 인사이트.

- OT 프로토콜, 디바이스, 애플리케이션 및 해당 동작에 대한 심층적인 ICS 인식 정보

- 취약성 및 알려진 제로 데이 위협에 대한 즉각적인 통찰력

- 독점 분석을 통해 대상 ICS 공격의 가장 가능성이 높은 경로를 예측하는 자동화된 ICS 위협 모델링 기술

Forescout 통합을 사용하면 산업 및 중요 인프라 조직이 사이버 위협에 대한 검색, 조사, 조치를 수행하는 데 필요한 시간을 줄일 수 있습니다.

- Azure Defender for IoT의 OT 디바이스 인텔리전스를 사용하여 Forescout 정책 작업을 트리거함으로써 보안 주기를 닫습니다. 예를 들어 특정 프로토콜이 검색되거나 펌웨어 세부 정보가 변경될 때 자동으로 SOC 관리자에게 경고 메일을 보낼 수 있습니다.

- Defender for IoT 정보를 모니터링, 인시던트 관리, 디바이스 제어를 감독하는 다른 *Forescout eyeExtended* 모듈과 상호 연결합니다.

Defender for IoT와 Forescout 플랫폼의 통합은 IoT 및 OT 환경에 대한 중앙 집중화된 가시성, 모니터링, 제어를 제공합니다. 이러한 브리지 플랫폼을 통해 ICS 디바이스와 사일로화된 워크플로에 대한 자동 디바이스 가시성 및 관리를 수행할 수 있습니다. 이 통합은 SOC 분석가에게 산업 환경에 배포된 OT 프로토콜에 대한 다단계 가시성을 제공합니다. Azure Defender for IoT 독점 기술을 기반으로 하는 펌웨어, 디바이스 유형, 운영 체제, 위험 분석 점수와 같은 정보를 얻을 수 있습니다.

이 자습서에서는 다음 방법에 관해 알아봅니다.

> [!div class="checklist"]
> - 액세스 토큰 생성
> - Forescout 플랫폼 구성
> - 통신 확인
> - Forescout에서 디바이스 특성 보기
> - Forescout에서 Azure Defender for IoT 정책 만들기

아직 Azure 계정이 없는 경우 [지금 Azure 체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Defender for IoT 버전 2.4 이상

- Forescout 버전 8.0 이상

- Azure Defender for IoT 플랫폼에 대한 Forescout eyeExtend 모듈의 라이선스.

## <a name="generate-an-access-token"></a>액세스 토큰 생성

액세스 토큰을 사용하면 Defender for IoT에서 발견한 데이터에 외부 시스템이 액세스할 수 있습니다. 액세스 토큰을 사용하면 외부 REST API 및 SSL 연결을 통해 데이터를 사용할 수 있습니다. Azure Defender for IoT REST API에 액세스하기 위해 액세스 토큰을 생성할 수 있습니다.

Defender for IoT에서 Forescout로 통신하려면 Defender for IoT에서 액세스 토큰을 생성해야 합니다.

**액세스 토큰을 생성하려면** 다음을 수행합니다.

1. Forescout에서 쿼리할 Defender for IoT 센서에 로그인합니다.

1. **일반** 섹션에서 **시스템 설정** > **액세스 토큰** 을 선택합니다.

1. **Generate new token**(새 토큰 생성)을 탭합니다.

    :::image type="content" source="media/tutorial-forescout/generate-access-tokens-screen.png" alt-text="액세스 토큰 생성 화면의 스크린샷":::

1. **새 액세스 토큰** 대화 상자에 토큰 설명을 입력합니다.

   :::image type="content" source="media/tutorial-forescout/new-forescout-token.png" alt-text="새 액세스 토큰":::

1. **다음** 을 선택합니다. 그러면 토큰이 대화 상자에 표시됩니다.

   > [!NOTE]
   > 토큰을 안전한 곳에 기록해 둡니다. Forescout 플랫폼을 구성할 때 필요합니다.

1. **마침** 을 선택합니다.

   :::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="토큰 추가 완료":::

## <a name="configure-the-forescout-platform"></a>Forescout 플랫폼 구성

이제 Defender for IoT 센서와 통신하도록 Forescout 플랫폼을 구성할 수 있습니다.

**Forescout 플랫폼을 구성하려면** 다음을 수행합니다.

1. Forescout 플랫폼에서 **CyberX용 Forescout eyeExtend 모듈** 을 검색하여 설치합니다.

1. CounterACT 콘솔에 로그인합니다.

1. 도구 메뉴에서 **옵션** 을 클릭합니다.

1. **모듈** > **CyberX 플랫폼** 으로 이동합니다.

   :::image type="content" source="media/tutorial-forescout/settings-for-module.png" alt-text="Azure Defender for IoT 모듈 설정":::

1. [서버 주소] 필드에 Forescout 어플라이언스에서 쿼리할 Defender for IoT 센서의 IP 주소를 입력합니다.

1. [액세스 토큰] 필드에 앞에서 생성한 액세스 토큰을 입력합니다.

1. **적용** 을 선택합니다.

### <a name="change-sensors-in-forescout"></a>Forescout에서 센서 변경

Forescout 플랫폼을 만들고 다른 센서와 통신하게 하려면 Forescout 내의 구성을 변경해야 합니다.

**Forescout에서 센서를 변경하려면** 다음을 수행합니다.

1. 관련 Defender for IoT 센서에서 새 액세스 토큰을 만듭니다.

1. **Forescout 모듈** > **CyberX 플랫폼** 으로 이동합니다.

1. 두 필드에 표시된 정보를 삭제합니다.

1. 새 Defender for IoT 센서에 로그인하고 [새 액세스 토큰을 생성](#generate-an-access-token)합니다.

1. [서버 주소] 필드에 Forescout 어플라이언스에서 쿼리할 Defender for IoT 센서의 새 IP 주소를 입력합니다.

1. [액세스 토큰] 필드에 새 액세스 토큰을 입력합니다.

1. **적용** 을 선택합니다.

## <a name="verify-communication"></a>통신 확인

연결이 구성되었으면 두 플랫폼이 통신하는지 확인해야 합니다.

**두 플랫폼이 통신하는지 확인하려면** 다음을 수행합니다.

1. Defender for IoT 센서에 로그인합니다.

1. **시스템 설정** > **액세스 토큰** 으로 이동합니다.

[사용됨] 필드는 센서와 Forescout 어플라이언스 간의 연결이 작동하지 않을 때 경고를 보냅니다. **사용 불가** 가 표시되면 연결이 작동하지 않는 것입니다. **사용됨** 이 표시되면 이 토큰을 사용한 마지막 외부 호출이 수신된 시간을 나타냅니다.

:::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="토큰을 받았는지 확인합니다.":::

## <a name="view-device-attributes-in-forescout"></a>Forescout에서 디바이스 특성 보기

Defender for IoT를 Forescout과 통합하면 Defender for IoT가 발견한 다른 디바이스의 특성을 Forescout 애플리케이션에서 볼 수 있습니다.

다음 표에는 Forescout 애플리케이션을 통해 볼 수 있는 모든 특성이 나와 있습니다.

| 항목 | 설명 |
|--|--|
| **Azure Defender for IoT에서 권한 부여** | 네트워크 학습 기간 동안 네트워크에서 Defender for IoT에 의해 검색된 디바이스입니다. |
| **펌웨어** | 디바이스의 펌웨어 세부 정보입니다. 예를 들어, 모델 및 버전 세부 정보입니다. |
| **이름** | 서버의 이름입니다. |
| **운영 체제** | 디바이스의 운영 체제입니다. |
| **형식** | 디바이스의 유형입니다. 예를 들어 PLC, Historian 또는 엔지니어링 스테이션입니다. |
| **공급업체** | 디바이스의 공급업체입니다. 예를 들어 Rockwell Automation입니다. |
| **위험 수준** | Defender for IoT에서 계산된 위험 수준입니다. |
| **프로토콜** | 디바이스에 의해 생성된 트래픽에서 검색된 프로토콜입니다. |

**디바이스의 특성을 보려면** 다음을 수행합니다.

1. Forescout 플랫폼에 로그인한 후 **자산 인벤토리** 로 이동합니다.

   :::image type="content" source="media/tutorial-forescout/device-firmware-attributes-in-forescout.png" alt-text="펌웨어 특성 보기.":::

1. **CyberX 플랫폼** 을 선택합니다.

   :::image type="content" source="media/tutorial-forescout/vendor-attributes-in-forescout.png" alt-text="공급업체 특성 보기.":::

### <a name="view-more-details"></a>세부 정보 보기

디바이스의 특성을 본 후에는 Forescout 규정 준수 및 정책 정보와 같은 각 디바이스의 세부 정보를 볼 수 있습니다.

**추가 세부 정보를 보려면** 다음을 수행합니다.

1. Forescout 플랫폼에 로그인한 후 **자산 인벤토리** 로 이동합니다.

1. **CyberX 플랫폼** 을 선택합니다.

1. [디바이스 인벤토리 호스트] 섹션에서 디바이스를 마우스 오른쪽 단추로 클릭합니다. 추가 정보를 포함하는 호스트 세부 정보 대화 상자가 열립니다.

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Forescout에서 Azure Defender for IoT 정책 만들기

Forescout 정책을 사용하여 Defender for IoT에서 검색된 디바이스의 제어 및 관리를 자동화할 수 있습니다. 예제:

- 특정 펌웨어 버전이 검색되면 SOC 관리자에게 자동으로 메일을 보냅니다.

- 다른 SIEM 통합과 같은 인시던트 및 보안 워크플로의 추가 처리를 위해 특정 Defender for IoT 검색 디바이스를 Forescout 그룹에 추가합니다.

Forescout에서 Defender for IoT 조건부 속성을 사용하여 사용자 지정 정책을 만들 수 있습니다.

**Defender for IoT 속성에 액세스하려면** 다음을 수행합니다.

1. **정책 조건** > **속성 트리** 로 이동합니다.

1. [속성 트리]에서 CyberX 플랫폼 폴더를 확장합니다. Defender for IoT 다음 속성을 사용할 수 있습니다.

:::image type="content" source="media/tutorial-forescout/forescout-property-tree.png" alt-text="속성":::

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Forescout 통합을 시작하는 방법을 알아보았습니다. 계속하여 [Palo Alto 통합](./tutorial-palo-alto.md)에 대해 알아보세요.

> [!div class="nextstepaction"]
> [다음 단계 단추](./tutorial-palo-alto.md)