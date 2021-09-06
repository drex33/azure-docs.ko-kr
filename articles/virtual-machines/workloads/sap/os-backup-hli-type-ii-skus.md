---
title: Azure(큰 인스턴스) 형식 II SKU에서 SAP HANA의 운영 체제 백업 및 복원| Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스) 형식 II SKU의 운영 체제 백업 및 복원 수행
services: virtual-machines-linux
documentationcenter: ''
author: jaawasth
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: jaawasth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 245c371a002ebfdad4dd0765ba3d283c81c86f03
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631711"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>수정 버전 3 스탬프의 Type II SKU에 대한 OS 백업 및 복원

본 문서에서는 수정 버전 3의 HANA 대규모 인스턴스의 **형식 II  SKU** 의 운영 체제 파일 레벨 백업 및 복원 수행 단계를 설명합니다. 

>[!Important]
> **해당 문서는 수정 버전 4의 HANA 대규모 인스턴스 스탬프의 형식 II SKU 배포에 적용할 수 없습니다.** 형식 I SKU가 이미 수정 버전 3 스탬프에 있으므로 수정 버전 4의 HANA 대규모 인스턴스 스탬프에 배포된 형식 II HANA 대규모 인스턴스 유닛의 Boot LUNS는 스토리지 스냅샷으로 백업할 수 있습니다.


>[!NOTE]
> * OS 백업 스크립트는 xfsdump 유틸리티를 사용합니다.  
> * 이 문서에서는 전체 루트 파일 시스템 백업을 지원하고 **증분 백업은 지원하지 않습니다**.
> * 백업을 만들 때 동일한 시스템에 작성되는 파일이 없는지 확인합니다.  그렇지 않으면 백업 중에 작성되는 파일이 백업에 포함되지 않을 수 있습니다.
> * ReaR 백업은 수정 버전 3의 HANA 대규모 인스턴스 유형 II SKU에서 사용되지 않습니다.
> * 여러 OS 손상 시나리오에 대해 이 절차를 내부에서 테스트했습니다. 그러나 OS에 대한 책임은 전적으로 고객에게 있으므로, 이 시나리오 설명서에 의존하기 전에 철저하게 테스트하는 것이 좋습니다.
> * SLES OS에서 이 프로세스를 테스트했습니다.
> * SLES 12.x ~ SLES 15x 같은 주 버전 업그레이드는 지원되지 않습니다.
> * 이 프로세스를 사용하여 OS 복원을 완료하려면 복구에 콘솔 액세스가 필요하므로 Microsoft 지원이 필요합니다. 복구를 지원하려면 Microsoft에서 지원 티켓을 만드세요.


## <a name="how-to-take-a-manual-backup"></a>수동 백업을 수행하는 방법

수동 백업을 수행하려면 다음을 따릅니다.

1. 백업 도구를 설치합니다.
   ```
   zypper in xfsdump
   ```

2. 전체 백업을 만듭니다. 
   ```
   xfsdump -l 0 -f /data1/xfs_dump /
   ```

   다음 화면에서는 샘플 수동 백업을 보여줍니다.
   
    [![덤프 캡처](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png)](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png#lightbox)


3. 중요: data1 파티션도 손상된 시나리오에서는 NFS 볼륨에도 백업 복사본을 저장합니다.
   ```
   cp /data1/xfs_dump /osbackup/
   ```

4. 덤프에서 일반 디렉터리와 파일을 제외하려면 chattr로 파일에 태그를 지정하세요.
   * chattr -R +d 디렉터리
   * chattr + d 파일
   * "-e" 옵션으로 xfsdump 실행
   * 참고로, nfs 파일 시스템를 제외할 수는 없음 [ntfs]




## <a name="how-to-restore-a-backup"></a>백업을 복원하는 방법

>[!NOTE]
> * 이 단계에서는 Microsoft 운영팀의 참여가 필요합니다.
> * 이 프로세스를 사용하여 OS 복원을 완료하려면 복구에 콘솔 액세스가 필요하므로 Microsoft 지원이 필요합니다. 복구를 지원하려면 Microsoft에서 지원 티켓을 만드세요.
> * 다음과 같이 전체 파일 시스템을 복원합니다.

1. 시스템에 OS iso를 탑재합니다.

2. 복구 모드로 들어갑니다.

3. 읽기/쓰기 모드에서 data1(또는 덤프가 저장되는 nfs 볼륨) 파티션을 탑재합니다.
   ```
   mount -o rw /dev/md126p4 /mnt1
   ```
4. 읽기/쓰기 모드에서 루트를 탑재합니다.
   ```
   mount -o rw /dev/md126p2 /mnt2
   ```
5. 파일 시스템을 복원합니다.
   ```
   xfsrestore -f /mnt1/xfs_dump /mnt2
   ```
   [![복원 스크린샷](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png)](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png#lightbox)
6. 시스템 다시 부팅합니다.
   ```
   reboot
   ```

사후 검사에 실패하는 경우 콘솔에 액세스하려면 OS 공급업체와 Microsoft에 문의하세요.

## <a name="post-restore-check"></a>복원 후 확인

1. 시스템에서 전체 특성이 복원되었는지 확인합니다.
   * 네트워크가 작동 중입니다.
   * NFS 볼륨이 탑재됩니다.
2. RAID가 구성되어 있는지 확인합니다. RAID 디바이스로 바꾸세요.
   ```
   mdadm -D /dev/md126
   ```
   [![raid 상태](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png)](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png#lightbox)

3. RAID 디스크가 동기화되어 있고 구성이 원래 상태인지 확인합니다.
   * RAID 디스크는 동기화하는 데 시간이 걸립니다. 동기화는 100% 동기화되기 전에 몇 분 동안 계속될 수 있습니다.

4. HANA DB를 시작하고 HANA가 예상대로 작동하는지 확인합니다.

5. HANA가 실행되고 오류가 없는지 확인합니다.
   ```
   hdbinfo
   ```
   [![hana 상태](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png)](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png#lightbox)

6. 사후 검사에 실패하는 경우 콘솔에 액세스하려면 OS 공급업체와 Microsoft에 문의하세요.