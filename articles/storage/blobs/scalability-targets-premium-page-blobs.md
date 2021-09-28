---
title: 프리미엄 페이지 Blob 스토리지 계정의 스케일링 목표
titleSuffix: Azure Storage
description: 프리미엄 성능 페이지 Blob 스토리지 계정은 읽기/쓰기 작업에 최적화되어 있습니다. 이 유형의 스토리지 계정은 Azure 가상 머신에 대해 비관리 디스크를 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/24/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d9d669d583563e81fa55d3626e6505ebe108340c
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153466"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>프리미엄 페이지 Blob 스토리지 계정의 스케일링 및 성능 목표

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>프리미엄 페이지 Blob 계정의 스케일링 목표

프리미엄 성능 페이지 Blob 스토리지 계정은 읽기/쓰기 작업에 최적화되어 있습니다. 이 유형의 스토리지 계정은 Azure 가상 머신에 대해 비관리 디스크를 지원합니다.

> [!NOTE]
> Microsoft에서는 가능하면 관리 디스크를 Azure VM(가상 머신)과 함께 사용할 것을 권장합니다. 관리 디스크에 대한 자세한 내용은 [VM에 대한 Azure Disk Storage 개요](../../virtual-machines/managed-disks-overview.md)를 참조하세요.

프리미엄 페이지 Blob 스토리지 계정의 스케일링 목표는 다음과 같습니다.

| 총 계정 용량                            | 로컬 중복 스토리지 계정의 총 대역폭                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 디스크 용량: 4TB(개별 디스크)/35TB(모든 디스크의 누적 합계) <br>스냅샷 용량: 10 TB<sup>3</sup> | 인바운드<sup>1</sup> + 아웃바운드<sup>2</sup>에 대해 초당 최대 50기가비트 |

<sup>1</sup> 스토리지 계정으로 전송되는 모든 데이터(요청)

<sup>2</sup> 스토리지 계정에서 수신하는 모든 데이터(응답)

<sup>3</sup> 개별 페이지 Blob이 가질 수 있는 총 스냅샷 수는 100개입니다.

프리미엄 페이지 Blob 계정은 프리미엄 성능을 위해 구성된 범용 계정입니다. 범용 v2 스토리지 계정을 사용하는 것이 좋습니다.

비관리 디스크에 프리미엄 페이지 Blob 스토리지 계정을 사용하며 애플리케이션이 단일 스토리지 계정의 스케일링 목표를 초과하는 경우, 관리 디스크로 마이그레이션하는 것이 좋습니다. 관리 디스크에 대한 자세한 내용은 [VM에 대한 Azure Disk Storage 개요](../../virtual-machines/managed-disks-overview.md)를 참조하세요.

관리 디스크로 마이그레이션할 수 없는 경우 애플리케이션이 다수의 스토리지 계정을 사용하도록 빌드하고 해당 스토리지 계정 전반의 데이터를 분할합니다. 예를 들어 51TB 디스크를 여러 VM에 연결하려는 경우에는 두 개의 스토리지 계정에 분산합니다. 단일 Premium Storage 계정의 한도는 35TB입니다. 단일 프리미엄 성능 스토리지 계정에 35TB를 넘는 프로비저닝된 디스크가 없도록 해야 합니다.

## <a name="see-also"></a>참고 항목

- [표준 스토리지 계정의 확장성 및 성능 목표](../common/scalability-targets-standard-account.md)
- [프리미엄 블록 Blob 스토리지 계정의 스케일링 목표](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure 구독 제한 및 할당량](../../azure-resource-manager/management/azure-subscription-service-limits.md)
