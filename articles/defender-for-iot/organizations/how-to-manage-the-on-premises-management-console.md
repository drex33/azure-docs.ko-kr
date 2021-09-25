---
title: 온-프레미스 관리 콘솔 관리
description: 백업 및 복원과 같은 온-프레미스 관리 콘솔 옵션, 호스트 이름 정의, 센서에 대한 프록시 설정에 대해 알아봅니다.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 9b956dbfa640862d10d0e47aef97e5a1727052c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625224"
---
# <a name="manage-the-on-premises-management-console"></a>온-프레미스 관리 콘솔 관리

이 문서에서는 백업 및 복원, 커밋된 디바이스 활성화 파일 다운로드, 인증서 업데이트, 센서에 프록시 설정과 같은 온-프레미스 관리 콘솔 옵션에 대해 설명합니다.

Azure Portal에서 온-프레미스 관리 콘솔을 온보딩합니다.

## <a name="upload-an-activation-file"></a>활성화 파일 업로드

처음 로그인하면 온-프레미스 관리 콘솔 활성화 파일이 다운로드됩니다. 이 파일에는 온보딩 프로세스 중에 정의된 집계 커밋된 디바이스가 포함되어 있습니다. 목록에는 여러 구독에 연결된 센서가 포함되어 있습니다.

초기 활성화 후 모니터링되는 디바이스 수가 온보딩 중에 정의된 커밋된 디바이스 수를 초과할 수 있습니다. 예를 들어 더 많은 센서를 관리 콘솔에 연결하는 경우 이 이벤트가 발생할 수 있습니다. 모니터링된 디바이스 수와 커밋된 디바이스 수가 일치하지 않으면 관리 콘솔에 경고가 표시됩니다. 이 이벤트가 발생하면 새 활성화 파일을 업로드해야 합니다.

**활성화 파일을 업로드하려면:**

1. Azure Defender for IoT **가격 책정** 페이지로 이동합니다.
1. **관리 콘솔 탭의 활성화 파일 다운로드** 를 선택하면 활성화 파일이 다운로드됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="활성화 파일 다운로드.":::

1. 관리 콘솔에서 **시스템 설정** 을 선택합니다.
1. **활성화** 를 선택합니다.
1. **파일 선택** 을 선택하고 저장한 파일을 선택합니다.

## <a name="manage-certificates"></a>인증서 관리

온-프레미스 관리 콘솔을 설치한 후 로컬 자체 서명된 인증서가 생성되어 웹 애플리케이션에 액세스하는 데 사용됩니다. 온-프레미스 관리 콘솔에 처음으로 로그인하면 관리자에게 SSL/TLS 인증서를 제공하라는 메시지가 표시됩니다. 

관리자는 초기 로그인 후 업로드된 인증서를 업데이트해야 할 수 있습니다. 인증서가 만료된 경우와 같이 발생할 수 있습니다.

**인증서를 업데이트하려면 다음을 수행합니다.**

1. **시스템 설정** 을 선택합니다.

1. **SSL/TLS 인증서** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/certificate-upload.png" alt-text="인증서 업로드":::

1. SSL/TLS 인증서 대화 상자에서 기존 인증서를 삭제하고 새 인증서를 추가합니다.

    - 인증서 이름을 추가합니다.
    - CRT 파일 및 키 파일을 업로드.
    - 필요한 경우 PEM 파일을 업로드합니다.

업로드가 실패하면 보안 또는 IT 관리자에게 문의하거나 인증서 정보 의 정보를 [검토합니다.](how-to-deploy-certificates.md)

**인증서 유효성 검사 설정을 변경하려면 다음을 수행합니다.**

