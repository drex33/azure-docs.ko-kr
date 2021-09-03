---
title: Azure NetApp Files에서 Azure 애플리케이션 Consistent Snapshot 도구를 사용하는 팁과 요령 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure 애플리케이션 Consistent Snapshot 도구를 사용하는 팁과 요령을 제공합니다.
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
ms.topic: how-to
ms.date: 08/04/2021
ms.author: phjensen
ms.openlocfilehash: 6650554c92f42a8b5c25a26be5f4ea41947105e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535996"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool"></a>Azure 애플리케이션 일치 스냅숏 도구 사용을 위한 팁과 요령

이 문서에서는 AzAcSnap을 사용할 때 유용한 팁과 요령을 제공합니다.

## <a name="limit-service-principal-permissions"></a>서비스 주체 권한 제한

AzAcSnap 서비스 주체의 범위를 제한해야 할 수도 있습니다.  Azure 리소스의 세분화된 액세스 관리에 대한 자세한 내용은 [Azure RBAC 설명서](../role-based-access-control/index.yml)를 검토하세요.  

다음은 AzAcSnap이 기능하는 데 필요한 최소한의 작업을 포함한 역할 정의 예시입니다.

```azurecli
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

복원 옵션이 제대로 작동하려면 AzAcSnap 서비스 주체도 볼륨을 만들 수 있어야 합니다.  이 경우 역할 정의에 추가 작업이 필요하므로 전체 서비스 주체는 다음 예시와 같습니다.

```azurecli
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>수동으로 스냅샷 만들기

백업 명령(`azacsnap -c backup`)을 실행하기 전에 테스트 명령을 실행하여 구성을 확인하고 성공적으로 실행되었는지 확인합니다.  이러한 테스트가 바르게 실행되면 `azacsnap`가 설치된 SAP HANA 데이터베이스 및 **Azure Large Instance** 또는 **Azure NetApp Files** 시스템에 있는 SAP HANA의 기본 스토리지 시스템과 통신할 수 있음이 입증되었습니다.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

그런 다음 수동 데이터베이스 스냅샷 백업을 수행하려면 다음 명령을 실행합니다.

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>자동 스냅샷 백업 설정

Unix/Linux 시스템에서는 일반적으로 시스템에서 명령 실행을 자동화하는 데 `cron`을 사용합니다. 스냅샷 도구의 표준 실행은 사용자의 `crontab`을 설정하는 것입니다.

아래 예시는 사용자 `azacsnap`가 `crontab`을 사용해서 스냅샷을 자동화하는 사례입니다.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

위의 crontab에 대한 설명입니다.

- `MAILTO=""`: 값이 비어 있으면 crontab 항목을 실행할 때 cron이 사용자에게 자동으로 메일을 보내지 못하게 합니다. 보낸 메일이 로컬 사용자의 메일 파일로 도착하는 경우가 많기 때문입니다.
- crontab 항목의 시간 설정 축약형 버전은 별도의 설명이 필요 없습니다.
  - `@monthly` = 한 달에 한 번 실행합니다. 즉, “0 0 1 * *”입니다.
  - `@weekly`  = 매주 한 번 실행합니다. 즉, “0 0 * * 0”입니다.
  - `@daily`   = 하루에 한 번 실행합니다. 즉, “0 0 * * *”입니다.
  - `@hourly`  = 한 시간에 한 번 실행합니다. 즉, “0 * * * *”입니다.
- 처음 다섯 개의 열을 사용하여 시간을 설정합니다. 아래 열 예제를 참조하세요.
  - `0,15,30,45`: 15분마다
  - `0-23`: 매 시간
  - `*` : 매일
  - `*` : 매월
  - `*` : 모든 요일
- 괄호 “()” 안에 포함된 실행 명령줄
  - `. /home/azacsnap/.profile` = 사용자의 .profile로 풀해서 $PATH 등의 환경을 설정합니다.
  - `cd /home/azacsnap/bin` = 실행 디렉터리를 구성 파일이 있는 위치 “/home/azacsnap/bin”으로 변경합니다.
  - `azacsnap -c .....` = 모든 옵션을 포함하여 실행할 전체 azacsnap 명령입니다.

cron 및 crontab 파일의 형식에 대한 자세한 설명은 <https://en.wikipedia.org/wiki/Cron>을 참조하세요.

> [!NOTE]
> 사용자는 스냅샷이 성공적으로 생성되고 있는지 확인하기 위해 cron 작업을 모니터링해야 합니다.

## <a name="monitor-the-snapshots"></a>스냅샷 모니터링

정상적인 시스템이 되도록 하려면 다음 조건을 모니터링해야 합니다.

