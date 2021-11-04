---
title: 디자이너 구성 요소 오류 문제 해결
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Designer에서 자동화된 구성 요소 오류 코드를 읽고 해결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: troubleshooting
author: likebupt
ms.author: keli19
ms.date: 03/25/2021
ms.openlocfilehash: 8ab0c27f8e27737c9208d77e9d1a1bd50d79afd0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570065"
---
# <a name="exceptions-and-error-codes-for-the-designer"></a>디자이너의 예외 및 오류 코드

이 문서에서는 기계 학습 파이프라인의 문제를 해결하는 데 도움이 되는 Azure Machine Learning 디자이너의 오류 메시지 및 예외 코드를 설명합니다.

다음 단계들을 수행하면 디자이너에서 오류 메시지를 찾을 수 있습니다.  

- 실패한 구성 요소를 선택하고 **출력 +로그** 탭으로 이동하면 **azureml-logs** 범주 **아래의70_driver_log.txt** 파일에서 자세한 로그를 찾을 수 있습니다.

- 자세한 구성 요소 오류는 module_statistics **범주의** error_info.json에서 확인할 수 있습니다.

다음은 디자이너 구성 요소의 오류 코드입니다.

## <a name="error-0001"></a>오류 0001  
 데이터 세트의 지정된 열 중 하나 이상을 찾을 수 없으면 예외가 발생합니다.  

 구성 요소에 대해 열을 선택했지만 선택한 열이 입력 데이터 집합에 없는 경우 이 오류가 표시됩니다. 이 오류는 열 이름을 수동으로 입력한 경우 또는 파이프라인 실행 시 열 선택기에서 데이터 세트에 존재하지 않는 열을 제공한 경우 발생할 수 있습니다.  

**해결 방법:** 이 예외를 throw하는 구성 요소를 다시 확인하고 열 이름 또는 이름이 올바르고 참조된 모든 열이 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|하나 이상의 지정된 열을 찾을 수 없습니다.|
|이름 또는 인덱스가 "{column_id}"인 열을 찾을 수 없습니다.|
|이름 또는 인덱스가 "{column_id}"인 열이 "{arg_name_missing_column}"에 없습니다.|
|이름 또는 인덱스가 "{column_id}"인 열이 "{arg_name_missing_column}"에 없으며 다만 "{arg_name_has_column}"에는 있습니다.|
|이름 또는 인덱스가 "{column_names}"인 열을 찾을 수 없습니다.|
|이름 또는 인덱스가 "{column_names}"인 열이 "{arg_name_missing_column}"에 없습니다.|
|이름 또는 인덱스가 "{column_names}"인 열이 "{arg_name_missing_column}"에 없으며 다만 "{arg_name_has_column}"에는 있습니다.|


## <a name="error-0002"></a>오류 0002  
 하나 이상의 매개 변수를 구문 분석할 수 없거나 지정한 형식에서 대상 방법 유형에 필요한 형식으로 변환할 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 매개 변수를 입력으로 지정하고 값 형식이 예상되는 형식과 차이가 있으며 암시적 변환을 수행할 수 없을 때 발생합니다.  

**해결 방법:** 구성 요소 요구 사항을 확인하고 필요한 값 형식(문자열, 정수, double 등)을 확인합니다.  

|예외 메시지|
|------------------------|
|매개 변수를 구문 분석하지 못했습니다.|
|"{arg_name_or_column}" 매개 변수를 구문 분석하지 못했습니다.|
|"{arg_name_or_column}" 매개 변수를 "{to_type}"(으)로 변환하지 못했습니다.|
|"{arg_name_or_column}" 매개 변수를 "{from_type}"에서 "{to_type}"(으)로 변환하지 못했습니다.|
|"{arg_name_or_column}" 매개 변수 값 "{arg_value}"을(를) "{from_type}"에서 "{to_type}"(으)로 변환하지 못했습니다.|
|제공된 "{fmt}" 형식을 사용하여 "{arg_name_or_column}" 열의 "{arg_value}" 값을 "{from_type}"에서 "{to_type}"(으)로 변환하지 못했습니다.|


## <a name="error-0003"></a>오류 0003  
 하나 이상의 입력이 null이거나 비어 있으면 예외가 발생합니다.  

 구성 요소에 대한 입력 또는 매개 변수가 null이거나 비어 있으면 Azure Machine Learning 이 오류가 표시됩니다.  예를 들어, 매개 변수에 대한 값을 입력하지 않을 때 이 오류가 발생할 수 있습니다. 또한 값이 누락된 데이터 세트 또는 빈 데이터 세트를 선택한 경우에도 이 오류가 발생할 수 있습니다.  

**해결 방법:**

+ 예외를 생성한 구성 요소를 열고 모든 입력이 지정되었는지 확인합니다. 모든 필수 입력이 지정되어 있는지 확인합니다. 
+ Azure Storage에서 로드된 데이터에 액세스할 수 있는지 그리고 계정 이름 또는 키가 변경되지 않았는지 확인합니다.  
+ 입력 데이터의 값이 누락되었거나 null인지 확인합니다.
+ 데이터 원본에 대한 쿼리를 사용하는 경우, 데이터가 예상되는 형식으로 반환되는지 확인합니다. 
+ 데이터 사양에 오타가 있거나 그 외 변경 사항이 있는지 확인합니다.
  
|예외 메시지|
|------------------------|
|하나 이상의 입력이 null이거나 비어 있습니다.|
|입력 "{name}"이(가) null이거나 비어 있습니다.|


## <a name="error-0004"></a>오류 0004  
 매개 변수가 특정 값 이하이면 예외가 발생합니다.  

 메시지의 매개 변수가 구성 요소에서 데이터를 처리하는 데 필요한 경계 값보다 낮으면 Azure Machine Learning 이 오류가 표시됩니다.  

**해결 방법:** 예외를 throw하는 구성 요소를 다시 방문하고 매개 변수를 지정된 값보다 크도록 수정합니다.  

|예외 메시지|
|------------------------|
|매개 변수는 경계 값보다 커야 합니다.|
|"{arg_name}" 매개 변수 값은 {lower_boundary}보다 커야 합니다.|
|"{arg_name}" 매개 변수의 값은 "{actual_value}"이며 이 값은 {lower_boundary}보다 커야 합니다.|


## <a name="error-0005"></a>오류 0005  
 매개 변수가 특정 값보다 작으면 예외가 발생합니다.  

 메시지의 매개 변수가 구성 요소에서 데이터를 처리하는 데 필요한 경계 값 이하이거나 같으면 Azure Machine Learning 이 오류가 발생합니다.  

**해결 방법:** 예외를 throw하는 구성 요소를 다시 방문하고 매개 변수를 지정된 값보다 크거나 같도록 수정합니다.  

|예외 메시지|
|------------------------|
|매개 변수는 경계 값과 같거나 그보다 커야 합니다.|
|"{arg_name}" 매개 변수 값은 {lower_boundary} 값과 같거나 그보다 커야 합니다.|
|"{arg_name}" 매개 변수 값은 "{value}"이며 이 값은 {lower_boundary} 값과 같거나 그보다 커야 합니다.|


## <a name="error-0006"></a>오류 0006  
 매개 변수가 지정된 값과 같거나 그보다 크면 예외가 발생합니다.  

 메시지의 매개 변수가 구성 요소에서 데이터를 처리하는 데 필요한 경계 값보다 크거나 같으면 Azure Machine Learning 이 오류가 표시됩니다.  

**해결 방법:** 예외를 throw하는 구성 요소를 다시 방문하고 매개 변수를 지정된 값보다 작은 값으로 수정합니다.  

|예외 메시지|
|------------------------|
|매개 변수가 일치하지 않습니다. 매개 변수 중 하나는 다른 매개 변수보다 작아야 합니다.|
|"{arg_name}" 매개 변수 값은 "{upper_boundary_parameter_name}" 매개 변수 값보다 작아야 합니다.|
|"{arg_name}" 매개 변수 값은 "{value}"이며 이 값은 {upper_boundary_parameter_name} 값보다 작아야 합니다.|


## <a name="error-0007"></a>오류 0007  
 매개 변수가 특정 값보다 크면 예외가 발생합니다.  

 구성 요소의 속성에서 허용되는 값보다 큰 값을 지정한 경우 Azure Machine Learning 이 오류가 발생합니다. 예를 들어 지원되는 날짜의 범위를 벗어난 데이터를 지정할 수 있으며, 또는 3개의 열만 사용 가능한 경우에는 5개의 열을 사용할 것을 나타낼 수 있습니다. 

 어떤 면에서 서로 일치해야 하는 두 데이터 세트를 지정하는 경우에도 이 오류가 나타날 수 있습니다. 예를 들어, 열의 이름을 바꾸고 인덱스에 따라 열을 지정하는 경우에는 사용자가 제공하는 이름의 수가 열 인덱스 수와 일치해야 합니다. 2개의 열을 사용하는 수학 연산은 또 다른 예가 될 수 있는데, 이러한 연산에서 각 열에는 동일한 수의 행이 있어야 합니다. 

**해결 방법:**

 + 해당 구성 요소를 열고 숫자 속성 설정을 검토합니다.
 + 모든 매개 변수 값이 해당 속성에 대해 지원되는 값의 범위 내에 있는지 확인합니다.
 + 구성 요소가 여러 입력을 받는 경우 입력 크기가 같은지 확인합니다.
<!-- + If the component has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + 데이터 세트 또는 데이터 원본이 변경되었는지 여부를 확인합니다. 열 수, 열 데이터 형식 또는 데이터 크기가 변경된 후에도 경우에 따라서는 이전 버전의 데이터에서 사용된 값이 실패합니다.  

|예외 메시지|
|------------------------|
|매개 변수가 일치하지 않습니다. 매개 변수 중 하나는 다른 매개 변수와 같거나 그보다 작아야 합니다.|
|"{arg_name}" 매개 변수 값은 "{upper_boundary_parameter_name}" 매개 변수 값과 같거나 그보다 작아야 합니다.|
|"{arg_name}" 매개 변수 값은 "{actual_value}"이며 이 값은 {upper_boundary} 값과 같거나 그보다 작아야 합니다.|
|"{arg_name}" 매개 변수 값 {actual_value}은(는) "{upper_boundary_parameter_name}" 매개 변수 값 {upper_boundary}와(과) 같거나 그보다 작아야 합니다.|
|"{arg_name}" 매개 변수 값 "{actual_value}"은(는) {upper_boundary_meaning} 값인 {upper_boundary}와 같거나 그보다 작아야 합니다.|


## <a name="error-0008"></a>오류 0008  
 매개 변수가 범위 내에 있지 않으면 예외가 발생합니다.  

 메시지의 매개 변수가 구성 요소에서 데이터를 처리하는 데 필요한 범위를 벗어나면 Azure Machine Learning 이 오류가 표시됩니다.  

 예를 들어 [행 추가](add-rows.md)를 사용하여 열 수가 다른 두 데이터 세트를 결합하려고 할 경우, 이 오류가 표시됩니다.  

**해결 방법:** 예외를 throw하는 구성 요소를 다시 방문하고 매개 변수를 지정된 범위 내에 있도록 수정합니다.  

|예외 메시지|
|------------------------|
|매개 변수 값이 지정된 범위에 없습니다.|
|"{arg_name}" 매개 변수 값이 범위에 없습니다.|
|"{arg_name}" 매개 변수 값은 [{lower_boundary}, {upper_boundary}]의 범위 내에 있어야 합니다.|
|"{arg_name}" 매개 변수 값이 범위에 없습니다. {reason}|


## <a name="error-0009"></a>오류 0009  
 Azure Storage 계정 이름 또는 컨테이너 이름을 잘못 지정할 때 예외가 발생합니다.  

이 오류는 Azure Storage 계정에 대한 매개 변수를 지정할 때 Azure Machine Learning 디자이너에서 발생하며, 다만 그 이름 또는 암호를 확인할 수 없습니다. 암호 또는 계정 이름에 대한 오류는 다음과 같은 여러 가지 이유로 발생할 수 있습니다.

 + 계정의 형식이 잘못된 경우. Azure Machine Learning 디자이너에서 사용할 수 있는 몇 가지 새로운 계정 유형이 지원되지 않습니다. 자세한 내용은 [데이터 가져오기](import-data.md)를 참조하세요.
 + 잘못된 계정 이름을 입력한 경우
 + 계정이 더 이상 존재하지 않음
 + 스토리지 계정에 대한 암호가 잘못되었거나 변경된 경우
 + 컨테이너 이름을 지정하지 않았거나 컨테이너가 존재하지 않는 경우
 + 파일 경로(Blob에 대한 경로)를 완전히 지정하지 않은 경우
   

**해결 방법:**

이러한 문제들은 계정 이름, 암호 또는 컨테이너 경로를 수동으로 입력하려고 할 때 발생하는 경우가 많습니다. 이름을 조회하고 확인하는 데 도움이 되는 [데이터 가져오기](import-data.md) 구성 요소에 새 마법사를 사용하는 것이 좋습니다.

