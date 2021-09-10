---
title: az sql mi-arc dag 참조
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc dag 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4ebd6e71c14c9a319e99e68eca15f19a21d52033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567336"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az sql mi-arc dag create](#az-sql-mi-arc-dag-create) | 분산형 가용성 그룹 사용자 지정 리소스를 만듭니다.
[az sql mi-arc dag delete](#az-sql-mi-arc-dag-delete) | sqlmi 인스턴스에서 분산형 가용성 그룹 사용자 지정 리소스를 삭제합니다.
[az sql mi-arc dag show](#az-sql-mi-arc-dag-show) | 분산형 가용성 그룹 사용자 지정 리소스를 표시합니다.
## <a name="az-sql-mi-arc-dag-create"></a>az sql mi-arc dag create
분산형 가용성 그룹 사용자 지정 리소스를 만들어 분산형 가용성 그룹을 만듭니다.
```bash
az sql mi-arc dag create --name -n 
                         --dag-name -d  
                         
--local-instance-name -l  
                         
--local-primary -p  
                         
--remote-instance-name -r  
                         
--remote-mirroring-url -u  
                         
--remote-mirroring-cert-file -f  
                         
[--k8s-namespace -k]  
                         
[--path]  
                         
[--use-k8s]
```
### <a name="examples"></a>예제
예제 1 - 분산형 가용성 그룹 사용자 지정 리소스 dagCr1을 만들어 로컬 sqlmi 인스턴스 sqlmi1과 원격 sqlmi 인스턴스 sqlmi2 사이에 분산형 가용성 그룹 dagName1을 만듭니다. 원격 sqlmi 주 미러 remotePrimary:5022와 원격 sqlmi 미러 엔드포인트 인증서 파일 ./sqlmi2.cer이 필요합니다.
```bash
az sql mi-arc dag create --name=dagCr1 --dag-name=dagName1  --local-instance-name=sqlmi1 --local-primary=true --remote-instance-name=sqlmi2  --remote-mirroring-url==remotePrimary:5022 --remote-mirroing-cert-file="./sqlmi2.cer"
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
분산형 가용성 그룹 리소스의 이름입니다.
#### `--dag-name -d`
이 SQL 관리형 인스턴스에 대한 분산형 가용성 그룹의 이름입니다. 로컬 및 원격 모두 같은 이름을 사용해야 합니다.
#### `--local-instance-name -l`
로컬 SQL 관리형 인스턴스의 이름입니다.
#### `--local-primary -p`
True이면 로컬 SQL 관리형 인스턴스가 주 지역임을 나타냅니다. False이면 로컬 SQL 관리형 인스턴스가 보조 지역임을 나타냅니다.
#### `--remote-instance-name -r`
원격 SQL 관리형 인스턴스 또는 원격 SQL 가용성 그룹의 이름입니다.
#### `--remote-mirroring-url -u`
원격 SQL 관리형 인스턴스 또는 원격 SQL 가용성 그룹의 미러링 엔드포인트 URL입니다.
#### `--remote-mirroring-cert-file -f`
원격 SQL 관리형 인스턴스 또는 원격 SQL 가용성 그룹에 대한 미러링 엔드포인트 퍼블릭 인증서의 파일 이름입니다. PEM 형식만 지원됩니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
SQL 관리형 인스턴스가 있는 네임스페이스입니다. 네임스페이스를 지정하지 않으면 kubeconfig에 정의된 네임스페이스가 사용됩니다.
#### `--path`
사용자 지정 리소스 사양의 경로(즉, custom/spec.json)입니다.
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
sqlmi 인스턴스에서 분산형 가용성 그룹 사용자 지정 리소스를 삭제하여 분산형 가용성 그룹을 삭제합니다. 사용자 지정 리소스 이름이 필요합니다.
```bash
az sql mi-arc dag delete --name 
                         [--k8s-namespace -k]  
                         
[--use-k8s]
```
### <a name="examples"></a>예제
예제 1 - dagCr1이라는 분산형 가용성 그룹 리소스를 삭제합니다.
```bash
az sql mi-arc dag delete --name=dagCr1
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name`
분산형 가용성 그룹 리소스의 이름입니다.
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
분산형 가용성 그룹 사용자 지정 리소스를 표시합니다. 사용자 지정 리소스 이름이 필요합니다.
```bash
az sql mi-arc dag show --name 
                       [--k8s-namespace -k]  
                       
[--use-k8s]
```
### <a name="examples"></a>예제
예제 1 - dagCr1이라는 분산형 가용성 그룹 리소스를 표시합니다.
```bash
az sql mi-arc dag show --name=dagCr1
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name`
분산형 가용성 그룹 리소스의 이름입니다.
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
