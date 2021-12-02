---
title: Azure NetApp Files 백업 FAQ | Microsoft Docs
description: Azure NetApp Files 백업 기능 사용에 대한 FAQ(질문과 대답)입니다.
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-hchen
ms.author: b-hchen
ms.date: 10/11/2021
ms.openlocfilehash: df98b54bde61d50333992ec6980bb9fdd47b1681
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133482699"
---
# <a name="azure-netapp-files-backup-faqs"></a>Azure NetApp Files 백업 FAQ

이 문서에서는 [Azure NetApp Files 백업](backup-introduction.md) 기능 사용에 대한 FAQ(질문과 대답)를 제공합니다. 

## <a name="when-do-my-backups-occur"></a>내 백업은 언제 발생합니까?   

Azure NetApp Files 백업은 백업 정책의 빈도가 입력된 후 임의 시간 프레임 내에 시작됩니다. 예를 들어 매주 백업은 오전 12시 이후에 임의로 할당된 간격 내에 일요일 시작됩니다. 자정. 이 타이밍은 현재 사용자가 수정할 수 없습니다. 기준 백업은 볼륨에 백업 정책을 할당하는 즉시 시작됩니다.

## <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>백업 작업에 문제가 발생하면 어떻게 되나요?

백업 작업 중에 문제가 발생하는 경우 Azure NetApp Files 백업은 사용자 상호 작용 없이 작업을 자동으로 다시 수행합니다. 다시 작업이 계속 실패하면 Azure NetApp Files 백업에서 작업 실패를 보고합니다.

## <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>백업 자격 증명 모음의 위치 또는 스토리지 계층을 변경할 수 있나요?

아니요, Azure NetApp Files Azure Storage 내에서 백업 데이터 위치를 자동으로 관리합니다. 이 위치 또는 Azure Storage 계층은 사용자가 수정할 수 없습니다.

## <a name="what-types-of-security-are-provided-for-the-backup-data"></a>백업 데이터에 대해 제공되는 보안 유형은 무엇인가요?

Azure NetApp Files 받은 백업 데이터를 인코딩하는 동안 AES-256 비트 암호화를 사용합니다. 또한 암호화된 데이터는 HTTPS TLSv1.2 연결을 사용하여 Azure Storage로 안전하게 전송됩니다. Azure NetApp Files 백업은 백업 데이터를 저장하기 위한 Azure Storage 계정의 미사용 데이터 암호화 기능에 따라 달라집니다.

## <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>볼륨 또는 NetApp 계정을 삭제하면 백업은 어떻게 되나요? 

 Azure NetApp Files 볼륨을 삭제하면 백업이 유지됩니다. 백업을 유지하지 않으려면 볼륨을 삭제하기 전에 백업을 사용하지 않도록 설정합니다. NetApp 계정을 삭제해도 동일한 구독의 다른 NetApp 계정에서 백업이 유지되고 표시되므로 복원에 계속 사용할 수 있습니다. 구독에서 모든 NetApp 계정을 삭제하는 경우 모든 NetApp 계정의 모든 볼륨을 삭제하기 전에 백업을 사용하지 않도록 설정해야 합니다.

## <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>볼륨에 대한 시스템의 최대 백업 재시도는 어떻게 되나요?  

시스템은 예약된 백업 작업을 처리할 때 10회 재시도를 수행합니다. 작업이 실패하면 시스템에서 백업 작업이 실패합니다. 구성된 정책에 따라 예약된 백업의 경우 시스템은 1시간마다 한 번씩 데이터를 백업하려고 시도합니다. 전송되지 않았거나 마지막 시도 중에 실패한 새 스냅샷을 사용할 수 있는 경우 해당 스냅샷은 전송으로 간주됩니다. 

## <a name="next-steps"></a>다음 단계  

- [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [네트워킹 FAQ](faq-networking.md)
- [보안 FAQ](faq-security.md)
- [성능 FAQ](faq-performance.md)
- [NFS FAQ](faq-nfs.md)
- [SMB FAQ](faq-smb.md)
- [용량 관리 FAQ](faq-capacity-management.md)
- [데이터 마이그레이션 및 보호 FAQ](faq-data-migration-protection.md)
- [애플리케이션 복원력 FAQ](faq-application-resilience.md)
- [통합 FAQ](faq-integration.md)
