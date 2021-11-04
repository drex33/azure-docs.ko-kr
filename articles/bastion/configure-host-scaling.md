---
title: 호스트 크기 조정을 위한 배율 단위 추가
titleSuffix: Azure Bastion
description: Azure Bastion에 추가 인스턴스(배율 단위)를 추가하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: c5ab750319ffdad58269c909bd14d660d8bdc252
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087177"
---
# <a name="configure-host-scaling"></a>호스트 크기 조정 구성

이 문서는 추가 동시 클라이언트 연결을 수용하기 위해 Azure Bastion에 추가 배율 단위(인스턴스)를 추가하는 데 도움이 됩니다. 미리 보기 중에 이 설정은 Azure Portal에서만 구성할 수 있습니다. 호스트 스케일링에 대한 자세한 내용은 [구성 설정](configuration-settings.md#instance)을 참조하세요.

## <a name="configuration-steps"></a>구성 단계

1. [Azure Portal](https://ms.portal.azure.com)에 로그인합니다.
1. Azure Portal에서 Bastion 호스트로 이동합니다.
1. 호스트 크기 조정 인스턴스 수에는 표준 계층이 필요합니다. **구성** 페이지에서 **계층** 에 대해 계층이 **표준** 인지 확인합니다. 계층이 기본인 경우 드롭다운에서 **표준** 을 선택합니다. 

   :::image type="content" source="./media/configure-host-scaling/select-sku.png" alt-text="계층 선택 스크린샷." lightbox="./media/configure-host-scaling/select-sku.png":::
1. 크기 조정을 구성하려면 인스턴스 수를 조정합니다. 각 인스턴스는 배율 단위입니다.

   :::image type="content" source="./media/configure-host-scaling/instance-count.png" alt-text="인스턴스 수 슬라이더의 스크린샷." lightbox="./media/configure-host-scaling/instance-count.png":::
1. 변경 사항을 적용하려면 **적용** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 추가 정보는 [Bastion FAQ](bastion-faq.md)를 참조하세요.
