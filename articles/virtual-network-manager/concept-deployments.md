---
title: Azure Virtual Network Manager의 구성 배포(미리 보기)
description: Azure Virtual Network Manager에서 구성 배포가 작동하는 방식에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bbf38eff6ffc5d24ae566ec94167a1c0c6ff9263
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103087"
---
# <a name="configuration-deployments-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager의 구성 배포(미리 보기)

이 문서에서는 구성이 네트워크 리소스에 적용되는 방법에 대해 알아봅니다. 또한 각 멤버 자격 유형에 대해 구성 배포 업데이트가 어떻게 다른지 살펴보겠습니다. 그런 다음 *배포 상태* 및 목표 *상태 모델에* 대한 세부 정보를 살펴보겠습니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="deployment"></a>배포

*배포는* Azure Virtual Network Manager에서 네트워크 그룹의 가상 네트워크에 구성을 적용하는 데 사용하는 방법입니다. 네트워크 그룹, 연결 및 보안 관리자 구성에 대한 변경 내용은 배포가 커밋되지 않는 한 적용되지 않습니다. 배포를 커밋할 때 구성이 적용될 지역을 선택합니다. 배포 요청이 Azure Virtual Network Manager로 전송되면 네트워크 리소스의 [목표 상태를](#goalstate) 계산하고 인프라에 필요한 변경 내용을 요청합니다. 변경 내용은 구성의 규모에 따라 약 15-20분 정도 걸릴 수 있습니다.

## <a name="deployment-against-network-group-membership-types"></a><a name="deployment"></a>네트워크 그룹 멤버 자격 유형에 대한 배포

배포 업데이트는 네트워크 그룹의 정적 및 동적 그룹 구성원에 대해 다릅니다. 동적 그룹 멤버가 있는 경우 목표 상태 모델이 사용됩니다. Azure Virtual Network Manager는 동적 멤버가 구성 요구 사항을 충족하는지 자동으로 확인하고 구성을 다시 배포할 필요 없이 조정합니다. 그러나 정적 멤버로 추가된 가상 네트워크가 있는 경우 변경 내용을 적용하려면 구성을 다시 배포해야 합니다. 예를 들어 새 가상 네트워크를 정적 멤버로 추가하는 경우 적용하려면 구성을 다시 배포해야 합니다.

## <a name="deployment-status"></a>배포 상태

구성 배포를 커밋할 때 API는 POST 작업을 수행하며 나중에 배포가 완료되지 않습니다. 배포 요청이 완료되면 Azure Virtual Network Manager는 네트워크의 목표 상태를 계산하고 기본 인프라에 변경을 요청합니다. Virtual Network Manager의 배포 페이지에서 *배포* 상태를 볼 수 있습니다.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="배포 목록에서 진행 중인 배포의 스크린샷.":::

## <a name="goal-state-model"></a><a name = "goalstate"></a> 목표 상태 모델

구성 배포를 커밋할 때 최종 결과로 원하는 구성의 목표 상태를 설명하는 것입니다. 예를 들어 *Config1* 및 *Config2라는* 구성을 지역에 커밋하면 이러한 두 구성이 적용됩니다. *Config1* 및 *Config3이라는* 구성을 동일한 지역에 커밋하기로 결정한 경우 *Config2가* 제거되고 *Config3이* 추가됩니다. 모든 구성을 제거하려면 더 이상 구성을 적용하지 않으려는 지역에 대해 **없음** 구성을 배포합니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Network Manager 인스턴스를 만드는](create-virtual-network-manager-portal.md)방법을 알아봅니다.
