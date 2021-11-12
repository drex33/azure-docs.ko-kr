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
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 609ed203c794447f4fd0e0cd1a5267186a8eb67e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301744"
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

## <a name="show-current-custom-values-if-they-have-been-set"></a>설정 된 경우 현재 사용자 지정 값 표시

**Az CLI 명령을 사용 합니다.**

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s 
```


**또는 kubectl 명령을 사용 하 여 다음을 수행 합니다.**
```console
   kubectl describe postgresql <server group name> -n <namespace name>
   ```

   예를 들면 다음과 같습니다.

   ```console
   kubectl describe postgresql postgres01 -n arc
```

두 명령 모두 사용자가 설정한 매개 변수를 확인 하는 서버 그룹의 사양을 반환 합니다. engine\settings 섹션이 없는 경우 모든 매개 변수가 해당 기본값에서 실행됨을 의미합니다.

:::row:::
    :::column:::
        Postgres 엔진 설정에 대해 사용자 지정 값이 설정 되지 않은 경우의 출력 예입니다. 사양은 engine\settings. 섹션을 표시 하지 않습니다.
    :::column-end:::
    :::column:::
        ```console
          ...
          "spec": {
            "dev": false,
            "engine": {
              "extensions": [
                {
                  "name": "citus"
                }
              ],
              "version": 12
            },
            "scale": {
              "replicas": 1,
              "syncReplicas": "0",
          "workers": 4
            },
            ...
        ```
        :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Example of an output when custom values have been set for some of Postgres engine setting. The specs do show a section engine\settings.
    :::column-end:::
    :::column:::
        ```console
             ...
                Spec:
                  Dev:  false
                  Engine:
                    Extensions:
                      Name:  citus
                    Settings:
                      Default:
                        max_connections:  51
                      Roles:
                        Coordinator:
                          max_connections:  53
                        Worker:
                          max_connections:  52
                    Version:                12
                  Scale:
                    Replicas:       1
                    Sync Replicas:  0
                    Workers:        4
            ...
            ```
    :::column-end:::
:::row-end:::


The default value is, refer to the PostgreSQL documentation [here](https://www.postgresql.org/docs/current/runtime-config.html).



## Set custom values for engine settings

### Set a single parameter

**On both coordinator and worker roles:**

General syntax of the command:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'max_connections=51' --k8s-namespace arc --use-k8s
```

**작업자 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'max_connections=52' --k8s-namespace arc --use-k8s
```

**코디네이터 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'max_connections=53' --k8s-namespace arc --use-k8s
```



### <a name="set-multiple-parameters-with-a-single-command"></a>단일 명령을 사용하여 여러 매개 변수 설정

**코디네이터 및 작업자 역할 모두:**  
 
명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**작업자 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**코디네이터 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

### <a name="reset-one-parameter-to-its-default-value"></a>하나의 매개 변수를 기본값으로 다시 설정 합니다.

**코디네이터 및 작업자 역할 모두:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings '<ParameterName>='  --coordinator-settings '<ParameterName>=' --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers='  --coordinator-settings 'shared_buffers=' --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**코디네이터 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**작업자 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
````

### <a name="reset-all-parameters-to-their-default-values"></a>모든 매개 변수를 기본값으로 다시 설정

**코디네이터 및 작업자 역할 모두:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`' --replace-settings --k8s-namespace <namespace> --use-k8s
```

예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**코디네이터 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s

```
예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**작업자 역할의 경우에만:**

명령의 일반적인 구문은 다음과 같습니다.
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s
```
예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
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
