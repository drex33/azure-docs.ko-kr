---
title: Azure Portal을 사용하여 유연한 확장 집합에서 가상 머신 만들기
description: Azure Portal에서 유연한 오케스트레이션 모드로 가상 머신 확장 집합 만드는 방법을 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 9804b8cd773eec6df8a8b7a5b06e61ee110987b3
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163561"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Azure Portal을 사용하여 유연한 확장 집합에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: 유연한 확장 집합

이 문서에서는 Azure Portal을 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 단계를 안내합니다. 유연한 확장 집합에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 유연한 오케스트레이션 모드](flexible-virtual-machine-scale-sets.md)를 참조하세요. 


> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="get-started-with-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 시작하기

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Azure Portal을 통해 Flexible 오케스트레이션 모드의 가상 머신 확장 집합 만들기

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
1. 검색 창에서 **가상 머신 확장 집합** 을 검색하고 선택합니다.
1. **가상 머신 확장 집합** 페이지에서 **만들기** 를 선택합니다.
1. **가상 머신 확장 집합 만들기** 페이지에서 **오케스트레이션** 섹션을 봅니다.
1. **오케스트레이션 모드** 의 경우 **Flexible** 옵션을 선택합니다.
1. **장애 도메인 개수** 를 설정합니다.
1. 확장 집합 만들기를 완료합니다. 확장 집합을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 확장 집합 만들기](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set)를 참조하세요.


### <a name="optional-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>필드 유연한 오케스트레이션 모드에서 확장 집합에 가상 머신을 추가 합니다.

1. 검색 창에서 **가상 머신** 을 검색하고 선택합니다.
1. **가상 머신** 페이지에서 **추가** 를 선택합니다.
1. **기본 사항** 탭에서 **인스턴스 세부 정보** 섹션을 확인합니다.
1. **가용성 옵션** 에서 확장 집합을 선택하여 Flexible 오케스트레이션 모드에서 확장 집합에 VM을 추가합니다. 동일한 지역, 영역 및 리소스 그룹의 확장 집합에 가상 머신을 추가할 수 있습니다.
1. **네트워킹** 탭으로 이동하여 아웃바운드 연결을 명시적으로 정의합니다.

    > [!IMPORTANT]
    > 유연한 오케스트레이션이 있는 가상 머신 확장 집합에는 명시적으로 정의된 아웃바운드 연결이 필요합니다. 자세한 정보는 [명시적인 아웃바운드 네트워크 연결](flexible-virtual-machine-scale-sets-migration-resources.md#explicit-network-outbound-connectivity-required)을 참조하세요.

1. 가상 머신 만들기를 완료합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 유연한 규모를 만드는 방법을 알아봅니다.](flexible-virtual-machine-scale-sets-cli.md)