계정, 컨테이너 또는 Blob이 삭제되었는지 여부도 확인합니다. 다른 Azure Storage 유틸리티를 사용하여 계정 이름과 암호가 올바르게 입력되었으며 컨테이너가 존재하는지 확인합니다. 

Azure Machine Learning에서는 일부 최신 계정 유형이 지원되지 않습니다. 예를 들어, 새로운 "핫" 또는 "콜드" 스토리지 유형은 기계 학습에 사용할 수 없습니다. 클래식 스토리지 계정과 "범용"으로 만들어진 스토리지 계정은 모두 제대로 작동합니다.

Blob에 대한 전체 경로가 지정된 경우, 이 경로가 **container/blobname** 으로 지정되어 있는지 그리고 컨테이너와 Blob이 모두 계정 내에 존재하는지 확인합니다.  

 이 경로에는 선행 슬래시가 포함되어서는 안 됩니다. 예를 들어, **/container/blob** 은 잘못된 입력이며 **container/blob** 으로 입력해야 합니다.  


|예외 메시지|
|------------------------|
|Azure Storage 계정 이름 또는 컨테이너 이름이 올바르지 않습니다.|
|Azure Storage 계정 이름 "{account_name}" 또는 컨테이너 이름 "{container_name}"이(가) 잘못되었습니다. 컨테이너/Blob 형식의 컨테이너 이름을 사용해야 합니다.|


## <a name="error-0010"></a>오류 0010  
 입력 데이터 세트에서 일치해야 할 열 이름이 일치하지 않을 경우, 예외가 발생합니다.  

 메시지의 열 인덱스에 두 입력 데이터 세트의 열 이름이 서로 다를 경우, 이 오류가 Azure Machine Learning에 표시됩니다.  

**해결 방법:** [메타데이터 편집](edit-metadata.md)을 사용하거나 지정된 열 인덱스에 대해 동일한 열 이름을 갖도록 원본 데이터 세트를 수정하세요.  

|예외 메시지|
|------------------------|
|입력 데이터 세트에서 해당하는 인덱스의 열 이름이 다릅니다.|
|입력 데이터 세트({dataset1} 및 {dataset2})의 {col_index}(0부터 시작) 열에 대한 열 이름이 동일하지 않습니다.|


## <a name="error-0011"></a>오류 0011  
 전달된 열 집합 인수가 데이트 세트 열에 적용되지 않으면 예외가 발생합니다.  

 지정된 열 선택이 주어진 데이터 세트의 열과 일치하지 않을 경우, Azure Machine Learning에서 이 오류가 표시됩니다.  

 구성 요소가 작동하려면 열을 선택하지 않았고 하나 이상의 열이 필요한 경우에도 이 오류가 발생할 수 있습니다.  

**해결 방법:** 데이터 세트의 열에 적용되도록 구성 요소의 열 선택을 수정합니다.  

 구성 요소에서 레이블 열과 같은 특정 열을 선택해야 하는 경우 올바른 열이 선택되어 있는지 확인합니다.  

 부적절한 열을 선택한 경우, 이 열을 제거하고 파이프라인을 다시 실행합니다.  

|예외 메시지|
|------------------------|
|지정된 열 집합이 데이터 세트 열에 적용되지 않습니다.|
|지정된 열 집합 "{column_set}"이(가) 데이터 세트 열에 적용되지 않습니다.|


## <a name="error-0012"></a>오류 0012  
 전달된 인수 집합을 사용하여 클래스 인스턴스를 만들 수 없는 경우에 예외가 발생합니다.  

**해결 방법:** 이 오류는 사용자가 조치를 취할 수 없으며 이후 릴리스에서 더 이상 사용되지 않습니다.  

|예외 메시지|
|------------------------|
|학습되지 않은 모델입니다. 먼저 모델 학습을 진행하세요.|
|학습되지 않은 모델 ({arg_name})입니다. 학습된 모델을 사용하세요.|


## <a name="error-0013"></a>오류 0013  
 구성 요소에 전달된 학습자가 잘못된 형식인 경우 예외가 발생합니다.  

 이 오류는 학습된 모델이 연결된 채점 구성 요소와 호환되지 않는 경우에 발생합니다. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**해결 방법:**

학습 구성 요소에서 생성되는 학습자 유형을 결정하고 학습자에게 적합한 점수 매기기 구성 요소를 결정합니다. 

특수 학습 구성 요소를 사용하여 모델을 학습한 경우 학습된 모델을 해당하는 특수 점수 매기기 구성 요소에만 연결합니다. 


|모델 형식|학습 구성 요소| 점수 매기기 구성 요소|
|----|----|----|
|분류자|[모델 학습](train-model.md) |[모델 채점](score-model.md)|
|회귀 모델|[모델 학습](train-model.md) |[모델 채점](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The component does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|예외 메시지|
|------------------------|
|잘못된 유형의 학습자가 전달되었습니다.|
|학습자 "{arg_name}"의 유형이 잘못되었습니다.|
|학습자 "{arg_name}"의 "{learner_type}" 유형이 잘못되었습니다.|
|잘못된 유형의 학습자가 전달되었습니다. 예외 메시지: {exception_message}|


## <a name="error-0014"></a>오류 0014  
 열의 고유 값 수가 허용되는 것보다 많으면 예외가 발생합니다.  

 이 오류는 열에 ID 열 또는 텍스트 열과 같은 고유 값이 너무 많은 경우에 발생합니다. 하나의 열이 범주형 데이터로 처리되도록 지정했지만 해당 열에 너무 많은 고유 값이 있어 처리를 완료할 수 없는 경우, 이 오류가 표시될 수 있습니다. 두 입력의 고유 값 개수가 서로 일치하지 않을 경우에도 이 오류가 표시될 수 있습니다.   

다음 두 조건을 **모두** 충족하는 경우 고유 값의 오류가 허용되는 것보다 큽니다.

- 하나의 열에 있는 97% 이상의 인스턴스가 고유 값이므로 거의 모든 범주가 서로 다릅니다.
- 하나의 열에 1000개 이상의 고유 값이 있습니다.

**해결 방법:**

오류를 생성한 구성 요소를 열고 입력으로 사용되는 열을 식별합니다. 일부 구성 요소의 경우 데이터 세트 입력을 마우스 오른쪽 단추로 클릭하고 **시각화를** 선택하여 고유 값의 수 및 해당 분포를 포함하여 개별 열에 대한 통계를 얻을 수 있습니다.

그룹화 또는 분류에 사용할 열의 경우, 각 열에서 고유한 값의 수를 줄이는 단계를 수행합니다. 그 수는 열의 데이터 형식에 따라 여러 가지 방법으로 줄일 수 있습니다. 

모델을 학습하는 동안 의미 없는 기능인 ID 열의 경우 [메타데이터 편집](../algorithm-module-reference/edit-metadata.md)을 사용하여 해당 열을 **명확한 기능** 으로 표시하면 모델을 학습하는 동안 이 열이 사용되지 않습니다. 

텍스트 열의 경우 [기능 해싱](../algorithm-module-reference/feature-hashing.md) 또는 [텍스트 구성 요소에서 N-Gram 기능 추출을](../algorithm-module-reference/extract-n-gram-features-from-text.md) 사용하여 텍스트 열을 전처리할 수 있습니다.
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> 시나리오와 일치하는 해결 방법을 찾을 수 없습니까? 오류를 생성한 구성 요소의 이름과 열의 데이터 형식 및 카디널리티를 포함하는 이 항목에 대한 피드백을 제공할 수 있습니다. 여기서는 일반적인 시나리오에 대한 대상으로 지정된 문제 해결 단계를 더 많이 제공하기 위해 이 정보를 사용합니다.   

|예외 메시지|
|------------------------|
|열의 고유 값 수가 허용되는 것보다 많습니다.|
|열의 고유 값 수: "{column_name}"이(가) 허용되는 것보다 많습니다.|
|열의 고유 값 수: "{column_name}"이(가) {limitation}의 튜플 개수를 초과합니다.|


## <a name="error-0015"></a>오류 0015  
 데이터베이스 연결이 실패하면 예외가 발생합니다.  

 잘못된 SQL 계정 이름, 암호, 데이터베이스 서버 또는 데이터베이스 이름을 입력하거나 데이터베이스 또는 서버 문제로 인해 데이터베이스와의 연결을 설정할 수 없는 경우, 이 오류가 표시됩니다.  

**해결 방법:** 계정 이름, 암호, 데이터베이스 서버 및 데이터베이스를 올바르게 입력했는지 그리고 지정된 계정에 올바른 수준의 권한이 있는지 각각 확인하세요. 현재 데이터베이스에 액세스할 수 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|데이터베이스에 연결하는 중에 오류가 발생했습니다.|
|데이터베이스에 연결하는 중에 {connection_str} 오류가 발생했습니다.|


## <a name="error-0016"></a>오류 0016  
 구성 요소에 전달 된 입력 데이터 집합의 열 유형이 호환 되어야 하지만 그렇지 않은 경우 예외가 발생 합니다.  

 두 개 이상의 데이터 세트에 전달된 열 형식이 서로 호환되지 않는 경우, Azure Machine Learning에서 이 오류가 표시됩니다.  

**해결 방법:** [메타데이터 편집](edit-metadata.md)을 사용하거나 원래의 입력 데이터 세트를 수정하여<!--, or use [Convert to Dataset](convert-to-dataset.md)--> 열 형식이 호환되는지 확인합니다.  

|예외 메시지|
|------------------------|
|입력 데이트 세트에서 해당하는 인덱스의 열 형식이 호환되지 않습니다.|
|열 '{first_col_names}'이(가) 학습 데이터와 테스트 데이터 간에 호환되지 않습니다.|
|열 '{first_col_names}' 및 '{second_col_names}'이(가) 호환되지 않습니다.|
|열 요소 형식은 입력 데이터 세트({first_dataset_names} 및 {second_dataset_names})의 열 '{first_col_names}'(0부터 시작)에 대해 호환되지 않습니다.|


## <a name="error-0017"></a>오류 0017  
 선택한 열에서 현재 구성 요소에서 지원 하지 않는 데이터 형식을 사용 하는 경우 예외가 발생 합니다.  

 예를 들어, 열 선택에 구성 요소에서 처리할 수 없는 데이터 형식의 열이 포함 된 경우 (예: 수치 연산에 대 한 문자열 열 또는 범주 기능 열이 필요한 점수 열) Azure Machine Learning에서이 오류가 나타날 수 있습니다.  

**해결 방법:**
 1. 문제가 되는 열을 식별합니다.
 2. 구성 요소에 대 한 요구 사항을 검토 합니다.
 3. 요구 사항을 준수하도록 열을 수정합니다. 다음 구성 요소 중 몇 가지를 사용 하 여 사용 하려는 열 및 변환에 따라 다음과 같은 몇 가지 구성 요소를 변경 해야 할 수도 있습니다.
    + [메타데이터 편집](edit-metadata.md)을 사용하여 열의 데이터 형식을 변경하거나 열 용도를 기능에서 숫자로 변경하거나 범주에서 범주 외 등으로 변경합니다.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) components to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These components provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) component to round or truncate values, or use the [Clip Values](clip-values.md) component to remove out of range values.  -->
 4. 최후의 수단으로 원래의 입력 데이터 세트를 수정해야 할 수도 있습니다.

> [!TIP]
> 시나리오와 일치하는 해결 방법을 찾을 수 없습니까? 오류를 생성 한 구성 요소의 이름과 열의 데이터 형식 및 카디널리티를 포함 하는이 항목에 대 한 피드백을 제공할 수 있습니다. 여기서는 일반적인 시나리오에 대한 대상으로 지정된 문제 해결 단계를 더 많이 제공하기 위해 이 정보를 사용합니다. 

|예외 메시지|
|------------------------|
|현재 형식의 열을 처리할 수 없습니다. 구성 요소에서 형식을 지원 하지 않습니다.|
|{col_type} 형식의 열을 처리할 수 없습니다. 구성 요소에서 형식을 지원 하지 않습니다.|
|{col_type} 형식의 열 "{col_name}"을(를) 처리할 수 없습니다. 구성 요소에서 형식을 지원 하지 않습니다.|
|{col_type} 형식의 열 "{col_name}"을(를) 처리할 수 없습니다. 구성 요소에서 형식을 지원 하지 않습니다. 매개 변수 이름: {arg_name}.|


## <a name="error-0018"></a>오류 0018  
 입력 데이트 세트가 올바르지 않으면 예외가 발생합니다.  

**해결 방법:** Azure Machine Learning에서 이 오류는 여러 상황에서 발생할 수 있으므로 절대적인 해결 방법은 없습니다. 일반적으로 오류는 구성 요소에 대 한 입력으로 제공 된 데이터의 열 수가 잘못 되었거나 데이터 형식이 구성 요소의 요구 사항과 일치 하지 않음을 나타냅니다. 예를 들어:  

-   구성 요소에 레이블 열이 필요 하지만 레이블로 표시 된 열이 없거나 레이블 열을 아직 선택 하지 않았습니다.  
  
