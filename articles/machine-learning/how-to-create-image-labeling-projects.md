---
title: 이미지 레이블 지정 프로젝트 설정
titleSuffix: Azure Machine Learning
description: 데이터 레이블 지정 도구를 사용하여 이미지에 레이블을 지정하는 프로젝트를 만듭니다. 작업에 도움이 되도록 ML 지원 레이블 지정 또는 루프 레이블 지정의 사용자 지정을 사용하도록 설정합니다.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: b8681974510b6d3fbee74248aab95b33de443109
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003821"
---
# <a name="create-an-image-labeling-project-and-export-labels"></a>이미지 레이블 지정 프로젝트 만들기 및 레이블 내보내기

데이터 레이블 지정 프로젝트를 만들고 실행하여 Azure Machine Learning 이미지에 레이블을 지정하는 방법을 알아봅니다.  기계 학습 지원 데이터 레이블 지정 또는 루프 레이블이 지정된 사용자를 사용하여 작업을 지원합니다.

분류, 개체 검색(경계 상자) 또는 인스턴스 구분(다각형)에 대한 레이블을 설정합니다.

데이터 레이블 지정 도구를 사용하여 [텍스트 레이블 지정 프로젝트 를 만들](how-to-create-text-labeling-projects.md)수도 있습니다.

## <a name="image-labeling-capabilities"></a>이미지 레이블 지정 기능

Azure Machine Learning 데이터 레이블 지정은 데이터 레이블 지정 프로젝트를 만들고, 관리하고, 모니터링하는 중앙 위치입니다.

- 데이터, 레이블 및 팀 멤버를 조정하여 레이블 지정 작업을 효율적으로 관리합니다.
- 진행 상황을 추적하고 불완전한 레이블 지정 작업의 큐를 유지 관리합니다.
- 프로젝트를 시작 및 중지하고 레이블 지정 진행률을 제어합니다.
- 레이블이 지정한 데이터를 검토하고 Azure Machine Learning 데이터 세트로 레이블이 지정되어 있는 데이터를 내보냅니다.

> [!Important]
> 데이터 이미지는 Azure Blob 데이터 저장소에서 사용할 수 있는 파일이어야 합니다. (기존 데이터 저장소가 없는 경우 프로젝트를 만드는 동안 파일을 업로드할 수 있습니다.)

이미지 데이터는 “.jpg”, “.jpeg”, “.png”, “.jpe”, “.jfif”, “.bmp”, “.tif”, “.tiff”, “.dcm”, “.dicom” 등의 형식을 가진 파일일 수 있습니다. 각 파일은 레이블이 지정될 항목입니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-an-image-labeling-project"></a>이미지 레이블 지정 프로젝트 만들기

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. 프로젝트를 만들려면 **프로젝트 추가** 를 선택합니다. 프로젝트에 적절한 이름을 지정합니다. 나중에 프로젝트를 삭제하더라도 프로젝트 이름을 다시 사용할 수 없습니다.

1. **이미지** 를 선택하여 이미지 레이블 지정 프로젝트를 만듭니다.

    :::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="병합 레이블 지정을 위한 레이블 지정 프로젝트 만들기":::

    * 이미지에 레이블 세트에서 단일 레이블만 적용하려면 프로젝트에 대해 **이미지 분류 다중 클래스** 를 선택합니다.
    * 이미지에 레이블 세트에서 하나 이상의 레이블을 적용하려면 프로젝트에 대해 **이미지 분류 다중 레이블** 을 선택합니다. 예를 들어 개의 사진에는 *dog* 및 *daytime* 이라는 레이블이 모두 지정될 수 있습니다.
    * 이미지 내의 각 개체에 레이블과 경계 상자를 할당하려면 프로젝트에 대해 **개체 식별(경계 상자)** 을 선택합니다.
    * 레이블을 할당하고 이미지 내 각 개체 주위에 다각형을 그리려면 프로젝트에 **인스턴스 구분(다각형)** 을 선택합니다.

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
1. **데이터 세트 형식은** 파일로 설정되고, 이미지에는 파일 데이터 세트 형식만 지원됩니다.
1. 데이터 저장소를 선택합니다.
1. 데이터가 Blob 스토리지 내의 하위 폴더에 있으면 **찾아보기** 를 선택하여 경로를 선택합니다.
    * 선택한 경로의 하위 폴더에 있는 모든 파일을 포함하려면 "/**"를 경로에 추가합니다.
    * 현재 컨테이너 및 해당 하위 폴더에 있는 모든 데이터를 포함하려면 "* */* .*"를 추가합니다.
