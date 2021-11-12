---
title: 온-프레미스 관리 콘솔에서 센서 관리
description: 센서 버전 업데이트, 센서에 시스템 설정 푸시, 인증서 관리, 센서에서 엔진 사용 및 사용 안 함을 포함 하 여 관리 콘솔에서 센서를 관리 하는 방법에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 1015810f8999671665cf48d74058e9ad3fc47558
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278838"
---
# <a name="manage-sensors-from-the-management-console"></a>관리 콘솔에서 센서 관리

이 문서는 다음을 포함하여 관리 콘솔에서 센서를 관리하는 방법을 설명합니다.

- 센서에 시스템 설정 푸시

- 센서의 엔진 사용 설정 및 사용 중지

- 센서 버전 업데이트

## <a name="push-configurations"></a>푸시 구성

다양한 시스템 설정을 정의하고 관리 콘솔에 연결된 센서에 자동으로 적용할 수 있습니다. 이렇게 하면 시간을 절약하고 엔터프라이즈 센서를 통해 원활한 설정을 보장할 수 있습니다.

관리 콘솔에서 다음과 같은 센서 시스템 설정을 정의할 수 있습니다.

- 메일 서버

- SNMP MIB 모니터링

- Active Directory

- DNS 설정

- 서브넷

- 포트 별칭

**시스템 설정을 적용** 하려면 다음을 수행합니다.

1. 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. **센서 구성** 창에서 옵션 중 하나를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="센서에 대한 시스템 설정 옵션.":::

   다음 예는 엔터프라이즈 센서에 대한 메일 서버 매개 변수를 정의하는 방법을 설명합니다.

1. **메일 서버** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="시스템 설정 화면에서 메일 서버를 선택합니다.":::

1. 왼쪽에서 센서를 선택합니다.

1. 메일 서버 매개 변수를 설정하고 **복제** 를 선택합니다. 센서 트리의 각 항목 옆에 확인란이 표시됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="센서의 확인란이 선택되었는지 확인합니다.":::

1. 센서 트리에서 구성을 적용할 항목을 선택합니다.

1. **저장** 을 선택합니다.

## <a name="update-sensor-versions"></a>센서 버전 업데이트

온-프레미스 관리 콘솔에서 동시에 여러 센서를 업데이트할 수 있습니다.

### <a name="update-sequence"></a>업데이트 시퀀스

온-프레미스 관리 콘솔과 관리형 센서를 업그레이드할 때 먼저 관리 콘솔을 업데이트한 다음, 센서를 업데이트합니다. 온-프레미스 관리 콘솔을 먼저 업데이트하지 않으면 센서 업데이트 프로세스가 성공하지 못합니다.

