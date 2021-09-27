---
title: Azure NetApp Files에 대한 Azure 애플리케이션 일치 스냅샷 도구 문제 해결 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구를 사용하는 팁과 요령을 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/17/2021
ms.author: phjensen
ms.openlocfilehash: 0fb0b0fc0734cc05952457e0e6fc6dc5ff5151b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614337"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅샷 도구 문제 해결

이 문서에서는 Azure NetApp Files 및 Azure 대규모 인스턴스에서 사용할 수 있는 Azure 애플리케이션 일치 스냅샷 도구를 사용에 대한 문제 해결 내용을 제공합니다.

명령을 실행하는 동안 발생할 수 있는 일반적인 문제는 다음과 같습니다. 문제를 해결하려면 설명된 해결 지침을 수행합니다. 여전히 문제가 발생하는 경우 Azure Portal에서 서비스 요청을 열고 Microsoft 지원에 응답할 SAP HANA 대규모 인스턴스 큐에 요청을 할당합니다.

## <a name="log-files"></a>로그 파일

AzAcSnap를 사용하여 오류를 디버깅하는 데 가장 적합한 정보 중 하나는 로그 파일입니다.  

### <a name="log-file-location"></a>로그 파일 위치

로그 파일은 AzAcSnap 구성 파일의 `logPath` 매개 변수에 따라 구성된 디렉터리에 저장됩니다.  기본 구성 파일 이름은 `azacsnap.json`이며 `logPath`의 기본값은 `"./logs"`입니다. 즉, 로그 파일은 `azacsnap` 명령이 실행되는 위치와 관련하여 `./logs` 디렉터리에 기록됩니다.  `logPath` 절대 위치(예: `/home/azacsnap/logs`)를 만들면 `azacsnap` 명령이 실행된 위치에 관계없이 `azacsnap`이 `/home/azacsnap/logs`에 로그를 출력합니다.

### <a name="log-file-naming"></a>로그 파일 이름 지정

로그 파일 이름은 애플리케이션 이름(예: `azacsnap`), 사용된 명령어 옵션 `-c`(예: `backup`, `test`, `details`) 및 구성 파일 이름(예: 기본값 = `azacsnap.json`)을 기반으로 합니다.  따라서 `-c backup` 명령을 사용하는 경우 기본적으로 로그 파일 이름은 `azacsnap-backup-azacsnap.log`이며 `logPath`로 구성된 디렉터리에 작성됩니다.  

이 명명 규칙은 데이터베이스당 여러 구성 파일을 허용하도록 설정되었으며, 연결된 로그 파일을 쉽게 찾을 수 있도록 합니다.  따라서 구성 파일 이름이 `SID.json`인 경우 `azacsnap -c backup --configfile SID.json` 옵션을 사용할 때 결과 파일 이름은 `azacsnap-backup-SID.log`가 됩니다.

### <a name="result-file-and-syslog"></a>결과 파일 및 syslog

