---
title: Azure(큰 인스턴스)에서 SAP HANA의 운영 체제 백업 및 복원| Microsoft Docs
description: Azure의 SAP HANA(Large Instances)의 운영 체제 백업 및 복원에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5685f7932b49f8af57faf159a51a8cb634128337
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217488"
---
# <a name="os-backup-and-restore"></a>OS 백업 및 복원

이 문서에서는 운영 체제(OS) 파일 수준 백업 및 복원을 수행하는 단계를 안내합니다. 절차는 형식I 또는 형식II, 수정 버전 3 이상, 위치 등과 같은 매개 변수에 따라 다릅니다. Microsoft 작업을 확인하여 리소스에 대한 이러한 매개 변수 값을 가져옵니다.

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>수정 버전 3 스탬프의 Type II SKU에 대한 OS 백업 및 복원

아래 정보에서는 HANA Large Instances 수정 버전 3의 **형식 II SKU** 운영 체제 파일 수준 백업 및 복원 수행 단계를 설명합니다.

>[!Important]
> **해당 문서는 수정 버전 4의 HANA 대규모 인스턴스 스탬프의 형식 II SKU 배포에 적용할 수 없습니다.** 형식 I SKU가 이미 수정 버전 3 스탬프에 있는 경우, 수정 버전 4의 HANA Large Instance 스탬프에 배포된 형식 II HANA Large Instance의 Boot LUNS는 스토리지 스냅샷으로 백업할 수 있습니다.


>[!NOTE]
>OS 백업 스크립트는 서버에 미리 설치되어 있는 ReaR 소프트웨어를 사용합니다.  

기본적으로 Microsoft 서비스 관리 팀에서 프로비전을 완료한 후에 서버는 전체 OS의 파일 시스템 수준을 백업하는 두 번의 일정으로 구성됩니다. 다음 명령을 사용하여 백업 작업 일정을 확인할 수 있습니다.

```
#crontab –l
```
다음 명령을 사용하여 언제든지 백업 일정을 변경할 수 있습니다.
```
#crontab -e
```
### <a name="take-a-manual-backup"></a>수동 백업 수행

**cron 작업** 을 사용하여 OS 파일 시스템 백업이 이미 예약되었습니다. 운영 체제 파일 수준 백업을 수동으로도 수행할 수도 있습니다. 수동 백업을 수행하려면 다음 명령을 실행합니다.

```
#rear -v mkbackup
```
다음 화면에서는 샘플 수동 백업을 보여줍니다.

