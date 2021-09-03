---
title: 센서 활성화 및 설정
description: 이 문서에서는 센서 콘솔 로그인 및 활성화 방법을 설명합니다.
ms.date: 04/29/2021
ms.topic: how-to
ms.openlocfilehash: 256e2b941d0eb2f24331abd892b798014cf0eb5d
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020964"
---
# <a name="activate-and-set-up-your-sensor"></a>센서 활성화 및 설정

이 문서에서는 센서를 활성화하고 초기 설치를 수행하는 방법을 설명합니다.

관리자 사용자는 처음으로 로그인할 때 및 정품 인증 관리가 필요할 때 활성화를 수행합니다. 설치 프로그램은 최적으로 검색 및 경고할 수 있게 센서를 구성합니다.

보안 분석가와 읽기 전용 사용자는 센서를 활성화하거나 새 암호를 생성할 수 없습니다.

## <a name="sign-in-and-activation-for-administrator-users"></a>관리자 사용자에 대한 로그인 및 활성화

최초로 로그인하는 관리자는 센서 온보딩 중에 다운로드한 활성화 및 암호 복구 파일에 액세스할 수 있는지 확인해야 합니다. 그렇지 못하다면 Azure 보안 관리자, 구독 기여자 또는 구독 소유자 권한이 있어야 Azure Defender for IoT 포털에서 이 파일을 생성할 수 있습니다.

### <a name="first-time-sign-in-and-activation-checklist"></a>최초 로그인 및 활성화 검사 목록

센서 콘솔에 로그인하기 전에 관리자 사용자는 다음에 액세스할 수 있어야 합니다.

- 설치 중 정의된 센서 IP 주소

- 센서에 대한 사용자 로그인 자격 증명 센서에 대한 ISO를 다운로드한 경우 설치 중에 받은 기본 자격 증명을 사용합니다. 활성화한 후 새 *관리자* 를 만드는 것이 좋습니다.

- 초기 암호입니다. Arrow를 통해 미리 구성된 센서를 구매한 경우, 최초 로그인 시 암호를 생성해야 합니다.

- 이 센서와 연결된 활성화 파일입니다. 이 파일은 Defender for IoT 포털에서 센서 온보딩 중에 생성 및 다운로드되었습니다.

- 회사에서 요구하는 SSL/TLS CA 서명 인증서입니다.

### <a name="about-activation-files"></a>활성화 파일 정보

센서가 특정 관리 모드에서 Azure Defender for IoT에 온보딩되었습니다.

| 모드 유형 | 설명 |
|--|--|
| **클라우드 연결 모드** | 센서가 검색한 정보는 센서 콘솔에 표시됩니다. 경고 정보는 IoT 허브를 통해서도 배달되며 다른 Azure 서비스(예: Azure Sentinel)와 공유할 수 있습니다. 자동 위협 인텔리전스 업데이트를 사용하도록 설정할 수도 있습니다. |
| **로컬 연결 모드** | 센서가 검색한 정보는 센서 콘솔에 표시됩니다. 검색 정보는 센서가 연결된 경우 온-프레미스 관리 콘솔과도 공유됩니다. |

이 센서에 대해 온보딩 중에 로컬 연결 또는 클라우드 연결 활성화 파일이 생성되어 다운로드되었습니다. 활성화 파일에는 센서의 관리 모드에 대한 지침이 포함되어 있습니다. *고유한 활성화 파일을 배포하는 각 센서에 업로드해야 합니다.*  처음 로그인할 때 이 센서의 관련 활성화 파일을 업로드해야 합니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender for IoT 포털, 센서 온보딩":::

### <a name="about-certificates"></a>인증서 정보

센서 설치에 따라 로컬 자체 서명된 인증서가 생성되어 센서 콘솔 액세스에 사용됩니다. 관리자가 처음으로 콘솔에 로그인하면 해당 사용자에게 SSL/TLS 인증서를 온보딩하라는 메시지가 표시됩니다.

다음 두 가지 수준의 보안을 사용할 수 있습니다.