**여러 센서를 업데이트** 하려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔을 센서를 업데이트하는 버전으로 이미 업데이트했는지 확인합니다. 온-프레미스 관리 콘솔 업데이트에 대한 자세한 내용은 [소프트웨어 버전 업데이트](how-to-manage-the-on-premises-management-console.md#update-the-software-version)를 참조하세요.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

1. IoT 용 Microsoft Defender로 이동 합니다.

1. **업데이트** 페이지로 이동합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="업데이트 대시보드 보기의 스크린샷":::

1. **센서** 섹션에서 **다운로드** 를 선택하고 파일을 저장합니다.

1. 관리 콘솔에 로그인하고 **시스템 설정** 을 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="시스템 설정을 선택하는 관리 메뉴의 스크린샷":::

1. **센서 엔진 구성** 섹션에서 업데이트할 센서를 선택한 다음, **자동 업데이트** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="학습 모드와 자동 업데이트를 보여 주는 두 개의 센서":::

1. **변경 내용 저장** 을 선택합니다.

1. 관리 콘솔에서 **시스템 설정** 을 선택합니다.
1. 센서 버전 업데이트 섹션에서 :::image type="icon" source="../media/how-to-manage-sensors-from-the-on-premises-management-console/add-icon.png" border="false"::: 단추를 선택합니다.

    :::image type="content" source="../media/how-to-manage-sensors-from-the-on-premises-management-console/sendor-version-update-window.png" alt-text="센서 버전 업데이트 창에서 + 아이콘을 선택하여 관리 콘솔에 연결된 모든 센서를 업데이트합니다.":::

9. **파일 업로드** 대화 상자가 열립니다. **업데이트** 페이지에서 다운로드한 파일을 업로드합니다.

    :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="찾아보기 단추를 선택하여 파일을 업로드합니다.":::

**사이트 관리** 창에서 각 센서의 업데이트 상태를 모니터링할 수 있습니다.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="업데이트의 진행률을 관찰합니다.":::

### <a name="update-sensors-from-the-on-premises-management-console"></a>온-프레미스 관리 콘솔에서 센서 업데이트

관리 콘솔에서 센서의 업데이트 상태를 볼 수 있습니다. 업데이트에 실패한 경우 온-프레미스 관리 콘솔(버전 2.3.5 이상)에서 센서를 다시 업데이트할 수 있습니다.

**온-프레미스 관리 콘솔에서 센서를 업데이트하려면 다음을 수행합니다.**

1. 온-프레미스 관리 콘솔에 로그인하고 **사이트 관리** 페이지로 이동합니다.

1. 업데이트 진행률 열 아래에서 **실패한** 센서를 찾고 다운로드 단추를 선택합니다. 

    :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/download-update-button.png" alt-text="다운로드 아이콘을 선택하여 센서에 대한 업데이트를 다운로드하고 설치합니다.":::

**사이트 관리** 창에서 각 센서의 업데이트 상태를 모니터링할 수 있습니다.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="업데이트의 진행률을 관찰합니다.":::

센서를 업데이트할 수 없는 경우 고객 지원 서비스에 문의하여 도움을 요청합니다.

## <a name="update-threat-intelligence-packages"></a>위협 인텔리전스 패키지 업데이트 

위협 인텔리전스의 데이터 패키지는 새로운 Defender for IoT 각 버전에 제공되거나 필요한 경우 릴리스 사이에 제공됩니다. 패키지에는 서명(맬웨어 서명 포함), CVE, 기타 보안 콘텐츠가 포함됩니다. 

Azure Portal에서이 파일을 수동으로 업로드 하 고 센서에 자동으로 업데이트할 수 있습니다. 

**위협 인텔리전스 데이터를 업데이트하려면 다음을 수행합니다.**

1. Defender for IoT **업데이트** 페이지로 이동합니다. 

1. 파일을 다운로드하고 저장합니다.

1. 관리 콘솔에 로그인합니다. 

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다. 

1. **센서 엔진 구성** 섹션에서 업데이트를 받을 센서를 선택합니다.  

1. **위협 인텔리전스 데이터 선택** 섹션에서 더하기 기호( **+** )를 선택합니다. 

1. Defender for IoT **업데이트** 페이지에서 다운로드한 패키지를 업로드합니다.

## <a name="understand-sensor-disconnection-events"></a>센서 연결 끊기 이벤트의 이해

할당된 온-프레미스 관리 콘솔에서 센서의 연결을 끊으면 **Site Manager** 창에 연결 끊기 정보가 표시됩니다. 다음 센서 연결 끊기 정보를 사용할 수 있습니다.

- “온-프레미스 관리 콘솔은 센서에서 받은 데이터를 처리할 수 없습니다.”

- “시간 드리프트가 감지되었습니다. 온-프레미스 관리 콘솔을 센서에서 분리했습니다.”

- “센서가 온-프레미스 관리 콘솔과 통신하지 않습니다. 네트워크 연결 또는 인증서 유효성 검사를 확인하세요”

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="영역 1 뷰의 스크린샷.":::

연결되지 않은 센서에 대한 정보를 사용하여 타사에 경고를 보낼 수 있습니다. 자세한 내용은 [센서 실패 경고 전달](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)을 참조하세요.

## <a name="enable-or-disable-sensors"></a>센서 사용 또는 사용 안 함

센서는 Defender for IoT 엔진 다섯 개에 의해 보호됩니다. 연결된 센서에 대해 엔진을 사용하거나 사용하지 않도록 설정할 수 있습니다.

| 엔진 | Description | 예제 시나리오 |
|--|--|--|
| 프로토콜 위반 엔진 | 패킷 구조 또는 필드 값이 프로토콜 사양을 따르지 않으면 프로토콜 위반이 발생합니다. | “잘못된 MODBUS 작업(함수 코드 0)” 경고입니다. 이 경고는 기본 디바이스에서 함수 코드가 0인 요청을 보조 디바이스로 보냈음을 나타냅니다. 이는 프로토콜 사양에 따라 허용되지 않으며, 보조 디바이스에서 입력을 올바르게 처리하지 못할 수 있습니다. |
| 정책 위반 엔진 | 정책 위반은 학습 또는 구성된 정책에 정의된 기준 동작의 편차에 따라 발생합니다. | “권한이 없는 HTTP 사용자 에이전트” 경고입니다. 이 경고는 정책에 의해 학습 또는 승인되지 않은 애플리케이션이 디바이스에서 HTTP 클라이언트로 사용됨을 나타냅니다. 이는 해당 디바이스의 새 웹 브라우저 또는 애플리케이션일 수 있습니다. |
| 맬웨어 엔진 | 맬웨어 엔진이 악의적인 네트워크 작업을 검색합니다. | “악성 작업 의심(Stuxnet)” 경고입니다. 이 경고는 센서가 산업용 제어 및 SCADA 네트워크를 목표로 하는 고급 영구 위협인 Stuxnet 맬웨어에 관련된 것으로 알려진 의심스러운 네트워크 활동을 발견했음을 나타냅니다. |
| 변칙 엔진 | 맬웨어 엔진이 네트워크 동작의 비정상을 감지합니다. | “통신 채널의 주기적인 동작”입니다. 이 구성 요소는 네트워크 연결을 검사하고 산업용 네트워크에서 공통적으로 발생하는 데이터 전송의 주기적 또는 순환 동작을 찾는 구성 요소입니다. |
| 운영 엔진 | 이 엔진은 작업 인시던트 또는 작동하지 않는 엔터티를 검색합니다. | `Device is Suspected to be Disconnected (Unresponsive)` 경고입니다. 이 경고는 디바이스가 미리 정의된 기간 동안 요청에 응답하지 않을 때 트리거됩니다. 디바이스 종료, 연결 끊기 또는 오작동을 나타낼 수 있습니다.
|

**연결된 센서에 대해 엔진을 사용하거나 사용하지 않도록 설정하려면 다음을 수행합니다.**

1. 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택합니다.

1. **센서 엔진 구성** 섹션에서 엔진에 대해 **사용** 또는 **사용 안 함** 을 선택합니다.
         
1. **변경 내용 저장** 을 선택합니다.

   엔터프라이즈 센서 중 하나에서 사용 설정된 엔진이 일치하지 않으면 빨간색 느낌표가 표시됩니다. 엔진이 센서에서 직접 사용하지 않도록 설정되었을 수 있습니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="사용 설정된 엔진이 일치하지 않습니다."::: 

## <a name="define-sensor-backup-schedules"></a>센서 백업 일정 정의

온-프레미스 관리 콘솔에서 센서 백업을 예약하고 주문형 센서 백업을 수행할 수 있습니다. 이렇게 하면 하드 드라이브 오류 및 데이터 손실을 방지할 수 있습니다.

- 백업되는 항목: 구성 및 데이터.

- 백업되지 않는 항목: PCAP 파일 및 로그. PCAP 및 로그를 수동으로 백업하고 복원할 수 있습니다.

기본적으로 센서는 매일 오전 3시에 자동으로 백업됩니다. 센서의 백업 일정 기능을 사용하면 해당 백업을 수집하여 온-프레미스 관리 콘솔 또는 외부 백업 서버에 저장할 수 있습니다. 파일은 암호화된 채널을 통해 센서에서 온-프레미스 관리 콘솔로 복사됩니다.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="센서 백업 화면의 뷰.":::

기본 센서 백업 위치가 변경되면 콘솔에 해당 위치에 대한 액세스 권한이 있는 경우 온-프레미스 관리 콘솔이 센서의 새 위치 또는 외부 위치에서 파일을 자동으로 검색합니다. 

센서가 온-프레미스 관리 콘솔에 등록되어 있지 않으면 **센서 백업 일정** 대화 상자가 관리되는 센서가 없음을 나타냅니다.  

복원 프로세스는 파일이 저장된 위치와 관계없이 동일합니다.

### <a name="backup-storage-for-sensors"></a>센서에 대한 백업 스토리지

백업된 파일이 할당된 최대 백업 공간을 초과하지 않는 경우 온-프레미스 관리 콘솔을 사용하여 각 관리 센서에 대해 최대 9개의 백업을 유지할 수 있습니다. 

사용 가능한 공간은 사용 중인 관리 콘솔 모델을 기반으로 계산됩니다. 

- **프로덕션 모델**: 기본 스토리지는 40GB입니다. 한도는 100GB입니다. 

- **중간 규모 모델**: 기본 스토리지는 20GB입니다. 한도는 50GB입니다. 

- **랩톱 모델**: 기본 스토리지는 10GB입니다. 한도는 25GB입니다. 

- **씬 모델**: 기본 스토리지는 2GB입니다. 한도는 4GB입니다. 

- **러기드 모델**: 기본 스토리지는 10GB입니다. 한도는 25GB입니다. 

기본 할당은 **센서 백업 일정** 대화 상자에 표시됩니다. 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="메일 서버 구성 편집 화면.":::

외부 서버에 백업하는 경우 스토리지 한도가 없습니다. 그러나 **센서 백업 일정** > **사용자 지정 경로** 필드에서 상한 할당 한도를 정의해야 합니다. 다음 숫자 및 문자가 지원됩니다. `/, a-z, A-Z, 0-9, and _` 

다음은 할당 스토리지 한도 초과에 대한 정보입니다.

- 할당된 스토리지 공간을 초과하는 경우 센서가 백업되지 않습니다. 

- 둘 이상의 센서를 백업하는 경우 관리 콘솔에서 관리되는 센서에 대한 센서 파일을 검색하려고 합니다.  

- 한 센서의 검색이 한도를 초과하는 경우 관리 콘솔은 다음 센서에서 백업 정보를 검색하려고 합니다. 

정의된 유지 백업 수를 초과하면 가장 오래된 백업 파일이 삭제되어 새 백업 파일이 적용됩니다.

센서 백업 파일은 `<sensor name>-backup-version-<version>-<date>.tar` 형식으로 이름이 자동 지정됩니다. 예: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar` 

**센서를 백업하려면 다음을 수행합니다.**

1. **시스템 설정** 창에서 **센서 백업 예약** 을 선택합니다. 온-프레미스 관리 콘솔에서 관리하는 센서가 **센서 백업 일정** 대화 상자에 표시됩니다.  

1. **백업 수집** 설정/해제를 사용하도록 설정합니다.  

1. 일정 간격, 날짜, 표준 시간대를 선택합니다. 시간 형식은 24시간 시계를 기준으로 합니다. 예를 들어 오후 6:00는 **18:00** 으로 입력합니다. 

1. **백업 스토리지 할당** 필드에 백업에 할당하려는 스토리지를 입력합니다. 최대 공간을 초과하는 경우 알림이 표시됩니다.

1. **마지막 보존** 필드에서 보존하려는 센서당 백업 수를 지정합니다. 제한을 초과하면 가장 오래된 백업이 삭제됩니다.  

1. 백업 위치 선택:  

   - 온-프레미스 관리 콘솔에 백업하려면 **사용자 지정 경로** 설정/해제를 사용하지 않도록 설정합니다. 기본 위치는 `/var/cyberx/sensor-backups`입니다.  

   - 외부 서버에 백업하려면 **사용자 지정 경로** 설정/해제를 사용하도록 설정하고 위치를 입력합니다. 다음 숫자 및 문자가 지원됩니다. `/, a-z, A-Z, 0-9, and, _` 

1. **저장** 을 선택합니다. 

**즉시 백업하려면 다음을 수행합니다.**

- **지금 백업** 을 선택합니다. 온-프레미스 관리 콘솔이 센서 백업 파일을 만들고 수집합니다. 

### <a name="receiving-backup-notifications-for-sensors"></a>센서에 대한 백업 알림 받기 

**센서 백업 일정** 대화 상자와 백업 로그에 백업 성공 및 실패에 대한 정보가 자동으로 나열됩니다.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="센서 및 해당 센서의 위치와 모든 관련 정보를 확인합니다.":::

다음 이유로 인해 오류가 발생할 수 있습니다.    

- 백업 파일을 찾을 수 없습니다. 

- 파일이 있지만 검색할 수 없습니다.  

- 네트워크 연결에 오류가 있습니다. 

- 온-프레미스 관리 콘솔에 할당된 공간이 부족하여 백업을 완료할 수 없습니다.  

오류가 발생하면 메일 알림, syslog 업데이트, 시스템 알림을 보낼 수 있습니다. 이렇게 하려면 **시스템 알림** 에서 전달 규칙을 만듭니다. 

### <a name="restoring-sensors"></a>센서 복원 

온-프레미스 관리 콘솔과 CLI를 사용하여 백업을 복원할 수 있습니다.  

**콘솔에서 복원하려면 다음을 수행합니다.**

- **센서 시스템** 설정 창에서 **이미지 복원** 을 선택합니다.

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="이미지 백업을 복원합니다.":::

  그러면 콘솔에 복원 오류가 표시됩니다.  

CLI를 사용하여 복원하려면 다음을 수행합니다. 

- 관리 계정에 로그인하고 `$ sudo cyberx-xsense-system-restore`를 입력합니다. 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>외부 SMB 서버에 센서 백업 저장

**외부 드라이브에 센서 백업을 저장할 수 있도록 SMB 서버를 설정하려면 다음을 수행합니다.**

1. 외부 SMB 서버에 공유 폴더를 만듭니다. 

1. SMB 서버에 액세스하는 데 필요한 폴더 경로, 사용자 이름 및 암호를 가져옵니다. 

1. Defender for IoT에서 백업용 디렉터리를 만듭니다. 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

1. fstab 편집:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

1. 공유할 자격 증명을 편집하거나 만듭니다. SMB 서버에 대한 자격 증명은 다음과 같습니다. 

   `sudo nano /etc/samba/user` 

1. 추가:  

   `username=<user name>` 

   `password=<password>` 

1. 디렉터리를 탑재합니다. 

   `sudo mount -a` 

1. Defender for IoT 센서의 공유 폴더에 백업 디렉터리를 구성합니다.  

   `sudo nano /var/cyberx/properties/backup.properties` 

1. `Backup.shared_location`를 `<backup_folder_name_on_cyberx_server>`로 설정합니다.

## <a name="see-also"></a>참고 항목

[개별 센서 관리](how-to-manage-individual-sensors.md)
