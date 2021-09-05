---
title: az arcdata dc status 참조
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc status 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9e795c71a5316960bfc7f0b1138afce3ecc4b957
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528349"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc status 표시](#az-arcdata-dc-status-show) | 데이터 컨트롤러의 상태를 표시합니다.
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status 표시
데이터 컨트롤러의 상태를 표시합니다.
```bash
az arcdata dc status show [--k8s-namespace -k] 
                          [--use-k8s]
```
### <a name="examples"></a>예제
특정 kubernetes 네임스페이스의 데이터 컨트롤러 상태를 표시합니다.
```bash
az arcdata dc status show --k8s-namespace <ns>
```
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--k8s-namespace -k`
데이터 컨트롤러가 있는 Kubernetes 네임스페이스입니다.
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
