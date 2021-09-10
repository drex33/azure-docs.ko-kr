---
title: SQL Managed Instance 삭제 후 서브넷 삭제
description: Azure SQL Managed Instance 삭제한 후 Azure 가상 네트워크를 삭제하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: d61a3f5a3dac8cfb6215bdfd6ff1c457c4e14150
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214430"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance 삭제 후 서브넷 삭제
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 서브넷에 있는 마지막 Azure SQL Managed Instance를 삭제한 후 서브넷을 수동으로 삭제하는 방법에 대한 지침을 제공합니다.

SQL Managed Instance는 [가상 클러스터](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)에 배포됩니다. 각 가상 클러스터는 서브넷과 연결되고 첫 번째 인스턴스 생성과 함께 배포됩니다. 동일한 방식으로, 가상 클러스터는 마지막 인스턴스 삭제와 함께 자동으로 제거되며, 서브넷은 비어있고 제거 준비를 마칩니다. 가상 클러스터에서 서브넷을 해제하기 위해 수동 작업을 수행할 필요는 없습니다. 마지막 가상 클러스터가 삭제되면 서브넷으로 이동하여 삭제할 수 있습니다.

만들기 작업이 실패하고 빈 가상 클러스터를 배포하게 되는 드문 경우가 있습니다. 또한 인스턴스 만들기는 [취소할 수](management-operations-cancel.md) 있으므로 가상 클러스터는 내부에 있는 인스턴스를 사용하여 실패 상태로 배포할 수 있습니다. 가상 클러스터 제거는 이러한 상황에서 자동으로 시작되며 백그라운드에서 제거됩니다.

> [!NOTE]
> 빈 가상 클러스터나 생성에 실패한 인스턴스를 유지하는 것은 요금이 부과되지 않습니다.

> [!IMPORTANT]
> - 삭제에 성공하려면 가상 클러스터가 SQL Managed Instance를 포함하지 않아야 합니다. 만들기에 실패한 인스턴스는 포함하지 않습니다. 
> - 가상 클러스터 삭제는 약 1.5시간 동안 지속되는 장기 실행 작업입니다(최신 가상 클러스터 삭제 시간은 [SQL Managed Instance 관리 작업](management-operations-overview.md) 참조). 가상 클러스터는 이 프로세스가 완료될 때까지 포털에 계속 표시됩니다.
> - 가상 클러스터에서는 하나의 삭제 작업만 실행할 수 있습니다. 삭제 작업이 이미 진행 중이므로 고객이 시작한 모든 후속 삭제 요청에는 오류가 발생합니다.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Azure Portal에서 가상 클러스터 삭제

> [!IMPORTANT]
> 2021년 9월 1일부터 시작합니다. 클러스터의 마지막 인스턴스가 삭제되면 모든 가상 클러스터가 자동으로 제거됩니다. 가상 클러스터를 더 이상 수동으로 제거할 필요가 없습니다.

Azure Portal 사용하여 가상 클러스터를 삭제하려면 가상 클러스터 리소스를 검색합니다.

> [!div class="mx-imgBorder"]
> ![검색 박스가 강조 표시된 Azure Portal의 스크린샷](./media/virtual-cluster-delete/virtual-clusters-search.png)

삭제하려는 가상 클러스터를 찾은 후 이 리소스를 선택하고 **삭제** 를 선택합니다. 가상 클러스터 삭제를 확인하라는 메시지가 표시됩니다.

> [!div class="mx-imgBorder"]
> ![삭제 옵션이 강조 표시된 Azure Portal 가상 클러스터 대시보드의 스크린샷](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Azure Portal 알림에는 가상 클러스터 삭제 요청이 성공적으로 제출되었다는 확인 메시지가 표시됩니다. 삭제 작업 자체는 약 1.5시간 동안 지속되며, 이 기간 동안 가상 클러스터는 포털에 계속 표시됩니다. 프로세스가 완료되면 가상 클러스터가 더 이상 표시되지 않으며, 가상 클러스터와 연결된 서브넷이 재사용을 위해 릴리스됩니다.

> [!TIP]
> 가상 클러스터에 표시된 SQL 관리 인스턴스가 없고 가상 클러스터를 삭제할 수 없는 경,우 진행 중인 인스턴스 배포가 진행되고 있는지 확인하십시오. 아직 진행 중인 시작 및 취소된 배포도 포함됩니다. 이는 이러한 작업이 가상 클러스터를 계속 사용하여 삭제되지 않는 것이기 때문입니다. 인스턴스가 배포된 리소스 그룹의 **배포** 탭을 검토하여 진행 중인 모든 배포를 리뷰합니다. 이 경우 배포가 완료될 때까지 기다렸다가 SQL Managed Instance를 삭제합니다. 가상 클러스터는 인스턴스 제거의 일부로서 동기적으로 삭제됩니다.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>API를 사용하여 가상 클러스터 삭제

API를 통해 가상 클러스터를 삭제하려면 [가상 클러스터 삭제 방법](/rest/api/sql/virtualclusters/delete)에 지정된 URI 매개 변수를 사용합니다.

## <a name="next-steps"></a>다음 단계

- 개요를 보려면 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- [SQL Managed Instance의 연결 아키텍처](connectivity-architecture-overview.md)에 대해 알아봅니다.
- [SQL Managed Instance의 기존 가상 네트워크 수정](vnet-existing-add-subnet.md)에 대해 알아봅니다.
- 가상 네트워크를 생성하고, Azure SQL Managed Instance를 생성하고, 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여주는 자습서는 [Azure SQL Managed Instance 생성하기(포털)](instance-create-quickstart.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](custom-dns-configure.md)을 참조하세요.
