---
title: 스튜디오 UI를 사용하여 데이터 스토리지에 연결
titleSuffix: Azure Machine Learning
description: 데이터 저장소 및 데이터 세트를 만들어 Azure Machine Learning 스튜디오를 사용하여 Azure에서 스토리지 서비스의 데이터에 안전하게 연결합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.author: yogipandey
author: ynpandey
ms.reviewer: nibaccam
ms.date: 10/21/2021
ms.custom: data4ml
ms.openlocfilehash: 93c5cd92822ee9fdc5f9594414bf2bf1f118a310
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562545"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Azure Machine Learning 스튜디오를 사용하여 데이터에 연결

이 문서에서는 [Azure Machine Learning 스튜디오](overview-what-is-machine-learning-studio.md)를 사용하여 데이터에 액세스하는 방법을 알아봅니다. [Azure Machine Learning 데이터 저장소](how-to-access-data.md)를 사용하여 Azure의 스토리지 서비스에서 데이터에 연결한 다음, [Azure Machine Learning 데이터 세트](how-to-create-register-datasets.md)를 사용하여 ML 워크플로의 작업을 위해 해당 데이터를 패키지합니다.

다음 표에서는 데이터 저장소 및 데이터 세트의 이점을 정의하고 요약합니다. 

|Object|Description| 이점|   
|---|---|---|
|데이터 저장소| 구독 ID 및 토큰 권한 부여 같은 연결 정보를 작업 영역과 연결된 [Key Vault](https://azure.microsoft.com/services/key-vault/)에 저장하여 Azure의 스토리지 서비스에 안전하게 연결합니다. | 정보가 안전하게 저장되므로 다음과 같은 이점이 있습니다. <br><br> <li> 인증 자격 증명 또는 원래 데이터 원본을 위험에 노출하지 않습니다. <li> 더 이상 스크립트에서 관련 정보를 하드 코딩할 필요가 없습니다.
|데이터 세트| 데이터 세트를 만들면 데이터 원본 위치에 대한 참조와 해당 메타데이터의 복사본을 만듭니다. 데이터 세트를 사용하여 다음을 수행할 수 있습니다. <br><br><li> 모델 학습 중에 데이터에 액세스합니다.<li> 데이터를 공유하고 다른 사용자와 협업합니다.<li> 데이터 검색을 위해 pandas 같은 오픈 소스 라이브러리를 이용합니다. | 데이터 세트는 지연 평가되고 데이터는 기존 위치에 남아 있기 때문에 <br><br><li>데이터의 단일 복사본을 스토리지에 보관합니다.<li> 추가 스토리지 비용이 발생하지 않습니다. <li> 실수로 데이터 원본을 변경할 위험이 없습니다.<li>ML 워크플로 성능 속도를 향상합니다. 

Azure Machine Learning의 전체 데이터 액세스 워크플로에서 데이터 저장소 및 데이터 세트에 적합한 위치를 파악하려면 [안전하게 데이터 액세스](concept-data.md#data-workflow) 문서를 참조하세요.

Code First 환경에서 [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/)를 사용하려면 다음 문서를 참조하세요.
* [데이터 저장소를 사용하여 Azure Storage 서비스에 연결](how-to-access-data.md). 
* [Azure Machine Learning 데이터 세트 만들기](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.

- [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 액세스합니다.

- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md).

    -  작업 영역을 만들면 Azure Blob 컨테이너와 Azure 파일 공유가 자동으로 작업 영역에 데이터 저장소로 등록됩니다. 각각 이름이 `workspaceblobstore` 및 `workspacefilestore`로 지정됩니다. Blob Storage가 요구 사항에 충분한 경우 `workspaceblobstore`는 기본 데이터 저장소로 설정되며 이미 사용하도록 구성되어 있습니다. 충분하지 않은 경우 [지원되는 스토리지 유형](how-to-access-data.md#matrix)의 스토리지 계정이 Azure에 있어야 합니다.
    

## <a name="create-datastores"></a>데이터 저장소 만들기

[이 Azure 스토리지 솔루션](how-to-access-data.md#matrix)에서 데이터 저장소를 만들 수 있습니다. **지원되지 않는 스토리지 솔루션의 경우** ML 실험 중에 데이터 송신 비용을 절약하려면 지원되는 Azure Storage 솔루션으로 [데이터를 이동](how-to-access-data.md#move)해야 합니다. [데이터 저장소에 관해 자세히 알아봅니다](how-to-access-data.md). 

Azure Machine Learning 스튜디오에서는 몇 단계로 새 데이터 저장소를 만듭니다.

> [!IMPORTANT]
> 데이터 스토리지 계정이 가상 네트워크에 있는 경우 스튜디오가 데이터에 액세스할 수 있도록 하려면 추가 구성 단계가 필요합니다. 적절한 구성 단계가 적용되도록 하려면 [네트워크 격리 및 프라이버시](how-to-enable-studio-virtual-network.md)를 참조하세요.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창의 **관리** 에서 **데이터 저장소** 를 선택합니다.
1. **+ 새 데이터 저장소** 를 선택합니다.
1. 양식을 완료하여 새 데이터 저장소를 만들고 등록합니다. 이 양식은 사용자가 선택하는 Azure 스토리지 유형 및 인증 형식에 따라 지능적으로 업데이트됩니다. 이 양식을 채우는 데 필요한 인증 자격 증명을 찾을 수 있는 위치를 파악하려면 [스토리지 액세스 및 권한 섹션](#access-validation)을 참조하세요.

다음 예제에서는 **Azure Blob 데이터 저장소** 를 만들 때 양식이 어떻게 표시되는지 보여 줍니다.

![새 데이터 저장소에 대한 양식](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>데이터 세트 생성

데이터 저장소를 만든 후 데이터 세트를 만들어 데이터와 상호 작용합니다. 데이터 세트는 학습 같은 기계 학습 작업의 지연 평가되는 소모성 개체로 패키지합니다. [데이터 세트](how-to-create-register-datasets.md)에 대해 자세히 알아보세요.

두 가지 형식의 데이터 세트인 FileDataset 및 TabularDataset가 있습니다. 
[FileDataset](how-to-create-register-datasets.md#filedataset)는 단일 또는 여러 파일이나 퍼블릭 URL에 대한 참조를 만듭니다. 반면, [TabularDataset](how-to-create-register-datasets.md#tabulardataset)는 데이터를 테이블 형식으로 표시합니다. .csv, .tsv, .parquet, .jsonl 파일 및 SQL 쿼리 결과에서 TabularDataset를 만들 수 있습니다.

다음 단계와 애니메이션은 [Azure Machine Learning 스튜디오](https://ml.azure.com)에서 데이터 세트를 만드는 방법을 보여 줍니다.

> [!Note]
> Azure Machine Learning 스튜디오를 통해 만든 데이터 세트는 작업 영역에 자동으로 등록됩니다.

![UI를 사용하여 데이터 세트 만들기](./media/how-to-connect-data-ui/create-dataset-ui.gif)

스튜디오에서 데이터 세트를 만들려면:
1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창의 **자산** 섹션에서 **데이터 세트** 를 선택합니다.
1. **데이터 세트 만들기** 를 선택하여 데이터 세트의 원본을 선택합니다. 이 원본은 로컬 파일, 데이터 저장소, 퍼블릭 URL 또는 [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md)일 수 있습니다.
1. 데이터 세트 형식으로 **테이블 형식** 또는 **파일** 을 선택합니다.
1. **다음** 을 선택하여 **데이터 저장소 및 파일 선택** 양식을 엽니다. 이 양식에서 만든 후 데이터 세트를 보관할 위치를 선택하고 데이터 세트에 사용할 데이터 파일을 선택합니다.
    1. 데이터가 가상 네트워크에 있는 경우 유효성 검사 건너뛰기를 사용합니다. [가상 네트워크 격리 및 프라이버시](how-to-enable-studio-virtual-network.md)에 관해 자세히 알아봅니다.

1. **다음** 을 선택하여 **설정 및 미리 보기** 및 **스키마** 양식을 채웁니다. 이 양식은 파일 형식에 따라 지능적으로 채워지며 이 양식에서 만들기 전에 데이터 세트를 추가로 구성할 수 있습니다. 
    1.  설정과 미리 보기 양식에서 데이터에 여러 줄 데이터가 포함되어 있는지 여부를 표시할 수 있습니다. 
    1. 스키마 양식에서 날짜 또는 시간 열에 대해 **타임스탬프** 유형을 선택하여 TabularDataset에 시간 구성 요소가 있음을 지정할 수 있습니다. 
        1. 데이터의 형식이 하위 집합(예: 기간)으로 지정된 상태에서 이 하위 집합을 학습에 사용하려는 경우 **파티션 타임스탬프** 형식을 선택합니다. 이렇게 하면 데이터 세트에서 시계열 작업을 수행할 수 있습니다. [데이터 세트의 파티션을 학습에 활용](how-to-monitor-datasets.md?tabs=azure-studio#create-target-dataset)하는 방법에 관해 자세히 알아봅니다.
1. **다음** 을 선택하여 **확인 세부 정보** 양식을 검토합니다. 선택 항목을 확인하고 데이터 세트의 선택적 데이터 프로필을 만듭니다. [데이터 프로파일링](#profile)에 대한 자세한 정보
1. **만들기** 를 선택하여 데이터 세트 만들기를 완료합니다.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>데이터 프로필 및 미리 보기

데이터 세트를 만든 후 다음 단계를 사용하여 스튜디오에서 프로필 및 미리 보기를 볼 수 있는지 확인합니다. 

1. [Azure Machine Learning 스튜디오](https://ml.azure.com/)에 로그인합니다.
1. 왼쪽 창의 **자산** 섹션에서 **데이터 세트** 를 선택합니다.
1. 보려는 데이터 세트 이름을 선택합니다. 
1. **탐색** 탭을 선택합니다. 
1. **미리 보기** 또는 **프로필** 탭을 선택합니다. 

![데이터 세트 프로필 및 미리 보기](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

데이터 세트 전체에 대한 다양한 요약 통계를 가져와서 데이터 세트가 ML 준비 상태인지 여부를 확인할 수 있습니다. 숫자가 아닌 열의 경우 최솟값, 최댓값 및 오류 수와 같은 기본 통계만 포함됩니다. 숫자 열의 경우 통계적 모멘트 및 예상 분위수를 검토할 수도 있습니다. 

특히 Azure Machine Learning 데이터 세트의 데이터 프로필에는 다음이 포함됩니다.

>[!NOTE]
> 관련이 없는 형식의 기능은 빈 항목으로 표시되어 있습니다.

|통계|Description
|------|------
|기능| 요약되는 열의 이름입니다.
|프로필| 유추된 형식을 기반으로 하는 인라인 시각화입니다. 예를 들어 문자열, 술래 및 날짜에는 값 개수가 있고 소수점(숫자)에는 근사 히스토그램이 있습니다. 이를 통해 데이터의 분포를 빠르게 파악할 수 있습니다.
|형식 분포| 열 내 유형의 인라인 값 개수입니다. Null은 고유 형식이므로 이 시각화는 홀수 또는 누락된 값을 탐지하는 데 유용합니다.
|Type|열의 유추된 형식입니다. 가능한 값은 문자열, 부울, 날짜 및 10진수입니다.
|최소값| 열의 최솟값입니다. 형식(예: 부울)에 고유한 순서가 없는 기능은 빈 항목으로 표시됩니다.
|최대값| 열의 최댓값입니다. 
|개수| 열에서 누락되거나 누락되지 않은 항목의 총 수입니다.
|누락되지 않은 수| 열에서 누락되지 않은 항목의 수입니다. 빈 문자열과 오류는 값으로 처리되므로 "누락되지 않은 수"에 영향을 주지 않습니다.
|분위수| 데이터 분포를 파악하기 위한 각 분위수의 근사 값입니다.
|평균| 열의 산술 평균 또는 평균입니다.
|표준 편차| 이 열의 데이터에 대한 분산 또는 편차를 측정한 값입니다.
|Variance| 이 열의 데이터가 평균 값에서 분산된 정도를 측정한 값입니다. 
|왜곡도| 이 열의 데이터가 정규 분포에서 치우쳐 있는 정도를 측정한 값입니다.
|첨도| 이 열의 데이터가 정규 분포와 비교하여 뾰족하게 밀집된 정도를 측정한 값입니다.

## <a name="storage-access-and-permissions"></a>스토리지 액세스 및 권한

Azure Storage 서비스에 안전하게 연결하려면 Azure Machine Learning에서 해당하는 데이터 스토리지에 액세스할 권한이 있어야 합니다. 이 액세스 권한은 데이터 저장소를 등록하는 데 사용되는 인증 자격 증명에 따라 다릅니다.

### <a name="virtual-network"></a>가상 네트워크

데이터 스토리지 계정이 **가상 네트워크** 에 있는 경우 Azure Machine Learning이 데이터에 액세스할 수 있도록 하려면 추가 구성 단계가 필요합니다. 데이터 저장소를 만들고 등록할 때 적절한 구성 단계가 적용되도록 하려면 [네트워크 격리 및 프라이버시](how-to-enable-studio-virtual-network.md)를 참조하세요.  

### <a name="access-validation"></a>액세스 유효성 검사

**초기 데이터 저장소 생성 및 등록 프로세스에서** Azure Machine Learning은 기본 스토리지 서비스가 있고 사용자가 제공한 보안 주체(사용자 이름, 서비스 주체 또는 SAS 토큰)가 지정된 스토리지에 대한 액세스 권한이 있는지 자동으로 유효성을 검사합니다.

**데이터 저장소가 생성된 후** 이 유효성 검사는 기본 스토리지 컨테이너에 액세스해야 하는 메서드에 대해서만 수행되고, 데이터 저장소 개체가 검색될 때마다 **수행되지는 않습니다**. 예를 들어 데이터 저장소에서 파일을 다운로드하려는 경우에는 유효성 검사가 수행되지만, 기본 데이터 저장소만 변경하려는 경우에는 유효성 검사가 수행되지 않습니다.

기본 스토리지 서비스에 대한 액세스를 인증하려면 만들려는 데이터 저장소 유형에 따라 계정 키, SAS(공유 액세스 서명) 토큰 또는 서비스 주체를 제공하면 됩니다. [스토리지 유형 행렬](how-to-access-data.md#matrix)에는 각 데이터 저장소 유형에 해당하는 지원되는 인증 유형이 나열됩니다.

[Azure Portal](https://portal.azure.com)에서 계정 키, SAS 토큰 및 서비스 주체 정보를 찾을 수 있습니다.

* 계정 키 또는 SAS 토큰을 인증에 사용할 계획이면 왼쪽 창에서 **스토리지 계정** 을 선택하고, 등록하려는 스토리지 계정을 선택합니다.
  * **개요** 페이지에서는 계정 이름, 컨테이너, 파일 공유 이름 등의 정보를 제공합니다.
      1. 계정 키의 경우 **설정** 창에서 **액세스 키** 로 이동합니다.
      1. SAS 토큰의 경우 **설정** 창에서 **공유 액세스 서명** 으로 이동합니다.

* [서비스 주체](../active-directory/develop/howto-create-service-principal-portal.md)를 인증에 사용하려면 **앱 등록** 으로 이동하고 사용하려는 앱을 선택합니다.
    * 해당하는 **개요** 페이지에는 테넌트 ID, 클라이언트 ID 등의 필요한 정보가 포함됩니다.

> [!IMPORTANT]
> * Azure Storage 계정(계정 키 또는 SAS 토큰)의 액세스 키를 변경해야 하는 경우 새 자격 증명을 작업 영역 및 이 작업 영역에 연결된 데이터 저장소와 동기화해야 합니다. [업데이트된 자격 증명을 동기화](how-to-change-storage-access-key.md)하는 방법을 알아봅니다. <br> <br>
> * 데이터 저장소의 등록을 취소하고 같은 이름으로 다시 등록할 때 실패하는 경우 작업 영역의 Azure Key Vault에서 일시 삭제가 사용되지 않는 것일 수 있습니다. 일시 삭제는 작업 영역에서 만든 키 자격 증명 모음 인스턴스에 대해 기본적으로 사용되지만, 기존 키 자격 증명 모음을 사용하거나 2020년 10월 이전에 작업 영역을 만든 경우에는 일시 삭제가 사용되지 않을 수 있습니다. 일시 삭제를 사용하도록 설정하는 방법에 관한 자세한 내용은 [기존 키 자격 증명 모음에 대해 일시 삭제 설정](../key-vault/general/soft-delete-change.md#turn-on-soft-delete-for-an-existing-key-vault)을 참조하세요.

### <a name="permissions"></a>권한

Azure Blob 컨테이너 및 Azure Data Lake Gen 2 스토리지의 경우 인증 자격 증명에 **Storage Blob 데이터 읽기 권한자** 액세스 권한이 있는지 확인합니다. [Storage Blob 데이터 읽기 권한자](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)에 관해 자세히 알아봅니다. 계정 SAS 토큰은 기본적으로 권한 없음으로 설정됩니다. 
* 데이터 **읽기 액세스** 의 경우 인증 자격 증명에는 컨테이너 및 개체에 대한 최소한의 나열 및 읽기 권한이 있어야 합니다. 

* 데이터 **쓰기 액세스** 의 경우 쓰기 및 추가 권한도 필요합니다.

## <a name="train-with-datasets"></a>데이터 세트로 학습

ML 모델 학습을 위한 기계 학습 실험에서 데이터 세트를 사용합니다. [데이터 세트를 사용하여 학습하는 방법에 관한 자세한 정보](how-to-train-with-datasets.md)

## <a name="next-steps"></a>다음 단계

* [TabularDataset 및 자동화된 기계 학습을 사용한 학습의 단계별 예제](tutorial-first-experiment-automated-ml.md).

* [모델을 학습합니다](how-to-set-up-training-targets.md).

* 추가 데이터 세트 학습 예제는 [샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)을 참조하세요.
