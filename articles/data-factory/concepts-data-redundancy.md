---
title: Azure Data Factory의 데이터 중복도 | Microsoft Docs
description: Azure Data Factory의 메타데이터 중복도 메커니즘에 대해 알아봅니다.
author: nabhishek
ms.reviewer: abnarain
ms.service: data-factory
ms.subservice: concepts
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 739ba42cbc39b9a902353ce5e831cfdf21090254
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436955"
---
# <a name="azure-data-factory-data-redundancy"></a>**Azure Data Factory 데이터 중복도**

Azure Data Factory 데이터에는 메타데이터(파이프라인, 데이터 세트, 연결된 서비스, 통합 런타임 및 트리거)와 모니터링 데이터(파이프라인, 트리거 및 활동 실행)가 포함됩니다. 

모든 지역(브라질 남부 및 동남 아시아 제외)에서 Azure Data Factory 데이터는 메타데이터 손실을 방지하기 위해 [쌍을 이루는 지역](../availability-zones/cross-region-replication-azure.md#azure-cross-region-replication-pairings-for-all-geographies)에 저장되고 복제됩니다. 지역 데이터 센터 오류가 발생하는 동안 Microsoft는 Azure Data Factory 인스턴스의 지역 장애 조치(failover)를 시작할 수 있습니다. 대부분 사용자의 작업은 필요하지 않습니다. Microsoft에서 관리하는 장애 조치(failover)가 완료되면 장애 조치(failover) 지역의 Azure Data Factory에 액세스할 수 있습니다. 

브라질 남부 및 동남 아시아에서는 데이터 보존 요구 사항으로 인해 Azure Data Factory 데이터가 [현지 지역에만](../storage/common/storage-redundancy.md#locally-redundant-storage) 저장됩니다. 동남 아시아의 경우 모든 데이터가 싱가포르에 저장됩니다. 브라질 남부의 경우 모든 데이터가 브라질에 저장됩니다. 심각한 재해가 발생하여 지역이 손실된 경우 Microsoft가 Azure Data Factory 데이터를 복구할 수 없습니다.  

> [!NOTE]
> Microsoft에서 관리하는 장애 조치(failover)는 일반적으로 고객이 관리하는 SHIR(자체 호스팅 통합 런타임)에는 적용되지 않습니다. Azure VM에 SHIR이 설정된 경우 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)를 활용하여 다른 지역으로의 [Azure VM 장애 조치(failover)](../site-recovery/azure-to-azure-architecture.md)를 처리하는 것이 좋습니다.



## <a name="using-source-control-in-azure-data-factory"></a>**Azure Data Factory에서 원본 제어 사용**

Azure Data Factory 메타데이터 변경 내용을 추적하고 감사하려면 Azure Data Factory에 원본 제어를 설정하는 것이 좋습니다. 파이프라인, 데이터 세트, 연결된 서비스, 트리거에 대한 메타데이터 JSON 파일에도 액세스할 수 있습니다. Azure Data Factory를 통해 다양한 Git 리포지토리(Azure DevOps 및 GitHub)를 사용할 수 있습니다. 

 [Azure Data Factory에서 원본 제어를 설정](./source-control.md)하는 방법을 알아봅니다. 

> [!NOTE]
> 재해(지역 손실)가 발생하면 수동 또는 자동화된 방식으로 새 데이터 팩터리를 프로비전할 수 있습니다. 새 데이터 팩터리를 만들면 기존 Git 리포지토리에서 파이프라인, 데이터 세트, 연결된 서비스 JSON을 복원할 수 있습니다. 



## <a name="data-stores"></a>**데이터 저장소**

Azure Data Factory를 사용하면 온-프레미스와 클라우드에 있는 데이터 저장소 간에 데이터를 이동할 수 있습니다. 데이터 저장소의 비즈니스 연속성을 보장하려면 이러한 각 데이터 저장소에 대한 비즈니스 연속성 권장 사항을 참조해야 합니다. 

 

## <a name="see-also"></a>참고 항목

- [Azure 지역 쌍](../availability-zones/cross-region-replication-azure.md)
- [Azure의 데이터 보존](https://azure.microsoft.com/global-infrastructure/data-residency/)