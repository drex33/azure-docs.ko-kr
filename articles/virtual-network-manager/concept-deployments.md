---
title: Azure Virtual Network Manager의 구성 배포 (미리 보기)
description: Azure Virtual Network Manager에서 구성 배포가 작동 하는 방식에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: f7e192133bac66d9ec593350a43d0d005bb05933
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131866680"
---
# <a name="configuration-deployments-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager의 구성 배포 (미리 보기)

이 문서에서는 구성이 네트워크 리소스에 적용 되는 방법에 대해 알아봅니다. 또한 각 멤버 자격 유형에 따라 구성 배포를 업데이트 하는 방법도 살펴봅니다. 그런 다음 *배포 상태* 및 *목표 상태 모델* 에 대 한 세부 정보로 이동 합니다.

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="deployment"></a>배포

*배포* 는 Azure Virtual Network Manager에서 네트워크 그룹의 가상 네트워크에 구성을 적용 하는 데 사용 하는 방법입니다. 배포가 커밋되지 않은 경우 네트워크 그룹, 연결 및 보안 관리자 구성에 대 한 변경 내용이 적용 되지 않습니다. 배포를 커밋하는 경우 구성이 적용 되는 지역을 선택 합니다. Azure Virtual Network Manager로 배포 요청을 보내면 네트워크 리소스의 [목표 상태](#goalstate) 를 계산 하 고 인프라에 필요한 변경 내용을 요청 합니다. 변경 내용은 구성의 크기에 따라 약 15-20 분 정도 걸릴 수 있습니다.

## <a name="deployment-against-network-group-membership-types"></a><a name="deployment"></a>네트워크 그룹 구성원 자격 유형에 대 한 배포

네트워크 그룹의 정의를 변경 해도이 네트워크 그룹을 사용 하는 구성이 배포 되지 않은 경우에는 영향을 주지 않습니다. 이와 같이 배포 업데이트는 네트워크 그룹의 정적 및 동적 그룹 구성원에 대해 다릅니다. 이름이 "production" 인 모든 가상 네트워크와 같이 동적 그룹 멤버 자격이 정의 된 경우 Azure Virtual Network Manager는 동적 멤버가 구성 요구 사항을 충족 하는지 자동으로 확인 하 고 구성을 다시 배포 하지 않고도 조정 합니다. 이는 이미 멤버 자격의 조건을 정의 했으며 정의가 변경 되지 않았기 때문입니다. 그러나 정적 구성원으로 추가 된 가상 네트워크가 있는 경우 변경 내용을 적용 하려면 구성을 다시 배포 해야 합니다. 예를 들어 새 가상 네트워크를 정적 멤버로 추가 하는 경우 구성을 다시 배포 하 여 적용 해야 합니다.

## <a name="deployment-status"></a>배포 상태

구성 배포를 커밋하면 API는 POST 작업을 수행 하 고 나중에 배포 완료를 표시 하지 않습니다. 배포 요청이 수행 되 면 Azure Virtual Network Manager는 네트워크의 목표 상태를 계산 하 고 기본 인프라를 요청 하 여 변경 합니다. Virtual Network Manager의 *배포* 페이지에서 배포 상태를 확인할 수 있습니다.

:::image type="content" source="./media/tutorial-create-secured-hub-and-spoke/deployment-in-progress.png" alt-text="배포 목록의 배포 진행 중 스크린샷.":::

## <a name="goal-state-model"></a><a name = "goalstate"></a> 목표 상태 모델

구성 배포를 커밋하면 최종 결과로 원하는 구성의 목표 상태를 설명 하는 것입니다. 예를 들어 *Config1* 및 *Config2* 라는 구성을 지역에 커밋하는 경우 이러한 두 가지 구성이 적용 됩니다. *Config1* 및 *Config3* 라는 구성을 동일한 지역에 커밋하려면 *Config2* 를 제거 하 고 *Config3* 를 추가 합니다. 모든 구성을 제거 하려면 더 이상 구성을 적용 하지 않으려는 지역에 대해 **없음** 구성을 배포 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Virtual Network Manager 인스턴스를 만드는](create-virtual-network-manager-portal.md)방법에 대해 알아봅니다.
