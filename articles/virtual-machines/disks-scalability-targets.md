---
title: VM 디스크의 확장성 및 성능 목표
description: VM에 연결된 가상 머신 디스크의 확장성 및 성능 목표를 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 09/27/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 773b4dbe5bcabc459b832f450610c395a981650a
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155169"
---
# <a name="scalability-and-performance-targets-for-vm-disks"></a>VM 디스크의 확장성 및 성능 목표

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

[!INCLUDE [azure-storage-limits-vm-disks](../../includes/azure-storage-limits-vm-disks.md)]

자세한 내용은 [VM 크기](sizes.md)를 참조하세요.

## <a name="managed-virtual-machine-disks"></a>관리되는 가상 머신 디스크

별표로 표시되는 크기는 현재 미리 보기로 제공됩니다. 제공되는 Azure 지역은 [FAQ](/azure/virtual-machines/faq-for-disks#new-disk-sizes-managed-and-unmanaged)를 참조하세요.

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>관리되지 않는 가상 머신 디스크
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>참고 항목

[Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)