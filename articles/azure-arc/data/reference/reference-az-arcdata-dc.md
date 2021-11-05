---
title: az arcdata dc 참조
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 11/04/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 23cbe9c98e3c0ad86704061dadfad0e83f627ac3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852963"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | 데이터 컨트롤러를 만듭니다.
[az arcdata dc upgrade](#az-arcdata-dc-upgrade) | 데이터 컨트롤러를 업그레이드 합니다.
[az arcdata dc list-업그레이드](#az-arcdata-dc-list-upgrades) | 사용 가능한 업그레이드 버전을 나열 합니다.
[az arcdata dc delete](#az-arcdata-dc-delete) | 데이터 컨트롤러를 삭제합니다.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | 엔드포인트 명령입니다.
[az arcdata dc status](reference-az-arcdata-dc-status.md) | 상태 명령입니다.
[az arcdata dc config](reference-az-arcdata-dc-config.md) | 구성 명령입니다.
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | 데이터 컨트롤러를 디버그합니다.
[az arcdata dc export](#az-arcdata-dc-export) | 메트릭, 로그 또는 사용량을 내보냅니다.
[az arcdata dc upload](#az-arcdata-dc-upload) | 내보낸 데이터 파일을 업로드합니다.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
데이터 컨트롤러 만들기-kube config는 다음 환경 변수에서 제공 하는 모니터링 대시보드의 자격 증명과 함께 시스템에 필요 합니다. 여기에는 로그 대시보드의 AZDATA_LOGSUI_USERNAME 및 AZDATA_LOGSUI_PASSWORD 및 메트릭 대시보드의 AZDATA_METRICSUI_USERNAME 및 AZDATA_METRICSUI_PASSWORD 또는 AZDATA_USERNAME 및 AZDATA_PASSWORD 환경 변수 집합이 없는 경우 대체 (fallback)로 사용 됩니다.
```bash
az arcdata dc create 
```
### <a name="examples"></a>예제
간접적으로 연결 된 데이터 컨트롤러를 배포 합니다.
```bash
az arcdata dc create --name name --k8s-namespace namespace --connectivity-mode indirect --resource-group group  --location location --subscription subscription --use-k8s
```
직접 연결 된 데이터 컨트롤러를 배포 합니다.
```bash
az arcdata dc create --name name  --connectivity-mode direct --resource-group group  --location location --subscription subscription  --custom-location custom-location         
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
## <a name="az-arcdata-dc-upgrade"></a>az arcdata dc upgrade
데이터 컨트롤러를 지정한 원하는 버전으로 업그레이드 합니다.  원하는 버전을 지정 하지 않으면 최신 버전으로 업그레이드 하려고 시도 합니다. 원하는 버전이 확실 하지 않은 경우 목록 업그레이드 명령을 사용 하 여 사용 가능한 버전을 보거나--원음 실행 인수를 사용 하 여 사용할 버전을 표시할 수 있습니다.
```bash
az arcdata dc upgrade 
```
### <a name="examples"></a>예제
데이터 컨트롤러 업그레이드.
```bash
az arcdata dc upgrade --k8s-namespace namespace --use-k8s
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
## <a name="az-arcdata-dc-list-upgrades"></a>az arcdata dc list-업그레이드
업그레이드를 위해 docker 이미지 레지스트리에서 사용할 수 있는 버전을 나열 하려고 시도 합니다. -kube config는 다음과 같은 환경 변수 [' AZDATA_USERNAME ', ' AZDATA_PASSWORD ']와 함께 시스템에 필요 합니다.
```bash
az arcdata dc list-upgrades 
```
### <a name="examples"></a>예제
데이터 컨트롤러 업그레이드.
```bash
az arcdata dc list-upgrades --k8s-namespace namespace --use-k8s            
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
데이터 컨트롤러를 삭제합니다. 시스템에 kube 구성이 있어야 합니다.
```bash
az arcdata dc delete 
```
### <a name="examples"></a>예
간접 연결 된 데이터 컨트롤러를 삭제 합니다.
```bash
az arcdata dc delete --name name --k8s-namespace namespace --use-k8s
```
직접 연결 된 데이터 컨트롤러를 삭제 합니다.
```bash
az arcdata dc delete --name name --resource-group resource-group            
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
메트릭, 로그 또는 사용량을 파일로 내보냅니다.
```bash
az arcdata dc export 
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
데이터 컨트롤러에서 내보낸 데이터 파일을 Azure로 업로드합니다.
```bash
az arcdata dc upload 
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
