---
title: Azure Arc 지원 SQL 관리형 인스턴스 구성
description: Azure Arc 지원 SQL 관리형 인스턴스 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 09/1/2021
ms.topic: how-to
ms.openlocfilehash: b95688eca33400956997b44bda43565454f82479
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481221"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL 관리형 인스턴스 구성

이 문서에서는 Azure Arc 지원 SQL 관리형 인스턴스를 구성하는 방법을 설명합니다.


## <a name="configure-resources-such-as-cores-memory"></a>코어, 메모리와 같은 리소스 구성


### <a name="configure-using-cli"></a>CLI를 사용하여 구성

CLI를 사용하여 Azure Arc 지원 SQL Managed Instances의 구성을 편집할 수 있습니다. 구성 옵션을 보려면 다음 명령을 실행합니다. 

```azurecli
az sql mi-arc edit --help
```

다음 명령을 사용하여 Azure Arc 사용하도록 설정된 SQL 관리형 인스턴스에 사용 가능한 메모리 및 코어를 업데이트할 수 있습니다.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

다음 예에서는 CPU 코어, 메모리 요청 및 제한을 설정합니다.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

관리되는 인스턴스에 SQL Azure Arc 사용하도록 설정된 변경 내용을 보려면 다음 명령을 사용하여 구성 yaml 파일을 볼 수 있습니다.

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>서버 구성 옵션

만든 시간 이후에 Azure Arc 지원 SQL 관리형 인스턴스의 서버 구성 설정을 구성할 수 있습니다. 이 문서에서는 mssql 에이전트 사용 여부 설정 구성, 문제 해결 시나리오에 특정 추적 플래그를 사용하도록 설정하는 방법을 설명합니다.


### <a name="enable-sql-server-agent"></a>SQL Server 에이전트 사용

SQL Server 에이전트는 기본적으로 사용하지 않도록 설정되어 있습니다. 다음 명령을 실행하여 사용하도록 설정할 수 있습니다.

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
예를 들어
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>추적 플래그 사용

추적 플래그는 다음과 같이 사용하도록 설정할 수 있습니다.
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```