-   구성 요소를 사용 하려면 데이터가 범주 여야 하지만 데이터는 숫자 여야 합니다.  

<!---   The component requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   데이터의 형식이 잘못되었습니다.  
  
-   가져온 데이터에 잘못된 문자, 잘못된 값 또는 범위를 벗어난 값이 포함되어 있습니다.  
-   열이 비어 있거나 누락된 값이 너무 많이 포함되어 있습니다.  

 요구 사항과 데이터의 방법에 대해 알아보려면 데이터 집합을 입력으로 사용 하는 구성 요소에 대 한 도움말 항목을 검토 하십시오.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these components to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|예외 메시지|
|------------------------|
|데이터 세트가 올바르지 않습니다.|
|{dataset1}에 잘못된 데이터가 포함되어 있습니다.|
|{dataset1} 및 {dataset2}의 열은 일치해야 합니다.|
|{dataset1}에 잘못된 데이터가 포함되어 있습니다. {reason}.|
|{dataset1}에 {invalid_data_category}이(가) 포함되어 있습니다. {troubleshoot_hint}|
|{dataset1}이(가) 잘못되었습니다. {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>오류 0019  
 열이 정렬된 값을 포함해야 하는데 그렇지 않으면 예외가 발생합니다.  

 지정된 열 값이 순서를 벗어난 경우, 이 오류가 Azure Machine Learning에 표시됩니다.  

**해결 방법:** 입력 데이터 집합을 수동으로 수정 하 고 구성 요소를 다시 실행 하 여 열 값을 정렬 합니다.  

|예외 메시지|
|------------------------|
|열의 값이 정렬되어 있지 않습니다.|
|열의 값 "{col_index}"이(가) 정렬되어 있지 않습니다.|
|데이터 세트 "{dataset}"의 열 "{col_index}"에 있는 값이 정렬되어 있지 않습니다.|
|"{arg_name}" 인수의 값이 "{sorting_order}" 순서대로 정렬되어 있지 않습니다.|


## <a name="error-0020"></a>오류 0020  
 구성 요소에 전달 된 데이터 집합 중 일부의 열 수가 너무 적으면 예외가 발생 합니다.  

 구성 요소에 대해 선택한 열이 충분 하지 않은 경우 Azure Machine Learning에서이 오류가 표시 됩니다.  

**해결 방법:** 구성 요소를 다시 방문 하 여 열 선택기에 올바른 수의 열이 선택 되어 있는지 확인 합니다.  

|예외 메시지|
|------------------------|
|입력 데이터 세트의 열 수가 허용되는 최소값보다 적습니다.|
|입력 데이터 세트 "{arg_name}"의 열 수가 허용되는 최소값보다 적습니다.|
|입력 데이터 세트의 열 수가 허용되는 최소 열 수인 {required_columns_count}개보다 적습니다.|
|입력 데이터 세트 "{arg_name}"의 열 수가 허용되는 최소 열 수인 {required_columns_count}개보다 적습니다.|


## <a name="error-0021"></a>오류 0021  
 구성 요소에 전달 된 데이터 집합 중 일부의 행 수가 너무 적으면 예외가 발생 합니다.  

 이 오류는 데이터 세트에서 지정된 작업을 수행하는 데 필요한 행이 부족할 때 Azure Machine Learning에 표시됩니다. 예를 들어, 입력 데이터 세트가 비어 있거나 최소 개수의 행이 유효해야 하는 작업을 수행하려는 경우, 이러한 오류가 표시될 수 있습니다. 이러한 작업에는 통계 방법에 기반한 그룹화 또는 분류, 특정 유형의 범주화, 개수로 알아보기 등이 포함될 수 있습니다(단, 이에 국한되지 않음).  

**해결 방법:**

 + 오류를 반환한 구성 요소를 열고 입력 데이터 집합 및 구성 요소 속성을 확인 합니다. 
 + 입력 데이터 집합이 비어 있지 않고 구성 요소 도움말에 설명 된 요구 사항을 충족 하기에 충분 한 데이터 행이 있는지 확인 합니다.  
 + 데이터가 외부 소스에서 로드되는 경우, 데이터 원본을 사용할 수 있는지 확인하며 가져오기 프로세스에서 가져올 행 수가 줄어드는 원인이 될만한 데이터 정의상의 오류나 변경은 없는지 확인합니다.
 + 데이터 형식에 영향을 줄 수 있는 구성 요소의 업스트림 데이터 또는 정리, 분할 또는 조인 작업과 같은 값의 수에 대 한 작업을 수행 하는 경우 해당 작업의 출력을 확인 하 여 반환 되는 행 수를 확인 합니다.  

|예외 메시지|
|------------------------|
|입력 데이트 세트의 행 수가 허용되는 최소값보다 적습니다.|
|입력 데이터 세트의 행 수가 허용되는 최소 행 수인 {required_rows_count}개보다 적습니다.|
|입력 데이터 세트의 행 수가 허용되는 최소 행 수인 {required_rows_count}개보다 적습니다. {reason}|
|입력 데이터 세트 "{arg_name}"의 열 수가 허용되는 최소 열 수인 {required_rows_count}개보다 적습니다.|
|입력 데이터 세트 "{arg_name}"의 행 수가 {actual_rows_count}개이며, 이는 허용되는 최소 행 수인 {required_rows_count}개보다 적습니다.|
|입력 데이터 세트 "{arg_name}"의 "{row_type}" 행 수가 {actual_rows_count}개이며, 이는 허용되는 최소 행 수인 {required_rows_count}개보다 적습니다.|


## <a name="error-0022"></a>오류 0022  
 입력 데이터 세트에서 선택한 열의 수가 필요한 수와 같지 않으면 예외가 발생합니다.  

 이 오류는 다운스트림 구성 요소나 작업에 특정 개수의 열 또는 입력이 필요한 경우 Azure Machine Learning에 발생할 수 있으며 너무 적거나 너무 많은 열 이나 입력을 제공한 경우에 발생할 수 있습니다. 다음은 그 예입니다.  

-   단일한 레이블 열 또는 키 열을 지정한 후 여러 개의 열을 실수로 선택했습니다.  
  
-   열 이름을 바꾸고 있지만 실제 존재하는 열의 수보다 더 많거나 더 적은 이름을 제공했습니다.  
  
-   원본 또는 대상의 열 수가 변경 되었거나 구성 요소에서 사용 하는 열 수와 일치 하지 않습니다.  
  
-   쉼표로 구분된 입력값 목록을 제공했지만 값 수가 일치하지 않거나 여러 입력이 지원되지 않습니다.  

**해결 방법:** 구성 요소를 다시 방문 하 여 열 선택 영역을 확인 하 여 올바른 열 수를 선택 했는지 확인 합니다. 업스트림 구성 요소의 출력과 다운스트림 작업의 요구 사항을 확인 합니다.  

 여러 열(열 인덱스, 모든 기능, 모든 숫자 등)을 선택할 수 있는 열 선택 옵션 중 한 가지를 사용하는 경우, 선택을 통해 반환된 열의 정확한 수를 확인합니다.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 업스트림 열의 수 또는 형식이 변경되지 않았는지 확인합니다.  

 권장 데이터 세트를 사용하여 모델을 학습하는 경우, 추천에는 사용자-항목 쌍 또는 사용자-항목 순위에 해당되는 제한된 수의 열이 필요합니다. 모델을 학습하거나 권장 데이터 세트를 분할하기 전에 추가 열을 제거합니다. 자세한 내용은 [데이터 분할](split-data.md)을 참조하세요.  

|예외 메시지|
|------------------------|
|입력 데이트 세트에서 선택한 열의 수가 필요한 수와 같지 않습니다.|
|입력 데이트 세트에서 선택한 열의 수가 {expected_col_count}와(과) 같지 않습니다.|
|열 선택 패턴 "{selection_pattern_friendly_name}"이(가) {expected_col_count}와(과) 같지 않은 입력 데이터 세트의 선택된 열 수를 제공합니다.|
|열 선택 패턴 "{selection_pattern_friendly_name}"이(가) 입력 데이터 세트에서 선택한 {expected_col_count}개의 열을 제공해야 하지만 실제로는 {selected_col_count}개의 열이 제공됩니다.|


## <a name="error-0023"></a>오류 0023  

입력 데이터 집합의 대상 열이 현재 강사 구성 요소에 대해 유효 하지 않으면 예외가 발생 합니다.  

이 오류는 구성 요소 매개 변수에서 선택한 것과 같은 대상 열이 올바른 데이터 형식이 아니거나, 누락 값이 모두 포함 되어 있거나, 예상 대로 범주에 포함 되지 않은 경우 Azure Machine Learning에 발생 합니다.  

**해결 방법:** 구성 요소 입력을 다시 방문 하 여 레이블/대상 열의 내용을 검사 합니다. 이 입력에 누락된 값은 없는지 확인합니다. 구성 요소에서 대상 열이 범주 여야 하는 경우 대상 열에 둘 이상의 고유 값이 있는지 확인 합니다.  

|예외 메시지|
|------------------------|
|입력 데이트 세트에 지원되지 않는 대상 열이 있습니다.|
|입력 데이터 세트에 지원되지 않는 대상 열 "{column_index}"이(가) 있습니다.|
|입력 데이터 세트에 {learner_type} 유형의 학습자에 대해 지원되지 않는 대상 열 "{column_index}"이(가) 있습니다.|


## <a name="error-0024"></a>오류 0024  
데이터 세트에 레이블 열이 없으면 예외가 발생합니다.  

 이 Azure Machine Learning 오류는 구성 요소에 레이블 열이 필요 하 고 데이터 집합에 레이블 열이 없을 때 발생 합니다. 예를 들어 채점된 데이터 세트를 평가할 경우, 정확도 메트릭을 계산하려면 일반적으로 레이블 열이 있어야 합니다.  

또한 레이블 열이 데이터 세트 내에 있더라도 Azure Machine Learning에 의해 올바르게 검색되지 않을 수도 있습니다.

**해결 방법:**

+ 오류를 생성 한 구성 요소를 열고 레이블 열이 있는지 확인 합니다. 이 열에 예측하려는 단일 결과(또는 종속 변수)가 포함되어 있다면 열의 이름 또는 데이터 형식은 중요하지 않습니다. 레이블이 어느 열에 있는지를 모를 경우, 클래스 또는 대상과 같은 일반 이름을 찾습니다. 
+  데이터 세트에 레이블 열이 포함되어 있지 않다면 레이블 열이 명시적으로 또는 실수로 업스트림에서 제거되었을 수도 있습니다. 데이터 집합이 업스트림 점수 매기기 구성 요소의 출력이 아닐 수도 있습니다.
+ 열을 레이블 열로 명시적으로 표시 하려면 [메타 데이터 편집](edit-metadata.md) 구성 요소를 추가 하 고 데이터 집합을 연결 합니다. 레이블 열만 선택하고 **필드** 드롭다운 목록에서 **레이블** 을 선택합니다. 
+ 잘못된 열이 레이블로 선택된 경우, **필드** 에서 **레이블 지우기** 를 선택하여 열의 메타데이터를 수정할 수 있습니다. 
  
|예외 메시지|
|------------------------|
|데이터 세트에 레이블 열이 없습니다.|
|"{dataset_name}"에 레이블 열이 없습니다.|


## <a name="error-0025"></a>오류 0025  
 데이터 세트에 점수 열이 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 모델 평가에 대한 입력에 유효한 점수 열이 포함되어 있지 않은 경우에 발생합니다. 예를 들면 사용자가 올바른 학습된 모델을 사용하여 채점되기 전의 데이터 세트를 평가하려고 하거나 점수 열이 명시적으로 업스트림에서 삭제된 경우가 이에 해당됩니다. 이러한 예외는 두 데이터 세트의 점수 열이 호환되지 않을 경우에도 발생합니다. 예를 들어 선형 회귀 변수의 정확도를 이진 분류자와 비교하려는 시도를 할 수도 있습니다.  

**해결 방법:** 모델 평가에 대한 입력을 다시 확인하고 이 입력에 하나 이상의 점수 열이 포함되어 있는지 검사합니다. 그렇지 않으면 데이터 집합의 점수가 매겨지지 않거나 업스트림 구성 요소에서 점수 열이 삭제 된 것입니다.  

|예외 메시지|
|------------------------|
|데이터 세트에 점수 열이 없습니다.|
|"{dataset_name}"에 점수 열이 없습니다.|
|"{learner_type}"에 의해 생성된 "{dataset_name}"에 점수 열이 없습니다. 올바른 학습자 유형을 사용하여 데이터 세트를 채점합니다.|


## <a name="error-0026"></a>오류 0026  
 이름이 같은 열이 허용되지 않으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 여러 열의 이름이 서로 동일한 경우에 발생합니다. 이러한 오류가 발생할 수 있는 경우에 해당되는 한 가지 예는 데이터 세트에 헤더 행이 없고 열 이름이 다음과 같이 자동으로 할당되는 경우입니다. Col0, Col1 등  

**해결 방법:** 열 이름이 동일한 경우 입력 데이터 집합과 구성 요소 사이에 [메타 데이터 편집](edit-metadata.md) 구성 요소를 삽입 합니다. [메타데이터 편집](edit-metadata.md)의 열 선택기를 사용하여 이름을 바꿀 열을 선택하고 새 이름을 **새 열 이름** 텍스트 상자에 입력합니다.  

|예외 메시지|
|------------------------|
|같은 열 이름이 인수에 지정되어 있습니다. 구성 요소에서는 동일한 열 이름을 사용할 수 없습니다.|
|"{arg_name_1}" 및 "{arg_name_2}" 인수에는 같은 열 이름을 사용할 수 없습니다. 다른 이름을 지정하세요.|


## <a name="error-0027"></a>오류 0027  
 두 개체의 크기가 같아야 하는데 실제로는 차이가 있다면 예외가 발생합니다.  

 이는 Azure Machine Learning에서 흔히 발생하는 오류에 속하며 다양한 조건으로 인해 발생할 수 있습니다.  

**해결 방법:** 특정한 해결 방법은 없습니다. 다만 다음과 같은 조건을 확인할 수 있습니다.  

-   열 이름을 바꾸는 경우, 각 목록(입력 열과 새 이름 목록)에 동일한 수의 항목이 있는지 확인합니다.  
  
-   두 개의 데이터 세트를 조인하거나 연결하는 경우, 동일한 스키마가 있는지 확인합니다.  
  
-   여러 열이 있는 두 데이터 세트를 조인하는 경우, 키 열에 동일한 데이터 형식이 있는지 확인하고 **선택 영역에서 중복 항목을 허용하고 열 순서 유지** 옵션을 선택합니다.  

|예외 메시지|
|------------------------|
|전달된 개체의 크기가 일치하지 않습니다.|
|"{friendly_name1}"의 크기가 "{friendly_name2}"의 크기와 일치하지 않습니다.|


## <a name="error-0028"></a>오류 0028  
 열 집합에서 열 이름이 중복되지 않아야 하며, 중복되는 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 열 이름이 중복될 때(즉, 고유하지 않을 때) 발생합니다.  

**해결 방법:** 열 이름이 동일한 경우 입력 데이터 집합과 오류를 발생 시키는 구성 요소 사이에 [메타 데이터 편집](edit-metadata.md) 인스턴스를 추가 합니다. [메타데이터 편집](edit-metadata.md)의 열 선택기를 사용하여 이름을 바꿀 열을 선택하고 새 열 이름을 **새 열 이름** 텍스트 상자에 입력합니다. 여러 열의 이름을 바꾸는 경우, **새 열 이름** 에 입력하는 값이 고유한 값인지 확인합니다.  

|예외 메시지|
|------------------------|
|열 집합에 중복된 열 이름이 있습니다.|
|이름 "{duplicated_name}"이(가) 중복되었습니다.|
|이름 "{duplicated_name}"이(가) "{arg_name}"에서 중복되었습니다.|
|이름 "{duplicated_name}"이(가) 중복되었습니다. 세부 정보: {details}|


## <a name="error-0029"></a>오류 0029  
 잘못된 URI가 전달되면 예외가 발생합니다.  

 잘못된 URI가 전달되는 경우 Azure Machine Learning에서 이 오류가 발생합니다.  다음 조건 중 하나라도 충족되면 이 오류가 표시됩니다.  

-   읽기 또는 쓰기를 위해 Azure Blob Storage에 제공된 공용 URI 또는 SAS URI에 오류가 포함되어 있습니다.  
  
-   SAS의 기간이 만료되었습니다.  
  
-   HTTP 소스를 통한 웹 URL은 파일 또는 루프백 URI를 나타냅니다.  
  
-   HTTP를 통한 웹 URL에 잘못된 형식의 URL이 포함되어 있습니다.  
  
-   원격 소스에서 URL을 확인할 수 없습니다.  

**해결 방법:** 구성 요소를 다시 방문 하 여 URI의 형식을 확인 합니다. 데이터 원본이 HTTP를 통해 연결되는 웹 URL인 경우, 의도한 원본이 파일 또는 루프백 URI(localhost)가 아닌지 확인합니다.  

|예외 메시지|
|------------------------|
|잘못된 URI가 전달되었습니다.|
|URI "{invalid_url}"이(가) 잘못되었습니다.|


## <a name="error-0030"></a>오류 0030  
 파일을 다운로드할 수 없는 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 예외는 파일을 다운로드할 수 없는 경우에 발생합니다. 세 번의 재시도 후에 HTTP 소스에서 읽기 시도가 실패하면 이 예외가 표시됩니다.  

**해결 방법:** HTTP 소스에 대한 URI가 올바르며 현재 인터넷을 통해 사이트에 액세스할 수 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|파일을 다운로드할 수 없습니다.|
|파일 {file_url}을(를) 다운로드하는 동안 오류가 발생했습니다.|


## <a name="error-0031"></a>오류 0031  
 열 집합의 열 수가 필요한 수보다 적으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 선택한 열 수가 필요한 것보다 적으면 발생합니다.  필요한 최소 열 수를 선택하지 않으면 이 오류가 표시됩니다.  

**해결 방법:** **열 선택기** 를 사용하여 열 선택 영역에 열을 더 추가합니다.  

|예외 메시지|
|------------------------|
|열 집합의 열 수가 필요한 수보다 적습니다.|
|입력 인수 "{arg_name}"에 대해 최소한 {required_columns_count}개 이상의 열을 지정해야 합니다.|
|입력 인수 "{arg_name}"에 대해 최소한 {required_columns_count}개 이상의 열을 지정해야 합니다. 실제로 지정된 열 수는 {input_columns_count}개입니다.|


## <a name="error-0032"></a>오류 0032  
 인수가 숫자가 아니면 예외가 발생합니다.  

 Azure Machine Learning에서 인수가 double 또는 NaN이면 이 오류가 표시됩니다.  

**해결 방법:** 유효한 값을 사용하도록 지정된 인수를 수정합니다.  

|예외 메시지|
|------------------------|
|인수가 숫자가 아닙니다.|
|"{arg_name}"이(가) 숫자가 아닙니다.|


## <a name="error-0033"></a>오류 0033  
 인수가 무한 값이면 예외가 발생합니다.  

 Azure Machine Learning에서 인수가 무한 값인 경우 이 오류가 발생합니다. 인수가 `double.NegativeInfinity` 또는 `double.PositiveInfinity`에 해당되면 이 오류가 표시됩니다.  

**해결 방법:** 유효한 값이 되도록 지정된 인수를 수정합니다.  

|예외 메시지|
|------------------------|
|인수는 유한 값이어야 합니다.|
|"{arg_name}"이(가) 유한 값이 아닙니다.|
|"{column_name}" 열에 무한 값이 포함되어 있습니다.|


## <a name="error-0034"></a>오류 0034  
 지정된 사용자-항목 쌍에 대해 등급이 두 개 이상인 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 사용자-항목 쌍에 둘 이상의 등급 값이 있다면 권장 시 발생합니다.  

**해결 방법:** 사용자-항목 쌍에 하나의 등급 값만 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|데이터 세트의 값에 대한 등급이 둘 이상입니다.|
|등급 예측 데이터 테이블에서 사용자 {user} 및 항목 {item}에 대한 등급이 둘 이상입니다.|
|{dataset}에서 사용자 {user} 및 항목 {item}에 대한 등급이 둘 이상입니다.|


## <a name="error-0035"></a>오류 0035  
 지정된 사용자나 항목에 대해 기능을 제공하지 않으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 채점에 추천 모델을 사용하려고 시도하지만 기능 벡터를 찾을 수 없는 경우에 발생합니다.  

**해결 방법:**

Matchbox 추천에는 항목 기능 또는 사용자 기능 중 하나를 사용할 때 반드시 충족해야 하는 특정 요구 사항이 있습니다.  이 오류는 입력으로 제공한 사용자 또는 항목에 대한 기능 벡터가 없음을 나타냅니다. 각 사용자 또는 항목에 대한 데이터에서 기능 벡터를 사용할 수 있는지 확인합니다.  

 예를 들어 사용자의 나이, 위치 또는 수입 등의 기능을 사용하여 추천 모델을 학습했지만 학습 중에 표시되지 않은 새 사용자에 대한 점수를 만들려는 경우, 해당 사용자에 대한 적절한 예측을 수행하기 위해 새 사용자에 대해 이와 동등한 기능 집합(예: 나이, 위치 및 수입 값)을 제공해야 합니다. 

 이러한 사용자에 대한 기능이 없는 경우, 기능 엔지니어링을 통해 적절한 기능을 생성하는 것이 좋습니다.  예를 들어 개별 사용자 사용 기간 또는 수입 값이 없는 경우, 사용자 그룹에 사용할 근사값을 생성할 수 있습니다. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > 귀하의 사례에 적용할 수 없는 해결 방법인가요? 이 문서에 대 한 사용자 의견을 보내고 구성 요소 및 열의 행 수를 비롯 한 시나리오에 대 한 정보를 제공 합니다. 저희는 이 정보를 사용하여 향후 더 자세한 문제 해결 단계를 제공할 것입니다.

|예외 메시지|
|------------------------|
|필요한 사용자 또는 항목에 대해 기능을 제공하지 않았습니다.|
|{required_feature_name}의 기능이 필요하지만 제공되지 않았습니다.|


## <a name="error-0036"></a>오류 0036  
 지정된 사용자나 항목에 대해 여러 개의 기능 벡터가 제공된 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 기능 벡터가 두 번 이상 정의된 경우에 발생합니다.  

**해결 방법:** 기능 벡터가 두 번 이상 정의되지는 않았는지 확인합니다.  

|예외 메시지|
|------------------------|
|사용자 또는 항목에 대한 기능 정의가 중복됩니다.|


## <a name="error-0037"></a>오류 0037  
 레이블 열이 하나만 허용되는데 여러 개를 지정하면 오류가 발생합니다.  

 Azure Machine Learning에서 이 오류는 두 개 이상의 열을 새 레이블 열로 선택한 경우에 발생합니다. 대부분의 감독된 학습 알고리즘에서는 단일 열을 대상 또는 레이블로 표시해야 합니다.  

**해결 방법:** 단일 열을 새 레이블 열로 선택해야 합니다.  

|예외 메시지|
|------------------------|
|여러 개의 레이블 열을 지정했습니다.|
|"{dataset_name}"에 여러 레이블 열이 지정되었습니다.|


## <a name="error-0039"></a>오류 0039  
 작업이 실패하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 내부 작업을 완료할 수 없을 때 발생합니다.  

**해결 방법:** 이 오류는 다양한 조건으로 인해 발생하며 특정 해결 방법이 없습니다.  
 다음 표에는 이 오류에 대한 일반 메시지가 포함되어 있으며 그 뒤에는 조건에 대한 특정 설명이 표시됩니다. 

 세부 정보를 사용할 수 없는 경우 [Microsoft Q&사용자 의견을 보내고](/answers/topics/azure-machine-learning-studio-classic.html) 오류 및 관련 조건을 생성 한 구성 요소에 대 한 정보를 제공 하기 위한 질문 페이지입니다.

|예외 메시지|
|------------------------|
|작업이 실패했습니다.|
|작업을 완료하는 동안 "{failed_operation}" 오류가 발생했습니다.|
|작업을 완료하는 동안 "{failed_operation}" 오류가 발생했습니다. 이유: "{reason}".|


## <a name="error-0042"></a>오류 0042  
 열을 다른 형식으로 변환할 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 열을 지정된 형식으로 변환할 수 없는 경우에 발생합니다.  구성 요소에 datetime, text, 부동 소수점 숫자 또는 정수와 같은 특정 데이터 형식이 필요한 경우에는이 오류가 표시 되지만 기존 열을 필요한 형식으로 변환할 수는 없습니다.  

예를 들어, 하나의 열을 선택하고 수학 연산에 사용하기 위해 숫자 데이터 유형으로 변환하려는 시도를 할 수 있는데, 유효하지 않은 데이터가 열에 포함된 경우에는 이 오류가 발생합니다. 

한편, 부동 소수점 숫자나 많은 고유 값을 포함하는 열을 범주 열로 사용하려고 할 때 이 오류가 발생할 수 있습니다. 

**해결 방법:**

+ 오류를 생성 한 구성 요소에 대 한 도움말 페이지를 열고 데이터 형식 요구 사항을 확인 합니다.
+ 입력 데이터 세트에 있는 열의 데이터 형식을 검토합니다.
+ 이른바 스키마 없는 데이터 원본에서 가져온 데이터를 검사합니다.
+ 데이터 세트에서 원하는 데이터 형식으로의 변환을 차단할 수 있는 특수 문자 또는 누락된 값이 있는지 확인합니다. 
    + 숫자 데이터 형식은 일치해야 합니다. 예를 들면 정수 열에서 부동 소수점 숫자를 확인합니다.
    + 숫자 열에서 텍스트 문자열 또는 NA 값이 있는지 확인합니다. 
    + 부울 값을 필요한 데이터 형식에 따라 적절한 표현으로 변환할 수 있습니다.
    + 텍스트 열에서 유니코드 외 문자, 탭 문자 또는 제어 문자가 있는지 검사합니다.
    + 날짜/시간 데이터는 모델링 오류가 발생하지 않도록 일관성을 유지해야 하며, 다만 정리는 다양한 형식으로 인해 복잡해질 수 있습니다. 다음 항목을 사용해보시기 바랍니다. <!--the [Execute R Script](execute-r-script.md) or -->Python 스크립트 구성 요소를 [실행하여](execute-python-script.md) 정리를 수행합니다.  
+ 필요하다면 열을 성공적으로 변환할 수 있도록 입력 데이터 세트의 값을 수정합니다. 수정에는 범주화, 잘림 또는 반올림 작업, 이상값 제거 또는 누락된 값의 대체가 포함될 수 있습니다. 기계 학습에서 몇 가지 일반적인 데이터 변환 시나리오에 관한 설명은 다음 문서들을 참조하세요.
    + [누락 데이터 정리](clean-missing-data.md)
    + [데이터 정규화](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> 해결 방법이 불명확하거나 귀하의 사례에 적용할 수 없는 방법인가요? 이 문서에 대한 피드백을 보내고 열의 구성 요소 및 데이터 형식을 포함하여 시나리오에 대한 정보를 제공해 드립니다. 저희는 이 정보를 사용하여 향후 더 자세한 문제 해결 단계를 제공할 것입니다.  

|예외 메시지|
|------------------------|
|허용되지 않는 변환입니다.|
|{type1} 형식의 열을 {type2} 형식의 열로 변환할 수 없습니다.|
|{type1} 형식의 "{col_name1}"열을 {type2} 형식의 열로 변환할 수 없습니다.|
|{type1} 형식의 "{col_name1}"열을 {type2} 형식의 "{col_name2}"열로 변환할 수 없습니다.|


## <a name="error-0044"></a>오류 0044  
 기존 값에서 열의 요소 형식을 파생할 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 데이터 세트에서 열의 형식을 유추할 수 없을 때 발생합니다. 이는 대체로 요소 형식이 서로 다른 두 개 이상의 데이터 세트를 연결할 때 발생합니다. Azure Machine Learning에서 정보 손실 없이 열에 있는 모든 값을 나타낼 수 있는 공용 형식을 판정할 수 없는 경우에 이 오류가 생성됩니다.  

**해결 방법:** 결합되는 두 데이터 세트의 지정된 열에 있는 모든 값이 동일한 형식(숫자, 부울, 범주, 문자열, 날짜 등)에 속하거나 동일한 형식으로 강제 변환될 수 있는지 확인하세요.  

|예외 메시지|
|------------------------|
|열의 요소 형식을 파생할 수 없습니다.|
|"{column_name}"열에 대한 요소 형식을 파생할 수 없습니다. 모든 형식이 null 참조입니다.|
|데이터 세트 "{dataset_name}"의 "{column_name}"열에 대한 요소 형식을 파생할 수 없습니다. 모든 요소가 null 참조입니다.|


## <a name="error-0045"></a>오류 0045  
 소스의 요소 형식이 혼합되어 있어 열을 만들 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 결합되는 두 데이터 세트의 요소 형식이 서로 다른 경우에 생성됩니다.  

**해결 방법:** 결합되는 두 데이터 세트의 지정된 열에 있는 모든 값이 동일한 형식(숫자, 부울, 범주, 문자열, 날짜 등)에 속하는지 확인하세요.  

|예외 메시지|
|------------------------|
|혼합 요소 형식으로 열을 만들 수 없습니다.|
|혼합 요소 형식의 id "{column_id}"(으)로 열을 만들 수 없습니다.<br />데이터 형식[{row_1}, {column_id}]은 "{type_1}"입니다. <br />데이터 형식[{row_2}, {column_id}]은 "{type_2}"입니다.|
|혼합 요소 형식의 id "{column_id}"(으)로 열을 만들 수 없습니다.<br />청크 {chunk_id_1}의 형식은 "{type_1}"입니다. <br />청크 {chunk_id_2}의 형식은 청크 크기가 {chunk_size}인 "{type_2}"입니다.|


## <a name="error-0046"></a>오류 0046  
 지정한 경로에 디렉터리를 만들 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 지정된 경로에서 디렉터리를 만들 수 없을 때 발생합니다. Hive 쿼리의 출력 디렉터리에 대한 경로 중 하나라도 잘못되었거나 액세스할 수 없는 경우 이 오류가 표시됩니다.  

**해결 방법:** 구성 요소를 다시 확인하고 디렉터리 경로의 형식이 올바르게 지정되어 있고 현재 자격 증명으로 액세스할 수 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|올바른 출력 디렉터리를 지정하세요.|
|디렉터리: {path}을(를) 만들 수 없습니다. 올바른 경로를 지정하세요.|


## <a name="error-0047"></a>오류 0047  
 구성 요소에 전달된 일부 데이터 세트의 기능 열 수가 너무 작으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 알고리즘에 필요한 최소 열 수가 학습을 위한 입력 데이터 세트에 포함되어 있지 않은 경우에 발생합니다. 일반적으로 데이터 세트는 비어 있거나 학습 열만 포함합니다.  

**해결 방법:** 입력 데이터 세트를 다시 방문하여 레이블 열 외에도 하나 이상의 추가 열이 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|입력 데이터 세트의 기능 열 수가 허용되는 최소값보다 적습니다.|
|입력 데이터 세트의 기능 열 수가 허용되는 최소 열 수인 {required_columns_count}개보다 적습니다.|
|입력 데이터 세트 "{arg_name}"의 기능 열 수가 허용되는 최소 열 수인 {required_columns_count}개보다 적습니다.|


## <a name="error-0048"></a>오류 0048  
 파일을 열 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 읽기 또는 쓰기를 위한 파일을 열 수 없을 때 발생합니다. 다음과 같은 이유로 이 오류가 표시될 수 있습니다.  

-   컨테이너 또는 파일(Blob)이 없음  
  
-   파일이나 컨테이너의 액세스 수준에서 파일에 액세스할 수 없음  
  
-   파일이 너무 커서 읽을 수 없거나 형식이 잘못됨  

**해결 방법:** 읽으려는 구성 요소 및 파일을 다시 방문합니다.  

 컨테이너 및 파일의 이름이 올바른지 확인합니다.  

 Azure 클래식 포털 또는 Azure Storage 도구를 사용해 파일에 액세스할 수 있는 권한이 있는지 확인합니다.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|예외 메시지|
|------------------------|
|파일을 열 수 없습니다.|
|{file_name} 파일을 여는 동안 오류가 발생했습니다.|
|{file_name} 파일을 여는 동안 오류가 발생했습니다. 스토리지 예외 메시지: {exception}.|


## <a name="error-0049"></a>오류 0049  
 파일을 구문 분석할 수 없는 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 파일을 구문 분석할 수 없을 때 발생합니다. [데이터 가져오기](import-data.md) 구성 요소에서 선택한 파일 형식이 파일의 실제 형식과 일치하지 않거나 파일에 인식할 수 없는 문자가 포함된 경우 이 오류가 표시됩니다.  

**해결 방법:** 구성 요소를 다시 확인하고 파일 형식과 일치하지 않는 경우 파일 형식 선택을 수정합니다. 가능하다면 파일을 검사하여 잘못된 문자가 포함되어 있지는 않은지 확인합니다.  

|예외 메시지|
|------------------------|
|파일을 구문 분석할 수 없습니다.|
|{file_format} 파일을 구문 분석하는 동안 오류가 발생했습니다.|
|{file_format} 파일 {file_name}을(를) 구문 분석하는 동안 오류가 발생 했습니다.|
|{file_format} 파일을 구문 분석하는 동안 오류가 발생했습니다. 이유: {failure_reason}.|
|{file_format} 파일 {file_name}을(를) 구문 분석하는 동안 오류가 발생 했습니다. 이유: {failure_reason}.|


## <a name="error-0052"></a>오류 0052  
 Azure Storage 계정 키를 잘못 지정하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 Azure Storage 계정에 액세스하는 데 사용된 키가 잘못된 경우에 발생합니다. 예를 들어, 복사 후 붙여넣기를 할 때 Azure Storage 키가 잘렸거나 잘못된 키가 사용된 경우, 이 오류가 표시될 수 있습니다.  

 Azure Storage 계정의 키를 얻는 방법에 대한 자세한 내용은 [스토리지 액세스 키 보기, 복사 및 다시 생성](../../storage/common/storage-account-create.md)을 참조하세요.  

**해결 방법:** 구성 요소를 다시 확인하고 Azure Storage 키가 계정에 대해 올바른지 확인합니다. 필요한 경우 Azure 클래식 포털에서 키를 다시 복사합니다.  

|예외 메시지|
|------------------------|
|Azure Storage 계정 키가 잘못되었습니다.|


## <a name="error-0053"></a>오류 0053  
 Matchbox 추천을 위한 사용자 기능이나 항목이 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 기능 벡터를 찾을 수 없을 때 생성됩니다.  

**해결 방법:** 기능 벡터가 입력 데이터 세트에 존재하는지 확인합니다.  

|예외 메시지|
|------------------------|
|사용자 기능 및/또는 항목이 필요하지만 제공되지 않았습니다.|


## <a name="error-0056"></a>오류 0056  
 작업에 대해 선택한 열이 요구 사항을 위반하는 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 열을 특정 데이터 형식으로 사용해야 하는 작업의 열을 선택할 때 발생합니다. 

 열이 올바른 데이터 형식인 경우에도 이 오류가 발생할 수 있지만 사용 중인 구성 요소에서 열이 기능, 레이블 또는 범주 열로 표시되어야 합니다.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) component requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**해결 방법:**

1.  현재 선택한 열의 데이터 형식을 검토합니다. 

2. 선택한 열이 범주, 레이블 또는 기능 열에 해당되는지 여부를 확인합니다.  
  
3.  열 선택을 한 구성 요소에 대한 도움말 항목을 검토하여 데이터 형식 또는 열 사용에 대한 특정 요구 사항이 있는지 확인합니다.  
  
3.  [메타데이터 편집](edit-metadata.md)을 사용하여 이 작업 기간에 대한 열 형식을 변경합니다. 다운스트림 작업에 필요하다면 [메타데이터 편집](edit-metadata.md)의 다른 인스턴스를 사용하여 열 형식을 원래 값으로 다시 변경하세요.  

|예외 메시지|
|------------------------|
|하나 이상의 선택한 열이 허용되는 범주에 포함되지 않습니다.|
|이름이 "{col_name}"인 열이 허용되는 범주에 포함되지 않습니다.|


## <a name="error-0057"></a>오류 0057  
 이미 있는 파일 또는 Blob을 만들려고 시도하는 경우 예외가 발생합니다.  

 이 예외는 [데이터 내보내기](export-data.md) 구성 요소 또는 다른 구성 요소를 사용하여 파이프라인 결과를 Azure Blob Storage에 Azure Machine Learning 저장하지만 이미 존재하는 파일 또는 Blob을 만들려고 할 때 발생합니다.   

**해결 방법:**

 이 오류는 이전에 **Azure Blob Storage 쓰기 모드** 속성을 **오류** 로 설정한 경우에만 표시됩니다. 의도적으로 이 구성 요소는 이미 존재하는 Blob에 데이터 세트를 쓰려고 하면 오류가 발생합니다.

 - 구성 요소 속성을 열고 **Azure Blob Storage 쓰기 모드** 속성을 **덮어쓰기로** 변경합니다.
 - 또는 다른 대상 Blob 또는 파일의 이름을 입력할 수 있으며, 아직 존재하지 않는 Blob을 반드시 지정해야 합니다.  

|예외 메시지|
|------------------------|
|파일 또는 Blob가 이미 있습니다.|
|파일 또는 Blob "{file_path}"가 이미 있습니다.|


## <a name="error-0058"></a>오류 0058  
 Azure Machine Learning에서 이 오류는 데이터 세트에 필요한 레이블 열이 포함되어 있지 않은 경우에 발생합니다.  

 제공된 레이블 열이 학습자에 필요한 데이터 또는 데이터 유형과 일치하지 않거나 잘못된 값을 갖는 경우에도 이 예외가 발생할 수 있습니다. 예를 들어, 이 예외는 이진 분류자를 학습할 경우 실제 값 레이블 열을 사용할 때 생성됩니다.  

**해결 방법:** 해결 방법은 사용 중인 학습자 또는 강사 그리고 데이터 세트에 있는 열의 데이터 유형에 따라 달라집니다. 먼저 기계 학습 알고리즘 또는 학습 구성 요소의 요구 사항을 확인합니다.  

 입력 데이터 세트를 다시 확인합니다. 레이블로 간주될 열에 지금 만들고 있는 모델에 대한 올바른 데이터 형식이 있는지 확인합니다.  

 누락된 값에 대한 입력을 확인하고 필요하다면 이를 제거하거나 바꿉니다.  

 필요한 경우 [메타데이터 편집](edit-metadata.md) 구성 요소를 추가하고 레이블 열이 레이블로 표시되는지 확인합니다.  

|예외 메시지|
|------------------------|
|레이블 열 값과 채점된 레이블 열 값은 비교할 수 없습니다.|
|"{dataset_name}"에서 레이블 열은 필요한 열이 아닙니다.|
|"{dataset_name}", {reason}에서 레이블 열은 필요한 열이 아닙니다.|
|"{dataset_name}"에서 "{column_name}" 레이블 열은 필요한 열이 아닙니다.|
|"{dataset_name}", {reason}에서 "{column_name}" 레이블 열은 필요한 열이 아닙니다.|


## <a name="error-0059"></a>오류 0059  
 열 선택기에서 지정한 열 인덱스를 구문 분석할 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 열 선택기를 사용할 때 지정된 열 인덱스를 구문 분석할 수 없는 경우에 발생합니다.  열 인덱스가 구문 분석할 수 없는 잘못된 형식인 경우, 이 오류가 표시됩니다.  

**해결 방법:** 유효한 인덱스 값을 사용하도록 열 인덱스를 수정합니다.  

|예외 메시지|
|------------------------|
|하나 이상의 지정된 열 인덱스 또는 인덱스 범위를 구문 분석할 수 없습니다.|
|열 인덱스 또는 범위 "{column_index_or_range}"을(를) 구문 분석할 수 없습니다.|


## <a name="error-0060"></a>오류 0060  
 열 선택기에서 범위를 벗어난 열 범위를 지정하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 범위를 벗어난 열 범위가 열 선택기에 지정된 경우에 발생합니다. 열 선택기의 열 범위가 데이터 세트의 열과 일치하지 않는 경우, 이 오류가 표시됩니다.  

**해결 방법:** 열 선택기의 열 범위를 데이터 세트의 열에 맞게 수정합니다.  

|예외 메시지|
|------------------------|
|잘못되었거나 범위를 벗어난 열 인덱스 범위를 지정했습니다.|
|열 범위 "{column_range}"(이)가 잘못되었거나 범위를 벗어났습니다.|


## <a name="error-0061"></a>오류 0061  
 테이블과 열 수가 다른 DataTable에 행을 추가하려고 하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 주어진 데이터 세트와 열 수가 다른 어떤 데이터 세트에 행을 추가하려고 할 때 발생합니다.  데이터 세트에 추가되는 행에서 입력 데이터 세트의 열 수가 다른 경우에 이 오류가 표시됩니다.  열 수가 서로 다른 경우에는 행을 데이터 세트에 추가할 수 없습니다.  

**해결 방법:** 추가된 행과 동일한 수의 열을 포함하도록 입력 데이터 세트를 수정하거나, 데이터 세트와 동일한 수의 열을 갖도록 추가된 행을 수정합니다.  

|예외 메시지|
|------------------------|
|모든 테이블의 열 수는 같아야 합니다.|
|청크 "{chunk_id_1}"의 열이 청크 크기가 {chunk_size}인 청크 "{chunk_id_2}"와(과) 다릅니다.|
|파일 "{filename_1}"의 열 수(count={column_count_1})가 파일 "{filename_2}"(count={column_count_2})와(과) 다릅니다.|


## <a name="error-0062"></a>오류 0062  
 서로 다른 학습자 유형으로 두 모델을 비교하려고 하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 채점된 두 가지 상이한 데이터 세트에 대한 평가 메트릭을 비교할 수 없을 때 발생합니다. 이 경우, 두 개의 채점된 데이터 세트를 생성하는 데 사용되는 모델의 효과를 비교할 수 없습니다.  

**해결 방법:** 채점된 결과가 동일한 종류의 기계 학습 모델(이진 분류, 회귀, 다중 클래스 분류, 권장 사항, 클러스터링, 변칙 검색 등)로 생성되는지 확인합니다. 비교하는 모든 모델의 학습자 유형은 같아야 합니다.  

|예외 메시지|
|------------------------|
|모든 모델의 학습자 유형은 같아야 합니다.|
|호환되지 않는 학습자 유형 "{actual_learner_type}"을(를) 가져왔습니다. 필요한 학습자 유형은 "{expected_learner_type_list}"입니다.|


## <a name="error-0064"></a>오류 0064  
 Azure Storage 계정 이름 또는 스토리지 키를 잘못 지정하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 Azure Storage 계정 이름 또는 스토리지 키가 잘못 지정된 경우에 발생합니다. 스토리지 계정에서 잘못된 계정 이름 또는 암호를 입력하면 이 오류가 표시됩니다. 계정 이름이나 암호를 수동으로 입력하는 경우, 이 오류가 발생할 수 있습니다. 이 오류는 계정이 삭제된 경우에도 발생할 수 있습니다.  

**해결 방법:** 계정 이름과 암호가 올바르게 입력되었으며 계정이 존재하는지 확인합니다.  

|예외 메시지|
|------------------------|
|Azure Storage 계정 이름 또는 스토리지 키가 잘못되었습니다.|
|Azure Storage 계정 이름 "{account_name}" 또는 계정 이름의 스토리지 키가 잘못되었습니다.|


## <a name="error-0065"></a>오류 0065  
 Azure Blob 이름을 잘못 지정한 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 Azure Blob 이름이 잘못 지정된 경우에 발생합니다.  다음과 같은 경우에 오류가 표시됩니다.  

-   지정된 컨테이너에서 Blob을 찾을 수 없습니다.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) components is greater than 512 characters.  -->

