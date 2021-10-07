---
title: Azure VMware Solution by CloudSimple 프라이빗 클라우드 축소
description: 기존 vSphere 클러스터에서 노드를 제거하거나 전체 클러스터를 제거하여 CloudSimple에서 프라이빗 클라우드를 동적으로 축소하는 방법에 대해 알아봅니다.
author: suzizuber
ms.author: v-szuber
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d6608ccc3cbeecc691663a7d10df6535e44c19ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616002"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드 축소

CloudSimple은 프라이빗 클라우드를 동적으로 축소할 수 있는 유연성을 제공합니다.  프라이빗 클라우드는 하나 이상의 vSphere 클러스터로 구성됩니다. 각 클러스터에는 3~16개의 노드가 있을 수 있습니다. 프라이빗 클라우드를 축소하는 경우 기존 클러스터에서 노드를 제거하거나 전체 클러스터를 삭제합니다. 

## <a name="before-you-begin"></a>시작하기 전에

프라이빗 클라우드를 축소하기 위해서는 다음 조건이 충족되어야 합니다.  프라이빗 클라우드를 만들 때 생성된 관리 클러스터(첫 번째 클러스터)는 삭제할 수 없습니다.

* vSphere 클러스터에는 3개의 노드가 있어야 합니다.  노드가 세 개만 있는 클러스터는 축소할 수 없습니다.
* 사용된 총 스토리지는 클러스터 축소 후 총 용량을 초과하지 않아야 합니다.
* DRS(Distributed Resource Scheduler) 규칙이 가상 머신의 vMotion을 차단하는지 확인합니다.  규칙이 있는 경우 규칙을 비활성하거나 삭제합니다.  DRS 규칙에는 선호도 규칙을 호스트하는 가상 머신이 포함됩니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="shrink-a-private-cloud"></a>프라이빗 클라우드 축소

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.

2. **리소스** 페이지를 엽니다.

3. 축소할 프라이빗 클라우드를 클릭합니다.

4. 요약 페이지에서 **축소** 를 클릭합니다.

    ![프라이빗 클라우드 축소](media/shrink-private-cloud.png)

5. 축소하거나 삭제할 클러스터를 선택합니다. 

    ![프라이빗 클라우드 축소 - 클러스터 선택](media/shrink-private-cloud-select-cluster.png)

6. **단일 노드 제거** 또는 **전체 클러스터 삭제** 를 선택합니다. 

7. 클러스터 용량 확인

8. **제출** 을 클릭하여 프라이빗 클라우드를 축소합니다.

프라이빗 클라우드 축소가 시작됩니다.  작업의 진행률을 모니터링할 수 있습니다.  축소 프로세스는 vSAN에서 다시 동기화해야 하는 데이터에 따라 몇 시간이 걸릴 수 있습니다.

> [!NOTE]
> 1. 데이터 센터에서 마지막 또는 유일한 클러스터를 삭제하여 프라이빗 클라우드를 축소하는 경우에는 데이터 센터가 삭제되지 않습니다.
> 2. DRS 규칙 위반이 발생하면 노드가 클러스터에서 제거되지 않으며 작업 설명에 노드 제거는 클러스터의 DRS 규칙을 위반하는 것으로 표시됩니다.    


## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [프라이빗 클라우드](cloudsimple-private-cloud.md)에 대해 자세히 알아보기
