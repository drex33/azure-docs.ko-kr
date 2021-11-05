---
title: az sql mi-arc config 참조
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc config 명령의 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 2dbe786f8a33528d028b4dc5f5476fa6f38a62b4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845684"
---
# <a name="az-sql-mi-arc-config"></a>az sql mi-arc config
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az sql mi-arc config init](#az-sql-mi-arc-config-init) | SQL 관리형 인스턴스의 CRD 및 사양 파일을 초기화합니다.
[az sql mi-arc config add](#az-sql-mi-arc-config-add) | 구성 파일에서 json 경로의 값을 추가합니다.
[az sql mi-arc config remove](#az-sql-mi-arc-config-remove) | 구성 파일에서 json 경로의 값을 제거합니다.
[az sql mi-arc config replace](#az-sql-mi-arc-config-replace) | 구성 파일에서 json 경로의 값을 바꿉니다.
[az sql mi-arc config patch](#az-sql-mi-arc-config-patch) | json 패치 파일을 기준으로 구성 파일을 패치합니다.
## <a name="az-sql-mi-arc-config-init"></a>az sql mi-arc config init
SQL 관리형 인스턴스의 CRD 및 사양 파일을 초기화합니다.
```bash
az sql mi-arc config init 
```
### <a name="examples"></a>예제
SQL 관리형 인스턴스의 CRD 및 사양 파일을 초기화합니다.
```bash
az sql mi-arc config init --path ./template
```
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, table, tsv  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org/](http://jmespath.org)를 참조하세요.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-sql-mi-arc-config-add"></a>az sql mi-arc config add
구성 파일에서 json 경로의 값을 추가합니다.  아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az sql mi-arc config add 
```
### <a name="examples"></a>예제
예제 1 - 스토리지를 추가합니다.
```bash
az sql mi-arc config add --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, table, tsv  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org/](http://jmespath.org)를 참조하세요.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-sql-mi-arc-config-remove"></a>az sql mi-arc config remove
구성 파일에서 json 경로의 값을 제거합니다. 아래의 모든 예제는 Bash로 제공됩니다. 다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다. 또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az sql mi-arc config remove 
```
### <a name="examples"></a>예제
예제 1 - 스토리지를 제거합니다.
```bash
az sql mi-arc config remove --path custom/spec.json --json-path ".spec.storage"
```
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, table, tsv  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org/](http://jmespath.org)를 참조하세요.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-sql-mi-arc-config-replace"></a>az sql mi-arc config replace
구성 파일에서 json 경로의 값을 바꿉니다.  아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az sql mi-arc config replace 
```
### <a name="examples"></a>예제
예제 1 - 단일 엔드포인트의 포트를 바꿉니다.
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
예제 2 - 스토리지를 바꿉니다.
```bash
az sql mi-arc config replace --path custom/spec.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, table, tsv  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org/](http://jmespath.org)를 참조하세요.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-sql-mi-arc-config-patch"></a>az sql mi-arc config patch
지정된 패치 파일에 따라 구성 파일을 패치합니다. 경로를 작성하는 방법에 대한 자세한 내용은 http://jsonpatch.com/ 을 참조하세요. replace 작업은 jsonpath 라이브러리(https://jsonpath.com/ )를 통해 해당 경로에 조건부를 사용할 수 있습니다. 모든 패치 json 파일은 해당 하는 `patch` op (추가, 바꾸기, 제거), 경로 및 값을 포함 하는 패치 배열이 있는의 키로 시작 해야 합니다. Op에는 `remove` 값, 경로만 필요 하지 않습니다. 아래 예제를 참조하세요.
```bash
az sql mi-arc config patch 
```
### <a name="examples"></a>예제
예제 1 - 패치 파일을 사용하여 단일 엔드포인트의 포트를 바꿉니다.
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
예제 2 - 패치 파일을 사용하여 스토리지를 바꿉니다.
```bash
az sql mi-arc config patch --path custom/spec.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
```
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, table, tsv  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org/](http://jmespath.org)를 참조하세요.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
