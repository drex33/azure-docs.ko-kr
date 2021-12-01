---
title: CLI(v2) 릴리스 정보
titleSuffix: Azure Machine Learning
description: Azure Machine Learning CLI (v2)의 최신 업데이트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: minxia
author: mx-iao
ms.date: 11/03/2021
ms.openlocfilehash: b75afa8752ba9223a4575835aa9d8c04b6264f19
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133357660"
---
# <a name="azure-machine-learning-cli-v2-release-notes"></a>Azure Machine Learning CLI (v2) 릴리스 정보

이 문서에서는 Azure Machine Learning CLI (v2) 릴리스에 대해 알아봅니다.

__RSS 피드__: 다음 URL을 복사하여 피드 판독기에 붙여넣으면 이 페이지가 업데이트될 때 알림을 받을 수 있습니다. `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes-v2%22&locale=en-us`

## <a name="2021-10-04"></a>2021-10-04

### <a name="azure-machine-learning-cli-v2-v202"></a>Azure Machine Learning CLI (v2) v 2.0.2

- `az ml workspace`
  - 업데이트 된 [작업 영역 YAML 스키마](reference-yaml-workspace.md)
- `az ml compute`
  - [Amlcompute](reference-yaml-compute-aml.md) 및 [계산 인스턴스에](reference-yaml-compute-instance.md) 대 한 yaml 스키마 업데이트
  - 을 통해 레거시 AKS 연결에 대 한 지원이 제거 `az ml compute attach` 되었습니다. Azure Arc-enabled Kubernetes attach는 다음 릴리스에서 지원 될 예정입니다.
- `az ml datastore`
  - [Azure blob](reference-yaml-datastore-blob.md), [azure file](reference-yaml-datastore-files.md), [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md)및 [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) 데이터 저장소의 업데이트 된 yaml 스키마
  - Azure Data Lake Storage Gen1 및 Gen2 데이터 저장소 만들기에 대 한 지원이 추가 됨
- `az ml job`
  - [명령 작업](reference-yaml-job-command.md) 및 [스윕 작업](reference-yaml-job-sweep.md) 의 업데이트 된 yaml 스키마
  - 파이프라인 작업 실행에 대 한 지원 추가 ([파이프라인 작업 YAML 스키마](reference-yaml-job-pipeline.md))
  - 모든 작업 유형에 대 한 작업 입력 리터럴 및 입력 데이터 Uri에 대 한 지원이 추가 됨
  - 모든 작업 유형에 대 한 작업 출력에 대 한 지원 추가
  - 식 구문을에서로 변경 `{ <expression> }` 했습니다 `${{ <expression> }}` . 자세한 내용은 [Azure ML 작업을 구성 하기 위한 식 구문](reference-yaml-core-syntax.md#expression-syntax-for-configuring-azure-ml-jobs-and-components) 을 참조 하세요.
- `az ml environment`
  - 업데이트 된 [환경 YAML 스키마](reference-yaml-environment.md)
  - Docker 빌드 컨텍스트에서 환경 만들기에 대 한 지원이 추가 됨
- `az ml model`
  - 업데이트 된 [모델 YAML 스키마](reference-yaml-model.md)
  - `model_format`코드 없는 배포 시나리오에 대 한 새 속성을 모델에 추가 했습니다.
- `az ml dataset`
  - 이름이 바뀐 하위 `az ml data` 그룹 `az ml dataset`
  - 업데이트 된 [데이터 집합 YAML 스키마](reference-yaml-dataset.md)
- `az ml component`
  - `az ml component`Azure ML 구성 요소를 관리 하기 위한 명령이 추가 되었습니다.
  - 명령 구성 요소에 대 한 지원 추가 ([명령 구성 요소 YAML 스키마](reference-yaml-component-command.md))
- `az ml online-endpoint`
  - `az ml endpoint` 두 개의 개별 그룹으로 분할 되는 하위 그룹: `az ml online-endpoint` 및 `az ml batch-endpoint`
  - [온라인 끝점 YAML 스키마](reference-yaml-endpoint-managed-online.md) 업데이트
  - 개발/테스트 시나리오에 대 한 로컬 끝점에 대 한 지원 추가
  - 로컬 끝점에 대 한 대화형 VSCode 디버깅 지원 추가 ( `--vscode-debug` 에 플래그를 추가 `az ml batch-endpoint create/update` )
- `az ml online-deployment`
  - `az ml deployment` 두 개의 개별 그룹으로 분할 되는 하위 그룹: `az ml online-deployment` 및 `az ml batch-deployment`
  - 업데이트 된 [관리 되는 온라인 배포 YAML 스키마](reference-yaml-endpoint-managed-online.md)
  - Azure Monitor 자동 크기 조정과의 통합을 통해 자동 크기 조정 지원이 추가 됨
  - 동일한 업데이트 작업에서 여러 온라인 배포 속성 업데이트에 대 한 지원이 추가 됨
  - 동일한 끝점에서 배포에 대 한 동시 작업을 수행 하기 위한 지원 추가
- `az ml batch-endpoint`
  - `az ml endpoint` 두 개의 개별 그룹으로 분할 되는 하위 그룹: `az ml online-endpoint` 및 `az ml batch-endpoint`
  - 업데이트 된 [일괄 처리 끝점 YAML 스키마](reference-yaml-endpoint-batch.md)
  - 속성이 제거 되었습니다 `traffic` . 구성 가능한 기본 배포 속성으로 바뀌었습니다.
  - 에 대 한 입력 데이터 Uri에 대 한 지원 추가 `az ml batch-endpoint invoke`
  - VNet 수신 (개인 링크)에 대 한 지원 추가 됨
- `az ml batch-deployment`
  - `az ml deployment` 두 개의 개별 그룹으로 분할 되는 하위 그룹: `az ml online-deployment` 및 `az ml batch-deployment`
  - 업데이트 된 [batch 배포 YAML 스키마](reference-yaml-deployment-batch.md)

## <a name="2021-05-25"></a>2021-05-25

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Azure Machine Learning용 CLI(v2) 발표(미리 보기)

Azure CLI에 대한 `ml` 확장은 Azure Machine Learning을 위한 차세대 인터페이스입니다. 이를 통해 모델 수명 주기를 추적하는 동안 데이터 과학 확장을 가속화하는 기능을 사용하여 명령줄에서 모델을 학습하고 배포할 수 있습니다. [설치 및 시작](how-to-configure-cli.md)합니다.
