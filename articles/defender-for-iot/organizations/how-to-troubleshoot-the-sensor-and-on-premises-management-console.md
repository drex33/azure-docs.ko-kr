---
title: 센서 및 온-프레미스 관리 콘솔 문제 해결
description: 센서 및 온-프레미스 관리 콘솔 문제를 해결하여 발생하는 모든 문제를 제거합니다.
ms.date: 10/17/2021
ms.topic: article
ms.openlocfilehash: 791df9cc7b95ac32dfcc794136bc53df51e3a0a8
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131314"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>센서 및 온-프레미스 관리 콘솔 문제 해결

이 문서에서는 센서 및 온-프레미스 관리 콘솔의 기본 문제 해결 도구에 대해 설명합니다. 여기에 설명된 항목 외에도 다음과 같은 방법으로 시스템의 상태를 확인할 수 있습니다.

**경고**: 트래픽을 모니터링하는 센서 인터페이스가 다운된 경우 경고가 생성됩니다.

**SNMP**: 센서 상태는 SNMP를 통해 모니터링됩니다. Azure Defender for IoT는 권한 있는 모니터링 서버에서 보낸 SNMP 쿼리에 응답합니다.

**시스템 알림**: 관리 콘솔에서 센서를 제어할 때 실패한 센서 백업 및 연결이 끊긴 센서에 대한 경고를 전달할 수 있습니다.

## <a name="sensor-troubleshooting-tools"></a>센서 문제 해결 도구

### <a name="investigate-password-failure-at-initial-sign-in"></a>초기 로그인 시 암호 오류 조사

미리 구성된 화살표 센서에 처음으로 로그인할 때 암호 복구를 수행해야 합니다.

**암호를 복구 하려면**:

1. IoT 용 Defender 로그인 화면에서  **암호 복구** 를 선택 합니다. **암호 복구** 화면이 열립니다.

1. **CyberX** 또는 **지원** 을 선택하고 고유 식별자를 복사합니다.

1. Azure Portal로 이동하고 **사이트 및 센서** 를 선택합니다.  

1. **추가 작업** 드롭다운 메뉴를 선택하고 **온-프레미스 관리 콘솔 암호 복구** 를 선택합니다.

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text=" 복구 온-프레미스 관리 콘솔 암호 옵션의 스크린샷":::

1. **암호 복구** 화면에서 받은 고유 식별자를 입력하고 **복구** 를 선택합니다. `password_recovery.zip` 파일이 다운로드됩니다.

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="에서 고유한 식별자를 입력 한 다음 복구를 선택 합니다.":::

    > [!NOTE]
    > 암호 복구 파일을 변경하지 마세요. 이는 서명된 파일이며 변조하는 경우에는 작동하지 않습니다.

1. **암호 복구** 화면에서 **업로드** 를 선택합니다. **암호 복구 파일 업로드** 창이 열립니다.

1. **찾아보기** 를 선택하여 `password_recovery.zip` 파일을 찾거나 `password_recovery.zip`을 창으로 끌어 놓습니다.

