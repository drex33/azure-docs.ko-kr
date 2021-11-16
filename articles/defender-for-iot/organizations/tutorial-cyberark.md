---
title: Microsoft Defender for IoT와 CyberArk 통합
description: 이 자습서에서는 Microsoft Defender for IoT와 CyberArk를 통합하는 방법을 알아봅니다.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbfa178c2e5358503208773109fc53ab3c274be7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278382"
---
# <a name="tutorial-integrate-cyberark-with-microsoft-defender-for-iot"></a>자습서: Microsoft Defender for IoT와 CyberArk 통합

이 자습서를 사용하면 CyberArk를 Microsoft Defender for IoT와 통합하고 사용하는 방법을 알아볼 수 있습니다.

Defender for IoT는 ICS 인식 위협 분석과 기계 학습을 사용하는 ICS 및 IoT 사이버 보안 플랫폼입니다.

위협 행위자는 원격 데스크톱 및 VPN 연결을 통해 중요한 인프라 네트워크에 액세스하는 데 손상된 원격 액세스 자격 증명을 사용하고 있습니다. 이 접근 방식은 신뢰할 수 있는 연결을 통해 모든 OT 경계 보안을 쉽게 무시합니다. 자격 증명은 일반적으로 일상적인 작업을 수행하기 위해 원격 액세스가 필요한 제어 엔지니어, 파트너 유지 관리 담당자와 같은 권한 있는 사용자로부터 도난당합니다.

CyberARK와 Defender for IoT 통합을 통해 다음을 수행할 수 있습니다.

- 무단 원격 액세스로 인한 OT 위험 감소

- OT에 대한 지속적인 모니터링과 권한 있는 액세스 보안 제공

- 사고 대응, 위협 헌팅, 위협 모델링 향상

Defender for IoT 어플라이언스는 스위치와 같은 네트워크 디바이스의 SPAN 포트(미러 포트)를 통해 OT 네트워크에 연결하거나 Defender for IoT 어플라이언스의 전용 네트워크 인터페이스에 대한 단방향(인바운드) 연결을 통해 라우터에 연결합니다.

또한 전용 네트워크 인터페이스는 Defender for IoT 어플라이언스에서 중앙 집중식 관리 및 API 액세스를 위해 제공됩니다. 이 인터페이스는 권한 있는 사용자를 관리하고 원격 액세스 연결을 보호하기 위해 조직의 데이터 센터에 배포된 CyberArk PSM 솔루션과 통신하는 데에도 사용됩니다.

:::image type="content" source="media/tutorial-cyberark/architecture.png" alt-text="CyberArk PSM 솔루션 배포":::

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> - CyberArk에서 PSM 구성
> - Defender for IoT에서 통합 사용
> - 검색 보기 및 관리
> - 통합 중지

## <a name="prerequisites"></a>필수 조건

- CyberARK 버전 2.0.

- 기업의 모든 Defender for IoT 어플라이언스에 대한 CLI 액세스 권한이 있는지 확인합니다.

