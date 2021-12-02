---
title: Azure NetApp Files에 대한 FAQ | Microsoft Docs
description: Azure NetApp Files 용량 관리에 대한 FAQ(질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 10/11/2021
ms.openlocfilehash: 530fa377788c66c4485e49c253971ea7cc2f72cf
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487362"
---
# <a name="capacity-management-faqs-for-azure-netapp-files"></a>Azure NetApp Files 대한 용량 관리 FAQ

이 문서에서는 Azure NetApp Files 용량 관리에 대한 FAQ(질문과 대답)를 제공합니다.

## <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Azure NetApp Files의 용량 풀 및 볼륨에 대한 사용량을 모니터링하려면 어떻게 할까요? 

Azure NetApp Files는 용량 풀 및 볼륨 사용 현황 메트릭을 제공합니다. Azure Monitor를 사용하여 Azure NetApp Files의 사용량을 모니터링할 수도 있습니다. 자세한 내용은 [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)을 참조하세요. 

## <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>디렉터리가 제한 크기에 도달하고 있는지 확인하려면 어떻게 할까요?

클라이언트에서 `stat` 명령을 사용하여 디렉터리가 디렉터리 메타데이터의 [최대 크기 한도](azure-netapp-files-resource-limits.md#resource-limits)(320MB)에 도달하는지 여부를 확인할 수 있습니다.
한도 및 계산은 [Azure NetApp Files에 대한 리소스 한도](azure-netapp-files-resource-limits.md#directory-limit)를 참조하세요. 

## <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>스냅샷 공간이 볼륨의 가용/프로비저닝된 용량에 포함되나요?

예. [사용된 스냅샷 용량](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)은 볼륨의 프로비저닝된 공간에 포함됩니다. 볼륨이 가득 찬 경우 다음 작업을 수행하는 것이 좋습니다.

* [볼륨의 크기를 조정](azure-netapp-files-resize-capacity-pools-or-volumes.md)합니다.
* [이전 스냅샷을 제거](snapshots-delete.md)하여 호스팅 볼륨의 공간을 확보합니다. 

## <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Azure NetApp Files는 볼륨 또는 용량 풀에 대해 자동 증가를 지원하나요?

아니요, Azure NetApp Files 볼륨 및 용량 풀은 가득 찰 때 자동으로 증가하지 않습니다. [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)을 참조하세요.   

커뮤니티에서 지원하는 [Logic Apps ANFCapacityManager 도구](https://github.com/ANFTechTeam/ANFCapacityManager)를 사용하여 용량 기반 경고 규칙을 관리할 수 있습니다. 이 도구는 볼륨 공간이 부족하지 않도록 볼륨 크기를 자동으로 늘릴 수 있습니다.

## <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>복제 대상 볼륨은 하드 볼륨 할당량에 포함되나요?  

아니요, 복제 대상 볼륨은 하드 볼륨 할당량에 포함되지 않습니다.

## <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure Storage Explorer를 통해 Azure NetApp Files를 관리할 수 있나요?

아니요. Azure NetApp Files는 Azure Storage Explorer에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계  

- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
- [통합 FAQ](faq-integration.md)
