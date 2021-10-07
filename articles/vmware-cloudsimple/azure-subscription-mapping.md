---
title: Azure 구독 매핑을 사용하여 리소스 풀 만들기
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure 구독 매핑을 통해 프라이빗 클라우드의 리소스 풀을 만드는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7fa189e16f71c2b0f04c27911fbcb6e811de45f6
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618065"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Azure 구독 매핑을 사용하여 프라이빗 클라우드의 리소스 풀 만들기
Azure 구독 매핑을 사용하면 사용 가능한 vSphere 리소스 풀에서 프라이빗 클라우드의 리소스 풀을 만들 수 있습니다. CloudSimple 포털에서 프라이빗 클라우드에 대한 Azure 구독을 보고 관리할 수 있습니다.

> [!NOTE]
> 리소스 풀을 매핑하면 모든 자식 리소스 풀도 매핑됩니다. 자식 리소스 풀이 이미 매핑된 경우 부모 리소스 풀을 매핑할 수 없습니다.

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.
2. **리소스** 페이지를 열고 **Azure 구독 매핑** 을 선택합니다.  
3. **Azure 구독 매핑 편집** 을 클릭합니다.  
4. 사용 가능한 리소스 풀을 매핑하려면 왼쪽에서 해당 리소스 풀을 선택하고 오른쪽 화살표를 클릭합니다. 
5. 매핑을 제거하려면 오른쪽에서 해당 매핑을 선택하고 왼쪽 화살표를 클릭합니다. 

    ![Azure 구독](media/resources-azure-mapping.png)

6. **확인** 을 클릭합니다.
