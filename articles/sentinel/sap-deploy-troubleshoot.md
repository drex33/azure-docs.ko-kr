---
title: Microsoft 센티널 SAP 솔루션 배포 문제 해결 | Microsoft Docs
description: Microsoft 센티널 SAP 솔루션 배포에서 발생할 수 있는 특정 문제를 해결 하는 방법에 대해 알아봅니다.
author: batamig
ms.author: bagold
ms.topic: troubleshooting
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 0a833292784f62c16a8b0394ac10e602fa054a2c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711962"
---
# <a name="troubleshooting-your-microsoft-sentinel-sap-solution-deployment"></a>Microsoft 센티널 SAP 솔루션 배포 문제 해결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="useful-docker-commands"></a>유용한 Docker 명령

SAP 데이터 커넥터 문제를 해결할 때 다음 명령이 유용할 수 있습니다.

|함수  |명령  |
|---------|---------|
|**Docker 컨테이너 중지**     |  `docker stop sapcon-[SID]`       |
|**Docker 컨테이너 시작**     |`docker start sapcon-[SID]`         |
|**Docker 시스템 로그 보기**     |  `docker logs -f sapcon-[SID]`       |
|**Docker 컨테이너 실행**     |   `docker exec -it sapcon-[SID] bash`      |
|     |         |

