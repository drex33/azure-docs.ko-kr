---
title: Azure Arc에서 PostgreSQL 하이퍼스케일 서버 그룹의 Postgres 엔진 서버 매개 변수 구성
titleSuffix: Azure Arc-enabled data services
description: Azure Arc에서 PostgreSQL 하이퍼스케일 서버 그룹의 Postgres 엔진 서버 매개 변수 구성
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e634bcc7d07cfba4016c8f2db323e78e9beda92a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536160"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에 대한 데이터베이스 엔진 설정 지정

이 문서에서는 PostgreSQL 하이퍼스케일 서버 그룹의 데이터베이스 엔진 설정을 사용자 지정 값(기본값 아님)으로 설정하는 단계에 대해 설명합니다. 설정할 수 있는 데이터베이스 엔진 매개 변수와 해당 기본값에 대한 자세한 내용은 [여기](https://www.postgresql.org/docs/current/runtime-config.html) PostgreSQL 설명서를 참조하세요.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> 미리 보기는 다음 매개 변수 설정을 지원하지 않습니다. 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>구문

데이터베이스 엔진 설정을 구성하는 명령의 일반적인 형식은 다음과 같습니다.

```azurecli
az postgres arc-server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-settings , --re}] {'<parameter name>=<parameter value>, ...'} --k8s-namespace <namespace> --use-k8s
```

## <a name="show-current-custom-values"></a>현재 사용자 지정 값 표시

### <a name="with-azure-data-cli-azdata-command"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 명령을 사용하는 경우

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s 
```

이 명령은 개발자가 설정한 매개 변수를 확인하는 서버 그룹의 사양을 반환합니다. engine\settings 섹션이 없는 경우 모든 매개 변수가 해당 기본값에서 실행됨을 의미합니다.

```console
 "
...
engine": {
      "settings": {
        "default": {
          "autovacuum_vacuum_threshold": "65"
        }
      }
    },
...
```

### <a name="with-kubectl-command"></a>kubectl 명령을 사용하는 경우

아래 단계를 수행하세요.

1. 서버 그룹에 대한 사용자 지정 리소스 정의의 종류를 검색합니다.

   다음을 실행합니다.

   ```azurecli
   az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
   ```

   예를 들면 다음과 같습니다.

   ```azurecli
   az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s
   ```

   이 명령은 개발자가 설정한 매개 변수를 확인하는 서버 그룹의 사양을 반환합니다. engine\settings 섹션이 없는 경우 모든 매개 변수가 해당 기본값에서 실행됨을 의미합니다.

   ```output
   > {
     >"apiVersion": "arcdata.microsoft.com/v1alpha1",
     >"**kind**": "**postgresql-12**",
     >"metadata": {
       >"creationTimestamp": "2020-08-25T14:32:23Z",
       >"generation": 1,
       >"name": "postgres01",
       >"namespace": "arc",  
   ```

   출력 결과에서 `kind` 필드를 찾아 값(예: `postgresql-12`을 따로 적어 둡니다.

2. 서버 그룹에 해당하는 Kubernetes 사용자 지정 리소스를 설명합니다. 

   이 명령의 일반적인 형식은 다음과 같습니다.

   ```console
   kubectl describe <kind of the custom resource> <server group name> -n <namespace name>
   ```

   예를 들면 다음과 같습니다.

   ```console
   kubectl describe postgresql-12 postgres01
   ```

   엔진 설정에 설정된 사용자 지정 값이 있는 경우 해당 값을 반환합니다. 예를 들면 다음과 같습니다.

   ```output
   Engine:
   ...
    Settings:
      Default:
        autovacuum_vacuum_threshold:  65
   ```

   엔진 설정에 대한 사용자 지정 값을 설정하지 않은 경우 `resultset`의 Engine Settings 섹션은 다음과 같이 비어 있게 됩니다.

   ```output
   Engine:
   ...
       Settings:
         Default:
   ```

## <a name="set-custom-values-for-engine-settings"></a>엔진 설정에 대한 사용자 지정 값 설정

아래 명령은 PostgreSQL 하이퍼스케일의 코디네이터 노드와 작업자 노드의 매개 변수를 동일한 값으로 설정합니다. 서버 그룹에는 역할당 매개 변수를 설정할 수 없습니다. 즉, 지정된 매개 변수를 코디네이터 노드에 대한 특정한 값과 작업자 노드에 대한 다른 값으로 구성할 수 없습니다.

### <a name="set-a-single-parameter"></a>단일 매개 변수 설정

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  <parameter name>=<parameter value> --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  shared_buffers=8MB --k8s-namespace <namespace> --use-k8s
```

### <a name="set-multiple-parameters-with-a-single-command"></a>단일 명령을 사용하여 여러 매개 변수 설정

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>=<parameter value>, <parameter name>=<parameter value>, --k8s-namespace <namespace> --use-k8s...'
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=50' --k8s-namespace <namespace> --use-k8s
```

### <a name="reset-a-parameter-to-its-default-value"></a>매개 변수를 기본값으로 다시 설정

매개 변수를 기본값으로 다시 설정하려면 값을 지정하지 않고 매개 변수를 설정합니다. 

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server edit -n postgres01 --k8s-namespace <namespace> --use-k8s --engine-settings  shared_buffers=
```

### <a name="reset-all-parameters-to-their-default-values"></a>모든 매개 변수를 기본값으로 다시 설정

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '' -re --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  '' -re --k8s-namespace <namespace> --use-k8s
```

## <a name="special-considerations"></a>특별 고려 사항

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>값에 쉼표, 공백 또는 특수 문자가 포함된 매개 변수를 설정합니다.

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>="<parameter value>"' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'custom_variable_classes = "plpgsql,plperl"' --k8s-namespace <namespace> --use-k8s
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>매개 변수 값에 환경 변수 전달

환경 변수는 설정되기 전에는 확인되지 않도록 "''" 안에 래핑해야 합니다.

예를 들면 다음과 같습니다. 

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'search_path = "$user"' --k8s-namespace <namespace> --use-k8s
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹 [스케일링 아웃(작업자 노드 추가)](scale-out-in-postgresql-hyperscale-server-group.md)에 관해 읽어보기
- 서버 그룹 [스케일링 아웃 또는 스케일링 다운(메모리/vCore 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)에 관해 읽어보기
