---
title: Azure Defender for IoT와 ClearPass 통합
description: 이 자습서에서는 ClearPass를 Azure Defender for IoT와 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 10/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: 7ff16da99f994ff4b708f0fb6f4a40e72f61df78
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129421243"
---
# <a name="tutorial-integrate-clearpass-with-azure-defender-for-iot"></a>자습서: Azure Defender for IoT와 ClearPass 통합

이 자습서를 사용하면 ClearPass Policy Manager(CPPM)를 Azure Defender for IoT와 통합하는 방법을 알아볼 수 있습니다.

Defender for IoT 플랫폼은 지속적인 ICS 위협 모니터링 및 장치 검색을 제공하고, ICS 관련 동작 변칙 검색, 위협 인텔리전스, 위험 분석 및 자동화된 위협 모델링을 통해 산업 프로토콜, 디바이스 및 애플리케이션에 대한 심층 임베디드 해석을 결합합니다.

Defender for IoT는 OT 및 ICS 엔드포인트를 검색, 발견 및 분류하고, Clearpass Security Exchange 프레임워크를 사용하여 Clearpass와 직접 정보를 공유하며, 오픈 API를 엽니다.

Defender for IoT는 엔드포인트 분류 데이터 및 여러 사용자 지정 보안 특성을 사용하여 ClearPass Policy Manager Endpoint Database를 자동으로 업데이트합니다.

통합으로 다음을 수행할 수 있습니다.

- ICS 및 IoT 보안 엔진용 Defender가 식별하는 SCADA 보안 위협 보기.

- IoT 센서용 Defender가 발견한 디바이스 인벤토리 정보 보기. 센서는 모든 네트워크 디바이스, IT 및 OT 인프라 전반에 걸친 엔드포인트를 중앙에 표시합니다. 여기 ClearPass 시스템에서 중앙 엔드포인트 및 에지 보안 정책을 정의하고 관리할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> - ClearPass API 사용자 만들기
> - ClearPass 운영자 프로필 만들기
> - ClearPass OAuth API 클라이언트 만들기
> - ClearPass와 통합하도록 IoT에 대한 Defender 구성하기
> - ClearPass 전달 규칙 정의하기
> - IoT 통신용 ClearPass 및 Defender 모니터링하기

## <a name="prerequisites"></a>필수 조건

### <a name="aruba-clearpass-requirements"></a>아루바 ClearPass 요구 사항

CPPM은 사전 설치된 소프트웨어가 있는 하드웨어 어플라이언스 또는 다음 하이퍼바이저에 있는 가상 머신으로 실행됩니다. VMware Player와 같은 클라이언트 컴퓨터에서 실행되는 하이퍼바이저는 지원되지 않습니다.

- VMware ESXi 5.5, 6.0, 6.5, 6.6 이상

- Microsoft Hyper-V Server 2012 R2 또는 2016 R2

- Hyper-V on Microsoft Windows Server 2012 R2 또는 2016 R2

- CentOS 7.5 이상의 KVM

### <a name="defender-for-iot-requirements"></a>Defender for IoT 요구 사항

- Defender for IoT 버전 2.5.1 이상

