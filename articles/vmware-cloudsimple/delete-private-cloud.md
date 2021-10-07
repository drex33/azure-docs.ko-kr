---
title: Azure VMware Solution by CloudSimple 프라이빗 클라우드 삭제
description: CloudSimple 프라이빗 클라우드를 삭제하는 방법에 대해 알아봅니다. 프라이빗 클라우드를 삭제하면 모든 클러스터가 삭제됩니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b8afadea5c1f0236166f9d2cd5b7c7fa7d6c3d6e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620396"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드 삭제

CloudSimple은 프라이빗 클라우드를 삭제할 수 있는 유연성을 제공합니다.  프라이빗 클라우드는 하나 이상의 vSphere 클러스터로 구성됩니다. 각 클러스터에는 3~16개의 노드가 있을 수 있습니다. 프라이빗 클라우드를 삭제하면 모든 클러스터가 삭제됩니다.

## <a name="before-you-begin"></a>시작하기 전에

프라이빗 클라우드 삭제는 프라이빗 클라우드 전체를 삭제합니다.  프라이빗 클라우드의 모든 구성 요소가 삭제됩니다.  데이터를 유지하려면 온-프레미스 스토리지 또는 Azure Storage에 데이터를 백업했는지 확인합니다.

프라이빗 클라우드의 구성 요소는 다음과 같습니다.

* CloudSimple 노드
* 가상 머신
* VLAN/서브넷
* 프라이빗 클라우드에 저장된 모든 사용자 데이터
* VLAN/서브넷에 대한 모든 방화벽 규칙 첨부 파일

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="delete-a-private-cloud"></a>프라이빗 클라우드 삭제

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.

2. **리소스** 페이지를 엽니다.

3. 삭제할 프라이빗 클라우드를 클릭합니다.

4. 요약 페이지에서 **삭제** 를 클릭합니다.

    ![프라이빗 클라우드 삭제](media/delete-private-cloud.png)

5. 확인 페이지에서 프라이빗 클라우드의 이름을 입력하고 **삭제** 를 클릭합니다. 

    ![프라이빗 클라우드 삭제 - 확인](media/delete-private-cloud-confirm.png)

프라이빗 클라우드는 삭제를 위해 표시되었습니다.  삭제 프로세스는 3시간 후에 시작되며 프라이빗 클라우드를 삭제합니다.

> [!CAUTION]
> 프라이빗 클라우드를 삭제한 후에는 노드를 삭제해야 합니다.  노드의 측정은 노드가 구독에서 삭제될 때까지 계속됩니다.

## <a name="next-steps"></a>다음 단계

* [노드 삭제](delete-nodes.md)
