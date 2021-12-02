---
title: Azure NetApp Files | 대한 성능 FAQ Microsoft Docs
description: Azure NetApp Files 성능에 대한 FAQ(질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 10/11/2021
ms.openlocfilehash: 7c3af1aa70c41edee4206ea9c6750189be32a955
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481312"
---
# <a name="performance-faqs-for-azure-netapp-files"></a>Azure NetApp Files 대한 성능 FAQ

이 문서에서는 Azure NetApp Files 성능에 대한 FAQ(질문과 대답)를 제공합니다.

## <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files 성능을 최적화하거나 튜닝하려면 어떻게 해야 하나요?

성능 요구 사항에 따라 다음 작업을 수행할 수 있습니다. 
- Virtual Machine의 크기가 적절한지 확인합니다.
- 가속화된 네트워킹을 VM에 사용하도록 설정합니다.
- 용량 풀에 대해 원하는 서비스 수준 및 크기를 선택합니다.
- 용량 및 성능에 대해 원하는 할당량 크기의 볼륨을 만듭니다.

## <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files의 처리량 기반 서비스 수준을 IOPS로 변환하려면 어떻게 할까요?

다음 수식을 사용하여 MB/초를 IOPS로 변환할 수 있습니다.  

`IOPS = (MBps Throughput / KB per IO) * 1024`

## <a name="how-do-i-change-the-service-level-of-a-volume"></a>볼륨의 서비스 수준을 변경하려면 어떻게 할까요?

볼륨에 대해 원하는 [서비스 수준](azure-netapp-files-service-levels.md)을 사용하는 다른 용량 풀로 볼륨을 이동하여 기존 볼륨의 서비스 수준을 변경할 수 있습니다. [볼륨의 서비스 수준을 동적으로 변경](dynamic-change-volume-service-level.md)을 참조하세요. 

## <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp Files 성능을 모니터링하려면 어떻게 할까요?

Azure NetApp Files는 볼륨 성능 메트릭을 제공합니다. Azure Monitor를 사용하여 Azure NetApp Files에 대한 사용 현황 메트릭을 모니터링할 수도 있습니다.  Azure NetApp Files에 대한 성능 메트릭 목록은 [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)을 참조하세요.

## <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv4.1에서 Kerberos의 성능에 미치는 영향은 어떻게 되나요?

NFSv4.1의 보안 옵션, 테스트된 성능 벡터 및 예상되는 성능 영향에 대한 자세한 내용은 [NFSv4.1에서 Kerberos의 성능 영향](performance-impact-kerberos.md)을 참조하세요. 

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files에서 SMB 다이렉트를 지원하나요?

아니요. Azure NetApp Files SMB 다이렉트를 지원하지 않습니다. 

## <a name="is-nic-teaming-supported-in-azure"></a>Azure에서 NIC 팀이 지원되나요?

NIC 팀은 Azure에서 지원되지 않습니다. Azure 가상 머신에서는 여러 네트워크 인터페이스가 지원되지만 물리적 구조가 아닌 논리적 구조를 나타냅니다. 따라서 내결함성을 제공하지 않습니다.  또한 Azure 가상 머신에서 사용할 수 있는 대역폭은 개별 네트워크 인터페이스가 아닌 컴퓨터 자체에 대해 계산됩니다.

## <a name="are-jumbo-frames-supported"></a>점보 프레임은 지원되나요?

점보 프레임은 Azure 가상 머신에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계  

- [NFSv4.1 볼륨에서 Kerberos의 성능 영향](performance-impact-kerberos.md)
- [Azure NetApp Files의 성능 고려 사항](azure-netapp-files-performance-considerations.md    )
- [Azure NetApp Files의 성능 벤치마크 테스트 추천 사항](azure-netapp-files-performance-metrics-volumes.md )
- [Linux용 성능 벤치마크](performance-benchmarks-linux.md)
- [NFSv4.1 볼륨에서 Kerberos의 성능 영향](performance-impact-kerberos.md)
- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files 백업 FAQ](faq-backup.md)
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
- [통합 FAQ](faq-integration.md)