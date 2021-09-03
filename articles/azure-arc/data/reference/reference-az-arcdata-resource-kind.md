---
title: az arcdata resource-kind reference
titleSuffix: Azure Arc-enabled data services
description: azdata arc resource-kind 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 597bc660140ec2f0ac856a3f6c67db600480aa63
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529184"
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
출력 형식입니다.  허용되는 값: json, jsonc, none, table, tsv, yaml, yamlc.  기본값: json
#### `--query -q`
JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 [http://jmespath.org](http://jmespath.org)를 참조하세요.
#### `--subscription`
구독의 이름 또는 ID입니다. `az account set -s NAME_OR_ID`를 사용하여 기본 구독을 구성할 수 있습니다.
#### `--verbose`
로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다.
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
Arc 리소스 종류의 템플릿 파일을 가져옵니다.
```bash
az arcdata resource-kind get --kind -k 
                             [--dest -d]
```
### <a name="examples"></a>예
Arc 리소스 종류의 CRD 템플릿 파일을 가져오기 위한 예제 명령입니다.
```bash
az arcdata resource-kind get --kind SqlManagedInstance
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--kind -k`
템플릿 파일을 가져오려는 arc 리소스 종류입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--dest -d`
템플릿 파일을 저장할 디렉터리입니다.
`template`
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
