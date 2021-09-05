---
title: az sql mi-arc endpoint 참조
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc endpoint 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ca3685c6769e45d43b513ea11355795af332a849
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536225"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az sql mi-arc endpoint 목록](#az-sql-mi-arc-endpoint-list) | SQL 엔드포인트를 나열합니다.
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint 목록
SQL 엔드포인트를 나열합니다.
```bash
az sql mi-arc endpoint list [--name -n] 
                            [--k8s-namespace -k]  
                            
[--use-k8s]
```
### <a name="examples"></a>예제
SQL 관리되는 인스턴스의 엔드포인트를 나열합니다.
```bash
az sql mi-arc endpoint list -n sqlmi1
```
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--name -n`
표시되는 SQL 인스턴스의 이름입니다. 생략하면 모든 인스턴스의 모든 엔드포인트가 표시됩니다.
#### `--k8s-namespace -k`
SQL Managed Instance가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--use-k8s`
로컬 Kubernetes API를 사용하여 이 작업을 수행합니다.
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org](http://jmespath.org)를 참조하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 구독을 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다.