1. (선택 사항) 데이터 세트에 대한 설명을 제공합니다.
1. **다음** 을 선택합니다.
1. 세부 정보를 확인합니다. **뒤로** 를 선택하여 설정을 수정하거나, **만들기** 를 선택하여 데이터 세트를 만듭니다.

### <a name="create-a-dataset-from-uploaded-data"></a>업로드된 데이터에서 데이터 세트 만들기

데이터를 직접 업로드하려면 다음을 수행합니다.

1. **데이터 세트 만들기** > **로컬 파일에서** 를 차례로 선택합니다.
1. **이름** 을 데이터 세트에 할당합니다.
1. **데이터 세트 형식은** 파일로 설정되고, 이미지에는 파일 데이터 세트 형식만 지원됩니다.
1. (선택 사항) 데이터 세트에 대한 설명을 제공합니다.
1. **다음** 을 선택합니다.
1. (선택 사항) 데이터 저장소를 선택하거나 만듭니다. 또는 기본값을 유지하여 Machine Learning 작업 영역의 기본 Blob 저장소("workspaceblobstore")에 업로드합니다.
1. **찾아보기를** 선택하여 업로드할 로컬 파일 또는 폴더를 선택합니다.
1. **다음** 을 선택합니다.
1. 세부 정보를 확인합니다. **뒤로** 를 선택하여 설정을 수정하거나, **만들기** 를 선택하여 데이터 세트를 만듭니다.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> 증분 새로 고침 구성

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>레이블 클래스 지정

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-image-labeling-task"></a>이미지 레이블 지정 작업 설명

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

경계 상자와 관련하여 중요한 질문은 다음과 같습니다.

* 이 작업에 대한 경계 상자는 어떻게 정의되나요? 전적으로 개체의 내부에 있어야 하나요, 아니면 외부에 있어야 하나요? 최대한 가깝게 잘라야 하나요, 아니면 약간의 여유 공간이 허용되나요?
* 레이블 지정자에서 경계 상자를 정의하는 경우 적용해야 하는 주의 사항과 일관성의 수준은 어떻게 되나요?
* 이미지에서 부분적으로 표시된 개체에 레이블을 지정하는 방법은 무엇인가요? 
* 다른 개체에서 부분적으로 포함하고 있는 개체에 레이블을 지정하는 방법은 무엇인가요?

>[!NOTE]
> 레이블 지정자는 1-9 숫자 키를 사용하여 처음 9개의 레이블을 선택할 수 있습니다.

## <a name="use-ml-assisted-data-labeling"></a>ML 지원 데이터 레이블 지정 사용

**ML 지원 레이블 지정** 페이지를 사용하면 자동 기계 학습 모델을 트리거하여 레이블 지정 작업을 가속화할 수 있습니다. 의료 이미지(“.dcm”)는 지원 레이블 지정에 포함되지 않습니다.

레이블 지정 프로젝트가 시작될 때 잠재적인 편차를 줄이기 위해 항목이 임의의 순서로 섞입니다. 그러나 데이터 세트에 있는 모든 편차는 학습된 모델에 반영됩니다. 예를 들어 항목의 80%가 단일 클래스인 경우 모델을 학습시키는 데 사용되는 데이터의 약 80%가 해당 클래스의 데이터입니다. 이 학습에는 활성 학습이 포함되지 않습니다.

*ML 지원 레이블 지정 사용* 을 선택하고, 지원 레이블 지정을 사용하도록 설정할 GPU를 지정합니다. 이는 다음 두 단계로 구성됩니다.

