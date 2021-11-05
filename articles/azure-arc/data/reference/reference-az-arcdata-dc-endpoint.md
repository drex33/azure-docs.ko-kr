---
title: az arcdata dc endpoint reference
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc endpoint 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9d07d5e5263fd887af49552bb45eb98ae7f35441
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845744"
---
# <a name="az-arcdata-dc-endpoint"></a>az arcdata dc endpoint
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc endpoint list](#az-arcdata-dc-endpoint-list) | 데이터 컨트롤러 엔드포인트를 나열합니다.
## <a name="az-arcdata-dc-endpoint-list"></a>az arcdata dc endpoint list
데이터 컨트롤러 엔드포인트를 나열합니다.
```bash
az arcdata dc endpoint list 
```
### <a name="examples"></a>예
사용 가능한 모든 데이터 컨트롤러 엔드포인트를 나열합니다.
```bash
az arcdata dc endpoint list --k8s-namespace namespace
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
