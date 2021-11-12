---
title: 개별 센서 관리
description: 활성화 파일 관리, 인증서, 백업 수행 및 독립 실행형 센서 업데이트를 포함 하 여 개별 센서를 관리 하는 방법에 대해 알아봅니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: dbbf08f1e139a265b947cc99c7287765d6a6d03a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339956"
---
# <a name="manage-individual-sensors"></a>개별 센서 관리

이 문서에서는 개별 센서를 관리하는 방법을 설명합니다. 활성화 파일 관리, 백업 수행, 독립 실행형 센서 업데이트 등의 작업이 포함됩니다.

또한 여러 센서를 동시에 관리할 수 있는 온-프레미스 관리 콘솔에서 특정 센서 관리 작업을 수행할 수 있습니다.

센서 온보딩 및 등록에 Azure Portal을 사용합니다.

## <a name="manage-sensor-activation-files"></a>센서 활성화 파일 관리

센서는 Azure Portal의 IoT 용 Microsoft Defender와 등록 되었습니다. 각 센서는 로컬로 연결된 센서 또는 클라우드 연결 센서로 온보딩되었습니다.

고유한 활성화 파일은 배포하는 각 센서에 업로드됩니다. 새 파일을 사용하는 시점 및 방법에 대한 자세한 내용은 [새 활성화 파일 업로드](#upload-new-activation-files)를 참조하세요. 파일을 업로드할 수 없으면 [활성화 파일 업로드 문제 해결](#troubleshoot-activation-file-upload)을 참조하세요.

### <a name="about-activation-files-for-locally-connected-sensors"></a>로컬로 연결된 센서의 활성화 파일 정보

로컬로 연결된 센서는 Azure 구독과 연결됩니다. 로컬로 연결된 센서의 활성화 파일에는 만료 날짜가 포함되어 있습니다. 이 날짜로부터 한 달 전에 센서 콘솔 상단에 경고 메시지가 표시됩니다. 경고 메시지는 활성화 파일을 업데이트할 때까지 유지됩니다.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="시스템 설정의 스크린샷.":::

활성화 파일이 만료된 경우에도 Defender for IoT 기능을 계속 사용할 수 있습니다. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>클라우드 연결 센서에 대한 활성화 파일 정보

클라우드 연결 센서는 Defender for IoT 허브와 연결됩니다. 이러한 센서는 활성화 파일 기간의 영향을 받지 않습니다. 클라우드 연결 센서의 활성화 파일은 Defender for IoT 허브에 대한 연결을 확인하는 데 사용됩니다.

### <a name="upload-new-activation-files"></a>새 활성화 파일 업로드

다음과 같은 경우 온보딩된 센서에 대한 새 활성화 파일을 업로드해야 할 수 있습니다.

- 활성화 파일이 로컬로 연결된 센서에서 만료되는 경우. 

- 다른 센서 관리 모드에서 작업하려는 경우. 

- 클라우드 연결 센서에 새 Defender for IoT 허브를 할당하려는 경우

**새 활성화 파일을 추가하려면 다음을 수행합니다.**

1. **센서 관리** 페이지로 이동합니다.

2. 새 활성화 파일을 업로드하려는 센서를 선택합니다.

3. 삭제합니다.

4. 새 모드의 **온보딩** 페이지나 Defender for IoT 허브에서 센서를 다시 온보딩합니다.

5. **활성화 파일 다운로드** 페이지에서 활성화 파일을 다운로드합니다.

6. 파일을 저장합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Defender for IoT에서 활성화 파일을 다운로드합니다.":::

7. Defender for IoT 센서 콘솔에 로그인합니다.

8. 센서 콘솔에서 **시스템 설정** >  **다시 활성화** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="시스템 설정 화면에서 다시 활성화 선택.":::

9. **업로드** 를 선택하고 저장한 파일을 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="저장한 파일을 업로드합니다.":::

10. **활성화** 를 선택합니다.

### <a name="troubleshoot-activation-file-upload"></a>활성화 파일 업로드 문제 해결

활성화 파일을 업로드할 수 없는 경우 오류 메시지가 표시됩니다. 다음 이벤트가 발생했을 수 있습니다.

- **로컬로 연결된 센서**: 활성화 파일이 유효하지 않습니다. 파일이 유효 하지 않으면 [Azure Portal에서 IoT 용 Defender](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)로 이동 합니다. **센서 관리** 페이지에서 유효하지 않은 파일이 있는 센서를 선택하고 새 활성화 파일을 다운로드합니다.

- **클라우드 연결 센서**: 센서를 인터넷에 연결할 수 없습니다. 센서의 네트워크 구성을 확인합니다. 인터넷에 액세스하기 위해 웹 프록시를 통해 센서를 연결해야 하는 경우 **센서 네트워크 구성** 화면에서 프록시 서버가 올바르게 구성되어 있는지 확인합니다. \*.azure-devices.net:443이 방화벽 및/또는 프록시에서 허용되는지 확인합니다. 와일드카드가 지원되지 않거나 더 많은 제어를 원하는 경우 특정 Defender for IoT 허브에 대한 FQDN을 방화벽 및/또는 프록시에서 열어야 합니다. 자세한 내용은 [참조 - IoT Hub 엔드포인트](../../iot-hub/iot-hub-devguide-endpoints.md)를 참조하세요.  

- **클라우드 연결 센서의 경우**: 활성화 파일이 유효하지만 Defender for IoT가 해당 파일을 거부했습니다. 이 문제를 해결할 수 없는 경우 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)의 **사이트 및 센서** 페이지에서 다른 정품 인증을 다운로드할 수 있습니다. 그래도 문제가 해결되지 않을 경우 Microsoft 지원에 문의하세요.

