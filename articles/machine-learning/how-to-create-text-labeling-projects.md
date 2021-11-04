---
title: 텍스트 레이블 지정 프로젝트 설정
titleSuffix: Azure Machine Learning
description: 데이터 레이블 지정 도구를 사용하여 텍스트에 레이블을 지정하는 프로젝트를 만듭니다. 텍스트의 각 부분에 적용할 단일 레이블 또는 여러 레이블을 지정합니다.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: data4ml, ignite-fall-2021
ms.openlocfilehash: e3097c6b00d97287526015836c44ddcaeb08177a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468710"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>텍스트 레이블 지정 프로젝트 만들기 및 레이블 내보내기(미리 보기)

데이터 레이블 지정 프로젝트를 만들고 실행하여 Azure Machine Learning 텍스트 데이터에 레이블을 지정하는 방법을 알아봅니다.  각 텍스트 항목에 적용할 단일 레이블 또는 여러 레이블을 지정합니다.

데이터 레이블 지정 도구를 사용하여 [이미지 레이블 지정 프로젝트 를 만들](how-to-create-image-labeling-projects.md)수도 있습니다.

> [!IMPORTANT]
> 텍스트 레이블 지정은 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="text-labeling-capabilities"></a>텍스트 레이블 지정 기능

Azure Machine Learning 데이터 레이블 지정은 데이터 레이블 지정 프로젝트를 만들고, 관리하고, 모니터링하는 중앙 위치입니다.

- 데이터, 레이블 및 팀 멤버를 조정하여 레이블 지정 작업을 효율적으로 관리합니다. 
- 진행 상황을 추적하고 불완전한 레이블 지정 작업의 큐를 유지 관리합니다.
- 프로젝트를 시작 및 중지하고 레이블 지정 진행률을 제어합니다.
- 레이블이 지정한 데이터를 검토하고 Azure Machine Learning 데이터 세트로 레이블이 지정되어 있는 데이터를 내보냅니다.

> [!Important]
> 텍스트 데이터는 Azure Blob 데이터 저장소에서 사용할 수 있어야 합니다. (기존 데이터 저장소가 없는 경우 프로젝트를 만드는 동안 파일을 업로드할 수 있습니다.)

텍스트 데이터에 사용할 수 있는 데이터 형식:

* **.txt:** 각 파일은 레이블이 지정될 하나의 항목을 나타냅니다.
* **.csv** 또는 **.tsv:** 각 행은 레이블 지정자에게 표시되는 하나의 항목을 나타냅니다.  행에 레이블을 지정하기 위해 레이블 지정자가 볼 수 있는 열을 결정합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>텍스트 레이블 지정 프로젝트 만들기

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. 프로젝트를 만들려면 **프로젝트 추가** 를 선택합니다. 프로젝트에 적절한 이름을 지정합니다. 나중에 프로젝트를 삭제하더라도 프로젝트 이름을 다시 사용할 수 없습니다.

1. **텍스트** 를 선택하여 텍스트 레이블 지정 프로젝트를 만듭니다.

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="텍스트 레이블 지정을 위한 레이블 지정 프로젝트 만들기":::

    * 각 텍스트에 레이블 세트의 *단일 레이블* 만 적용하려면 프로젝트에 대해 **텍스트 분류 다중 클래스(미리 보기)** 를 선택합니다.
    * 각 텍스트에 레이블 세트의 레이블을 *하나 이상* 적용하려면 프로젝트에 대해 **텍스트 분류 다중 레이블(미리 보기)** 을 선택합니다.

1. 계속할 준비가 되면 **다음** 을 선택합니다.

## <a name="add-workforce-optional"></a>인력 추가(선택 사항)

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>레이블을 지정할 데이터 지정

데이터가 포함된 데이터 세트를 이미 만든 경우 **기존 데이터 세트 선택** 드롭다운 목록에서 해당 데이터 세트를 선택합니다. 또는 **데이터 세트 만들기** 를 선택하여 기존 Azure 데이터 저장소를 사용하거나 로컬 파일을 업로드합니다.

