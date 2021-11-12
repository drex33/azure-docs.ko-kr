---
title: Azure VMware Solution by CloudSimple 프라이빗 클라우드 확장
description: 기존 CloudSimple 프라이빗 클라우드를 확장하여 기존 또는 새 클러스터에 용량을 추가하는 방법을 설명함
author: suzizuber
ms.author: v-szuber
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 52d983bfdb492ff9e2a61774fe19300e0e7b4d7e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345152"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드 확장

CloudSimple은 프라이빗 클라우드를 동적으로 확장할 수 있는 유연성을 제공합니다. 더 작은 구성으로 시작한 다음 더 높은 용량이 필요한 경우 확장할 수 있습니다. 또는 현재 요구 사항에 따라 프라이빗 클라우드를 만든 다음 소비 증가에 따라 확장할 수 있습니다.

프라이빗 클라우드는 하나 이상의 vSphere 클러스터로 구성됩니다. 각 클러스터에는 3~16개의 노드가 있을 수 있습니다.  프라이빗 클라우드를 확장할 때 기존 클러스터에 노드를 추가하거나 새 클러스터를 만듭니다. 기존 클러스터를 확장하려면 추가 노드가 기존 노드와 동일한 유형(SKU)이어야 합니다. 새 클러스터를 만드는 경우 노드의 형식이 다를 수 있습니다. 프라이빗 클라우드 제한에 대한 자세한 내용은 [CloudSimple 프라이빗 클라우드 개요](cloudsimple-private-cloud.md) 문서의 제한 섹션을 참조하세요.

vCenter의 기본 **데이터 센터** 를 사용하여 프라이빗 클라우드를 만듭니다.  각 데이터 센터는 최상위 관리 엔터티로 사용됩니다.  새 클러스터의 경우 CloudSimple은 기존 데이터 센터에 추가하거나 새 데이터 센터를 만들 수 있는 옵션을 제공합니다.

새 클러스터 구성의 일부로 CloudSimple은 VMware 인프라를 구성합니다.  설정에는 vSAN 디스크 그룹에 대한 스토리지 설정, VMware 고가용성 및 DRS(Distributed Resource Scheduler)가 포함됩니다.

프라이빗 클라우드는 여러 번 확장할 수 있습니다. 확장은 전체 노드 제한 내에 유지되는 경우에만 수행할 수 있습니다. 프라이빗 클라우드를 확장할 때마다 기존 클러스터에 추가하거나 새로 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

프라이빗 클라우드를 확장하려면 먼저 노드를 프로비전해야 합니다.  노드를 프로비전하는 방법에 대한 자세한 내용은 [CloudSimple로 VMware 솔루션에 대한 노드 프로비전 - Azure](create-nodes.md) 문서를 참조하세요.  새 클러스터를 만들려면 동일한 SKU의 사용 가능한 노드가 세 개 이상 있어야 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="expand-a-private-cloud"></a>프라이빗 클라우드 확장

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.

2. **리소스** 페이지를 열고 확장할 프라이빗 클라우드를 선택합니다.

3. 요약 섹션에서 **확장** 을 클릭합니다.

    ![프라이빗 클라우드 확장](media/resources-expand-private-cloud.png)

4. 기존 클러스터를 확장할지 아니면 새 vSphere 클러스터를 만들지를 선택합니다. 변경 작업을 수행하면 페이지의 요약 정보가 업데이트됩니다.

    * 기존 클러스터를 확장하려면 **기존 클러스터 확장** 을 클릭합니다. 확장할 클러스터를 선택하고 추가할 노드 수를 입력합니다. 각 클러스터에는 최대 16개의 노드를 포함할 수 있습니다.
    * 새 클러스터를 추가하려면 **새 클러스터 만들기** 를 클릭합니다. 클러스터의 이름을 입력합니다. 기존 데이터 센터를 선택하거나 이름을 입력하여 새 데이터 센터를 만듭니다. 노드 형식을 선택합니다. 새 vSphere 클러스터를 만들 때 다른 노드 유형을 선택할 수 있지만 기존 vSphere 클러스터를 확장할 때는 선택할 수 없습니다. 노드 수를 선택합니다. 각 새 클러스터에는 세 개 이상의 노드가 있어야 합니다.

    ![프라이빗 클라우드 확장 - 노드 추가](media/resources-expand-private-cloud-add-nodes.png)

5. **제출** 을 클릭하여 프라이빗 클라우드를 확장합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [프라이빗 클라우드](cloudsimple-private-cloud.md)에 대해 자세히 알아보기