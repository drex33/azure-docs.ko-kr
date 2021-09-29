---
title: Azure NetApp Files 백업 | 이해 Microsoft Docs
description: 백업이 수행하는 Azure NetApp Files, 지원되는 지역 및 비용 모델에 대해 설명합니다.
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
ms.date: 09/28/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 8880b128add7e13a34d3b4e3b6b6ad23a8c5df08
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218360"
---
# <a name="understand-azure-netapp-files-backup"></a>Azure NetApp Files 백업 이해

Azure NetApp Files 백업은 장기 복구, 보관 및 규정 준수를 위한 완전 관리형 백업 솔루션을 제공하여 Azure NetApp Files 데이터 보호 기능을 확장합니다. 서비스에서 만든 백업은 단기 복구 또는 복제에 사용할 수 있는 볼륨 스냅샷과 관계없이 Azure Storage에 저장됩니다. 서비스에서 수행된 백업은 지역 내의 새 Azure NetApp Files 볼륨으로 복원할 수 있습니다. Azure NetApp Files 백업은 정책 기반(예약된) 백업과 수동(주문형) 백업을 모두 지원합니다. 자세한 내용은 [Azure NetApp Files 스냅샷 작동 방법을 참조하세요.](snapshots-introduction.md)

> [!IMPORTANT]
> Azure NetApp Files 백업 기능은 현재 미리 보기로 제공됩니다. **[Azure NetApp Files Backup 공개 미리 보기](https://aka.ms/anfbackuppreviewsignup)** 페이지를 통해 기능에 액세스하기 위한 대기 목록 요청을 제출해야 합니다. Azure NetApp Files 백업 기능을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 이메일을 기다립니다.

## <a name="supported-regions"></a>지원되는 지역 

Azure NetApp Files 백업은 다음 지역에서 지원됩니다.   

* 미국 동부
* 미국 동부 2
* 미국 서부 
* 중남부
* 미국 서부2
* 북유럽 
* 서유럽
* 오스트레일리아 동부
* 일본 동부

## <a name="cost-model-for-azure-netapp-files-backup"></a>Azure NetApp Files 백업에 대한 비용 모델

Azure NetApp Files 백업에 대한 가격 책정은 백업에서 사용하는 총 스토리지 용량을 기준으로 합니다. 설치 요금 또는 최소 사용 요금은 없습니다. 백업 복원은 청구 주기 동안 복원된 총 백업 용량 양에 따라 가격이 책정됩니다.

가격 책정 예제로 다음과 같은 상황을 가정합니다.

* 원본 볼륨은 Azure NetApp Files Premium 서비스 수준에서 가져온 것입니다. 볼륨 할당량 크기가 1000GiB이고, 1일부터 500GiB의 볼륨이 사용됩니다. 볼륨이 미국 중남부 지역에 있습니다.
* 유지할 로컬 스냅샷 5개와 백업 복사본 30개 유지를 위한 일일 *백업* 정책을 사용하여 일별 *스냅샷* 정책을 구성했습니다.
* 간단히 하기 위해 원본 볼륨에 매일 1% 일정한 데이터 변경이 있지만 소비된 총 볼륨 크기가 증가하지 않는다고 가정합니다(500 GiB로 유지).

백업 정책이 볼륨에 할당되면 서비스 관리 Azure Storage에 대한 기준 백업이 시작됩니다. 백업이 완료되면 500 GiB의 기준 백업이 볼륨의 백업 목록에 추가됩니다. 기준 전송 후 매일 백업은 변경된 블록만 백업합니다. 매일 5-GiB 증분 백업이 추가되었다고 가정하면 소비되는 총 백업 스토리지는 `500GiB + 30*5GiB = 650GiB` 입니다.

백업에 사용하는 총 스토리지 양에 대해 월 $0.05의 속도로 백업에 대한 요금이 월말에 청구됩니다.  즉, 총 월별 백업 요금이 인 650 `650*$0.05=$32.5` GiB입니다. 일반 Azure NetApp Files 스토리지 용량은 로컬 스냅샷에 적용됩니다. 자세한 내용은 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 페이지를 참조하세요.

예를 들어 600 GiB의 백업을 새 볼륨으로 복원하도록 선택하면 백업 용량 복원의 GiB당 $0.02의 요금이 청구됩니다. 이 경우 `600*$0.02 = $12` 복원 작업을 위한 것입니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files 대한 요구 사항 및 고려 사항](backup-requirements-considerations.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [정책 기반 백업 구성](backup-configure-policy-based.md)
* [수동 백업 구성](backup-configure-manual.md)
* [백업 정책 관리](backup-manage-policies.md)
* [백업 검색](backup-search.md)
* [새 볼륨으로 백업 복원](backup-restore-new-volume.md)
* [볼륨에 대한 백업 기능 사용 안 함](backup-disable.md)
* [볼륨의 백업 삭제](backup-delete.md)
* [볼륨 백업 메트릭](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files 백업 FAQ](azure-netapp-files-faqs.md#azure-netapp-files-backup-faqs)
* [Azure NetApp Files 스냅샷 작동 방식](snapshots-introduction.md)
