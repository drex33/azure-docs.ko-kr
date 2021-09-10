---
title: SKU 업그레이드
titleSuffix: Azure Bastion
description: 기본 SKU에서 표준 SKU로 계층을 변경하는 방법을 알아보세요.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 8a2c35f2ef4dafe9024deab1c26116c0ca20e01e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733307"
---
# <a name="upgrade-a-sku-preview"></a>SKU 업그레이드(미리 보기)

이 문서는 기본 계층(SKU)에서 표준으로 업그레이드하는 데 도움이 됩니다. 업그레이드한 후에는 Bastion을 삭제하고 다시 구성하지 않고는 기본 SKU로 되돌릴 수 없습니다. 미리 보기 중에 이 설정은 Azure Portal에서만 구성할 수 있습니다. 호스트 확장에 대한 자세한 내용은 [구성 설정 - SKU](configuration-settings.md#skus)를 참조하세요. 

## <a name="configuration-steps"></a>구성 단계

[!INCLUDE [Azure Bastion preview portal](../../includes/bastion-preview-portal-note.md)]

1. Azure Portal에서 배스천 호스트로 이동합니다.
1. **구성** 페이지의 **계층** 에 대해 드롭다운에서 **표준** 을 선택합니다.

   :::image type="content" source="./media/upgrade-sku/select-sku.png" alt-text="표준이 선택된 계층 선택 드롭다운의 스크린샷" lightbox="./media/upgrade-sku/select-sku-expand.png":::

1. 변경 사항을 적용하려면 **적용** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 추가 구성 정보는 [구성 설정](configuration-settings.md)을 참조하세요.
* [Bastion FAQ](bastion-faq.md)를 참조하세요.
