---
title: Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 연결 엔드포인트 가져오기 및 연결 문자열 구성하기
titleSuffix: Azure Arc-enabled data services
description: Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 연결 엔드포인트 가져오기 및 연결 문자열 구성하기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 964b7fcca00afb91a457203d2ed53b885a254d5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528592"
---
# <a name="get-connection-endpoints-and-form-the-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 연결 엔드포인트 가져오기 및 연결 문자열 구성하기

이 문서에서는 서버 그룹에 대한 연결 엔드포인트를 검색하는 방법과 애플리케이션 및/또는 도구에서 사용할 수 있는 연결 문자열을 구성하는 방법을 설명합니다.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>연결 엔드포인트를 가져옵니다.

다음 명령을 실행합니다.
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
예를 들면 다음과 같습니다.
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```

애플리케이션을 연결하고 데이터베이스를 사용하기 위해 사용하는 PostgreSQL 엔드포인트, 로그 분석 및 모니터링을 위한 Kibana 및 Grafana 엔드포인트 등 엔드포인트 목록이 표시됩니다. 예를 들면 다음과 같습니다. 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
엔드포인트를 사용하여 다음을 수행합니다.
- 연결 문자열을 구성하고 클라이언트 도구 또는 애플리케이션에 연결합니다.
- 브라우저에서 Grafana 및 Kibana 대시보드 액세스합니다.

예를 들어 ‘PostgreSQL 인스턴스’라는 엔드포인트를 사용하여 psql을 통해 서버 그룹에 연결할 수 있습니다. 예를 들면 다음과 같습니다.
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - ‘PostgreSQL 인스턴스’라는 엔드포인트에 표시된 _postgres_ 사용자의 암호는 서버 그룹을 배포할 때 선택한 암호입니다.
> _ERROR: (401)_ 
> _Reason: Unauthorized_
> _HTTP response headers: HTTPHeaderDict({'Date': 'Sun, 06 Sep 2020 16:58:38 GMT', 'Content-Length': '0', 'WWW-Authenticate': '_ 
> _Basic realm="Login_ credentials required", Bearer error="invalid_token", error_description="The token is expired"'})_ 이 경우 위에서 설명한 대로 azdata에 다시 연결해야 합니다.

## <a name="from-cli-with-kubectl"></a>kubectl을 사용하는 CLI에서
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

이러한 명령은 아래와 같은 출력을 생성합니다. 해당 정보를 사용하여 연결 문자열을 구성할 수 있습니다.
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 

## <a name="form-connection-strings"></a>연결 문자열을 구성합니다.
서버 그룹에 대한 연결 문자열에 아래 템플릿 표를 사용합니다. 그런 다음, 필요에 따라 복사/붙여넣고 사용자 지정할 수 있습니다.

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>다음 단계
- 서버 그룹 [스케일링 아웃(작업자 노드 추가)](scale-out-in-postgresql-hyperscale-server-group.md)에 관해 읽어보기
- 서버 그룹 [스케일링 아웃 또는 스케일링 다운(메모리/vCore 늘리기/줄이기)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)에 관해 읽어보기


