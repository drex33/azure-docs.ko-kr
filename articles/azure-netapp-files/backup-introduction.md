---
title: Azure NetApp Files 백업 이해 | Microsoft Docs
description: 백업 Azure NetApp Files, 지원 되는 지역 및 비용 모델을 설명 합니다.
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
ms.date: 10/13/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 3836dc37a6be1b21a46424cbcf14a8960908969d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256327"
---
# <a name="understand-azure-netapp-files-backup"></a>Azure NetApp Files 백업 이해

Azure NetApp Files 백업은 장기 복구, 보관 및 규정 준수를 위해 완벽 하 게 관리 되는 백업 솔루션을 제공 하 여 Azure NetApp Files의 데이터 보호 기능을 확장 합니다. 서비스에서 생성 된 백업은 단기 복구 또는 복제에 사용할 수 있는 볼륨 스냅숏과는 별도로 Azure storage에 저장 됩니다. 서비스에서 수행한 백업은 해당 지역 내의 새 Azure NetApp Files 볼륨으로 복원할 수 있습니다. Azure NetApp Files 백업은 정책 기반 (예약 된) 백업과 수동 (주문형) 백업을 모두 지원 합니다. 자세한 내용은 [Azure NetApp Files 스냅숏 작동 방법](snapshots-introduction.md)을 참조 하세요.

> [!IMPORTANT]
> Azure NetApp Files 백업 기능은 현재 미리 보기로 제공 됩니다. **[Azure NetApp Files 백업 공개 미리 보기](https://aka.ms/anfbackuppreviewsignup)** 페이지를 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. Azure NetApp Files 팀에서 Azure NetApp Files 백업 기능을 사용 하기 전에 공식 확인 전자 메일을 기다리십시오.

## <a name="supported-regions"></a>지원되는 지역 

Azure NetApp Files 백업은 다음 지역에서 지원 됩니다.   

* 미국 동부
* 미국 동부 2
* 미국 서부 
* 남아프리카 중부
* 미국 서부 2
* 북유럽 
* 서유럽
* 오스트레일리아 동부
* 일본 동부

## <a name="cost-model-for-azure-netapp-files-backup"></a>Azure NetApp Files 백업에 대 한 비용 모델

Azure NetApp Files 백업의 가격은 백업에서 사용 하는 저장소의 총 크기를 기준으로 합니다. 설정 요금 또는 최소 사용 요금은 없습니다. 백업 복원은 청구 주기 동안 복원 된 백업 용량의 총 크기를 기준으로 가격이 책정 됩니다.

가격 책정 예를 들어 다음과 같은 상황을 가정해 보겠습니다.

* 원본 볼륨은 Azure NetApp Files Premium 서비스 수준에서 가져온 것입니다. 볼륨 할당량 크기가 1000GiB이고, 1일부터 500GiB의 볼륨이 사용됩니다. 볼륨이 미국 중남부 지역에 있습니다.
* 5 개의 로컬 스냅숏이 유지 되도록 일별 *스냅숏* 정책을 구성 하 고 백업 복사본을 30 개 유지 하는 매일 *백업* 정책을 구성 했습니다.
* 간단히 하기 위해 원본 볼륨에는 매일 일정 1%의 데이터 변경이 있지만 사용 된 총 볼륨 크기는 증가 하지 않는다고 가정 합니다 (500 GiB에 유지 됨).

백업 정책이 볼륨에 할당 되 면 서비스에서 관리 하는 Azure storage에 대 한 기준 백업이 시작 됩니다. 백업이 완료 되 면 500 GiB의 기준 백업이 볼륨의 백업 목록에 추가 됩니다. 기준 전송 후 매일 백업은 변경 된 블록만 백업 합니다. 5-GiB 일별 증분 백업이 추가 된 것으로 가정 합니다. 사용 된 총 백업 저장소는 `500GiB + 30*5GiB = 650GiB` 입니다.

백업에서 사용 하는 총 저장소 용량에 대해 매월 $0.05의 속도로 백업에 대 한 요금이 청구 됩니다.  즉, 650 GiB의 총 월별 백업 요금이 청구 됩니다 `650*$0.05=$32.5` . 일반 Azure NetApp Files 저장소 용량은 로컬 스냅숏에 적용 됩니다. 자세한 내용은 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 페이지를 참조하세요.

백업 (예: 600 GiB)을 새 볼륨으로 복원 하도록 선택 하면 백업 용량 복원 GiB $0.02 요금이 청구 됩니다. 이 경우 복원 작업을 수행 하는 것 `600*$0.02 = $12` 입니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대 한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대 한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](faq-backup.md)
* [Azure NetApp Files 스냅샷 작동 방식](snapshots-introduction.md)