## <a name="manage-certificates"></a>인증서 관리

센서 설치 후에는 로컬 자체 서명된 인증서가 생성되어 센서 웹 애플리케이션에 액세스하는 데 사용됩니다. 센서에 처음 로그인할 때 관리자 사용자에게 SSL/TLS 인증서를 제공하라는 메시지가 표시됩니다. 

센서 관리자는 초기 로그인 후에 업로드 된 인증서를 업데이트 해야 할 수 있습니다. 예를 들어 인증서가 만료 된 경우이 오류가 발생할 수 있습니다.

**인증서를 업데이트하려면 다음을 수행합니다.**

1. **시스템 설정** 을 선택합니다.

1. **SSL/TLS 인증서** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="인증서 업로드":::

1. SSL/TLS 인증서 대화 상자에서 기존 인증서를 삭제 하 고 새 인증서를 추가 합니다.

    - 인증서 이름을 추가합니다.
    - CRT 파일 및 키 파일을 업로드 합니다.
    - 필요한 경우 PEM 파일을 업로드합니다.

업로드에 실패 하는 경우 보안 또는 IT 관리자에 게 문의 하거나 [인증서](how-to-deploy-certificates.md)정보를 검토 합니다.

**인증서 유효성 검사 설정을 변경 하려면:**

1. **인증서 유효성 검사 사용** 설정/해제를 사용 하도록 설정 합니다. 이 옵션을 사용 하도록 설정 하 고 유효성 검사에 실패 하면 관련 구성 요소 간의 통신이 중단 되 고 콘솔에 유효성 검사 오류가 표시 됩니다. 사용 하지 않도록 설정 된 경우 인증서 유효성 검사가 수행 되지 않습니다. 자세한 내용은 [인증서 유효성 검사 정보](how-to-deploy-certificates.md#about-certificate-validation) 를 참조 하세요.

1. **저장** 을 선택합니다.

첫 번째 인증서 업로드에 대 한 자세한 내용은 [첫 번째 로그인 및 정품 인증 검사 목록](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist) 을 참조 하세요.

## <a name="connect-a-sensor-to-the-management-console"></a>관리 콘솔에 센서 연결

이 섹션에서는 센서와 온-프레미스 관리 콘솔 간의 연결을 확인하는 방법을 설명합니다. 개별 네트워크에서 작업 중이며 센서에서 관리 콘솔로 디바이스 및 경고 정보를 보내려는 경우 이 작업을 수행해야 합니다. 이 연결을 통해 관리 콘솔에서 센서에 시스템 설정을 푸시하고 센서에서 다른 관리 작업을 수행할 수도 있습니다.

연결하려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔에 로그인합니다.

2. **시스템 설정** 을 선택합니다.

3. **센서 설정 – 연결 문자열** 섹션에서 자동으로 생성된 연결 문자열을 복사합니다.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="이 화면에서 연결 문자열을 복사합니다."::: 

4. 센서 콘솔에 로그인합니다.

5. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

6. **관리 콘솔 연결** 을 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="관리 콘솔 연결 대화 상자의 스크린샷.":::

7. **연결 문자열** 상자에 연결 문자열을 붙여넣고 **연결** 을 선택합니다.

8. 온-프레미스 관리 콘솔의 **사이트 관리** 창에서 센서를 영역에 할당합니다.

## <a name="change-the-name-of-a-sensor"></a>센서 이름 변경

센서 콘솔의 이름을 변경할 수 있습니다. 새 이름이 콘솔 웹 브라우저, 다양한 콘솔 창, 문제 해결 로그에 표시됩니다.

센서 이름을 변경하는 프로세스는 로컬로 연결된 센서 및 클라우드 연결 센서에 따라 다릅니다. 기본 이름은 **센서** 입니다.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>로컬로 연결된 센서의 이름 변경

이름을 변경하는 방법은 다음과 같습니다.

1. 콘솔 왼쪽 창의 맨 아래에서 현재 센서 레이블을 선택합니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="센서 레이블을 표시하는 스크린샷.":::

1. **센서 이름 편집** 대화 상자에서 이름을 입력합니다.

1. **저장** 을 선택합니다. 새 이름이 적용됩니다.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>클라우드 연결 센서의 이름 변경

센서가 클라우드 연결 센서로 등록된 경우 센서 이름은 등록하는 동안 할당된 이름으로 정의됩니다. 이 이름은 처음으로 로그인할 때 업로드한 활성화 파일에 포함됩니다. 센서의 이름을 변경하려면 새 활성화 파일을 업로드해야 합니다.

이름을 변경하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)사이트 및 센서 페이지로 이동합니다.

