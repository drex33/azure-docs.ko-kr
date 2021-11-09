---
title: '자습서: 이미지 분류를 위한 레이블 지정 프로젝트 만들기'
titleSuffix: Azure Machine Learning
description: 다중 클래스 이미지 분류 모델에서 사용할 수 있도록 이미지 레이블 지정 프로세스를 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.reviewer: ranku
ms.date: 10/21/2021
ms.custom: data4ml
ms.openlocfilehash: 148b779e56b14652bfb8b9c4eaf0e68585203cf8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061535"
---
# <a name="tutorial-create-a-labeling-project-for-multi-class-image-classification"></a>자습서: 다중 클래스 이미지 분류를 위한 레이블 지정 프로젝트 만들기 


이 자습서에서는 기계 학습 모델을 빌드하기 위한 데이터로 사용되는 이미지 레이블 지정(태그 지정이라고도 함) 프로세스를 관리하는 방법을 보여 줍니다. Azure Machine Learning의 데이터 레이블 지정은 공개 미리 보기에 있습니다.

이미지를 분류하는 기계 학습 모델을 학습시키려면 레이블이 올바르게 지정된 수백 또는 수천 개의 이미지가 필요합니다.  Azure Machine Learning은 도메인 전문가의 프라이빗 팀에서 데이터에 레이블을 지정하는 작업의 진행 상황을 관리하는 데 도움이 됩니다.
 
이 자습서에서는 고양이와 개의 이미지를 사용합니다.  각 이미지가 고양이 또는 개이므로 *다중 클래스* 레이블 지정 프로젝트가 됩니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
>
> * Azure 스토리지 계정을 만들고, 이미지를 계정에 업로드합니다.
> * Azure Machine Learning 작업 영역을 만듭니다.
> * 다중 클래스 이미지 레이블 지정 프로젝트를 만듭니다.
> * 데이터에 레이블을 지정합니다.  사용자 또는 레이블 지정자에서 이 작업을 수행할 수 있습니다.
> * 데이터를 검토하고 내보내서 프로젝트를 완료합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다.

[작업 영역을 만드는 방법](how-to-manage-workspace.md)에는 여러 가지가 있습니다. 이 자습서에서는 Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="start-a-labeling-project"></a>레이블 지정 프로젝트 시작

다음으로, 모든 기술 수준의 데이터 과학 전문가를 위한 데이터 과학 시나리오를 수행하는 기계 학습 도구가 포함된 통합 인터페이스인 Azure Machine Learning Studio에서 데이터 레이블 지정 프로젝트를 관리합니다. 이 Studio는 Internet Explorer 브라우저에서 지원되지 않습니다.

