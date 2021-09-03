---
title: Azure NetApp Files에 대한 Azure Application Consistent Snapshot 도구를 사용하여 백업 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 백업 명령을 실행하는 방법에 대한 지침을 제공합니다.
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
ms.topic: reference
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 5fd588cc9ff36f4213d62ee47ce296e9eadfc40e
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296760"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool"></a>Azure Application Consistent Snapshot Tool을 사용한 백업

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구의 백업 명령을 실행하는 방법에 대한 지침을 제공합니다.

## <a name="introduction"></a>소개

스토리지 스냅샷 기반 백업은 `azacsnap -c backup` 명령을 사용하여 실행됩니다.  이 명령은 DATA 볼륨에서 데이터베이스 일치 스토리지 스냅샷의 오케스트레이션을 수행하고 OTHER 볼륨에서는 데이터베이스 일치 설정 없이 스토리지 스냅샷을 수행합니다.  

DATA 볼륨의 경우 `azacsnap`은 스토리지 스냅샷에 대한 데이터베이스를 준비한 다음 구성된 모든 볼륨에 대한 스토리지 스냅샷을 만들고 마지막으로 데이터베이스에 스냅샷이 완료되었음을 알립니다.  또한 스냅샷 백업 작업을 기록하는 모든 데이터베이스 항목을 관리합니다(예: SAP HANA 백업 카탈로그).

## <a name="command-options"></a>명령 옵션

`-c backup` 명령은 다음과 같은 인수를 사용합니다.

- `--volume=` 스냅샷을 작성할 볼륨의 유형입니다. 이 매개 변수는 `data` 또는 `other`를 포함할 수 있습니다.
  - `data` 구성 파일의 dataVolume stanza 내에서 볼륨의 스냅샷을 작성합니다.
  - `other` 구성 파일의 otherVolume stanza 내에서 볼륨의 스냅샷을 작성합니다.
  
  > [!NOTE]
  > otherVolume으로 부팅 볼륨을 사용하여 별도의 구성 파일을 만들면 `boot` 스냅샷을 완전히 다른 일정(예: 매일)으로 수행할 수 있습니다.

- `--prefix=` 스냅샷 이름에 대한 고객 스냅샷 접두사입니다. 이 매개 변수는 두 가지 용도로 사용됩니다. 첫 번째 용도는 스냅샷 그룹화를 위한 고유한 이름을 제공하는 것입니다. 두 번째는 지정된 `--prefix`에 대해 유지되는 스토리지 스냅샷의 `--retention` 수를 결정하는 것입니다.

    > [!IMPORTANT]
    > 영숫자("A-Z, a-z, 0-9"), 밑줄("_") 및 대시("-") 문자만 사용할 수 있습니다.

- `--retention` 유지하도록 정의된 `--prefix`의 스냅샷 수입니다. 이 `--prefix`에 대한 새 스냅샷이 생성된 후에는 추가 스냅숏이 제거됩니다.

- `--trim` SAP HANA v2 이상에서 사용할 수 있습니다. 이 옵션은 백업 카탈로그와 디스크 카탈로그 및 로그 백업을 유지 관리합니다. 백업 카탈로그에 유지할 항목 수는 위의 `--retention` 옵션으로 결정되며, 백업 카탈로그에서 정의된 접두사(`--prefix`)의 이전 항목 및 관련된 물리적 로그 백업을 삭제합니다. 또한 로그 백업이 아닌 가장 오래된 항목보다 오래된 로그 백업 항목을 모두 삭제합니다. 이 작업을 통해 로그 백업에서 사용 가능한 모든 디스크 공간을 사용하지 않도록 방지할 수 있습니다.

  > [!NOTE]
  > 다음 예제 명령은 9개의 스토리지 스냅샷을 유지하고 보존 중인 9개의 스토리지 스냅샷에 맞게 백업 카탈로그가 지속적으로 트리밍되는지 확인합니다.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` SAP HANA와 통신하는 데 사용되는 암호화 방법을 정의하는 선택적 매개 변수입니다(`openssl` 또는 `commoncrypto`). 정의된 경우 `azacsnap -c backup` 명령은 동일한 디렉터리에서 두 개의 파일을 찾으려고 합니다. 이러한 파일은 해당 SID 이후에 이름이 지정되어야 합니다. [SAP HANA와의 통신을 위한 SSL 사용](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana)을 참조하세요. 다음 예에서는 `hana_TEST` 접두사가 있는 `hana` 형식 스냅샷을 사용하고 이 중 `9`개가 SSL(`openssl`)을 사용하여 SAP HANA와 통신을 유지합니다.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]`은 사용자 지정 구성 파일 이름을 허용하는 선택적 매개 변수입니다.

