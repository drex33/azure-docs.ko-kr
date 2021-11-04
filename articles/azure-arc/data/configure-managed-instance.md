---
title: Azure Arc 지원 SQL 관리형 인스턴스 구성
description: Azure Arc 지원 SQL 관리형 인스턴스 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: d4dc8843804d48fb98fef7cd336e6b56f54f49ae
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554358"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL 관리형 인스턴스 구성

이 문서에서는 Azure Arc 지원 SQL 관리형 인스턴스를 구성하는 방법을 설명합니다.


## <a name="configure-resources-such-as-cores-memory"></a>코어, 메모리 등의 리소스 구성


### <a name="configure-using-cli"></a>CLI를 사용하여 구성

CLI를 사용하여 Azure Arc 지원 SQL Managed Instances의 구성을 편집할 수 있습니다. 구성 옵션을 보려면 다음 명령을 실행합니다. 

```azurecli
az sql mi-arc edit --help
```

다음 명령을 사용 하 여 Azure Arc 사용 SQL 관리 되는 인스턴스에 대해 사용 가능한 메모리와 코어를 업데이트할 수 있습니다.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

다음 예에서는 CPU 코어, 메모리 요청 및 제한을 설정합니다.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

Azure Arc 사용 SQL 관리 되는 인스턴스에 대 한 변경 내용을 보려면 다음 명령을 사용 하 여 구성 yaml 파일을 볼 수 있습니다.

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>서버 구성 옵션

만든 시간 이후에 Azure Arc 지원 SQL 관리형 인스턴스의 서버 구성 설정을 구성할 수 있습니다. 이 문서에서는 mssql 에이전트 사용 여부 설정 구성, 문제 해결 시나리오에 특정 추적 플래그를 사용하도록 설정하는 방법을 설명합니다.


### <a name="enable-sql-server-agent"></a>SQL Server 에이전트 사용

SQL Server 에이전트는 기본적으로 사용 하지 않도록 설정 되어 있습니다. 다음 명령을 실행 하 여 사용 하도록 설정할 수 있습니다.

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
예를 들어
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>추적 플래그 사용

추적 플래그는 다음과 같이 사용 하도록 설정할 수 있습니다.
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```