1. 사용 가능한 디스크 공간. 오래된 디스크 블록을 스냅샷에 유지하기 때문에 스냅샷은 디스크 공간을 천천히 사용합니다.
    1. 디스크 공간 관리를 자동화하는 데 유용하도록 `--retention`과 `--trim` 옵션을 사용하여 이전 스냅샷 및 데이터베이스 로그 파일을 자동으로 정리합니다.
1. 스냅샷 도구의 성공적 실행
    1. `*.result` 파일을 확인해서 최근에 실행한 `azacsnap`의 성공 혹은 실패 여부를 파악합니다.
    1. `/var/log/messages` 에서 `azacsnap` 명령의 출력을 확인합니다.
1. 다른 시스템에 주기적으로 복원하여 스냅샷의 일관성을 유지합니다.

> [!NOTE]
> 스냅샷 상세 정보를 나열하려면 `azacsnap -c details` 명령을 실행합니다.

## <a name="delete-a-snapshot"></a>스냅샷 삭제

스냅샷을 삭제하려면 `azacsnap -c delete` 명령을 사용합니다. OS 수준에서 스냅샷 삭제는 불가능합니다. 스토리지 스냅샷을 삭제하려면 올바른 명령(`azacsnap -c delete`)을 사용해야 합니다.

> [!IMPORTANT]
> 스냅샷을 삭제하는 경우 유의하세요. 삭제한 후에 삭제된 스냅샷 복구는 **불가능** 합니다.

## <a name="restore-a-snapshot"></a>스냅샷 복원

스토리지 볼륨 스냅샷은 새 볼륨으로 복원할 수 있습니다(`-c restore --restore snaptovol`).  Azure Large Instance의 경우 볼륨을 스냅샷으로 되돌릴 수 있습니다(`-c restore --restore revertvolume`).

> [!NOTE]
> 제공된 데이터베이스 복구 명령이 **없음** 입니다.

스냅샷은 SAP HANA 데이터 영역에 다시 복사할 수 있지만 복사본이 만들어질 때 SAP HANA가 실행 중이 아니어야 합니다(`cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*`).

Azure Large Instance의 경우 서비스 요청을 시작해 Microsoft 작업 팀에 문의하여 기존의 사용 가능한 스냅샷 중에서 원하는 스냅샷을 복원할 수 있습니다. <https://portal.azure.com>와 같이 Azure Portal에서 서비스 요청을 시작할 수 있습니다.

재해 복구 장애 조치(failover)를 수행하기로 결정한 경우 DR 사이트의 `azacsnap -c restore --restore revertvolume` 명령이 SAP HANA 복구를 위해 최신(`/hana/data` 및 `/hana/logbackups`) 볼륨 스냅샷을 자동으로 사용할 수 있게 합니다. 프로덕션 및 DR 사이트 간 복제가 중단되는 경우 이 명령을 주의해서 사용합니다.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>‘부팅’ 볼륨에 대해서만 스냅샷 설정

> [!IMPORTANT]
> 이 작업은 Azure Large Instance에만 적용됩니다.

일부 경우에는 고객이 이미 SAP HANA를 보호하고 ‘부팅’ 볼륨 스냅샷을 구성할 수 있는 도구를 소유하고 있을 수 있습니다.  이 경우 다음 단계만 완료하면 됩니다.

1. 설치를 위한 사전 필수 구성 요소 1-4단계를 완료합니다.
1. 스토리지와의 통신을 사용합니다.
1. 설치 프로그램을 다운로드하고 실행해서 스냅샷 도구를 설치합니다.
1. 스냅샷 도구의 설치를 완료합니다.
1. azacsnap 구성 파일에 추가할 볼륨 목록을 가져옵니다. 이 예에서 스토리지 사용자 이름은 `cl25h50backup`이고 스토리지 IP 주소는 `10.1.1.10`입니다. 
    ```bash
    ssh cl25h50backup@10.1.1.10 "volume show -volume *boot*"
    ```
    ```output
    Last login time: 7/20/2021 23:54:03
    Vserver   Volume       Aggregate    State      Type       Size  Available Used%
    --------- ------------ ------------ ---------- ---- ---------- ---------- -----
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v51_vol aggr_n01_ssd online RW 150GB 57.24GB  61%
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v52_vol aggr_n01_ssd online RW 150GB 81.06GB  45%
    ams07-a700s-saphan-1-01v250-client25-nprod t250_sles_boot_sollabams07v53_vol aggr_n01_ssd online RW 150GB 79.56GB  46%
    3 entries were displayed.
    ```
    > [!NOTE] 
    > 이 예에서 이 호스트는 3개 노드 스케일 아웃 시스템의 일부이며 이 호스트에서 3개의 부팅 볼륨을 모두 볼 수 있습니다.  즉, 3개의 부팅 볼륨이 모두 이 호스트에서 스냅샷될 수 있으며 다음 단계에서 3개 모두를 구성 파일에 추가해야 합니다.

