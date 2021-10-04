---
title: 'ML Studio(클래식): Azure Machine Learning으로 마이그레이션 - 데이터 세트 다시 빌드'
description: Azure Machine Learning designer에서 Studio (클래식) 데이터 집합을 다시 빌드합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: f6ad2a2120d998a89ebb59fc6f70779487f7c275
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129429512"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Studio(클래식) 데이터 세트를 Azure Machine Learning로 마이그레이션

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

이 문서에서는 Studio(클래식) 데이터 세트를 Azure Machine Learning으로 마이그레이션하는 방법에 대해 알아봅니다. Studio(클래식)에서 마이그레이션하는 방법에 대한 자세한 내용은 [마이그레이션 개요 문서](migrate-overview.md)를 참조하세요.

데이터 세트를 Azure Machine Learning으로 마이그레이션할 수 있는 세 가지 옵션이 있습니다. 각 섹션을 읽고 시나리오에 가장 적합한 옵션을 결정하세요.


|데이터의 위치는 어디인가요? | 마이그레이션 옵션  |
|---------|---------|
|Studio(클래식)     |  옵션 1: [Studio(클래식)에서 데이터 세트를 다운로드하고 Azure Machine Learning에 업로드합니다](#download-the-dataset-from-studio-classic).      |
|클라우드 스토리지     | 옵션 2: [클라우드 원본에서 데이터 세트를 등록합니다](#import-data-from-cloud-sources). <br><br>  옵션 3: [데이터 가져오기 모듈을 사용하여 클라우드 원본에서 데이터를 가져옵니다](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning은 데이터 세트 생성 및 관리를 위한 [코드 우선 워크플로](how-to-create-register-datasets.md)도 지원합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure Machine Learning 작업 영역 [Azure Machine Learning 작업 영역을 만듭니다](how-to-manage-workspace.md#create-a-workspace).
- 마이그레이션할 Studio(클래식) 데이터 세트입니다.


## <a name="download-the-dataset-from-studio-classic"></a>Studio(클래식)에서 데이터 세트 다운로드

Studio(클래식) 데이터 세트를 Azure Machine Learning으로 마이그레이션하는 가장 간단한 방법은 데이터 세트를 다운로드하여 Azure Machine Learning에 등록하는 것입니다. 이렇게 하면 데이터 세트의 새 복사본이 생성되고 Azure Machine Learning 데이터 저장소에 업로드됩니다.

다음 Studio(클래식) 데이터 세트 형식을 직접 다운로드할 수 있습니다.

* 일반 텍스트(.txt)
* 헤더가 있거나(.csv) 없는(.nh.csv) 쉼표로 구분된 값(CSV)
* 헤더가 있거나(.tsv) 없는(.nh.tsv) 탭으로 구분된 값(TSV)
* Excel 파일
* Zip 파일(.zip)

데이터 세트를 직접 다운로드하려면 다음을 수행합니다.
1. Studio(클래식) 작업 영역([https://studio.azureml.net](https://studio.azureml.net))으로 이동합니다.
1. 왼쪽 탐색 모음에서 **데이터 세트** 탭을 선택합니다.
1. 다운로드하려는 데이터 세트를 선택합니다.
1. 아래쪽 작업 모음에서 **다운로드** 를 선택합니다.

    ![Studio(클래식)에서 데이터 세트를 다운로드하는 방법을 보여주는 스크린샷](./media/migrate-register-dataset/download-dataset.png)

다음 데이터 형식에 대해 **CSV로 변환** 모듈을 사용하여 데이터 세트를 다운로드해야 합니다.

* SVMLight 데이터(.svmlight) 
* 특성 관계 파일 형식(ARFF) 데이터(.arff) 
* R 개체 또는 작업 영역 파일(. RData)
* 데이터 세트 형식(.data). 데이터 세트 형식은 모듈 출력에 대한 Studio(클래식) 내부 데이터 형식입니다.

데이터 세트를 CSV로 변환하고 결과를 다운로드하려면 다음을 수행합니다.

1. Studio(클래식) 작업 영역([https://studio.azureml.net](https://studio.azureml.net))으로 이동합니다.
1. 새로운 실험 만들기
1. 다운로드하려는 데이터 세트를 캔버스로 끌어다 놓습니다.
1. **CSV로 변환** 모듈을 추가합니다.
1. **CSV로 변환** 입력 포트를 데이터 세트의 출력 포트에 연결합니다.
1. 실험을 실행합니다.
1. **CSV로 변환** 모듈을 마우스 오른쪽 단추로 클릭합니다.
1. **결과 데이터 세트** > **다운로드** 를 선택합니다.

    ![CSV로 변환 파이프라인을 설정하는 방법을 보여 주는 스크린샷](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Azure Machine Learning으로 데이터 세트 업로드

데이터 파일을 다운로드한 후 Azure Machine Learning에서 데이터 세트를 등록할 수 있습니다.

1. Azure Machine Learning 스튜디오([ml.azure.com](https://ml.azure.com))로 이동합니다.
1. 왼쪽 탐색 창에서 **데이터 세트** 탭을 선택합니다.
1. **데이터 세트 만들기** > **로컬 파일에서** 를 차례로 선택합니다.
    ![로컬 파일을 만들기 위한 데이터 세트 탭 및 단추를 보여 주는 스크린샷](./media/migrate-register-dataset/register-dataset.png)
1. 이름 및 설명을 입력합니다.
1. **데이터 세트 형식** 에 대해 **테이블 형식** 을 선택합니다.

    > [!NOTE]
    > ZIP 파일을 데이터 세트로 업로드할 수도 있습니다. ZIP 파일을 업로드하려면 **데이터 세트 형식** 에 대해 **파일** 을 선택합니다.

1. **데이터 저장소 및 파일 선택의 경우** 데이터 세트 파일을 업로드할 데이터 저장소를 선택합니다.

    기본적으로 Azure Machine Learning은 기본 작업 영역 Blobstore에 데이터 세트를 저장합니다. 데이터 저장소에 대한 자세한 내용은 [Storage 서비스에 연결](how-to-access-data.md)을 참조하세요.

1. 데이터 세트에 대한 데이터 구문 분석 설정 및 스키마를 설정합니다. 그런 다음, 설정을 확인합니다.

## <a name="import-data-from-cloud-sources"></a>클라우드 소스에서 데이터 가져오기

데이터가 이미 클라우드 스토리지 서비스에 있고 데이터를 기본 위치에 저장하려는 경우 다음 옵션 중 하나를 사용할 수 있습니다.

|수집 방법|Description|
|---| --- |
|Azure Machine Learning 데이터 세트 등록|로컬 및 온라인 데이터 원본(Blob, ADLS Gen1, ADLS Gen2, 파일 공유, SQL DB)에서 데이터를 수집합니다. <br><br>런타임에 지연 평가되는 데이터 소스에 대한 참조를 만듭니다. 이 데이터 세트에 반복적으로 액세스하고 데이터 버전 관리 및 모니터링과 같은 고급 데이터 기능을 사용하려는 경우 이 옵션을 사용합니다.
|데이터 가져오기 모듈|온라인 데이터 원본(Blob, ADLS Gen1, ADLS Gen2, 파일 공유, SQL DB)에서 데이터를 수집합니다. <br><br> 데이터 세트는 현재 디자이너 파이프라인 실행으로만 가져오게 됩니다.


>[!Note]
> Studio(클래식) 사용자는 다음과 같은 클라우드 원본이 Azure Machine Learning에서 기본적으로 지원되지 않는다는 점에 유의해야 합니다.
> - 하이브 쿼리
> - Azure 테이블
> - Azure Cosmos DB
> - 온-프레미스 SQL 데이터베이스
>
> 사용자가 Azure Data Factory를 사용하여 지원되는 스토리지 서비스로 데이터를 마이그레이션하는 것이 좋습니다.  

### <a name="register-an-azure-machine-learning-dataset"></a>Azure Machine Learning 데이터 세트 등록

다음 단계를 사용하여 클라우드 서비스에서 Azure Machine Learning 데이터 세트를 등록합니다. 

1. 클라우드 스토리지 서비스를 Azure Machine Learning 작업 영역에 연결하는 [데이터 저장소를 만듭니다](how-to-connect-data-ui.md#create-datastores). 

1. [데이터 세트를 등록합니다](how-to-connect-data-ui.md#create-datasets). Studio(클래식) 데이터 세트를 마이그레이션하는 경우 **테이블 형식** 데이터 세트 설정을 선택합니다.

Azure Machine Learning에서 데이터 세트를 등록한 후 디자이너에서 사용할 수 있습니다.
 
1. 새 디자이너 파이프라인 초안을 만듭니다.
1. 왼쪽에 있는 모듈 팔레트에서 **데이터 세트** 를 확장합니다.
1. 등록된 데이터 세트를 캔버스로 끌어다 놓습니다. 

### <a name="use-the-import-data-module"></a>데이터 가져오기 모듈을 사용합니다.

다음 단계를 사용하여 데이터를 디자이너 파이프라인으로 직접 가져올 수 있습니다.

1. 클라우드 스토리지 서비스를 Azure Machine Learning 작업 영역에 연결하는 [데이터 저장소를 만듭니다](how-to-connect-data-ui.md#create-datastores). 

데이터 저장소를 만든 후 디자이너의 [**데이터 가져오기**](algorithm-module-reference/import-data.md) 모듈을 사용하여 데이터를 수집할 수 있습니다.

1. 새 디자이너 파이프라인 초안을 만듭니다.
1. 왼쪽의 모듈 팔레트에서 **데이터 가져오기** 모듈을 찾아 캔버스로 끌어서 놓습니다.
1. **데이터 가져오기** 모듈을 선택하고 오른쪽 패널의 설정을 사용하여 데이터 원본을 구성합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Studio(클래식) 데이터 세트를 Azure Machine Learning으로 마이그레이션하는 방법에 대해 알아보았습니다. 다음 단계는 [Studio(클래식) 학습 파이프라인을 다시 빌드](migrate-rebuild-experiment.md)하는 것입니다.


Studio(클래식) 마이그레이션 시리즈의 다른 문서를 참조하세요.

1. [마이그레이션 개요](migrate-overview.md)입니다.
1. **데이터 세트 마이그레이션**
1. [Studio(클래식) 학습 파이프라인을 다시 빌드](migrate-rebuild-experiment.md)합니다.
1. [Studio(클래식) 웹 서비스를 다시 빌드](migrate-rebuild-web-service.md)합니다.
1. [Azure Machine Learning 웹 서비스를 클라이언트 앱과 통합](migrate-rebuild-integrate-with-client-app.md)합니다.
1. [R 스크립트 실행을 마이그레이션](migrate-execute-r-script.md)합니다.
