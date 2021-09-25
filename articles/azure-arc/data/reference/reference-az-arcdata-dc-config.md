---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc config 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4b40220b6675649808a63b5258859004796986ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128650309"
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
az arcdata dc config init [--path -p] 
                          [--source -s]  
                          
[--force -f]
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
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--path -p`
구성 프로필을 배치할 위치의 파일 경로입니다. 기본적으로 \<cwd\>/custom으로 설정되어 있습니다.
#### `--source -s`
구성 프로필 원본: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
#### `--force -f`
대상 파일을 강제로 덮어씁니다.
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는를 참조 [http://jmespath.org](http://jmespath.org) 하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 위치를 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
`arcdata dc config init`에서 사용할 수 있는 구성 프로필 선택 항목을 나열합니다.
```bash
az arcdata dc config list [--config-profile -c] 
                          
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
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--config-profile -c`
기본 구성 프로필: ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는를 참조 [http://jmespath.org](http://jmespath.org) 하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 위치를 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
구성 파일에서 json 경로의 값을 추가합니다. 아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az arcdata dc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>예제
데이터 컨트롤러 스토리지를 추가합니다.
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--path -p`
예를 들어 설정 하려는 구성의 데이터 컨트롤러 구성 파일 경로 `custom/control.json` 입니다.
#### `--json-values -j`
값에 해당하는 json 경로의 키-값 쌍 목록입니다. 예: key1.subkey1=value1,key2.subkey2=value2. key=‘{“kind”:“cluster”,“name”:“test-cluster”}’와 같은 인라인 json 값을 제공하거나 key=./values.json과 같은 파일 경로를 제공할 수 있습니다. add 명령은 조건을 지원하지 않습니다.  지정한 인라인 값이 “=”와 “,”를 포함하는 키-값 쌍인 경우 해당 문자를 이스케이프합니다.  (예: key1="key2\=val2\,key3\=val3") 경로를 표시 [http://jsonpatch.com/](http://jsonpatch.com/) 하는 방법에 대 한 예제는를 참조 하세요.  배열에 액세스하려는 경우 key.0=value와 같이 인덱스를 지정해야 합니다.
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는를 참조 [http://jmespath.org](http://jmespath.org) 하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 위치를 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
구성 파일에서 json 경로의 값을 제거합니다.  아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az arcdata dc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>예제
예제 1 - 데이터 컨트롤러 스토리지를 제거합니다.
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--path -p`
설정 하려는 구성의 데이터 컨트롤러 구성 파일 경로입니다. 예를 들면 다음과 같습니다. `custom/control.json`
#### `--json-path -j`
제거 하려는 값 (예:)을 나타내는 jsonpatch 라이브러리를 기반으로 하는 json 경로의 목록입니다 `key1.subkey1,key2.subkey2` . remove 명령은 조건을 지원하지 않습니다. 경로를 표시 [http://jsonpatch.com/](http://jsonpatch.com/) 하는 방법에 대 한 예제는를 참조 하세요.  배열에 액세스하려는 경우 key.0=value와 같이 인덱스를 지정해야 합니다.
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 를 [http://jmespath.org](http://jmespath.org) 참조하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 위치를 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
구성 파일에서 json 경로의 값을 바꿉니다.  아래의 모든 예제는 Bash로 제공됩니다.  다른 명령줄을 사용하는 경우 따옴표를 적절하게 이스케이프해야 할 수도 있습니다.  또는 패치 파일 기능을 사용할 수 있습니다.
```bash
az arcdata dc config replace --path -p 
                             --json-values -j
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
### <a name="required-parameters"></a>필수 매개 변수
#### `--path -p`
설정하려는 구성의 데이터 컨트롤러 구성 파일 경로입니다(예: `custom/control.json` ).
#### `--json-values -j`
값에 해당하는 json 경로의 키-값 쌍 목록입니다. 예: key1.subkey1=value1,key2.subkey2=value2. key=‘{“kind”:“cluster”,“name”:“test-cluster”}’와 같은 인라인 json 값을 제공하거나 key=./values.json과 같은 파일 경로를 제공할 수 있습니다. replace 명령은 jsonpath 라이브러리를 통한 조건을 지원합니다.  이 기능을 사용하려면 경로를 $로 시작합니다. 그러면 -j $.key1.key2[?(@.key3==“someValue”].key4=value와 같은 조건부를 수행할 수 있습니다. 지정한 인라인 값이 “=”와 “,”를 포함하는 키-값 쌍인 경우 해당 문자를 이스케이프합니다.  예들 들어 `key1="key2\=val2\,key3\=val3"`입니다. 아래 예제를 참조할 수 있습니다. 추가 도움말은 https://jsonpath.com/ 을 참조하세요.
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 를 [http://jmespath.org](http://jmespath.org) 참조하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 위치를 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
지정된 패치 파일에 따라 구성 파일을 패치합니다. 경로를 작성하는 방법에 대한 자세한 내용은 http://jsonpatch.com/ 을 참조하세요. replace 작업은 jsonpath 라이브러리(https://jsonpath.com/ )를 통해 해당 경로에 조건부를 사용할 수 있습니다. 모든 패치 json 파일은 패치 배열과 해당 작업(추가, 바꾸기, 제거), 경로 및 값을 포함하는 “patch” 키로 시작해야 합니다. “제거” 작업에는 값이 필요하지 않으며 경로만 있으면 됩니다. 아래 예제를 참조하세요.
```bash
az arcdata dc config patch --path 
                           --patch-file -p
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
### <a name="required-parameters"></a>필수 매개 변수
#### `--path`
설정하려는 구성의 데이터 컨트롤러 구성 파일 경로입니다(예: `custom/control.json` ).
#### `--patch-file -p`
jsonpatch 라이브러리(http://jsonpatch.com/ )를 기반으로 하는 패치 json 파일의 경로입니다. 수행하려는 패치 작업의 배열이 값으로 포함된 “patch” 키로 패치 json 파일을 시작해야 합니다. 패치 작업 경로의 경우 대부분의 작업은 key1.key2와 같은 점 표기법을 사용할 수 있습니다. 바꾸기 작업을 수행하고 조건부가 필요한 배열의 값을 바꾸려는 경우 경로를 $로 시작하여 jsonpath 표기법을 사용합니다. 그러면 $.key1.key2[?(@.key3==“someValue”].key4와 같은 조건부를 수행할 수 있습니다. 아래 예제를 참조하세요. 조건에 대한 추가 도움말을 보려면 https://jsonpath.com/ 을 참조하세요.
### <a name="global-arguments"></a>전역 인수
#### `--debug`
로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다.
#### `--help -h`
이 도움말 메시지를 표시하고 종료합니다.
#### `--output -o`
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 를 [http://jmespath.org](http://jmespath.org) 참조하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 위치를 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 보려면 `--debug`를 사용합니다.
