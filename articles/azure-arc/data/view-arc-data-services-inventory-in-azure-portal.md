---
title: Azure Portal에서 인스턴스의 인벤토리 보기
description: Azure Portal에서 인스턴스의 인벤토리 보기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5a5f13c74799e172d7ce9aefb170626cf61eb98e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558785"
---
# <a name="inventory-of-arc-enabled-data-services"></a>Arc 사용 데이터 서비스의 인벤토리


## <a name="view-resources-in-azure-portal"></a>Azure Portal에서 리소스 보기

[메트릭, 로그](upload-metrics-and-logs-to-azure-monitor.md)또는 [사용량](view-billing-data-in-azure.md)을 업로드 한 후 Azure Portal에서 azure arc 사용 SQL 관리 되는 인스턴스 또는 azure arc를 사용 하는 postgres hyperscale server 그룹을 볼 수 있습니다. [포털](https://portal.azure.com) 에서 리소스를 보려면 다음 단계를 수행 합니다.

1. **모든 서비스** 로 이동합니다.
1. 데이터베이스 인스턴스 유형을 검색합니다.
1. 즐겨찾기에 형식을 추가합니다.
1. 왼쪽 메뉴에서 인스턴스 유형을 선택합니다.
1. 다른 Azure SQL 또는 Azure PostgreSQL Hyperscale 리소스와 동일한 보기에서 인스턴스를 봅니다(세분화된 보기를 위해 필터 사용).

## <a name="view-resources-in-your-kubernetes-cluster"></a>Kubernetes 클러스터에서 리소스 보기

azure arc 데이터 컨트롤러가 **간접** 연결 모드로 배포 되는 경우 아래 명령을 실행 하 여 관리 되는 인스턴스 SQL 모든 Azure arc 목록을 가져올 수 있습니다.
```
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
#Example
az sql mi-arc list --k8s-namespace arc --use-k8s
```

azure arc data controller가 **직접** 연결 모드로 배포 되는 경우 아래 명령을 실행 하 여 관리 되는 인스턴스 SQL 모든 Azure arc의 목록을 가져올 수 있습니다.
```
az sql mi-arc list --resource-group <resourcegroup>
#Example
az sql mi-arc list --resource-group myResourceGroup
```