`-c backup` 명령 옵션의 경우 AzAcSnap은 `logger` 명령을 사용하여 `*.result` 파일 및 시스템 로그(`/var/log/messages`)에 작성합니다.  `*.result` 파일 이름은 [로그 파일](#log-file-naming)과 동일한 기본 이름이 있으며 [로그 파일과 동일한 위치](#log-file-location)로 이동합니다.  다음 예제에 따라 간단한 한 줄 출력 파일입니다.

`*.result` 파일의 출력 예제.
```output
Database # 1 (PR1) : completed ok
```

`/var/log/messages` 파일의 출력 예제.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Azure NetApp Files와의 통신 실패

Azure NetApp Files와의 통신 유효성을 검사하는 경우 통신이 실패하거나 시간이 초과될 수 있습니다.  방화벽 규칙이 AzAcSnap을 실행하는 시스템에서 다음 주소 및 TCP/IP 포트로의 아웃바운드 트래픽을 차단하지 않는지 확인합니다.

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

### <a name="testing-communication-using-cloud-shell"></a>Cloud Shell를 사용 하 여 통신 테스트

Azure Portal을 통해 Cloud Shell를 사용 하 여 서비스 사용자가 올바르게 구성 되었는지 테스트할 수 있습니다. 이렇게 하면 구성이 VNet 또는 가상 머신 내의 네트워크 제어를 우회 하 여 올바른지 테스트 합니다. 

**해결 방법:**

1. Azure Portal에서 [Cloud Shell](/azure/cloud-shell/overview) 세션을 엽니다. 
1. 테스트 디렉터리를 만듭니다 (예: `mkdir azacsnap` ).
1. cd를 azacsnap 디렉터리로 이동 하 여 최신 버전의 azacsnap 도구를 다운로드 합니다.
    
    ```bash
    wget https://aka.ms/azacsnapinstaller
    ```
   
    ```output
    ----<snip>----
    HTTP request sent, awaiting response... 200 OK
    Length: 24402411 (23M) [application/octet-stream]
    Saving to: ‘azacsnapinstaller’

    azacsnapinstaller 100%[=================================================================================>] 23.27M 5.94MB/s in 5.3s

    2021-09-02 23:46:18 (4.40 MB/s) - ‘azacsnapinstaller’ saved [24402411/24402411]
    ```
    
1. 설치 관리자 실행 파일을 만듭니다. (예: `chmod +x azacsnapinstaller`)
1. 테스트용 이진 파일의 압축을 풉니다.

    ```bash
    ./azacsnapinstaller -X -d .
    ```
    
    ```output
    +-----------------------------------------------------------+
    | Azure Application Consistent Snapshot Tool Installer |
    +-----------------------------------------------------------+
    |-> Installer version '5.0.2_Build_20210827.19086'
    |-> Extracting commands into ..
    |-> Cleaning up .NET extract dir
    ```

1. Cloud Shell 업로드/다운로드 아이콘을 사용 하 여 서비스 주체 파일 (예: `azureauth.json` ) 및 테스트를 위한 AzAcSnap 구성 파일 (예: `azacsnap.json` )을 업로드 합니다.
1. Azure Cloud Shell 콘솔에서 Storage 테스트를 실행 합니다. 

    > [!NOTE]
    > 테스트 명령을 완료 하는 데 90 초 정도 걸릴 수 있습니다.

    ```bash
    ./azacsnap -c test --test storage
    ```

    ```output
    BEGIN : Test process started for 'storage'
    BEGIN : Storage test snapshots on 'data' volumes
    BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
    PASSED: Task#1/1 Storage test successful for Volume
    END : Storage tests complete
    END : Test process complete for 'storage'
    ```

## <a name="problems-with-sap-hana"></a>SAP HANA 관련 문제

### <a name="running-the-test-command-fails"></a>테스트 명령 실행 실패

`azacsnap -c test --test hana`로 테스트를 실행하여 SAP HANA와의 통신 유효성을 검사하는 경우 다음 오류가 표시됩니다.

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**해결 방법:**

1. 각 HANA 인스턴스에 대해 구성 파일(예: `azacsnap.json`)을 확인하여 SAP HANA 데이터베이스 값이 올바른지 확인합니다.
1. 아래 명령을 실행하여 `hdbsql` 명령이 경로에 있는지, SAP HANA 서버에 연결할 수 있는지 확인합니다. 다음 예제에서는 명령 및 해당 출력의 올바른 실행을 보여줍니다.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    이 예제에서는 `hdbsql` 명령이 사용자의 `$PATH`에 있지 않습니다.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    이 예제에서는 `hdbsql` 명령이 일시적으로 사용자의 `$PATH`에 추가되지만 실행 시 `hdbuserstore Set` 명령으로 올바르게 설정되지 않은 연결 키가 표시됩니다(자세한 내용은 시작 가이드 참조).

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U AZACSNAP "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (AZACSNAP)
    ```

    > [!NOTE]
    > 사용자의 `$PATH`에 영구적으로 추가하려면 사용자의 `$HOME/.profile` 파일을 업데이트합니다.

### <a name="insufficient-privilege"></a>권한이 부족함

`azacsnap`을 실행하여 `* 258: insufficient privilege`와 같은 오류가 표시되는 경우 적절한 권한이 "AZACSNAP" 데이터베이스 사용자에게 할당되었는지 확인합니다([설치 안내서](azacsnap-installation.md#enable-communication-with-database)에 따라 생성된 사용자라고 가정).  다음 명령을 사용하여 사용자의 현재 권한을 확인합니다.

```bash
hdbsql -U AZACSNAP "select GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE from sys.granted_privileges "' | grep -i -e GRANTEE -e azacsnap
```

```output
GRANTEE,GRANTEE_TYPE,PRIVILEGE,IS_VALID,IS_GRANTABLE
"AZACSNAP","USER","BACKUP ADMIN","TRUE","FALSE"
"AZACSNAP","USER","CATALOG READ","TRUE","FALSE"
"AZACSNAP","USER","CREATE ANY","TRUE","TRUE"
```

오류는 `Detailed info for this error can be found with guid '99X9999X99X9999X99X99XX999XXX999' SQLSTATE: HY000`의 출력과 같은 필수 SAP HANA 권한을 결정하는 데 도움이 되는 추가 정보를 제공할 수도 있습니다.  이 경우 [SAP 도움말 포털 - GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/9a73c4c017744288b8d6f3b9bc0db043.html)에서 SAP의 지침을 따르세요. 이 지침은 다음 SQL 쿼리를 사용하여 필요한 권한에 대한 세부 정보를 결정하는 것을 권장합니다.

```sql
CALL SYS.GET_INSUFFICIENT_PRIVILEGE_ERROR_DETAILS ('99X9999X99X9999X99X99XX999XXX999', ?)
```

```output
GUID,CREATE_TIME,CONNECTION_ID,SESSION_USER_NAME,CHECKED_USER_NAME,PRIVILEGE,IS_MISSING_ANALYTIC_PRIVILEGE,IS_MISSING_GRANT_OPTION,DATABASE_NAME,SCHEMA_NAME,OBJECT_NAME,OBJECT_TYPE
"99X9999X99X9999X99X99XX999XXX999","2021-01-01 01:00:00.180000000",120212,"AZACSNAP","AZACSNAP","DATABASE ADMIN or DATABASE BACKUP ADMIN","FALSE","FALSE","","","",""
```

위의 예에서는 SYSTEMDB의 AZACSNAP 사용자에게 'DATABASE BACKUP ADMIN' 권한을 추가하려면 권한 부족 오류를 해결해야 합니다.

### <a name="the-hdbuserstore-location"></a>`hdbuserstore` 위치

SAP HANA와의 통신을 설정할 때 `hdbuserstore` 프로그램을 사용하여 보안 통신 설정을 만듭니다.  `hdbuserstore` 프로그램은 일반적으로 `/usr/sap/<SID>/SYS/exe/hdb/` 또는 `/usr/sap/hdbclient` 아래에 있습니다.  일반적으로 설치 프로그램은 `azacsnap` 사용자 `$PATH`에 올바른 위치를 추가합니다.

## <a name="failed-test-with-storage"></a>스토리지 테스트 실패

`azacsnap -c test --test storage` 명령이 성공적으로 완료되지 않습니다.

### <a name="azure-large-instance"></a>Azure 대규모 인스턴스

다음 예제는 Azure의 SAP HANA(대규모 인스턴스)에서 `azacsnap`을 실행하는 경우입니다.

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**해결 방법:** 위의 오류는 일반적으로 Azure 대규모 인스턴스 스토리지 사용자가 기본 스토리지에 액세스할 수 없을 때 표시됩니다. 제공된 스토리지 사용자로 스토리지에 대한 액세스를 유효성 감사하려면 `ssh` 명령을 실행하여 스토리지 플랫폼과의 통신을 유효성 검사합니다.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

예상 출력이 있는 예제:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>호스트 '172.18.18.11 (172.18.18.11)'의 신뢰성을 설정할 수 없습니다.

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**해결 방법:** 예를 선택하지 마세요. 스토리지 IP 주소가 올바른지 확인하세요. 여전히 문제가 발생하는 경우 Microsoft 운영 팀에서 스토리지 IP 주소를 확인합니다.

### <a name="azure-netapp-files"></a>Azure NetApp Files

다음 예는 Azure NetApp Files을 사용하여 VM에서 실행되는 `azacsnap`입니다.

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**해결 방법:**

1. `azacsnap.json` 구성 파일에 설정된 대로 서비스 주체 파일 `azureauth.json`이 있는지 확인합니다.
1. 로그 파일(예: `logs/azacsnap-test-azacsnap.log`)을 확인하여 서비스 주체(`azureauth.json`)에 올바른 콘텐츠가 있는지 확인합니다.  로그의 예는 다음과 같습니다.

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. 로그 파일(예: `logs/azacsnap-test-azacsnap.log`)을 확인하여 서비스 주체(`azureauth.json`)가 만료되었는지 확인합니다. 로그의 예는 다음과 같습니다.

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>다음 단계

- [팁](azacsnap-tips.md)
