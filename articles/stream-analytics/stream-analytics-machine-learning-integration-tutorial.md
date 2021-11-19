---
title: Machine Learning Studio(클래식)와 Azure Stream Analytics 통합
description: 이 문서에서는 사용자 정의 함수를 사용하여 Azure Machine Learning Studio(클래식)를 통합하는 간단한 Azure Stream Analytics 작업을 신속하게 설정하는 방법을 설명합니다.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 974d2075f2d77792165d7d195d57e998870dcc57
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808853"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-machine-learning-studio-classic"></a>Azure Stream Analytics 및 Machine Learning Studio(클래식)를 통해 감정 분석 수행

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

이 문서에서는 감정 분석에 Machine Learning Studio(클래식)를 사용하는 간단한 Azure Stream Analytics 작업을 설정하는 방법을 보여줍니다. Cortana Intelligence 갤러리의 Studio(클래식) 감정 분석 모델을 사용하여 스트리밍 텍스트 데이터를 분석하고 감정 점수를 결정합니다.

> [!TIP]
> 성능 및 안정성 향상을 위해 [Machine Learning](machine-learning-udf.md) Studio(클래식) UDF 대신 Azure Machine Learning UDF를 사용하는 것이 좋습니다.

문서에서 알아본 내용을 다음과 같은 시나리오에 적용할 수 있습니다.

* 스트리밍 Twitter 데이터에 대한 실시간 감정 분석
* 지원 담당자와 고객 채팅 레코드 분석
* 포럼, 블로그 및 동영상에 대한 의견 평가
* 기타 실시간 예측 점수 매기기 시나리오

사용자가 만든 Stream Analytics 작업은 감정 분석 모델을 Blob Storage의 샘플 텍스트에 UDF(사용자 정의 함수)로 적용합니다. 출력(감정 분석의 결과)은 다른 CSV 파일에 있는 동일한 Blob Storage에 기록됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음이 필요합니다.

* 활성화된 Azure 구독.

* 일부 Twitter 데이터가 있는 CSV 파일. [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)에서 샘플 파일을 다운로드하거나 사용자 고유의 파일을 만들 수 있습니다. 실제 시나리오에서는 Twitter 데이터 스트림에서 직접 데이터를 가져옵니다.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>스토리지 컨테이너 만들기 및 CSV 입력 파일 업로드

이 단계에서는 스토리지 컨테이너에 CSV 파일을 업로드합니다.

1. Azure Portal 리소스 Storage Storage 계정 **만들기를**  >    >  선택합니다.

