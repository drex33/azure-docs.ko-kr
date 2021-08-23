---
title: Azure Portal을 사용하여 Azure 관리 디스크의 성능 변경
description: Azure Portal을 사용하여 새 관리 디스크 및 기존 관리 디스크의 성능 계층을 변경하는 방법에 대해 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/13/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a82865930a5ed770b742c3d5e8906d4e69be3f64
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110079411"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Azure Portal을 사용하여 성능 계층 변경

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>시작

### <a name="new-disks"></a>새 디스크

다음 단계는 디스크를 처음 만들 때 디스크의 성능 계층을 변경하는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 새 디스크를 만들려는 VM으로 이동합니다.
1. 새 디스크를 선택하는 경우 먼저 필요한 디스크 크기를 선택합니다.
1. 크기를 선택한 후에는 다른 성능 계층을 선택하여 성능을 변경합니다.
1. **확인** 을 선택하여 디스크를 만듭니다.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="디스크 생성 블레이드의 스크린샷, 디스크가 강조 표시되고 성능 계층 드롭다운이 강조 표시됩니다." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>기존 디스크

다음 단계는 기존 디스크의 성능 계층을 변경하는 방법을 간략하게 설명합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 변경하려는 디스크가 포함된 VM으로 이동합니다.
1. VM 할당을 무효화하거나 디스크를 분리합니다.
1. 디스크 선택
1. **크기 + 성능** 을 선택합니다.
1. **성능 계층** 드롭다운에서 디스크의 현재 성능 계층이 아닌 다른 계층을 선택합니다.
1. **크기 조정** 을 선택합니다.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="크기 + 성능 블레이드의 스크린샷, 성능 계층이 강조 표시됩니다." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

### <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>가동 중지 시간 없이 디스크의 성능 계층 변경(미리 보기)

가동 중지 시간 없이 성능 계층을 변경할 수도 있으므로 VM 할당을 중단하거나 디스크를 분리하여 계층을 변경할 필요가 없습니다.

### <a name="prerequisites"></a>필수 구성 요소

디스크는 [가동 중지 시간 없이 성능 등급 변경(미리 보기)](#change-performance-tier-without-downtime-preview) 섹션에 명시된 요구사항을 충족해야 합니다. 그렇지 않은 경우 성능 등급을 변경하면 가동 중지 시간이 발생합니다.

가동 중지 시간 없이 디스크의 성능 계층을 변경하려면 먼저 구독에 대한 기능을 사용하도록 설정해야 합니다. 다음 단계를 수행하여 구독에 대한 기능을 사용하도록 설정하세요.

1.  다음 명령을 실행하여 구독에 대한 기능 등록

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  기능을 사용해보기 전에 다음 명령을 사용하여 등록 상태가 **등록됨**(몇 분 정도 소요될 수 있음)인지 확인합니다.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>성능 계층 변경

기능이 등록되었으므로 가동 중지 시간 없이 해당 디스크의 성능 계층을 변경할 수 있습니다.

1. 다음 링크에서 Azure Portal에 로그인합니다. [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
1. 변경하려는 디스크가 포함된 VM으로 이동합니다.
1. VM 할당을 무효화하거나 디스크를 분리합니다.
1. 디스크 선택
1. **크기 + 성능** 을 선택합니다.
1. **성능 계층** 드롭다운에서 디스크의 현재 성능 계층이 아닌 다른 계층을 선택합니다.
1. **크기 조정** 을 선택합니다.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="크기 + 성능 블레이드의 스크린샷, 성능 계층이 강조 표시됩니다." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>다음 단계

더 높은 성능 계층을 활용하기 위해 디스크 크기를 조정해야 하는 경우 다음 문서를 참조하세요.

- [Azure CLI를 사용하여 Linux VM에서 가상 하드 디스크 확장](linux/expand-disks.md)
- [Windows 가상 머신에 연결된 관리 디스크 확장](windows/expand-os-disk.md)
