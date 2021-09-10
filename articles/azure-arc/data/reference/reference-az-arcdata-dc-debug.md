---
title: az arcdata dc debug reference
titleSuffix: Azure Arc-enabled data services
description: azdata arc dc debug 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 219471cf793394c024061f39319a98cc89477c76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535897"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc debug
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc debug copy-logs](#az-arcdata-dc-debug-copy-logs) | 로그를 복사합니다.
[az arcdata dc debug dump](#az-arcdata-dc-debug-dump) | 메모리 덤프를 트리거합니다.
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc debug copy-logs
데이터 컨트롤러에서 디버그 로그를 복사합니다. 시스템에 Kubernetes 구성이 있어야 합니다.
```bash
az arcdata dc debug copy-logs --k8s-namespace -k 
                              [--container -c]  
                              
[--target-folder -d]  
                              
[--pod]  
                              
[--resource-kind]  
                              
[--resource-name]  
                              
[--timeout -t]  
                              
[--skip-compress]  
                              
[--exclude-dumps]  
                              
[--exclude-system-logs ]  
                              
[--use-k8s]
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--k8s-namespace -k`
데이터 컨트롤러의 Kubernetes 네임스페이스입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--container -c`
유사한 이름을 가진 컨테이너의 로그를 복사합니다. 선택 사항이며, 기본적으로 모든 컨테이너의 로그를 복사합니다. 여러 번 지정할 수 없습니다. 여러 번 지정하면 마지막 항목이 사용됩니다.
#### `--target-folder -d`
로그를 복사할 대상 폴더 경로입니다. 선택 사항이며, 기본적으로 로컬 폴더에 결과를 만듭니다.  여러 번 지정할 수 없습니다. 여러 번 지정하면 마지막 항목이 사용됩니다.
#### `--pod`
유사한 이름을 가진 Pod의 로그를 복사합니다. 선택 사항이며, 기본적으로 모든 Pod의 로그를 복사합니다. 여러 번 지정할 수 없습니다. 여러 번 지정하면 마지막 항목이 사용됩니다.
#### `--resource-kind`
특정 종류의 리소스에 대한 로그를 복사합니다. 여러 번 지정할 수 없습니다. 여러 번 지정하면 마지막 항목이 사용됩니다. 지정한 경우 --resource-name도 지정하여 리소스를 식별해야 합니다.
#### `--resource-name`
지정한 이름의 리소스에 대한 로그를 복사합니다. 여러 번 지정할 수 없습니다. 여러 번 지정하면 마지막 항목이 사용됩니다. 지정한 경우 --resource-kind도 지정하여 리소스를 식별해야 합니다.
#### `--timeout -t`
명령이 완료될 때까지 대기할 시간(초)입니다. 기본값은 0으로, 무제한입니다.
#### `--skip-compress`
결과 폴더 압축을 건너뛸지 여부를 지정합니다. 기본값은 False입니다. 이 값은 결과 폴더를 압축합니다.
#### `--exclude-dumps`
결과 폴더에서 덤프를 제외할지 여부를 지정합니다. 기본값은 False입니다. 이 값은 덤프를 포함합니다.
#### `--exclude-system-logs `
컬렉션에서 시스템 로그를 제외할지 여부입니다. 기본값은 False입니다. 이 값은 시스템 로그를 포함합니다.
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
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc debug dump
메모리 덤프를 트리거하고 컨테이너에서 복사합니다. 시스템에 Kubernetes 구성이 있어야 합니다.
```bash
az arcdata dc debug dump --k8s-namespace -k 
                         [--container -c]  
                         
[--target-folder -d]  
                         
[--use-k8s]
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--k8s-namespace -k`
데이터 컨트롤러의 Kubernetes 네임스페이스입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--container -c`
실행 중인 프로세스의 덤프를 위해 트리거할 대상 컨테이너입니다.
`controller`
#### `--target-folder -d`
덤프를 복사할 대상 폴더입니다. `./output/dump`
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