![방법](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="restore-a-backup"></a>백업 복원

백업에서 전체 백업 또는 개별 파일을 복원할 수 있습니다. 복원하려면 다음 명령을 실행합니다.

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
복원한 후에 파일은 현재 작업 디렉터리에 복구됩니다.

다음 명령에서는 백업 파일(*backup.tar.gz*)에서 파일( */etc/fstabfrom*)을 복원하는 것을 보여줍니다.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>백업에서 복원된 후에 원하는 위치에 파일을 복사해야 합니다.

다음 스크린샷은 전체 백업의 복원을 보여줍니다.

![스크린샷에 복원 관련 명령 프롬프트 창이 표시됩니다.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="install-the-rear-tool-and-change-the-configuration"></a>ReaR 도구를 설치하고 구성을 변경합니다 

Relax-and-Recover(ReaR) 패키지는 HANA Large Instances의 **형식 II SKU** 에 **사전 설치** 되어 있습니다. 고객은 어떠한 조치도 취할 필요가 없습니다. 운영 체제 백업을 위해 ReaR 도구를 사용하여 직접 시작할 수 있습니다.

그러나 고유한 패키지를 설치해야 하는 경우, ReaR 도구를 설치하고 구성하는 다음 단계를 따르면 됩니다.

**ReaR** 백업 패키지를 설치하려면 다음 명령을 사용합니다.

**SLES** 운영 체제의 경우, 다음 명령을 사용합니다.
```
#zypper install <rear rpm package>
```
**RHEL** 운영 체제의 경우, 다음 명령을 사용합니다. 

```
#yum install rear -y
```
ReaR 도구를 구성하려면 *file /etc/rear/local.conf* 에서 **OUTPUT_URL** 및 **BACKUP_URL** 매개 변수를 업데이트해야 합니다.

```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

다음 스크린샷은 전체 백업의 복원을 보여 줍니다. ![스크린샷에 ReaR 도구를 사용하는 복원 관련 명령 프롬프트 창이 표시됩니다.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)


## <a name="os-backup-and-restore-for-all-other-skus"></a>모든 다른 SKU에 대한 OS 백업 및 복원

아래 정보는 수정 버전 3의 HANA Large Instances의 **유형 II SKU** 를 제외하고 모든 수정 버전의 모든 SKU에 대한 운영 체제 파일 수준의 백업 및 복원을 수행하는 단계를 설명합니다.

### <a name="take-a-manual-backup"></a>수동 백업 수행

[Azure Application Consistent Snapshot 도구란 무엇인가](../../../azure-netapp-files/azacsnap-introduction.md)로 시작하는 일련의 문서에 설명된 대로 SAP HANA 용 최신 Microsoft Snapshot Tool을 다운로드하여 다름 문서에 설명된 것처럼 구성하고 테스트합니다.

- [Azure 애플리케이션 일치 스냅샷 도구 구성](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Azure Application Consistent Snapshot Tool 테스트](../../../azure-netapp-files/azacsnap-cmd-ref-test.md) 

[Azure Application Consistent Snapshot 도구를 사용한 백업](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)에 설명된 대로 `crontab`을 통해 주기적으로 실행합니다. 

자세한 내용은 다음 문서를 참조하세요.

- [Azure Application Consistent Snapshot 도구 설치](../../../azure-netapp-files/azacsnap-installation.md)
- [Azure 애플리케이션 일치 스냅샷 도구 구성](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [Azure Application Consistent Snapshot Tool 테스트](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [Azure Application Consistent Snapshot Tool을 사용한 백업](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [Azure Application Consistent Snapshot 도구로 세부 정보 가져오기](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [Azure Application Consistent Snapshot 도구를 사용하여 삭제](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [Azure Application Consistent Snapshot 도구를 사용하여 복원](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [Azure 애플리케이션 일치 스냅샷 도구를 사용하여 재해 복구](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [Azure 애플리케이션 일치 스냅샷 도구 문제 해결](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [Azure 애플리케이션 일치 스냅숏 도구 사용을 위한 팁과 요령](../../../azure-netapp-files/azacsnap-tips.md)


### <a name="restore-a-backup"></a>백업 복원

OS 자체에서는 복원 작업을 수행할 수 없습니다. Microsoft 작업을 통해 지원 티켓을 제기해야 합니다. 복원 작업을 수행하려면 HANA Large Instance(HLI) 인스턴스가 전원이 꺼진 상태여야 하므로 그에 따라 일정을 계획합니다.

### <a name="managed-os-snapshots"></a>관리되는 OS 스냅샷

Azure는 HLI 리소스에 대해 자동으로 OS 백업을 수행할 수 있습니다. 이러한 백업은 매일 한 번씩 수행되며, Azure는 최근 3개의 백업을 유지합니다. 이 백업들은 다음 지역의 모든 고객에 대해 기본적으로 사용하도록 설정됩니다.
- 미국 서부
- 오스트레일리아 동부
- 오스트레일리아 남동부
- 미국 중남부
- 미국 동부 2

이 기능은 다음 지역에서 부분적으로 사용할 수 있습니다.
- 미국 동부
- 북유럽
- 서유럽

이 기능에서 수행하는 백업의 빈도 또는 보존 기간은 변경할 수 없습니다. HLI 리소스에 대해 다른 OS 백업 전략이 필요한 경우, Microsoft 작업을 사용하여 지원 티켓을 제기하여 이 기능을 옵트아웃하도록 선택할 수 있습니다. 그런 다음, [수동 백업 실행](#take-a-manual-backup) 섹션의 앞부분에 제공된 지침을 사용하여 OS 백업을 수행 하도록 SAP HANA 용 Microsoft Snapshot Tools를 구성 합니다.

## <a name="next-steps"></a>다음 단계

HANA Large Instances용 kdump 사용 설정 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Large Instances의 SAP HANA용 kdump](hana-large-instance-enable-kdump.md)