* Clustering
* 사전 레이블 지정

지원 레이블 지정을 시작하는 데 필요한 레이블 지정 데이터의 정확한 수는 고정된 수가 아닙니다.  이는 레이블 지정 프로젝트마다 크게 다를 수 있습니다. 일부 프로젝트의 경우 레이블이 300개의 항목에 수동으로 지정되면 사전 레이블 또는 클러스터 작업을 볼 수 있는 경우가 있습니다. ML 지원 레이블 지정에서는 미리 학습된 모델을 사용하여 학습 프로세스를 시작하는 *전이 학습* 이라는 기술을 사용합니다. 데이터 세트의 클래스가 미리 학습된 모델의 클래스와 비슷한 경우 레이블이 수백 개의 항목에 수동으로 지정된 후에만 사전 레이블을 사용할 수 있습니다. 데이터 세트가 모델을 미리 학습하는 데 사용되는 데이터와 크게 다른 경우 훨씬 더 오래 걸릴 수 있습니다.

최종 레이블에서 여전히 레이블 지정자의 입력을 사용하므로 이 기술을 *사용자 참여형* 레이블 지정이라고 하는 경우도 있습니다.

> [!NOTE]
> ML 지원 데이터 레이블 지정은 [가상 네트워크](how-to-network-security-overview.md) 뒤에서 보호되는 기본 스토리지 계정을 지원하지 않습니다. ML 지원 데이터 레이블 지정에는 기본이 아닌 스토리지 계정을 사용해야 합니다. 기본이 아닌 스토리지 계정은 가상 네트워크 뒤에서 보호할 수 있습니다.

### <a name="clustering"></a>Clustering

특정 개수의 레이블이 제출되면 분류를 위한 기계 학습 모델이 유사한 항목의 그룹화를 시작합니다.  이러한 비슷한 이미지는 수동 태그 지정 속도를 높이기 위해 동일한 화면의 레이블 지정자에 표시됩니다. 클러스터링은 레이블 지정자에서 4, 6 또는 9개의 이미지 그리드를 표시할 때 특히 유용합니다.

기계 학습 모델이 수동으로 레이블이 지정된 데이터에 대해 학습되면 모델은 마지막으로 완전히 연결된 계층으로 잘립니다. 그런 다음, 레이블이 없는 이미지가 일반적으로 "포함" 또는 "기능화"라고 알려진 프로세스에서 잘린 모델을 통과합니다. 이렇게 하면 각 이미지가 이 모델 계층에서 정의한 상위 차원 공간에 포함됩니다. 클러스터링 작업에는 공간에서 가장 가까운 이미지가 사용됩니다. 

개체 감지 모델 또는 텍스트 분류에 대해서는 클러스터링 단계가 표시되지 않습니다.

### <a name="prelabeling"></a>사전 레이블 지정

충분한 레이블이 제출되면 분류 모델을 사용하여 태그를 예측합니다. 또는 개체 검색 모델을 사용하여 경계 상자를 예측할 수 있습니다. 이제 레이블 지정자에는 이미 각 항목에 있는 예측된 레이블이 포함된 페이지가 표시됩니다. 개체 검색의 경우 예측 상자도 표시됩니다. 그런 다음, 페이지를 제출하기 전에 이러한 예측을 검토하고 레이블이 잘못 지정된 이미지를 수정합니다.  

기계 학습 모델이 수동으로 레이블이 지정된 데이터에 대해 학습되면 수동으로 레이블이 지정된 항목의 테스트 세트에 대해 모델을 평가하여 서로 다른 신뢰도 임계값으로 정확도를 결정합니다. 이 평가 프로세스는 사전 레이블을 표시할 만큼 충분히 정확한 모델의 신뢰도 임계값을 결정하는 데 사용됩니다. 그런 다음, 레이블이 없는 데이터에 대해 모델을 평가합니다. 이 임계값보다 더 확실한 예측이 있는 항목은 사전 레이블 지정에 사용됩니다.