- CA 서명 인증서를 업로드하여 조직에서 요청하는 특정 인증서 및 암호화 요구 사항을 충족합니다.
- 관리 콘솔과 연결된 센서 간의 유효성 검사를 허용합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가됩니다. *유효성 검사에 실패하면 관리 콘솔과 센서 간의 통신이 중단되고 콘솔에 유효성 검사 오류가 표시됩니다.* 이 옵션은 설치 후 기본적으로 사용하도록 설정됩니다.  

콘솔은 다음 인증서 유형을 지원합니다.

- 프라이빗 및 엔터프라이즈 키 인프라(프라이빗 PKI)

- 공개 키 인프라(공개 PKI)

- 어플라이언스에서 로컬로 생성됨(로컬로 자체 서명됨) 

  > [!IMPORTANT]
  > 기본 자체 서명된 인증서를 사용하지 않는 것이 좋습니다. 이 인증서는 안전하지 않으므로 테스트 환경에만 사용해야 합니다. 인증서 소유자의 유효성을 검사할 수 없으며 시스템 보안을 유지할 수 없습니다. 프로덕션 네트워크에는 이 옵션을 사용하지 마세요.

인증서 사용에 관한 자세한 내용은 [인증서 관리](how-to-manage-individual-sensors.md#manage-certificates)를 참조하세요.

### <a name="sign-in-and-activate-the-sensor"></a>로그인 및 센서 활성화

**로그인 및 활성화하려면**

1. 설치 중에 정의한 IP를 사용하여 브라우저에서 센서 콘솔로 이동합니다. 로그인 대화 상자가 열립니다.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender for IoT 센서":::

1. 센서 설치 중에 정의한 자격 증명을 입력하거나 **암호 복구** 옵션을 선택합니다. Arrow를 통해 미리 구성된 센서를 구매한 경우 먼저 암호를 생성합니다. 암호 복구에 대한 자세한 내용은 [초기 로그인 시 암호 실패 조사](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in)를 참조하세요.

1. 로그인하면 **활성화** 대화 상자가 열립니다. **업로드** 를 선택하고 센서 온보딩 중에 다운로드한 활성화 파일로 이동합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="업로드를 선택하고 활성화 파일로 이동합니다.":::

1. 활성화하기 전에 센서 네트워크 구성을 변경하려면 **센서 네트워크 구성** 링크를 선택합니다. [활성화하기 전에 센서 네트워크 매개 변수 업데이트](#update-sensor-network-configuration-before-activation)를 참조하세요.

1. 사용 약관에 동의합니다.

1. **활성화** 를 선택합니다. SSL/TLS 인증서 대화 상자가 열립니다.

1. 인증서 이름을 정의합니다.
1. CRT 및 키 파일을 업로드합니다.
1. 암호를 입력하고, 필요한 경우 PEM 파일을 업로드합니다.
1. **다음** 을 선택합니다. 유효성 검사 화면이 열립니다. 기본적으로 관리 콘솔과 연결된 센서 간의 유효성 검사를 사용하도록 설정되어 있습니다.
1. **시스템 차원의 유효성 검사 사용** 토글을 꺼서 유효성 검사를 사용하지 않게 설정합니다. 유효성 검사는 사용하는 것이 좋습니다.
1. **저장** 을 선택합니다.  

CA 서명된 인증서를 업로드한 후에는 화면을 새로 고쳐야 할 수도 있습니다.

새 인증서 업로드, 지원되는 인증서 매개 변수, CLI 인증서 명령 작업에 대한 정보는 [개별 센서 관리](how-to-manage-individual-sensors.md)를 참조하세요.

#### <a name="update-sensor-network-configuration-before-activation"></a>활성화하기 전에 센서 네트워크 매개 변수 업데이트  

센서 네트워크 구성 매개 변수는 소프트웨어를 설치하는 동안 또는 미리 구성된 센서를 구매할 때 정의되었습니다. 다음 매개 변수가 정의되었습니다.

- IP 주소
- DNS
- 기본 게이트웨이
- 서브넷 마스크
- 호스트 이름

센서를 활성화하기 전에 이 정보를 업데이트하려 할 수도 있습니다. 예를 들어 Arrow에서 정의한 미리 구성된 매개 변수를 변경해야 할 수 있습니다. 센서를 활성화하기 전에 프록시 설정을 정의할 수도 있습니다.

**센서 네트워크 구성 매개 변수를 업데이트하려면 다음을 수행합니다.**

1. **활성화** 대화 상자에서 **센서 네트워크 구성** 링크를 선택합니다.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="센서 네트워크 구성":::

2. 설치 중에 정의된 매개 변수가 표시됩니다. 프록시를 정의하는 옵션도 사용할 수 있습니다. 필요한 대로 설정을 업데이트하고 **저장** 을 선택합니다.

### <a name="activate-an-expired-license-versions-under-100"></a>만료된 라이선스(10.0 미만 버전) 활성화

10.0 이전 버전 사용자인 경우 라이선스가 만료될 수 있고 다음 경고가 표시됩니다. 

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/activation-popup.png" alt-text="라이선스가 만료되면 활성화 파일을 통해 라이선스를 업데이트해야 합니다.":::

**라이선스를 활성화하려면 다음을 수행합니다.**

1. [고객 지원팀](https://ms.portal.azure.com/?passwordRecovery=true&Microsoft_Azure_IoT_Defender=canary#create/Microsoft.Support)에서 사례를 엽니다.

1. 지원은 활성화 ID 번호를 통해 제공됩니다.

1. 고객 지원팀에서 문자열 양식의 새 라이선스 정보를 제공합니다.

1. 사용 약관을 읽고 승인할 확인란을 선택합니다.

1. 제공된 공간에 문자열을 붙여넣습니다.

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/add-license.png" alt-text="제공된 필드에 문자열을 붙여넣습니다.":::

1. **활성화** 를 선택합니다.

### <a name="subsequent-sign-ins"></a>후속 로그인

처음 활성화한 후에는 활성화 파일 없는 로그인 이후에 Azure Defender for IoT 센서 콘솔이 열립니다. 본인의 로그인 자격 증명만 필요합니다.

로그인하면 Azure Defender for IoT 콘솔이 열립니다.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender for IoT 콘솔":::

## <a name="initial-setup-and-learning-for-administrators"></a>초기 설정 및 학습(관리자용)

처음 로그인하면 Azure Defender for IoT 센서가 자동으로 네트워크를 모니터링하기 시작합니다. 디바이스 맵 및 디바이스 인벤토리 섹션에 네트워크 디바이스가 표시됩니다. Azure Defender for IoT가 네트워크에서 발생하는 모든 보안 및 운영 인시던트에 대한 검색과 경고를 시작하게 됩니다. 그러면 검색된 정보를 기반으로 보고서 및 쿼리를 만들 수 있습니다.

처음에는 이 작업을 학습 모드에서 수행하여 센서가 네트워크의 일상적 활동을 학습하게 합니다. 예를 들어, 센서는 네트워크에서 검색된 디바이스와 프로토콜, 특정 디바이스 간에 발생하는 파일 전송을 학습합니다. 이 활동이 네트워크 기준 활동이 됩니다.

### <a name="review-and-update-basic-system-settings"></a>기본 시스템 설정 검토 및 업데이트

센서의 시스템 설정을 검토하여 센서가 최적으로 검색 및 경고할 수 있게 구성되었는지 확인합니다.

센서의 시스템 설정을 정의합니다. 예:

- ICS(또는 IoT) 및 분리 서브넷을 정의합니다.

- 사이트별 프로토콜에 대한 포트 별칭을 정의합니다.

- 사용 중인 VLAN 및 이름을 정의합니다.

- DHCP를 사용하는 경우 합당한 DHCP 범위를 정의합니다.

- Active Directory 및 메일 서버와의 통합을 적절하게 정의합니다.

### <a name="disable-learning-mode"></a>학습 모드 사용 안 함

시스템 설정을 조정한 후에는 시스템 검색이 네트워크 활동을 정확하게 반영한다고 생각될 때까지 Azure Defender for IoT 센서를 학습 모드에서 실행되게 할 수 있습니다.

학습 모드는 네트워크 규모 및 복잡도에 따라 약 2~6주 동안 실행해야 합니다. 학습 모드를 사용하지 않도록 설정한 후에는 기준 활동과 다른 활동이 발생하면 경고가 트리거됩니다.

**학습 모드를 사용하지 않으려면**

- **시스템 설정** 을 선택하고 **학습** 옵션을 해제합니다.

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>보안 분석가 및 읽기 전용 사용자에 대한 최초 로그인

로그인하기 전에 다음이 있는지 확인합니다.

- 센서 IP 주소
- 관리자가 제공한 로그인 자격 증명

## <a name="console-tools-overview"></a>콘솔 도구: 개요

옆 메뉴에서 콘솔 도구에 액세스합니다.

**탐색** 

| 시간 범위 | 아이콘 | 설명 |
| -----------|--|--|
| 대시보드 | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | 네트워크 보안 상태의 직관적 스냅샷을 확인합니다. |
| 디바이스 맵 | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | 지도에서 네트워크 디바이스, 디바이스 연결 및 디바이스 속성을 봅니다. 다양한 확대/축소, 강조 표시 및 필터 옵션을 사용하여 네트워크를 표시할 수 있습니다. |
| 디바이스 인벤토리 | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | 디바이스 인벤토리는 이 센서가 검색하는 디바이스 특성 목록을 표시합니다. 다음 옵션을 사용할 수 있습니다. <br /> - 테이블 필드에 따라 정보를 정렬하거나 필터링하고, 나타나는 필터링된 정보를 확인합니다. <br /> - 정보를 CSV 파일로 내보냅니다. <br /> - Windows 레지스트리 세부 정보를 가져옵니다.|
| 경고 | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | 정책 위반이 발생하거나, 기준 동작과의 차이가 있거나, 네트워크에서 의심스러운 형태의 활동이 검색되면 경고를 표시합니다. |
| 보고서 | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | 데이터 마이닝 쿼리를 기반으로 하는 보고서를 봅니다. |

**분석**

| 시간 범위| 아이콘 | 설명 |
|---|---|---|
| 이벤트 타임라인 | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | 경고, 네트워크 이벤트(정보), 사용자 작업(예: 사용자 로그인, 사용자 삭제)에 대한 정보를 포함한 타임라인을 확인합니다.|

**탐색**

| 시간 범위 | 아이콘 | 설명 |
|---|---|---|
| 데이터 마이닝 | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | 다양한 계층에서 네트워크 디바이스에 대한 포괄적이고 세부적인 정보를 생성합니다. |
| 조사 | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | 광범위한 위젯에서 추세와 통계를 봅니다. |
| 위험 평가 | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | **취약성** 창을 표시합니다. |

**관리자**

| 시간 범위 | 아이콘 | 설명 |
|---|---|---|
| 사용자 | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | 다양한 액세스 수준으로 사용자와 역할을 정의합니다. |
| 전달 | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | 경고 정보를 파트너, Defender for IoT와 통합하는 내부 원본(예: Azure Sentinel), 이메일 주소, 웹후크 서버 등으로 전달합니다. <br /> 자세한 내용은 [경고 정보 전달](how-to-forward-alert-information-to-partners.md)을 참조하세요. |
| 시스템 설정 | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | 시스템 설정을 구성합니다. 예를 들어 DHCP 설정을 정의하거나, 메일 서버 세부 정보를 제공하거나, 포트 별칭을 만듭니다. |
| 설정 가져오기 | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | **가져오기 설정** 창을 표시합니다. 디바이스 정보를 수동으로 변경할 수 있습니다.<br /> 자세한 내용은 [디바이스 정보 가져오기](how-to-import-device-information.md)를 참조하세요. |

**지원**

| 시간 범위| 아이콘 | 설명 |
|----|---|---|
| 지원 | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | 지원이 필요하면 [Microsoft 지원](https://support.microsoft.com/)에 문의하세요. |

## <a name="see-also"></a>참조

[위협 인텔리전스 리서치 및 패키지 #](how-to-work-with-threat-intelligence-packages.md)

[센서 온보딩](getting-started.md#onboard-a-sensor)

[센서 활성화 파일 관리](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[모니터링된 트래픽 제어](how-to-control-what-traffic-is-monitored.md)