> [!NOTE]
> 프로젝트에는 500,000개가 넘는 파일을 포함할 수 없습니다.  데이터 세트에 더 많은 데이터가 있는 경우 처음 500,000개의 파일만 로드됩니다.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Azure 데이터 저장소에서 데이터 세트 만들기

대부분의 경우 로컬 파일만 업로드해도 괜찮습니다. 그러나 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)는 대량의 데이터를 더 빠르고 강력하게 전송할 수 있는 방법을 제공합니다. 파일을 이동하는 기본 방법으로 Storage Explorer를 사용하는 것이 좋습니다.

Azure Blob 스토리지에 이미 저장한 데이터에서 데이터 세트를 만들려면 다음을 수행합니다.

1. **데이터 세트 만들기** > **데이터 저장소에서** 를 차례로 선택합니다.
1. **이름** 을 데이터 세트에 할당합니다.
1. 데이터 **세트 유형** 를 선택합니다.
    * 각 행에 응답이 포함된 .csv 또는 .tsv 파일을 사용하는 경우 **테이블 형식을** 선택합니다.
    * 각 응답에 대해 별도의 .txt 파일을 사용하는 **경우** 파일을 선택합니다.
1. (선택 사항) 데이터 세트에 대한 설명을 제공합니다.
1. **다음** 을 선택합니다.
1. 데이터 저장소를 선택합니다.
1. 데이터가 Blob 스토리지 내의 하위 폴더에 있으면 **찾아보기** 를 선택하여 경로를 선택합니다.
    * 선택한 경로의 하위 폴더에 있는 모든 파일을 포함하려면 "/**"를 경로에 추가합니다.
    * 현재 컨테이너 및 해당 하위 폴더에 있는 모든 데이터를 포함하려면 "* */* .*"를 추가합니다.
1. **다음** 을 선택합니다.
1. 세부 정보를 확인합니다. **뒤로** 를 선택하여 설정을 수정하거나, **만들기** 를 선택하여 데이터 세트를 만듭니다.

### <a name="create-a-dataset-from-uploaded-data"></a>업로드된 데이터에서 데이터 세트 만들기

데이터를 직접 업로드하려면 다음을 수행합니다.

1. **데이터 세트 만들기** > **로컬 파일에서** 를 차례로 선택합니다.
1. **이름** 을 데이터 세트에 할당합니다.
1. **데이터 세트 형식** 을 선택합니다.
    * 각 행이 응답인 .csv 또는 .tsv 파일을 사용하는 경우 **테이블 형식을** 선택합니다.
    * 각 응답에 대해 별도의 .txt 파일을 사용하는 **경우** 파일을 선택합니다.
1. (선택 사항) 데이터 세트에 대한 설명을 제공합니다.
1. **다음** 을 선택합니다.
1. (선택 사항) 데이터 저장소를 선택하거나 만듭니다. 또는 기본값을 유지하여 Machine Learning 작업 영역의 기본 Blob 저장소("workspaceblobstore")에 업로드합니다.
1. **업로드** 선택하여 업로드할 로컬 파일 또는 폴더를 선택합니다.
1. **다음** 을 선택합니다.
1. .csv 또는 .tsv 파일을 업로드하는 경우:
    * 설정 및 미리 보기를 확인하고 **다음을** 선택합니다.
    * 해당 행을 분류할 때 레이블 지정자가 보려는 텍스트의 모든 열을 포함합니다.  ML 지원 레이블 지정을 사용하는 경우 숫자 열을 추가하면 ML 지원 모델이 저하될 수 있습니다.
    * **다음** 을 선택합니다.
1.  세부 정보를 확인합니다. **뒤로** 를 선택하여 설정을 수정하거나, **만들기** 를 선택하여 데이터 세트를 만듭니다.


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> 증분 새로 고침 구성

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

> [!NOTE]
> 테이블 형식(.csv 또는 .tsv) 데이터 세트 입력을 사용하는 프로젝트에는 증분 새로 고침을 사용할 수 없습니다.