자세한 내용은 [Docker CLI 설명서](https://docs.docker.com/engine/reference/commandline/docker/)를 참조하세요.

## <a name="review-system-logs"></a>시스템 로그 검토

[데이터 커넥터를 설치하거나 초기화](#reset-the-sap-data-connector)한 후에는 시스템 로그를 검토하는 것이 좋습니다.

다음을 실행합니다.

```bash
docker logs -f sapcon-[SID]
```

## <a name="enable-debug-mode-printing"></a>디버그 모드 인쇄 사용

**디버그 모드 인쇄를 사용하도록 설정하려면 다음을 수행합니다.**

1. https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml 파일을 **sapcon/[SID]** 디렉터리에 복사한 다음, 이름을 `loggingconfig.yaml` 로 바꿉니다.

1. [SAP 데이터 커넥터를 초기화](#reset-the-sap-data-connector)합니다.

예를 들어 SID `A4H`의 경우:

```bash
wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/loggingconfig_DEV.yaml
              cp loggingconfig.yaml ~/sapcon/A4H
              docker restart sapcon-A4H
```

**디버그 모드 인쇄를 다시 사용하지 않도록 설정하려면 다음을 실행합니다**.

```bash
mv loggingconfig.yaml loggingconfig.old
ls
docker restart sapcon-[SID]
```

## <a name="view-all-docker-execution-logs"></a>모든 Docker 실행 로그 보기

Microsoft 센티널 SAP 데이터 커넥터 배포에 대 한 모든 Docker 실행 로그를 보려면 다음 명령 중 하나를 실행 합니다.

```bash
docker exec -it sapcon-[SID] bash && cd /sapcon-app/sapcon/logs
```

또는

```bash
docker exec –it sapcon-[SID] cat /sapcon-app/sapcon/logs/[FILE_LOGNAME]
```

다음과 유사한 출력이 표시 됩니다.

```bash
Logs directory:
root@644c46cd82a9:/sapcon-app# ls sapcon/logs/ -l
total 508
-rwxr-xr-x 1 root root      0 Mar 12 09:22 ' __init__.py'
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPAppLog.log
-rw-r--r-- 1 root root   1056 Mar 12 16:01  ABAPAuditLog.log
-rw-r--r-- 1 root root    465 Mar 12 16:01  ABAPCRLog.log
-rw-r--r-- 1 root root    515 Mar 12 16:01  ABAPChangeDocsLog.log
-rw-r--r-- 1 root root    282 Mar 12 16:01  ABAPJobLog.log
-rw-r--r-- 1 root root    480 Mar 12 16:01  ABAPSpoolLog.log
-rw-r--r-- 1 root root    525 Mar 12 16:01  ABAPSpoolOutputLog.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  ABAPTableDataLog.log
-rw-r--r-- 1 root root    495 Mar 12 16:01  ABAPWorkflowLog.log
-rw-r--r-- 1 root root 465311 Mar 14 06:54  API.log # view this log to see submits of data into Microsoft Sentinel
-rw-r--r-- 1 root root      0 Mar 12 15:51  LogsDeltaManager.log
-rw-r--r-- 1 root root      0 Mar 12 15:51  PersistenceManager.log
-rw-r--r-- 1 root root   4830 Mar 12 16:01  RFC.log
-rw-r--r-- 1 root root   5595 Mar 12 16:03  SystemAdmin.log
```

호스트 운영 체제에 로그를 복사하려면 다음을 실행합니다.

```bash
docker cp sapcon-[SID]:/sapcon-app/sapcon/logs /directory
```

예를 들면 다음과 같습니다.

```bash
docker cp sapcon-A4H:/sapcon-app/sapcon/logs /tmp/sapcon-logs-extract
```

## <a name="review-and-update-the-sap-data-connector-configuration"></a>SAP 데이터 커넥터 구성 검토 및 업데이트

SAP 데이터 커넥터 구성 파일을 확인하고 수동으로 업데이트하려면 다음 단계를 수행합니다.

1. VM의 사용자 홈 디렉터리에서 **~/sapcon/[SID]/systemconfig.ini** 파일을 엽니다.
1. 필요한 경우 구성을 업데이트한 다음, 컨테이너를 다시 시작합니다.

    ```bash
    docker restart sapcon-[SID]
    ```

## <a name="reset-the-sap-data-connector"></a>SAP 데이터 커넥터 초기화

다음 단계에서는 커넥터를 초기화하고 지난 24시간의 SAP 로그를 다시 수집합니다.

1.  커넥터를 중지합니다. 다음을 실행합니다.

    ```bash
    docker stop sapcon-[SID]
    ```

1.  **sapcon/[SID]** 디렉터리에서 **metadata.db** 파일을 삭제합니다. 다음을 실행합니다.

    ```bash
    cd ~/sapcon/<SID>
    ls
    mv metadata.db metadata.old
    ```

    > [!NOTE]
    > **metadata.db** 파일은 각 로그의 마지막 타임스탬프를 포함하고 있으며 중복을 방지하기 위해 작동합니다.

1. 커넥터를 다시 시작합니다. 다음을 실행합니다.

    ```bash
    docker start sapcon-[SID]
    ```

모두 마쳤으면 [시스템 로그를 검토](#review-system-logs)합니다.



## <a name="common-issues"></a>일반적인 문제

SAP 데이터 커넥터와 보안 콘텐츠를 모두 배포하면 다음과 같은 오류나 문제가 발생할 수 있습니다.

### <a name="corrupt-or-missing-sap-sdk-file"></a>SAP SDK 파일이 손상되거나 누락됨

이 오류는 커넥터가 PyRfc를 사용하여 부팅할 수 없거나 zip 관련 오류 메시지가 표시될 때 발생합니다.

1. SAP SDK를 다시 설치합니다.
1. 올바른 Linux 64비트 버전인지 확인합니다. 현재 날짜를 기준으로 릴리스 파일 이름은 **nwrfc750P_8-70002752.zip** 입니다.

데이터 커넥터를 수동으로 설치한 경우 SDK 파일을 Docker 컨테이너에 복사했는지 확인합니다.

다음을 실행합니다.

```bash
Docker cp SDK by running docker cp nwrfc750P_8-70002752.zip /sapcon-app/inst/
```

### <a name="abap-runtime-errors-appear-on-a-large-system"></a>대규모 시스템에서 ABAP 런타임 오류 발생

대규모 시스템에서 ABAP 런타임 오류가 발생하는 경우 다음과 같이 더 작은 청크 크기를 설정해 보세요.

1. **sapcon/SID/systemconfig.ini** 파일을 편집하고 `timechunk = 5`를 정의합니다.
2. [SAP 데이터 커넥터를 초기화](#reset-the-sap-data-connector)합니다.

> [!NOTE]
> **timechunk** 크기는 분 단위로 정의됩니다.

### <a name="empty-or-no-audit-log-retrieved-with-no-special-error-messages"></a>특별한 오류 메시지 없이 감사 로그가 비어 있거나 감사 로그가 검색되지 않음

1. SAP에서 감사 로그가 사용하도록 설정되어 있는지 확인합니다.
1. **SM19** 또는 **RSAU_CONFIG** 트랜잭션을 확인합니다.
1. 필요에 따라 이벤트를 사용하도록 설정합니다.
1. 메시지가 SAP **SM20** 또는 **RSAU_READ_LOG** 에 도착하여 잘 있으며 커넥터 로그에 특별한 오류가 표시되지 않는지 확인합니다.


### <a name="incorrect-microsoft-sentinel-workspace-id-or-key"></a>Microsoft 센티널 작업 영역 ID 또는 키가 잘못 되었습니다.

[배포 스크립트](sap-deploy-solution.md#create-a-key-vault-for-your-sap-credentials)에서 잘못된 작업 영역 ID 또는 키를 입력한 경우 Azure KeyVault에 저장된 자격 증명을 업데이트합니다.

Azure Key Vault에서 자격 증명을 확인한 후 컨테이너를 다시 시작합니다.

```bash
docker restart sapcon-[SID]
```

### <a name="incorrect-sap-abap-user-credentials-in-a-fixed-configuration"></a>고정 구성의 SAP ABAP 사용자 자격 증명이 잘못됨

고정 구성의 경우 암호가 **systemconfig.ini** 구성 파일에 직접 저장됩니다.

여기에 저장된 자격 증명이 올바르지 않으면 자격 증명을 확인합니다.

base64 암호화를 사용하여 사용자 및 암호를 암호화합니다. 자격 증명을 암호화하려면 온라인 암호화 도구를 사용할 수 있습니다(예: https://www.base64encode.org/ ).

### <a name="incorrect-sap-abap-user-credentials-in-key-vault"></a>키 자격 증명 모음의 SAP ABAP 사용자 자격 증명이 잘못됨

자격 증명을 확인하고 Azure Key Vault에서 **ABAPUSER** 및 **ABAPPASS** 값에 올바른 값을 적용하여 필요에 따라 수정하세요.

그런 다음 컨테이너를 다시 시작하세요.

```bash
docker restart sapcon-[SID]
```


### <a name="missing-abap-sap-user-permissions"></a>ABAP(SAP 사용자) 권한 없음

**..Missing Backend RFC Authorization..** 과 비슷한 오류 메시지가 표시되면 SAP 권한 부여 및 역할이 올바르게 적용되지 않은 것입니다.

1. [변경 요청](sap-solution-detailed-requirements.md#required-sap-log-change-requests) 전송의 일부로 **MSFTSEN/SENTINEL_CONNECTOR** 역할을 가져와서 커넥터 사용자에게 적용했는지 확인합니다.

1. SAP 트랜잭션 PFCG를 사용하여 역할 생성 및 사용자 비교 프로세스를 실행합니다.

### <a name="missing-data-in-your-workbooks-or-alerts"></a>통합 문서 또는 경고에 데이터 누락

Microsoft 센티널 통합 문서 또는 경고에 데이터가 누락 된 경우 로그 파일에서 오류가 발생 하지 않고 SAP 쪽에서 **감사 로그** 정책을 제대로 사용 하도록 설정 해야 합니다. 

이 단계에는 **RSAU_CONFIG_LOG** 트랜잭션을 사용합니다.


### <a name="missing-sap-change-request"></a>SAP 변경 요청 누락

필요한 [SAP 변경 요청](sap-solution-detailed-requirements.md#required-sap-log-change-requests)이 누락되었다는 오류가 표시되면 시스템에 대한 올바른 SAP 변경 요청을 가져왔는지 확인합니다.

자세한 내용은 [SAP 시스템 구성](sap-deploy-solution.md#configure-your-sap-system)을 참조하세요.

### <a name="network-connectivity-issues"></a>네트워크 연결 문제

SAP 환경 또는 Microsoft 센티널에 네트워크 연결 문제가 있는 경우 네트워크 연결을 확인 하 여 데이터가 예상 대로 흐르는 지 확인 합니다.

일반적인 문제는 다음과 같습니다.

- Docker 컨테이너와 SAP 호스트 간의 방화벽은 트래픽을 차단할 수 있습니다. SAP 호스트는 다음과 같은 TCP 포트를 통해 통신을 수신 합니다. **32xx**, **5xx13** 및 **33XX**. 여기서 **xx** 는 SAP 인스턴스 번호입니다.

- SAP 호스트에서 Microsoft Container Registry 또는 Azure로의 아웃 바운드 통신에는 프록시 구성이 필요 합니다. 이는 일반적으로 설치에 영향을 주며 `HTTP_PROXY` 및 환경 변수를 구성 해야 합니다 `HTTPS_PROXY` . `-e`Docker 명령에 플래그를 추가 하 여 컨테이너를 만들 때 docker 컨테이너에 환경 변수를 수집할 수도 있습니다 `create`  /  `run` .

### <a name="other-unexpected-issues"></a>기타 예기치 않은 문제

이 문서에 나열되지 않은 예기치 않은 문제가 있는 경우 다음 단계를 시도합니다.

- [커넥터를 초기화하고 로그를 다시 로드](#reset-the-sap-data-connector)
- [커넥터를](sap-deploy-solution.md#update-your-sap-data-connector) 최신 버전으로 업그레이드 합니다.

> [!TIP]
> 또한 주요 구성 변경 후에는 커넥터를 초기화하고 최신 업그레이드가 설치되었는지 확인하는 것이 좋습니다.

### <a name="retrieving-an-audit-log-fails-with-warnings"></a>감사 로그를 검색하면 경고와 함께 실패

[필수 변경 요청](sap-solution-detailed-requirements.md#required-sap-log-change-requests)을 배포하지 않은 상태로 또는 이전/패치되지 않은 버전에서 감사 로그를 검색하려고 하면 경고와 함께 프로세스가 실패하는 경우 다음 방법 중 하나를 사용하여 SAP Auditlog를 검색할 수 있는지 확인합니다.

- 이전 버전에서 *XAL* 이라는 호환성 모드 사용
- 최근에 패치되지 않은 버전 사용
- 필수 변경 요청이 설치되지 않은 채로

필요한 경우 시스템이 자동으로 호환성 모드로 전환되지만, 수동으로 전환해야 할 수도 있습니다. 수동으로 호환성 모드로 전환하려면 다음을 수행합니다.

1. **sapcon/SID** 디렉터리에서 **systemconfig.ini** 파일을 편집합니다.

1. `auditlogforcexal = True`를 정의합니다.

1. Docker 컨테이너 다시 시작:

    ```bash
    docker restart sapcon-[SID]
    ```

### <a name="sapcontrol-or-java-subsystems-unable-to-connect"></a>SAPCONTROL 또는 JAVA 하위 시스템이 연결할 수 없음

OS 사용자가 유효하고 대상 SAP 시스템에서 다음 명령을 실행할 수 있는지 확인합니다.

```bash
sapcontrol -nr <SID> -function GetSystemInstanceList
```

### <a name="sapcontrol-or-java-subsystem-fails-with-timezone-related-error-message"></a>SAPCONTROL 또는 JAVA 하위 시스템이 표준 시간대 관련 오류 메시지와 함께 실패

SAPCONTROL 또는 JAVA 하위 시스템이 표준 시간대 관련 오류 메시지와 함께 실패하는 경우(예: **SAP 서버 - 'Etc/NZST'에 대한 구성 및 네트워크 액세스를 확인하세요.** ) 표준 시간대 코드를 사용하고 있는지 확인합니다.

예를 들면 `javatz = GMT+12` 또는 `abaptz = GMT-3**`를 사용합니다.


### <a name="unable-to-import-the-change-request-transports-to-sap"></a>SAP로 변경 요청 전송을 가져올 수 없음

[필요한 SAP 로그 변경 요청](sap-solution-detailed-requirements.md#required-sap-log-change-requests)을 가져올 수 없고 구성 요소 버전이 올바르지 않다는 오류가 발생하는 경우 변경 요청을 가져올 때 `ignore invalid component version`을 추가합니다.

### <a name="audit-log-data-not-ingested-past-initial-load"></a>초기 로드 이후에 수집되지 않은 감사 로그 데이터

**RSAU_READ_LOAD** 또는 **SM200** 트랜잭션에 표시되는 SAP 감사 로그 데이터가 초기 로드를 지나 Microsoft Sentinel에 수집되지 않은 경우 SAP 시스템 및 SAP 호스트 운영 체제가 잘못 구성되었을 수 있습니다.

- 초기 로드는 SAP 데이터 커넥터를 새로 설치한 후 또는 **metadata.db** 파일이 삭제된 후 수집됩니다.
- SAP 시스템 표준 시간대가 **STZAC** 트랜잭션에서 **CET** 로 설정되어 있지만 SAP 호스트 운영 체제 표준 시간대가 **UTC** 로 설정되어 있으면 샘플 구성이 잘못되었을 수 있습니다.

잘못된 구성을 확인하려면 트랜잭션 **SE38** 에서 **RSDBTIME** 보고서를 실행합니다. SAP 시스템과 SAP 호스트 운영 체제 간 불일치가 발견되면:

1. Docker 컨테이너를 중지합니다. 실행

    ```bash
    docker stop sapcon-[SID]
    ```

1.  **sapcon/[SID]** 디렉터리에서 **metadata.db** 파일을 삭제합니다. 다음을 실행합니다.

    ```bash
    rm ~/sapcon/[SID]/metadata.db
    ```

1. 설정이 일치하도록(예: 동일한 표준 시간대) SAP 시스템 및 SAP 호스트 운영 체제를 업데이트합니다. 자세한 내용은 [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/Basis/Time+zone+settings%2C+SAP+vs.+OS+level)를 참조하세요.

1. 컨테이너를 다시 시작합니다. 다음을 실행합니다.

    ```bash
    docker start sapcon-[SID]
    ```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [SAP 지속적인 위협 모니터링 배포(퍼블릭 미리 보기)](sap-deploy-solution.md)
- [Microsoft Sentinel SAP 솔루션 로그 참조(공개 미리 보기)](sap-solution-log-reference.md)
- [SNC를 사용하여 Microsoft Sentinel SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)
- [전문가 구성 옵션, 온-프레미스 배포, SAPControl 로그 원본](sap-solution-deploy-alternate.md)
- [Microsoft Sentinel SAP 솔루션: 보안 콘텐츠 참조(공개 미리 보기)](sap-solution-security-content.md)
- [Microsoft Sentinel SAP 솔루션 세부 SAP 요구 사항(공개 미리 보기)](sap-solution-detailed-requirements.md)
