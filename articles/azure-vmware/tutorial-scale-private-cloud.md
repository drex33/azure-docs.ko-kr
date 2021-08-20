---
title: 자습서 - 프라이빗 클라우드의 클러스터 확장 또는 축소
description: 이 자습서에서는 Azure Portal을 사용하여 Azure VMware Solution 프라이빗 클라우드의 크기를 조정합니다.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 79368483c7edd3ebf6811eeca1853b4bcb4ecec9
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113433997"
---
# <a name="tutorial-expand-or-shrink-clusters-in-a-private-cloud"></a>자습서: 프라이빗 클라우드의 클러스터 확장 또는 축소

Azure VMware Solution 프라이빗 클라우드 환경을 최대한 활용하려면 계획된 워크로드에 필요한 사항을 반영하도록 클러스터와 호스트 크기를 조정합니다. 애플리케이션 워크로드에 필요한 만큼 프라이빗 클라우드의 클러스터 및 호스트를 확장할 수 있습니다. 특정 서비스의 성능 및 가용성 제한은 사례별로 다루어야 합니다. 클러스터 및 호스트 제한은 [프라이빗 클라우드 개념](concepts-private-clouds-clusters.md) 문서에 제공됩니다.

이 자습서에서는 Azure Portal을 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 기존 프라이빗 클라우드에 클러스터 추가
> * 기존 클러스터에 호스트 추가

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 기존 프라이빗 클라우드가 필요합니다. 프라이빗 클라우드를 만들지 않은 경우 [프라이빗 클라우드 만들기 자습서](tutorial-create-private-cloud.md)를 사용하여 만드세요. 

## <a name="add-a-new-cluster"></a>새 클러스터 추가

1. 기존 프라이빗 클라우드의 개요 페이지의 **관리** 에서 **프라이빗 클라우드 스케일링** 을 선택합니다. 다음으로 **+ 클러스터 추가** 를 선택합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="Azure VMware Solution 프라이빗 클라우드에 클러스터를 추가하는 방법을 보여 주는 스크린샷" border="true":::

1. **클러스터 추가** 페이지에서 슬라이더를 사용하여 호스트 수를 선택합니다. **저장** 을 선택합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="새 클러스터를 구성하는 방법을 보여 주는 스크린샷" border="true":::

   새 클러스터의 배포가 시작됩니다.

## <a name="scale-a-cluster"></a>클러스터 크기 조정 

1. 기존 프라이빗 클라우드의 개요 페이지에서 **프라이빗 클라우드 스케일링** 을 선택하고 연필 아이콘을 선택하여 클러스터를 편집합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="기존 클러스터를 편집할 위치를 보여 주는 스크린샷" border="true":::

1. **클러스터 편집** 페이지에서 슬라이더를 사용하여 호스트 수를 선택합니다. **저장** 을 선택합니다.

   클러스터에 호스트 추가가 시작됩니다.

## <a name="next-steps"></a>다음 단계

다른 Azure VMware Solution 프라이빗 클라우드가 필요한 경우 동일한 네트워킹 필수 구성 요소, 클러스터 및 호스트 제한에 따라 [다른 프라이빗 클라우드를 만듭니다](tutorial-create-private-cloud.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
