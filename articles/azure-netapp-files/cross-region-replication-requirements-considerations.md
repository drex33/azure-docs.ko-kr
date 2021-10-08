---
title: Azure NetApp Files 볼륨 지역 간 복제를 사용하기 위한 요구 사항 및 고려 사항 | Microsoft Docs
description: Azure NetApp Files의 볼륨 지역 간 복제 기능을 사용하기 위한 요구 사항 및 고려 사항을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: b-juche
ms.openlocfilehash: 6e711f3101426f59aead80b84b88350af86bbd6b
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669191"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>지역 간 복제 사용을 위한 요구 사항 및 고려 사항 

Azure NetApp Files의 [볼륨 지역 간 복제를 사용하는 것](cross-region-replication-create-peering.md)에 대한 다음 요구 사항 및 고려 사항에 유의하세요.  

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항 

* 지역 간 복제 기능은 현재 미리 보기로 제공됩니다. [Azure NetApp Files 지역 간 복제 대기 목록 제출 페이지](https://aka.ms/anfcrrpreviewsignup)를 통해 기능에 액세스하기 위한 대기 목록 요청을 제출해야 합니다. 지역 간 복제 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 메일을 기다리세요.
* Azure NetApp Files 복제는 특정 고정 지역 쌍에서만 사용할 수 있습니다. [지원되는 지역 쌍](cross-region-replication-introduction.md#supported-region-pairs)을 참조하세요. 
* SMB 볼륨은 NFS 볼륨과 함께 지원됩니다. SMB 볼륨을 복제하려면 원본 및 대상 NetApp 계정에 Active Directory 연결이 필요합니다. 대상 AD 연결에는 대상 지역의 위임된 서브넷에서 연결할 수 있는 DNS 서버 또는 ADDS 도메인 컨트롤러에 대한 액세스 권한이 있어야 합니다. 자세한 내용은 [Active Directory 연결에 대한 요구 사항](create-active-directory-connections.md#requirements-for-active-directory-connections)을 참조하세요. 
* 대상 계정은 원본 볼륨 지역과 다른 지역에 있어야 합니다. 다른 지역에서 기존 NetApp 계정을 선택할 수 있습니다.  
* 대상 볼륨에 읽기 및 쓰기를 사용하도록 설정하기 위해 [대상 지역으로 장애 조치(failover)](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume)할 때까지 복제 대상 볼륨은 읽기 전용입니다. 
* Azure NetApp Files 복제는 현재 여러 구독을 지원하지 않습니다. 모든 복제는 단일 구독에서 수행해야 합니다.
* 지역 간 복제 대상 볼륨의 최대 수는 [리소스 제한을](azure-netapp-files-resource-limits.md) 참조하세요. 지원 티켓을 열어 복제 대상 볼륨의 기본 할당량 [한도 증가를 요청할](azure-netapp-files-resource-limits.md#request-limit-increase) 수 있습니다(지역의 구독당).
* 인터페이스가 원본 볼륨에 새로 추가된 스냅샷을 반영하는 데 최대 5분이 지연될 수 있습니다.  
* 연계 및 팬 입력/출력 토폴로지는 지원되지 않습니다.
* 스냅샷에 생성된 원본 볼륨에 대한 볼륨 복제 구성은 지금은 지원되지 않습니다.
* 지역 간 복제를 설정하고 나면 복제 프로세스에서 원본 볼륨과 대상 볼륨 간의 참조를 제공하는 *snapmirror 스냅샷* 을 만듭니다. Snapmirror 스냅샷은 모든 증분 전송에 대해 새 스냅샷이 생성될 때 자동으로 순환됩니다. 복제 관계와 볼륨이 삭제될 때까지 snapmirror 스냅샷을 삭제할 수 없습니다. 
* 복제 관계가 활성 상태이거나 손상된 경우 및 복제 관계가 삭제된 후에 복제 관계의 원본 볼륨에서 수동 스냅샷을 삭제할 수 있습니다. 복제 관계가 끊어질 때까지 대상 볼륨의 수동 스냅샷을 삭제할 수 없습니다.
* 지역 간 복제의 원본 또는 대상 볼륨을 스냅샷으로 되돌릴 수 없습니다. 스냅샷 되돌리기 기능은 복제 관계의 볼륨에 대해 회색으로 표시됩니다. 

## <a name="next-steps"></a>다음 단계
* [볼륨 복제 만들기](cross-region-replication-create-peering.md)
* [복제 관계의 상태 표시](cross-region-replication-display-health-status.md)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [볼륨 복제 또는 볼륨 삭제](cross-region-replication-delete.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)