## <a name="specify-label-classes"></a>레이블 클래스 지정

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>텍스트 레이블 지정 작업 설명

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> 레이블 지정자는 1-9 숫자 키를 사용하여 처음 9개의 레이블을 선택할 수 있습니다.

## <a name="use-ml-assisted-data-labeling"></a>ML 지원 데이터 레이블 지정 사용

**ML 지원 레이블 지정** 페이지를 사용하면 자동 기계 학습 모델을 트리거하여 레이블 지정 작업을 가속화할 수 있습니다. ML 지원 레이블 지정은 파일(.txt) 및 테이블 형식(.csv) 텍스트 데이터 입력 모두에 사용할 수 있습니다.

ML **지원 레이블 지정을** 사용하려면:

* **ML 지원 레이블 지정 사용을** 선택합니다.
* 프로젝트의 **데이터 세트 언어를** 선택합니다. [TextDNNLanguages 클래스에서](/python/api/azureml-automl-core/azureml.automl.core.constants.textdnnlanguages?view=azure-ml-py&preserve-view=true) 지원하는 모든 언어가 이 목록에 있습니다.
* 사용할 컴퓨팅 대상을 지정합니다. 작업 영역에 컴퓨팅 클러스터가 없으면 컴퓨팅 클러스터가 만들어지고 작업 영역에 추가됩니다.   클러스터는 최소 0개의 노드로 만들어지므로 사용하지 않을 때 비용이 발생하지 않습니다.

### <a name="how-does-ml-assisted-labeling-work"></a>ML 지원 레이블 지정은 어떻게 작동하나요?

레이블 지정 프로젝트가 시작될 때 잠재적인 편차를 줄이기 위해 항목이 임의의 순서로 섞입니다. 그러나 데이터 세트에 있는 모든 편차는 학습된 모델에 반영됩니다. 예를 들어 항목의 80%가 단일 클래스인 경우 모델을 학습시키는 데 사용되는 데이터의 약 80%가 해당 클래스의 데이터입니다. 

ML-assist에서 사용하는 텍스트 DNN 모델을 학습하기 위해 학습 예제당 입력 텍스트는 문서의 처음 128개 단어로 제한됩니다.  테이블 형식 입력의 경우 이 제한을 적용하기 전에 모든 텍스트 열이 먼저 결합됩니다. 이는 모델 학습이 적시에 완료되도록 하기 위해 적용된 실제 제한입니다. 문서의 실제 텍스트(파일 입력의 경우) 또는 텍스트 열 집합(테이블 형식 입력의 경우)은 128개 단어를 초과할 수 있습니다.  이 제한은 학습 프로세스 중에 모델에서 내부적으로 활용하는 것에만 해당합니다.

지원 레이블 지정을 시작하는 데 필요한 레이블이 있는 항목의 정확한 수는 고정된 숫자가 아닙니다. 레이블 클래스 수 및 레이블 분포를 비롯한 여러 요인에 따라 레이블 지정 프로젝트마다 크게 달라질 수 있습니다.

최종 레이블에서 여전히 레이블 지정자의 입력을 사용하므로 이 기술을 *사용자 참여형* 레이블 지정이라고 하는 경우도 있습니다.

> [!NOTE]
> ML 지원 데이터 레이블 지정은 [가상 네트워크](how-to-network-security-overview.md) 뒤에서 보호되는 기본 스토리지 계정을 지원하지 않습니다. ML 지원 데이터 레이블 지정에는 기본이 아닌 스토리지 계정을 사용해야 합니다. 기본이 아닌 스토리지 계정은 가상 네트워크 뒤에서 보호할 수 있습니다.

### <a name="pre-labeling"></a>사전 레이블 지정

학습을 위해 충분한 레이블을 제출한 후에는 학습된 모델을 사용하여 태그를 예측합니다. 이제 레이블 지정자에는 이미 각 항목에 있는 예측된 레이블이 포함된 페이지가 표시됩니다. 그런 다음, 페이지를 제출하기 전에 이러한 예측을 검토하고 레이블이 잘못 지정된 항목을 수정해야 합니다.  

