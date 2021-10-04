---
title: 작업 영역 데이터 내보내기 또는 삭제
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 스튜디오, CLI, SDK, 인증된 REST API를 사용하여 작업 영역을 내보내거나 삭제하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
author: lobrien
ms.author: laobri
ms.date: 08/05/2021
ms.topic: how-to
ms.openlocfilehash: 519494db9a1bb06e43dc60ffed998b800555a887
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426210"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning 서비스 작업 영역 데이터 내보내기 또는 삭제

Azure Machine Learning에서 포털의 그래픽 인터페이스 또는 Python SDK를 사용하여 작업 영역 데이터를 내보내거나 삭제할 수 있습니다. 이 문서에서는 이 두 가지 옵션에 대해 설명합니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>작업 영역 데이터 제어

Azure Machine Learning에 의해 저장된 산출 데이터는 내보내고 삭제할 수 있습니다. Azure Machine Learning 스튜디오, CLI, SDK를 사용하여 내보내고 삭제하면 됩니다. 원격 분석 데이터는 Azure 개인 정보 보호 포털을 통해 액세스할 수 있습니다. 

Azure Machine Learning에서 개인 데이터는 실행 기록 문서의 사용자 정보로 구성됩니다. 

## <a name="delete-high-level-resources-using-the-portal"></a>포털을 사용하여 상위 수준 리소스 삭제

작업 영역을 만들 때 Azure는 리소스 그룹 내에 여러 리소스를 만듭니다.

- 작업 영역 자체
- 스토리지 계정
- 컨테이너 레지스트리
- Application Insights 인스턴스
- 키 자격 증명 모음

해당 리소스는 목록에서 선택하고 **삭제** 를 선택하여 삭제할 수 있습니다. 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="삭제 아이콘이 강조 표시된 포털 스크린샷":::

개인 사용자 정보를 포함할 수 있는 실행 기록 문서는 Blob Storage의 스토리지 계정(`/azureml`의 하위 폴더)에 저장됩니다. 포털에서 데이터를 다운로드하고 삭제할 수 있습니다.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="포털 내 스토리지 계정의 azureml 디렉터리 스크린샷":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Azure Machine Learning 스튜디오를 사용하여 기계 학습 리소스 내보내기 및 삭제

Azure Machine Learning 스튜디오에서는 Notebook, 데이터 세트, 모델 및 실험 등의 기계 학습 리소스에 대한 통합 보기를 제공합니다. Azure Machine Learning 스튜디오는 데이터 및 실험 레코드를 보존한다는 점을 강조합니다. 파이프라인 및 컴퓨팅 리소스와 같은 계산 리소스는 브라우저를 사용하여 삭제할 수 있습니다. 리소스의 경우 해당 리소스로 이동하고 **삭제** 를 선택합니다. 

데이터 세트의 등록을 취소하고 실험을 보관할 수 있지만 해당 작업은 데이터를 삭제하지 않습니다. 데이터를 완전히 제거하려면 데이터 세트 및 실험 데이터를 스토리지 수준에서 삭제해야 합니다. 이전에 설명한 대로 스토리지 수준에서의 삭제 작업은 포털을 사용하여 이루어집니다. 스튜디오에서 개별 실행을 직접 삭제할 수 있습니다. 실행을 삭제하면 실행의 데이터가 삭제됩니다. 

> [!NOTE]
> 데이터 세트의 등록을 취소하기 전에 해당 **데이터 원본** 링크를 사용하여 삭제할 특정 데이터 URL을 찾습니다. 

스튜디오를 사용하는 경우, 실험을 실행하여 학습 아티팩트를 다운로드할 수 있습니다. 관심 있는 **실험** 및 **실행** 을 선택합니다. **출력 + 로그** 를 선택하고 다운로드하려는 특정 아티팩트로 이동합니다. **...** 및 **다운로드** 를 선택합니다.

**모델** 로 이동하고 **다운로드** 를 선택하여 등록된 모델을 다운로드할 수 있습니다. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="다운로드 옵션이 강조 표시된 스튜디오 모델 페이지 스크린샷":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Python SDK를 사용하여 리소스 내보내기 및 삭제

다음을 사용하여 특정 실행의 출력을 다운로드할 수 있습니다. 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Python SDK를 사용하여 다음 기계 학습 리소스를 삭제할 수 있습니다. 

| 형식 | 함수 호출 | 메모 | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace#delete-delete-dependent-resources-false--no-wait-false-) | `delete-dependent-resources`를 사용하여 하위 항목까지 삭제 |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29#delete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget#delete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29) | |