1. 사이트 및 센서 페이지에서 센서를 삭제합니다.

1. 시작 페이지에서 **온보딩 센서** 를 선택하여 새 이름으로 등록합니다.

1. 새 활성화 파일을 다운로드합니다.

1. Defender for IoT 센서 콘솔에 로그인합니다.

1. 센서 콘솔에서 **시스템 설정** 을 선택한 다음 **다시 활성화** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="활성화 파일을 업로드하여 센서를 다시 활성화합니다.":::

1. **업로드** 를 선택하고 저장한 파일을 선택합니다.

1. **활성화** 를 선택합니다.

## <a name="update-the-sensor-network-configuration"></a>센서 네트워크 구성 업데이트

센서 네트워크 구성은 센서 설치 중에 정의되었습니다. 구성 매개 변수를 변경할 수 있습니다. 프록시 구성을 설정할 수도 있습니다.

새 IP 주소를 만드는 경우 다시 로그인해야 할 수 있습니다.

구성을 변경하려면 다음을 수행합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **설정** 창에서 **네트워크** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="네트워크 설정을 구성합니다.":::

3. 매개 변수 설정:

    | 매개 변수 | Description |
    |--|--|
    | IP 주소 | 센서 IP 주소 |
    | 서브넷 마스크 | 마스크 주소 |
    | 기본 게이트웨이 | 기본 게이트웨이 주소 |
    | DNS | DNS 서버 주소 |
    | 호스트 이름 | 센서 호스트 이름 |
    | Proxy (프록시) | 프록시 호스트 및 포트 이름 |

4. **저장** 을 선택합니다.

## <a name="synchronize-time-zones-on-the-sensor"></a>센서의 표준 시간대를 동기화

모든 사용자에게 동일한 시간과 지역이 표시되도록 센서의 시간 및 지역을 구성할 수 있습니다.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="시간 및 지역을 구성합니다.":::

| 매개 변수 | Description |
|--|--|
| 표준 시간대 | 다음에 대해 표준 시간대를 정의합니다.<br />- 경고<br />- 동향 및 통계 위젯<br />- 데이터 마이닝 보고서<br />   \- 위험 평가 보고서<br />- 공격 벡터 |
| 날짜 형식 | 다음 형식 옵션 중 하나를 선택합니다.<br />- dd/MM/yyyy HH:mm:ss<br />- MM/dd/yyyy HH:mm:ss<br />- yyyy/MM/dd HH:mm:ss |
| 날짜 및 시간 | 현재 날짜와 현지 시간을 선택한 형식으로 표시합니다.<br />예를 들어 실제 위치가 미국 및 뉴욕에 있지만 표준 시간대가 유럽 및 베를린으로 설정된 경우, 베를린 현지 시각에 따라 시간이 표시됩니다. |

센서 시간을 구성하려면 다음을 수행합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **시스템 설정** 창에서 **시간 및 지역** 을 선택합니다.

3. 매개 변수를 설정하고 **저장** 을 선택합니다.

## <a name="set-up-backup-and-restore-files"></a>백업 설정 및 파일 복원

시스템 백업은 매일 오전 3:00에 자동으로 수행됩니다. 데이터는 센서의 다른 디스크에 저장됩니다. 기본 위치는 `/var/cyberx/backups`입니다.

이 파일을 내부 네트워크에 자동으로 전송할 수 있습니다.

> [!NOTE]
> - 백업 및 복원 프로시저는 동일한 버전에서만 수행할 수 있습니다.
> - 일부 아키텍처에서는 백업을 사용할 수 없습니다. `/var/cyberx/properties/backup.properties` 파일에서 사용하도록 설정할 수 있습니다.

온-프레미스 관리 콘솔을 사용하여 센서를 제어하는 경우, 센서의 백업 일정을 사용하여 이러한 백업을 수집하고 관리 콘솔 또는 외부 백업 서버에 저장할 수 있습니다.

