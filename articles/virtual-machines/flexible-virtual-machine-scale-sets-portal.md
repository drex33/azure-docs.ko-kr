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
ms.openlocfilehash: cef23894759b0b2dca7098ef1c430548d1720406
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699309"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>미리 보기: Azure Portal에서 유연한 확장 집합에서 가상 머신 만들기

**적용 대상:** :heavy_check_mark: 유연한 확장 집합

이 문서에서는 Azure Portal을 사용하여 유연한 오케스트레이션 모드에서 가상 머신 확장 집합을 만드는 단계를 안내합니다. 유연한 확장 집합에 대한 자세한 내용은 [가상 머신 확장 집합에 대한 유연한 오케스트레이션 모드](flexible-virtual-machine-scale-sets.md)를 참조하세요. 


> [!IMPORTANT]
> Flexible 오케스트레이션 모드의 가상 머신 확장 집합은 현재 공개 미리 보기로 제공됩니다. 아래에서 자세하게 설명하는 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저를 사용해야 합니다.
> 이 미리 보기 버전은 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


> [!CAUTION]
> 오케스트레이션 모드는 확장 집합을 만들 때 정의되며 나중에 변경하거나 업데이트할 수 없습니다.


## <a name="register-for-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 등록

Flexible 오케스트레이션 모드로 가상 머신 확장 집합을 배포하려면 먼저 미리 보기 기능에 대한 구독을 등록해야 합니다. 기능 등록에는 최대 15분이 걸립니다.

확장 집합 미리 보기에 대한 유연한 오케스트레이션 모드를 실행할 때 아래 단계에 연결된 ‘미리 보기’ Azure Portal을 사용합니다. 

1. https://preview.portal.azure.com 에서 Azure Portal에 로그인합니다.
1. **구독** 으로 이동합니다.
1. 구독의 이름을 선택함으로써 유연한 오케스트레이션 모드에서 확장 집합을 만들려는 구독에 대한 세부 정보 페이지로 이동합니다.
1. **설정** 아래 메뉴에서 **미리 보기 기능** 을 선택합니다.
1. 사용하도록 설정할 4개의 오케스트레이터 기능(*VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview*, *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*)을 선택합니다.
1. **등록** 을 선택합니다.

구독에 대한 기능을 등록한 후에는 컴퓨팅 리소스 공급자에 변경 내용을 전파하여 옵트인 프로세스를 완료합니다. 

1. **설정** 아래의 메뉴에서 **리소스 공급자** 를 선택합니다.
1. `Microsoft.compute`를 선택합니다.
1. **다시 등록** 을 선택합니다.


## <a name="get-started-with-flexible-orchestration-mode"></a>Flexible 오케스트레이션 모드 시작하기

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Azure Portal을 통해 Flexible 오케스트레이션 모드의 가상 머신 확장 집합 만들기

확장 집합 미리 보기에 대한 유연한 오케스트레이션 모드를 실행할 때 아래 단계에 연결된 ‘미리 보기’ Azure Portal을 사용합니다. 

1. https://preview.portal.azure.com 에서 Azure Portal에 로그인합니다.
1. 검색 창에서 **가상 머신 확장 집합** 을 검색하고 선택합니다.
1. **가상 머신 확장 집합** 페이지에서 **만들기** 를 선택합니다.
1. **가상 머신 확장 집합 만들기** 페이지에서 **오케스트레이션** 섹션을 봅니다.
1. **오케스트레이션 모드** 의 경우 **Flexible** 옵션을 선택합니다.
1. **장애 도메인 개수** 를 설정합니다.
1. 확장 집합 만들기를 완료합니다. 확장 집합을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 확장 집합 만들기](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set)를 참조하세요.


### <a name="next-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>다음으로, Flexible 오케스트레이션 모드에서 확장 집합에 가상 머신을 추가합니다.

1. 검색 창에서 **가상 머신** 을 검색하고 선택합니다.
1. **가상 머신** 페이지에서 **추가** 를 선택합니다.
1. **기본 사항** 탭에서 **인스턴스 세부 정보** 섹션을 확인합니다.
1. **가용성 옵션** 에서 확장 집합을 선택하여 Flexible 오케스트레이션 모드에서 확장 집합에 VM을 추가합니다. 동일한 지역, 영역 및 리소스 그룹의 확장 집합에 가상 머신을 추가할 수 있습니다.
1. **네트워킹** 탭으로 이동하여 아웃바운드 연결을 명시적으로 정의합니다.

    > [!IMPORTANT]
    > 유연한 오케스트레이션이 있는 가상 머신 확장 집합에는 명시적으로 정의된 아웃바운드 연결이 필요합니다. 자세한 정보는 [명시적인 아웃바운드 네트워크 연결](flexible-virtual-machine-scale-sets.md#explicit-network-outbound-connectivity-required)을 참조하세요.

1. 가상 머신 만들기를 완료합니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure CLI를 사용하여 유연한 규모를 만드는 방법을 알아봅니다.](flexible-virtual-machine-scale-sets-cli.md)