1. 다음과 같이 새 구성 파일을 만듭니다. 부팅 볼륨 세부 정보는 OtherVolume stanza에 있어야 합니다.
    ```bash
    azacsnap -c configure --configuration new --configfile BootVolume.json
    ```
    ```output
    Building new config file
    Add comment to config file (blank entry to exit adding comments): Boot only config file.
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: y
    HANA SID (for example, H80): X
    HANA Instance Number (for example, 00): X
    HANA HDB User Store Key (for example, `hdbuserstore List`): X
    HANA Server's Address (hostname or IP address): X
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: y
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v51_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v52_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: y
    Storage User Name (for example, clbackup25): cl25h50backup
    Storage IP Address (for example, 192.168.1.30): 10.1.1.10
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): t250_sles_boot_sollabams07v53_vol
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. 구성 파일을 확인하고 다음 예제를 참조하세요.

    구성 파일의 콘텐츠를 `cat` 명령을 사용하여 표시합니다.

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v51_vol"
                  },
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v52_vol"
                  },
                  {
                    "backupName": "cl25h50backup",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t250_sles_boot_sollabams07v53_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. 부팅 볼륨 백업 테스트

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. 나열되어 있는지 확인하고 `--snapshotfilter` 옵션을 추가하여 반환되는 스냅샷 목록을 제한합니다.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    명령 출력:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t250_sles_boot_sollabams07v51_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v51_vol, , , Size used by Snapshots, 1.31GB
    #1, t250_sles_boot_sollabams07v52_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v52_vol, , , Size used by Snapshots, 1.31GB
    #1, t250_sles_boot_sollabams07v53_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t250_sles_boot_sollabams07v53_vol, , , Size used by Snapshots, 1.31GB
    ```

1. *선택 사항* `crontab` 또는 `azacsnap` 백업 명령을 실행할 수 있는 적절한 스케줄러를 사용하여 자동 스냅샷 백업을 설정합니다.

> [!NOTE]
> SAP HANA와의 통신 설정은 필요하지 않습니다.

## <a name="restore-a-boot-snapshot"></a>‘부팅’ 스냅샷 복원

> [!IMPORTANT]
> 이 작업은 Azure Large Instance에서만 가능합니다.
> 서버가 스냅샷이 만들어진 시점으로 복원됩니다.

‘부팅’ 스냅샷은 다음과 같이 복구할 수 있습니다.

1. 고객은 서버를 종료해야 합니다.
1. 서버가 종료된 후 고객은 복원할 머신 ID와 스냅샷이 포함된 서비스 요청을 열어야 합니다.
    > 고객은 <https://portal.azure.com>과 같이 Azure Portal에서 서비스 요청을 열 수 있습니다.
1. Microsoft는 지정된 머신 ID와 스냅샷을 사용하여 운영 체제 LUN을 복원한 다음 서버를 부팅합니다.
1. 그러면 고객은 서버가 부팅되고 정상 상태인지 확인해야 합니다.

복원 후 수행할 추가 단계는 없습니다.

## <a name="key-facts-to-know-about-snapshots"></a>스냅샷에 대해 알아야 할 주요 사항

스토리지 볼륨 스냅샷의 주요 특성

- **스냅샷 위치**: 스냅샷은 볼륨 내의 가상 디렉터리(`.snapshot`)에서 찾을 수 있습니다.  **Azure Large Instance** 에 대한 다음 예제를 참조하세요.
  - 데이터베이스: `/hana/data/<SID>/mnt00001/.snapshot`
  - 공유: `/hana/shared/<SID>/.snapshot`
  - 로그: `/hana/logbackups/<SID>/.snapshot`
  - 부팅: HLI의 부팅 스냅샷은 OS 수준에서 **표시되지 않지만** `azacsnap -c details`을 사용하여 나열할 수 있습니다.

  > [!NOTE]
  >  `.snapshot`는 스냅샷에 읽기 전용 액세스를 제공하는 읽기 전용 숨겨진 *가상* 폴더입니다.

- **최대 스냅샷:** 하드웨어는 볼륨당 최대 250개의 스냅샷을 유지할 수 있습니다. 스냅샷 명령은 명령줄의 보존 집합에 따라 접두사에 대한 최대 스냅샷 수를 유지하고, 보존할 최대 개수를 초과하는 경우 가장 오래된 스냅샷을 삭제합니다.
- **스냅샷 이름:** 스냅샷 이름에는 고객이 작성한 접두사 레이블이 포함됩니다.
- **스냅샷의 크기:** 데이터베이스 수준의 크기/변경 내용에 따라 달라집니다.
- **로그 파일 위치:** 명령으로 생성되는 로그 파일은 JSON 구성 파일에 정의된 폴더에 출력됩니다. 이 파일은 기본값으로 명령이 실행되는 하위 폴더입니다(예: `./logs`).

## <a name="next-steps"></a>다음 단계

- [문제 해결](azacsnap-troubleshoot.md)
