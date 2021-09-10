---
title: Azure Arc에서 Azure SQL Managed Instance 만들기
description: Azure Arc에서 Azure SQL Managed Instance 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: a5aac5704463b28ea9bb124f9fb3120d7a1da52b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102078"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc에서 Azure SQL Managed Instance 만들기

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]


## <a name="create-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance 만들기

SQL Managed Instance에 대한 만들기 명령에 사용 가능한 옵션을 보려면 다음 명령을 사용합니다.
```azurecli
az sql mi-arc create --help
```

SQL Managed Instance를 만들려면 다음 명령을 사용합니다.

```azurecli
az sql mi-arc create -n <instanceName> --k8s-namespace <namespace> --use-k8s
```

예제:

```azurecli
az sql mi-arc create -n sqldemo --k8s-namespace my-namespace --use-k8s
```
> [!NOTE]
>  이름은 13자 미만이어야 하며 [DNS 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)을 준수해야 합니다.
>
>  메모리 할당 및 vCore 할당을 지정할 때 이 수식을 사용하여 성능이 허용 가능한지 확인합니다. 각 1개 vCore에 대해 SQL Managed Instance Pod가 실행되는 Kubernetes 노드에서 4GB 이상의 RAM을 사용할 수 있어야 합니다.
>
>  SQL 인스턴스 만들기를 자동화하고 관리자 암호에 대한 대화식 프롬프트를 피하려면 `az sql mi-arc create` 명령을 실행하기 전에 `AZDATA_USERNAME` 및 `AZDATA_PASSWORD` 환경 변수를 원하는 사용자 이름 및 암호로 설정하면 됩니다.
> 
>  동일한 터미널 세션에서 AZDATA_USERNAME 및 AZDATA_PASSWORD를 사용하여 데이터 컨트롤러를 만든 경우 AZDATA_USERNAME 및 AZDATA_PASSWORD 값도 SQL 관리형 인스턴스를 만드는 데 사용됩니다.

> [!NOTE]
> 간접 연결 모드를 사용하는 경우 Kubernetes에서 Azure SQL Managed Instance를 만들면 Azure에 리소스가 자동으로 등록되지 않습니다. 리소스를 등록하는 단계는 다음 문서에 나와 있습니다. 
> - [청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Azure Arc에서 인스턴스 보기

인스턴스를 보려면 다음 명령을 사용합니다.

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

여기에서 외부 IP 및 포트 번호를 복사하고 Azure Data Studio 또는 SQL Server Management Studio와 같은 SQL Server/Azure SQL 인스턴스에 연결하기 위해 선호하는 도구를 사용하여 연결할 수 있습니다.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="next-steps"></a>다음 단계
- [Azure Arc 지원 SQL Managed Instance에 연결](connect-managed-instance.md)
- [Azure에 인스턴스를 등록하고 인스턴스에 대한 메트릭 및 로그 업로드](upload-metrics-and-logs-to-azure-monitor.md)
- [Azure Data Studio를 사용하여 Azure SQL Managed Instance 배포](create-sql-managed-instance-azure-data-studio.md)