- Azure 계정. 아직 Azure 계정이 없는 경우 [지금 Azure 체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.

## <a name="create-a-clearpass-api-user"></a>ClearPass API 사용자 만들기

Defender for IoT는 두 제품 간 통신 채널의 일환으로 많은 API(TIPS 및 REST)를 사용합니다. TIPS API에 대한 액세스는 사용자 이름 및 암호 조합 자격 증명을 통해 확인됩니다. 이 사용자 ID에는 최소 수준의 액세스 권한이 있어야 합니다. 슈퍼 관리자 프로필이 아니라 아래와 같이 API 관리자를 사용합니다.

**ClearPass API 사용자를 만들려면 다음을 수행합니다.**

1. 왼쪽 창에서 **관리** > **사용자 및 권한** 을 선택하고 **추가** 를 선택합니다.

1. **관리자 사용자 추가** 대화 상자에서 다음 매개 변수를 설정합니다.

    :::image type="content" source="media/tutorial-clearpass/policy-manager.png" alt-text="관리자 사용자 대화 상자 보기의 스크린샷":::

    | 매개 변수 | Description |
    |--|--|
    | **UserID** | 사용자 ID를 입력합니다. |
    | **이름** | 사용자 이름을 입력합니다. |
    | **암호** | 암호를 입력합니다. |
    | **사용자 사용** | 이 옵션을 사용하도록 설정되었는지 확인합니다. |
    | **권한 수준** | **API 관리자** 를 선택합니다. |

1. **추가** 를 선택합니다.

## <a name="create-a-clearpass-operator-profile"></a>ClearPass 운영자 프로필 만들기

Defender for IoT는 통합의 일부로 REST API를 사용합니다. REST API는 OAuth 프레임워크에서 인증됩니다. Defender for IoT와 동기화하려면 API 클라이언트를 만들어야 합니다.

API 클라이언트의 REST API에 대한 액세스를 보호하려면 액세스가 제한된 운영자 프로필을 만듭니다.

**ClearPass 운영자 프로필을 만들려면 다음을 수행합니다.**

1. **운영자 프로필 편집** 창으로 이동합니다.

1. 다음을 제외하고 모든 옵션을 **액세스 권한 없음** 으로 설정합니다.

| 매개 변수 | Description |
|--|--|
| **API Services** | **액세스 허용** 설정 |
| **정책 관리자** | 다음과 같이 설정합니다. <br />- **사전**: **읽기, 쓰기, 삭제** 하도록 설정된 **특성**<br />- **사전**: **읽기, 쓰기, 삭제** 하도록 설정된 **지문**<br />- **ID**: **읽기, 쓰기, 삭제** 하도록 설정된 **엔드포인트** |

:::image type="content" source="media/tutorial-clearpass/api-profile.png" alt-text="운영자 프로필 편집의 스크린샷":::

:::image type="content" source="media/tutorial-clearpass/policy.png" alt-text="정책 관리자 화면에서 옵션의 스크린샷":::

## <a name="create-a-clearpass-oauth-api-client"></a>ClearPass OAuth API 클라이언트 만들기

1. 주 창에서 **관리자** > **API 서비스** > **API 클라이언트** 를 선택합니다.

1. API 클라이언트 만들기 탭에서 다음 매개 변수를 설정합니다.

    - **운영 모드**: 이 매개 변수는 Clearpass에 대한 API 통화에 사용됩니다. **Clearpass REST API – 클라이언트** 를 선택합니다.

    - **운영자 프로필**: 이전에 만든 프로필을 사용합니다.

    - **권한 부여 유형**: **클라이언트 자격 증명(grant_type = client_credentials)** 을 설정합니다.

1. **클라이언트 암호** 와 클라이언트 ID를 기록해야 합니다. 예: `defender-rest`.

    :::image type="content" source="media/tutorial-clearpass/aruba.png" alt-text="API 클라이언트 만들기의 스크린샷":::

1. 정책 관리자에서 다음 단계로 진행하기 전에 다음 정보 목록을 수집했는지 확인합니다.

    - CPPM 사용자 ID

    - CPPM 사용자 ID 암호

    - CPPM OAuth2 API 클라이언트 ID

    - CPPM OAuth2 API 클라이언트 암호

## <a name="configure-defender-for-iot-to-integrate-with-clearpass"></a>ClearPass와 통합하도록 IoT에 대한 Defender 구성하기

ClearPass에서 장치 인벤토리를 볼 수 있도록 설정하려면 Defender for IoT와 Clearpass 간 동기화를 설정해야 합니다. 동기화 구성이 완료되면 Defender for IoT 플랫폼이 새로운 엔드포인트를 발견할 때 ClearPass Policy Manager EndpointDb를 업데이트합니다.

**Defender for IoT 센서에서 ClearPass 동기화를 구성하려면 다음을 수행합니다.**

1. Defender for IoT 센서의 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. **시스템 설정** 창의 :::image type="content" source="media/tutorial-clearpass/clearpass-icon.png" alt-text="왼쪽에서 ClearPass 아이콘의 스크린샷":::을 선택합니다.

1. 다음 매개 변수를 설정합니다.

    :::image type="content" source="media/tutorial-clearpass/settings.png" alt-text="시스템 설정 창에 필요한 정보를 작성하는 스크린샷":::

    - **동기화 사용:** Defender for IoT와 ClearPass 간 동기화를 설정합니다.

    - **동기화 빈도:** 동기화 빈도를 분 단위로 정의합니다. 기본값은 60분입니다. 최솟값은 5분입니다.

    - **ClearPass IP 주소:** Defender for IoT가 동기화된 ClearPass 시스템의 IP 주소입니다.

    - **클라이언트 ID:** Defender for IoT와 데이터를 동기화하기 위해 ClearPass에서 생성된 클라이언트 ID입니다.

    - **클라이언트 암호:** Defender for IoT와 데이터를 동기화하기 위해 ClearPass에서 생성된 클라이언트 암호입니다.

    - **사용자 이름:** ClearPass 관리자 사용자입니다.

    - **암호:** ClearPass 관리자 암호입니다.

1. **저장** 을 선택합니다.

## <a name="define-a-clearpass-forwarding-rule"></a>ClearPass 전달 규칙 정의하기

Defender for IoT가 발견한 경고를 아루바에서 볼 수 있도록 설정하려면 전달 규칙을 설정해야 합니다. 이 규칙은 ICS 및 Defender for IoT 보안 엔진이 식별한 SCADA 보안 위협에 대한 어떤 정보를 ClearPass로 보낼지 정의합니다.

**Defender for IoT 센서에서 ClearPass 전달 규칙을 정의하려면 다음을 수행합니다.**

1. Defender for IoT 센서의 왼쪽 창에서 **전달** 을 선택합니다.

1. **전달** 창에서 **전달 규칙 만들기** 를 선택합니다.

    :::image type="content" source="media/tutorial-clearpass/forwarding.png" alt-text="모든 옵션이 포함된 전달 창의 스크린샷":::

1. 이름 및 규칙의 심각도를 추가하고 **작업** 드롭다운 목록에서 **보내기** > **ClearPass** 를 선택합니다.

    :::image type="content" source="media/tutorial-clearpass/rule.png" alt-text="전달 규칙 만들기 화면의 스크린샷":::

1. **작업** 창에서 다음 매개 변수를 설정합니다.

    :::image type="content" source="media/tutorial-clearpass/actions.png" alt-text="작업 창에서 작업을 선택합니다.":::

    | 매개 변수 | Description |
    |--|--|
    | **Host** | ClearPass 서버 IP 주소를 입력합니다. |
    | **포트** | 전달이 수행되는 ClearPass의 포트를 입력합니다. |
    | **구성** | ClearPass 시스템에서 Defender for IoT 경고를 볼 수 있도록 다음 옵션을 설정합니다. <br />- **불법 함수 코드 신고**: 프로토콜 위반 - ICS 프로토콜 사양을 위반하는 불법 필드 값(잠재적 악용)<br />- **권한이 없는 PLC 프로그래밍 및 펌웨어 업데이트 신고**: 권한 없는 PLC 변경 내용<br />- **권한 없는 PLC 중지 신고**: PLC 중단(가동 중지 시간)<br />- **맬웨어 관련 경고 신고:** TRITON, NotPetya 등의 산업용 맬웨어 시도<br />- **권한 없는 검색 신고**: 권한 없는 검색(잠재적 정찰) |

1. **제출** 을 선택합니다.

## <a name="monitor-clearpass-and-defender-for-iot-communication"></a>IoT 통신용 ClearPass 및 Defender 모니터링하기

동기화가 시작되면 엔드포인트 데이터가 Policy Manager EndpointDb로 직접 채워지므로 통합 구성 화면에서 마지막 업데이트 시간을 볼 수 있습니다.

**ClearPass에 마지막으로 동기화된 시간을 검토하려면 다음을 수행합니다.**

1. Defender for IoT 센서에 로그인합니다.

1. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. **ClearPass** 를 선택합니다.

    :::image type="content" source="media/tutorial-clearpass/last-sync.png" alt-text="마지막 동기화 시간 및 날짜를 보여주는 스크린샷":::

동기화가 작동하지 않거나 오류가 표시되는 경우 일부 정보를 캡처하지 못한 것일 수 있습니다. 기록된 데이터를 다시 확인합니다. 또한 **게스트** > **관리** > **지원** > **애플리케이션 로그** 에서 Defender for IoT와 ClearPass 사이의 API 통화를 볼 수도 있습니다.

다음은 Defender for IoT와 ClearPass 간 API 로그의 예입니다.

:::image type="content" source="media/tutorial-clearpass/log.png" alt-text="Defender for IoT와 ClearPass 간 API 로그 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ClearPass 통합을 시작하는 방법을 알아보았습니다. 계속하여 CyberArk에 대해 알아보세요.

> [!div class="nextstepaction"]
> [다음 단계 단추](./tutorial-cyberark.md)
