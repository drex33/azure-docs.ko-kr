---
title: Azure에서 Red Hat Enterprise Linux 이미지의 현재 위치 업그레이드
description: Red Hat Enterprise 7.x 이미지를 현재 위치에서 최신 8.x 버전으로 업그레이드하는 방법을 알아봅니다.
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mamccrea
ms.openlocfilehash: e127ec753a6db0556c901aff737656da47883c7a
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129454870"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>현재 위치의 Red Hat Enterprise Linux 업그레이드

**적용 대상:** :heavy_check_mark: Linux VM 

본 문서에서는 RHEL(Red Hat Enterprise Linux) 7을 현재 위치에서 Red Hat Enterprise Linux 8로 업그레이드하는 방법에 대한 지침을 제공합니다. 본 지침에서는 Azure의 `leapp` 도구를 사용합니다. 현재 위치 업그레이드 중, 기존의 RHEL 7 운영 체제는 RHEL 8 버전으로 대체됩니다.

>[!Note] 
> Red Hat Enterprise Linux의 SQL Server 제품은 Azure의 현재 위치 업그레이드를 지원하지 않습니다.

## <a name="what-to-expect-during-the-upgrade"></a>업그레이드 중 예상되는 상황
업그레이드 중에 시스템은 몇 차례 다시 시작합니다. 마지막으로 재시작하면 VM가 RHEL 8 최신 부 릴리스로 업그레이드됩니다. 

업그레이드 프로세스는 20분에서 2시간까지 소요될 수 있습니다. VM 규모나 시스템 상에 설치된 패키지 수 등의 몇 가지 요소가 전체 시간을 결정합니다.

## <a name="preparations"></a>준비
Red Hat과 Azure에서는 현재 위치 업그레이드를 통해 시스템을 다음 주 버전으로 전환하는 것이 좋습니다. 

업그레이드 시작 전에는 다음 사항을 고려해야 합니다. 

>[!Important] 
> 업그레이드를 시작하기 전에 이미지 스냅샷을 만듭니다.

* 최신 RHEL 7 버전 사용 중인지 확인합니다. 현재 최신 버전은 RHEL 7.9입니다. 잠긴 버전을 사용 중이어서 RHEL 7.9로 업그레이드할 수 없는 경우, [non-EUS(확장 업데이트 지원) 리포지토리로의 전환을 위한 관련 단계](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)를 따릅니다.

* 다음 명령을 실행하여 업그레이드를 확인하고 해당 명령을 실행하여 진행되는 업그레이드가 성공적으로 완료하는지 확인합니다. 해당 명령을 실행하여 */var/log/leapp/leapp-report.txt* 파일이 생성됩니다. 해당 파일은 프로세스, 진행 상황, 업그레이드 가능 여부를 알려 줍니다.

    >[!NOTE]
    > 본 문서의 해당 명령을 실행하려면 루트 계정을 사용합니다. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* 직렬 콘솔이 작동하는지 확인합니다. 업그레이드 프로세스 중에 해당 콘솔을 사용해 모니터링합니다.

* */etc/ssh/sshd_config* 에서 SSH 루트 액세스를 사용하도록 다음과 같이 설정합니다.
    1. */etc/ssh/sshd_config* 파일을 엽니다.
    1. `#PermitRootLogin yes`를 검색합니다.
    1. 숫자 기호(`#`)를 제거하여 해당 문자열의 주석 처리를 제거합니다.

## <a name="upgrade-steps"></a>업그레이드 단계

해당 단계를 신중하게 수행합니다. 프로덕션 인스턴스에서 이를 시도하기 전에 테스트 머신에서 업그레이드를 미리 시도해 보는 것이 좋습니다.

1. 최신 클라이언트 패키지를 가져오기 위해 `yum` 업데이트를 실행합니다.
    ```bash
    yum update -y
    ```

1. `leapp-client-package`를 설치합니다.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. [Red Hat 포털](https://access.redhat.com/articles/3664871)에서 *repomap.csv* 와 *pes-events.json* 이 들어 있는 *leapp-data.tar.gz* 파일을 확보합니다. *leapp-data.tar.gz* 파일의 압축을 풉니다.
    1. *leapp-data.tar.gz* 파일을 다운로드합니다.
    1. 콘텐츠를 추출한 다음 파일을 제거합니다. 다음 명령을 사용합니다.
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. `leapp`에 대한 `answers` 파일을 추가합니다.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. 업그레이드를 시작합니다.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade` 명령이 성공적으로 완료되면 프로세스를 마무리하기 위해 수동으로 시스템을 재시작합니다. 시스템이 몇 번 정도 재시작하는 동안에는 해당 시스템을 사용할 수 없습니다. 직렬 콘솔을 사용하여 프로세스를 모니터링합니다.

1.  업그레이드가 성공적으로 마무리되었는지 확인합니다.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. 업그레이드가 완료되면 다음 루트 SSH 액세스를 제거합니다.
    1. */etc/ssh/sshd_config* 파일을 엽니다.
    1. `#PermitRootLogin yes`를 검색합니다.
    1. 숫자 기호(`#`)를 추가하여 해당 문자열을 주석으로 처리합니다.

1. SSHD 서비스를 재시작하여 변경된 내용을 적용합니다.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>일반적인 문제

다음 오류들은 일반적으로 `leapp preupgrade` 프로세스가 실패했거나 `leapp upgrade` 프로세스가 실패한 경우 발생합니다.

* **오류**: 다음 사용하지 않는 플러그 인 패턴과 일치하는 항목이 없습니다.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **솔루션**: 구독 관리자 플러그 인을 사용하지 않도록 설정합니다. */etc/yum/pluginconf.d/subscription-manager.conf* 파일을 편집하고 `enabled`를 `enabled=0`으로 변경하여 사용하지 않도록 설정합니다.

    해당 오류는 사용하도록 설정된 구독 관리자 `yum` 플러그 인이 `PAYG` VM에 사용되지 않는 경우 발생합니다.

* **오류**: 루트를 이용한 원격 로그인과 관련해 발생할 수 있는 문제들

    해당 오류는 `leapp preupgrade` 실패 시에 발생할 수 있습니다.

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **솔루션**: */etc/sshd_config* 에서 루트 액세스를 사용하도록 설정합니다.

    해당 오류는 */etc/sshd_config* 에서 루트 SSH 액세스를 사용하도록 설정하지 않았을 때 발생합니다. 본 문서의 [준비](#preparations) 섹션에서 자세한 내용을 확인하세요. 


## <a name="next-steps"></a>다음 단계
* [Azure의 Red Hat 이미지](./redhat-images.md)에 대해 자세히 알아봅니다.
* [Red Hat 업데이트 인프라](./redhat-rhui.md)에 대해 자세히 알아봅니다.
* [RHEL BYOS 제품](./byos.md)에 대해 자세히 알아봅니다.
* Red Hat 현재 위치 업그레이드 프로세스에 대한 자세한 내용은 Red Hat 설명서의 [RHEL 7에서 RHEL 8로 업그레이드하기](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)를 참조하세요.
* 전체 RHEL 버전에 대한 Red Hat 지원 정책에 대한 자세한 내용은 Red Hat 설명서의 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조하세요.