-   형식이 인코딩된 Excel 또는 CSV인 경우 [데이터 가져오기](import-data.md) 요청에서 컨테이너만 소스로 지정되었습니다. 컨테이너 내 모든 Blob의 콘텐츠를 연결하는 것은 이러한 형식에서 허용되지 않습니다.  
  
-   SAS URI에 올바른 Blob 이름이 포함되어 있지 않습니다.  

**해결 방법:** 예외를 throw하는 구성 요소를 다시 방문합니다. 지정된 Blob이 스토리지 계정의 컨테이너에 존재하며 해당 사용 권한을 통해 Blob을 볼 수 있는지 확인합니다. 인코딩 형식의 Excel 또는 CSV가 있는 경우, 입력이 **containername/filename** 형식인지 확인합니다. SAS URI에 유효한 Blob 이름이 포함되어 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|Azure Storage Blob 이름이 잘못되었습니다.|
|Azure Storage Blob 이름 "{blob_name}"이(가) 잘못되었습니다.|
|접두사가 "{blob_name_prefix}"인 Azure Storage Blob 이름이 없습니다.|
|컨테이너 "{container_name}"에서 Azure Storage Blob을 찾을 수 없습니다.|
|와일드카드 경로 "{blob_wildcard_path}"을(를) 사용하여 Azure Storage Blob을 찾을 수 없습니다.|