1. 인증서 유효성 **검사 사용** 토글을 사용하거나 사용하지 않도록 설정합니다. 옵션을 사용할 수 있고 유효성 검사가 실패하면 관련 구성 요소 간의 통신이 중지되고 유효성 검사 오류가 콘솔에 표시됩니다. 사용하지 않도록 설정하면 인증서 유효성 검사가 수행되지 않습니다. 자세한 내용은 [인증서 유효성 검사](how-to-deploy-certificates.md#about-certificate-validation) 정보를 참조하세요.

1. **저장** 을 선택합니다.

처음 인증서 업로드에 대한 자세한 내용은 [최초 로그인 및 활성화 검사 목록을 참조하세요.](how-to-activate-and-set-up-your-sensor.md#first-time-sign-in-and-activation-checklist)

## <a name="define-backup-and-restore-settings"></a>백업 및 복원 설정 정의

온-프레미스 관리 콘솔 시스템 백업은 매일 자동으로 수행됩니다. 데이터는 다른 디스크에 저장됩니다. 기본 위치는 `/var/cyberx/backups`입니다. 

이 파일을 내부 네트워크에 자동으로 전송할 수 있습니다. 

> [!NOTE]
> 백업 및 복원 절차는 동일한 버전에서만 수행할 수 있습니다. 

온-프레미스 관리 콘솔 컴퓨터를 백업하려면 다음을 수행합니다. 

- 관리 계정에 로그인하고 `sudo cyberx-management-backup -full`을 입력합니다.

최신 백업 파일을 복원하려면 다음을 수행합니다.

- 관리 계정에 로그인하고 `$ sudo cyberx-management-system-restore`를 입력합니다.

백업을 외부 SMB 서버에 저장하려면 다음을 수행합니다.

1. 외부 SMB 서버에 공유 폴더를 만듭니다.  

   SMB 서버에 액세스하는 데 필요한 폴더 경로, 사용자 이름 및 암호를 가져옵니다. 

2. Defender for IoT에서 백업용 디렉터리를 만듭니다.

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. fstab 편집:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. SMB 서버가 공유할 자격 증명을 편집하거나 만듭니다. 

   - `sudo nano /etc/samba/user` 

5. 추가: 

   - `username=<user name>`

   - `password=<password>` 

6. 디렉터리를 탑재합니다. 

   - `sudo mount -a` 

7. Defender for IoT 온-프레미스 관리 콘솔의 공유 폴더에 백업 디렉터리를 구성합니다.  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>호스트 이름 편집

조직 DNS 서버에 구성된 관리 콘솔의 호스트 이름을 편집하려면 다음을 수행합니다.

1. 관리 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택합니다.  

2. 콘솔의 네트워킹 섹션에서 **네트워크** 를 선택합니다. 

3. 조직 DNS 서버에 구성된 호스트 이름을 입력합니다. 

4. **저장** 을 선택합니다.

## <a name="define-vlan-names"></a>VLAN 이름 정의

VLAN 이름은 센서와 관리 콘솔 간에 동기화되지 않습니다. 구성 요소에 동일한 이름을 정의합니다.

네트워킹 영역에서 **VLAN** 을 선택하고 검색된 VLAN ID에 이름을 추가합니다. 그런 다음 **저장** 을 선택합니다.

## <a name="define-a-proxy-to-sensors"></a>센서에 프록시 정의

사용자가 센서에 직접 로그인하지 못하도록 하여 시스템 보안을 강화합니다. 대신 단일 방화벽 규칙을 사용하여 사용자가 온-프레미스 관리 콘솔에서 센서에 액세스할 수 있도록 프록시 터널링을 사용합니다. 이 향상된 기능은 센서 너머의 네트워크 환경에 대한 무단 액세스 가능성을 줄입니다.

프록시는 센서에 직접 연결되지 않는 환경에서 사용하세요.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="사용자.":::

다음 절차에서는 온-프레미스 관리 콘솔에 센서를 연결하고 해당 콘솔에서 터널링을 사용하도록 설정합니다.

1. 관리 자격 증명을 사용하여 온-프레미스 관리 콘솔 어플라이언스 CLI에 로그인합니다.

2. `sudo cyberx-management-tunnel-enable`을 입력하고 **Enter** 키를 선택합니다.

4. `--port 10000`을 입력하고 **Enter** 키를 선택합니다.

## <a name="adjust-system-properties"></a>시스템 속성 조정

시스템 속성은 관리 콘솔의 다양한 작업 및 설정을 제어합니다. 이를 편집하거나 수정하면 관리 콘솔의 작업이 손상될 수 있습니다. 설정을 변경하기 전에 [Microsoft 지원](https://support.microsoft.com)에 문의하세요.

시스템 속성에 액세스하려면 다음을 수행합니다. 

1. 온-프레미스 관리 콘솔 또는 센서에 로그인합니다.

2. **시스템 설정** 을 선택합니다.

3. **일반** 섹션에서 **시스템 속성** 을 선택합니다.

## <a name="change-the-name-of-the-on-premises-management-console"></a>온-프레미스 관리 콘솔의 이름 변경

온-프레미스 관리 콘솔의 이름을 변경할 수 있습니다. 새 이름은 콘솔 웹 브라우저, 다양한 콘솔 창 및 문제 해결 로그에 표시됩니다. 기본 이름은 **management console** 입니다.

이름을 변경하는 방법은 다음과 같습니다.

1. 왼쪽 창의 맨 아래에서 현재 이름을 선택합니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="온-프레미스 관리 콘솔 버전의 스크린샷.":::

2. **관리 콘솔 구성 편집** 대화 상자에서 새 이름을 입력합니다. 이름은 25자를 초과할 수 없습니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Defender for IoT 플랫폼 구성을 편집하는 스크린샷.":::

3. **저장** 을 선택합니다. 새 이름이 적용됩니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="변경된 콘솔 이름을 보여 주는 스크린샷.":::

## <a name="password-recovery"></a>암호 복구

온-프레미스 관리 콘솔 암호 복구는 디바이스가 연결된 구독과 연결됩니다. 디바이스가 연결된 구독을 모르면 암호를 복구할 수 없습니다.

암호를 다시 설정하려면:

1. 온-프레미스 관리 콘솔의 로그인 페이지로 이동합니다.
1. **암호 복구** 를 선택합니다.
1. 고유 식별자를 복사합니다.
1. Defender for IoT **사이트 및 센서** 페이지로 이동하여 **내 암호 복구** 탭을 선택합니다.
1. 고유 식별자를 입력하고 **복구** 를 선택합니다. 활성화 파일이 다운로드됩니다.
1. **암호 복구** 페이지로 이동하여 활성화 파일을 업로드합니다.
1. **다음** 을 선택합니다.
 
   이제 사용자 이름과 새 시스템 생성 암호가 제공됩니다.

> [!NOTE]
> 센서는 원래 연결됐던 구독에 연결됩니다. 암호가 연결된 구독과 동일한 구독을 사용해야만 암호를 복구할 수 있습니다.

## <a name="update-the-software-version"></a>소프트웨어 버전 업데이트

다음 절차에서는 온-프레미스 관리 콘솔 소프트웨어 버전을 업데이트하는 방법을 설명합니다. 업데이트 프로세스는 30분 정도 걸립니다.

온-프레미스 관리 콘솔 및 관리형 센서로 작업하는 경우 **먼저 관리 콘솔을 업데이트** 합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

1. Defender for IoT로 이동합니다.

1. **업데이트** 페이지로 이동합니다.

1. 온-프레미스 관리 콘솔 섹션에서 버전을 선택합니다.

1. **다운로드** 를 선택하고 파일을 저장합니다.

1. 온-프레미스 관리 콘솔에 로그인하고 측면 메뉴에서 **시스템 설정** 을 선택합니다.

1. **버전 업데이트** 창에서 **업데이트** 를 선택합니다.

1. Defender for IoT **업데이트** 페이지에서 다운로드한 파일을 선택합니다.

## <a name="mail-server-settings"></a>메일 서버 설정

온-프레미스 관리 콘솔의 SMTP 메일 서버 설정을 정의합니다.

정의하려면 다음을 수행합니다.

1. 관리 자격 증명을 사용하여 온-프레미스 관리의 CLI에 로그인합니다.
1. ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Enter 키를 선택합니다. 다음과 같은 메시지가 표시됩니다.
   `mail.smtp_server=`
   `mail.port=25`
   `mail.sender=`
1. SMTP 서버 이름 및 발신자를 입력하고 enter 키를 선택합니다.

## <a name="see-also"></a>참고 항목

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)

[개별 센서 관리](how-to-manage-individual-sensors.md)
