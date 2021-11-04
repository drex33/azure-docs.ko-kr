---
title: CLI(v2) 릴리스 정보
titleSuffix: Azure Machine Learning
description: Azure Machine Learning CLI의 최신 업데이트에 대해 알아보기(v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: minxia
author: mx-iao
ms.date: 11/03/2021
ms.openlocfilehash: 9619b98ad824de757273cbe38567b5f9418686a3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570509"
---
# <a name="azure-machine-learning-cli-v2-release-notes"></a>Azure Machine Learning CLI(v2) 릴리스 정보

이 문서에서는 Azure Machine Learning CLI(v2) 릴리스에 대해 알아봅니다.

__RSS 피드__: 다음 URL을 복사하여 피드 판독기에 붙여넣으면 이 페이지가 업데이트될 때 알림을 받을 수 있습니다. `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes-v2%22&locale=en-us`

## <a name="2021-10-04"></a>2021-10-04

### <a name="azure-machine-learning-cli-v2-v201a6"></a>Azure Machine Learning CLI(v2) v2.0.1a6

- `az ml workspace`
  - [업데이트된 작업 영역 YAML 스키마](reference-yaml-workspace.md)
- `az ml compute`
  - [AmlCompute](reference-yaml-compute-aml.md) 및 [Compute 인스턴스에](reference-yaml-compute-instance.md) 대한 업데이트된 YAML 스키마
  - 을 통해 레거시 AKS 연결에 대한 지원이 `az ml compute attach` 제거되었습니다. Azure Arc 지원 Kubernetes 연결은 다음 릴리스에서 지원됩니다.
- `az ml datastore`
  - [Azure Blob, Azure](reference-yaml-datastore-blob.md) [파일, Azure](reference-yaml-datastore-files.md)Data Lake Gen1 및 Azure Data Lake [Gen2](reference-yaml-datastore-data-lake-gen1.md)데이터스토어에 대한 YAML 스키마가 업데이트됨 [](reference-yaml-datastore-data-lake-gen2.md)
  - Azure Data Lake Storage Gen1 및 Gen2 데이터스토어 만들기에 대한 지원이 추가되었습니다.
- `az ml job`
  - [명령 작업](reference-yaml-job-command.md) 및 [스윕 작업에](reference-yaml-job-sweep.md) 대한 YAML 스키마 업데이트
  - 파이프라인[작업(파이프라인 작업 YAML 스키마)을](reference-yaml-job-pipeline.md)실행하기 위한 지원이 추가되었습니다.
  - 모든 작업 유형에 대한 작업 입력 리터럴 및 입력 데이터 URI에 대한 지원이 추가되었습니다.
  - 모든 작업 유형에 대한 작업 출력에 대한 지원이 추가되었습니다.
  - 식 구문을 에서 로 `{ <expression> }` `${{ <expression> }}` 변경했습니다. 자세한 내용은 [Azure ML 작업을 구성하기 위한 식 구문을 참조하세요.](reference-yaml-core-syntax.md#expression-syntax-for-configuring-azure-ml-jobs-and-components)
- `az ml environment`
  - 업데이트된 [환경 YAML 스키마](reference-yaml-environment.md)
  - Docker 빌드 컨텍스트에서 환경을 만들기 위한 지원 추가
- `az ml model`
  - 업데이트된 [모델 YAML 스키마](reference-yaml-model.md)
  - 코드 `model_format` 없는 배포 시나리오에 대한 모델에 새 속성이 추가되었습니다.
- `az ml dataset`
  - 하위 그룹의 이름이 로 `az ml data` 변경되었습니다. `az ml dataset`
  - 업데이트된 [데이터 세트 YAML 스키마](reference-yaml-dataset.md)
- `az ml component`
  - Azure `az ml component` ML 구성 요소를 관리하기 위한 명령이 추가되었습니다.
  - 명령 구성[요소(명령 구성 요소 YAML 스키마)에](reference-yaml-component-command.md)대한 지원이 추가되었습니다.
- `az ml online-endpoint`
  - `az ml endpoint` 하위 그룹은 두 개의 개별 그룹으로 분할됩니다. `az ml online-endpoint` 및 `az ml batch-endpoint`
  - [업데이트된 온라인 엔드포인트 YAML 스키마](reference-yaml-endpoint-managed-online.md)
  - 개발/테스트 시나리오에 대한 로컬 엔드포인트에 대한 지원이 추가되었습니다.
  - 로컬 엔드포인트에 대한 대화형 VSCode 디버깅 지원이 추가되었습니다(에 `--vscode-debug` 플래그 `az ml batch-endpoint create/update` 추가).
- `az ml online-deployment`
  - `az ml deployment` 하위 그룹은 두 개의 개별 그룹으로 분할됩니다. `az ml online-deployment` 및 `az ml batch-deployment`
  - [업데이트된 관리되는 온라인 배포 YAML 스키마](reference-yaml-endpoint-managed-online.md)
  - Azure Monitor 자동 크기 조정과 통합을 통해 자동 크기 조정 지원이 추가되었습니다.
  - 동일한 업데이트 작업에서 여러 온라인 배포 속성을 업데이트하기 위한 지원이 추가되었습니다.
  - 동일한 엔드포인트에서 배포에 대한 동시 작업을 수행하기 위한 지원이 추가되었습니다.
- `az ml batch-endpoint`
  - `az ml endpoint` 하위 그룹은 두 개의 개별 그룹으로 분할됩니다. `az ml online-endpoint` 및 `az ml batch-endpoint`
  - [업데이트된 일괄 처리 엔드포인트 YAML 스키마](reference-yaml-endpoint-batch.md)
  - 속성이 제거되었습니다. `traffic` 구성 가능한 기본 배포 속성으로 대체되었습니다.
  - 에 대한 입력 데이터 URI에 대한 지원이 추가되었습니다. `az ml batch-endpoint invoke`
  - VNet 수신에 대한 지원 추가(프라이빗 링크)
- `az ml batch-deployment`
  - `az ml deployment` 하위 그룹은 두 개의 개별 그룹으로 분할됩니다. `az ml online-deployment` 및 `az ml batch-deployment`
  - [업데이트된 일괄 처리 배포 YAML 스키마](reference-yaml-deployment-batch.md)

## <a name="2021-05-25"></a>2021-05-25

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Azure Machine Learning용 CLI(v2) 발표(미리 보기)

Azure CLI에 대한 `ml` 확장은 Azure Machine Learning을 위한 차세대 인터페이스입니다. 이를 통해 모델 수명 주기를 추적하는 동안 데이터 과학 확장을 가속화하는 기능을 사용하여 명령줄에서 모델을 학습하고 배포할 수 있습니다. [설치 및 시작](how-to-configure-cli.md)합니다.
