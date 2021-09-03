---
title: az postgres arc-server reference
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c58819707eb5987bddeb0d8983c29e7519b69818
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566882"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만듭니다.
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성을 편집합니다.
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 삭제합니다.
[az postgres arc-server show](#az-postgres-arc-server-show) | Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 세부 정보를 표시합니다.
[az postgres arc-server list](#az-postgres-arc-server-list) | Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 나열합니다.
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 엔드포인트를 관리합니다.
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
서버 그룹의 암호를 설정하려면 AZDATA_PASSWORD 환경 변수를 설정합니다.
```bash
az postgres arc-server create --name -n 
                              [--path]  
                              
[--k8s-namespace -k]  
                              
[--cores-limit]  
                              
[--cores-request]  
                              
[--memory-limit]  
                              
[--memory-request]  
                              
[--storage-class-data]  
                              
[--storage-class-logs]  
                              
[--storage-class-backups]  
                              
[--volume-claim-mounts]  
                              
[--extensions]  
                              
[--volume-size-data]  
                              
[--volume-size-logs]  
                              
[--volume-size-backups]  
                              
[--workers -w]  
                              
[--engine-version]  
                              
[--no-external-endpoint]  
                              
[--port]  
                              
[--no-wait]  
                              
[--engine-settings]  
                              
[--coordinator-settings]  
                              
[--worker-settings]  
                              
[--use-k8s]
```
### <a name="examples"></a>예
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만듭니다.
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
엔진 설정을 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만듭니다. 아래 두 예제는 모두 유효합니다.
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
볼륨 클레임 탑재를 사용하여 PostgreSQL 서버 그룹을 만듭니다.
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
다른 노드 역할에 대한 특정 메모리 제한을 사용하여 PostgreSQL 서버 그룹을 만듭니다.
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 이름입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--path`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 소스 json 파일의 경로입니다. 선택 사항입니다.
#### `--k8s-namespace -k`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 배포되는 Kubernetes 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--cores-limit`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대해 노드당 사용할 수 있는 최대 CPU 코어 개수입니다. 부분 코어가 지원됩니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--cores-request`
서비스를 예약하기 위해 노드당 사용할 수 있어야 하는 최소 CPU 코어 수입니다. 부분 코어가 지원됩니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--memory-limit`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 메모리 제한으로, 숫자 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--memory-request`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 메모리 요청으로, 숫자 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--storage-class-data`
영구적 데이터 볼륨에 사용할 스토리지 클래스입니다.
#### `--storage-class-logs`
영구적 로그 볼륨에 사용할 스토리지 클래스입니다.
#### `--storage-class-backups`
영구적 백업 볼륨에 사용할 스토리지 클래스입니다.
#### `--volume-claim-mounts`
쉼표로 구분된 볼륨 클레임 탑재 목록입니다. 볼륨 클레임 탑재는(동일한 네임스페이스에 있는) 영구 볼륨 클레임과 볼륨 유형(및 볼륨 유형에 따라 선택적 메타데이터)이 콜론으로 구분된 쌍입니다. 영구 볼륨은 PostgreSQL 서버 그룹의 각 Pod에 탑재됩니다. 탑재 경로는 볼륨 유형에 따라 달라질 수 있습니다.
#### `--extensions`
시작 시 로드해야 하는 Postgres 확장의 쉼표로 구분된 목록입니다. 지원되는 값은 postgres 설명서를 참조하세요.
#### `--volume-size-data`
데이터에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--volume-size-logs`
로그에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--volume-size-backups`
백업에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--workers -w`
서버 그룹에서 프로비저닝할 작업자 노드의 개수입니다. 미리 보기에서는 작업자 노드의 개수를 줄이는 것이 지원되지 않습니다. 자세한 내용은 설명서를 참조하세요.
#### `--engine-version`
11 또는 12여야 합니다. 기본 값은 12입니다.
`12`
#### `--no-external-endpoint`
지정하면 외부 서비스가 생성되지 않습니다. 지정하지 않으면 데이터 컨트롤러와 동일한 서비스 유형을 사용하여 외부 서비스가 생성됩니다.
#### `--port`
선택 사항입니다.
#### `--no-wait`
지정된 경우에는 인스턴스가 준비 상태로 전환될 때까지 기다리지 않고 명령이 반환됩니다.
#### `--engine-settings`
‘key1=val1, key2=val2’ 형식으로 구성된 Postgres 엔진 설정의 쉼표로 구분된 목록입니다.
#### `--coordinator-settings`
‘코디네이터’ 노드 역할에 적용할 ‘key1=val1, key2=val2’ 형식으로 된 Postgres 엔진 설정의 쉼표로 구분된 목록입니다. 노드 역할별 설정을 지정하면 기본 설정이 무시되고 여기에 제공된 설정으로 재정의됩니다.
#### `--worker-settings`
‘작업자’ 노드 역할에 적용할 ‘key1=val1, key2=val2’ 형식으로 된 Postgres 엔진 설정의 쉼표로 구분된 목록입니다. 노드 역할별 설정을 지정하면 기본 설정이 무시되고 여기에 제공된 설정으로 재정의됩니다.
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성을 편집합니다.
```bash
az postgres arc-server edit --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--workers -w]  
                            
[--cores-limit]  
                            
[--cores-request]  
                            
[--memory-limit]  
                            
[--memory-request]  
                            
[--extensions]  
                            
[--port]  
                            
[--no-wait]  
                            
[--engine-settings]  
                            
[--replace-settings]  
                            
[--coordinator-settings]  
                            
[--worker-settings]  
                            
[--admin-password]  
                            
[--use-k8s]
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성을 편집합니다.
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
코디네이터 노드의 엔진 설정을 사용하여 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 편집합니다.
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 편집하고 기존 엔진 설정을 새 설정 key1=val1로 바꿉니다.
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
편집하려는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 이름입니다. 인스턴스를 배포하는 데 사용되는 이름은 변경할 수 없습니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 배포되는 Kubernetes 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--path`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대한 소스 json 파일의 경로입니다. 선택 사항입니다.
#### `--workers -w`
서버 그룹에서 프로비저닝할 작업자 노드의 개수입니다. 미리 보기에서는 작업자 노드의 개수를 줄이는 것이 지원되지 않습니다. 자세한 내용은 설명서를 참조하세요.
#### `--cores-limit`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 대해 노드당 사용할 수 있는 최대 CPU 코어 개수입니다. 부분 코어가 지원됩니다. cores_limit를 제거하려면 해당 값을 빈 문자열로 지정합니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--cores-request`
서비스를 예약하기 위해 노드당 사용할 수 있어야 하는 최소 CPU 코어 수입니다. 부분 코어가 지원됩니다. cores_request를 제거하려면 해당 값을 빈 문자열로 지정합니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--memory-limit`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 메모리 제한으로, 숫자 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다. memory_limit를 제거하려면 해당 값을 빈 문자열로 지정합니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--memory-request`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 메모리 요청으로, 숫자 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다. memory_request를 제거하려면 해당 값을 빈 문자열로 지정합니다. 선택적으로 값이 포함된 쉼표로 구분된 역할 목록을 <role>= 형식으로 지정할 수 있습니다.<value>. 유효한 역할은 “코디네이터” 또는 “c”, “작업자” 또는 “w”입니다. 역할을 지정하지 않으면 PostgreSQL 하이퍼스케일 서버 그룹의 모든 노드에 설정이 적용됩니다.
#### `--extensions`
시작 시 로드해야 하는 Postgres 확장의 쉼표로 구분된 목록입니다. 지원되는 값은 postgres 설명서를 참조하세요.
#### `--port`
선택 사항입니다.
#### `--no-wait`
지정된 경우에는 인스턴스가 준비 상태로 전환될 때까지 기다리지 않고 명령이 반환됩니다.
#### `--engine-settings`
‘key1=val1, key2=val2’ 형식으로 구성된 Postgres 엔진 설정의 쉼표로 구분된 목록입니다. 지정한 설정이 기존 설정과 병합됩니다. 설정을 제거하려면 ‘removedKey=’와 같이 빈 값을 지정합니다. 다시 시작해야 하는 엔진 설정을 변경하면 서비스가 다시 시작되어 설정을 즉시 적용합니다.
#### `--replace-settings`
--engine-settings로 지정하면 기존의 모든 사용자 지정 엔진 설정이 새로운 설정 및 값 세트로 바뀝니다.
#### `--coordinator-settings`
‘코디네이터’ 노드 역할에 적용할 ‘key1=val1, key2=val2’ 형식으로 된 Postgres 엔진 설정의 쉼표로 구분된 목록입니다. 노드 역할별 설정을 지정하면 기본 설정이 무시되고 여기에 제공된 설정으로 재정의됩니다.
#### `--worker-settings`
‘작업자’ 노드 역할에 적용할 ‘key1=val1, key2=val2’ 형식으로 된 Postgres 엔진 설정의 쉼표로 구분된 목록입니다. 노드 역할별 설정을 지정하면 기본 설정이 무시되고 여기에 제공된 설정으로 재정의됩니다.
#### `--admin-password`
지정된 경우 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 관리자 암호는 AZDATA_PASSWORD 환경 변수(있는 경우)의 값으로 설정되거나, 해당 환경 변수가 없으면 프롬프트 값으로 설정됩니다.
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 삭제합니다.
```bash
az postgres arc-server delete --name -n 
                              [--k8s-namespace -k]  
                              
[--force -f]  
                              
[--use-k8s]
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 삭제합니다.
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 이름입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 배포되는 Kubernetes 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--force -f`
확인 없이 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 강제 삭제합니다.
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 세부 정보를 표시합니다.
```bash
az postgres arc-server show --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--use-k8s]
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 세부 정보를 표시합니다.
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 이름입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 배포되는 Kubernetes 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--path`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 전체 사양을 작성할 경로입니다. 생략하면 사양이 표준 출력에 기록됩니다.
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 나열합니다.
```bash
az postgres arc-server list [--k8s-namespace -k] 
                            [--use-k8s]
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 나열합니다.
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 배포되는 Kubernetes 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
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