- Azure 계정. 아직 Azure 계정이 없는 경우 [지금 Azure 체험 계정을 만들](https://azure.microsoft.com/free/) 수 있습니다.

## <a name="configure-psm-cyberark"></a>PSM CyberArk 구성

CyberArk는 Defender for IoT와 통신을 허용하도록 구성해야 합니다. 이 통신은 PSM을 구성하여 수행됩니다.

**PSM을 구성하려면**:

1. `c:\Program Files\PrivateArk\Server\dbparam.xml` 파일을 찾아서 엽니다.

1. 다음 매개 변수를 추가합니다.

    `[SYSLOG]` <br>
    `UseLegacySyslogFormat=Yes` <br>
    `SyslogTranslatorFile=Syslog\CyberX.xsl` <br>
    `SyslogServerIP=<CyberX Server IP>` <br>
    `SyslogServerProtocol=UDP` <br>
    `SyslogMessageCodeFilter=319,320,295,378,380` <br>

1. 파일을 저장하고 닫습니다.

1. Defender for IoT syslog 구성 파일 `CyberX.xsl`을 `c:\Program Files\PrivateArk\Server\Syslog\CyberX.xsl`에 저장합니다.

1. **서버 중앙 관리** 를 엽니다.

1. **신호등 중지** 를 선택하여 서버를 중지합니다.

    :::image type="content" source="media/tutorial-cyberark/server.png" alt-text="서버 중앙 관리 신호등 중지의 스크린샷":::

1. **신호등 시작** 을 선택하여 서버를 시작합니다.

## <a name="enable-the-integration-in-defender-for-iot"></a>Defender for IoT에서 통합 사용

통합을 사용하려면 Defender for IoT 관리 콘솔에서 Syslog 서버를 사용하도록 설정해야 합니다. 기본적으로 Syslog 서버는 포트 514 UDP를 사용하여 시스템의 IP 주소를 수신 대기합니다.

**Defender for IoT를 구성하려면**:

1. Defender for IoT 관리 콘솔에서 **시스템 설정** 으로 이동합니다.

1. Syslog 서버를 **켜기** 로 전환합니다.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="켜기로 전환된 syslog 서버의 스크린샷":::

1. (선택 사항) CLI를 통해 시스템에 로그인하여 포트를 변경하고, `/var/cyberx/properties/syslog.properties`로 이동하고, `listener: 514/udp`를 변경합니다.

## <a name="view-and-manage-detections"></a>검색 보기 및 관리

Microsoft Defender for IoT와 CyberArk PSM 간 통합은 syslog 메시지를 통해 수행됩니다. 이러한 메시지는 PSM 솔루션을 통해 Defender for IoT로 전송되어 Defender for IoT에 원격 세션 또는 확인 실패에 관해 알립니다.

Defender for IoT 플랫폼이 PSM에서 이러한 메시지를 수신하면 네트워크에 표시되는 데이터와 상관 관계를 지정하여 권한이 없는 사용자가 아닌 PSM 솔루션에 의해 네트워크에 대한 원격 액세스 연결이 생성되었는지 유효성을 검사합니다.

### <a name="view-alerts"></a>경고 보기

Defender for IoT 플랫폼은 PSM에서 권한이 부여되지 않은 원격 세션을 식별할 때마다 `Unauthorized Remote Session`을 실행합니다. 즉각적인 조사를 쉽게 수행하도록 경고에는 원본 및 대상 디바이스의 IP 주소와 이름도 표시됩니다.

**경고를 보려면**:

1. 관리 콘솔에 로그인합니다.

1. 왼쪽 패널에서 **경고** 를 선택합니다.

1. 경고 목록에서 **권한이 없는 원격 세션** 이라는 경고를 선택합니다.

    :::image type="content" source="media/tutorial-cyberark/unauthorized.png" alt-text="권한이 없는 원격 세션 경고":::

### <a name="event-timeline"></a>이벤트 타임라인

PSM이 원격 연결에 권한을 부여할 때마다 Defender for IoT 이벤트 타임라인 페이지에 표시됩니다. 이벤트 타임라인 페이지에는 모든 경고와 알림의 타임라인이 표시됩니다.

**이벤트 타임라인을 보려면**:

1. Defender for IoT 센서에 로그인합니다.

1. 왼쪽 패널에서 **이벤트 타임라인** 을 선택합니다.

1. PSM 원격 세션이라는 이벤트를 찾습니다.

    :::image type="content" source="media/tutorial-cyberark/event.png" alt-text="이벤트 로그 화면의 보기":::

### <a name="auditing--forensics"></a>감사 및 포렌식

관리자는 기본 제공 데이터 마이닝 인터페이스를 통해 Defender for IoT 플랫폼을 쿼리하여 원격 액세스 세션을 감사하고 조사할 수 있습니다. 이 정보는 원본 또는 대상 디바이스, 프로토콜(RDP 또는 SSH), 원본 및 대상 사용자, 타임스탬프, 세션에 PSM을 사용하여 권한이 부여되었는지 여부와 같은 포렌식 세부 정보를 포함하여 발생한 모든 원격 액세스 연결을 식별하는 데 사용할 수 있습니다.

**감사하고 조사하려면**:

1. Defender for IoT 센서에 로그인합니다.

1. 왼쪽 패널에서 **데이터 마이닝** 을 선택합니다.

1. **원격 액세스** 를 선택합니다.

    :::image type="content" source="media/tutorial-cyberark/data-mining.png" alt-text="데이터 마이닝 인터페이스의 보기":::

## <a name="stop-the-integration"></a>통합 중지

언제든지 통신에서 통합을 중지할 수 있습니다.

**통합을 중지하려면**:

1. Defender for IoT 관리 콘솔에서 **시스템 설정** 화면으로 이동합니다.

1. Syslog 서버 옵션을 **끄기** 로 전환합니다.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="서버 상태 보기":::

## <a name="clean-up-resources"></a>리소스 정리

정리할 리소스가 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 CyberArk 통합을 시작하는 방법을 알아보았습니다. 계속해서 Forescout 통합에 관해 알아봅니다.

> [!div class="nextstepaction"]
> [다음 단계 단추](./tutorial-forescout.md)
