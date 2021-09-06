---
title: az arcdata dc 참조
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc 명령에 대한 참조 문서입니다.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f5b804ca5bf0c38165d848f75de7a3cd428fb2f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528934"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>명령
| 명령 | 설명|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | 데이터 컨트롤러를 만듭니다.
[az arcdata dc delete](#az-arcdata-dc-delete) | 데이터 컨트롤러를 삭제합니다.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | 엔드포인트 명령입니다.
[az arcdata dc status](reference-az-arcdata-dc-status.md) | 상태 명령입니다.
[az arcdata dc config](reference-az-arcdata-dc-config.md) | 구성 명령입니다.
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | 데이터 컨트롤러를 디버그합니다.
[az arcdata dc export](#az-arcdata-dc-export) | 메트릭, 로그 또는 사용량을 내보냅니다.
[az arcdata dc upload](#az-arcdata-dc-upload) | 내보낸 데이터 파일을 업로드합니다.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
데이터 컨트롤러를 만듭니다. 시스템에 kube 구성과 [‘AZDATA_USERNAME’, ‘AZDATA_PASSWORD’] 환경 변수가 있어야 합니다.
```bash
az arcdata dc create --k8s-namespace -k 
                     --name -n  
                     
--connectivity-mode  
                     
--resource-group -g  
                     
--location -l  
                     
[--profile-name]  
                     
[--path -p]  
                     
[--storage-class]  
                     
[--infrastructure]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-annotations]  
                     
[--service-labels]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]
```
### <a name="examples"></a>예
데이터 컨트롤러 배포입니다.
```bash
az arcdata dc create --name name --k8s-namespace namespace  --connectivity-mode indirect --resource-group group  --location location, --subscription subscription
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--k8s-namespace -k`
데이터 컨트롤러를 배포할 Kubernetes 네임스페이스입니다. 이미 있으면 해당 네임스페이스가 사용됩니다. 네임스페이스가 없으면 먼저 만들려고 시도합니다.
#### `--name -n`
데이터 컨트롤러의 이름입니다.
#### `--connectivity-mode`
데이터 컨트롤러를 실행할 Azure 연결(간접 또는 직접)입니다.
#### `--resource-group -g`
데이터 컨트롤러 리소스를 추가할 Azure 리소스 그룹입니다.
#### `--location -l`
데이터 컨트롤러 메타데이터를 저장할 Azure 위치(예: eastus)입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--profile-name`
기존 구성 프로필의 이름입니다. `az arcdata dc config list`를 실행하여 사용 가능한 옵션을 확인합니다. ['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-hci', 'azure-arc-aks-premium-storage'] 중 하나입니다.
#### `--path -p`
사용할 사용자 지정 구성 프로필이 포함된 디렉터리의 경로입니다. `az arcdata dc config init`를 실행하여 사용자 지정 구성 프로필을 만듭니다.
#### `--storage-class`
볼륨이 필요한 모든 데이터 컨트롤러 Pod의 모든 영구적 데이터 및 로그 볼륨에 사용할 스토리지 클래스입니다.
#### `--infrastructure`
데이터 컨트롤러를 실행하는 데 사용할 인프라입니다. 허용되는 값: ['aws', 'gcp', 'azure', 'alibaba', 'onpremises', 'other', 'auto']
#### `--labels`
모든 데이터 컨트롤러 리소스에 적용할 쉼표로 구분된 레이블 목록입니다.
#### `--annotations`
모든 데이터 컨트롤러 리소스를 적용할 쉼표로 구분된 주석 목록입니다.
#### `--service-annotations`
모든 외부 데이터 컨트롤러 서비스에 적용할 쉼표로 구분된 주석 목록입니다.
#### `--service-labels`
모든 외부 데이터 컨트롤러 서비스에 적용할 쉼표로 구분된 레이블 목록입니다.
#### `--storage-labels`
데이터 컨트롤러에서 만든 모든 PVC에 적용할 쉼표로 구분된 레이블 목록입니다.
#### `--storage-annotations`
데이터 컨트롤러에서 만든 모든 PVC에 적용할 쉼표로 구분된 주석 목록입니다.
#### `--use-k8s`
로컬 Kubernetes API를 사용하여 데이터 컨트롤러를 만듭니다.
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
데이터 컨트롤러를 삭제합니다. 시스템에 kube 구성이 있어야 합니다.
```bash
az arcdata dc delete --name -n 
                     --k8s-namespace -k  
                     
[--force -f]  
                     
[--yes -y]
```
### <a name="examples"></a>예
데이터 컨트롤러 배포입니다.
```bash
az arcdata dc delete --name name --k8s-namespace namespace
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--name -n`
데이터 컨트롤러 이름입니다.
#### `--k8s-namespace -k`
데이터 컨트롤러가 있는 Kubernetes 네임스페이스입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--force -f`
데이터 컨트롤러와 모든 데이터 서비스를 강제로 삭제합니다.
#### `--yes -y`
확인 메시지를 표시하지 않고 데이터 컨트롤러를 삭제합니다.
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
메트릭, 로그 또는 사용량을 파일로 내보냅니다.
```bash
az arcdata dc export --type -t 
                     --path -p  
                     
--k8s-namespace -k  
                     
[--force -f]  
                     
[--use-k8s]
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--type -t`
내보낼 데이터 형식입니다. 옵션: logs, metrics, usage.
#### `--path -p`
내보낼 파일의 파일 이름을 포함하는 전체 경로 또는 상대 경로입니다.
#### `--k8s-namespace -k`
데이터 컨트롤러가 있는 Kubernetes 네임스페이스입니다.
### <a name="optional-parameters"></a>선택적 매개 변수
#### `--force -f`
출력 파일을 강제로 만듭니다. 같은 경로에 있는 기존 파일을 덮어씁니다.
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
데이터 컨트롤러에서 내보낸 데이터 파일을 Azure로 업로드합니다.
```bash
az arcdata dc upload --path -p 
                     
```
### <a name="required-parameters"></a>필수 매개 변수
#### `--path -p`
업로드할 파일의 파일 이름을 포함하는 전체 경로 또는 상대 경로입니다.
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
