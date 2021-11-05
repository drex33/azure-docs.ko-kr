---
title: az arcdata resource-kind reference
titleSuffix: Azure Arc-enabled data services
description: azdata arc resource-kind 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ad138bfc322aa109078e622b26d1f5254ec789ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852985"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | 정의하고 만들 수 있는 Arc의 사용 가능한 사용자 지정 리소스 종류를 나열합니다.
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | Arc 리소스 종류의 템플릿 파일을 가져옵니다.
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
정의하고 만들 수 있는 Arc의 사용 가능한 사용자 지정 리소스 종류를 나열합니다. 나열한 후 해당 사용자 지정 리소스를 정의하거나 만드는 데 필요한 템플릿 파일을 가져올 수 있습니다.
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>예
Arc의 사용 가능한 사용자 지정 리소스 종류를 나열하기 위한 예제 명령입니다.
```bash
az arcdata resource-kind list
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Arc 리소스 종류의 템플릿 파일을 가져옵니다.
```bash
az arcdata resource-kind get 
```
### <a name="examples"></a>예
Arc 리소스 종류의 CRD 템플릿 파일을 가져오기 위한 예제 명령입니다.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
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
