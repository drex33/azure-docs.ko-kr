---
title: 'ML 스튜디오(클래식): 웹 서비스 배포 - Azure'
description: 학습 실험을 예측 실험으로 변환하고 배포할 준비를 한 다음 Machine Learning 스튜디오(클래식) 웹 서비스로 배포하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 0f57a5a30823b5d08afffafcff552121a34717bd
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581757"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning 스튜디오(클래식) 웹 서비스 배포하기

**적용 대상:** ![이 문서가 Machine Learning Studio(클래식)에 적용됨을 의미하는 확인 표시입니다.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식) ![이 문서가 Azure Machine Learning에 적용되지 않음을 의미하는 X입니다.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Machine Learning 스튜디오(클래식)를 사용하면 예측 분석 솔루션을 빌드 및 테스트할 수 있습니다. 그런 다음, 솔루션을 웹 서비스로 배포할 수 있습니다.

Machine Learning 스튜디오(클래식) 웹 서비스는 애플리케이션과 Machine Learning 스튜디오(클래식) 워크플로 채점 모델 간의 인터페이스를 제공합니다. 외부 애플리케이션은 Machine Learning 스튜디오(클래식) 워크플로 채점 모델과 실시간으로 통신할 수 있습니다. Machine Learning 스튜디오(클래식) 웹 서비스를 호출하면 외부 애플리케이션에 예측 결과를 반환합니다. 웹 서비스를 호출하려면 웹 서비스를 배포할 때 만들어진 API 키를 전달합니다. Machine Learning 스튜디오(클래식) 웹 서비스는 웹 프로그래밍 프로젝트에 일반적으로 사용되는 아키텍처인 REST를 기반으로 합니다.

Machine Learning 스튜디오(클래식)에는 다음 두 가지 형식의 웹 서비스가 있습니다.

* RRS(요청-응답 서비스): 단일 데이터 레코드의 점수를 매기는 서비스로, 대기 시간이 짧고 스케일링 성능이 뛰어납니다.
* BES(일괄 처리 실행 서비스): 데이터 레코드 일괄 처리의 점수를 매기는 비동기 서비스입니다.

BES에 대한 입력은 RRS에서 사용하는 데이터 입력과 유사합니다. 가장 중요한 차이는 BES에서는 Azure Blob Storage 및 Azure Table Storage, Azure SQL Database, HDInsight(Hive 쿼리), HTTP 소스 등의 다양한 소스에서 레코드 블록을 읽는다는 점입니다.

고급 관점에서 모델을 다음과 같은 3단계로 배포합니다.

* **[학습 실험 만들기]** - 스튜디오(클래식)에서는 다양한 기본 제공 기계 학습 알고리즘 집합을 사용하여 제공하는 학습 데이터를 통해 예측 분석 모델을 학습하고 테스트할 수 있습니다.
* **[예측 실험으로 변환]** - 기존 데이터로 모델을 학습시키고 새 데이터의 점수를 매기는 데 사용할 준비가 되면, 예측을 위해 실험을 준비하고 간소화합니다.
* **[새 웹 서비스]** 또는 **[클래식 웹 서비스]** 로 **배포** - 예측 실험을 Azure 웹 서비스로 배포하는 경우 사용자는 데이터를 모델로 전송하고 모델의 예측을 수신할 수 있습니다.

## <a name="create-a-training-experiment"></a>학습 실험 만들기

예측 분석 모델을 학습하려면 Azure Machine Learning 스튜디오(클래식)를 사용하여, 학습 데이터를 로드하고 필요한 대로 데이터를 준비하며, 기계 학습 알고리즘을 적용하고 결과를 평가하는 다양한 모듈을 포함한 학습 실험을 만듭니다. 실험은 반복할 수 있으며 여러 다른 기계 학습 알고리즘을 시도하여 결과를 비교하고 평가할 수 있습니다.

학습 실험을 만들고 관리하는 프로세스는 다른 부분에서 더욱 철저히 다룹니다. 자세한 내용은 다음 문서를 참조하세요.

* [Machine Learning 스튜디오(클래식)에서 간단한 실험 만들기](create-experiment.md)
* [Machine Learning 스튜디오(클래식)를 사용하여 예측 솔루션 개발하기](tutorial-part1-credit-risk.md)
* [Machine Learning 스튜디오(클래식)에 학습 데이터 가져오기](import-data.md)
* [Machine Learning 스튜디오에서 반복 실험 관리](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>학습 실험에서 예측 실험으로 변환

모델을 학습했으면 학습 실험을 예측 실험으로 변환하여 새 데이터의 점수를 매길 준비가 된 것입니다.

예측 실험으로 변환하면 학습된 모델을 점수 매기기 웹 서비스로 배포할 준비가 완료됩니다. 웹 서비스 사용자가 입력 데이터를 모델로 보내면 모델에서 예측 결과를 다시 전송합니다. 따라서 예측 실험을 변환할 때 다른 사람이 사용할 모델을 어떻게 예측할 것인지를 염두에 두어야 합니다.

학습 실험을 예측 실험으로 변환하는 프로세스는 세 단계로 구성됩니다.

1. 기계 학습 알고리즘 모듈을 학습된 모델로 바꿉니다.
2. 점수 매기기에 필요한 모듈로만 실험을 자릅니다. 학습 실험에는 학습에 필요하지만, 모델을 학습한 후에는 필요 없는 여러 모듈이 포함되어 있습니다.
3. 모델에서 웹 서비스 사용자의 데이터를 받아들이는 방법과 반환할 데이터를 정의합니다.

> [!TIP]
> 학습 실험에서는 사용자 고유의 데이터를 사용하여 모델을 학습하고 점수를 매기는 데 관심이 있지만, 일단 배포되면 사용자는 모델에 새 데이터를 보내고, 모델에서는 예측 결과를 반환합니다. 따라서 학습 실험을 예측 실험으로 변환하여 배포할 준비가 되면 다른 사용자가 이 모델을 어떻게 사용할지를 명심해야 합니다.

![점수 매기기 실험으로 변환](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>웹 서비스 설정 단추
실험 캔버스 아래쪽의 **실행** 을 클릭하여 실험을 실행한 후, **웹 서비스 설정** 단추를 클릭하여 **예측 웹 서비스** 옵션을 선택합니다. **웹 서비스 설정** 에서는 학습 실험을 예측 실험으로 변환하는 세 단계를 수행합니다.

1. 모듈 팔레트의 **학습된 모델** 섹션(실험 캔버스의 왼쪽)에 학습된 모델을 저장합니다. 그런 다음 기계 학습 알고리즘과 [모델 학습][train-model] 모듈을 저장된 학습 모델로 바꿉니다.
2. 실험을 분석하고, 분명히 학습에만 사용되었지만 더 이상 필요하지 않은 모듈을 제거합니다.
3. _웹 서비스 입력_ 및 _출력_ 모듈을 실험의 기본 위치에 삽입합니다(이러한 모듈은 사용자 데이터를 수락하고 반환합니다).

예를 들어 다음 실험은 샘플 인구 조사 데이터를 사용하여 2클래스 향상된 의사 결정 트리 모델을 학습합니다.

![학습 실험](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

이 실험의 모듈은 기본적으로 네 가지 기능을 수행합니다.

![모듈 함수](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

이 학습 실험을 예측 실험으로 변환하면 이러한 모듈 중 일부는 다음과 같이 더 이상 필요하지 않거나 다른 용도로 사용됩니다.

* **데이터** - 이 샘플 데이터 세트의 데이터는 점수를 매기는 동안 사용되지 않습니다. 웹 서비스 사용자가 점수를 매길 데이터를 제공합니다. 그러나 데이터 형식과 같은 이 데이터 세트의 메타데이터는 학습된 모델에서 사용됩니다. 따라서 이 메타데이터를 제공할 수 있도록 예측 실험에서 데이터 세트를 유지해야 합니다.

* **준비** - 점수 매기기를 위해 제출할 사용자 데이터에 따라 이러한 모듈이 들어오는 데이터를 처리하는 데 필요할 수도 있고 그렇지 않을 수도 있습니다. **웹 서비스 설정** 단추는 이러한 모듈을 수정하지 않지만, 이들을 처리할 방법은 결정해야 합니다.
  
    예를 들어 이 예제에서 샘플 데이터 세트에는 누락된 값이 있을 수 있으므로 이를 처리하기 위해 [누락된 데이터 정리][clean-missing-data] 모듈이 포함되어 있습니다. 또한 샘플 데이터 세트에는 모델을 학습하는 데 필요하지 않은 열도 포함되어 있습니다. 따라서 [데이터 세트의 열 선택][select-columns] 모듈이 데이터 흐름에서 이러한 여분의 열을 제외하기 위해 포함되었습니다. 점수 매기기를 위해 웹 서비스를 통해 제출할 데이터에 누락된 값이 있다는 것을 아는 경우 [누락된 데이터 정리][clean-missing-data] 모듈을 제거할 수 있습니다. 그러나 [데이터 세트의 열 선택][select-columns] 모듈은 학습된 모델에 필요한 데이터 열을 정의하는 데 유용하므로 해당 모듈을 유지해야 합니다.

* **학습** - 이러한 모듈은 모델 학습에 사용됩니다. **웹 서비스 설정** 을 클릭하면 이러한 모듈이 학습한 모델을 포함하는 단일 모듈로 바뀝니다. 이 새 모듈은 모듈 팔레트의 **학습한 모델** 섹션에 저장됩니다.

* **점수** - 이 예제에서는 [분할 데이터][split] 모듈을 사용하여 데이터 스트림을 테스트 데이터와 학습 데이터로 나눕니다. 예측 실험에서는 더 이상 학습하지 않으므로 [분할 데이터][split]를 제거할 수 있습니다. 마찬가지로 두 번째 [모델 점수 매기기][score-model] 모듈 및 [모델 평가][evaluate-model] 모듈을 사용하여 테스트 데이터의 결과를 비교하므로 예측 실험에는 이러한 모듈도 필요하지 않습니다. 그러나 나머지 [모델 점수 매기기][score-model] 모듈은 웹 서비스를 통해 점수 결과를 반환하는 데 필요합니다.

다음은 **웹 서비스 설정** 을 클릭한 후 이 예제의 변화된 모양입니다.

![변환된 예측 실험](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

**웹 서비스 설정** 에서 수행한 작업으로 실험을 웹 서비스로 배포하도록 준비하는 데 충분할 수 있습니다. 그러나 실험에 특정한 몇 가지 추가 작업을 수행할 수도 있습니다.

#### <a name="adjust-input-and-output-modules"></a>입력 및 출력 모듈 조정
학습 실험에서는 학습 데이터 집합을 사용한 다음 일부 처리를 수행하여 기계 학습 알고리즘에 필요한 형식의 데이터를 가져왔습니다. 웹 서비스를 통해 받아야 하는 데이터에 이 처리가 필요하지 않으면 무시할 수 있으며, **웹 서비스 입력 모듈** 의 출력을 실험의 다른 모듈에 연결할 수 있습니다. 이제 사용자의 데이터가 이 위치의 모델에 도착합니다.

예를 들어 기본적으로 **웹 서비스 설정** 은 위 그림과 같이 **웹 서비스 입력** 모듈을 데이터 흐름의 맨 위에 배치합니다. 그러나 다음과 같이 **웹 서비스 입력** 을 데이터 처리 모듈 뒤에 수동으로 배치할 수 있습니다.

![웹 서비스 입력 이동](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

이제 웹 서비스를 통해 제공되는 입력 데이터가 전처리 없이 모델 점수 매기기 모듈로 직접 전달됩니다.

마찬가지로 기본적으로 **웹 서비스 설정** 은 웹 서비스 출력 모듈을 데이터 흐름의 맨 아래에 둡니다. 이 예제에서 웹 서비스는 사용자에게 전체 입력 데이터 벡터와 채점 결과가 포함된 [점수 모델][score-model] 모듈의 결과를 반환합니다.
그러나 다른 모듈을 반환하고 싶은 경우에는 **웹 서비스 출력** 모듈 앞에 모듈을 더 추가할 수 있습니다. 

예를 들어 입력 데이터의 전체 벡터가 아닌 점수 매기기 결과만 반환하려면 [데이터 세트의 열 선택][select-columns] 모듈을 추가하여 점수 매기기 결과를 제외한 모든 열을 제외합니다. 그런 다음, **웹 서비스 출력** 모듈을 [데이터 세트의 열 선택][select-columns] 모듈의 출력으로 이동합니다. 실험은 다음과 같습니다.

![웹 서비스 출력 이동](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>추가 데이터 처리 모듈 추가 또는 제거
점수를 매기는 동안 필요 없다는 것을 알고 있는 추가 모듈이 실험에 있는 경우 이러한 모듈을 제거할 수 있습니다. 예를 들어 **웹 서비스 입력** 모듈을 데이터 처리 모듈 이후의 시점으로 이동했기 때문에 예측 실험에서 [누락된 데이터 정리][clean-missing-data] 모듈을 제거할 수 있습니다.

이제 예측 실험은 다음과 같은 모양입니다.

![추가 모듈 제거](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>선택적 웹 서비스 매개 변수 추가
경우에 따라 서비스에 액세스할 때 웹 서비스 사용자가 모듈의 동작을 변경하도록 허용할 수 있습니다. *웹 서비스 매개 변수* 를 통해 이 작업을 수행할 수 있습니다.

일반적인 예는 배포된 웹 서비스에 액세스할 때 해당 웹 서비스의 사용자가 다른 데이터 원본을 지정할 수 있도록 [데이터 가져오기][import-data] 모듈을 설정하는 것입니다. 또는 다른 대상을 지정할 수 있도록 [데이터 내보내기][export-data] 모듈을 구성하는 것입니다.

웹 서비스 매개 변수를 정의하여 하나 이상의 모듈 매개 변수와 연결하고 이러한 매개 변수가 필수인지 또는 선택 사항인지 지정할 수 있습니다. 웹 서비스의 사용자는 서비스에 액세스할 때 이러한 매개 변수의 값을 제공하며, 이에 따라 모듈 작업이 수정됩니다.

웹 서비스 매개 변수 및 사용 방법에 대한 자세한 내용은 [Machine Learning 웹 서비스 매개 변수 사용][webserviceparameters]을 참조하세요.

다음 단계에서는 예측 실험을 새 웹 서비스로 배포하는 방법을 설명합니다. 실험을 기존 웹 서비스로 배포할 수도 있습니다.

## <a name="deploy-it-as-a-new-web-service"></a>새 웹 서비스로 배포

이제 예측 실험이 준비되었으므로 이를 새 Azure 웹 서비스(Resource Manager 기반)로 배포할 수 있습니다. 웹 서비스를 사용하면 사용자가 모델에 데이터를 보낼 수 있으며, 이 경우 모델에서 해당 예측을 반환합니다.

예측 실험을 배포하려면 실험 캔버스의 맨 아래에서 **실행** 을 클릭합니다. 실험 실행이 완료되면 **웹 서비스 배포** 를 클릭하고 **웹 서비스 배포 [신규]** 를 선택합니다.  Machine Learning 스튜디오(클래식) 웹 서비스 포털의 배포 페이지가 열립니다.

> [!NOTE] 
> 새 웹 서비스를 배포하려면 웹 서비스를 배포하려는 구독에 충분한 권한이 있어야 합니다. 자세한 내용은 [Machine Learning 웹 서비스 포털에서 웹 서비스 관리](manage-new-webservice.md)를 참조하세요. 

### <a name="web-service-portal-deploy-experiment-page"></a>웹 서비스 포털 실험 배포 페이지

실험 배포 페이지에서 웹 서비스의 이름을 입력합니다.
가격 책정 계획을 선택합니다. 기존 가격 책정 계획이 있는 경우 해당 계획을 선택하고 그렇지 않으면 서비스에 대한 새 가격 계획을 만들어야 합니다.

1. **가격 계획** 드롭다운에서 기존 계획을 선택하거나 **새 계획 선택** 옵션을 선택합니다.
2. **계획 이름** 에 청구서의 계획을 식별하는 이름을 입력합니다.
3. **월별 계획 계층** 중 하나를 선택합니다. 계획 계층이 기본적으로 기본 하위 지역에 대한 계획으로 설정되고 웹 서비스는 해당 하위 지역에 배포됩니다.

**배포** 를 클릭하면 웹 서비스에 대한 **빠른 시작** 페이지가 열립니다.

웹 서비스 빠른 시작 페이지를 사용하면 웹 서비스를 만든 후 수행할 가장 일반적인 작업에 대한 액세스 권한 및 지침을 제공합니다. 여기에서 테스트 페이지 및 사용 페이지에 모두 쉽게 액세스할 수 있습니다.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>새 웹 서비스 테스트

새 웹 서비스를 테스트하려면 일반적인 작업에서 **웹 서비스 테스트** 를 클릭합니다. 테스트 페이지에서 웹 서비스를 RRS(요청-응답 서비스) 또는 BES(일괄 처리 실행 서비스)로 테스트할 수 있습니다.

RRS 테스트 페이지에서는 실험에 대해 정의된 입력, 출력 및 모든 전역 매개 변수를 표시합니다. 웹 서비스를 테스트하려면 입력에 적절한 값을 수동으로 입력하거나 테스트 값을 포함하는 CSV(쉼표로 구분된 값) 형식의 파일을 제공할 수 있습니다.

RRS를 테스트하려면 목록 보기 모드에서 입력에 적절한 값을 입력하고 **요청-응답 테스트** 를 클릭합니다. 예측 결과는 왼쪽의 출력 열에 표시됩니다.

![적절한 값을 입력하여 웹 서비스 테스트](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

BES를 테스트하려면 **Batch** 를 클릭합니다. Batch 테스트 페이지의 입력에서 찾아보기를 클릭하고 적절한 샘플 값이 포함된 CSV 파일을 선택합니다. CSV 파일이 없고 Machine Learning 스튜디오(클래식)를 사용하여 예측 실험을 만든 경우 예측 실험에 대한 데이터 세트를 다운로드하여 사용할 수 있습니다.

데이터 세트를 다운로드하려면 Machine Learning 스튜디오(클래식)를 엽니다. 예측 실험을 열고 실험에 대한 입력을 마우스 오른쪽 단추로 클릭합니다. 상황에 맞는 메뉴에서 **데이터 세트** 를 선택한 다음, **다운로드** 를 선택합니다.

![스튜디오(클래식) 캔버스에서 데이터 세트 다운로드](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

**테스트** 를 클릭합니다. Batch 실행 작업의 상태는 **Batch 작업 테스트** 의 오른쪽에 표시됩니다.

![웹 서비스 포털을 사용하여 Batch 실행 작업 테스트](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

**구성** 페이지에서 설명, 제목을 변경하고 스토리지 계정 키를 업데이트하며 웹 서비스에 대한 샘플 데이터를 사용하도록 설정할 수 있습니다.

![웹 서비스 구성](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>새 웹 서비스 액세스

Machine Learning 스튜디오(클래식)에서 웹 서비스를 배포하면 서비스에 데이터를 보내고 프로그래밍 방식으로 응답을 받을 수 있습니다.

**사용** 페이지에서는 웹 서비스에 액세스하는 데 필요한 모든 정보를 제공합니다. 예를 들어, 서비스에 대한 권한이 부여된 액세스를 허용하도록 API 키를 제공합니다.

Machine Learning 스튜디오(클래식) 웹 서비스 액세스에 대한 자세한 내용은 [Machine Learning 스튜디오(클래식) 웹 서비스를 사용하는 방법](consume-web-services.md)을 참조하세요.

### <a name="manage-your-new-web-service"></a>새 웹 서비스 관리

Machine Learning 스튜디오(클래식) 웹 서비스 포털을 사용하여 새 웹 서비스를 관리할 수 있습니다. [기본 포털 페이지](https://services.azureml.net/)에서 **웹 서비스** 를 클릭합니다. 웹 서비스 페이지에서 서비스를 삭제하거나 복사할 수 있습니다. 특정 서비스를 모니터링하려면 서비스를 클릭한 다음 **대시보드** 를 클릭합니다. 웹 서비스와 연결된 배치 작업을 모니터링하려면 **Batch 요청 로그** 를 클릭합니다.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> 여러 지역에 새 웹 서비스 배포

여러 지역에 웹 서비스를 쉽게 배포할 수 있으며 여러 구독 또는 작업 영역이 필요하지 않습니다.

가격 책정은 지역별로 이루어집니다. 따라서 웹 서비스를 배포할 각 지역에 대한 청구 계획을 정의해야 합니다.

#### <a name="create-a-plan-in-another-region"></a>다른 지역에서 계획 만들기

1. [Machine Learning 웹 서비스](https://services.azureml.net/)에 로그인합니다.
2. **계획** 메뉴 옵션을 클릭합니다.
3. 보기 페이지의 계획에서 **새로 만들기** 를 클릭합니다.
4. **구독** 드롭다운에서 새 계획이 상주할 구독을 선택합니다.
5. **지역** 드롭다운에서 새 계획에 대한 지역을 선택합니다. 선택한 영역에 대한 계획 옵션은 페이지의 **계획 옵션** 섹션에 표시됩니다.
6. **리소스 그룹** 드롭다운에서 계획에 대한 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.
7. **계획 이름** 에 계획의 이름을 입력합니다.
8. **계획 옵션** 에서 새 계획에 대한 청구 수준을 클릭합니다.
9. **만들기** 를 클릭합니다.

#### <a name="deploy-the-web-service-to-another-region"></a>다른 지역에 웹 서비스 배포

1. Machine Learning 웹 서비스 페이지에서 **웹 서비스** 메뉴 옵션을 클릭합니다.
2. 새 지역에 배포하는 웹 서비스를 선택합니다.
3. **복사** 를 클릭합니다.
4. **웹 서비스 이름** 에 웹 서비스의 새 이름을 입력합니다.
5. **웹 서비스 설명** 에 웹 서비스에 대한 설명을 입력합니다.
6. **구독** 드롭다운에서 새 웹 서비스가 상주할 구독을 선택합니다.
7. **리소스 그룹** 드롭다운에서 웹 서비스에 대한 리소스 그룹을 선택합니다. 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.
8. **지역** 드롭다운에서 웹 서비스를 배포할 지역을 선택합니다.
9. **Storage 계정** 드롭다운에서 웹 서비스에 저장할 Storage 계정을 선택합니다.
10. **가격 계획** 드롭다운에서 8단계에서 선택한 영역에서 계획을 선택합니다.
11. **복사** 를 클릭합니다.

## <a name="deploy-it-as-a-classic-web-service"></a>기존 웹 서비스로 배포

이제 예측 실험이 충분히 준비되었으므로 이를 기존 Azure 웹 서비스로 배포할 수 있습니다. 웹 서비스를 사용하면 사용자가 모델에 데이터를 보낼 수 있으며, 이 경우 모델에서 해당 예측을 반환합니다.

예측 실험을 배포하려면 실험 캔버스의 맨 아래에서 **실행** 을 클릭한 다음 **웹 서비스 배포** 를 클릭합니다. 웹 서비스가 설정되고 웹 서비스 대시보드에 배치됩니다.

![스튜디오(클래식)에서 웹 서비스 배포](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>기존 웹 서비스 테스트

Machine Learning 스튜디오(클래식) 웹 서비스 포털 또는 Machine Learning 스튜디오(클래식)에서 웹 서비스를 테스트할 수 있습니다.

리소스 요청 웹 서비스를 테스트하려면 웹 서비스 대시보드에서 **테스트** 단추를 클릭합니다. 서비스에 대한 입력 데이터를 요청하는 대화 상자가 나타납니다. 이러한 열이 점수 매기기 실험에 필요합니다. 데이터 집합을 입력하고 **확인** 을 클릭합니다. 웹 서비스에서 생성된 결과가 대시보드 아래쪽에 표시됩니다.

이전에 새 웹 서비스 섹션에서 설명한 것처럼 Machine Learning 스튜디오(클래식) 웹 서비스 포털에서 **테스트** 미리 보기 링크를 클릭하여 서비스를 테스트할 수 있습니다.

Batch 실행 서비스를 테스트하려면 **테스트** 미리 링크를 클릭합니다. Batch 테스트 페이지의 입력에서 찾아보기를 클릭하고 적절한 샘플 값이 포함된 CSV 파일을 선택합니다. CSV 파일이 없고 Machine Learning 스튜디오(클래식)를 사용하여 예측 실험을 만든 경우 예측 실험에 대한 데이터 세트를 다운로드하여 사용할 수 있습니다.

![웹 서비스 테스트](./media/publish-a-machine-learning-web-service/figure-3.png)

**구성** 페이지에서 서비스의 표시 이름을 변경하고 설명을 제공할 수 있습니다. 웹 서비스를 관리하는 [Azure Portal](https://portal.azure.com/)에 이름과 설명이 표시됩니다.

**입력 스키마**, **출력 스키마** 및 **웹 서비스 매개 변수** 의 각 열에 문자열을 입력하여 입력 데이터, 출력 데이터 및 웹 서비스 매개 변수에 대한 설명을 제공할 수 있습니다. 이러한 설명은 웹 서비스에 제공된 샘플 코드 설명서에서 사용합니다.

로깅을 사용하도록 설정하면 웹 서비스에 액세스할 때 나타나는 실패를 진단할 수 있습니다. 자세한 내용은 [Machine Learning 스튜디오(클래식) 웹 서비스에 대해 로깅 사용](web-services-logging.md)을 참조하세요.

![웹 서비스 포털에서 로깅 사용](./media/publish-a-machine-learning-web-service/figure-4.png)

또한 앞서 새 웹 서비스 섹션에 나온 절차와 유사하게 Machine Learning 웹 서비스 포털에서 웹 서비스에 대한 엔드포인트를 구성할 수 있습니다. 옵션은 여러 가지가 있습니다. 서비스 설명을 추가하거나 변경하고, 로깅을 사용하도록 설정하고, 테스트에 사용할 샘플 데이터를 사용하도록 설정할 수 있습니다.

### <a name="access-your-classic-web-service"></a>기존 웹 서비스 액세스

Machine Learning 스튜디오(클래식)에서 웹 서비스를 배포하면 서비스에 데이터를 보내고 프로그래밍 방식으로 응답을 받을 수 있습니다.

대시보드에서는 웹 서비스에 액세스하는 데 필요한 모든 정보를 제공합니다. 예를 들어, 서비스에 대해 권한 부여된 액세스가 가능하도록 API 키가 제공되고, 코드 작성을 시작하는 데 도움이 되도록 API 도움말 페이지가 제공됩니다.

Machine Learning 스튜디오(클래식) 웹 서비스 액세스에 대한 자세한 내용은 [Machine Learning 스튜디오(클래식) 웹 서비스를 사용하는 방법](consume-web-services.md)을 참조하세요.

### <a name="manage-your-classic-web-service"></a>기존 웹 서비스 관리

다양한 작업을 통해 웹 서비스를 모니터링할 수 있습니다. 업데이트 및 삭제할 수 있습니다. 배포할 때 생성되는 기본 엔드포인트 외에, 기존 웹 서비스에 추가 엔드포인트도 추가할 수 있습니다.

자세한 내용은 [Machine Learning 스튜디오(클래식) 작업 영역 관리](manage-workspace.md) 및 [Machine Learning 스튜디오(클래식) 웹 서비스 포털을 사용하여 웹 서비스 관리](manage-new-webservice.md)를 참조하세요.

## <a name="update-the-web-service"></a>웹 서비스 업데이트
추가 학습 데이터로 모델 업데이트와 같은 방식으로 웹 서비스를 변경하고 다시 배포하여 원래 웹 서비스를 덮어쓸 수 있습니다.

웹 서비스를 업데이트하려면 웹 서비스를 배포하는 데 사용한 원래 예측 실험을 열고 **다른 이름으로 저장** 을 클릭하여 편집 가능한 사본을 만듭니다. 변경한 다음 **웹 서비스 배포** 를 클릭합니다.

이 실험을 이전에 배포했기 때문에 기존 서비스를 덮어쓰거나(기존 웹 서비스) 업데이트(새 웹 서비스)할지 묻는 메시지가 표시됩니다. **예** 또는 **업데이트** 를 클릭하여 기존 웹 서비스를 중지하고 대신 새 예측 실험을 배포합니다.

> [!NOTE]
> 예를 들어, 새 표시 이름 또는 설명을 입력하여 원래 웹 서비스의 구성을 변경한 경우 해당 값을 다시 입력해야 합니다.

웹 서비스를 업데이트하기 위한 한 가지 옵션은 프로그래밍 방식으로 모델을 다시 학습하는 것입니다. 자세한 내용은 [프로그래밍 방식으로 Machine Learning 스튜디오(클래식) 모델 다시 학습](./retrain-machine-learning-model.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 배포 방식에 대한 자세한 기술 정보는 [실험에서 조작 가능한 웹 서비스까지 Machine Learning 스튜디오(클래식) 모델의 진행 과정](model-progression-experiment-to-web-service.md)을 참조하세요.

* 모델 배포를 준비하는 방법에 대한 자세한 내용은 [Machine Learning 스튜디오(클래식)에서 모델을 배포하기 위해 준비하는 방법](deploy-a-machine-learning-web-service.md)을 참조하세요.

* REST API를 사용하고 웹 서비스에 액세스하는 여러 가지 방법이 있습니다. [Machine Learning 스튜디오(클래식) 웹 서비스를 사용하는 방법](consume-web-services.md)을 참조하세요.

<!-- internal links -->
[학습 실험 만들기]: #create-a-training-experiment
[예측 실험으로 변환]: #convert-the-training-experiment-to-a-predictive-experiment
[새 웹 서비스]: #deploy-it-as-a-new-web-service
[기존 웹 서비스]: #deploy-it-as-a-classic-web-service
[new]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data