1. **다음** 을 선택하면 관리 콘솔에 사용할 수 있는 사용자, 그리고 시스템에서 생성된 암호가 표시됩니다.

    > [!NOTE]
    > 처음으로 센서 또는 온-프레미스 관리 콘솔에 로그인하면 연결된 구독에 연결됩니다. CyberX 또는 지원 사용자에 대한 암호를 다시 설정해야 하는 경우 해당 구독을 선택해야 합니다. CyberX 복구 또는 사용자 암호 지원에 대한 자세한 내용은 [온-프레미스 관리 콘솔 또는 센서의 암호 복구](how-to-create-and-manage-users.md#recover-the-password-for-the-on-premises-management-console-or-the-sensor)를 참조하세요.

### <a name="investigate-a-lack-of-traffic"></a>트래픽 부족 조사

센서는 구성된 포트 중 하나에 트래픽이 없음을 인식하면 콘솔 맨 위에 표시기가 나타납니다. 이 표시기는 모든 사용자에게 표시됩니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="트래픽이 검색되지 않았다는 경고 스크린샷":::

이 메시지가 표시되면 트래픽이 없는 위치를 조사할 수 있습니다. 범위 케이블이 연결되어 있고 범위 아키텍처에 변경 내용이 없는지 확인합니다.  

지원 및 문제 해결 정보는 [Microsoft 지원 서비스](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에 문의하세요.

### <a name="check-system-performance"></a>시스템 성능을 확인합니다.

새 센서가 배포되거나, 예를 들어 센서가 느리게 작동하거나 경고를 표시하지 않는 경우 시스템 성능을 확인할 수 있습니다.

**시스템 성능을 확인 하려면**:

1. 대시보드에서 `PPS > 0`인지 확인합니다.

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="샘플 대시보드의 스크린샷":::

1. 측면 메뉴에서 **디바이스** 를 선택합니다.

1. **디바이스** 창에서 디바이스가 검색되고 있는지 확인합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="검색 된 장치의 스크린샷":::

1. 측면 메뉴에서 **데이터 마이닝** 을 선택합니다.

1. **데이터 마이닝** 창에서 **모두** 를 선택하고 보고서를 생성합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="데이터 마이닝을 사용 하 여 새 보고서 생성 화면 스크린샷":::

1. 보고서에 데이터가 포함되어 있는지 확인합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="의 스크린샷 보고서에 데이터 화면이 포함 되어 있는지 확인 합니다.":::

1. 측면 메뉴에서 **추세 및 통계** 를 선택합니다.

1. **추세 및 통계** 창에서 **위젯 추가** 를 선택합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="위젯 추가를 선택 하 여 위젯을 추가 합니다.":::

1. 위젯을 추가하고 데이터를 표시하는지 확인합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="데이터를 표시 하는 위젯의 스크린샷":::

1. 측면 메뉴에서 **경고** 를 선택합니다. **경고** 창이 나타납니다.

1. 경고가 생성되었는지 확인합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="경고의 스크린샷을 만들었습니다.":::

### <a name="investigate-a-lack-of-expected-alerts-on-the-sensor"></a>센서에 대해 예상 되는 경고가 없다는 것을 조사 합니다.

**경고** 창에 필요한 경고가 표시되지 않으면 다음을 확인합니다.

- 동일한 경고가 **경고** 창에 다른 보안 인스턴스에 대한 반응으로 이미 나타나는지 확인합니다. 그렇다면 이 경고가 아직 처리되지 않은 경우에는 센서 콘솔에 새 경고가 표시되지 않습니다.

- 관리 콘솔에서 **경고 제외** 규칙을 사용하여 이 경고를 제외하지 않았는지 확인합니다.

### <a name="investigate-widgets-that-show-no-data"></a>데이터를 표시하지 않는 위젯 조사

위젯의 **추세 및 통계** 창에 데이터가 표시되지 않으면 다음을 수행합니다.

- [시스템 성능을 검사합니다](#check-system-performance).

- 시간 및 지역 설정이 제대로 구성되어 있고 이후 시간으로 설정되어 있지 않은지 확인합니다.

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>브로드캐스팅 디바이스만 표시하는 디바이스 맵 조사

맵에 표시된 디바이스가 서로 연결되어 있지 않으면 SPAN 포트 구성에 문제가 있을 수 있습니다. 즉, 브로드캐스팅 디바이스만 표시되고 유니캐스트 트래픽은 표시되지 않을 수 있습니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="브로드캐스팅 장치의 스크린샷":::

이 경우 브로드캐스트 트래픽만 확인한 다음, 유니캐스트 트래픽도 볼 수 있도록 네트워크 엔지니어에게 SPAN 포트 구성을 수정하도록 요청합니다.

브로드캐스트 트래픽만 표시되는지 확인하려면

- **데이터 마이닝** 화면에서 **모두** 옵션을 사용하여 보고서를 만듭니다. 그런 다음, 브로드캐스트 및 멀티캐스트 트래픽만 보고서에 표시되는지 확인합니다(유니캐스트 트래픽은 없음).

또는

- 스위치에서 직접 PCAP를 기록하거나 Wireshark를 사용하여 랩톱을 연결합니다.

### <a name="connect-the-sensor-to-ntp"></a>NTP에 센서 연결

독립 실행형 센서 및 센서가 연결된 관리 콘솔을 구성하여 NTP에 연결할 수 있습니다.

독립 실행형 센서를 NTP에 연결하려면

- [지원 팀에 도움을 요청하세요](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

관리 콘솔에서 제어하는 센서를 NTP에 연결하려면

- NTP에 대한 연결은 관리 콘솔에서 구성됩니다. 관리 콘솔에서 제어하는 모든 센서가 NTP 연결을 자동으로 가져옵니다.

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>맵에 디바이스가 표시되지 않거나 인터넷 관련 경고가 여러 개 있는 경우 조사

경우에 따라 ICS 디바이스는 외부 IP 주소로 구성됩니다. 이러한 ICS 디바이스는 맵에 표시되지 않습니다. 디바이스 대신, 인터넷 클라우드가 맵에 표시됩니다. 이러한 디바이스의 IP 주소는 클라우드 이미지에 포함됩니다.

동일한 문제가 있다는 또 다른 표시는 인터넷 관련 경고가 여러 개 나타나는 경우입니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="여러 인터넷 관련 경고의 스크린샷":::

**구성을 수정 하려면**:

1. 디바이스 맵의 클라우드 아이콘을 마우스 오른쪽 단추로 클릭하고 **IP 주소 내보내기** 를 선택합니다. 프라이빗인 퍼블릭 범위를 복사하고 서브넷 목록에 추가합니다. 자세한 내용은 [서브넷 구성](how-to-control-what-traffic-is-monitored.md#configure-subnets)을 참조하세요.

1. 인터넷 연결에 대한 새 데이터 마이닝 보고서를 생성합니다.

1. 데이터 마이닝 보고서에서 :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false":::를 선택하여 관리자 모드를 시작하고 ICS 디바이스의 IP 주소를 삭제합니다.

### <a name="tweak-the-sensors-quality-of-service-qos"></a>센서의 QoS(서비스 품질) 조정

네트워크 리소스를 저장하기 위해 센서가 일상적인 절차에 사용하는 인터페이스 대역폭을 제한할 수 있습니다.

인터페이스 대역폭을 제한하려면 sudo 권한으로 실행해야 하는 `cyberx-xsense-limit-interface` CLI 도구를 사용합니다. 이 도구는 다음과 같은 인수를 가져옵니다.

- `* -i`: 인터페이스(예: eth0)

- `* -l`: 제한(예: 30kbit/1mbit) Kbps, mbps, kbit, mbit 또는 bps와 같은 대역폭 단위를 사용할 수 있습니다.

- `* -c`: 지우기(인터페이스 대역폭 제한 지우기)

**QoS (서비스 품질)를 조정 하려면 다음을 수행 합니다**.

1. 센서 CLI에 Defender for IoT 사용자 권한으로 로그인하고 `sudo cyberx-xsense-limit-interface-I eth0 -l value`를 입력합니다.

   예: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > 실제 어플라이언스의 경우 em1 인터페이스를 사용합니다.

1. 인터페이스 제한을 지우려면 `sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c`를 입력합니다.

## <a name="on-premises-management-console-troubleshooting-tools"></a>온-프레미스 관리 콘솔 문제 해결 도구

### <a name="investigate-a-lack-of-expected-alerts-on-the-management-console"></a>관리 콘솔에서 예상 하지 못하는 경고 조사

**경고** 창에 예상되는 경고가 표시되지 않으면 다음을 확인합니다.

- 동일한 경고가 **경고** 창에 다른 보안 인스턴스에 대한 반응으로 이미 나타나는지 확인합니다. 이 경우에 해당하며 이 경고를 아직 처리하지 않았으면 새 경고가 표시되지 않습니다.

- 온-프레미스 관리 콘솔에서 **경고 제외** 규칙을 사용하여 이 경고를 제외하지 않았는지 확인합니다.  

### <a name="tweak-the-quality-of-service-qos"></a>QoS(서비스 품질) 조정

네트워크 리소스를 저장하기 위해 어플라이언스와 온-프레미스 관리 콘솔 간의 단일 동기화 작업에서 외부 시스템(예: 메일 또는 SIEM)으로 전송되는 경고 수를 제한할 수 있습니다.

기본값은 50입니다. 즉, 어플라이언스와 온-프레미스 관리 콘솔 간의 단일 통신 세션에서 외부 시스템에 대한 경고는 50개 이하여야 합니다. 

경고 수를 제한하려면 `/var/cyberx/properties/management.properties`에서 사용할 수 있는 `notifications.max_number_to_report` 속성을 사용합니다. 이 속성을 변경한 후에는 다시 시작할 필요가 없습니다.

**QoS (서비스 품질)를 조정 하려면 다음을 수행 합니다**.

1. Defender for IoT 사용자로 로그인합니다.

1. 다음과 같이 기본값을 확인합니다.

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   다음 기본값이 표시됩니다.

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. 기본 설정을 편집합니다.

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. 다음 줄의 설정을 편집합니다.

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. 변경 내용을 저장합니다. 다시 시작하지 않아도 됩니다.

## <a name="export-information-from-the-sensor-for-troubleshooting"></a>문제 해결을 위해 센서에서 정보 내보내기

네트워크를 모니터링하고 분석하기 위한 도구 외에, 추가 조사를 위한 정보를 지원 팀에 보낼 수 있습니다. 로그를 내보낼 때 센서는 내보낸 로그에 대해 고유한 OTP(일회용 암호)를 별도의 텍스트 파일에 자동으로 생성합니다.

**로그를 내보내려면**:

1. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. **로그 내보내기** 를 선택합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/sensor-export-log.png" alt-text="시스템 지원으로 로그 내보내기 화면의 스크린샷":::

1. **파일 이름** 필드에 로그 내보내기에 사용할 파일 이름을 입력 합니다. 기본값은 현재 날짜입니다.

1. 내보낼 데이터를 정의하려면 데이터 범주를 선택합니다.  

    | 범주 내보내기 | Description |
    |--|--|
    | **운영 체제 로그** | 운영 체제 상태에 대한 정보를 가져오려면 이 옵션을 선택합니다. |
    | **설치/업그레이드 로그** | 설치 및 업그레이드 구성 매개 변수를 조사하려면 이 옵션을 선택합니다. |
    | **시스템 온전성 출력** | 시스템 성능을 확인하려면 이 옵션을 선택합니다. |
    | **분석 로그** | 프로토콜 분석에 대한 고급 검사를 허용하려면 이 옵션을 선택합니다. |
    | **OS 커널 덤프** | 커널 메모리 덤프를 내보내려면 이 옵션을 선택합니다. 커널 메모리 덤프에는 이 커널에서 해당 문제가 발생한 시간에 커널이 사용하고 있던 모든 메모리가 포함됩니다. 덤프 파일의 크기가 전체 메모리 덤프보다 작습니다. 일반적으로 덤프 파일은 시스템 실제 메모리 크기의 1/3입니다. |
    | **로그 전달** | 전달 규칙을 조사하려면 이 옵션을 선택합니다. |
    | **SNMP 로그** | SNMP 상태 검사 정보를 수신하려면 이 옵션을 선택합니다. |
    | **핵심 애플리케이션 로그** | 핵심 애플리케이션 구성 및 작업에 대한 데이터를 내보내려면 이 옵션을 선택합니다. |
    | **CM 로그와 통신** | 관리 콘솔과의 연결 중단이 지속적으로 발생하는 경우 이 옵션을 선택합니다. |
    | **웹 애플리케이션 로그** | 애플리케이션의 웹 인터페이스에서 보낸 모든 요청에 대한 정보를 가져오려면 이 옵션을 선택합니다. |
    | **시스템 백업**: | 시스템의 정확한 상태를 조사하기 위해 모든 시스템 데이터의 백업을 내보내려면 이 옵션을 선택합니다. |
    | **분석 통계** | 프로토콜 통계에 대한 고급 검사를 허용하려면 이 옵션을 선택합니다. |
    | **데이터베이스 로그** | 시스템 데이터베이스에서 로그를 내보내려면 이 옵션을 선택합니다. 시스템 로그를 조사하면 시스템 문제를 식별하는 데 도움이 됩니다. |
    | **Configuration** | 모든 구성 가능한 매개 변수에 대한 정보를 내보내 모든 구성 요소가 올바르게 구성되었는지 확인하려면 이 옵션을 선택합니다. |

1. 모든 옵션을 선택하려면 **범주 선택** 옆에 있는 **모두 선택** 을 선택합니다.

1. **로그 내보내기** 를 선택합니다.

내보낸 로그가 **보관된 로그** 목록에 추가됩니다. 별도 메시지 및 내보낸 로그의 미디어 형태로 OTP를 지원 팀에 보냅니다. 지원 팀에서는 로그를 암호화하는 데 사용되는 고유 OTP를 사용하여 내보낸 로그를 추출할 수 있습니다.

보관된 로그 목록에는 최대 5개의 항목이 포함될 수 있습니다. 목록의 항목 수가 해당 이 개수를 벗어나면 가장 먼저 보관된 항목이 삭제됩니다.

## <a name="export-audit-log-from-the-management-console"></a>관리 콘솔에서 감사 로그 내보내기

감사 로그는 발생 시 키 정보를 기록 합니다. 감사 로그는 변경 된 내용 및 사용자를 확인 하려는 경우에 유용 합니다. 감사 로그는 관리 콘솔에서 내보낼 수 있으며 다음 정보를 포함 합니다.

| 작업 | 기록 되는 정보 |
|--|--|
| **경고 학습 및 수정** | 경고 ID |
| **암호 변경** | 사용자, 사용자 ID |
| **로그인** | 사용자 |
| **사용자 만들기** | 사용자, 사용자 역할 |
| **암호 재설정** | 사용자 이름 |
| **제외 규칙**: </br></br>- 만들기 </br></br>- 편집 </br></br>- 삭제 | </br></br>규칙 요약 </br></br>규칙 ID, 규칙 요약 </br></br>규칙 ID |
| **관리 콘솔 업그레이드** | 사용된 업그레이드 파일 |
| **센서 업그레이드 다시 시도** | 센서 ID |
| **업로드된 TI 패키지** | 기록된 추가 정보가 없습니다. |

**감사 로그를 내보내려면:**

1. 관리 콘솔의 왼쪽 창에서 시스템 **설정** 를 선택합니다.

1. **내보내기** 를 선택합니다.

1. 파일 이름 필드에 내보낸 로그에 사용할 파일 이름을 입력합니다. 이름을 입력하지 않으면 기본 파일 이름이 현재 날짜가 됩니다.

1. **감사 로그를** 선택합니다.

1. **내보내기** 를 선택합니다.

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/audit-logs-export.png" alt-text="감사 로그를 선택한 다음 내보내기를 선택하여 파일을 만드는 스크린샷.":::

내보낸 로그가 보관 로그 목록에 **추가됩니다.** :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/eye-icon.png" border="false":::OTP를 보려면 단추를 선택합니다. 내보낸 로그와 별도의 메시지로 지원 팀에 OTP 문자열을 보냅니다. 지원 팀에서는 로그를 암호화하는 데 사용되는 고유 OTP를 사용하여 내보낸 로그를 추출할 수 있습니다.

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/archived-files.png" alt-text="문제 해결 정보 내보내기 창의 보관된 파일 섹션에서 만든 파일의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

- [경고 보기](how-to-view-alerts.md)

- [SNMP MIB 모니터링 설정](how-to-set-up-snmp-mib-monitoring.md)

- [센서 연결 끊기 이벤트의 이해](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
