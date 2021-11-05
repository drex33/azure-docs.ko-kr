---
title: az sql midb-arc
titleSuffix: Azure Arc-enabled data services
description: Az sql midb-arc 명령에 대 한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 8053ea28fe1ce1f7f05c1bb710265fb86839dcd2
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131859299"
---
# <a name="az-sql-midb-arc"></a>az sql midb-arc
## <a name="commands"></a>명령
| 명령 | Description|
| --- | --- |
[az sql midb-arc 복원](#az-sql-midb-arc-restore) | Azure Arc 사용 SQL 관리 되는 인스턴스로 데이터베이스를 복원 합니다.
## <a name="az-sql-midb-arc-restore"></a>az sql midb-arc 복원
Azure Arc 사용 SQL 관리 되는 인스턴스로 데이터베이스를 복원 합니다.
```bash
az sql midb-arc restore 
```
### <a name="examples"></a>예제
예 1-특정 시점 복원을 사용 하 여 데이터베이스 복원
```bash
az sql midb-arc restore --managed-instance sqlmi1 --name mysourcedb
 --dest-name mynewdb --time "2021-10-20T05:34:22Z" --k8s-namespace
 arc --use-k8s --dry-run
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