## <a name="initialize-the-image-labeling-project"></a>이미지 레이블 지정 프로젝트 초기화

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>프로젝트 실행 및 모니터링

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>대시보드

**대시보드** 탭에는 레이블 지정 작업의 진행률이 표시됩니다.

:::image type="content" source="./media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="데이터 레이블 지정 대시보드":::

진행률 차트는 레이블이 지정되었거나 건너뛰거나 검토가 필요하거나 아직 완료되지 않은 항목 수를 표시합니다.  차트를 마우스로 가리키면 각 섹션의 항목 수가 표시됩니다.

가운데 섹션에는 아직 할당되지 않은 작업 큐가 표시됩니다. ML 지원 레이블 지정이 해제된 경우, 이 섹션에는 할당할 수동 작업 수가 표시됩니다. ML 지원 레이블 지정이 설정된 경우 다음 사항도 표시됩니다.

* 큐의 클러스터된 항목을 포함하는 작업
* 큐의 미리 레이블이 지정된 항목을 포함하는 작업

또한 ML 지원 레이블 지정을 사용하는 경우 다음 학습 실행이 발생하는 경우 작은 진행률 표시줄이 보입니다.  실험 섹션에는 각 기계 학습 실행에 대한 링크가 제공됩니다.

* 학습 - 레이블을 예측하도록 모델 교육
* 유효성 검사 - 이 모델의 예측을 항목에 미리 레이블을 지정하는 데 사용할지 여부를 결정 
* 유추 - 새 항목에 대한 예측 실행
* 기능화 - 클러스터 항목(이미지 분류 프로젝트에만 해당)

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
* ML 지원 레이블 지정에 대한 세부 정보(사용/사용 안 함 포함) 편집

### <a name="access-for-labelers"></a>레이블 지정자에 대한 액세스

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>프로젝트에 새 레이블 클래스 추가

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>레이블 내보내기

레이블 지정 프로젝트의 **프로젝트 세부 정보** 페이지에서 **내보내기** 단추를 사용합니다. 레이블 데이터는 Machine Learning 실험을 위해 언제든지 내보낼 수 있습니다. 

* 이미지 레이블은 다음으로 내보낼 수 있습니다.
    * [COCO 형식](http://cocodataset.org/#format-data). COCO 파일은 *Labeling/export/coco* 내의 폴더에 있는 Azure Machine Learning 작업 영역의 기본 Blob 저장소에 생성됩니다. 
    * [레이블이 있는 Azure Machine Learning 데이터 세트](how-to-use-labeled-dataset.md) 

Machine Learning의 **데이터 세트** 섹션에서 내보낸 Azure Machine Learning 데이터 세트에 액세스합니다. 데이터 세트 세부 정보 페이지에는 Python에서 레이블에 액세스할 수 있는 샘플 코드도 있습니다.

![내보낸 데이터 세트](./media/how-to-create-labeling-projects/exported-dataset.png)

레이블이 지정한 데이터를 Azure Machine Learning 데이터 세트로 내보낸 후에는 AutoML을 사용하여 레이블이 지정한 데이터에 대해 학습된 컴퓨터 비전 모델을 빌드할 수 있습니다. [Python을 사용하여 컴퓨터 비전 모델을 학습하도록 AutoML 설정(미리 보기)에서](how-to-auto-train-image-models.md) 자세히 알아보세요.

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]

### <a name="object-detection-troubleshooting"></a>개체 검색 문제 해결

|문제  |해결 방법  |
|---------|---------|
|개체 검색에 대한 레이블을 지정하는 동안 Esc 키를 누르면 왼쪽 위 모서리에 크기가 0인 레이블이 생성됩니다. 이 상태의 레이블 제출은 실패합니다.     |   레이블 옆에 있는 십자 표시를 클릭하여 레이블을 삭제합니다.  |

## <a name="next-steps"></a>다음 단계

* [자습서: 첫 번째 이미지 분류 레이블 지정 프로젝트 만들기](tutorial-labeling.md)를 참조하세요.
* [이미지에 태그를 붙이는 방법](how-to-label-data.md)
