---
title: 프로덕션 모델에서 데이터 수집
titleSuffix: Azure Machine Learning
description: AKS(Azure Kubernetes Service) 클러스터에 배포된 Azure Machine Learning 모델에서 데이터를 수집하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: how-to
ms.custom: data4ml
ms.openlocfilehash: a629b2ca07b9b807ba42e4e071b7f9430ace2108
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427574"
---
# <a name="collect-data-from-models-in-production"></a>프로덕션 환경에서 모델용 데이터 수집

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 배포된 Azure Machine Learning 모델에서 데이터를 수집하는 방법을 보여 줍니다. 수집된 데이터는 Azure Blob Storage에 저장됩니다.

수집을 사용하도록 설정하면 수집되는 데이터를 활용하여 다음 작업을 수행할 수 있습니다.

* 수집한 프로덕션 데이터의 [데이터 드리프트를 모니터링](how-to-monitor-datasets.md).

* [Power BI](#powerbi) 또는 [Azure Databricks](#databricks) 를 사용하여 수집된 데이터를 분석

* 모델을 다시 학습시키거나 최적화할 시기를 더 효율적으로 결정

* 수집한 데이터로 모델 다시 학습

## <a name="what-is-collected-and-where-it-goes"></a>수집되는 데이터 및 데이터가 이동하는 위치 파악

다음 데이터를 수집할 수 있습니다.

* AKS 클러스터에 배포된 웹 서비스의 모델 입력 데이터. 음성 오디오, 이미지, 비디오는 수집되지 ‘않습니다’.
  
* 프로덕션 입력 데이터를 사용하는 모델 예측

>[!NOTE]
> 이 데이터에 대한 사전 집계 및 사전 계산은 현재 수집 서비스에서 지원하지 않습니다.

출력은 Blob 스토리지에 저장됩니다. 데이터가 Blob 스토리지에 추가되기 때문에 원하는 도구를 선택하여 분석을 실행할 수 있습니다.

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> 0\.1.0a16 이전 버전의 Python용 Azure Machine Learning SDK 버전에서는 `designation` 인수의 이름이 `identifier`로 지정됩니다. 이전 버전을 사용하여 코드를 개발한 경우 이에 맞게 업데이트해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

- Azure Machine Learning 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. 해당 항목을 설치하는 방법을 알아보려면 [개발 환경 구성 방법](how-to-configure-environment.md)을 참조하세요.

- AKS에 배포할 학습된 기계 학습 모델이 필요합니다. 해당 모델이 없으면 [이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

- AKS 클러스터가 필요합니다. 클러스터를 만들고 배포하는 방법에 대한 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

- [환경을 설정](how-to-configure-environment.md)하고 [Azure Machine Learning 모니터링 SDK](/python/api/overview/azure/ml/install)를 설치합니다.

## <a name="enable-data-collection"></a>데이터 수집 사용

Azure Machine Learning 또는 기타 도구를 통해 배포하는 모델과 관계없이 [데이터 수집](/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector)을 사용하도록 설정할 수 있습니다.

데이터 수집을 사용하도록 설정하려면 다음을 수행해야 합니다.

1. 점수 매기기 파일을 엽니다.

1. 파일 맨 위에 다음 코드를 추가합니다.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. `init` 함수에서 데이터 수집 변수를 선언합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3", "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* 는 선택적 매개 변수입니다. 모델에 필요하지 않은 경우 사용할 필요가 없습니다. *CorrelationId* 를 사용하면 *LoanNumber* 나 *CustomerId* 와 같은 다른 데이터와 함께 보다 쉽게 매핑할 수 있습니다.
    
    *Identifier* 매개 변수는 나중에 Blob에서 폴더 구조를 빌드하는 데 사용됩니다. 이를 사용하여 원시 데이터와 처리된 데이터를 구분할 수 있습니다.

1. `run(input_df)` 함수에 다음 코드 줄을 추가합니다.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our prediction data into Azure Blob
    ```

1. 데이터 수집은 AKS에서 서비스를 배포할 때 **true** 로 자동 설정되지 ‘않습니다’. 다음 예제와 같이 구성 파일을 업데이트합니다.

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    이 구성을 변경하여 서비스 모니터링용 Application Insights도 사용 설정할 수 있습니다.

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. 새 이미지를 만들고 기계 학습 모델을 배포하려면 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

1. 웹 서비스 환경의 Conda 종속성에 ‘Azure 모니터링’ pip 패키지를 추가합니다.
  ```Python
    env = Environment('webserviceenv')
    env.python.conda_dependencies = CondaDependencies.create(conda_packages=['numpy'],pip_packages=['azureml-defaults','azureml-monitoring','inference-schema[numpy-support]'])
  ```


## <a name="disable-data-collection"></a>데이터 수집 비활성화

데이터 수집은 언제든지 중지할 수 있습니다. 데이터 수집을 사용하지 않도록 설정하려면 Python 코드를 사용합니다.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>데이터 유효성 검사 및 분석

선호하는 도구를 선택하여 Blob 스토리지에 수집된 데이터를 분석할 수 있습니다.

### <a name="quickly-access-your-blob-data"></a>Blob 데이터에 빠르게 액세스

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. 작업 영역을 엽니다.

1. **스토리지** 를 선택합니다.

    [![스토리지 옵션을 선택합니다](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob의 출력 데이터 경로는 다음 구문을 따릅니다.

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Power BI를 사용하여 모델 데이터 분석

1. [Power BI Desktop](https://www.powerbi.com)을 다운로드하고 엽니다.

1. **데이터 가져오기** 를 선택하고 [**Azure Blob Storage**](/power-bi/desktop-data-sources)를 선택합니다.

    [![Power BI Blob 설정](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. 스토리지 계정 이름을 추가하고 스토리지 키를 입력합니다. Blob의 **설정** > **액세스 키** 를 선택하여 이 정보를 찾을 수 있습니다.

1. **모델 데이터** 컨테이너를 선택하고 **편집** 을 선택합니다.

    [![Power BI 탐색기](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 쿼리 편집기에서 **이름** 열 아래를 클릭하고 스토리지 계정을 추가합니다.

1. 필터에 모델 경로를 입력합니다. 특정 연도 또는 특정 월의 파일만 보려면 필터 경로를 확장하기만 하면 됩니다. 예를 들어 3월 데이터만 보려면 다음 필터 경로를 사용합니다.

   /modeldata/\<subscriptionid>/\<resourcegroupname>/\<workspacename>/\<webservicename>/\<modelname>/\<modelversion>/\<designation>/\<year>/3

1. **이름** 값을 기준으로 관련된 데이터를 필터링합니다. 예측 및 입력을 저장한 경우 각각에 대해 쿼리를 만들어야 합니다.

1. **콘텐츠** 열 제목 옆에 있는 하향 이중 화살표를 선택하여 파일을 결합합니다.

    [![Power BI 콘텐츠](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. **확인** 을 선택합니다. 데이터가 미리 로드됩니다.

    [![Power BI 파일 결합](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. **닫기 및 적용** 을 선택합니다.

1. 입력 및 예측을 추가한 경우 자동으로 **RequestId** 값에 따라 테이블 상관관계 순서가 설정됩니다.

1. 모델 데이터에 대한 사용자 지정 보고서 빌드를 시작합니다.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Azure Databricks를 사용하여 모델 데이터 분석

1. [Azure Databricks 작업 영역](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) 만들기

1. Databricks 작업 영역으로 이동합니다.

1. Databricks 작업 영역에서 **데이터 업로드** 를 선택합니다.

    [![Databricks 데이터 업로드 옵션 선택](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. **새 테이블 만들기** 를 선택하고 **기타 데이터 원본** > **Azure Blob Storage** > **Notebook에서 테이블 만들기** 를 선택합니다.

    [![Databricks 테이블 만들기](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 데이터 위치를 업데이트합니다. 예를 들면 다음과 같습니다.

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Databricks 설정](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 데이터를 보고 분석하려면 템플릿의 단계를 따릅니다.

## <a name="next-steps"></a>다음 단계

수집한 데이터에 대한 [데이터 드리프트를 검색](how-to-monitor-datasets.md)합니다.