## <a name="error-0066"></a>오류 0066  
 리소스를 Azure Blob에 업로드할 수 없는 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 리소스를 Azure Blob에 업로드할 수 없는 경우에 발생합니다.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> 둘 다 입력 파일을 포함하는 계정과 동일한 Azure Storage 계정에 저장됩니다.  

**해결 방법:** 구성 요소를 다시 방문합니다. Azure 계정 이름, 스토리지 키 및 컨테이너가 올바르며 컨테이너에 쓸 수 있는 권한이 해당 계정에 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|리소스를 Azure Storage에 업로드할 수 없습니다.|
|"{source_path}" 파일을 Azure Storage에 "{dest_path}"(으)로 업로드할 수 없습니다.|


## <a name="error-0067"></a>오류 0067  
 데이터 세트의 열 수가 필요한 수와 다르면 예외가 발생합니다.  

 Azure Machine Learning에서 데이터 세트의 열 수가 필요한 수와 일치하지 않으면 이 오류가 발생합니다.  데이터 세트의 열 수가 실행 중에 구성 요소에서 예상하는 열 수와 다른 경우 이 오류가 표시됩니다.  

**해결 방법:** 입력 데이터 세트 또는 매개 변수를 수정합니다.  

|예외 메시지|
|------------------------|
|데이터 테이블의 열 수가 필요한 수와 다릅니다.|
|데이터 세트 "{dataset_name}"의 열 수가 필요한 수와 다릅니다.|
|"{expected_column_count}"개의 열이 필요하지만 실제로는 "{actual_column_count}"개의 열을 찾았습니다.|
|입력 데이터 세트 "{dataset_name}"에서 "{expected_column_count}"개의 열이 필요하지만 실제로는 "{actual_column_count}"개의 열을 찾았습니다.|


