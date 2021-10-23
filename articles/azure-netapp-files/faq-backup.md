---
title: Azure NetApp Files backup Faq | Microsoft Docs
description: Azure NetApp Files 백업 기능 사용에 대 한 Faq (질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: cec399e00215994e55e06430f2170d8c19c20b03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273761"
---
# <a name="azure-netapp-files-backup-faqs"></a>Azure NetApp Files 백업 FAQ

이 문서에서는 [Azure NetApp Files 백업](backup-introduction.md) 기능 사용에 대 한 faq (질문과 대답)에 대해 설명 합니다. 

## <a name="when-do-my-backups-occur"></a>백업이 언제 발생 하나요?   

백업 정책 빈도를 입력 한 후에는 임의 시간 프레임 내에서 백업이 시작 Azure NetApp Files. 예를 들어 주간 백업은 오전 12:00 이후 무작위로 할당 된 간격 내에 시작 됩니다. 자정이. 지금은 사용자가이 타이밍을 수정할 수 없습니다. 백업 정책을 볼륨에 할당 하는 즉시 기준 백업이 시작 됩니다.

## <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>백업 작업에 문제가 발생 하는 경우 어떻게 되나요?

백업 작업 중에 문제가 발생 하는 경우 Azure NetApp Files 백업에서 자동으로 작업을 다시 시도 하며 사용자 조작이 필요 하지 않습니다. 재시도를 계속 실패할 경우 백업 Azure NetApp Files 작업의 실패를 보고 합니다.

## <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>백업 자격 증명 모음의 위치 또는 저장소 계층을 변경할 수 있나요?

아니요, Azure NetApp Files는 Azure storage 내에서 백업 데이터 위치를 자동으로 관리 합니다. 이 위치 또는 Azure 저장소 계층은 사용자가 수정할 수 없습니다.

## <a name="what-types-of-security-are-provided-for-the-backup-data"></a>백업 데이터에 제공 되는 보안 유형은 무엇 인가요?

Azure NetApp Files는 수신 된 백업 데이터를 인코딩하는 동안 AES-256 비트 암호화를 사용 합니다. 또한 암호화 된 데이터는 HTTPS TLSv 1.2 연결을 사용 하 여 Azure storage로 안전 하 게 전송 됩니다. Azure NetApp Files 백업은 백업 데이터를 저장 하는 데 사용할 Azure Storage 계정의 미사용 암호화 기능에 따라 달라 집니다.

## <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>볼륨이 나 NetApp 계정을 삭제할 때의 백업은 어떻게 되나요? 

 Azure NetApp Files 볼륨을 삭제 하면 백업이 유지 됩니다. 백업을 보존 하지 않으려면 볼륨을 삭제 하기 전에 백업을 사용 하지 않도록 설정 합니다. NetApp 계정을 삭제 하면 백업이 계속 유지 되 고 동일한 구독의 다른 NetApp 계정에 표시 되므로 여전히 복원에 사용할 수 있습니다. 구독에서 모든 NetApp 계정을 삭제 하는 경우 모든 NetApp 계정에서 모든 볼륨을 삭제 하기 전에 백업을 사용 하지 않도록 설정 해야 합니다.

## <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>볼륨에 대 한 시스템의 최대 백업 다시 시도는 무엇 인가요?  

예약 된 백업 작업을 처리할 때 시스템에서 10 번 다시 시도 합니다. 작업이 실패 하면 시스템에서 백업 작업이 실패 합니다. 구성 된 정책에 따라 예약 된 백업의 경우 시스템은 매시간 데이터를 백업 하려고 시도 합니다. 전송 되지 않은 새 스냅숏 (또는 마지막 시도 중에 실패 한 경우)을 사용할 수 있는 경우 해당 스냅숏은 전송 대상으로 고려 됩니다. 

## <a name="next-steps"></a>다음 단계  

- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [응용 프로그램 복원 력 Faq](faq-application-resilience.md)
- [통합 Faq](faq-integration.md)
