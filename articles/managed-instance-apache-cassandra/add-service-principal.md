---
title: Apache Cassandra 용 Azure Managed Instance Cosmos DB 서비스 주체를 추가 하는 방법
description: Apache Cassandra에 대 한 Azure Managed Instance의 기존 가상 네트워크에 Cosmos DB 서비스 주체를 추가 하는 방법을 알아봅니다.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06fd7f23db4b4df9b1153ac8b00afb35c265d0be
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131859292"
---
# <a name="use-azure-portal-to-add-cosmos-db-service-principal"></a>Azure Portal를 사용 하 여 Cosmos DB 서비스 사용자 추가

기존 가상 네트워크에 성공적으로 배포 하려면 Apache Cassandra에 대 한 Azure Managed Instance에는 작업을 허용 하는 역할 (예: 네트워크 기여자)의 Azure Cosmos DB 서비스 주체가 필요 합니다 `Microsoft.Network/virtualNetworks/subnets/join/action` . 경우에 따라 이러한 권한을 수동으로 추가 해야 할 수도 있습니다. 이 문서에서는 Azure Portal를 사용 하 여이 작업을 수행 하는 방법을 보여 줍니다. 

## <a name="add-cosmos-db-service-principal"></a>Cosmos DB 서비스 주체 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 구독에서 대상 가상 네트워크로 이동 하 고, 액세스 제어 탭을 선택 하 고, 다음을 클릭 합니다 `add role assignment` .

   :::image type="content" source="./media/add-service-principal/service-principal-1.png" alt-text="역할 할당 추가" lightbox="./media/add-service-principal/service-principal-1.png" border="true"::: 

1. 역할을 검색 `Network Contributor` 하 고 강조 표시 한 다음 탭을 선택 합니다 `members` .

   :::image type="content" source="./media/add-service-principal/service-principal-2.png" alt-text="네트워크 참가자 추가" lightbox="./media/add-service-principal/service-principal-2.png" border="true"::: 

   > [!NOTE]
   > 네트워크 기여자와 같은 광범위 한 권한이 있는 역할은 필요 하지 않습니다 .이는 간단한 예제로 사용 됩니다. 작업을 허용 하는 한, 보다 좁은 권한으로 고객 역할을 만들 수도 있습니다. `Microsoft.Network/virtualNetworks/subnets/join/action`

1. `User, group, or service principal`에 대해가 선택 되어 있는지 확인 한 `Assign access to` 다음를 클릭 하 여 `Select members` 서비스 주체를 검색 `Azure Cosmos DB` 합니다. 오른쪽 창에서 선택 합니다.

   :::image type="content" source="./media/add-service-principal/service-principal-3.png" alt-text="Cosmos DB 서비스 주체 선택" lightbox="./media/add-service-principal/service-principal-3.png" border="true"::: 

1. 위쪽의 탭을 클릭 `Review + assign` 한 다음 아래쪽의 단추를 `Review + assign` 클릭 합니다. 이제 Cosmos DB 서비스 주체가 할당 되어야 합니다. 

   :::image type="content" source="./media/add-service-principal/service-principal-4.png" alt-text="검토 및 할당" lightbox="./media/add-service-principal/service-principal-4.png" border="true"::: 

## <a name="next-steps"></a>다음 단계

이 문서에서는 관리 되는 Cassandra 배포를 허용 하기 위해 적절 한 역할을 사용 하 여 Azure Cosmos DB 서비스 주체를 가상 네트워크에 할당 하는 방법을 배웠습니다. 다음 문서에서 Apache Cassandra용 Azure Managed Instance에 대해 자세히 알아보세요.

* [Apache Cassandra용 Azure Managed Instance 개요](introduction.md)
* [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