**백업되는 항목**: 구성 및 데이터.

**백업되지 않는 항목:** PCAP 파일 및 로그. PCAP 및 로그를 수동으로 백업하고 복원할 수 있습니다.

센서 백업 파일은 `<sensor name>-backup-version-<version>-<date>.tar` 형식으로 이름이 자동 지정됩니다. 예제는 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`입니다.

백업을 구성하려면 다음을 수행합니다.

- 관리 계정에 로그인하고 `$ sudo cyberx-xsense-system-backup`을 입력합니다.

최신 백업 파일을 복원하려면 다음을 수행합니다.

- 관리 계정에 로그인하고 `$ sudo cyberx-xsense-system-restore`를 입력합니다.

백업을 외부 SMB 서버에 저장하려면 다음을 수행합니다.

1. 외부 SMB 서버에 공유 폴더를 만듭니다.

    SMB 서버에 액세스하는 데 필요한 폴더 경로, 사용자 이름, 암호를 가져옵니다.

2. 센서에서 백업용 디렉터리를 만듭니다.

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. 편집 `fstab`:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. SMB 서버에 대해 공유할 자격 증명을 편집하고 만듭니다.

    `sudo nano /etc/samba/user` 

5. 추가:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. 디렉터리를 탑재합니다.

    `sudo mount -a`

7. Defender for IoT 센서의 공유 폴더에 백업 디렉터리를 구성합니다.  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>센서 복원

센서 콘솔에서 백업을 복원할 수 있으며 CLI를 사용해서 복원할 수도 있습니다.

**콘솔에서 복원하려면 다음을 수행합니다.**

- 센서의 **시스템 설정** 창에서 **이미지 복원** 을 선택합니다.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="단추를 선택하여 이미지를 복원합니다.":::

콘솔에 복원 오류가 표시됩니다.

**CLI를 사용하여 복원하려면 다음을 수행합니다.**

- 관리 계정에 로그인하고 `$ sudo cyberx-management-system-restore`를 입력합니다.

## <a name="update-a-standalone-sensor-version"></a>독립 실행형 센서 버전 업데이트

다음 프로시저에서는 센서 콘솔을 사용하여 독립 실행형 센서를 업데이트하는 방법을 설명합니다. 업데이트 프로세스는 30분 정도 걸립니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. Defender for IoT로 이동합니다.

3. **업데이트** 페이지로 이동합니다.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Defender for IoT 업데이트 페이지의 스크린샷.":::

4. **센서** 섹션에서 **다운로드** 를 선택하고 파일을 저장합니다.

5. 센서 콘솔의 사이드바에서 **시스템 설정** 을 선택합니다.

6. **버전 업데이트** 창에서 **업데이트** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="업데이트 창의 스크린샷.":::

7. Defender for IoT **업데이트** 페이지에서 다운로드한 파일을 선택합니다.

8. 업데이트 프로세스가 시작되고 그동안 시스템이 두 번 다시 부팅됩니다. 업데이트 프로세스가 완료되기 전에 첫 번째 다시 부팅한 후에는 로그인 창과 함께 시스템이 열립니다. 로그인하면 업그레이드 버전이 사이드바의 왼쪽 아래에 나타납니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="로그인한 후 표시되는 업그레이드 버전의 스크린샷.":::

## <a name="forward-sensor-failure-alerts"></a>센서 오류 경고 전달

타사에 경고를 전달하여 다음에 대한 정보를 제공할 수 있습니다.

- 센서 연결 해제

- 원격 백업 실패

이 정보는 시스템 알림에 대한 전달 규칙을 만들 때 전송됩니다.

> [!NOTE]
> 관리자는 시스템 알림을 보낼 수 있습니다.

알림을 보내려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔에 로그인합니다.
1. 측면 메뉴에서 **전달** 을 선택합니다.
1. 전달 규칙을 만듭니다.
1. **보고서 시스템 알림** 을 선택합니다.

전달 규칙에 대한 자세한 내용은 [전달 경고 정보](how-to-forward-alert-information-to-partners.md)를 참조하세요.

## <a name="adjust-system-properties"></a>시스템 속성 조정

시스템 속성은 센서의 다양한 작업 및 설정을 제어합니다. 이러한 항목을 편집하거나 수정하면 센서 콘솔 작업이 손상될 수 있습니다.

설정을 변경하기 전에 [Microsoft 지원](https://support.microsoft.com/)에 문의하세요.

시스템 속성에 액세스하려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔 또는 센서에 로그인합니다.

2. **시스템 설정** 을 선택합니다.

3. **일반** 섹션에서 **시스템 속성** 을 선택합니다.

## <a name="see-also"></a>참고 항목

[위협 인텔리전스 리서치 및 패키지](how-to-work-with-threat-intelligence-packages.md)

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)
