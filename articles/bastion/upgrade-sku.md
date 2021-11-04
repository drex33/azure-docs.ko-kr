---
title: SKU 업그레이드
titleSuffix: Azure Bastion
description: 기본 SKU에서 표준 SKU로 계층을 변경하는 방법을 알아보세요.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 08/30/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: f88ab798618608b8dbb0e27fb772a82a5199a889
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080862"
---
# <a name="upgrade-a-sku"></a>SKU 업그레이드

이 문서는 기본 계층(SKU)에서 표준으로 업그레이드하는 데 도움이 됩니다. 업그레이드한 후에는 Bastion을 삭제하고 다시 구성하지 않고는 기본 SKU로 되돌릴 수 없습니다. 현재이 설정은 Azure Portal 에서만 구성할 수 있습니다. 호스트 확장에 대한 자세한 내용은 [구성 설정 - SKU](configuration-settings.md#skus)를 참조하세요. 

## <a name="configuration-steps"></a>구성 단계

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.
1. Azure Portal에서 배스천 호스트로 이동합니다.
1. **구성** 페이지의 **계층** 에 대해 드롭다운에서 **표준** 을 선택합니다.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="표준이 선택된 계층 선택 드롭다운의 스크린샷" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. 변경 사항을 적용하려면 **적용** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 추가 구성 정보는 [구성 설정](configuration-settings.md)을 참조하세요.
* [Bastion FAQ](bastion-faq.md)를 참조하세요.
