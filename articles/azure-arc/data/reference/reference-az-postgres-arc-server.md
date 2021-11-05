---
title: az postgres arc-server reference
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9a5746f4a18cf0df2045c22c58cf26a9aa44fc74
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845706"
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
az postgres arc-server create 
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성을 편집합니다.
```bash
az postgres arc-server edit 
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 삭제합니다.
```bash
az postgres arc-server delete 
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 삭제합니다.
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 세부 정보를 표시합니다.
```bash
az postgres arc-server show 
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 세부 정보를 표시합니다.
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 나열합니다.
```bash
az postgres arc-server list 
```
### <a name="examples"></a>예제
Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 나열합니다.
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
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