기계 학습 모델이 수동으로 레이블이 지정된 데이터에 대해 학습되면 수동으로 레이블이 지정된 항목의 테스트 세트에 대해 모델을 평가하여 서로 다른 신뢰도 임계값으로 정확도를 결정합니다. 이 평가 프로세스는 사전 레이블을 표시할 만큼 충분히 정확한 모델의 신뢰도 임계값을 결정하는 데 사용됩니다. 그런 다음, 레이블이 없는 데이터에 대해 모델을 평가합니다. 이 임계값보다 더 확실한 예측이 있는 항목은 사전 레이블 지정에 사용됩니다.

## <a name="initialize-the-text-labeling-project"></a>텍스트 레이블 지정 프로젝트 초기화

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>프로젝트 실행 및 모니터링

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>대시보드


**대시보드** 탭에는 레이블 지정 작업의 진행률이 표시됩니다.

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="텍스트 데이터 레이블 지정 대시보드":::


진행률 차트는 레이블이 지정되었거나 건너뛰거나 검토가 필요하거나 아직 완료되지 않은 항목 수를 표시합니다.  차트를 마우스로 가리키면 각 섹션의 항목 수가 표시됩니다.

가운데 섹션에는 아직 할당되지 않은 작업 큐가 표시됩니다. ML 지원 레이블 지정이 켜진 경우 미리 레이블이 지정된 항목의 수도 표시됩니다.


오른쪽에는 완료된 작업에 대한 레이블 분포가 있습니다.  일부 프로젝트 형식은 항목에 여러 레이블이 있을 수 있으며, 이런 경우 레이블의 총 합계가 총 합계 항목보다 클 수 있습니다.

### <a name="data-tab"></a>데이터 탭

**데이터** 탭에서 데이터 세트를 보고, 레이블이 지정된 데이터를 검토할 수 있습니다. 레이블이 있는 데이터를 스크롤하여 레이블을 확인합니다. 잘못된 레이블이 지정된 데이터가 표시되면 이를 선택하고 **거부** 를 선택합니다. 그러면 해당 레이블이 제거되고, 해당 데이터를 레이블이 지정되지 않은 큐에 다시 넣습니다.

### <a name="details-tab"></a>자세히 탭

프로젝트에 대한 세부 정보를 봅니다.  이 탭에서는 다음을 수행할 수 있습니다.

* 프로젝트 세부 정보 및 입력 데이터 세트 보기
* 증분 새로 고침을 사용하도록 설정
* 프로젝트에 레이블이 지정된 출력을 저장하는 데 사용되는 스토리지 컨테이너의 세부 정보 보기
* 프로젝트에 레이블 추가
* 레이블에 지정하는 지침 편집

### <a name="access-for-labelers"></a>레이블 지정자에 대한 액세스

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>프로젝트에 새 레이블 클래스 추가

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>레이블 내보내기
 
레이블 지정 프로젝트의 **프로젝트 세부 정보** 페이지에서 **내보내기** 단추를 사용합니다. 레이블 데이터는 Machine Learning 실험을 위해 언제든지 내보낼 수 있습니다.

다음을 내보낼 수 있습니다.

* CSV 파일. CSV 파일은 *Labeling/export/csv* 내의 폴더에 있는 Azure Machine Learning 작업 영역의 기본 Blob 저장소에 생성됩니다. 
* [레이블이 있는 Azure Machine Learning 데이터 세트](how-to-use-labeled-dataset.md) 

Machine Learning의 **데이터 세트** 섹션에서 내보낸 Azure Machine Learning 데이터 세트에 액세스합니다. 데이터 세트 세부 정보 페이지에는 Python에서 레이블에 액세스할 수 있는 샘플 코드도 있습니다.

![내보낸 데이터 세트](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>다음 단계

* [텍스트에 태그를 붙이는 방법](how-to-label-data.md#label-text)
