---
title: az sql mi-arc 참조
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 81c8b2dbca45ac25db35fe15c2510b967bab1ae8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566628"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | SQL 엔드포인트를 보고 관리합니다.
[az sql mi-arc create](#az-sql-mi-arc-create) | SQL 관리형 인스턴스를 만듭니다.
[az sql mi-arc edit](#az-sql-mi-arc-edit) | SQL 관리형 인스턴스의 구성을 편집합니다.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | SQL 관리형 인스턴스를 삭제합니다.
[az sql mi-arc show](#az-sql-mi-arc-show) | SQL 관리형 인스턴스의 세부 정보를 표시합니다.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | sql mi에서 가용성 그룹 미러링 엔드포인트의 인증서를 검색하고 파일에 저장합니다.
[az sql mi-arc list](#az-sql-mi-arc-list) | SQL 관리형 인스턴스를 나열합니다.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | 구성 명령입니다.
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | 분산형 가용성 그룹을 만들거나 삭제합니다.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
SQL 관리형 인스턴스의 암호를 설정하려면 AZDATA_PASSWORD 환경 변수를 설정합니다.
```bash
az sql mi-arc create --name -n 
                     --k8s-namespace -k  
                     
[--path]  
                     
[--replicas]  
                     
[--cores-limit -c]  
                     
[--cores-request]  
                     
[--memory-limit -m]  
                     
[--memory-request]  
                     
[--storage-class-data -d]  
                     
[--storage-class-logs -g]  
                     
[--storage-class-datalogs]  
                     
[--storage-class-backups]  
                     
[--volume-size-data]  
                     
[--volume-size-logs]  
                     
[--volume-size-datalogs]  
                     
[--volume-size-backups]  
                     
[--no-wait]  
                     
[--no-external-endpoint]  
                     
[--cert-public-key-file]  
                     
[--cert-private-key-file]  
                     
[--service-cert-secret]  
                     
[--admin-login-secret]  
                     
[--license-type -l]  
                     
[--tier -t]  
                     
[--dev]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-labels]  
                     
[--service-annotations]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]  
                     
[--collation]  
                     
[--language]  
                     
[--agent-enabled]  
                     
[--trace-flags]
```
### <a name="examples"></a>예
SQL 관리형 인스턴스를 만듭니다.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace
```
HA 시나리오에서 3개의 복제본을 사용하여 SQL 관리형 인스턴스를 만듭니다.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3 --k8s-namespace namespace
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
SQL 관리형 인스턴스의 이름입니다.
#### `--k8s-namespace -k`
SQL 관리형 인스턴스를 배포할 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--path`
SQL 관리형 인스턴스 json 파일에 대한 azext_arcdata 파일의 경로입니다.
#### `--replicas`
이 옵션은 고가용성을 위해 Kubernetes 클러스터에 배포되는 SQL Managed Instance 복제본 수를 지정합니다. 허용되는 값은 '3' 또는 '1'이고, 기본값은 '1'입니다.
#### `--cores-limit -c`
관리형 인스턴스의 코어 한도(정수)입니다.
#### `--cores-request`
관리형 인스턴스의 코어(정수)에 대한 요청입니다.
#### `--memory-limit -m`
관리형 인스턴스의 용량 한도로, 뒤에 Gi(기가바이트)가 붙은 정수로 표시됩니다. 예: 4Gi
#### `--memory-request`
관리형 인스턴스의 용량에 대한 요청으로, 뒤에 Gi(기가바이트)가 붙은 정수로 표시됩니다. 예: 4Gi
#### `--storage-class-data -d`
데이터 파일(.mdf, .ndf)에 사용할 스토리지 클래스입니다. 값을 지정하지 않으면 스토리지 클래스가 지정되지 않으므로 Kubernetes에서 기본 스토리지 클래스를 사용합니다.
#### `--storage-class-logs -g`
로그(/var/log)에 사용할 스토리지 클래스입니다. 값을 지정하지 않으면 스토리지 클래스가 지정되지 않으므로 Kubernetes에서 기본 스토리지 클래스를 사용합니다.
#### `--storage-class-datalogs`
데이터베이스 로그(.ldf)에 사용할 스토리지 클래스입니다. 값을 지정하지 않으면 스토리지 클래스가 지정되지 않으므로 Kubernetes에서 기본 스토리지 클래스를 사용합니다.
#### `--storage-class-backups`
백업(/var/opt/mssql/backups)에 사용할 스토리지 클래스입니다. 값을 지정하지 않으면 스토리지 클래스가 지정되지 않으므로 Kubernetes에서 기본 스토리지 클래스를 사용합니다.
#### `--volume-size-data`
데이터에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--volume-size-logs`
로그에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--volume-size-datalogs`
데이터 로그에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--volume-size-backups`
백업에 사용할 스토리지 볼륨 크기로, 양수 뒤에 Ki(킬로바이트), Mi(메가바이트) 또는 Gi(기가바이트)가 옵니다.
#### `--no-wait`
지정된 경우에는 인스턴스가 준비 상태로 전환될 때까지 기다리지 않고 명령이 반환됩니다.
#### `--no-external-endpoint`
지정하면 외부 서비스가 생성되지 않습니다. 지정하지 않으면 데이터 컨트롤러와 동일한 서비스 유형을 사용하여 외부 서비스가 생성됩니다.
#### `--cert-public-key-file`
SQL Server에 사용할 PEM 형식 인증서 퍼블릭 키가 포함된 파일의 경로입니다.
#### `--cert-private-key-file`
SQL Server에 사용할 PEM 형식 인증서 프라이빗 키가 포함된 파일의 경로입니다.
#### `--service-cert-secret`
SQL 서비스 인증서를 호스트하도록 생성할 Kubernetes 비밀의 이름입니다.
#### `--admin-login-secret`
사용자 관리자 로그인 계정 자격 증명을 호스트하도록 생성할 Kubernetes 비밀의 이름입니다.
#### `--license-type -l`
이 관리형 인스턴스에 적용할 라이선스 유형입니다. 허용되는 값은 BasePrice, LicenseIncluded입니다. 기본값은 LicenseIncluded입니다. 라이선스 유형은 변경할 수 없습니다.
#### `--tier -t`
인스턴스에 대한 가격 책정 계층입니다. 허용되는 값은 BusinessCritical(줄여서 bc) 또는 GeneralPurpose(줄여서 gp)입니다. 기본값은 GeneralPurpose입니다. 가격 책정 계층은 변경할 수 없습니다.
#### `--dev`
이 매개 변수를 지정하면 개발 인스턴스로 간주되어 요금이 청구되지 않습니다.
#### `--labels`
SQL 관리형 인스턴스 레이블의 쉼표로 구분된 목록입니다.
#### `--annotations`
SQL 관리형 인스턴스 주석의 쉼표로 구분된 목록입니다.
#### `--service-labels`
모든 외부 서비스에 적용할 레이블의 쉼표로 구분된 목록입니다.
#### `--service-annotations`
모든 외부 서비스에 적용할 주석의 쉼표로 구분된 목록입니다.
#### `--storage-labels`
모든 PVC에 적용할 레이블의 쉼표로 구분된 목록입니다.
#### `--storage-annotations`
모든 PVC에 적용할 주석의 쉼표로 구분된 목록입니다.
#### `--use-k8s`
로컬 Kubernetes API를 사용하여 SQL 관리형 인스턴스를 만듭니다.
#### `--collation`
인스턴스에 대한 SQL Server 데이터 정렬입니다.
#### `--language`
인스턴스에 지원되는 언어 식별자(LCID)에 대한 SQL Server 로캘입니다.
#### `--agent-enabled`
인스턴스에 대해 SQL Server 에이전트를 사용하도록 설정합니다. 기본값은 사용 안 함입니다. 허용되는 값은 ‘true’ 또는 ‘false’입니다.
#### `--trace-flags`
쉼표로 구분된 추적 플래그 목록입니다. 기본적으로 플래그가 없습니다.
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
## <a name="az-sql-mi-arc-edit"></a>az sql mi-arc edit
SQL 관리형 인스턴스의 구성을 편집합니다.
```bash
az sql mi-arc edit --name -n 
                   [--k8s-namespace -k]  
                   
[--path]  
                   
[--cores-limit -c]  
                   
[--cores-request]  
                   
[--memory-limit -m]  
                   
[--memory-request]  
                   
[--no-wait]  
                   
[--dev]  
                   
[--labels]  
                   
[--annotations]  
                   
[--service-labels]  
                   
[--service-annotations]  
                   
[--agent-enabled]  
                   
[--trace-flags]  
                   
[--use-k8s]
```
### <a name="examples"></a>예
SQL 관리형 인스턴스의 구성을 편집합니다.
```bash
az sql mi-arc edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
편집 중인 SQL 관리형 인스턴스의 이름입니다. 인스턴스를 배포하는 데 사용되는 이름은 변경할 수 없습니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
SQL 관리형 인스턴스가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--path`
SQL 관리형 인스턴스 json 파일에 대한 azext_arcdata 파일의 경로입니다.
#### `--cores-limit -c`
관리형 인스턴스의 코어 한도(정수)입니다.
#### `--cores-request`
관리형 인스턴스의 코어(정수)에 대한 요청입니다.
#### `--memory-limit -m`
관리형 인스턴스의 용량 한도로, 뒤에 Gi(기가바이트)가 붙은 정수로 표시됩니다. 예: 4Gi
#### `--memory-request`
관리형 인스턴스의 용량에 대한 요청으로, 뒤에 Gi(기가바이트)가 붙은 정수로 표시됩니다. 예: 4Gi
#### `--no-wait`
지정된 경우에는 인스턴스가 준비 상태로 전환될 때까지 기다리지 않고 명령이 반환됩니다.
#### `--dev`
이 매개 변수를 지정하면 개발 인스턴스로 간주되어 요금이 청구되지 않습니다.
#### `--labels`
SQL 관리형 인스턴스 레이블의 쉼표로 구분된 목록입니다.
#### `--annotations`
SQL 관리형 인스턴스 주석의 쉼표로 구분된 목록입니다.
#### `--service-labels`
모든 외부 서비스에 적용할 레이블의 쉼표로 구분된 목록입니다.
#### `--service-annotations`
모든 외부 서비스에 적용할 주석의 쉼표로 구분된 목록입니다.
#### `--agent-enabled`
인스턴스에 대해 SQL Server 에이전트를 사용하도록 설정합니다. 기본값은 사용 안 함입니다.
#### `--trace-flags`
쉼표로 구분된 추적 플래그 목록입니다. 기본적으로 플래그가 없습니다.
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
SQL 관리형 인스턴스를 삭제합니다.
```bash
az sql mi-arc delete --name -n 
                     [--k8s-namespace -k]  
                     
[--use-k8s]
```
### <a name="examples"></a>예
SQL 관리형 인스턴스를 삭제합니다.
```bash
az sql mi-arc delete -n sqlmi1
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
삭제할 SQL 관리형 인스턴스의 이름입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
SQL 관리형 인스턴스가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
SQL 관리형 인스턴스의 세부 정보를 표시합니다.
```bash
az sql mi-arc show --name -n 
                   [--path -p]  
                   
[--k8s-namespace -k]  
                   
[--use-k8s]
```
### <a name="examples"></a>예
SQL 관리형 인스턴스의 세부 정보를 표시합니다.
```bash
az sql mi-arc show -n sqlmi1
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
표시할 SQL 관리형 인스턴스의 이름입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--path -p`
SQL 관리형 인스턴스의 전체 사양을 기록할 경로입니다. 생략하면 사양이 표준 출력에 기록됩니다.
#### `--k8s-namespace -k`
SQL 관리형 인스턴스가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
sql mi에서 가용성 그룹 미러링 엔드포인트의 인증서를 검색하고 파일에 저장합니다.
```bash
az sql mi-arc get-mirroring-cert --name -n 
                                 --cert-file  
                                 
[--k8s-namespace -k]  
                                 
[--use-k8s]
```
### <a name="examples"></a>예제
sqlmi1에서 가용성 그룹 미러링 엔드포인트의 인증서를 검색하고 fileName1 파일에 저장합니다.
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
SQL 관리형 인스턴스의 이름입니다.
#### `--cert-file`
검색된 인증서를 PEM 형식으로 저장할 로컬 파일 이름입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
SQL 관리형 인스턴스가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
SQL 관리형 인스턴스를 나열합니다.
```bash
az sql mi-arc list [--k8s-namespace -k] 
                   [--use-k8s]
```
### <a name="examples"></a>예
SQL 관리형 인스턴스를 나열합니다.
```bash
az sql mi-arc list
```
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
SQL 관리형 인스턴스가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
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
