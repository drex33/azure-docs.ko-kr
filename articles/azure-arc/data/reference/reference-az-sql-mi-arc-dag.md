---
title: az sql mi-arc dag 참조
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc dag 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f568f7086f2730bde53a1cd4dd31cf25092c5dd7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848651"
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
az sql mi-arc dag create 
```
### <a name="examples"></a>예제
예제 1 - 분산형 가용성 그룹 사용자 지정 리소스 dagCr1을 만들어 로컬 sqlmi 인스턴스 sqlmi1과 원격 sqlmi 인스턴스 sqlmi2 사이에 분산형 가용성 그룹 dagName1을 만듭니다. 원격 sqlmi 주 미러 remotePrimary:5022와 원격 sqlmi 미러 엔드포인트 인증서 파일 ./sqlmi2.cer이 필요합니다.
```bash
az sql mi-arc dag create --name dagCr1 --dag-name dagName1 --local-instance-name sqlmi1 --local-primary local --remote-instance-name sqlmi2 --remote-mirroring-url remotePrimary:5022 --remote-mirroring-cert-file ./sqlmi2.cer --use-k8s
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
## <a name="az-sql-mi-arc-dag-delete"></a>az sql mi-arc dag delete
sqlmi 인스턴스에서 분산형 가용성 그룹 사용자 지정 리소스를 삭제하여 분산형 가용성 그룹을 삭제합니다. 사용자 지정 리소스 이름이 필요 합니다.
```bash
az sql mi-arc dag delete 
```
### <a name="examples"></a>예제
예제 1 - dagCr1이라는 분산형 가용성 그룹 리소스를 삭제합니다.
```bash
az sql mi-arc dag delete --name dagCr1 --use-k8s
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
## <a name="az-sql-mi-arc-dag-show"></a>az sql mi-arc dag show
분산형 가용성 그룹 사용자 지정 리소스를 표시합니다. 사용자 지정 리소스 이름이 필요합니다.
```bash
az sql mi-arc dag show 
```
### <a name="examples"></a>예제
예제 1 - dagCr1이라는 분산형 가용성 그룹 리소스를 표시합니다.
```bash
az sql mi-arc dag show --name dagCr1 --use-k8s
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
