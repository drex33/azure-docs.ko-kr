---
title: 고가용성 설정
description: 온-프레미스 관리 콘솔 고가용성 어플라이언스를 설치하여 Defender for IoT 배포의 복원력을 높이세요. 고가용성 배포는 관리형 센서가 활성 온-프레미스 관리 콘솔에 지속적으로 보고하도록 합니다.
ms.date: 07/11/2021
ms.topic: how-to
ms.openlocfilehash: d5ea894188c4801f9ca29379347bb0a737a8ed8f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471783"
---
# <a name="about-high-availability"></a>고가용성 정보

온-프레미스 관리 콘솔 고가용성 어플라이언스를 설치하여 Defender for IoT 배포의 복원력을 높이세요. 고가용성 배포는 관리형 센서가 활성 온-프레미스 관리 콘솔에 지속적으로 보고하도록 합니다.

이 배포는 기본 및 보조 어플라이언스를 포함하는 온-프레미스 관리 콘솔 쌍을 사용하여 구현됩니다.

## <a name="about-primary-and-secondary-communication"></a>기본 및 보조 통신 정보

기본 및 보조 온-프레미스 관리 콘솔이 쌍으로 연결된 경우:

- 기본 어플라이언스와 보조 어플라이언스 간에 보안 연결을 만들기 위해 온-프레미스 관리 콘솔 SLL 인증서가 적용됩니다. SLL은 기본적으로 설치되는 자체 서명된 인증서 또는 고객이 설치한 인증서일 수 있습니다.

    유효성 검사가 `ON`이면 어플라이언스는 인증서에 정의된 CRL 서버에 대한 연결을 설정할 수 있어야 합니다.

- 기본 온-프레미스 관리 콘솔 데이터는 10분마다 보조 온-프레미스 관리 콘솔로 자동 백업됩니다. 온-프레미스 관리 콘솔 구성 및 디바이스 데이터가 백업됩니다. PCAP 파일 및 로그는 백업에 포함되지 않습니다. PCAP 및 로그는 수동으로 백업하고 복원할 수 있습니다.

- 관리 콘솔의 기본 설정은 보조 설정에 복제됩니다(예: 시스템 설정). 이러한 설정이 기본에서 업데이트되면 보조에서도 업데이트됩니다.

- 보조의 라이선스가 만료되기 전에 라이선스를 업데이트하기 위해 이를 기본으로 정의해야 합니다.

## <a name="about-failover-and-failback"></a>장애 조치(failover) 및 장애 복구(failback) 정보

센서가 기본 온-프레미스 관리 콘솔에 연결할 수 없는 경우 보조에 자동으로 연결됩니다. 보조와 통신하는 센서가 절반 미만인 경우 기본 및 보조 모두 시스템을 동시에 지원합니다. 보조는 센서의 절반 이상이 보조와 통신하는 경우에 사용됩니다. 기본에서 보조로의 장애 조치(failover)는 약 3분 정도 걸립니다. 장애 조치(failover)가 발생하면 기본 온-프레미스 관리 콘솔이 중지됩니다. 이런 경우 동일한 로그인 자격 증명을 사용하여 보조에 로그인할 수 있습니다.

센서는 장애 조치(failover) 도중에도 기본 어플라이언스와 통신을 계속 시도합니다. 절반 이상의 관리형 센서가 기본과 통신하는 데 성공하면 기본이 복원됩니다. 기본이 복원되면 보조 콘솔에 다음과 같은 메시지가 표시됩니다.

:::image type="content" source="media/how-to-set-up-high-availability/secondary-console-message.png" alt-text="기본이 복원될 때 보조 콘솔에 표시되는 메시지의 스크린샷.":::

리디렉션 후 기본 어플라이언스에 다시 로그인합니다.

## <a name="high-availability-setup-overview"></a>고가용성 설정 개요

설치 및 구성 절차는 다음 네 가지 기본 단계로 수행됩니다.

1. 온-프레미스 관리 콘솔 기본 어플라이언스를 설치합니다. 

1. 온-프레미스 관리 콘솔 기본 어플라이언스를 구성합니다. 예약된 백업 설정, VLAN 설정을 예로 들 수 있습니다. 자세한 내용은 온-프레미스 관리 콘솔 사용자 가이드를 참조하세요. 모든 설정은 페어링 후 자동으로 보조 어플라이언스에 적용됩니다.

1. 온 프레미스 관리 콘솔 보조 어플라이언스를 설치합니다. 자세한 내용은 [Defender for IoT 설치 정보](how-to-install-software.md)를 참조하세요.

