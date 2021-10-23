---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: 83671800c0068ad606b6dbdad7448d55a1aea8cf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130287504"
---
다음 표에는 ML Studio(클래식)와 Azure Machine Learning 간에 주요 차이점이 요약되어 있습니다.

| 기능 | ML Studio(클래식) | Azure Machine Learning |
|---| --- | --- |
| 끌어서 놓기 인터페이스 | 클래식 환경 | 환경이 업데이트됨 - [Azure Machine Learning 디자이너](../articles/machine-learning/concept-designer.md)| 
| 코드 SDK | 지원되지 않음 | [Azure Machine Learning Python](/python/api/overview/azure/ml/) 및 [R](https://github.com/Azure/azureml-sdk-for-r) SDK와 완전히 통합됨 |
| 실험 | 크기 조정 가능(10GB 학습 데이터 제한) | 컴퓨팅 대상으로 크기 조정 |
| 컴퓨팅 대상 학습 | 전용 컴퓨팅 대상, CPU만 지원 | 광범위한 사용자 지정이 가능한 [컴퓨팅 대상 학습](../articles/machine-learning/concept-compute-target.md#train). GPU 및 CPU 지원 포함 | 
| 컴퓨팅 대상 배포 | 전용 웹 서비스 형식, 사용자 지정 불가능 | 광범위한 사용자 지정이 가능한 [컴퓨팅 대상 배포](../articles/machine-learning/concept-compute-target.md#deploy). GPU 및 CPU 지원 포함 |
| ML 파이프라인 | 지원되지 않음 | 유연한 모듈식 [파이프라인](../articles/machine-learning/concept-ml-pipelines.md)을 빌드하여 워크플로 자동화 |
| MLOps | 기본 모델 관리 및 배포, CPU 전용 배포 | 엔터티 버전 관리(모델, 데이터, 워크플로), 워크플로 자동화, CICD 도구와의 통합, CPU 및 GPU 배포 [등](../articles/machine-learning/concept-model-management-and-deployment.md) |
| 모델 형식 | 전용 형식, Studio(클래식)만 해당 | 학습 작업 유형에 따라 지원되는 여러 형식 |
| 자동화된 모델 학습 및 하이퍼 매개변수 튜닝 |  지원되지 않음 | [지원됨](../articles/machine-learning/concept-automated-ml.md)이라는 의미입니다. 코드 우선 및 코드 없음 옵션. | 
| 데이터 드리프트 검색 | 지원되지 않음 | [지원됨](../articles/machine-learning/how-to-monitor-datasets.md) |
| 데이터 레이블 지정 프로젝트 | 지원되지 않음 | [지원됨](../articles/machine-learning/how-to-create-image-labeling-projects.md) |
| 역할 기반 Access Control(RBAC) | 기여자 및 소유자 역할만 | [유연한 역할 정의 및 RBAC 제어](../articles/machine-learning/how-to-assign-roles.md) |
| AI 갤러리 | 지원됨([https://gallery.azure.ai/](https://gallery.azure.ai/)) | 지원되지 않음 <br><br> [샘플 Python SDK Notebook](https://github.com/Azure/MachineLearningNotebooks)에서 알아보세요. |