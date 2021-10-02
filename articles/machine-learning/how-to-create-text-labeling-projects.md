---
title: 텍스트 레이블 지정 프로젝트 설정
titleSuffix: Azure Machine Learning
description: 데이터 레이블 지정 도구를 사용 하 여 텍스트에 레이블을 지정 하는 프로젝트를 만듭니다. 텍스트의 각 부분에 적용 될 레이블 하나 또는 여러 개를 지정 합니다.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: 4194a771014ec0e05b588d0d9f4ca64734c54d92
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372823"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>텍스트 레이블 지정 프로젝트 및 레이블 내보내기 (미리 보기)

데이터 레이블 지정 프로젝트를 만들고 실행 하 여 Azure Machine Learning에서 텍스트 데이터에 레이블을 지정 하는 방법에 대해 알아봅니다.  텍스트의 각 부분에 적용 될 레이블 하나 또는 여러 개를 지정 합니다.

또한 데이터 레이블 지정 도구를 사용 하 여 [이미지 레이블 지정 프로젝트를 만들](how-to-create-image-labeling-projects.md)수 있습니다.

> [!IMPORTANT]
> 텍스트 레이블 지정은 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="text-labeling-capabilities"></a>텍스트 레이블 기능

Azure Machine Learning 데이터 레이블 지정은 데이터 레이블 지정 프로젝트를 만들고 관리 하 고 모니터링 하는 중앙 위치입니다.

- 데이터, 레이블 및 팀 멤버를 조정하여 레이블 지정 작업을 효율적으로 관리합니다. 
- 진행 상황을 추적하고 불완전한 레이블 지정 작업의 큐를 유지 관리합니다.
- 프로젝트를 시작 및 중지하고 레이블 지정 진행률을 제어합니다.
- 레이블이 지정 된 데이터를 검토 하 고 Azure Machine Learning 데이터 집합으로 레이블을 내보냅니다.

> [!Important]
> Azure blob 데이터 저장소에서 텍스트 데이터를 사용할 수 있어야 합니다. (기존 데이터 저장소가 없는 경우 프로젝트를 만드는 동안 파일을 업로드할 수 있습니다.)

텍스트 데이터는 ".txt" 또는 ".csv" 파일일 수 있습니다.

* ".txt" 파일의 경우 각 파일은 레이블이 지정될 하나의 항목을 나타냅니다.
* ".csv" 파일의 경우 각 행은 labeler에 제공 된 하나의 항목을 나타냅니다.  해당 행의 레이블을 지정할 때 사용할 하나 이상의 열을 표시할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>텍스트 레이블 지정 프로젝트 만들기

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. 프로젝트를 만들려면 **프로젝트 추가** 를 선택합니다. 프로젝트에 적절 한 이름을 지정 합니다. 나중에 프로젝트를 삭제하더라도 프로젝트 이름을 다시 사용할 수 없습니다.

1. **텍스트** 를 선택하여 텍스트 레이블 지정 프로젝트를 만듭니다.

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="텍스트 레이블 지정을 위한 레이블 지정 프로젝트 만들기":::

    * 각 텍스트에 레이블 세트의 *단일 레이블* 만 적용하려면 프로젝트에 대해 **텍스트 분류 다중 클래스(미리 보기)** 를 선택합니다.
    * 각 텍스트에 레이블 세트의 레이블을 *하나 이상* 적용하려면 프로젝트에 대해 **텍스트 분류 다중 레이블(미리 보기)** 을 선택합니다.

1. 계속할 준비가 되면 **다음** 을 선택합니다.

## <a name="add-workforce-optional"></a>직원 추가 (선택 사항)

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
1. **데이터 집합 유형을** 선택 합니다.
    * .csv 파일을 사용 하는 경우 **테이블 형식** 을 선택 합니다. 각 행에는 응답이 포함 됩니다.
    * 각 응답에 별도의 .txt 파일을 사용하는 경우 **파일** 을 선택합니다.
1. 필드 데이터 집합에 대 한 설명을 제공 합니다.
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
    * 각 행이 응답인 .csv 파일을 사용하는 경우 **테이블 형식** 을 선택합니다.
    * 각 응답에 별도의 .txt 파일을 사용하는 경우 **파일** 을 선택합니다.
1. 필드 데이터 집합에 대 한 설명을 제공 합니다.
1. **다음** 을 선택합니다.
1. 필드 데이터 저장소를 선택 하거나 만듭니다. 또는 Machine Learning 작업 영역의 기본 blob 저장소 ("workspaceblobstore")에 업로드 하도록 기본값을 유지 합니다.
1. 업로드할 로컬 파일 또는 폴더를 선택 하려면 **업로드** 을 선택 합니다.
1. **다음** 을 선택합니다.
1. .csv 파일을 업로드 하는 경우:
    * 설정 및 미리 보기를 확인 하 고 **다음** 을 선택 합니다.
    * 해당 행을 분류할 때 labeler에서 보려는 텍스트의 모든 열을 포함 합니다.
    * **다음** 을 선택합니다.
1.  세부 정보를 확인합니다. **뒤로** 를 선택하여 설정을 수정하거나, **만들기** 를 선택하여 데이터 세트를 만듭니다.


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> 증분 새로 고침 구성

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>레이블 클래스 지정

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>텍스트 레이블 지정 태스크 설명

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> 레이블 지정자는 1-9 숫자 키를 사용하여 처음 9개의 레이블을 선택할 수 있습니다.


## <a name="initialize-the-text-labeling-project"></a>텍스트 레이블 지정 프로젝트를 초기화 합니다.

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>프로젝트 실행 및 모니터링

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>대시보드


**대시보드** 탭에는 레이블 지정 작업의 진행률이 표시됩니다.

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="텍스트 데이터 레이블 지정 대시보드":::


진행률 차트에는 표시 된 항목의 수, 건너뛴 항목, 검토 필요 또는 아직 완료 되지 않은 항목이 표시 됩니다.  각 섹션의 항목 수를 보려면 차트를 마우스로 가리킵니다.

가운데 섹션에는 아직 할당되지 않은 작업 큐가 표시됩니다. 


오른쪽에는 완료된 작업에 대한 레이블 분포가 있습니다.  일부 프로젝트 형식은 항목에 여러 레이블이 있을 수 있으며, 이런 경우 레이블의 총 합계가 총 합계 항목보다 클 수 있습니다.

### <a name="data-tab"></a>데이터 탭

**데이터** 탭에서 데이터 세트를 보고, 레이블이 지정된 데이터를 검토할 수 있습니다. 레이블이 지정 된 데이터를 스크롤하여 레이블을 확인 합니다. 잘못된 레이블이 지정된 데이터가 표시되면 이를 선택하고 **거부** 를 선택합니다. 그러면 해당 레이블이 제거되고, 해당 데이터를 레이블이 지정되지 않은 큐에 다시 넣습니다.

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

