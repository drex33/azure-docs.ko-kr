---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc config 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 72df8db00f1ab0366cc4daf8af8e5613645fc813
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852525"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | `az arcdata dc create`와 함께 사용할 수 있는 데이터 컨트롤러 구성 프로필을 초기화합니다.
[az arcdata dc config list](#az-arcdata-dc-config-list) | 사용 가능한 구성 프로필 선택 항목을 나열합니다.
[az arcdata dc config add](#az-arcdata-dc-config-add) | 구성 파일에서 json 경로의 값을 추가합니다.
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | 구성 파일에서 json 경로의 값을 제거합니다.
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | 구성 파일에서 json 경로의 값을 바꿉니다.
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | json 패치 파일을 기반으로 구성 파일을 패치합니다.
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
`az arcdata dc create`와 함께 사용할 수 있는 데이터 컨트롤러 구성 프로필을 초기화합니다. 구성 프로필의 특정 원본을 인수에 지정할 수 있습니다.
```bash
az arcdata dc config init 
```
### <a name="examples"></a>예제
안내에 따른 데이터 컨트롤러 구성 초기화 환경 - 필요한 값에 대한 프롬프트가 표시됩니다.
```bash
az arcdata dc config init
```
인수가 있는 arcdata dc config init는 ./custom에 aks-dev-test의 구성 프로필을 만듭니다.
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
`arcdata dc config init`에서 사용할 수 있는 구성 프로필 선택 항목을 나열합니다.
```bash
az arcdata dc config list 
```
### <a name="examples"></a>예제
사용 가능한 구성 프로필 이름을 모두 표시합니다.
```bash
az arcdata dc config list
```
특정 구성 프로필의 json을 표시합니다.
```bash
az arcdata dc config list --config-profile aks-dev-test
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
구성 파일에서 json 경로의 값을 추가합니다. 아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az arcdata dc config add 
```
### <a name="examples"></a>예제
데이터 컨트롤러 스토리지를 추가합니다.
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
구성 파일에서 json 경로의 값을 제거합니다.  아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az arcdata dc config remove 
```
### <a name="examples"></a>예제
예제 1 - 데이터 컨트롤러 스토리지를 제거합니다.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
구성 파일에서 json 경로의 값을 바꿉니다.  아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az arcdata dc config replace 
```
### <a name="examples"></a>예제
예제 1 - 단일 엔드포인트(데이터 컨트롤러 엔드포인트)의 포트를 바꿉니다.
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
예제 2 - 데이터 컨트롤러 스토리지를 바꿉니다.
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
지정된 패치 파일에 따라 구성 파일을 패치합니다. 경로를 작성하는 방법에 대한 자세한 내용은 http://jsonpatch.com/ 을 참조하세요. replace 작업은 jsonpath 라이브러리(https://jsonpath.com/ )를 통해 해당 경로에 조건부를 사용할 수 있습니다. 모든 패치 json 파일은 패치 배열과 해당 작업(추가, 바꾸기, 제거), 경로 및 값을 포함하는 “patch” 키로 시작해야 합니다. “제거” 작업에는 값이 필요하지 않으며 경로만 있으면 됩니다. 아래 예제를 참조하세요.
```bash
az arcdata dc config patch 
```
### <a name="examples"></a>예제
예제 1 - 패치 파일을 사용하여 단일 엔드포인트(데이터 컨트롤러 엔드포인트)의 포트를 바꿉니다.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
예제 2 - 패치 파일을 사용하여 데이터 컨트롤러 스토리지를 바꿉니다.
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

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
