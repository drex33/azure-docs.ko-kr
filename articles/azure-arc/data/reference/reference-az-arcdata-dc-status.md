---
title: az arcdata dc status 참조
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc status 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 542f0f9568150abf0646e1a494e14eec2be6e524
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841639"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc status 표시](#az-arcdata-dc-status-show) | 데이터 컨트롤러의 상태를 표시합니다.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status 표시
데이터 컨트롤러의 상태를 표시합니다.
```bash
az arcdata dc status show 
```
### <a name="examples"></a>예제
특정 kubernetes 네임스페이스의 데이터 컨트롤러 상태를 표시합니다.
```bash
az arcdata dc status show --k8s-namespace namespace --use-k8s
```
특정 리소스 그룹에 직접 연결 된 데이터 컨트롤러의 상태를 표시 합니다.
```bash
az arcdata dc status show --resource-group resource-group    
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