1. [여기](https://infrascale.secure.force.com/pkb/articles/Support_Article/How-to-access-your-Appliance-Management-Console)에 설명된 대로 기본 및 보조 온-프레미스 관리 콘솔 어플라이언스를 페어링합니다. 설정을 수행하려면 기본 온-프레미스 관리 콘솔이 관리하는 센서가 두 개 이상이어야 합니다.

## <a name="high-availability-requirements"></a>고가용성 요구 사항

다음과 같은 고가용성 요구 사항을 충족했는지 확인합니다.

- 인증서 요구 사항

- 소프트웨어 및 하드웨어 요구 사항

- 네트워크 액세스 요구 사항

### <a name="software-and-hardware-requirements"></a>소프트웨어 및 하드웨어 요구 사항

- 기본 온-프레미스 관리 콘솔 어플라이언스와 보조 온-프레미스 관리 콘솔 어플라이언스는 동일한 하드웨어 모델 및 소프트웨어 버전을 실행해야 합니다.

- 고가용성 시스템은 Defender for IoT 사용자만 CLI 도구를 사용하여 설정할 수 있습니다.

### <a name="network-access-requirements"></a>네트워크 액세스 요구 사항

조직 보안 정책이 기본 및 보조 온-프레미스 관리 콘솔에서 다음 서비스에 대한 액세스를 허용하는지 확인해야 합니다. 이러한 서비스는 센서와 보조 온-프레미스 관리 콘솔 간 연결도 허용합니다.

|포트|서비스|Description|
|----|-------|-----------|
|**443 또는 TCP**|HTTPS|온-프레미스 관리 콘솔 웹 콘솔에 대한 액세스 권한을 부여합니다.|
|**22 또는 TCP**|SSH|기본 온-프레미스 관리 콘솔 어플라이언스와 보조 온-프레미스 관리 콘솔 어플라이언스 간 데이터를 동기화합니다.|
|**123 또는 UDP**|NTP| 온-프레미스 관리 콘솔의 NTP 시간 동기화입니다. 액티브 어플라이언스와 패시브 어플라이언스가 동일한 표준 시간대로 정의되어 있는지 확인합니다.|

## <a name="create-the-primary-and-secondary-pair"></a>기본 및 보조 쌍 만들기

절차를 시작하기 전에 기본 및 보조 온-프레미스 관리 콘솔 어플라이언스가 모두 켜졌는지 확인합니다.

### <a name="on-the-primary"></a>기본

1. 관리 콘솔에 로그인합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

1. 연결 문자열을 복사합니다.

    :::image type="content" source="../media/how-to-set-up-high-availability/connection-string.png" alt-text="다음 명령에서 사용할 연결 문자열을 복사합니다.":::

1. 기본에서 다음 명령을 실행합니다.

    ```bash
    sudo cyberx-management-trusted-hosts-add -ip <Secondary IP> -token <connection string>
    ```

    >[!NOTE]
    > 이 문서에서는 주 온-프레미스 관리 콘솔을 기본이라고 하고, 에이전트를 보조라고 합니다.

1. ```<Secondary ip>``` 필드에 보조 어플라이언스의 IP 주소를 입력하고 Enter를 선택합니다. 그러면 IP 주소의 유효성이 검사되고 SSL 인증서가 기본으로 다운로드됩니다. IP 주소를 입력하면 센서가 보조 어플라이언스에 연결됩니다.

1. 기본에서 다음 명령을 실행하여 인증서가 제대로 설치되었는지 확인합니다.

    ```bash
    sudo cyberx-management-trusted-hosts-apply
    ```

1. 기본에서 다음 명령을 실행합니다. **sudo를 사용하여 실행하지 마세요.**

    ```bash
    cyberx-management-deploy-ssh-key <Secondary IP>
    ```

   이렇게 하면 백업 및 복원을 위한 기본 어플라이언스와 보조 어플라이언스 간 연결이 가능합니다.

1. 보조 어플라이언스의 IP 주소를 입력하고 Enter 키를 선택합니다.

### <a name="on-the-secondary"></a>보조

1. Defender for IoT 사용자로 CLI에 로그인합니다.

1. 보조에서 다음 명령을 실행합니다. **sudo를 사용하여 실행하지 마세요.**

    ```bash
    cyberx-management-deploy-ssh-key <Primary ip>
    ```

    이렇게 하면 백업 및 복원을 위한 기본 어플라이언스와 보조 어플라이언스 간 연결이 가능합니다.

1. 기본의 IP 주소를 입력하고 Enter 키를 누릅니다.

### <a name="track-high-availability-activity"></a>고가용성 활동 추적

핵심 애플리케이션 로그를 Defender for IoT 지원 팀으로 내보내 고가용성 문제를 처리할 수 있습니다.  

**핵심 로그에 액세스하려면**:

1. **시스템 설정** 창에서 **내보내기** 를 선택합니다.

## <a name="update-the-on-premises-management-console-with-high-availability"></a>고가용성으로 온-프레미스 관리 콘솔 업데이트

다음 순서로 고가용성 업데이트를 수행합니다. 새 단계를 시작하기 전에 각 단계를 완료해야 합니다.

**고가용성으로 업데이트하려면**:

1. 기본 온-프레미스 관리 콘솔을 업데이트합니다.

1. 보조 온-프레미스 관리 콘솔을 업데이트합니다.

1. 센서를 업데이트합니다.

## <a name="see-also"></a>참고 항목

[온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)