## <a name="error-0068"></a>오류 0068  
 지정한 Hive 스크립트가 올바르지 않으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 Hive QL 스크립트에 구문 오류가 있거나 쿼리 또는 스크립트를 실행하는 동안 Hive 인터프리터에서 오류가 발생하는 경우에 발생합니다.  

**해결 방법:**

특정 오류에 따라 작업을 수행할 수 있도록 Hive의 오류 메시지는 대체로 오류 로그에 다시 보고됩니다. 

+ 구성 요소를 열고 쿼리에서 실수를 검사합니다.  
+ Hadoop 클러스터의 Hive 콘솔에 로그인한 후 쿼리를 실행하여 쿼리가 Azure Machine Learning 외부에서 올바르게 작동하는지 확인합니다.  
+ 실행문과 주석을 한 줄에 결합하는 대신 Hive 스크립트의 주석을 별도의 줄에 배치합니다.  

### <a name="resources"></a>리소스

기계 학습을 위한 Hive 쿼리와 관련된 도움말은 다음 문서를 참조하세요.

+ [Hive 테이블을 만들고 Azure Blob Storage에서 데이터 로드](/azure/architecture/data-science-process/move-hive-tables)
+ [Hive 쿼리를 사용하여 테이블의 데이터 탐색](/azure/architecture/data-science-process/explore-data-hive-tables)
+ [Hive 쿼리를 사용하여 Hadoop 클러스터의 데이터에 대한 기능 만들기](/azure/architecture/data-science-process/create-features-hive)
+ [SQL 사용자용 Hive 참고 자료(PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|예외 메시지|
|------------------------|
|Hive 스크립트가 잘못되었습니다.|


## <a name="error-0069"></a>오류 0069  
 지정한 SQL 스크립트가 올바르지 않으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 지정된 SQL 스크립트에 구문 문제가 있거나 스크립트에 지정된 열 또는 테이블이 유효하지 않은 경우에 발생합니다. 

 SQL 엔진에서 쿼리 또는 스크립트를 실행하는 동안 오류가 발생하면 이 오류가 표시됩니다. 특정 오류에 따라 작업을 수행할 수 있도록 SQL 오류 메시지는 대체로 오류 로그에 다시 보고됩니다.  

**해결 방법:** 구성 요소를 다시 방문하고 SQL 쿼리에서 실수를 검사합니다.  

 데이터베이스 서버에 직접 로그인하고 쿼리를 실행하여 쿼리가 Azure ML 외부에서 올바르게 작동하는지 확인합니다.  

 구성 요소 예외에서 보고한 SQL 생성된 메시지가 있는 경우 보고된 오류에 따라 작업을 수행합니다. 예를 들어 오류 메시지에는 발생 가능한 오류에 대한 특정 지침이 때때로 포함됩니다.
+ 해당 열 또는 누락된 데이터베이스가 없음: 열 이름을 잘못 입력했을 수도 있음을 나타냅니다. 열 이름이 올바른지 잘 모르겠다면 대괄호 또는 따옴표를 사용하여 열 식별자를 묶습니다.
+ *\<SQL keyword\> 근처에 SQL 논리 오류가 있음*. 이는 지정된 키워드 앞에 구문 오류가 발생했을 수도 있음을 나타냅니다.

  
|예외 메시지|
|------------------------|
|SQL 스크립트가 잘못되었습니다.|
|SQL 쿼리 "{sql_query}"이(가) 올바르지 않습니다.|
|SQL 쿼리 "{sql_query}"이(가) 올바르지 않습니다. 예외 메시지: {exception}.|


## <a name="error-0070"></a>오류 0070  
 존재하지 않는 Azure 테이블에 액세스하려고 하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 없는 Azure 테이블에 액세스하려고 할 때 발생합니다. Azure Table Storage에서 읽기 또는 쓰기 작업을 할 때에는 존재하지 않는 테이블을 Azure Storage에 지정하는 경우, 이 오류가 표시됩니다. 이 오류는 원하는 테이블의 이름을 잘못 입력하거나 대상 이름과 저장소 유형이 일치하지 않는 경우에 발생할 수 있습니다. 예를 들어 테이블에서 읽기를 수행하려고 하지만 실제로는 Blob의 이름을 입력했습니다.  

**해결 방법:** 구성 요소를 다시 검토하여 테이블 이름이 올바른지 확인합니다.  

|예외 메시지|
|------------------------|
|Azure 테이블이 없습니다.|
|Azure 테이블 "{table_name}"이(가) 없습니다.|


## <a name="error-0072"></a>오류 0072  
 연결 시간이 초과되면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 연결 시간이 초과할 때 발생합니다. 데이터 원본 또는 대상에서 현재 연결 문제가 있는 경우(예: 인터넷 연결 속도가 느리거나 데이터 세트가 크거나 데이터에서 읽을 SQL 쿼리가 복잡한 처리를 수행하는 경우), 이 오류가 표시됩니다.  

**해결 방법:** 현재 Azure Storage 또는 인터넷에 대한 연결 속도가 느린 문제가 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|연결 시간이 초과되었습니다.|


## <a name="error-0073"></a>오류 0073  
 열을 다른 형식으로 변환하는 동안 오류가 발생하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 열을 또 다른 형식으로 변환할 수 없는 경우에 발생합니다.  구성 요소에 특정 형식이 필요하고 열을 새 형식으로 변환할 수 없는 경우 이 오류가 표시됩니다.  

**해결 방법:** 내부 예외를 기준으로 열을 변환할 수 있도록 입력 데이터 세트를 수정합니다.  

|예외 메시지|
|------------------------|
|열을 변환하지 못했습니다.|
|열을 {target_type}(으)로 변환하지 못했습니다.|


## <a name="error-0075"></a>오류 0075  
데이터 세트를 양자화할 때 잘못된 범주화 함수를 사용하면 예외가 발생합니다.  

Azure Machine Learning에서 이 오류는 지원되지 않는 메서드를 사용하여 데이터를 저장하려고 할 때 또는 매개 변수 조합이 잘못된 경우에 발생합니다.  

**해결 방법:**

이 이벤트에 대한 오류 처리는 범주화 방법의 더 많은 사용자 지정이 허용된 이전 버전의 Azure Machine Learning에서 도입되었습니다. 현재 모든 범주화 방법은 드롭다운 목록에서 선택한 항목을 기반으로 하므로 기술적으로는 이 오류가 더 이상 발생하지 않아야 합니다.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) component, consider reporting the issue in the [Microsoft Q&A question page for Azure Machine Learning](/answers/topics/azure-machine-learning-studio-classic.html), providing the data types, parameter settings, and the exact error message.  -->

|예외 메시지|
|------------------------|
|잘못된 범주화 함수를 사용했습니다.|


## <a name="error-0077"></a>오류 0077  
 알 수 없는 Blob 파일 쓰기 모드를 전달하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 Blob 파일 대상 또는 원본의 사양에 잘못된 인수가 전달된 경우에 발생합니다.  

**해결 방법:** Azure Blob Storage에서 데이터를 가져오거나 내보내는 거의 모든 구성 요소에서 쓰기 모드를 제어하는 매개 변수 값은 드롭다운 목록을 사용하여 할당됩니다. 따라서 잘못된 값을 전달할 수 없으므로 이 오류가 표시되지 않아야 합니다. 이 오류는 이후 릴리스에서 더 이상 사용되지 않습니다.  

|예외 메시지|
|------------------------|
|지원되지 않는 Blob 쓰기 모드입니다.|
|지원되지 않는 Blob 쓰기 모드 {blob_write_mode}입니다.|


## <a name="error-0078"></a>오류 0078  
 [데이터 가져오기](import-data.md)에 대한 HTTP 옵션이 리디렉션을 나타내는 3xx 상태 코드를 수신할 때 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 [데이터 가져오기](import-data.md)에 대한 HTTP 옵션이 리디렉션을 나타내는 3xx(301, 302, 304 등) 상태 코드를 수신할 때 발생합니다. 브라우저를 다른 페이지로 리디렉션하는 HTTP 원본에 연결하려고 하면 이 오류가 표시됩니다. 웹 사이트 리디렉션은 보안상의 이유로 Azure Machine Learning에 대한 데이터 원본으로 허용되지 않습니다.  

**해결 방법:** 웹 사이트가 신뢰할 수 있는 웹 사이트인 경우, 리디렉션된 URL을 직접 입력합니다.  

|예외 메시지|
|------------------------|
|HTTP 리디렉션이 허용되지 않습니다.|


## <a name="error-0079"></a>오류 0079  
 Azure Storage 컨테이너 이름을 잘못 지정한 경우 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 Azure Storage 컨테이너 이름이 잘못 지정된 경우에 발생합니다. Azure Blob Storage에 쓰기를 실행할 때 **컨테이너로 시작하는 Blob 경로** 옵션을 사용하여 컨테이너와 Blob(파일) 이름을 모두 지정하지 않으면 이 오류가 발생합니다.  

**해결 방법:** [데이터 내보내기](export-data.md) 구성 요소를 다시 방문 하 고 blob에 대 한 지정 된 경로에 컨테이너 **/** 파일 이름 형식의 컨테이너와 파일 이름을 모두 포함 하는지 확인 합니다.  

|예외 메시지|
|------------------------|
|Azure Storage 컨테이너 이름이 잘못되었습니다.|
|Azure Storage 계정 이름 "{container_name}"이(가) 잘못되었습니다. 컨테이너/Blob 형식의 컨테이너 이름이 필요합니다.|


## <a name="error-0080"></a>오류 0080  
 구성 요소에서 모든 값이 누락 된 열을 사용할 수 없는 경우 예외가 발생 합니다.  

 Azure Machine Learning에서이 오류는 구성 요소에서 사용 하는 하나 이상의 열에 누락 값이 모두 포함 되어 있을 때 생성 됩니다. 예를 들어 구성 요소에서 각 열에 대 한 집계 통계를 계산 하는 경우 데이터를 포함 하지 않는 열에 대해서는 작업할 수 없습니다. 이러한 경우에는이 예외를 제외 하 고 구성 요소 실행이 중단 됩니다.  

**해결 방법:** 입력 데이터 세트를 다시 방문하여 누락된 모든 값을 포함하는 열을 제거합니다.  

|예외 메시지|
|------------------------|
|모든 값이 누락된 열은 허용되지 않습니다.|
|열 {col_index_or_name}에 누락된 모든 값이 있습니다.|


## <a name="error-0081"></a>오류 0081  
 축소할 차원의 수가 하나 이상의 스파스 기능 열을 포함 하는 입력 데이터 집합의 기능 열 수와 같으면 PCA 구성 요소에서 예외가 발생 합니다.  

 다음 조건이 충족되는 경우 Azure Machine Learning에서 이 오류가 발생합니다. (a) 입력 데이터 세트에 하나 이상의 스파스 열이 있고 (b) 요청된 차원의 최종 개수가 입력 차원의 수와 동일한 경우.  

**해결 방법:** 출력의 차원 수를 입력의 차원 수보다 적게 줄이는 것이 좋습니다. PCA 애플리케이션에서는 일반적입니다.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|예외 메시지|
|------------------------|
|스파스 기능 열을 포함하는 데이터 세트에서 줄이려는 차원 수는 기능 열의 수보다 작아야 합니다.|


## <a name="error-0082"></a>오류 0082  
 모델을 정상적으로 역직렬화할 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 저장된 기계 학습 모델 또는 변환을 호환성이 손상되는 변경의 결과로서 Azure Machine Learning 런타임의 최신 버전에서 로드할 수 없을 때 발생합니다.  

**해결 방법:** 모델 또는 변환을 생성한 학습 파이프라인을 다시 실행해야 하며 해당 모델 또는 변환은 다시 저장해야 합니다.  

|예외 메시지|
|------------------------|
|모델을 역직렬화할 수 없습니다. 모델이 이전 버전 serialization 형식으로 직렬화되었을 수 있습니다. 모델을 재학습시킨 뒤 다시 저장합니다.|


## <a name="error-0083"></a>오류 0083  
 학습에 사용되는 데이터 세트를 구체적 학습자 유형에 사용할 수 없으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 데이터 세트가 학습 중인 학습자와 호환되지 않을 때 발생합니다. 예를 들어 데이터 세트에는 각 행에 누락된 값이 적어도 하나 이상 포함되어 있을 수 있으므로 학습 중에 전체 데이터 세트를 건너뛰면 됩니다. 경우에 따라서는 변칙 검색 등의 일부 기계 학습 알고리즘에서 레이블을 표시할 필요가 없으며, 데이터 세트에 레이블이 있는 경우에는 이 예외가 발생할 수 있습니다.  

**해결 방법:** 입력 데이터 세트에 대한 요구 사항을 확인하는 데 사용되는 학습자의 설명서를 참조하세요. 열을 검사하여 필요한 모든 열이 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|학습에 사용되는 데이터 세트가 잘못되었습니다.|
|{data_name}에 잘못된 학습용 데이터가 포함되어 있습니다.|
|{data_name}에 잘못된 학습용 데이터가 포함되어 있습니다. 학습자 유형: {learner_type}.|
|{data_name}에 잘못된 학습용 데이터가 포함되어 있습니다. 학습자 유형: {learner_type}. 이유: {reason}.|
|학습 데이터 {data_name}에서 "{action_name}" 작업을 적용할 수 없습니다. 이유: {reason}.|


## <a name="error-0084"></a>오류 0084  
 R 스크립트에서 생성된 점수를 평가할 때 예외가 발생합니다. 이는 현재 지원되지 않습니다.  

 이 Azure Machine Learning 오류는 점수를 포함 하는 R 스크립트의 출력을 사용 하 여 모델을 평가 하기 위해 구성 요소 중 하나를 사용 하려고 할 때 발생 합니다.  

**해결 방법:**

|예외 메시지|
|------------------------|
|사용자 지정 모델에서 생성된 점수 평가는 현재 지원되지 않습니다.|


## <a name="error-0085"></a>오류 0085  
 오류와 함께 스크립트 평가가 실패하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 구문 오류를 포함하는 사용자 지정 스크립트를 실행할 때 발생합니다.  

**해결 방법:** 외부 편집기에서 코드를 검토하고 오류가 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|스크립트 평가 중에 오류가 발생했습니다.|
|스크립트를 평가하는 동안 다음 오류가 발생했습니다. 자세한 내용은 출력 로그를 참조하세요.<br />---------- {script_language} 인터프리터에서 오류 메시지 시작 ----------<br />{message}<br />---------- {script_language} 인터프리터에서 오류 메시지 종료 ----------|


## <a name="error-0090"></a>오류 0090  
 Hive 테이블 만들기가 실패하면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 [데이터 내보내기](export-data.md)를 사용하거나 다른 옵션을 사용하여 HDInsight 클러스터에 데이터를 저장하고 지정된 Hive 테이블을 만들 수 없을 때 발생합니다.  

**해결 방법:** 클러스터와 연결 된 Azure storage 계정 이름을 확인 하 고 구성 요소 속성에 동일한 계정을 사용 하 고 있는지 확인 합니다.  

|예외 메시지|
|------------------------|
|Hive 테이블을 만들 수 없습니다. HDInsight 클러스터의 경우 클러스터와 연결 된 Azure storage 계정 이름이 구성 요소 매개 변수를 통해 전달 되는 것과 동일한 지 확인 하세요.|
|Hive 테이블 "{table_name}"을(를) 만들 수 없습니다. HDInsight 클러스터의 경우 클러스터와 연결 된 Azure storage 계정 이름이 구성 요소 매개 변수를 통해 전달 되는 것과 동일한 지 확인 하세요.|
|Hive 테이블 "{table_name}"을(를) 만들 수 없습니다. HDInsight 클러스터의 경우 클러스터와 연결된 Azure Storage 계정 이름이 "{cluster_name}"인지 확인합니다.|


## <a name="error-0102"></a>오류 0102  
 ZIP 파일을 추출할 수 없을 때 발생합니다.  

 Azure Machine Learning에서 이 오류는 .zip 확장명을 사용하는 압축된 패키지를 가져올 경우 패키지가 zip 파일이 아니거나 해당 파일이 지원되는 zip 형식을 사용하지 않을 때 발생합니다.  

**해결 방법:** 선택한 파일이 유효한 .zip 파일인지 확인하고 지원되는 압축 알고리즘 중 하나를 사용하여 압축되었는지 확인합니다.  

 데이터 세트를 압축된 형식으로 가져올 때 이 오류가 발생하는 경우, 포함된 모든 파일이 지원되는 파일 형식 중 하나를 사용하며 유니코드 형식으로 되어 있는지 확인합니다.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 원하는 파일을 압축 된 새 폴더로 읽어 보고 사용자 지정 구성 요소를 다시 추가 해 보십시오.  

|예외 메시지|
|------------------------|
|지정한 ZIP 파일이 올바른 형식이 아닙니다.|


## <a name="error-0105"></a>오류 0105  
 구성 요소 정의 파일에 지원 되지 않는 매개 변수 형식이 포함 된 경우이 오류가 표시 됩니다.  
  
 이 오류는 사용자 지정 구성 요소 xml 정의를 만들 때 정의에 있는 매개 변수 또는 인수의 형식이 지원 되는 형식과 일치 하지 않는 경우 Azure Machine Learning에 생성 됩니다.  
  
**해결 방법:** 사용자 지정 구성 요소 xml 정의 파일에서 **Arg** 요소의 type 속성이 지원 되는 유형 인지 확인 합니다.  
  
|예외 메시지|  
|------------------------|  
|지원되지 않는 매개 변수 유형입니다.|  
|지원되지 않는 '{0}' 매개 변수 유형을 지정했습니다.|  


## <a name="error-0107"></a>오류 0107  
 구성 요소 정의 파일이 지원 되지 않는 출력 형식을 정의 하는 경우 throw 됩니다.  
  
 Azure Machine Learning에서이 오류는 사용자 지정 구성 요소 xml 정의의 출력 포트 형식이 지원 되는 형식과 일치 하지 않는 경우에 생성 됩니다.  
  
**해결 방법:** 사용자 지정 구성 요소 xml 정의 파일에 있는 Output 요소의 type 속성이 지원 되는 유형 인지 확인 합니다.  
  
|예외 메시지|  
|------------------------|  
|지원되지 않는 출력 형식입니다.|  
|지원되지 않는 출력 형식 '{output_type}'이(가) 지정되었습니다.|  


## <a name="error-0125"></a>오류 0125  
 여러 데이터 세트의 스키마가 일치하지 않으면 발생합니다.  

**해결 방법:**

|예외 메시지|
|------------------------|
|데이터 세트 스키마가 일치하지 않습니다.|


## <a name="error-0127"></a>오류 0127  
 이미지 픽셀 크기가 허용되는 제한을 초과합니다.  

 이 오류는 분류를 위해 이미지 데이터 세트에서 이미지를 읽고 이 이미지가 모델에서 처리할 수 있는 것보다 큰 경우에 발생합니다.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|예외 메시지|
|------------------------|
|이미지 픽셀 크기가 허용되는 제한을 초과합니다.|
|'{file_path}' 파일의 이미지 픽셀 크기가 허용된 제한 '{size_limit}'을(를) 초과합니다.|


## <a name="error-0128"></a>오류 0128  
 범주 열에 대한 조건부 확률 수가 제한을 초과합니다.  

**해결 방법:**

|예외 메시지|
|------------------------|
|범주 열에 대한 조건부 확률 수가 제한을 초과합니다.|
|범주 열에 대한 조건부 확률 수가 제한을 초과합니다. '{column_name_or_index_1}' 및 '{column_name_or_index_2}' 열은 문제가 있는 쌍입니다.|


## <a name="error-0129"></a>오류 0129  
 데이터 세트의 열 수가 허용되는 제한을 초과합니다.  

**해결 방법:**

|예외 메시지|
|------------------------|
|데이터 세트의 열 수가 허용되는 제한을 초과합니다.|
|데이터 세트 '{dataset_name}'의 열 수가 허용되는 수를 초과합니다.|
|'{dataset_name}'의 데이터 세트에 있는 열 수가 허용되는 '{component_name}'의 제한을 초과합니다.|
|'{dataset_name}'의 데이터 세트에 있는 열 수가 '{component_name}'의 허용되는 '{limit_columns_count}' 제한을 초과합니다.|


## <a name="error-0134"></a>오류 0134
레이블 열이 없거나 레이블이 지정된 행 수가 부족할 때 예외가 발생합니다.  

이 오류는 구성 요소에 레이블 열이 필요하지만 열 선택 항목에 레이블 열을 포함하지 않았거나 레이블 열에 너무 많은 값이 누락된 경우에 발생합니다.

이 오류는 다운스트림 작업에 사용할 수 있는 행이 부족할 정도로 이전 작업에서 데이터 세트를 변경할 때 발생할 수도 있습니다. 예를 들어 **Partition 및 Sample** 구성 요소의 식을 사용하여 데이터 세트를 값으로 나눕니다. 이 식에 일치하는 항목이 없다면 파티션에서 생성된 데이터 세트 중 하나가 비게 됩니다.

해결 방법: 

 열 선택에 레이블 열을 포함하지만 인식할 수 없는 경우 [메타데이터 편집](edit-metadata.md) 구성 요소를 사용하여 레이블 열로 표시합니다.

  <!--Use the [Summarize Data](summarize-data.md) component to generate a report that shows how many values are missing in each column. -->
  그런 다음 [누락된 데이터 정리](clean-missing-data.md) 구성 요소를 사용하여 레이블 열에 누락된 값이 있는 행을 제거할 수 있습니다. 

 입력 데이터 세트에 올바른 데이터가 포함되어 있는지 확인하고 작업 요구 사항을 충족하기에 충분한 수의 행이 있는지 확인합니다. 알고리즘에서 최소 수의 데이터 행이 필요하며 다만 데이터에 몇 개의 행만 포함되어 있거나 헤더만 포함되어 있는 경우, 상당수 알고리즘에서 오류 메시지가 생성됩니다.

|예외 메시지|
|------------------------|
|레이블 열이 없거나 레이블이 지정된 행 수가 부족할 때 예외가 발생합니다.|
|레이블 열이 없거나 레이블이 지정된 {required_rows_count}개의 행보다 레이블 열의 수가 적을 때 예외가 발생합니다.|
|데이터 세트 {dataset_name}의 레이블 열이 없거나 레이블이 지정된 {required_rows_count}개의 행보다 레이블 열의 수가 적을 때 예외가 발생합니다.|


## <a name="error-0138"></a>오류 0138  
 메모리가 소진되어 구성 요소 실행을 완료할 수 없습니다. 데이터 세트를 저해상도 처리하면 문제를 완화하는 데 도움이 될 수 있습니다.  

 이 오류는 실행 중인 구성 요소에 Azure 컨테이너에서 사용할 수 있는 것보다 더 많은 메모리가 필요할 때 발생합니다. 이는 대량 데이터 세트를 사용하여 작업을 진행 중일 때 그리고 현재 작업이 메모리에 적합지 않은 경우에 발생할 수 있습니다.  

**해결 방법:** 대량 데이터 세트를 읽으려고 할 때 작업을 완료할 수 없는 경우, 데이터 세트를 저해상도 처리하면 도움이 될 수 있습니다.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to such error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|예외 메시지|
|------------------------|
|메모리가 소진되어 구성 요소 실행을 완료할 수 없습니다.|
|메모리가 소진되어 구성 요소 실행을 완료할 수 없습니다. 세부 정보: {details}|


## <a name="error-0141"></a>오류 0141  
 범주 및 문자열 열에서 선택한 숫자 열 및 고유 값의 수가 너무 적으면 예외가 발생합니다.  

 Azure Machine Learning에서 이 오류는 선택한 열에서 작업을 수행할 수 있는 고유 값이 부족한 경우에 발생합니다.  

**해결 방법:** 일부 작업은 기능 및 범주 열에 대한 통계 연산을 수행하며, 값이 충분하지 않으면 작업이 실패하거나 잘못된 결과를 반환할 수 있습니다. 데이터 세트를 검사하여 기능 및 레이블 열에 있는 값이 몇 개인지 확인한 다음, 수행하려는 작업이 통계적으로 유효한지 여부를 확인합니다.  

 원본 데이터 세트가 올바르면 일부 업스트림 데이터 조작 또는 메타데이터 작업에서 데이터를 변경하고 일부 값을 제거했는지 여부도 확인할 수 있습니다.  

 업스트림 작업에 분할, 샘플링 또는 리샘플링이 포함된 경우, 필요한 수의 행과 값이 출력에 포함되어 있는지 확인합니다.  

|예외 메시지|
|------------------------|
|범주 및 문자열 열에서 선택한 숫자 열 및 고유 값의 수가 너무 적습니다.|
|범주 및 문자열 열에서 선택된 숫자 열 및 고유 값의 총 개수(현재 {actual_num})는 적어도 {lower_boundary} 이상이어야 합니다.|


## <a name="error-0154"></a>오류 0154  
 사용자가 호환되지 않는 열 형식으로 키 열의 데이터를 조인하려고 할 때 예외가 발생합니다.

|예외 메시지|
|------------------------|
|키 열 요소 형식이 호환되지 않습니다.|
|키 열 요소 형식이 호환되지 않습니다(왼쪽: {keys_left}, 오른쪽: {keys_right}).|


## <a name="error-0155"></a>오류 0155  
 데이터 세트의 열 이름이 문자열이 아닐 때 예외가 발생합니다.

|예외 메시지|
|------------------------|
|데이터 프레임 열 이름은 문자열 형식이어야 합니다. 열 이름은 문자열이 아닙니다.|
|데이터 프레임 열 이름은 문자열 형식이어야 합니다. 열 이름 {column_names}은(는) 문자열이 아닙니다.|


## <a name="error-0156"></a>오류 0156  
 Azure SQL Database에서 데이터를 읽지 못할 때 예외가 발생합니다.

|예외 메시지|
|------------------------|
|Azure SQL Database에서 데이터를 읽지 못했습니다.|
|Azure SQL Database에서 데이터를 읽지 못함: {detailed_message} DB: {database_server_name}:{database_name} 쿼리: {sql_statement}|


## <a name="error-0157"></a>오류 0157  
 데이터 저장소를 찾을 수 없습니다.

|예외 메시지|
|------------------------|
|데이터 저장소 정보가 잘못되었습니다.|
|데이터 저장소 정보가 잘못되었습니다. 작업 영역 '{workspace_name}'에서 AzureML 데이터 저장소 '{datastore_name}'을(를) 가져오지 못했습니다.|


## <a name="error-0158"></a>오류 0158
 변환 디렉터리가 잘못된 경우에 발생합니다.

|예외 메시지|
|------------------------------------------------------------|
|지정된 TransformationDirectory가 잘못되었습니다.|
|TransformationDirectory "{arg_name}"이(가) 잘못되었습니다. 이유: {reason}. 변환 파일을 생성하는 학습 실험을 다시 실행합니다. 학습 실험이 삭제된 경우, 변환 파일을 다시 만들고 저장하세요.|
|TransformationDirectory "{arg_name}"이(가) 잘못되었습니다. 이유: {reason}. {troubleshoot_hint}|


## <a name="error-0159"></a>오류 0159
 구성 요소 모델 디렉터리를 유효하지 않으면 예외가 발생합니다. 

|예외 메시지|
|------------------------------------------------------------|
|지정된 ModelDirectory가 잘못되었습니다.|
|ModelDirectory "{arg_name}"이(가) 잘못되었습니다.|
|ModelDirectory "{arg_name}"이(가) 잘못되었습니다. 이유: {reason}.|
|ModelDirectory "{arg_name}"이(가) 잘못되었습니다. 이유: {reason}. {troubleshoot_hint}|


## <a name="error-1000"></a>오류 1000  
내부 라이브러리 예외입니다.  

이 오류는 달리 처리되지 않은 내부 엔진 오류를 캡처하기 위해 제공됩니다. 따라서 이 오류의 원인은 오류를 생성한 구성 요소에 따라 다를 수 있습니다.  

자세한 도움말을 보려면 해당 오류와 함께 표시되는 세부 메시지를 시나리오에 대한 설명(입력으로 사용되는 데이터를 포함)을 덧붙여 [Azure Machine Learning 포럼](/answers/topics/azure-machine-learning.html)에 게시하는 것이 좋습니다. 이 피드백은 오류의 우선 순위를 지정하고 추가 작업을 위해 가장 중요한 문제를 식별하는 데 도움이 됩니다.  

|예외 메시지|
|------------------------|
|라이브러리 예외입니다.|
|라이브러리 예외: {exception}.|
|알 수 없는 라이브러리 예외: {exception}. {customer_support_guidance}.|


## <a name="execute-python-script-component"></a>Python 스크립트 실행 구성 요소

**Python 스크립트 실행 구성 요소의** **70_driver_logs** **azureml_main** 검색하면 오류가 발생한 줄을 찾을 수 있습니다. 예를 들어 "File "/tmp/tmp01_ID/user_script.py", line 17, in azureml_main"은 Python 스크립트의 17번째 줄에서 오류가 발생했음을 나타냅니다.

## <a name="distributed-training"></a>분산 학습

현재 디자이너는 [및 PyTorch 모델](train-pytorch-model.md) 학습 구성 요소에 대한 분산 학습을 지원합니다.

<!-- [Train Wide and Deep Recommender](train-wide-and-deep-recommender.md) component  -->

구성 요소에서 사용하도록 설정된 분산 학습이 로그 없이 실패하는 경우 `70_driver` 오류 세부 정보를 확인할 수 `70_mpi_log` 있습니다.

  다음 예제에서는 실행 설정의 **노드 수** 가 컴퓨팅 클러스터의 사용 가능한 노드 수보다 크다는 것을 보여줍니다.
  
  [![노드 수 오류를 보여주는 스크린샷](./media/module/distributed-training-node-count-error.png)](./media/module/distributed-training-node-count-error.png#lightbox)

  다음 예제에서는 **노드당 프로세스 수** 가 컴퓨팅의 **처리 단위** 보다 크다는 것을 보여줍니다.

  [ ![mpi 로그를 보여주는 스크린샷](./media/module/distributed-training-error-mpi-log.png) ](./media/module/distributed-training-error-mpi-log.png#lightbox)

그렇지 않으면 각 프로세스별로 `70_driver_log`를 확인할 수 있습니다. `70_driver_log_0`은 마스터 프로세스용입니다.

  [![드라이버 로그를 보여주는 스크린샷](./media/module/distributed-training-error-driver-log.png) ](./media/module/distributed-training-error-driver-log.png#lightbox)