## <a name="snapshot-backups-are-fast"></a>빠른 스냅샷 백업

스냅샷 백업의 지속 시간은 볼륨 크기와 무관하며, 10GB 볼륨과 동일한 예상 시간 내에 10TB 볼륨이 스냅됩니다.  

전반적인 실행 시간에 영향을 주는 주요 요소는 스냅샷되는 볼륨 수와 `--retention` 매개 변수의 변경 내용(감소하는 경우 초과 스냅샷이 제거될 때 실행 시간을 증가시킬 수 있음)입니다.

위의 구성 예( **Azure 대규모 인스턴스** 의 경우)에서는 두 볼륨에 대한 스냅샷이 완료되는 데 5초가 걸리지 않았습니다. **Azure NetApp Files** 의 경우 두 볼륨의 스냅샷은 약 60초 정도 걸립니다.

> [!NOTE]
> 이전 `azacsnap` 실행 시 `--retention`이 크게 줄어드는 경우(예: `--retention 50`에서 `--retention 5`로) `azacsnap`이 추가 스냅샷을 제거하기 위해 소요되는 시간이 늘어납니다.

## <a name="example-with-data-parameter"></a>`data` 매개 변수를 사용한 예

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

명령은 콘솔에 출력되지 않지만 로그 파일, 결과 파일 및 `/var/log/messages`에만 기록됩니다.

이 예에서 *로그 파일* 이름은 `azacsnap-backup-azacsnap.log`입니다([로그 파일](#log-files) 참조).

`-c backup`을 `--volume data` 옵션과 함께 실행하면 백업 결과를 빠르게 확인할 수 있도록 결과 파일도 파일로 생성됩니다.  *결과* 파일의 기본 이름은 로그 파일과 같으며 접미사로 `.result`가 있습니다.

이 예에서 *결과 파일* 이름은 `azacsnap-backup-azacsnap.result`이고 다음 출력을 포함합니다.

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

`/var/log/messages` 파일에는 `.result` 파일과 동일한 출력이 포함됩니다. 다음 예를 참조하세요(루트로 실행).

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>`other` 매개 변수를 사용한 예

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

명령은 콘솔에 출력되지 않지만 로그 파일에만 기록됩니다.  결과 파일 또는 `/var/log/messages`에는 기록되지 _않습니다_.

이 예에서 *로그 파일* 이름은 `azacsnap-backup-azacsnap.log`입니다([로그 파일](#log-files) 참조).

## <a name="example-with-other-parameter-to-backup-host-os"></a>`other` 매개 변수를 사용한 예(호스트 OS 백업)

> [!NOTE]
> 부팅 볼륨만 포함하는 다른 구성 파일(`--configfile bootVol.json`)을 사용합니다.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

> [!IMPORTANT]
> Azure 대규모 인스턴스의 경우 부팅 볼륨에 대한 구성 파일 볼륨 매개 변수가 호스트 운영 체제 수준에서 표시되지 않을 수 있습니다.
> 이 값은 Microsoft Operations에서 제공할 수 있습니다.

명령은 콘솔에 출력되지 않지만 로그 파일에만 기록됩니다.  결과 파일 또는 `/var/log/messages`에는 기록되지 _않습니다_.

이 예에서 *로그 파일* 이름은 `azacsnap-backup-bootVol.log`입니다([로그 파일](#log-files) 참조).

## <a name="log-files"></a>로그 파일

로그 파일 이름은 "(명령 이름)-(`-c` 옵션)-(구성 파일 이름)"으로 구성됩니다.  예를 들어, `azacsnap -c backup --configfile h80.json --retention 5 --prefix one-off` 명령을 실행하는 경우 로그 파일은 `azacsnap-backup-h80.log`라고 합니다.  또는 `-c test` 옵션을 동일한 구성 파일(예: `azacsnap -c test --configfile h80.json`)과 사용하는 경우 로그 파일은 `azacsnap-test-h80.log`가 됩니다.

## <a name="next-steps"></a>다음 단계

- [스냅샷 세부 정보 가져오기](azacsnap-cmd-ref-details.md)
- [스냅샷 삭제](azacsnap-cmd-ref-delete.md)