2. *기본 사항* 탭에 다음 세부 정보를 입력하고 나머지 필드의 기본값을 그대로 둡니다.

   |필드  |값  |
   |---------|---------|
   |Subscription|구독을 선택합니다.|
   |Resource group|리소스 그룹을 선택합니다.|
   |스토리지 계정 이름|스토리지 계정의 이름을 입력합니다. 이름은 Azure에서 고유해야 합니다.|
   |위치|위치를 선택합니다. 모든 리소스는 동일한 위치를 사용해야 합니다.|
   |계정 종류|BlobStorage|

   ![스토리지 계정 세부 정보 입력](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. **검토 + 생성** 를 선택합니다. 그런 다음, **만들기를** 선택하여 스토리지 계정을 배포합니다.

4. 배포가 완료되면 스토리지 계정으로 이동합니다. **Blob service** 아래에서 **컨테이너** 를 선택합니다. 그런 **다음, + 컨테이너를** 선택하여 새 컨테이너를 만듭니다.

   ![입력에 대한 Blob Storage 컨테이너 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. 컨테이너의 이름을 제공하고 공용 **액세스 수준이** 프라이빗 으로 설정되어 있는지 **확인합니다.** 완료되면 **만들기** 를 선택합니다.

   ![Blob 컨테이너 정보 지정](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. 새로 만든 컨테이너로 이동하여 **업로드** 선택합니다. 이전에 다운로드한sampleinput.csv파일을 **업로드.**

   ![컨테이너의 '업로드' 단추](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence Gallery의 정서 분석 모델 추가

이제 Blob에 샘플 데이터가 있으므로 Cortana Intelligence Gallery에서 감정 분석 모델을 사용할 수 있습니다.

1. Cortana Intelligence Gallery에서 [예측 감정 분석 모델](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) 페이지로 이동합니다.  

2. **Studio에서 열기(클래식)** 를 선택합니다.  
   
   ![Stream Analytics Machine Learning Studio(클래식), Studio(클래식) 열기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 로그인하여 작업 영역으로 이동합니다. 위치를 선택합니다.

4. 페이지 아래쪽에서 **실행을** 선택합니다. 프로세스가 실행되려면 약 1분이 걸립니다.

   ![Studio에서 실험 실행(클래식)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. 프로세스를 성공적으로 실행한 후에 페이지의 맨 아래에서 **웹 서비스 배포** 를 선택합니다.

   ![Studio(클래식)에서 실험을 웹 서비스로 배포](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 감정 분석 모델을 사용할 준비가 됐 있는지 확인하려면 **테스트** 단추를 선택합니다. "I love Microsoft"와 같은 텍스트를 입력합니다.

   ![Studio에서 테스트 실험(클래식)](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   테스트가 작동하면 다음 예제와 유사한 결과가 표시됩니다.

   ![Studio(클래식)에서 테스트 결과](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. **앱** 열에서 Excel **2010 이전 통합 문서** 링크를 선택하여 Excel 통합 문서를 다운로드합니다. 통합 문서는 나중에 Stream Analytics 작업을 설정하는 데 필요한 API 키와 URL을 포함합니다.

    ![Stream Analytics Machine Learning Studio(클래식), 빠른 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Studio(클래식) 모델을 사용하는 Stream Analytics 작업 만들기

이제 CSV 파일에서 Blob Storage로의 샘플 트윗을 읽을 수 있는 Stream Analytics 작업을 만들 수 있습니다.

### <a name="create-the-job"></a>작업 만들기

[Azure Portal](https://portal.azure.com) 이동하여 Stream Analytics 작업을 만듭니다. 이 프로세스에 익숙하지 않은 경우 [Azure Portal 사용하여 Stream Analytics 작업 만들기를](stream-analytics-quick-create-portal.md)참조하세요.

### <a name="configure-the-job-input"></a>작업 입력 구성

작업은 이전에 업로드한 CSV 파일에서 Blob Storage로 해당 입력을 가져옵니다.

1. Stream Analytics 작업으로 이동합니다. **작업 토폴로지** 아래에서 **입력** 옵션을 선택합니다. **스트림 입력**  > **Blob Storage** 추가를 선택합니다.

2. **Blob Storage** 세부 정보를 다음 값으로 채웁니다.

   |필드  |값  |
   |---------|---------|
   |입력 별칭|입력에 이름을 지정합니다. 쿼리를 작성할 때 이 별칭을 기억하세요.|
   |Subscription|구독을 선택합니다.|
   |스토리지 계정|이전 단계에서 만든 스토리지 계정을 선택합니다.|
   |컨테이너|이전 단계에서 만든 컨테이너를 선택합니다.|
   |이벤트 직렬화 형식|CSV|

3. **저장** 을 선택합니다.

### <a name="configure-the-job-output"></a>작업 출력 구성

작업은 입력을 가져오는 동일한 Blob Storage에 결과를 보냅니다.

1. Stream Analytics 작업으로 이동합니다. **작업 토폴로지에서** **출력** 옵션을 선택합니다.   >  **Blob Storage** 추가를 선택합니다.

2. **Blob Storage** 양식을 다음 값으로 채웁니다.

   |필드  |값  |
   |---------|---------|
   |입력 별칭|입력에 이름을 지정합니다. 쿼리를 작성할 때 이 별칭을 기억하세요.|
   |Subscription|구독을 선택합니다.|
   |스토리지 계정|이전 단계에서 만든 스토리지 계정을 선택합니다.|
   |컨테이너|이전 단계에서 만든 컨테이너를 선택합니다.|
   |이벤트 직렬화 형식|CSV|

3. **저장** 을 선택합니다.

### <a name="add-the-studio-classic-function"></a>Studio(클래식) 함수 추가

이전에 Studio(클래식) 모델을 웹 서비스에 게시했습니다. 이 시나리오에서는 Stream Analysis 작업이 실행될 때 감정 분석에 대한 샘플을 입력에서 웹 서비스로 보냅니다. Studio(클래식) 웹 서비스는 감정( `positive` `neutral` , 또는 `negative` )과 트윗이 긍정적일 확률을 반환합니다.

이 섹션에서는 스트림 분석 작업에서 함수를 정의합니다. 웹 서비스에 트윗을 보내고 응답을 다시 가져오기 위해 함수를 호출할 수 있습니다.

1. Excel 통합 문서의 앞부분에서 다운로드한 웹 서비스 URL 및 API 키가 있는지 확인합니다.

2. Stream Analytics 작업으로 이동합니다. 그런 **다음, Functions**  >  + Azure ML Studio **추가를**  >   선택합니다.

3. **Azure Machine Learning 함수** 양식을 다음 값으로 채웁니다.

   |필드  |값  |
   |---------|---------|
   | 함수 별칭 | 이름을 사용하고 `sentiment` 수동으로 Azure Machine Learning 함수 설정 **제공을** 선택합니다. 이 설정은 URL 및 키를 입력하는 옵션을 제공합니다.      |
   | URL| 웹 서비스 URL을 붙여넣습니다.|
   |키 | API 키를 붙여넣습니다. |

4. **저장** 을 선택합니다.

### <a name="create-a-query-to-transform-the-data"></a>데이터를 변환하는 쿼리 만들기

Stream Analytics는 선언적인 SQL 기반 쿼리를 사용하여 입력을 검사하고 처리합니다. 이 섹션에서는 입력에서 각 트윗을 읽은 다음, Studio(클래식) 함수를 호출하여 감정 분석을 수행하는 쿼리를 만듭니다. 그런 다음 쿼리는 정의한 출력으로 결과를 보냅니다(Blob Storage).

1. Stream Analytics 작업 개요로 돌아갑니다.

2. **작업 토폴로지** 에서 **쿼리** 를 선택합니다.

3. 다음 쿼리를 입력합니다.

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   쿼리는 이전에 만든 `sentiment` 함수()를 호출하여 입력의 각 트윗에 대한 감정 분석을 수행합니다.

4. **저장을** 선택하여 쿼리를 저장합니다.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics 작업을 시작하고 출력을 확인합니다.

이제 Stream Analytics 작업을 시작할 수 있습니다.

### <a name="start-the-job"></a>작업 시작

1. Stream Analytics 작업 개요로 돌아갑니다.

2. 페이지 위쪽에서 **시작을** 선택합니다.

3. **작업 시작에서** **사용자 지정** 을 선택한 다음, CSV 파일을 Blob Storage에 업로드하기 하루 전에 선택합니다. 완료되면 **시작** 을 선택합니다.  

### <a name="check-the-output"></a>출력 파일 확인

1. **모니터링** 상자에서 작업을 확인할 때까지 몇 분 동안 작업을 실행하겠습니다.

2. 일반적으로 Blob Storage의 콘텐츠를 검사하는 데 사용하는 도구가 있는 경우 해당 도구를 사용하여 컨테이너를 검사합니다. 또는 Azure Portal에서 다음 단계를 수행합니다.

      1. Azure Portal 스토리지 계정을 찾고 계정 내에서 컨테이너를 찾습니다. 컨테이너에 두 개의 파일이 표시됩니다. 하나는 샘플 트윗을 포함하는 파일이고 다른 하나는 Stream Analytics 작업에 의해 생성된 CSV 파일입니다.
      2. 생성된 파일을 마우스 오른쪽 단추로 클릭한 다음 **다운로드** 를 선택합니다.

3. 생성된 CSV 파일을 엽니다. 다음 예제와 유사한 출력이 표시됩니다.  

   ![Stream Analytics Machine Learning Studio(클래식), CSV 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>메트릭 보기

Studio(클래식) 함수 관련 메트릭을 볼 수도 있습니다. 다음 함수 관련 메트릭은 작업 개요의 **모니터링** 상자에 표시됩니다.

* **함수 요청은** Studio(클래식) 웹 서비스로 전송된 요청 수를 나타냅니다.  
* **함수 이벤트** 는 요청의 이벤트 수를 나타냅니다. 기본적으로 Studio(클래식) 웹 서비스에 대한 각 요청에는 최대 1,000개의 이벤트가 포함됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API 및 Machine Learning Studio 통합(클래식)](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)
