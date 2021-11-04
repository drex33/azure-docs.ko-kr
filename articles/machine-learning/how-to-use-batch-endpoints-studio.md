---
title: 스튜디오에서 일괄 처리 엔드포인트를 사용하는 방법
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Machine Learning 스튜디오에서 일괄 처리 엔드포인트를 만드는 방법을 알아봅니다. 일괄 처리 엔드포인트는 대량 데이터를 지속해서 일괄 채점할 때 사용됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.custom: how-to, studio, managed-batch-endpoints
ms.openlocfilehash: d9d6287903466283e0b94e246a17a75a77929c8a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560590"
---
# <a name="how-to-use-batch-endpoints-preview-in-azure-machine-learning-studio"></a>Azure Machine Learning 스튜디오에서 일괄 처리 엔드포인트(미리 보기)를 사용하는 방법

이 문서에서는 일괄 처리 엔드포인트(미리 보기)를 사용하여 [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 일괄 채점하는 방법을 알아봅니다. 자세한 내용은 [Azure Machine Learning 엔드포인트(미리 보기)란?](concept-endpoints.md)을 참조하세요.

이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * MLflow 모델에 대한 코드 없는 환경을 사용하여 일괄 처리 엔드포인트 만들기
> * 일괄 처리 엔드포인트 세부 정보 확인
> * 일괄 채점 작업 시작
> * Azure Machine Learning 스튜디오에서의 일괄 처리 엔드포인트 기능 개요

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>전제 조건

* Azure 구독 - Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

* 예제 리포지토리 - [AzureML 예제 리포지토리](https://github.com/Azure/azureml-examples)를 복제합니다. 이 문서에서는 `/cli/endpoints/batch`의 자산을 사용합니다.

* 일괄 채점 워크플로를 실행할 수 있는 컴퓨팅 대상. 컴퓨팅 대상을 만드는 방법에 대한 자세한 내용은 [Azure Machine Learning 스튜디오에서 컴퓨팅 대상 만들기](how-to-create-attach-compute-studio.md)를 참조하세요.

* 기계 학습 모델을 등록하세요.

## <a name="create-a-batch-endpoint"></a>일괄 처리 엔드포인트 만들기

Azure Machine Learning 스튜디오에서 일괄 처리 엔드포인트를 만드는 방법에는 두 가지가 있습니다.

* **엔드포인트** 페이지에서 **일괄 처리 엔드포인트** 를 선택한 다음, **+ 만들기** 를 선택합니다. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-endpoints.png" alt-text="엔드포인트에서 일괄 처리 엔드포인트를/배포 만들기 페이지의 스크린샷":::

또는

* **모델** 페이지에서 배포하려는 모델 선택 후 **일괄 처리 엔드포인트에 배포(미리 보기)** 를 선택합니다.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/models-page-deployment.png" alt-text="모델에서 일괄 처리 엔드포인트/배포 만들기 페이지의 스크린샷":::

> [!TIP]
> MLflow 모델을 사용하는 경우 코드 없는 일괄 처리 엔드포인트 만들기를 사용할 수 있습니다. 즉, 채점 스크립트 및 환경을 준비할 필요가 없으며 둘 다 자동 생성될 수 있습니다. 자세한 내용은 [MLflow 및 Azure Machine Learning을 통해 ML 모델 학습 및 추적(미리 보기)](how-to-use-mlflow.md)를 참조하세요.
> 
> :::image type="content" source="media/how-to-use-batch-endpoints-studio/mlflow-model-wizard.png" alt-text="MLflow 모델 배포 스크린샷":::

마법사의 모든 단계를 완료하여 일괄 처리 엔드포인트를 및 배포를 만듭니다.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/review-batch-wizard.png" alt-text="일괄 처리 엔드포인트/배포 검토 화면의 스크린샷":::

## <a name="check-batch-endpoint-details"></a>일괄 처리 엔드포인트 세부 정보 확인

일괄 처리 엔드포인트를 만든 후 **엔드포인트** 페이지에서 선택하여 세부 정보를 확인하세요.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/batch-endpoint-details.png" alt-text="일괄 처리 엔드포인트 및 배포 세부 정보의 스크린샷":::

## <a name="start-a-batch-scoring-job"></a>일괄 채점 작업 시작

일괄 처리 채점 작업은 오프라인 작업으로 실행됩니다. 기본적으로 일괄 처리 채점 출력은 Blob 스토리지에 저장됩니다. 또한 출력 위치를 구성하고 최상의 성능을 얻기 위해 일부 설정을 덮어쓸 수도 있습니다.

1. **작업 만들기** 를 선택합니다.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-job.png" alt-text="일괄 채점을 시작하는 작업 만들기 옵션의 스크린샷":::

1. 드롭다운으로부터 작업을 제출하는 동안 기본 배포를 업데이트할 수 있습니다.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/job-setting-batch-scoring.png" alt-text="배포를 사용하여 일괄 작업을 제출하는 스크린샷":::

### <a name="overwrite-settings"></a>설정 덮어쓰기

일부 설정은 일괄 채점 작업을 시작할 때 덮어쓰일 수 있습니다. 예를 들어 컴퓨팅 리소스를 더 효율적으로 사용하거나 성능을 향상하기 위해 설정을 덮어쓸 수 있습니다. 설정을 재정의하려면 __Override deployment settings__(배포 설정 재정의)를 선택하고 설정을 제공합니다. 자세한 내용은 [일괄 처리 엔드포인트 사용](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings)을 참조하세요.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/overwrite-setting.png" alt-text="일괄 작업을 시작할 때 덮어쓰기 설정의 스크린샷":::

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>다른 입력 옵션을 사용하여 일괄 채점 작업 시작

Azure Machine Learning 스튜디오에서 데이터 입력을 지정하는 두 가지 옵션이 있습니다.

* **등록된 데이터 세트** 사용:

    > [!NOTE]
    > 미리 보기 중에는 FileDataset만 지원됩니다. 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-dataset-for-job.png" alt-text="등록된 데이터 세트를 입력 옵션으로 선택하는 스크린샷":::

또는

* **데이터 저장소** 사용:

    AML 등록된 데이터 저장소를 지정하거나 데이터를 공개적으로 사용할 수 있는 경우 퍼블릭 경로를 지정합니다.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-datastore-job.png" alt-text="데이터 저장소를 입력 옵션으로 선택하는 스크린샷":::

### <a name="configure-the-output-location"></a>출력 위치 구성

기본적으로 일괄 채점 결과는 작업 영역에 대한 기본 Blob 저장소에 저장됩니다. 결과는 작업 이름 뒤에 이름이 지정된 폴더(시스템에서 생성된 GUID)에 있습니다.

작업을 시작할 때 Blob 저장소 및 출력 경로를 제공하여 결과가 저장되는 위치를 변경합니다.

> [!IMPORTANT]
> 고유한 출력 위치를 사용해야 합니다. 출력 파일이 있으면 일괄 처리 채점 작업이 실패합니다. 

:::image type="content" source="media/how-to-use-batch-endpoints-studio/configure-output-location.png" alt-text="선택적으로 출력 위치를 구성하는 스크린샷":::

### <a name="summary-of-all-submitted-jobs"></a>제출된 모든 작업 요약

엔드포인트에 대해 제출된 모든 작업의 요약을 보려면 엔드포인트를 선택한 다음, **실행** 탭을 선택합니다.

:::image type="content" source="media/how-to-use-batch-endpoints-studio/summary-jobs.png" alt-text="일괄 처리 엔드포인트에 제출된 작업 요약의 스크린샷":::
## <a name="check-batch-scoring-results"></a>일괄 처리 채점 결과 확인

채점 결과를 확인하는 방법에 대한 내용은 [일괄 처리 엔드포인트 사용](how-to-use-batch-endpoint.md#check-batch-scoring-results)을 참조하세요.

## <a name="add-a-deployment-to-an-existing-batch-endpoint"></a>기존 일괄 처리 엔드포인트에 배포 추가

Azure Machine Learning 스튜디오에서 기존 일괄 처리 엔드포인트에 배포를 추가하는 방법에는 두 가지가 있습니다.

* **엔드포인트** 페이지에서 새 배포를 추가할 일괄 처리 엔드포인트를 선택합니다. **+ 배포 추가** 를 선택하고 새 배포를 추가할 마법사를 완성합니다.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-option.png" alt-text="새 배포 추가 옵션의 스크린샷":::

또는

* **모델** 페이지에서 배포할 모델을 선택합니다. 그리고 드롭다운에서 **일괄 처리 엔드포인트에 배포(미리 보기)** 옵션을 선택합니다. 마법사의 **엔드포인트** 화면에서 **기존** 을 선택합니다. 마법사를 완료하여 새 배포를 추가합니다.

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-models-page.png" alt-text="새 배포를 추가할 기존 일괄 처리 엔드포인트를 선택하는 스크린샷":::

## <a name="update-the-default-deployment"></a>기본 배포 업데이트

엔드포인트에 여러 배포가 있는 경우 배포 중 하나가 ‘기본값’입니다. 기본 배포는 엔드포인트에 대한 트래픽의 100%를 수신합니다. 기본 배포를 변경하려면 다음 단계를 수행합니다.

1. **엔드포인트** 페이지에서 엔드포인트를 선택합니다.
1. **기본 배포 업데이트** 를 선택합니다. **세부 정보** 탭에서 기본값으로 설정하려는 배포를 선택한 후 **업데이트** 를 선택합니다.
    :::image type="content" source="media/how-to-use-batch-endpoints-studio/update-default-deployment.png" alt-text="기본 배포 업데이트의 스크린샷":::

## <a name="delete-batch-endpoint-and-deployments"></a>일괄 처리 엔드포인트 및 배포 삭제

**엔드포인트** 를 삭제하려면 **엔드포인트** 페이지에서 해당 엔드포인트를 선택한 다음, 삭제를 선택합니다.

> [!WARNING]
> 엔드포인트를 삭제하면 해당 엔드포인트에 대한 배포도 모두 삭제됩니다.

**엔드포인트** 를 삭제하려면 **엔드포인트** 페이지에서 해당 엔드포인트를 선택하고, 배포를 선택한 뒤 삭제를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 일괄 처리 엔드포인트를 만들고 호출하는 방법을 배웠습니다. Azure Machine Learning에 대한 자세한 내용은 다음 문서를 참조하세요.

* [일괄 처리 엔드포인트 문제 해결](how-to-troubleshoot-batch-endpoints.md)
* [관리형 온라인 엔드포인트(미리 보기)를 통해 기계 학습 모델 배포 및 채점](how-to-deploy-managed-online-endpoints.md)
