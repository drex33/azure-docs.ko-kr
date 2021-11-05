---
title: az sql mi-arc 참조
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 0a98cd873de2da0daaf793a2e40df5c4d5c38285
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852297"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | SQL 엔드포인트를 보고 관리합니다.
[az sql mi-arc create](#az-sql-mi-arc-create) | SQL 관리형 인스턴스를 만듭니다.
[az sql mi-arc update](#az-sql-mi-arc-update) | SQL 관리 되는 인스턴스의 구성을 업데이트 합니다.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | SQL 관리형 인스턴스를 삭제합니다.
[az sql mi-arc show](#az-sql-mi-arc-show) | SQL 관리형 인스턴스의 세부 정보를 표시합니다.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | sql mi에서 가용성 그룹 미러링 엔드포인트의 인증서를 검색하고 파일에 저장합니다.
[az sql mi – arc 업그레이드](#az-sql-mi-arc-upgrade) | SQL 관리 되는 인스턴스를 업그레이드 합니다.
[az sql mi-arc list](#az-sql-mi-arc-list) | SQL 관리형 인스턴스를 나열합니다.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | 구성 명령입니다.
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | 분산형 가용성 그룹을 만들거나 삭제합니다.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
SQL 관리형 인스턴스의 암호를 설정하려면 AZDATA_PASSWORD 환경 변수를 설정합니다.
```bash
az sql mi-arc create 
```
### <a name="examples"></a>예
간접적으로 연결 된 SQL 관리 되는 인스턴스를 만듭니다.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace --use-k8s
```
HA 시나리오에서 3 개의 복제본을 사용 하 여 간접적으로 연결 된 SQL 관리 되는 인스턴스를 만듭니다.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3  --k8s-namespace namespace --use-k8s
```
직접 연결 된 SQL 관리 되는 인스턴스를 만듭니다.
```bash
az sql mi-arc create --name name --resource-group group  --location location --subscription subscription   --custom-location custom-location
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
## <a name="az-sql-mi-arc-update"></a>az sql mi-arc update
SQL 관리 되는 인스턴스의 구성을 업데이트 합니다.
```bash
az sql mi-arc update 
```
### <a name="examples"></a>예제
SQL 관리 되는 인스턴스의 구성을 업데이트 합니다.
```bash
az sql mi-arc update --path ./spec.json -n sqlmi1 --use-k8s
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
SQL 관리형 인스턴스를 삭제합니다.
```bash
az sql mi-arc delete 
```
### <a name="examples"></a>예
제공 된 네임 스페이스를 사용 하 여 SQL 관리 되는 인스턴스를 삭제 합니다.
```bash
az sql mi-arc delete --name sqlmi1 --k8s-namespace namespace --use-k8s
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
SQL 관리형 인스턴스의 세부 정보를 표시합니다.
```bash
az sql mi-arc show 
```
### <a name="examples"></a>예
간접 연결 SQL 관리 되는 인스턴스의 세부 정보를 표시 합니다.
```bash
az sql mi-arc show --name sqlmi1 --k8s-namespace namespace --use-k8s
```
직접 연결 된 SQL 관리 되는 인스턴스에 대 한 세부 정보를 표시 합니다.
```bash
az sql mi-arc show --name sqlmi1 --resource-group resource-group            
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
sql mi에서 가용성 그룹 미러링 엔드포인트의 인증서를 검색하고 파일에 저장합니다.
```bash
az sql mi-arc get-mirroring-cert 
```
### <a name="examples"></a>예제
sqlmi1에서 가용성 그룹 미러링 엔드포인트의 인증서를 검색하고 fileName1 파일에 저장합니다.
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
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
## <a name="az-sql-mi-arc-upgrade"></a>az sql mi – arc 업그레이드
관리 되는 인스턴스 SQL 지정 된 버전으로 업그레이드 합니다.  원하는 버전을 지정 하지 않으면 데이터 컨트롤러 버전이 사용 됩니다.
```bash
az sql mi-arc upgrade 
```
### <a name="examples"></a>예제
SQL 관리 되는 인스턴스를 업그레이드 합니다.
```bash
az sql mi-arc upgrade -n sqlmi1 -k arc --desired-version v1.1.0 --use-k8s
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
SQL 관리형 인스턴스를 나열합니다.
```bash
az sql mi-arc list 
```
### <a name="examples"></a>예
SQL 관리형 인스턴스를 나열합니다.
```bash
az sql mi-arc list --use-k8s
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
