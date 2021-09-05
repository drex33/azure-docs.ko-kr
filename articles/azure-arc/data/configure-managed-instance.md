---
title: Azure Arc 지원 SQL 관리형 인스턴스 구성
description: Azure Arc 지원 SQL 관리형 인스턴스 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e84d5be7252f81c4e80d6070ada2151fcc3960f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536520"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL 관리형 인스턴스 구성

이 문서에서는 Azure Arc 지원 SQL 관리형 인스턴스를 구성하는 방법을 설명합니다.


## <a name="configure-resources"></a>리소스 구성

### <a name="configure-using-cli"></a>CLI를 사용하여 구성

CLI를 사용하여 Azure Arc 지원 SQL Managed Instances의 구성을 편집할 수 있습니다. 구성 옵션을 보려면 다음 명령을 실행합니다. 

```azurecli
az sql mi-arc edit --help
```

다음 예에서는 CPU 코어, 메모리 요청 및 제한을 설정합니다.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

SQL Managed Instance에 대한 변경 내용을 보려면 다음 명령을 사용하여 구성 yaml 파일을 볼 수 있습니다.

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>서버 구성 옵션

만든 시간 이후에 Azure Arc 지원 SQL 관리형 인스턴스의 서버 구성 설정을 구성할 수 있습니다. 이 문서에서는 mssql 에이전트 사용 여부 설정 구성, 문제 해결 시나리오에 특정 추적 플래그를 사용하도록 설정하는 방법을 설명합니다.

이러한 설정을 변경하려면 다음 단계를 수행합니다.

1. 대상 설정을 포함하는 사용자 지정 `mssql-custom.conf` 파일을 만듭니다. 다음 예에서는 SQL 에이전트를 사용하고 추적 플래그 1204를 사용하도록 설정합니다.

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. `mssql-custom.conf` 파일을 `master-0` Pod의 `mssql-miaa` 컨테이너에 있는 `/var/opt/mssql`에 복사합니다. `<namespaceName>`을 Arc 네임스페이스 이름으로 대체합니다.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. SQL Server 인스턴스를 다시 시작합니다.  `<namespaceName>`을 Arc 네임스페이스 이름으로 대체합니다.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**알려진 제한 사항**
- 위의 단계를 수행하려면 Kubernetes cluster 관리자 권한이 필요합니다.