1. [Azure Machine Learning Studio](https://ml.azure.com)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

### <a name="create-a-datastore"></a><a name="create-datastore"></a>데이터 저장소 만들기

Azure Machine Learning 데이터 저장소는 구독 ID 및 토큰 권한 부여와 같은 연결 정보를 저장하는 데 사용됩니다. 여기서는 데이터 저장소를 사용하여 이 자습서의 이미지가 포함된 스토리지 계정에 연결합니다.

1. 작업 영역의 왼쪽에서 **데이터 저장소** 를 선택합니다.

1. **+ 새 데이터 저장소** 를 선택합니다.

1. 다음 설정을 사용하여 양식을 작성합니다.

    필드|Description 
    ---|---
    데이터 저장소 이름 | 데이터 저장소 이름을 지정합니다.  여기서는 **labeling_tutorial** 을 사용합니다.
    데이터 저장소 유형 | 스토리지 유형을 선택합니다.  여기서는 이미지에 대한 기본 설정 스토리지인 **Azure Blob Storage** 를 사용합니다.
    계정 선택 방법 | **수동으로 입력** 을 선택합니다.
    URL | `https://azureopendatastorage.blob.core.windows.net/openimagescontainer`
    인증 유형 | **SAS 토큰** 을 선택합니다.
    계정 키 | `ZPlDx0bFHFEqwoy8/B/ZZg1YKi/+cIiPamOPUrRptWbvkO6d84n4loitnSMorv/AxrvE0s86cUr6rULWaSGA2A==`

1. **만들기** 를 선택하여 데이터 저장소를 만듭니다.

### <a name="create-a-labeling-project"></a>레이블 지정 프로젝트 만들기

이제 레이블을 지정하려는 데이터에 액세스할 수 있으므로 레이블 지정 프로젝트를 만듭니다.

1. 페이지 위쪽에서 **프로젝트** 를 선택합니다.

1. **+ 프로젝트 추가** 를 선택합니다.

    :::image type="content" source="media/tutorial-labeling/create-project.png" alt-text="프로젝트 만들기":::

### <a name="project-details"></a>프로젝트 세부 정보

1. 다음 입력을 **프로젝트 세부 정보** 양식에 사용합니다.

    필드|Description 
    ---|---
    프로젝트 이름 | 프로젝트 이름을 지정합니다.  여기서는 **tutorial-cats-n-dogs** 를 사용합니다.
    레이블 지정 작업 유형 | **이미지 분류 다중 클래스** 를 선택합니다.  
    
    **다음** 을 선택하여 프로젝트 만들기를 계속합니다.

### <a name="add-workforce-optional"></a>인력 추가(선택 사항)

**다음** 을 선택하여 계속합니다.  이 자습서에서는 외부 인력을 사용하지 않습니다.

### <a name="select-or-create-a-dataset"></a>데이터 세트 선택 또는 만들기

1.   **데이터 세트 선택 또는 만들기** 양식에서 두 번째 선택 항목인 **데이터 세트 만들기** 를 선택한 다음, **데이터 저장소에서** 링크를 선택합니다.

1. 다음 입력을 **데이터 저장소에서 데이터 세트 만들기** 양식에 사용합니다.

    1. **기본 정보** 양식에서 이름을 추가합니다. 여기서는 **images-for-tutorial** 을 사용합니다.  원하는 경우 설명을 추가합니다.  그런 후 **다음** 을 선택합니다.
    1. **데이터 저장소 선택** 양식에서 **이전에 만든 데이터 저장소** 를 선택한 다음, 데이터 저장소 이름을 클릭하고 **데이터 저장소 선택** 을 선택합니다.
    1. 다음 페이지에서 현재 선택한 데이터 저장소가 올바른지 확인합니다. 그렇지 않은 경우 **이전에 만든 데이터 저장소** 를 선택하고 이전 단계를 반복합니다.
    1. 다음으로, 여전히 **데이터 저장소 선택** 양식에서 **찾아보기** 를 선택한 다음, **MultiClass - DogsCats** 를 선택합니다.  **저장** 을 선택하여 **/MultiClass - DogsCats** 를 경로로 사용합니다.
    1. **다음** 을 선택하여 세부 정보를 확인한 다음, **만들기** 를 선택하여 데이터 세트를 만듭니다.
    1. 목록에서 데이터 세트 이름(예: **images-for-tutorial**) 옆에 있는 원을 선택합니다.

1. **다음** 을 선택하여 프로젝트 만들기를 계속합니다.

### <a name="incremental-refresh"></a>증분 새로 고침

데이터 세트에 새 이미지를 추가하려는 경우 증분 새로 고침은 이러한 새 이미지를 찾아 프로젝트에 추가합니다.  이 기능을 활성화하면 프로젝트가 정기적으로 새 이미지를 확인합니다.  이 자습서에서는 데이터 저장소에 새 이미지를 추가하지 않으므로 이 기능을 선택하지 않은 상태로 둡니다.

**다음** 을 선택하여 계속합니다.

### <a name="label-classes"></a>레이블 클래스

1. **레이블 클래스** 양식에서 레이블 이름을 입력한 다음, **+ 레이블 추가** 를 선택하여 다음 레이블을 입력합니다.  이 프로젝트의 경우 레이블은 **고양이**, **개** 및 **불확실** 입니다.

1. 모든 레이블이 추가되었으면 **다음** 을 선택합니다.

### <a name="labeling-instructions"></a>레이블 지정 지침

1. **레이블 지정 지침** 양식에서 레이블 지정자에 대한 자세한 지침을 제공하는 웹 사이트에 대한 링크를 제공할 수 있습니다.  이 자습서에서는 비워 둡니다.

1. 작업에 대한 간단한 설명을 양식에 직접 추가할 수도 있습니다.  **레이블 지정 자습서 - 고양이 및 개** 를 입력합니다.

1. **다음** 을 선택합니다.

1. **ML 지원 레이블 지정** 섹션에서 확인란을 선택 취소한 상태로 둡니다. ML 지원 레이블 지정에는 이 자습서에서 사용하는 것보다 더 많은 데이터가 필요합니다.

1. **프로젝트 만들기** 를 선택합니다.

이 페이지는 자동으로 새로 고쳐지지 않습니다. 일시 중지한 후 프로젝트 상태가 **만들어짐** 으로 변경될 때까지 페이지를 수동으로 새로 고칩니다.

## <a name="start-labeling"></a>레이블 지정 시작

Azure 리소스가 설정되고 데이터 레이블 지정 프로젝트가 구성되었습니다. 이제는 데이터에 레이블을 추가할 차례입니다.

### <a name="tag-the-images"></a>이미지 태그 지정

자습서의 이 부분에서는 역할을 *프로젝트 관리자* 에서 *레이블 지정자* 로 전환합니다.  작업 영역에 대한 기여자 액세스 권한이 있는 사용자는 모두 레이블 지정자가 될 수 있습니다.

1. [Machine Learning Studio](https://ml.azure.com)의 왼쪽에서 **데이터 레이블 지정** 을 선택하여 프로젝트를 찾습니다.  

1. 프로젝트의 **레이블 링크** 를 선택합니다.

1. 지침을 읽은 다음, **작업** 을 선택합니다.

1. 레이블을 지정할 많은 이미지를 한 번에 표시하기 위해 오른쪽의 미리 보기 이미지를 선택합니다. 계속 진행하기 전에 이러한 모든 이미지에 레이블을 지정해야 합니다. 레이블이 지정되지 않은 데이터의 새 페이지가 있는 경우에만 레이아웃을 전환하세요. 레이아웃을 전환하면 페이지의 진행 중인 태그 지정 작업이 취소됩니다.

1. 하나 이상의 이미지를 선택한 다음, 선택 항목에 적용할 태그를 선택합니다. 태그는 이미지 아래에 표시됩니다.  페이지의 모든 이미지를 계속 선택하고 태그를 지정합니다.  표시된 모든 이미지를 동시에 선택하려면 **모두 선택** 을 선택합니다. 태그를 적용할 하나 이상의 이미지를 선택합니다.


    > [!TIP]
    > 키보드의 숫자 키를 사용하여 처음 9개의 태그를 선택할 수 있습니다.

1. 페이지의 모든 이미지에 태그가 지정되면 **제출** 을 선택하여 이러한 레이블을 제출합니다.

    ![이미지에 태그 지정](media/tutorial-labeling/catsndogs.gif)

1. 현재 데이터에 대한 태그가 제출되면 Azure에서 페이지를 작업 큐의 새 이미지 세트로 새로 고칩니다.

## <a name="complete-the-project"></a>프로젝트 완료

이제 레이블 지정 프로젝트에 대한 역할을 *프로젝트 관리자* 로 다시 전환합니다.

관리자는 레이블 지정자의 작업을 검토할 수 있습니다.  

### <a name="review-labeled-data"></a>레이블이 지정된 데이터 검토

1. [Machine Learning Studio](https://ml.azure.com)의 왼쪽에서 **데이터 레이블 지정** 을 선택하여 프로젝트를 찾습니다.  

1. 프로젝트 이름 링크를 선택합니다.

1. 대시보드에서 프로젝트의 진행 상황을 보여 줍니다.

1. 페이지 위쪽에서 **데이터** 를 선택합니다.

1. 왼쪽에서 **레이블이 지정된 데이터** 를 선택하여 태그가 지정된 이미지를 확인합니다.  

1. 레이블에 동의하지 않으면 해당 이미지를 선택한 다음, 페이지 아래쪽에서 **거부** 를 선택합니다.  태그가 제거되고, 이미지가 레이블이 없는 이미지의 큐에 다시 배치됩니다.

### <a name="export-labeled-data"></a>레이블이 지정된 데이터 내보내기

레이블 데이터는 Machine Learning 실험을 위해 언제든지 내보낼 수 있습니다. 사용자는 모든 이미지에 레이블이 지정될 때까지 기다리는 대신, 여러 번 내보내고 다른 모델을 학습시키는 경우가 많습니다.

이미지 레이블은 [COCO 형식](http://cocodataset.org/#format-data) 또는 Azure Machine Learning 데이터 세트로 내보낼 수 있습니다. 데이터 세트 형식을 사용하면 Azure Machine Learning 학습에 쉽게 사용할 수 있습니다.  

1. [Machine Learning Studio](https://ml.azure.com)의 왼쪽에서 **데이터 레이블 지정** 을 선택하여 프로젝트를 찾습니다.  

1. 프로젝트 이름 링크를 선택합니다.

1. **내보내기**, **Azure ML 데이터 세트로 내보내기** 를 차례로 선택합니다. 

    내보내기 상태가 **내보내기** 단추 바로 아래에 표시됩니다. 

1. 레이블이 성공적으로 내보내지면 왼쪽에서 **데이터 세트** 를 선택하여 결과를 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리


[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기계 학습 이미지 인식 모델 학습](how-to-use-labeled-dataset.md)
