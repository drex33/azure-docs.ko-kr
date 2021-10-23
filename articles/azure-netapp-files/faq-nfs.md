---
title: Azure NetApp Files | 대한 NFS FAQ Microsoft Docs
description: Azure NetApp Files NFS 프로토콜에 대한 FAQ(질문과 대답)에 답변합니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/19/2021
ms.openlocfilehash: e9c38f573a613debbb9f3e3c6b1da00ab23f92f1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273731"
---
# <a name="nfs-faqs-for-azure-netapp-files"></a>Azure NetApp Files 대한 NFS FAQ

이 문서에서는 Azure NetApp Files NFS 프로토콜에 대한 FAQ(질문과 대답)에 답변합니다.

## <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Azure VM을 시작하거나 다시 부팅할 때 볼륨이 자동으로 탑재되도록 하려고 합니다.  내 호스트를 영구 NFS 볼륨에 대해 구성하려면 어떻게 할까요?

VM 시작 또는 다시 부팅 시 NFS 볼륨이 자동으로 탑재되도록 하려면 항목을 호스트의 `/etc/fstab` 파일에 추가합니다. 

자세한 내용은 [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)를 참조하세요.  

## <a name="what-nfs-version-does-azure-netapp-files-support"></a>Azure NetApp Files에서 지원하는 NFS 버전은 무엇인가요?

Azure NetApp Files는 NFSv3 및 NFSv4.1을 지원합니다. NFS 버전 중 하나를 사용하여 [볼륨을 만들](azure-netapp-files-create-volumes.md) 수 있습니다. 

## <a name="how-do-i-enable-root-squashing"></a>루트 quash 병합을 사용하도록 설정하려면 어떻게 할까요?

볼륨의 내보내기 정책을 사용하여 루트 계정에서 볼륨에 액세스할 수 있는지 여부를 지정할 수 있습니다. 자세한 내용은 [NFS 볼륨에 대한 내보내기 정책 구성](azure-netapp-files-configure-export-policy.md)을 참조하세요.

## <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>동일한 파일 경로(볼륨 만들기 토큰)를 여러 볼륨에 사용할 수 있나요?

예, 할 수 있습니다. 그러나 파일 경로는 각 서브넷 내에서 고유해야 합니다.     

## <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Windows 클라이언트를 통해 NFS 볼륨에 액세스하려고 할 때 클라이언트에서 폴더 및 하위 폴더를 검색하는 데 오래 걸리는 이유는 무엇인가요?

Windows 클라이언트에서 `CaseSensitiveLookup`을 사용하도록 설정하여 폴더 및 하위 폴더의 조회 속도를 높일 수 있는지 확인합니다.

1. 다음 PowerShell 명령을 사용하여 CaseSensitiveLookup을 사용하도록 설정합니다.   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. 볼륨을 Windows 서버에 탑재합니다.   
    예제:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

## <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Azure NetApp Files에서 NFSv4.1 파일 잠금을 지원하려면 어떻게 해야 하나요? 

NFSv4.1 클라이언트의 경우 Azure NetApp Files는 임대 기반 모델에서 모든 파일 잠금 상태를 유지 관리하는 NFSv4.1 파일 잠금 메커니즘을 지원합니다. 

RFC 3530에 따라 Azure NetApp Files는 NFS 클라이언트에서 보유한 모든 상태에 대한 단일 임대 기간을 정의합니다. 클라이언트에서 정의된 기간 내에 임대를 갱신하지 않으면 서버에서 클라이언트의 임대와 관련된 모든 상태를 해제합니다.  

예를 들어 볼륨을 탑재하는 클라이언트에서 응답하지 않거나 시간 제한을 초과하여 작동이 중단되면 잠금이 해제됩니다. 클라이언트는 파일 읽기 등의 작업을 수행하여 임대를 명시적 또는 암시적으로 갱신할 수 있습니다.   

유예 기간은 클라이언트에서 서버 복구 중에 잠금 상태를 회수하려고 시도할 수 있는 특별한 처리 기간을 정의합니다. 기본 임대 시간 제한은 30초이며, 유예 기간은 45초입니다. 해당 시간이 지나면 클라이언트의 임대가 해제됩니다. 

## <a name="next-steps"></a>다음 단계  

- [Microsoft Azure ExpressRoute FAQ](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network FAQ](../virtual-network/virtual-networks-faq.md)
- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Azure NetApp Files의 SMB 성능에 대한 FAQ](azure-netapp-files-smb-performance.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
- [통합 FAQ](faq-integration.md)