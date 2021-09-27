---
title: 릴리스의 새로운 기능이란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 및 기계 학습 및 데이터 준비 Python SDK에 대한 최신 업데이트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 08/18/2021
ms.openlocfilehash: f4c7c43c0406ed7a6c05c3d83e467bc335c43093
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628112"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning 릴리스 정보

이 문서에서는 Azure Machine Learning 릴리스에 대해 알아봅니다.  전체 SDK 참조 콘텐츠를 보려면 Azure Machine Learning의 [**Python용 기본 SDK**](/python/api/overview/azure/ml/intro) 참조 페이지를 확인하세요.

__RSS 피드__: 다음 URL을 복사하여 피드 판독기에 붙여넣으면 이 페이지가 업데이트될 때 알림을 받을 수 있습니다. `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`

## <a name="2021-09-07"></a>2021-09-07

### <a name="azure-machine-learning-sdk-for-python-v1340"></a>Azure Machine Learning Python v 1.34.0 SDK
  + **azureml-automl-core**
    + 이전에 학습 된 예측 파이프라인을 다시 맞추기 위한 지원이 추가 되었습니다.
    + 예측을 위해 학습 데이터 (샘플 내 예측)에 대 한 예측을 가져오는 기능을 추가 했습니다.
  + **azureml-automl-runtime**
    + AutoML 분류자 모델의 배포 된 끝점에서 예측 된 확률을 반환 하는 지원을 추가 합니다.
    + 사용자가 모든 예측이 정수 여야 함을 지정 하는 예측 옵션이 추가 되었습니다.
    + Training_data_label_column_name를 사용 하 여 로컬 실험을 위한 모델 설명 기능 이름의 일부가 되지 않도록 대상 열 이름이 제거 되었습니다.
    + 데이터 집합 입력으로
    + 이전에 학습 된 예측 파이프라인을 다시 맞추기 위한 지원이 추가 되었습니다.
    + 예측을 위해 학습 데이터 (샘플 내 예측)에 대 한 예측을 가져오는 기능을 추가 했습니다.
  + **azureml-core**
    + 테이블 형식 데이터 집합에서 스트림 열 유형, 탑재 및 스트림 열을 설정 하는 지원이 추가 되었습니다.
    + Kubernetes.attach_configuration (identity_type = None, identity_ids = None)에 추가 된 새 선택적 필드를 사용 하 여 SystemAssigned 또는 UserAssigned 된 id로 KubernetesCompute을 연결할 수 있습니다. Print (compute_target) 또는 compute_target ()를 호출할 때 새 id 필드가 포함 됩니다. identity_type, identity_id, principal_id 및 tenant_id/client_id.
  + **azureml-dataprep**
    + 테이블 형식 데이터 집합의 스트림 열 유형 설정에 대 한 지원이 추가 되었습니다. 테이블 형식 데이터 집합에서 스트림 열을 탑재 및 다운로드 하는 지원이 추가 되었습니다.
  + **azureml-defaults**
    + 종속성 `azureml-inference-server-http==0.3.1` 이에 추가 되었습니다 `azureml-defaults` .
  + **azureml.mlflow**
    + `max_results`및 선택적 매개 변수를 추가 하 여 LIST_EXPERIMENTS API의 페이지 매김을 허용 `page_token` 합니다. 설명서는 MLflow 공식 문서를 참조 하세요.
  + **azureml-sdk**
    + 사용 되지 않는 패키지 (azureml 교육)의 종속성이 azureml-sdk 내에서 바뀌었습니다.
    + Responsibleai에 azureml 추가-sdk 스페셜 기능
  + **azureml-train-automl-client**
    + `test_data` `test_size` 에서 및 매개 변수를 노출 `AutoMLConfig` 합니다. 이러한 매개 변수를 사용 하 여 모델 이후에 테스트 실행을 자동으로 시작할 수 있습니다. 
    + 학습 단계를 완료 했습니다. 테스트 실행은 최상의 모델을 사용 하 여 예측을 계산 하 고 이러한 예측이 제공 되 면 메트릭을 생성 합니다.

## <a name="2021-08-24"></a>2021-08-24

### <a name="azure-machine-learning-experimentation-user-interface"></a>Azure Machine Learning 실험 사용자 인터페이스
  + **삭제 실행**
    + 실행 삭제는 사용자가 작업 영역에서 하나 이상의 실행을 삭제할 수 있는 새로운 기능입니다.
    + 이 기능을 통해 사용자는 저장소 비용을 줄이고 UI에서 직접 실행 및 실험을 정기적으로 삭제 하 여 저장소 용량을 관리할 수 있습니다.
  + **일괄 처리 취소 실행**
    + 일괄 처리 취소 실행은 사용자가 실행 목록에서 취소할 실행을 하나 또는 여러 개 선택할 수 있게 해 주는 새로운 기능입니다. 
    + 이 기능을 통해 사용자는 여러 개의 지연 된 실행을 취소 하 고 클러스터에서 공간을 확보할 수 있습니다.

## <a name="2021-08-18"></a>2021-08-18

### <a name="azure-machine-learning-experimentation-user-interface"></a>Azure Machine Learning 실험 사용자 인터페이스
  + **실행 표시 이름**
    + 실행 표시 이름은 실행에 할당할 수 있는 편집 가능하고 선택적인 새 표시 이름입니다. 
    + 이 이름을 통해 실행을 더 효과적으로 추적, 구성 및 검색할 수 있습니다. 
    + 실행 표시 이름은 기본적으로 adjective_noun_guid 형식(예: awesome_watch_2i3uns)으로 지정됩니다. 
    + 이 기본 이름은 더 높은 수준의 사용자 지정이 가능한 이름으로 편집할 수 있습니다. Azure Machine Learning 스튜디오 사용자 인터페이스의 실행 세부 정보 페이지에서 편집할 수 있습니다.   

## <a name="2021-08-02"></a>2021-08-02

### <a name="azure-machine-learning-sdk-for-python-v1330"></a>Python용 Azure Machine Learning SDK v1.33.0
  + **azureml-automl-core**
    + XGBoost 모델 검색에 대한 오류 처리가 향상되었습니다.
    + 예측 및 회귀 작업을 위해 float에서 정수로 예측을 변환할 수 있는 가능성이 추가되었습니다.
    + AutoMLConfig의 enable_early_stopping 기본값이 True로 업데이트되었습니다.
  + **azureml-automl-runtime**
    + 예측 및 회귀 작업을 위해 float에서 정수로 예측을 변환할 수 있는 가능성이 추가되었습니다.
    + AutoMLConfig의 enable_early_stopping 기본값이 True로 업데이트되었습니다.
  + **azureml-contrib-automl-pipeline-steps**
    + 파이프라인을 통한 예측 작업에 대해 HTS(계층적 시계열)를 사용합니다.
    + 유추에 대한 표 형식 데이터 세트 지원을 추가합니다.
    + 유추 데이터에 대해 사용자 지정 경로를 지정할 수 있습니다.
  + **azureml-contrib-reinforcementlearning**
    + 보충 학습 작업에서 Ray 작업자가 사용하는 `shm_size` 속성 등, `azureml.core.environment.DockerSection`의 일부 속성은 사용되지 않습니다. 대신 이 속성을 이제 `azureml.contrib.train.rl.WorkerConfiguration`에서 지정할 수 있습니다.
  + **azureml-core**
    + `ScriptRunConfig.distributed_job_config` 설명서의 하이퍼링크 수정
    + 이제 작업 영역 위치와 다른 곳에 Azure Machine Learning 컴퓨팅 클러스터를 만들 수 있습니다. 이 기능은 할당량 사용만을 위해 작업 영역을 더 만들거나 특정 위치에 컴퓨팅 클러스터를 만들지 않고도, 유휴 용량 할당을 극대화하고 서로 다른 위치에서 할당량 사용을 관리하는 데 유용합니다. 자세한 내용은 [Azure Machine Learning 컴퓨팅 클러스터 만들기](how-to-create-attach-compute-cluster.md?tabs=python)를 참조하세요.
    + display_name이 실행 개체의 변경 가능한 이름 필드로 추가되었습니다.
    + 이제 데이터 세트 from_files는 대량 입력 데이터에 대한 데이터 확장 건너뛰기를 지원합니다.
  + **azureml-dataprep**
    + 경합 상태 때문에 to_dask_dataframe이 실패하는 버그를 수정했습니다.
    + 이제 데이터 세트 from_files는 대량 입력 데이터에 대한 데이터 확장 건너뛰기를 지원합니다.
  + **azureml-defaults**
    + azureml-defaults에서 종속성 azureml-model-management-sdk==1.0.1b6.post1을 제거하는 과정에 있습니다.
  + **azureml-interpret**
    + azureml-interpret가 interpret-community 0.19로 업데이트되었습니다.*
  + **azureml-pipeline-core**
    + 파이프라인을 통한 예측 작업에 대해 HTS(계층적 시계열)를 사용합니다.
  + **azureml-train-automl-client**
    + 자동 ML에서 캐싱에 BLOB 저장소를 사용하도록 전환합니다.
    + 파이프라인을 통한 예측 작업에 대해 HTS(계층적 시계열)를 사용합니다.
    + XGBoost 모델 검색에 대한 오류 처리가 향상되었습니다.
    + AutoMLConfig의 enable_early_stopping 기본값이 True로 업데이트되었습니다.
  + **azureml-train-automl-runtime**
    + 자동 ML에서 캐싱에 BLOB 저장소를 사용하도록 전환합니다.
    + 파이프라인을 통한 예측 작업에 대해 HTS(계층적 시계열)를 사용합니다.
    + AutoMLConfig의 enable_early_stopping 기본값이 True로 업데이트되었습니다.


## <a name="2021-07-06"></a>2021-07-06

### <a name="azure-machine-learning-sdk-for-python-v1320"></a>Python용 Azure Machine Learning SDK v1.32.0
+ **버그 수정 및 개선 사항**
  + **azureml-core**
    + SDK/CLI에서 진단 작업 영역 상태 표시
  + **azureml-defaults**
    + azureml-defaults에 `opencensus-ext-azure==1.0.8` 종속성 추가
  + **azureml-pipeline-core**
    + 작업 제출을 위한 환경이 기본 환경과 일치하면 미리 작성된 이미지를 사용하도록 AutoMLStep이 업데이트되었습니다.
  + **azureml-responsibleai**
    + 오류 분석 보고서를 업로드, 다운로드 및 나열하기 위해 추가된 새 오류 분석 클라이언트입니다.
    + `raiwidgets` 및 `responsibleai` 패키지의 버전이 동기화 상태인지 확인합니다.
  + **azureml-train-automl-runtime**
    + 다양한 기능화 전략에서의 동적 검색을 위해 할당된 시간을 전체 실험 시간 제한의 최대 1/4로 설정합니다.


## <a name="2021-06-21"></a>2021-06-21

### <a name="azure-machine-learning-sdk-for-python-v1310"></a>Python용 Azure Machine Learning SDK v1.31.0
+ **버그 수정 및 개선 사항**
  + **azureml-core**
    + 환경 클래스의 플랫폼 속성에 대한 설명서가 향상되었습니다.
    + 기본 AML 컴퓨팅 노드 스케일 다운 시간이 120초에서 1800초로 변경되었습니다.
    + 실패한 실행의 문제 해결을 위한 포털에 표시되는 기본 문제 해결 링크가 https://aka.ms/azureml-run-troubleshooting 으로 업데이트되었습니다.
  + **azureml-automl-runtime**
    + 데이터 정리: [None, "", "nan", np. nan]에서 대상 값이 있는 샘플이 기능화 및/또는 모델 학습 이전에 삭제됩니다.
  + **azureml-interpret**
    + 제한 시간을 늘려 ExplanationClient를 사용하는 원격 AzureML 실행에서의 작업 큐 플러시 오류를 방지합니다.
  + **azureml-pipeline-core**
    + 시냅스 단계에 jar 매개 변수 추가
  + **azureml-train-automl-runtime**
    + 문서와의 일치 수준을 높이기 위해 높은 카디널리티 가드 레일 수정

## <a name="2021-06-07"></a>2021-06-07

### <a name="azure-machine-learning-sdk-for-python-v1300"></a>Python용 Azure Machine Learning SDK v1.30.0
+ **버그 수정 및 개선 사항**
  + **azureml-core**
    + 0\.17.5에서 호환성이 손상되는 변경이 릴리스됨에 따라 종속성 `ruamel-yaml`을 0.17.5 미만으로 고정합니다.
    + `aml_k8s_config` 속성이 `KubernetesCompute` 첨부에 대한 `namespace`, `default_instance_type` 및 `instance_types` 매개 변수로 대체됩니다.
    + 작업 영역 동기화 키가 장기 실행 작업으로 변경되었습니다.
  + **azureml-automl-runtime**
    + `Elements of y_test cannot be NaN`으로 빅 데이터 실행이 실패할 수 있는 문제가 수정되었습니다.
  + **azureml.mlflow**
    + 서명이 없는 모델에 대한 MLFlow 배포 플러그 인 버그 수정입니다.
  + **azureml-pipeline-steps**
    + ParallelRunConfig: process_count_per_node에 대한 문서를 업데이트합니다.
  + **azureml-train-automl-runtime**
    + MM 유추 중 정의된 분위수의 사용자 지정 지원
    + 일괄 처리 유추 중 forecast_quantiles를 지원합니다.
  + **azureml-contrib-automl-pipeline-steps**
    + MM 유추 중 정의된 분위수의 사용자 지정 지원
    + 일괄 처리 유추 중 forecast_quantiles를 지원합니다.

## <a name="2021-05-25"></a>2021-05-25

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Azure Machine Learning용 CLI(v2) 발표(미리 보기)

Azure CLI에 대한 `ml` 확장은 Azure Machine Learning을 위한 차세대 인터페이스입니다. 이를 통해 모델 수명 주기를 추적하는 동안 데이터 과학 확장을 가속화하는 기능을 사용하여 명령줄에서 모델을 학습하고 배포할 수 있습니다. [설치 및 시작](how-to-configure-cli.md)합니다.

### <a name="azure-machine-learning-sdk-for-python-v1290"></a>Python용 Azure Machine Learning SDK v1.29.0
+ **버그 수정 및 개선 사항**
  + **주요 변경 내용**
    + Python 3.5에 대한 지원이 중단되었습니다.
  + **azureml-automl-runtime**
    + 시계열 길이가 계절성보다 짧은 경우 STLFeaturizer가 실패하는 버그를 수정했습니다. 이 오류는 IndexError로 나타납니다. 이 경우 STL의 계절성 구성 요소는 0으로만 구성되지만 사례는 오류 없이 처리됩니다.
  + **azureml-contrib-automl-dnn-vision**
    + 파일 경로로 일괄 처리 유추를 위한 메서드를 추가했습니다.
  + **azureml-contrib-gbdt**
    + azureml-contrib-gbdt 패키지는 더 이상 사용되지 않으며, 향후 업데이트를 받지 못할 수 있으며, 배포에서 완전히 제거됩니다.
  + **azureml-core**
    + Datastore.register_azure_blob_container의 매개 변수 create_if_not_exists에 대한 설명이 수정되었습니다.
    + DatasetConsumptionConfig 클래스에 샘플 코드가 추가되었습니다.
    + run.log()의 스칼라 메트릭 값에 대한 대체 축으로 단계에 대한 지원이 추가되었습니다.
  + **azureml-dataprep**
    + `_with_partition_size()`에서 허용되는 파티션 크기를 2GB로 제한
  + **azureml-interpret**
    + azureml-interpret를 최신 interpret-core 패키지 버전으로 업데이트
    + SHAP 0.36.0에서 더 이상 사용되지 않는 SHAP DenseData에 대한 지원이 중단되었습니다.
    + `ExplanationClient`를 활성화하여 사용자 지정 데이터 저장소에 업로드합니다.
  + **azureml.mlflow**
    + azureml-mlflow를 mlflow-skinny로 이동하여 전체 플러그 인 지원을 유지하면서 종속성 공간을 줄입니다.
  + **azureml-pipeline-core**
    + PipelineParameter 코드 샘플은 올바른 매개 변수를 사용하도록 참조 문서에서 업데이트되었습니다.


## <a name="2021-05-10"></a>2021-05-10

### <a name="azure-machine-learning-sdk-for-python-v1280"></a>Python용 Azure Machine Learning SDK v1.28.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + 디자이너와 일치하도록 AutoML 점수 매기기 스크립트를 개선했습니다.
    + 이전 버전의 SDK에서 학습한 경우 Prophet 모델을 통해 예측할 때 "누락된 열" 오류를 throw하는 패치 버그입니다.
    + AutoML SDK의 공개 대상, 예측 지원 모델 목록에 ARIMAX 모델을 추가했습니다. 여기서 ARIMAX는 ARIMA 오류가 발생한 회귀이며, Box 및 Jenkins에서 개발한 전송 함수 모델의 특별한 경우입니다. 두 접근 방식이 어떻게 다른지에 대한 설명은 [ARIMAX 모델 머들](https://robjhyndman.com/hyndsight/arimax/)을 참조하세요. AutoML에서 자동 생성된 시간 종속 기능(하루 중 시간, 년 중 일 등)을 사용하는 나머지 다변량 모델과 달리 이 모델은 사용자가 제공하는 기능만 사용하며 계수를 쉽게 해석할 수 있습니다.
  + **azureml-contrib-dataset**
    + 탑재를 사용하는 동안 libfuse를 설치해야 함을 나타내는 문서 설명이 업데이트되었습니다.
  + **azureml-core**
    + 이제 기본 CPU 큐레이트 이미지는 mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04입니다. 이제 기본 GPU 이미지는 mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.2-cudnn8-ubuntu18.04입니다.
    + Run.fail()은 이제 사용되지 않습니다. 실행을 실패한 것으로 표시하려면 Run.tag()를 사용하고, 실행을 취소된 것으로 표시하려면 Run.cancel()을 사용합니다.
    + 파일 데이터 세트를 탑재할 때 libfuse를 설치해야 한다는 메모가 포함된 문서가 업데이트되었습니다.
    + 표 형식 데이터 세트에 실험적 register_dask_dataframe() 지원을 추가합니다.
    + Azure Blob/ADL-S를 입/출력으로 사용하여 DatabricksStep을 지원하고, i/o 액세스 구성을 클러스터에 추가해야 할 때 AML이 클러스터를 다시 시작할 수 있는지 여부를 고객이 결정할 수 있도록 permit_cluster_restart 매개 변수를 노출합니다.
  + **azureml-dataset-runtime**
    + azureml-dataset-runtime은 이제 pyarrow < 4.0.0 버전을 지원합니다.
  + **azureml.mlflow**
    + MLFlow 플러그 인을 통해 AzureML에 배포하기 위한 지원이 추가되었습니다.
  + **azureml-pipeline-steps**
    + Azure Blob/ADL-S를 입/출력으로 사용하여 DatabricksStep을 지원하고, i/o 액세스 구성을 클러스터에 추가해야 할 때 AML이 클러스터를 다시 시작할 수 있는지 여부를 고객이 결정할 수 있도록 permit_cluster_restart 매개 변수를 노출합니다.
  + **azureml-synapse**
    + msi 인증에서 대상 그룹 사용
  + **azureml-train-automl-client**
    + 컴퓨팅 대상 문서에 대한 변경된 링크가 추가되었습니다.


## <a name="2021-04-19"></a>2021-04-19

### <a name="azure-machine-learning-sdk-for-python-v1270"></a>Python용 Azure Machine Learning SDK v1.27.0
+ **버그 수정 및 개선 사항**
  + **azureml-core**
    + "AZUREML_ARTIFACTS_DEFAULT_TIMEOUT" 환경 변수를 통해 아티팩트를 업로드하기 위해 기본 시간 제한 값을 재정의하는 기능이 추가되었습니다.
    + ScriptRunConfig의 Environment 개체에서 docker 설정이 적용되지 않는 버그를 수정했습니다.
    + 대상에 복사할 때 데이터 세트를 분할할 수 있습니다.
    + 링크 함수를 통해 파이프라인에서 생성된 데이터 세트를 전달할 수 있도록 OutputDatasetConfig에 사용자 지정 모드가 추가되었습니다. 이러한 지원 개선 사항은 PRS에 테이블 형식 분할을 사용하도록 설정하기 위한 것입니다.
    + azureml-core에 새 KubernetesCompute 컴퓨팅 유형이 추가되었습니다.
  + **azureml-pipeline-core**
    + OutputDatasetConfig에 사용자 지정 모드를 추가하고 사용자가 링크 함수를 통해 파이프라인에서 생성된 데이터 세트를 전달할 수 있도록 합니다. 파일 경로 대상에 자리 표시자가 지원됩니다. 이러한 지원 개선 사항은 PRS에 테이블 형식 분할을 사용하도록 설정하기 위한 것입니다.
    + azureml-core에 새 KubernetesCompute 컴퓨팅 유형이 추가되었습니다.
  + **azureml-pipeline-steps**
    + azureml-core에 새 KubernetesCompute 컴퓨팅 유형이 추가되었습니다.
  + **azureml-synapse**
    + azureml synapse의 위젯에서 spark UI URL 업데이트
  + **azureml-train-automl-client**
    + 이제 예측 작업에 대한 STL 기능화 도구가 시계열의 빈도에 따라 보다 강력한 계절성 검색을 사용합니다.
  + **azureml-train-core**
    + Environment 개체의 Docker 설정이 적용되지 않는 버그가 수정되었습니다.
    + azureml-core에 새 KubernetesCompute 컴퓨팅 유형이 추가되었습니다.


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Python용 Azure Machine Learning SDK v1.26.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoMLStep 실행에서 Naive 모델이 권장되고 구간 또는 이동 기간 기능과 함께 실패하는 문제를 해결했습니다. 대상 구간 또는 대상 이동 기간 크기가 설정된 경우에는 이러한 모델이 권장되지 않습니다.
    +  AutoML 실행을 제출할 때 콘솔 출력이 실행에 대한 포털 링크를 표시하도록 변경되었습니다.
  + **azureml-core**
    + 설명서에 HDFS 모드가 추가되었습니다.
    + glob 구조에 따라 파일 데이터 세트 파티션을 이해하도록 지원하는 기능이 추가되었습니다.
    + AzureML 작업 영역과 연결된 컨테이너 레지스트리 업데이트에 대한 지원이 추가되었습니다.
    + DockerSection에서 사용되지 않는 환경 특성 "enabled", "shared_volume" 및 "arguments"는 이제 RunConfiguration에서 DockerConfiguration의 일부입니다.
    + 파이프라인 CLI 복제 설명서 업데이트
    + 인증용 테넌트를 포함하도록 포털 URI 업데이트
    + 리디렉션을 방지하기 위해 실행 URI에서 실험 이름 제거 
    + 실험 ID를 사용하도록 실험 URO가 업데이트되었습니다.
    + AzureML CLI를 사용하여 원격 컴퓨팅을 연결하기 위한 버그 수정.
    + 인증용 테넌트를 포함하도록 포털 URI가 업데이트되었습니다.
    + 실험 ID를 사용하도록 실험 URI가 업데이트되었습니다.
  + **azureml-interpret**
    + interpret-community 0.17.0을 사용하도록 azureml-interpret 업데이트
  + **azureml-opendatasets**
    + 날짜/시간 형식이 아닌 경우 입력 시작 날짜와 종료 날짜 형식 유효성 검사 및 오류 표시.
  + **azureml-parallel-run**
    + [실험적 기능] ParallelRunConfig에 `partition_keys` 매개 변수를 추가합니다. 지정된 경우 입력 데이터 세트가 지정된 키를 기준으로 미니 일괄 처리로 분할됩니다. 모든 입력 데이터 세트가 분할된 데이터 세트여야 합니다.
  + **azureml-pipeline-steps**
    + 버그 수정 - 데이터 세트 구성을 다운로드로 전달하는 동안 path_on_compute 지원.
    + 파이프라인에서 R 스크립트를 실행할 때 CommandStep을 사용하기 위해 RScriptStep 사용 중단. 
    + 파이프라인에서 ML 학습(분산 학습 포함)을 실행하는 데 CommandStep을 사용하기 위해 EstimatorStep 사용 중단.
  + **azureml-sdk**
    + azureml-sdk에서 python_requires를 3.9 미만으로 업데이트
  + **azureml-train-automl-client**
    +  AutoML 실행을 제출할 때 콘솔 출력이 실행에 대한 포털 링크를 표시하도록 변경되었습니다.
  + **azureml-train-core**
    + ScriptRunConfig와 함께 DockerConfiguration을 사용하기 위해 DockerSection의 'enabled', 'shared_volume' 및 'arguments' 특성이 사용되지 않습니다.
    +  MNIST 데이터 세트에 Azure Open 데이터 세트 사용
    + Hyperdrive 오류 메시지가 업데이트되었습니다.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Python용 Azure Machine Learning SDK v1.25.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    +  AutoML 실행을 제출할 때 콘솔 출력이 실행에 대한 포털 링크를 표시하도록 변경되었습니다.
  + **azureml-core**
    + SDK 및 CLI에서 작업 영역에 대한 컨테이너 레지스트리 업데이트 지원 시작
    + ScriptRunConfig와 함께 DockerConfiguration을 사용하기 위해 DockerSection의 'enabled', 'shared_volume' 및 'arguments' 특성이 사용되지 않습니다.
    + 파이프라인 CLI 복제 설명서 업데이트
    + 인증용 테넌트를 포함하도록 포털 URI 업데이트
    + 리디렉션을 방지하기 위해 실행 URI에서 실험 이름 제거
    + 실험 ID를 사용하도록 실험 URO가 업데이트되었습니다.
    + az cli를 사용하여 원격 컴퓨팅을 연결하기 위한 버그 수정
    + 인증용 테넌트를 포함하도록 포털 URI가 업데이트되었습니다.
    + glob 구조에 따라 파일 데이터 세트 파티션을 이해하도록 지원하는 기능이 추가되었습니다.
  + **azureml-interpret**
    + interpret-community 0.17.0을 사용하도록 azureml-interpret 업데이트
  + **azureml-opendatasets**
    + 날짜/시간 형식이 아닌 경우 입력 시작 날짜와 종료 날짜 형식 유효성 검사 및 오류 표시.
  + **azureml-pipeline-core**
    + 버그 수정 - 데이터 세트 구성을 다운로드로 전달하는 동안 path_on_compute 지원.
  + **azureml-pipeline-steps**
    + 버그 수정 - 데이터 세트 구성을 다운로드로 전달하는 동안 path_on_compute 지원.
    + 파이프라인에서 R 스크립트를 실행할 때 CommandStep을 사용하기 위해 RScriptStep 사용 중단. 
    + 파이프라인에서 ML 학습(분산 학습 포함)을 실행하는 데 CommandStep을 사용하기 위해 EstimatorStep 사용 중단.
  + **azureml-train-automl-runtime**
    +  AutoML 실행을 제출할 때 콘솔 출력이 실행에 대한 포털 링크를 표시하도록 변경되었습니다.
  + **azureml-train-core**
    + ScriptRunConfig와 함께 DockerConfiguration을 사용하기 위해 DockerSection의 'enabled', 'shared_volume' 및 'arguments' 특성이 사용되지 않습니다.
    + MNIST 데이터 세트에 Azure Open 데이터 세트 사용
    + Hyperdrive 오류 메시지가 업데이트되었습니다.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(3월 업데이트)
+ **새로운 기능**
  + CSV/TSV를 렌더링합니다. 사용자는 데이터를 더 쉽게 분석하기 위해 TSV/CSV 파일을 그리드 형식으로 렌더링할 수 있습니다. 
  + 컴퓨팅 인스턴스의 SSO 인증. 이제 사용자는 Notebook UI에서 바로 새 컴퓨팅 인스턴스를 쉽게 인증하여 AzureML에서 바로 Azure SDK를 쉽게 인증하고 사용할 수 있습니다. 
  + 컴퓨팅 인스턴스 메트릭. 사용자는 터미널을 통해 CPU 사용량 및 메모리와 같은 컴퓨팅 메트릭을 볼 수 있습니다.
  + 파일 세부 정보. 이제 사용자는 파일 옆에 있는 점 3개를 클릭하여 마지막으로 수정한 시간과 파일 크기를 비롯한 파일 정보를 확인할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + 페이지 로드 시간 향상.
  + 성능 향상.
  + 속도 및 커널 안정성 향상.
  + Notebook 파일 창을 영구적으로 이동하여 세로 공간 확보
  + 이제 터미널에서 링크를 클릭할 수 있습니다.
  + Intellisense 성능이 개선되었습니다.


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Python용 Azure Machine Learning SDK v1.24.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + `azureml.automl.core.shared`에서 이전 버전과 호환되는 가져오기를 제거했습니다. `azureml.automl.runtime.shared`에서 가져오는 방법으로 `azureml.automl.core.shared` 네임스페이스의 모듈을 찾을 수 없음 오류를 해결할 수 있습니다.
  + **azureml-contrib-automl-dnn-vision**
    + 개체 검색 yolo 모델이 노출되었습니다.
  + **azureml-contrib-dataset**
    + 열 값으로 데이터 세트를 필터링하고, 메타데이터로 파일 데이터 세트를 필터링하는 기능이 추가되었습니다.
  + **azureml-contrib-fairness**
    + `azureml-contrib-fairness`의 휠에 JSON 스키마 포함
  + **azureml-contrib-mir**
    + 모델을 배포할 때 show_output을 True로 설정하면 요청을 서버로 보내기 전에 유추 구성 및 배포 구성이 재생됩니다.
  + **azureml-core**
    + 열 값으로 데이터 세트를 필터링하고, 메타데이터로 파일 데이터 세트를 필터링하는 기능이 추가되었습니다.
    + 이전에는 사용자가 `admin_user_password` 필드에 대한 암호 강도 요구 사항을 충족하지 않는 ComputeTarget에 대한 프로비저닝 구성을 만들 수 있었습니다(즉, ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` 집합에서 소문자 1개, 대문자 1개, 숫자 1개, 특수 문자 1개 중 3개 이상을 포함해야 함). 사용자가 약한 암호로 구성을 만들고 해당 구성을 사용하여 작업을 실행하면 런타임에 작업이 실패합니다. 이제 `AmlCompute.provisioning_configuration`을 호출하면 암호 강도 요구 사항을 설명하는 오류 메시지와 함께 `ComputeTargetException`이 throw됩니다. 
    + 또한 경우에 따라 최대 노드 수가 음수인 구성을 지정할 수도 있었습니다. 이 작업은 더 이상 수행할 수 없습니다. 이제 `AmlCompute.provisioning_configuration`은 `max_nodes` 인수가 음의 정수이면 `ComputeTargetException`을 throw합니다.
    + 모델을 배포할 때 show_output을 True로 설정하면 유추 구성 및 배포 구성이 표시됩니다.
    + 모델 배포가 완료될 때까지 기다릴 때 show_output을 True로 설정하면 배포 작업의 진행률이 표시됩니다.
    + 환경 변수 AZUREML_AUTH_CONFIG_DIR을 통해 고객이 지정한 AzureML 인증 구성 디렉터리 허용
    + 이전에는 최소 노드 수가 최대 노드 수보다 적은 프로비저닝 구성을 만들 수 있었습니다. 작업이 실행되지만 런타임에 실패합니다. 이 버그는 현재 수정되었습니다. 이제 `min_nodes < max_nodes`를 사용하여 프로비저닝 구성을 만들려고 하면 SDK가 `ComputeTargetException`을 표시합니다.
  + **azureml-interpret**
    + 스파스 엔지니어링 설명에 대한 집계 기능 중요도를 표시하지 않는 설명 대시보드 수정
    + azureml-interpret 패키지에서 ExplanationClient의 메모리 사용량 최적화
  + **azureml-train-automl-client**
    +  spark를 사용하여 실행할 때 사용자에게 컨트롤을 반환하도록 show_output=False를 수정했습니다.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(2월 업데이트)
+ **새로운 기능**
  + [기본 터미널(GA)](./how-to-access-terminal.md). 이제 사용자는 통합 터미널에 액세스할 수 있으며 통합 터미널을 통해 Git 작업에도 액세스할 수 있습니다.
  + Notebook 코드 조각(미리 보기). 이제는 일반적인 Azure ML 코드 발췌 내용을 편리하게 이용할 수 있습니다. 도구 모음을 통해 액세스할 수 있는 코드 조각 패널로 이동하거나 Ctrl + Space를 사용하여 코드 내 조각 메뉴를 활성화합니다.  
  + [바로 가기 키](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts). Jupyter에서 바로 가기 키를 사용할 수 있는 전체 패리티입니다. 
  + 셀 매개 변수를 표시합니다. Notebook의 셀이 매개 변수 셀이며 컴퓨팅 인스턴스의 [Papermill](https://github.com/nteract/papermill)을 통해 매개 변수화된 Notebook을 실행할 수 있는 사용자를 보여 줍니다.
  + 터미널 및 커널 세션 관리자: 사용자가 컴퓨팅에서 실행되는 모든 커널 및 터미널 세션을 관리할 수 있습니다.
  + 공유 단추. 이제 사용자는 Notebook 파일 탐색기에서 파일을 마우스 오른쪽 단추로 클릭하고 공유 단추를 사용하여 모든 파일을 공유할 수 있습니다.


+ **버그 수정 및 개선 사항**
  + 페이지 로드 시간 향상
  + 성능 향상 
  + 속도 및 커널 안정성 향상
  + 진행 중인 모든 [컴퓨팅 인스턴스 작업](./how-to-run-jupyter-notebooks.md#status-indicators)에 대한 진행률을 표시하는 회전 휠이 추가되었습니다.
  + 파일 탐색기에서 마우스 오른쪽 단추를 클릭합니다. 이제 파일을 마우스 오른쪽 단추로 클릭하면 파일 작업이 열립니다. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Python용 Azure Machine Learning SDK v1.23.0
+ **버그 수정 및 개선 사항**
  + **azureml-core**
    + [실험적 기능] Synapse 작업 영역을 연결된 서비스로 AML에 연결하는 지원 추가
    + [실험적 기능] Synapse spark 풀을 AML에 컴퓨팅으로 연결하는 지원 추가
    + [실험적 기능] ID 기반 데이터 액세스에 대한 지원 추가. 사용자는 자격 증명을 제공하지 않고 데이터 저장소 또는 데이터 세트를 등록할 수 있습니다. 이 경우 사용자의 AAD 토큰 또는 컴퓨팅 대상의 관리 ID가 인증에 사용됩니다. [여기](./how-to-identity-based-data-access.md)를 참조하세요.
  + **azureml-pipeline-steps**
    + [실험적 기능] [SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)에 대한 지원 추가
  + **azureml-synapse**
    + [실험적 기능] Synapse Spark 풀에서 대화형 세션을 실행하는 spark 매직에 대한 지원 추가.
+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + 이 업데이트에서는 AutoML SDK의 예측 도구 상자에 홀트 윈터스 지수 평활법을 추가했습니다. 시계열이 제공되면 [AICc(Corrected Akaike's Information Criterion)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors)에서 최적의 모델을 선택하고 반환합니다.
    + 이제 AutoML은 한 개가 아닌 두 개의 로그 파일을 생성합니다. 로그 문은 로그 문이 생성된 프로세스에 따라 어느 한쪽으로 이동합니다.
    + 교차 유효성 검사를 통해 모델 학습 중에 불필요한 샘플 내 예측을 제거합니다. 그러면 경우에 따라(특히 시계열 예측 모델) 모델 학습 시간이 줄어듭니다.
  + **azureml-contrib-fairness**
    + dashboardDictionary 업로드에 대한 JSON 스키마를 추가합니다.
  + **azureml-contrib-interpret**
    + azureml-contrib-interpret README는 해당 패키지가 10월부터 사용이 중단되고 다음 업데이트에서 제거될 예정임을 반영하도록 업데이트되었습니다. azureml-interpret 패키지를 대신 사용하세요.
  + **azureml-core**
    + 이전에는 최소 노드 수가 최대 노드 수보다 적은 프로비저닝 구성을 만들 수 있었습니다. 이제 이 기능이 수정되었습니다. 이제 `min_nodes < max_nodes`를 사용하여 프로비저닝 구성을 만들려고 하면 SDK가 `ComputeTargetException`을 표시합니다.
    +  작업이 실제로 완료되기 전에 함수가 제어 흐름을 반환하도록 하는 AmlCompute의 wait_for_completion 버그 수정
    + Run.fail()은 이제 사용되지 않습니다. 실행을 실패한 것으로 표시하려면 Run.tag()를 사용하고, 실행을 취소된 것으로 표시하려면 Run.cancel()을 사용합니다.
    + 제공된 환경 이름이 문자열이 아닌 경우 '환경 이름에 문자열이 필요한데 {}이(가) 발견됨'이라는 오류 메시지가 표시됩니다.
  + **azureml-train-automl-client**
    + Azure Databricks 클러스터에서 수행된 AutoML 실험이 취소되지 않도록 하는 버그가 수정되었습니다.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Python용 Azure Machine Learning SDK v1.22.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 비전 모델에 대한 conda yml 파일에 추가 pip 종속성이 추가되는 버그가 수정되었습니다.
  + **azureml-automl-runtime**
    + 기존 예측 모델(예: AutoArima)에서 대체된 대상 값을 가진 행이 없는 학습 데이터를 받을 수 있는 버그를 수정했습니다. 이는 이러한 모델의 데이터 계약에 위배되었습니다. * 시계열 지연 연산자의 lag-by-occurrence 동작과 관련된 다양한 버그를 수정했습니다. 이전에는 lag-by-occurrence 연산이 모든 대체 행을 올바르게 표시하지 않았으므로 항상 올바른 발생 구간 값을 생성하지는 않았습니다. 또한 lag-by-occurrence 동작에서 구간 연산자와 이동 기간 연산자 간의 일부 호환성 문제가 해결되었습니다. 이로 인해 이전에는 이동 기간 연산자가 학습 데이터에서 일부 행을 삭제했으며, 그렇지 않을 경우 사용해야 했습니다.
  + **azureml-core**
    + 대상 그룹의 토큰 인증에 대한 지원 추가.
    + 다중 프로세스 다중 노드 PyTorch 작업을 지원하려면 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)에 `process_count`를 추가합니다.
  + **azureml-pipeline-steps**
    + 이제 [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep)이 일반 공급(GA)되며 더 이상 실험적이지 않습니다.
    + [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig): 최소 일괄 처리 수준에서 오류 임계값을 확인하는 allowed_failed_count 및 allowed_failed_percent 인수 추가 오류 임계값에는 이제 다음과 같은 3가지 종류가 있습니다.
       + error_threshold - 허용되는 최소 일괄 처리 실패 항목 수 
       + allowed_failed_count - 허용되는 최소 일괄 처리 실패 횟수 
       + allowed_failed_percent- 허용되는 최소 일괄 처리 실패 비율 
       
       이 중 하나를 초과하면 작업이 중지됩니다. error_threshold는 이전 버전과의 호환성을 유지하는 데 필요합니다. 이를 무시하려면 값을 -1로 설정합니다.
    + AutoMLStep 이름에서 공백 처리를 수정했습니다.
    + 이제 ScriptRunConfig가 HyperDriveStep에서 지원됩니다.
  + **azureml-train-core**
    + 이제 ScriptRun에서 호출된 HyperDrive 실행이 자식 실행으로 간주됩니다.
    + 다중 프로세스 다중 노드 PyTorch 작업을 지원하려면 [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration)에 `process_count`를 추가합니다.
  + **azureml-widgets**
    + ParallelRunStepDetails 위젯을 추가하여 ParallelRunStep의 상태를 시각화합니다.
    + 하이퍼 드라이브 사용자는 각 자식 실행에 대한 하이퍼 매개변수의 각 집합에 해당하는 메트릭 값을 보여 주는 병렬 좌표 차트에서 추가 축을 볼 수 있습니다.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(1월 업데이트)
+ **새로운 기능**
  + AzureML의 네이티브 Markdown 편집기. 이제 사용자는 AzureML Studio에서 기본적으로 markdown 파일을 렌더링하고 편집할 수 있습니다.
  + [스크립트(.py, .R 및 .sh)의 실행 단추](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script). 이제 사용자는 AzureML에서 Python, R 및 Bash 스크립트를 쉽게 실행할 수 있음
  + [변수 탐색기](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook). 팝업 패널에서 변수 및 데이터 프레임의 내용을 살펴봅니다. 사용자는 데이터 형식, 크기 및 내용을 쉽게 확인할 수 있습니다.
  + [목차](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc). Markdown 헤더가 나타내는 Notebook의 섹션으로 이동합니다.
  + Notebook을 Latex/HTML/Py로 내보냅니다. LaTex, HTML 또는 .py로 내보내어 공유하기 쉬운 Notebook 파일을 만듭니다.
  + Intellicode. ML 기반 결과는 향상된 [지능형 자동 완성 환경](/visualstudio/intellicode/overview)을 제공합니다.

+ **버그 수정 및 개선 사항**
  + 페이지 로드 시간 향상
  + 성능 향상 
  + 속도 및 커널 안정성 향상
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Python용 Azure Machine Learning SDK v1.21.0
+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + AmlCompute를 UserAssigned ID와 함께 사용할 때 CLI 도움말 텍스트가 수정됨
  + **azureml-contrib-automl-dnn-vision**
    + AutoML 비전 실행에 대해 배포 및 다운로드 단추가 표시되며 다른 AutoML 실행과 유사하게 모델을 배포하거나 다운로드할 수 있습니다. 새로운 두 파일(scoring_file_v_1_0_0.py 및 conda_env_v_1_0_0.yml)이 있으며, 이는 유추를 실행할 스크립트가 포함된 파일과 conda 환경을 다시 만드는 yml 파일입니다. 'model.pth' 파일도 '.pt' 확장명을 사용하도록 이름이 변경되었습니다.
  + **azureml-core**
    + azure-cli-ml에 대한 MSI 지원
    + 사용자 할당 관리 ID 지원.
    + 이러한 변경을 통해 고객은 고객 키 자격 증명 모음에서 미사용 암호화를 위한 키를 페치하는 데 사용할 수 있는 사용자 할당 ID를 제공할 수 있습니다.
    +  매우 큰 파일의 프로필에서 row_count=0 수정 - 공백 패딩을 사용하여 구분된 값에 대한 이중 변환 오류 해결
    + 출력 데이터 세트의 실험적 플래그 제거 GA
    + 특정 버전의 모델을 페치하는 방법에 대한 설명서 업데이트
    + 프라이빗 링크의 경우 혼합 모드 액세스를 위한 작업 영역 업데이트 허용
    + 실행 다시 시작 기능을 위해 데이터 저장소에 대한 추가 등록을 제거하도록 수정
    + 작업 영역의 기본 사용자 할당 ID를 업데이트하기 위한 CLI/SDK 지원이 추가됨
  + **azureml-interpret**
    + azureml-interpret가 interpret-community 0.16.0으로 업데이트됨
    + azureml-interpret의 설명 클라이언트에 대한 메모리 최적화
  + **azureml-train-automl-runtime**
    + ADB 실행에 대한 스트리밍 지원
  + **azureml-train-core**
    + 실행 다시 시작 기능을 위해 데이터 저장소에 대한 추가 등록을 제거하도록 수정
  + **azureml-widgets**
    + 고객은 위젯을 사용하여 기존 실행 데이터 시각화에 대한 변경 내용을 볼 수 없으며, 이제 필요에 따라 조건부 하이퍼 매개 변수를 사용하는 경우가 지원됩니다.
    + 이제 사용자 실행 위젯에 실행이 대기 상태인 이유에 대한 자세한 설명이 포함됩니다.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Python용 Azure Machine Learning SDK v1.20.0
+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + OptimizationConfig에 framework_version이 추가되었습니다. 이는 모델이 프레임워크 MULTI에 등록된 경우에 사용됩니다.
  + **azureml-contrib-optimization**
    + OptimizationConfig에 framework_version이 추가되었습니다. 이는 모델이 프레임워크 MULTI에 등록된 경우에 사용됩니다.
  + **azureml-pipeline-steps**
    + 처리할 명령을 받는 CommandStep 도입 명령에는 실행 파일, 셸 명령, 스크립트 등이 포함될 수 있습니다.
  + **azureml-core**
    + 이제 작업 영역 만들기에 사용자 할당 ID가 지원됩니다. SDK/CLI의 uai 지원 추가
    + 로컬 배포에서 score.py에 대한 변경 내용을 선택하는 service.reload()의 문제가 해결되었습니다.
    + `run.get_details()`에 이 실행의 작성자 이름을 표시하는 "submittedBy"라는 추가 필드가 있습니다.
    + 실행에서 모델을 직접 등록하는 방법을 설명하도록 Model.register 메서드 설명서가 편집됨
    + IOT-Server 연결 상태 변경 처리 문제가 해결되었습니다.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(12월 업데이트)
+ **새로운 기능**
  + 사용자 파일 이름 검색. 이제 사용자는 작업 영역에 저장된 모든 파일을 검색할 수 있습니다.
  + Notebook 셀당 Markdown Side-by-Side 지원. Notebook 셀에서 사용자는 이제 렌더링된 Markdown 및 Markdown 구문을 Side-by-Side로 볼 수 있습니다.
  + 셀 상태 표시줄. 상태 표시줄은 코드 셀의 상태, 셀 실행 성공 여부 및 실행에 걸린 시간을 나타냅니다. 
   
+ **버그 수정 및 개선 사항**
  + 페이지 로드 시간 향상
  + 성능 향상 
  + 속도 및 커널 안정성 향상

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Python용 Azure Machine Learning SDK v1.19.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 테스트 데이터에 대한 실험적 지원이 AutoMLStep에 추가되었습니다.
    + 테스트 집합 수집 기능의 초기 핵심 구현이 추가되었습니다.
    + joblib에 직접 종속되도록 sklearn.externals.joblib에 대한 참조가 이동했습니다.
    + "image-instance-segmentation"이라는 새로운 AutoML 작업 유형 도입.
  + **azureml-automl-runtime**
    + 테스트 집합 수집 기능의 초기 핵심 구현이 추가되었습니다.
    + 텍스트 열의 모든 문자열 길이가 정확히 1자인 경우, 해당 토크나이저가 2자 미만의 문자열을 무시하므로 TfIdf word-gram 기능화 도구가 작동하지 않습니다. 현재 코드 변경 사항으로 인해 AutoML이 이 사용 사례를 처리할 수 있습니다.
    + "image-instance-segmentation"이라는 새로운 AutoML 작업 유형 도입.
  + **azureml-contrib-automl-dnn-nlp**
    + 새 dnn-nlp 패키지에 대한 초기 PR
  + **azureml-contrib-automl-dnn-vision**
    + "image-instance-segmentation"이라는 새로운 AutoML 작업 유형 도입.
  + **azureml-contrib-automl-pipeline-steps**
    + 이 새 패키지는 여러 모델 학습/유추 시나리오에 필요한 단계를 만드는 작업을 담당합니다. - 또한 큐레이팅된 환경 릴리스를 통해 향후 수정 사항이 자동으로 제공되도록 학습/유추 코드를 azureml.train.automl.runtime 패키지로 이동합니다.
  + **azureml-contrib-dataset**
    + "image-instance-segmentation"이라는 새로운 AutoML 작업 유형 도입.
  + **azureml-core**
    + 테스트 집합 수집 기능의 초기 핵심 구현이 추가되었습니다.
    + azureml-core 패키지의 설명서에 대한 xref 경고 수정
    + SDK의 명령 지원 기능에 대한 문서 문자열 수정
    + RunConfiguration에 명령 속성 추가. 이 기능을 사용하면 사용자가 AzureML SDK를 통해 컴퓨팅에서 실제 명령 또는 실행 파일을 실행할 수 있습니다.
    + 사용자는 해당 실험의 ID가 지정된 빈 실험을 삭제할 수 있습니다.
  + **azureml-dataprep**
    + Scala 2.12로 빌드된 Spark에 대한 데이터 세트 지원이 추가되었습니다. 이는 기존 2.11 지원에 추가됩니다.
  + **azureml.mlflow**
    + AzureML-MLflow는 원격 스크립트에 안전 장치를 추가하여 제출된 실행의 조기 종료를 방지합니다.
  + **azureml-pipeline-core**
    + UI를 통해 만든 파이프라인 엔드포인트에 대한 기본 파이프라인을 설정하는 버그가 수정됨
  + **azureml-pipeline-steps**
    + 테스트 데이터에 대한 실험적 지원이 AutoMLStep에 추가되었습니다.
  + **azureml-tensorboard**
    + azureml-core 패키지의 설명서에 대한 xref 경고 수정
  + **azureml-train-automl-client**
    + 테스트 데이터에 대한 실험적 지원이 AutoMLStep에 추가되었습니다.
    + 테스트 집합 수집 기능의 초기 핵심 구현이 추가되었습니다.
    + "image-instance-segmentation"이라는 새로운 AutoML 작업 유형 도입.
  + **azureml-train-automl-runtime**
    + 테스트 집합 수집 기능의 초기 핵심 구현이 추가되었습니다.
    + AutoML 모델이 validation_size 설정을 사용하여 학습된 경우 최적의 AutoML 모델에 대한 원시 설명의 계산을 수정합니다.
    + joblib에 직접 종속되도록 sklearn.externals.joblib에 대한 참조가 이동했습니다.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric()이 이제 더 빠르게 완료됨
    + HyperDrive SDK의 오류 처리가 향상되었습니다.
    +  ScriptRunConfig를 사용하여 실험 실행을 구성하기 위해 모든 예측 도구 클래스가 더 이상 사용되지 않습니다. 사용되지 않는 클래스는 다음과 같습니다.
        + MMLBase
        + 평가자
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + ScriptRunConfig와 PyTorchConfiguration을 사용하기 위해 Estimator 클래스에 대한 유효한 입력 형식으로 Nccl 및 Gloo를 사용하는 기능은 더 이상 지원되지 않습니다.
    + ScriptRunConfig와 MpiConfiguration을 사용하기 위해 Estimator 클래스에 대한 유효한 입력 형식으로 Mpi를 사용하는 기능은 더 이상 지원되지 않습니다.
    + runconfiguration에 명령 속성 추가. 이 기능을 사용하면 사용자가 AzureML SDK를 통해 컴퓨팅에서 실제 명령 또는 실행 파일을 실행할 수 있습니다.

    +  ScriptRunConfig를 사용하여 실험 실행을 구성하기 위해 모든 예측 도구 클래스가 더 이상 사용되지 않습니다. 사용되지 않는 클래스는 MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn 등입니다.
    + ScriptRunConfig와 PyTorchConfiguration을 사용하기 위해 Estimator 클래스에 대한 유효한 입력 형식으로 Nccl 및 Gloo를 사용하는 기능은 더 이상 지원되지 않습니다. 
    + ScriptRunConfig와 MpiConfiguration을 사용하기 위해 Estimator 클래스에 대한 유효한 입력 형식으로 Mpi를 사용하는 기능은 더 이상 지원되지 않습니다.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(11월 업데이트)
+ **새로운 기능**
   + 기본 터미널. 이제 사용자는 통합 터미널에 액세스할 수 있으며 [통합 터미널](./how-to-access-terminal.md)을 통해 Git 작업에도 액세스할 수 있습니다.
  + 중복 폴더 
  + 컴퓨팅 비용 드롭다운 
  + 오프라인 컴퓨팅 Pylance 

+ **버그 수정 및 개선 사항**
  + 페이지 로드 시간 향상
  + 성능 향상 
  + 속도 및 커널 안정성 향상
  + 대량 파일 업로드. 이제 95mb를 초과하는 파일을 업로드할 수 있습니다.

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Python용 Azure Machine Learning SDK v1.18.0
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    +  가우스 노이즈를 사용한 패딩을 허용하여 짧은 시계열의 처리를 개선했습니다.
  + **azureml-automl-runtime**
    + DateTime 열에 OutOfBoundsDatetime 값이 있는 경우 ConfigException throw
    + 가우스 노이즈를 사용한 패딩을 허용하여 짧은 시계열의 처리를 개선했습니다.
    + 각 텍스트 열이 해당 텍스트 열의 문자열 길이에 따라 n-gram 범위에서 char-gram 변환을 활용할 수 있도록 함
    + 사용자의 로컬 컴퓨팅에서 실행되는 AutoML 실험에 가장 적합한 모드에 대한 원시 기능 설명 제공
  + **azureml-core**
    + 향후 릴리스에서 주요 버전을 끌어올 수 없도록 pyjwt 패키지가 고정되었습니다.
    + 실험을 만들면 해당 실험이 존재하거나 새 실험이 있는 경우 동일한 이름을 가진 활성 또는 마지막으로 보관된 실험이 반환됩니다.
    + 이름으로 get_experiment를 호출하면 지정된 이름을 가진 활성 또는 마지막으로 보관된 실험이 반환됩니다.
    + 사용자는 다시 활성화하는 동안 실험의 이름을 바꿀 수 없습니다.
    + 데이터 세트가 실험에 잘못 전달될 때 잠재적 수정 사항을 포함하도록 오류 메시지가 개선되었습니다(예: ScriptRunConfig). 
    + 이름이 이미 있을 때 수행되는 동작을 포함하도록 `OutputDatasetConfig.register_on_complete`에 대한 설명서가 개선되었습니다.
    + 공통 환경 변수와 충돌할 가능성이 있는 데이터 세트 입력 및 출력 이름을 지정하면 경고가 발생함
    + 데이터 저장소를 등록할 때 `grant_workspace_access` 매개 변수의 용도가 변경되었습니다. Machine Learning 스튜디오에서 가상 네트워크 뒤의 데이터에 액세스하려면 `True`로 설정합니다.
      [자세한 정보](./how-to-enable-studio-virtual-network.md)
    + 연결된 서비스 API가 구체화되었습니다. 리소스 ID를 제공하는 대신 3개의 개별 매개 변수(sub_id, rg 및 name)를 구성에 정의합니다.
    + 고객이 토큰 손상 문제를 자체적으로 해결할 수 있도록 작업 영역 토큰 동기화를 공용 메서드로 지원합니다.
    + 이러한 변경 사항으로 인해 빈 문자열을 script_param의 값으로 사용할 수 있습니다.
  + **azureml-train-automl-client**
    +  가우스 노이즈를 사용한 패딩을 허용하여 짧은 시계열의 처리를 개선했습니다.
  + **azureml-train-automl-runtime**
    + DateTime 열에 OutOfBoundsDatetime 값이 있는 경우 ConfigException throw
    + 사용자의 로컬 컴퓨팅에서 실행되는 AutoML 실험에 가장 적합한 모드에 대한 원시 기능 설명을 제공하는 지원이 추가됨
    + 가우스 노이즈를 사용한 패딩을 허용하여 짧은 시계열의 처리를 개선했습니다.
  + **azureml-train-core**
    + 이러한 변경 사항으로 인해 빈 문자열을 script_param의 값으로 사용할 수 있습니다.
  + **azureml-train-restclients-hyperdrive**
    + README가 추가 컨텍스트를 제공하도록 변경됨
  + **azureml-widgets**
    + 위젯의 차트/병렬-좌표 라이브러리에 문자열 지원 추가.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>이미지 인스턴스 조각화를 위한 데이터 레이블 지정(다각형 주석) (미리 보기)

이제 데이터 레이블에서 이미지 인스턴스 조각화(다각형 주석) 프로젝트 형식을 사용할 수 있으므로 이미지에 있는 개체의 윤곽선 주변에 다각형을 그리거나 주석을 달 수 있습니다. 사용자는 이미지 내에서 관심이 있는 각 개체에 클래스 및 다각형을 할당할 수 있습니다.

[이미지 인스턴스 조각화 레이블 지정](how-to-label-data.md)에 대해 자세히 알아보세요.



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Python용 Azure Machine Learning SDK v1.17.0
+ **새 예제**
  + https://github.com/Azure/azureml-examples 에 새 커뮤니티 기반 예제 리포지토리가 제공됩니다.
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + get_output이 XGBoostError를 발생시킬 수 있는 문제를 해결했습니다.
  + **azureml-automl-runtime**
    + AutoML에서 만든 시간/달력 기반 기능에 이제 접두사가 있습니다.
    + 다수의 클래스와 하위 샘플링을 사용하는 분류 데이터 세트에 StackEnsemble을 학습시키는 동안 발생하는 IndexError를 해결했습니다.
    + 모델을 다시 맞춘 후 VotingRegressor 예측이 정확하지 않을 수 있는 문제가 해결되었습니다.
  + **azureml-core**
    + AKS 배포 구성 및 Azure Kubernetes 서비스 개념 간의 관계에 대한 추가 세부 정보가 추가되었습니다.
    + 환경 클라이언트 레이블 지원. 사용자가 환경에 레이블을 지정하고 레이블로 환경을 참조할 수 있습니다.
  + **azureml-dataprep**
    + Scala 2.12에서 현재 지원되지 않는 Spark를 사용하는 경우에 표시되는 오류 메시지가 개선되었습니다.
  + **azureml-explain-model**
    + azureml-explain-model 패키지가 공식적으로 사용이 중단됨
  + **azureml.mlflow**
    + 완료 중 상태가 제대로 처리되지 않은 azureml 백 엔드에 대한 mlflow.projects.run의 버그가 해결되었습니다.
  + **azureml-pipeline-core**
    + 하나의 파이프라인 엔드포인트를 기반으로 파이프라인 일정을 만들고, 나열하고, 가져오는 지원 추가.
    +  잘못된 사용법 예제로 PipelineData.as_dataset 설명서 개선 - 이제 PipelineData.as_dataset를 잘못 사용하면 ValueException이 throw됩니다.
    + HyperDriveStep 실행 직후 PipelineStep 내에서 최적의 모델을 등록하도록 HyperDriveStep 파이프라인 Notebook을 변경했습니다.
  + **azureml-pipeline-steps**
    + HyperDriveStep 실행 직후 PipelineStep 내에서 최적의 모델을 등록하도록 HyperDriveStep 파이프라인 Notebook을 변경했습니다.
  + **azureml-train-automl-client**
    + get_output이 XGBoostError를 발생시킬 수 있는 문제를 해결했습니다.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(10월 업데이트)
+ **새로운 기능**
  + [전체 가상 네트워크 지원](./how-to-enable-studio-virtual-network.md)
  + [포커스 모드](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Notebook 저장 Ctrl-S
  + 줄 번호

+ **버그 수정 및 개선 사항**
  + 속도 및 커널 안정성 향상
  + Jupyter 위젯 UI 업데이트

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Python용 Azure Machine Learning SDK v1.16.0
+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + AKSWebservice 및 AKSEndpoints는 이제 Pod 수준 CPU 및 메모리 리소스 제한을 지원합니다. 이러한 선택적 제한은 해당 CLI 호출에서 `--cpu-cores-limit` 및 `--memory-gb-limit`플래그를 설정하여 사용할 수 있습니다.
  + **azureml-core**
    + azureml-core 직접 종속성의 주 버전 고정
    + AKSWebservice 및 AKSEndpoints는 이제 Pod 수준 CPU 및 메모리 리소스 제한을 지원합니다. [Kubernetes 리소스 및 제한](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)에 대한 자세한 정보
    + 개별 행을 로그할 수 있도록 run.log_table이 업데이트되었습니다.
    + 작업 영역을 사용하여 실행만 검색하는 정적 메서드 `Run.get(workspace, run_id)`이 추가됨 
    + 작업 영역 내에서 실행을 검색하기 위한 인스턴스 메서드 `Workspace.get_run(run_id)`이 추가됨
    + 사용자가 스크립트 및 인수 대신 명령을 제출할 수 있게 해주는 실행 구성의 명령 속성 도입.
  + **azureml-interpret**
    + azureml-interpret에서 설명 클라이언트 is_raw 플래그 동작이 고정됨
  + **azureml-sdk**
    + `azureml-sdk`는 공식적으로 Python 3.8을 지원합니다.
  + **azureml-train-core**
    + TensorFlow 2.3 큐레이팅된 환경 추가
    + 사용자가 스크립트 및 인수 대신 명령을 제출할 수 있게 해주는 실행 구성의 명령 속성 도입.
  + **azureml-widgets**
    + 새롭게 설계된 스크립트 실행 위젯 인터페이스.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Python용 Azure Machine Learning SDK v1.15.0
+ **버그 수정 및 개선 사항**
  + **azureml-contrib-interpret**
    + LIME 설명자가 azureml-contrib-interpret에서 interpret-community 패키지로 이동했고, azureml-contrib-interpret 패키지에서 이미지 설명자가 제거됨
    + azureml-contrib-interpret 패키지에서 시각화 대시보드가 제거되었고, 설명 클라이언트가 azureml-interpret 패키지로 이동했으며, azureml-contrib-interpret 패키지에서 더 이상 사용되지 않고, 개선된 API를 반영하도록 Notebook이 업데이트됨
    + azureml-interpret, azureml-explain-model, azureml-contrib-interpret 및 azureml-tensorboard에 대한 pypi 패키지 설명 수정
  + **azureml-contrib-notebook**
    + papermill 1.x가 계속 작동하도록 nbcovert 종속성을 6 미만으로 고정.
  + **azureml-core**
    + 사용자가 각 개별 특성을 설정하지 않고도 클래스 특성을 보다 효율적으로 초기화할 수 있게 해주는 매개 변수가 TensorflowConfiguration 및 MpiConfiguration 생성자에 추가되었습니다. ScriptRunConfig에서 분산 PyTorch 작업을 구성하기 위한 PyTorchConfiguration 클래스를 추가했습니다.
    + 인증 오류를 해결하기 위해 azure-mgmt-resource 리소스의 버전을 고정했습니다.
    + Triton 코드 없는 배포 지원
    + 이제 Run.start_logging()에 지정된 출력 디렉터리가 대화형 시나리오에서 실행을 사용할 때 추적됩니다. 추적된 파일은 Run.complete() 호출 시 ML Studio에 표시됩니다.
    + 이제 `Dataset.Tabular.from_delimited_files` 및 `Dataset.Tabular.from_json_lines_files`로 데이터 세트를 만드는 동안 `encoding` 인수를 전달하여 파일 인코딩을 지정할 수 있습니다. 지원되는 인코딩은 'utf8', 'iso88591', 'latin1', 'ascii', utf16', 'utf32', 'utf8bom' 및 'windows1252'입니다.
    + 환경 개체가 ScriptRunConfig 생성자에 전달되지 않은 경우에 발생하는 버그 수정.
    + 다른 컴퓨터에서 로컬 실행을 취소할 수 있도록 Run.cancel()이 업데이트되었습니다.
  + **azureml-dataprep**
    +  데이터 세트 탑재 시간 제한 문제가 해결되었습니다.
  + **azureml-explain-model**
    + azureml-interpret, azureml-explain-model, azureml-contrib-interpret 및 azureml-tensorboard에 대한 pypi 패키지 설명 수정
  + **azureml-interpret**
    + azureml-contrib-interpret 패키지에서 시각화 대시보드가 제거되었고, 설명 클라이언트가 azureml-interpret 패키지로 이동했으며, azureml-contrib-interpret 패키지에서 더 이상 사용되지 않고, 개선된 API를 반영하도록 Notebook이 업데이트됨
    + azureml-interpret 패키지가 interpret-community 0.15.0에 종속되도록 업데이트됨
    + azureml-interpret, azureml-explain-model, azureml-contrib-interpret 및 azureml-tensorboard에 대한 pypi 패키지 설명 수정
  + **azureml-pipeline-core**
    +  기존 데이터 세트 이름으로 설정된 `name` 매개 변수를 사용하여 `register_on_complete`를 호출할 때 시스템이 응답을 중지할 수 있는 `OutputFileDatasetConfig` 관련 파이프라인 문제가 해결되었습니다.
  + **azureml-pipeline-steps**
    + 오래된 databrick Notebook이 제거되었습니다.
  + **azureml-tensorboard**
    + azureml-interpret, azureml-explain-model, azureml-contrib-interpret 및 azureml-tensorboard에 대한 pypi 패키지 설명 수정
  + **azureml-train-automl-runtime**
    + azureml-contrib-interpret 패키지에서 시각화 대시보드가 제거되었고, 설명 클라이언트가 azureml-interpret 패키지로 이동했으며, azureml-contrib-interpret 패키지에서 더 이상 사용되지 않고, 개선된 API를 반영하도록 Notebook이 업데이트됨
  + **azureml-widgets**
    + azureml-contrib-interpret 패키지에서 시각화 대시보드가 제거되었고, 설명 클라이언트가 azureml-interpret 패키지로 이동했으며, azureml-contrib-interpret 패키지에서 더 이상 사용되지 않고, 개선된 API를 반영하도록 Notebook이 업데이트됨

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Python용 Azure Machine Learning SDK v1.14.0
+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 그리드 프로파일링이 SDK에서 제거되었으며 더 이상 지원되지 않습니다.
  + **azureml-accel-models**
    + azureml-accel-models 패키지가 이제 TensorFlow 2.x를 지원합니다.
  + **azureml-automl-core**
    + 로컬 버전의 pandas/sklearn이 학습 중에 사용된 버전과 일치하지 않는 경우에 대한 get_output 오류 처리가 추가됨
  + **azureml-automl-runtime**
    + AutoArima 반복이 PredictionException 및 "예측 중에 자동 오류가 발생했습니다"라는 메시지와 함께 실패하는 버그가 수정되었습니다.
  + **azureml-cli-common**
    + 그리드 프로파일링이 SDK에서 제거되었으며 더 이상 지원되지 않습니다.
  + **azureml-contrib-server**
    + pypi 개요 페이지에 대한 패키지 설명 업데이트.
  + **azureml-core**
    + 그리드 프로파일링이 SDK에서 제거되었으며 더 이상 지원되지 않습니다.
    + 작업 영역 검색에 실패할 경우의 오류 메시지 수가 줄었습니다.
    + 메타데이터를 페치하지 못할 때 경고 표시 안 함
    + 새 Kusto 단계 및 Kusto 컴퓨팅 대상.
    + sku 매개 변수에 대한 문서 업데이트. CLI 및 SDK의 작업 영역 업데이트 기능에서 sku 제거.
    + pypi 개요 페이지에 대한 패키지 설명 업데이트.
    + AzureML 환경에 대한 설명서가 업데이트되었습니다.
    + SDK의 AML 작업 영역에 대한 서비스 관리형 리소스 설정 노출.
  + **azureml-dataprep**
    + 데이터 세트 탑재를 위해 파일에 대한 실행 권한 지원.
  + **azureml.mlflow**
    + AzureML MLflow 설명서 및 Notebook 샘플이 업데이트됨 
    + AzureML 백 엔드를 사용하는 MLflow 프로젝트에 대한 새로운 지원
    + MLflow 모델 레지스트리 지원
    + AzureML-MLflow 작업에 대한 Azure RBAC 지원이 추가됨 
    
  + **azureml-pipeline-core**
    + PipelineOutputFileDataset.parse_ * 메서드에 대한 설명서가 개선되었습니다.
    + 새 Kusto 단계 및 Kusto 컴퓨팅 대상.
    + 사용자가 게시된 파이프라인 엔드포인트에 대한 스키마 정의를 보는 데 사용할 수 있는 pipeline-endpoint 엔터티에 대한 Swaggerurl 속성이 제공되었습니다.
  + **azureml-pipeline-steps**
    + 새 Kusto 단계 및 Kusto 컴퓨팅 대상.
  + **azureml-telemetry**
    + pypi 개요 페이지에 대한 패키지 설명 업데이트.
  + **azureml-train**
    + pypi 개요 페이지에 대한 패키지 설명 업데이트.
  + **azureml-train-automl-client**
    + 로컬 버전의 pandas/sklearn이 학습 중에 사용된 버전과 일치하지 않는 경우에 대한 get_output 오류 처리가 추가됨
  + **azureml-train-core**
    + pypi 개요 페이지에 대한 패키지 설명 업데이트.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Python용 Azure Machine Learning SDK v1.13.0
+ **미리 보기 기능**
  + **azureml-core** 새 출력 데이터 세트 기능을 사용하면 Blob, ADLS Gen 1, ADLS Gen 2 및 FileShare를 포함한 클라우드 스토리지에 쓰기 저장할 수 있습니다. 데이터를 출력할 위치, 탑재 또는 업로드를 통해 데이터를 출력하는 방법, 향후 재사용 및 공유를 위해 출력 데이터를 등록하고 및 파이프라인 단계 간에 중간 데이터를 원활하게 전달할지 여부를 구성할 수 있습니다. 이렇게 하면 재현, 공유가 가능해지고, 데이터 중복이 방지되며, 비용 효율 및 생산성 향상이 가능합니다. [사용 방법 알아보기](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoML에 대한 모든 pip 종속성을 고정하기 위한 validated_{platform}_requirements.txt 파일이 추가되었습니다.
    + 이 릴리스는 4Gb보다 큰 모델을 지원합니다.
    + 업그레이드된 AutoML 종속성: `scikit-learn`(현재 0.22.1), `pandas`(현재 0.25.1), `numpy`(현재 1.18.2).
  + **azureml-automl-runtime**
    + 항상 fp16 압축을 사용하도록 텍스트 DNN에 horovod를 설정합니다.
    + 이 릴리스는 4Gb보다 큰 모델을 지원합니다.
    + AutoML이 ImportError: `RollingOriginValidator` 이름을 가져올 수 없음과 함께 실패하는 문제가 해결되었습니다.
    + 업그레이드된 AutoML 종속성: `scikit-learn`(현재 0.22.1), `pandas`(현재 0.25.1), `numpy`(현재 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + 업그레이드된 AutoML 종속성: `scikit-learn`(현재 0.22.1), `pandas`(현재 0.25.1), `numpy`(현재 1.18.2).
  + **azureml-contrib-fairness**
    + azureml-contrib-fairness에 대한 간단한 설명을 제공합니다.
  + **azureml-contrib-pipeline-steps**
    + 이 패키지가 더 이상 사용되지 않으며 사용자가 azureml-pipeline-steps를 대신 사용해야 함을 나타내는 메시지가 추가되었습니다.
  + **azureml-core**
    + 작업 영역에 대한 키 나열 명령이 추가되었습니다.
    + 작업 영역 SDK 및 CLI에 태그 매개 변수가 추가되었습니다.
    + `TypeError: can't pickle _thread.RLock objects`로 인해 데이터 세트가 있는 자식 실행을 제출하지 못하는 버그가 수정되었습니다.
    + Model list()에 대한 page_count 기본값/설명서 추가.
    + adbworkspace 매개 변수 및 Add workspace adb lin/unlink runner를 사용하도록 CLI&SDK가 수정되었습니다.
    + 업데이트가 호출된 데이터 세트의 버전이 아니라 최신 데이터 세트 버전이 업데이트되도록 하는 Dataset.update의 버그 수정. 
    + 특정 이전 버전이 검색된 경우에도 최신 데이터 세트 버전에 대한 태그를 표시하는 Dataset.get_by_name의 버그 수정.
  + **azureml-interpret**
    + 원래 설명자의 shap_values_output 매개 변수를 기반으로 azureml-interpret의 shap 채점 설명자에 가능성 출력이 추가되었습니다.
  + **azureml-pipeline-core**
    + `PipelineOutputAbstractDataset.register`의 설명서 개선.
  + **azureml-train-automl-client**
    + 업그레이드된 AutoML 종속성: `scikit-learn`(현재 0.22.1), `pandas`(현재 0.25.1), `numpy`(현재 1.18.2).
  + **azureml-train-automl-runtime**
    + 업그레이드된 AutoML 종속성: `scikit-learn`(현재 0.22.1), `pandas`(현재 0.25.1), `numpy`(현재 1.18.2).
  + **azureml-train-core**
    + 이제 사용자는 HyperDriveConfig를 만들 때 유효한 hyperparameter_sampling 인수를 제공해야 합니다. 또한 사용자에게 HyperDriveRunConfig의 사용 중단을 알리도록 HyperDriveRunConfig에 대한 설명서가 편집되었습니다.
    + PyTorch 기본 버전이 1.4로 되돌아감.
    + PyTorch 1.6 및 TensorFlow 2.2 이미지와 큐레이팅된 환경을 추가합니다.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning 스튜디오 Notebook 환경(8월 업데이트)
+ **새로운 기능**
  + 새 시작 방문 페이지 
  
+ **미리 보기 기능**
    + Notebook의 수집 기능. 이제 사용자는 [수집](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) 기능을 사용하여 Notebook을 쉽게 정리할 수 있습니다. 수집 기능은 Notebook의 자동화된 종속성 분석을 사용하는 중요한 코드를 유지하고 관련이 없는 부분을 제거합니다.

+ **버그 수정 및 개선 사항**
  + 속도 및 안정성 향상
  + 어둡게 모드 버그가 수정됨
  + 출력 스크롤 버그가 수정됨
  + 이제 샘플 검색이 Azure Machine Learning 샘플 Notebook 리포지토리에 있는 모든 파일의 모든 콘텐츠를 검색함
  + 이제 여러 줄 R 셀을 실행할 수 있음
  + 이제 최초 확인 후 "이 파일의 내용을 신뢰합니다"가 자동으로 확인됨
  + 새로운 "복사본 만들기" 옵션을 통해 충돌 해결 대화 상자가 개선됨
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Python용 Azure Machine Learning SDK v1.12.0

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 빌드된 패키지 이미지의 이름을 바꿀 수 있도록 Model.package()에 image_name 및 image_label 매개 변수 추가
  + **azureml-automl-core**
    + 읽고 있는 콘텐츠가 수정되면 AutoML이 dataprep에서 새 오류 코드를 발생시킵니다.
  + **azureml-automl-runtime**
    + 데이터에 누락된 값이 포함되어 있지만 기능화가 해제된 경우에 사용자에게 표시되는 경고가 추가되었습니다.
    + 데이터에 nan이 포함되어 있고 기능화가 해제된 경우에 발생하는 자식 실행 오류가 해결되었습니다.
    + 읽고 있는 콘텐츠가 수정되면 AutoML이 dataprep에서 새 오류 코드를 발생시킵니다.
    + 예측 메트릭이 조직별로 발생하도록 정규화가 업데이트되었습니다.
    + 되돌아보기 기능이 비활성화된 경우의 예측 분위수 계산이 개선되었습니다.
    + AutoML 후 설명을 계산할 때 부울 스파스 행렬 처리가 수정되었습니다.
  + **azureml-core**
    + 이제 새 메서드 `run.get_detailed_status()`에서 현재 실행 상태에 대한 자세한 설명을 보여 줍니다. 현재는 `Queued` 상태에 대한 설명만 보여 줍니다.
    + 빌드된 패키지 이미지의 이름을 바꿀 수 있도록 Model.package()에 image_name 및 image_label 매개 변수 추가
    + [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies)의 전체 pip 섹션을 한 번에 설정하는 새 메서드 `set_pip_requirements()`.
    + 자격 증명이 없는 ADLS Gen2 데이터 저장소 등록 지원.
    + 잘못된 데이터 세트 형식을 다운로드하거나 탑재하려고 할 때 발생하는 오류 메시지가 개선되었습니다.
    + 필터 최적화를 제공하는 partition_timestamp의 더 많은 예제로 시계열 데이터 세트 필터 샘플 Notebook 업데이트.
    + 프라이빗 엔드포인트 연결을 삭제할 때 ArmResourceId 대신 subscriptionId, resourceGroup, workspaceName, peConnectionName을 매개 변수로 허용하도록 sdk 및 CLI 변경.
    + 실험적 데코레이터는 더욱 쉽게 식별할 수 있는 클래스 이름을 표시합니다.
    + 모델 내 자산에 대한 설명이 더 이상 실행을 기반으로 자동 생성되지 않습니다.
  + **azureml-datadrift**
    + DataDriftDetector의 create_from_model API를 사용되지 않는 것으로 표시.
  + **azureml-dataprep**
    + 잘못된 데이터 세트 형식을 다운로드하거나 탑재하려고 할 때 발생하는 오류 메시지가 개선되었습니다.
  + **azureml-pipeline-core**
    + 등록된 데이터 세트를 포함하는 파이프라인 그래프를 역직렬화할 때 발생하는 버그가 수정되었습니다.
  + **azureml-pipeline-steps**
    + RScriptStep은 azureml.core.environment의 RSection을 지원합니다.
    + `AutoMLStep` 공용 API에서 passthru_automl_config 매개 변수가 제거되고 내부 전용 매개 변수로 변환되었습니다.
  + **azureml-train-automl-client**
    + AutoML에서 로컬 비동기 관리 환경이 제거되었습니다. 실행이 시작된 환경에서 모든 로컬 실행이 수행됩니다.
    + 사용자 제공 스크립트 없이 AutoML 실행을 제출할 때 발생하는 스냅샷 문제가 해결되었습니다.
    + 데이터에 nan이 포함되어 있고 기능화가 해제된 경우에 발생하는 자식 실행 오류가 해결되었습니다.
  + **azureml-train-automl-runtime**
    + 읽고 있는 콘텐츠가 수정되면 AutoML이 dataprep에서 새 오류 코드를 발생시킵니다.
    + 사용자 제공 스크립트 없이 AutoML 실행을 제출할 때 발생하는 스냅샷 문제가 해결되었습니다.
    + 데이터에 nan이 포함되어 있고 기능화가 해제된 경우에 발생하는 자식 실행 오류가 해결되었습니다.
  + **azureml-train-core**
    + `pip_requirements_file` 매개 변수를 통해 [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator)에 전달된 pip 요구 사항 파일에 pip 옵션(예: --extra-index-url)을 지정하기 위한 지원이 추가되었습니다.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Python용 Azure Machine Learning SDK v1.11.0

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI 모델 등록 경로의 실행 개체에 전달되지 않는 모델 프레임워크 수정
    + 테넌트 ID 및 보안 주체 ID를 표시하도록 CLI amlcompute identity show 명령 수정 
  + **azureml-train-automl-client**
    + 연결된 모델을 다운로드하지 않고 AutoML 실행에 대한 최적의 자식 실행을 페치하는 get_best_child ()가 AutoMLRun에 추가되었습니다.
    + 모델을 로컬에 다운로드하지 않아도 예측이 원격 학습 환경에서 실행될 수 있도록 하는 ModelProxy 개체가 추가되었습니다.
    + AutoML의 처리되지 않은 예외가 이제 오류에 대한 자세한 정보를 확인할 수 있는 알려진 문제 HTTP 페이지를 가리킵니다.
  + **azureml-core**
    + 모델 이름은 255자까지 가능합니다.
    + Environment.get_image_details() 반환 개체 형식이 변경되었습니다. `dict`가 `DockerImageDetails` 클래스로 대체되었습니다. 새 클래스 속성에서 이미지 세부 정보를 볼 수 있습니다. 변경 내용은 이전 버전과 호환됩니다.
    + 종속성 구조를 유지하도록 Environment.from_pip_requirements()에 대한 버그 수정
    + int 및 double이 동일한 목록에 포함된 경우 log_list가 실패하는 버그가 수정되었습니다.
    + 기존 작업 영역에서 프라이빗 링크를 사용하도록 설정하는 동안 작업 영역과 연결된 컴퓨팅 대상이 있는 경우 해당 대상이 작업 영역 프라이빗 엔드포인트와 동일한 가상 네트워크 뒤에 있지 않으면 작동하지 않습니다.
    + 실험에서 데이터 세트를 사용할 때 `as_named_input`이 선택 사항이 되었고 `as_mount` 및 `as_download`가 `FileDataset`에 추가되었습니다. `as_mount` 또는 `as_download`가 호출되면 입력 이름이 자동으로 생성됩니다.
  + **azureml-automl-core**
    + AutoML의 처리되지 않은 예외가 이제 오류에 대한 자세한 정보를 확인할 수 있는 알려진 문제 HTTP 페이지를 가리킵니다.
    + 연결된 모델을 다운로드하지 않고 AutoML 실행에 대한 최적의 자식 실행을 페치하는 get_best_child ()가 AutoMLRun에 추가되었습니다.
    + 모델을 로컬에 다운로드하지 않아도 예측이 원격 학습 환경에서 실행될 수 있도록 하는 ModelProxy 개체가 추가되었습니다.
  + **azureml-pipeline-steps**
    + `AutoMLStep`에 `enable_default_model_output` 및 `enable_default_metrics_output` 플래그가 추가되었습니다. 이러한 플래그를 사용하여 기본 출력을 사용하거나 사용하지 않도록 설정할 수 있습니다.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Python용 Azure Machine Learning SDK v1.10.0

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoML을 사용하는 경우 경로가 AutoMLConfig 개체에 전달되며, 이 개체가 아직 없는 경우 자동으로 만들어집니다.
    + 이제 사용자는 `freq` 매개 변수를 사용하여 예측 작업에 대한 시간 계열 빈도를 지정할 수 있습니다.
  + **azureml-automl-runtime**
    + AutoML을 사용하는 경우 경로가 AutoMLConfig 개체에 전달되며, 이 개체가 아직 없는 경우 자동으로 만들어집니다.
    + 이제 사용자는 `freq` 매개 변수를 사용하여 예측 작업에 대한 시간 계열 빈도를 지정할 수 있습니다.
    + AutoML 예측은 이제 이동 평가를 지원합니다. 이 평가는 테스트 또는 유효성 검사 집합의 길이가 입력 구간보다 길며, 알려진 y_pred 값이 예측 컨텍스트로 사용되는 사용 사례에 적용됩니다.
  + **azureml-core**
    + 실행의 데이터 저장소에서 파일이 다운로드되지 않은 경우 경고 메시지가 인쇄됩니다.
    + `Datastore.register_azure_sql_database method`에 `skip_validation`에 대한 설명서가 추가되었습니다.
    + 자동 승인 프라이빗 엔드포인트를 만들려는 사용자는 sdk v1.10.0 이상으로 업그레이드해야 합니다. 여기에는 VNet 뒤에서 사용할 수 있는 Notebook 리소스가 포함됩니다.
    + get workspace에 대한 응답에 NotebookInfo가 노출됩니다.
    + 컴퓨팅 대상을 나열하고 컴퓨팅 대상을 가져오는 호출이 원격 실행에서 성공하도록 변경되었습니다. 컴퓨팅 대상을 가져오고 작업 영역 컴퓨팅 대상을 나열하는 Sdk 함수가 이제 원격 실행에서 작동합니다.
    + azureml.core.image 클래스에 대한 클래스 설명에 사용 중단 메시지 추가.
    + 작업 영역 프라이빗 엔드포인트 생성에 실패할 경우 예외를 throw하고 작업 영역 및 종속 리소스를 정리합니다.
    + 작업 영역 업데이트 메서드에서 작업 영역 sku 업그레이드를 지원합니다.
  + **azureml-datadrift**
    + python 3.8을 지원하도록 matplotlib 버전이 3.0.2에서 3.2.1로 업데이트되었습니다.
  + **azureml-dataprep**
    + `Range` 또는 `Head` 요청에 웹 url 데이터 원본에 대한 지원이 추가되었습니다. 
    + 파일 데이터 세트 탑재 및 다운로드의 안정성이 개선되었습니다.
  + **azureml-train-automl-client**
    + Setuptools에서 `RequirementParseError` 제거와 관련된 문제가 해결되었습니다.
    + "compute_target='local'"을 사용하여 제출된 로컬 실행에 conda 대신 docker 사용
    + 콘솔에 인쇄된 반복 기간이 수정되었습니다. 이전에는 반복 기간이 실행 종료 시간에서 실행 생성 시간을 뺀 시간으로 인쇄되는 경우가 있었습니다. 실행 종료 시간에서 실행 시작 시간을 뺀 값과 동일하게 수정되었습니다.
    + AutoML을 사용하는 경우 경로가 AutoMLConfig 개체에 전달되며, 이 개체가 아직 없는 경우 자동으로 만들어집니다.
    + 이제 사용자는 `freq` 매개 변수를 사용하여 예측 작업에 대한 시간 계열 빈도를 지정할 수 있습니다.
  + **azureml-train-automl-runtime**
    + 최적의 모델 설명이 실패할 경우 표시되는 콘솔 출력이 개선되었습니다.
    + 입력 매개 변수의 이름을 "blocked_models"로 변경하여 중요한 용어를 제거했습니다.
      + 입력 매개 변수의 이름을 "allowed_models"로 변경하여 중요한 용어를 제거했습니다.
    + 이제 사용자는 `freq` 매개 변수를 사용하여 예측 작업에 대한 시간 계열 빈도를 지정할 수 있습니다.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Python용 Azure Machine Learning SDK v1.9.0

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoML 자동 생성 채점 스크립트에서 get_model_path()가 AZUREML_MODEL_DIR 환경 변수로 바뀌었습니다. init() 중에 오류를 추적하는 원격 분석도 추가되었습니다.
    + AutoMLConfig의 일부로 `enable_cache`를 지정하는 기능이 제거되었습니다.
    + 특정 예측 실행 중 서비스 오류로 인해 실행이 실패할 수 있는 버그가 수정됨
    + `get_output` 도중 특정 모델에 대한 오류 처리 개선
    + y 변환기를 사용하여 분류하기 위해 fitted_model.fit(X, y)에 대한 호출이 수정됨
    + 예측 작업에서 사용자 지정된 정방향 채우기 입력자가 지원됨
    + dict 형식의 예측 매개 변수 대신 새 ForecastingParameters 클래스가 사용됨
    + 대상 구간 자동 검색 개선
    + BERT를 통한 다중 노드, 다중 GPU 분산 기능화의 제한된 가용성이 추가됨
  + **azureml-automl-runtime**
    + Prophet이 이제 곱셈 대신 가산적 계절성 모델링을 수행합니다.
    + 긴 조직과 빈도가 다른 짧은 조직으로 인해 실행이 실패하는 문제를 해결했습니다.
  + **azureml-contrib-automl-dnn-vision**
    + 학습 및 채점용 시스템/gpu 통계 및 로그 평균 수집
  + **azureml-contrib-mir**
    + ManagedInferencing에서 enable-app-insights 플래그에 대한 지원이 추가됨
  + **azureml-core**
    + 현재 컴퓨팅에서 데이터 원본에 액세스할 수 없는 경우 유효성 검사를 건너뛸 수 있도록 하는 이러한 API에 대한 유효성 검사 매개 변수.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + 모델 목록에 대한 프레임워크 필터링 지원을 추가하고 Notebook에 NCD AutoML 샘플을 다시 추가함
    + Datastore.register_azure_blob_container 및 Datastore.register_azure_file_share(SAS 토큰을 지원하는 옵션만)에서 일반적인 읽기 및 쓰기 시나리오에 대한 최소 권한 요구 사항을 포함하도록 `sas_token` 필드에 대한 문서 문자열이 업데이트되었습니다.
    + ws.get_mlflow_tracking_uri()에서 _with_auth 매개 변수 사용 중단
  + **azureml.mlflow**
    + AzureML-MLflow에서 로컬 file:// 모델 배포에 대한 지원 추가
    + ws.get_mlflow_tracking_uri()에서 _with_auth 매개 변수 사용 중단
  + **azureml-opendatasets**
    + 이제 SDK에서 최근에 게시된 코로나19 추적 데이터 세트를 사용할 수 있음
  + **azureml-pipeline-core**
    + "azureml-defaults"가 pip-dependency의 일부로 포함되지 않은 경우 로그 아웃 경고
    + 메모 렌더링 개선.
    + 구분 기호로 분리된 파일을 PipelineOutputFileDataset로 구문 분석할 때 따옴표로 묶인 줄 바꿈에 대한 지원이 추가되었습니다.
    + PipelineDataset 클래스는 더 이상 사용되지 않습니다. 자세한 내용은 https://aka.ms/dataset-deprecation를 참조하세요. 파이프라인에서 데이터 세트를 사용하는 방법을 알아보려면 https://aka.ms/pipeline-with-dataset 를 참조하세요.
  + **azureml-pipeline-steps**
    + azureml-pipeline-steps에 대한 문서 업데이트.
    +  ParallelRunConfig의 `load_yaml()`에서 사용자가 구성의 나머지 부분과 일치하도록 또는 별도의 파일을 사용하여 Environments를 정의할 수 있는 지원이 추가되었습니다.
  + **azureml-train-automl-client**.
    + AutoMLConfig의 일부로 `enable_cache`를 지정하는 기능이 제거되었습니다.
  + **azureml-train-automl-runtime**
    + BERT를 통한 다중 노드, 다중 GPU 분산 기능화의 제한된 가용성이 추가되었습니다.
    + ADB 기반의 자동화된 Machine Learning 실행에서 호환되지 않는 패키지에 대한 오류 처리가 추가되었습니다.
  + **azureml-widgets**
    + azureml-widgets에 대한 문서 업데이트.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Python용 Azure Machine Learning SDK v1.8.0
  
  + **미리 보기 기능**
    + **azureml-contrib-fairness** `azureml-contrib-fairness` 패키지는 오픈 소스 공정성 평가와 공정성 완화 패키지 [Fairlearn](https://fairlearn.github.io) 및 Azure Machine Learning 스튜디오 간에 통합을 제공합니다. 특히 이 패키지를 사용하면 모델 공정성 평가 대시보드가 AzureML 실행의 일부로 업로드되고 Azure Machine Learning 스튜디오에 표시됩니다.

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + init 컨테이너의 로그 가져오기 지원.
    + ComputeInstance를 관리하는 새 CLI 명령이 추가됨
  + **azureml-automl-core**
    + 이제 사용자는 잠재적으로 과잉 맞춤될 수 있다는 경고와 스택 앙상블 반복을 시계열 작업에 사용하도록 설정할 수 있습니다.
    + 캐시 저장소 콘텐츠가 변조된 경우에 발생하는 새 유형의 사용자 예외가 추가됨
  + **azureml-automl-runtime**
    + 사용자가 기능화를 사용하지 않도록 설정하면 Class Balancing Sweeping이 더 이상 사용되지 않습니다.  
  + **azureml-contrib-itp**
    + CmAks 컴퓨팅 유형이 지원됩니다. 학습 작업을 위해 작업 영역에 사용자 고유의 AKS 클러스터를 연결할 수 있습니다.
  + **azureml-contrib-notebook**
    + azureml-contrib-notebook 패키지에 대한 문서 개선.
  + **azureml-contrib-pipeline-steps**
    + azureml-contrib--pipeline-steps 패키지에 대한 문서 개선.
  + **azureml-core**
    + 고객이 작업 영역 연결 리소스에서 작업을 수행할 수 있도록 set_connection, get_connection, list_connections, delete_connection 함수 추가
    + zureml-coore/azureml.exceptions 패키지에 대한 설명서 업데이트.
    + azureml-core 패키지에 대한 설명서 업데이트.
    + ComputeInstance 클래스에 대한 문서 업데이트.
    + azureml-core/azureml.core.compute 패키지에 대한 문서 개선.
    + azureml-core의 웹 서비스 관련 클래스에 대한 문서 개선.
    + 사용자가 선택한 데이터 저장소에 프로파일링 데이터를 저장하도록 지원
    + 모델 목록 API에 대한 expand 및 page_count 속성이 추가됨
    + overwrite 속성을 제거하면 제출된 실행이 역직렬화 오류로 인해 실패하는 버그가 수정되었습니다.
    + 단일 파일을 참조하는 FileDataset를 다운로드하거나 탑재할 때 일관되지 않은 폴더 구조가 수정되었습니다.
    + 이제 parquet 파일 to_spark_dataframe의 데이터 세트가 더 빠르게 로드되고 모든 parquet 및 Spark SQL 데이터 형식이 지원됩니다.
    + init 컨테이너의 로그 가져오기 지원.
    + AutoML 실행이 이제 병렬 실행 단계의 자식 실행으로 표시됩니다.
  + **azureml-datadrift**
    + azureml-contrib-notebook 패키지에 대한 문서 개선.
  + **azureml-dataprep**
    + 이제 parquet 파일 to_spark_dataframe의 데이터 세트가 더 빠르게 로드되고 모든 parquet 및 Spark SQL 데이터 형식이 지원됩니다.
    + to_pandas_dataframe에 대한 OutOfMemory 문제의 메모리 처리가 개선되었습니다.
  + **azureml-interpret**
    + interpret-community 커뮤니티 버전 0.12를 사용하도록 azureml-interpret가 업그레이드되었습니다.*
  + **azureml.mlflow**
    + azureml-mlflow에 대한 문서 개선.
    + MLFlow를 사용하는 AML 모델 레지스트리에 대한 지원 추가.
  + **azureml-opendatasets**
    + Python 3.8에 대한 지원이 추가됨
  + **azureml-pipeline-core**
    + 내부 클래스임을 명시하도록 `PipelineDataset`의 설명서가 업데이트되었습니다.
    + ParallelRunStep이 한 인수에 여러 값을 허용하도록 업데이트되었습니다(예: "--group_column_names", "Col1", "Col2", "Col3").
    + 파이프라인에서 AutoMLStep의 중간 데이터 사용에 대한 passthru_automl_config 요구 사항이 제거되었습니다.
  + **azureml-pipeline-steps**
    + azureml-pipeline-steps 패키지에 대한 문서 개선.
    + 파이프라인에서 AutoMLStep의 중간 데이터 사용에 대한 passthru_automl_config 요구 사항이 제거되었습니다.
  + **azureml-telemetry**
    + azureml-telemetry에 대한 문서 개선.
  + **azureml-train-automl-client**
    + `AutoMLConfig` 개체에서 `experiment.submit()`가 두 번 호출되어 다른 동작을 발생시킨 버그가 수정되었습니다.
    + 이제 사용자는 잠재적으로 과잉 맞춤될 수 있다는 경고와 스택 앙상블 반복을 시계열 작업에 사용하도록 설정할 수 있습니다.
    + 서비스에서 사용자 오류를 throw하는 경우 UserErrorException이 발생하도록 AutoML 실행 동작이 변경됨
    + 원격 컴퓨팅 대상에서 AutoML 실험을 수행할 때 azureml_automl.log가 생성되지 않거나 누락된 로그가 되는 버그가 수정되었습니다.
    + 불균형 클래스가 있는 Classification 데이터 세트에 Weight Balancing이 적용됩니다(기능 스위퍼에서 하위 샘플링된 데이터에 대해 이를 결정한 경우). Weight Balancing은 특정 임계값으로 분류 작업의 성능을 개선합니다.
    + AutoML 실행이 이제 병렬 실행 단계의 자식 실행으로 표시됩니다.
  + **azureml-train-automl-runtime**
    + 서비스에서 사용자 오류를 throw하는 경우 UserErrorException이 발생하도록 AutoML 실행 동작이 변경됨
    + AutoML 실행이 이제 병렬 실행 단계의 자식 실행으로 표시됩니다.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Python용 Azure Machine Learning SDK v1.7.0

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI 명령 및 패키지 의존성 정리를 통해 mir contrib에서 모델 프로파일링 제거 완료. 코어에서 모델 프로파일링을 사용할 수 있습니다.
    + 최소 Azure CLI 버전이 2.3.0으로 업그레이드됨
  + **azureml-automl-core**
    + 사용자 지정 변환기 매개 변수로 인한 기능화 단계 fit_transform()에 대한 예외 메시지 개선.
    + 자동화된 ML에서 BERT와 같은 딥 러닝 변환기 모델에 대한 다국어 지원 추가.
    + 설명서에서 사용되지 않는 lag_length 매개 변수 제거.
    + 예측 매개 변수 설명서가 개선되었습니다. lag_length 매개 변수는 더 이상 사용되지 않습니다.
  + **azureml-automl-runtime**
    + 예측/테스트 시간에 범주 열 중 하나가 비어 있을 때 발생하는 오류가 해결되었습니다.
    + 되돌아보기 기능이 설정되어 있고 데이터에 짧은 조직이 포함된 경우 발생하는 실행 오류가 해결되었습니다.
    + 구간 또는 이동 기간이 'auto'로 설정된 경우 중복된 시간 인덱스 오류 메시지와 관련된 문제가 해결되었습니다.
    + 되돌아보기 기능을 포함하는 데이터 세트의 Prophet 및 Arima 모델과 관련된 문제가 해결되었습니다.
    + 예측 작업의 날짜 시간 열에 1677-09-21 이전 또는 2262-04-11 이후의 날짜 지원이 추가되었습니다. 오류 메시지가 개선되었습니다.
    + 예측 매개 변수 설명서가 개선되었습니다. lag_length 매개 변수는 더 이상 사용되지 않습니다.
    + 사용자 지정 변환기 매개 변수로 인한 기능화 단계 fit_transform()에 대한 예외 메시지 개선.
    + 자동화된 ML에서 BERT와 같은 딥 러닝 변환기 모델에 대한 다국어 지원 추가.
    + 일부 OSError를 발생시키는 캐시 작업이 사용자 오류를 유발합니다.
    + 학습 및 유효성 검사 데이터의 개수와 열 집합이 같은지 확인하는 검사가 추가됨
    + 데이터에 따옴표가 포함된 경우 자동으로 생성되는 AutoML 채점 스크립트 관련 문제가 해결됨
    + AutoML Prophet 및 Prophet 모델을 포함하는 앙상블 모델에 대한 설명 지원.
    + 최근 고객 문제에서 Class Balancing 논리가 제대로 설정되지 않은 경우에도 Class-Balancing-Sweeping을 따라 메시지를 로그하는 라이브 사이트 버그가 출현했습니다. 이 PR을 사용하여 해당 로그/메시지 제거.
  + **azureml-cli-common**
    + CLI 명령 및 패키지 의존성 정리를 통해 mir contrib에서 모델 프로파일링 제거 완료. 코어에서 모델 프로파일링을 사용할 수 있습니다.
  + **azureml-contrib-reinforcementlearning**
    + 부하 테스트 도구
  + **azureml-core**
    + Script_run_config.py에 대한 설명서 변경
    + run submit-pipeline CLI의 출력 인쇄와 관련된 버그 수정
    + azureml-core/azureml.data에 대한 설명서 개선
    + hdfs getconf 명령을 사용하여 스토리지 계정을 검색할 때 발생하는 문제 해결
    + register_azure_blob_container 및 register_azure_file_share 설명서 개선
  + **azureml-datadrift**
    + 데이터 세트 드리프트 모니터 비활성화 및 활성화 구현 개선
  + **azureml-interpret**
    + 설명 클라이언트에서 아티팩트에서 업로드할 때 json 직렬화 전에 NaN 또는 Inf 제거
    + 많은 기능 및 클래스를 제공하는 최신 버전의 interpret-community로 업데이트하여 글로벌 설명에 대한 메모리 부족 오류 개선
    + 설명 업로드에 선택적 매개 변수 true_ys를 추가하여 스튜디오 UI에서 추가 기능 지원
    + download_model_explanations() 및 list_model_explanations() 성능 향상
    + 디버깅에 도움이 되도록 Notebook을 약간 수정
  + **azureml-opendatasets**
    + azureml-opendatasets를 사용하려면 azureml-dataprep 버전 1.4.0 이상이 필요합니다. 하위 버전이 검색되는 경우에 표시되는 경고가 추가됨
  + **azureml-pipeline-core**
    + 이러한 변경으로 인해 사용자가 module.Publish_python_script를 호출할 때 moduleVersion에 선택적 runconfig를 제공할 수 있습니다.
    + 노드 계정 사용은 azureml.pipeline.steps의 ParallelRunStep에서 파이프라인 매개 변수일 수 있습니다.
  + **azureml-pipeline-steps**
    + 이러한 변경으로 인해 사용자가 module.Publish_python_script를 호출할 때 moduleVersion에 선택적 runconfig를 제공할 수 있습니다.
  + **azureml-train-automl-client**
    + 자동화된 ML에서 BERT와 같은 딥 러닝 변환기 모델에 대한 다국어 지원 추가.
    + 설명서에서 사용되지 않는 lag_length 매개 변수 제거.
    + 예측 매개 변수 설명서가 개선되었습니다. lag_length 매개 변수는 더 이상 사용되지 않습니다.
  + **azureml-train-automl-runtime**
    + AutoML Prophet 및 Prophet 모델을 포함하는 앙상블 모델에 대한 설명 지원.
    + azureml-train-automl-* 패키지에 대한 설명서 업데이트.
  + **azureml-train-core**
    + PyTorch 예측 도구에서 TensorFlow 버전 2.1 지원
    + azureml-train-core 패키지 개선.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Python용 Azure Machine Learning SDK v1.6.0

+ **새로운 기능**
  + **azureml-automl-runtime**
    + 이제 AutoML 예측이 모델을 재학습시키지 않고 미리 지정된 최대 구간을 초과하는 고객 예측을 지원합니다. 예측 대상이 지정된 최대 구간보다 더 미래에 있는 경우, forecast() 함수는 재귀 작업 모드를 사용하여 이후 날짜까지 지점 예측을 수행합니다. 새 기능에 대한 설명은 [폴더](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)에 있는 "forecasting-forecast-function" Notebook의 "최대 구간보다 더 멀리 예측" 섹션을 참조하세요.
  
  + **azureml-pipeline-steps**
    + ParallelRunStep이 이제 릴리스되었으며 **azureml-pipeline-steps** 패키지의 일부입니다. **azureml-contrib-pipeline-steps** 패키지의 기존 ParallelRunStep은 더 이상 사용되지 않습니다. 공개 미리 보기 버전의 변경 내용:
      + 지정된 일괄 처리의 실행 메서드에 대한 최대 호출을 제어하는 구성 가능한 선택적 매개 변수 `run_max_try` 추가. 기본값은 3입니다.
      + PipelineParameter가 더 이상 자동 생성되지 않습니다. 다음 구성 가능한 값은 명시적으로 PipelineParameter로 설정할 수 있습니다.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + process_count_per_node의 기본값이 1로 변경되었습니다. 성능을 개선하려면 사용자가 이 값을 조정해야 합니다. 모범 사례는 노드의 GPU 또는 CPU 수로 설정하는 것입니다.
      + ParallelRunStep은 패키지를 주입하지 않으며, 사용자는 **azureml-core** 및 **azureml-dataprep[pandas, fuse]** 패키지를 환경 정의에 포함해야 합니다. user_managed_dependencies에 사용자 지정 docker 이미지를 사용하는 경우, 사용자가 이미지에 conda를 설치해야 합니다.
      
+ **주요 변경 내용**
  + **azureml-pipeline-steps**
    + azureml.dprep.Dataflow가 더 이상 AutoMLConfig의 유효한 입력 형식으로 사용되지 않음
  + **azureml-train-automl-client**
    + azureml.dprep.Dataflow가 더 이상 AutoMLConfig의 유효한 입력 형식으로 사용되지 않음

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + `get_output` 중에 사용자에게 클라이언트 다운그레이드를 요청하는 경고가 인쇄될 수 있는 버그를 수정했습니다.
    + 아직 버전 10에서 사용할 수 없으므로 cudatoolkit=9.0을 사용하도록 Mac을 업데이트했습니다.
    + 원격 컴퓨팅에 대해 학습된 경우 prophet 및 xgboost 모델에 대한 제한 제거.
    + AutoML의 로깅 개선
    + 예측 작업의 사용자 지정 기능화에 대한 오류 처리가 향상되었습니다.
    + 사용자가 지연된 기능을 포함하여 예측을 생성할 수 있는 기능이 추가되었습니다.
    + 사용자 오류를 올바르게 표시하도록 오류 메시지 업데이트.
    + training_data에 사용할 cv_split_column_names 지원
    + 예외 메시지 및 역추적 로깅 업데이트.
  + **azureml-automl-runtime**
    + 누락된 값 대체 예측을 위한 가드 레일 지원.
    + AutoML의 로깅 개선
    + 데이터 준비 예외에 대한 세분화된 오류 처리가 추가됨
    + 원격 컴퓨팅에 대해 학습된 경우 prophet 및 xgboost 모델에 대한 제한 제거.
    + `azureml-train-automl-runtime` 및 `azureml-automl-runtime`에서 `pytorch`, `scipy` 및 `cudatoolkit`에 대한 종속성이 업데이트되었습니다. 이제 `pytorch==1.4.0`, `scipy>=1.0.0,<=1.3.1` 및 `cudatoolkit==10.1.243`을 지원합니다.
    + 예측 작업의 사용자 지정 기능화에 대한 오류 처리가 향상되었습니다.
    + 예측 데이터 세트 빈도 검색 메커니즘이 개선되었습니다.
    + 일부 데이터 세트에 대한 Prophet 모델 학습 문제가 해결되었습니다.
    + 예측 중 최대 구간의 자동 검색이 개선되었습니다.
    + 사용자가 지연된 기능을 포함하여 예측을 생성할 수 있는 기능이 추가되었습니다.
    +  예측 모델을 재학습시키지 않고 학습된 구간을 넘어 예측을 제공할 수 있도록 예측 함수에 기능이 추가되었습니다.
    + training_data에 사용할 cv_split_column_names 지원
  + **azureml-contrib-automl-dnn-forecasting**
    + AutoML의 로깅 개선
  + **azureml-contrib-mir**
    + ManagedInferencing의 Windows 서비스에 대한 지원이 추가됨
    + MIR 컴퓨팅 연결과 같은 이전 MIR 워크플로 제거, SingleModelMirWebservice 클래스 - contrib-mir 패키지에 배치된 모델 프로파일링 제거
  + **azureml-contrib-pipeline-steps**
    + YAML 지원에 대한 사소한 수정
    + ParallelRunStep은 일반 공급으로 릴리스되었습니다. azureml.contrib.pipeline.steps는 사용 중단이 고지되었고 azureml.pipeline.steps로 이동합니다.
  + **azureml-contrib-reinforcementlearning**
    + RL 부하 테스트 도구
    + RL 예측 도구에 스마트 기본값이 있음
  + **azureml-core**
    + MIR 컴퓨팅 연결과 같은 이전 MIR 워크플로 제거, SingleModelMirWebservice 클래스 - contrib-mir 패키지에 배치된 모델 프로파일링 제거
    + 프로파일링 실패 시 사용자에게 제공되는 정보 수정: 요청 ID를 포함하고 메시지를 보다 의미 있게 각색했습니다. 프로파일링 실행기에 새 프로파일링 워크플로 추가
    + 데이터 세트 실행 실패 시 표시되는 오류 텍스트가 개선되었습니다.
    + 작업 영역 프라이빗 링크 CLI 지원이 추가되었습니다.
    + 잘못된 JSON이 포함된 줄을 처리하는 방법을 지정할 수 있게 해주는 선택적 매개 변수 `invalid_lines`가 `Dataset.Tabular.from_json_lines_files`에 추가되었습니다.
    + 다음 릴리스에서 컴퓨팅의 실행 기반 생성의 사용이 중단될 예정입니다. 실제 Amlcompute 클러스터를 영구 컴퓨팅 대상으로 만들고, 실행 구성에서 클러스터 이름을 컴퓨팅 대상으로 사용하는 것이 좋습니다. 예제 Notebook 참조: aka.ms/amlcomputenb
    + Dataset 실행 실패 시 표시되는 오류 메시지가 개선되었습니다.
  + **azureml-dataprep**
    + pyarrow 버전을 업그레이드하라는 경고가 더 명시적으로 바뀌었습니다.
    + 데이터 흐름을 실행하는 데 실패할 경우에 반환되는 메시지와 오류 처리가 개선되었습니다.
  + **azureml-interpret**
    + azureml-interpret 패키지에 대한 설명서 업데이트.
    + 최신 sklearn 업데이트와 호환되도록 해석력 패키지 및 Notebook 수정
  + **azureml-opendatasets**
    + 반환된 데이터가 없으면 None을 반환합니다.
    + to_pandas_dataframe의 성능을 개선합니다.
  + **azureml-pipeline-core**
    + YAML 로드가 중단된 ParallelRunStep에 대한 빠른 수정
    + ParallelRunStep은 일반 공급으로 릴리스되었습니다. azureml.contrib.pipeline.steps는 사용 중단이 고지되었고 azureml.pipeline.steps로 이동합니다. 새 기능은 다음과 같습니다. 1. PipelineParameter로 사용되는 데이터 세트 2. 새 매개 변수 run_max_retry 3. 구성 가능한 append_row 출력 파일 이름
  + **azureml-pipeline-steps**
    + azureml.dprep.Dataflow가 입력 데이터에 유효한 형식으로 사용되지 않습니다.
    + YAML 로드가 중단된 ParallelRunStep에 대한 빠른 수정
    + ParallelRunStep은 일반 공급으로 릴리스되었습니다. azureml.contrib.pipeline.steps는 사용 중단이 고지되었고 azureml.pipeline.steps로 이동합니다. 새 기능은 다음과 같습니다.
      + PipelineParameter로 사용되는 데이터 세트
      + 새 매개 변수 run_max_retry
      + 구성 가능한 append_row 출력 파일 이름
  + **azureml-telemetry**
    + 예외 메시지 및 역추적 로깅 업데이트.
  + **azureml-train-automl-client**
    + AutoML의 로깅 개선
    + 사용자 오류를 올바르게 표시하도록 오류 메시지 업데이트.
    + training_data에 사용할 cv_split_column_names 지원
    + azureml.dprep.Dataflow가 입력 데이터에 유효한 형식으로 사용되지 않습니다.
    + 아직 버전 10에서 사용할 수 없으므로 cudatoolkit=9.0을 사용하도록 Mac을 업데이트했습니다.
    + 원격 컴퓨팅에 대해 학습된 경우 prophet 및 xgboost 모델에 대한 제한 제거.
    + `azureml-train-automl-runtime` 및 `azureml-automl-runtime`에서 `pytorch`, `scipy` 및 `cudatoolkit`에 대한 종속성이 업데이트되었습니다. 이제 `pytorch==1.4.0`, `scipy>=1.0.0,<=1.3.1` 및 `cudatoolkit==10.1.243`을 지원합니다.
    + 사용자가 지연된 기능을 포함하여 예측을 생성할 수 있는 기능이 추가되었습니다.
  + **azureml-train-automl-runtime**
    + AutoML의 로깅 개선
    + 데이터 준비 예외에 대한 세분화된 오류 처리가 추가됨
    + 원격 컴퓨팅에 대해 학습된 경우 prophet 및 xgboost 모델에 대한 제한 제거.
    + `azureml-train-automl-runtime` 및 `azureml-automl-runtime`에서 `pytorch`, `scipy` 및 `cudatoolkit`에 대한 종속성이 업데이트되었습니다. 이제 `pytorch==1.4.0`, `scipy>=1.0.0,<=1.3.1` 및 `cudatoolkit==10.1.243`을 지원합니다.
    + 사용자 오류를 올바르게 표시하도록 오류 메시지 업데이트.
    + training_data에 사용할 cv_split_column_names 지원
  + **azureml-train-core**
    + 새 HyperDrive 관련 예외 집합을 추가했습니다. 이제 azureml.train.hyperdrive가 자세한 예외를 throw합니다.
  + **azureml-widgets**
    + AzureML Widgets가 JupyterLab에 표시되지 않습니다.
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Python용 Azure Machine Learning SDK v1.5.0

+ **새로운 기능**
  + **미리 보기 기능**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning은 [Ray](https://ray.io) 프레임워크를 사용하여 보충 학습에 대한 미리 보기 지원을 릴리스합니다. `ReinforcementLearningEstimator`를 사용하면 Azure Machine Learning의 GPU 및 CPU 컴퓨팅 대상에서 보충 학습 에이전트의 학습을 수행할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 이전 PR에서 실수로 남겨진 경고 로그를 수정합니다. 로그가 디버깅에 사용된 후 실수로 남아 있었습니다.
    + 버그 수정: 프로파일링하는 동안 클라이언트에 부분 오류를 알립니다.
  + **azureml-automl-core**
    + 데이터 세트에 여러 개의 시계열이 있는 경우 시계열에 대한 병렬 맞춤을 사용하도록 설정하여 AutoML 예측의 Prophet/AutoArima 모델 속도를 높입니다. 이 새로운 기능을 활용하려면 AutoMLConfig에서 "max_cores_per_iteration = -1"(즉, 사용 가능한 모든 cpu 코어 사용)을 설정하는 것이 좋습니다.
    + 콘솔 인터페이스에서 가드 레일 인쇄 시 KeyError 수정
    + experimentation_timeout_hours에 대한 오류 메시지 수정
    + AutoML에 TensorFlow 모델이 사용되지 않습니다.
  + **azureml-automl-runtime**
    + experimentation_timeout_hours에 대한 오류 메시지 수정
    + 캐시 저장소에서 역직렬화하려고 할 때 분류되지 않은 예외가 수정됨
    + 데이터 세트에 여러 개의 시계열이 있는 경우 시계열에 대한 병렬 맞춤을 사용하도록 설정하여 AutoML 예측의 Prophet/AutoArima 모델 속도를 높입니다.
    + 테스트/예측 집합에 학습 집합의 조직 중 하나가 포함되지 않은 경우, 데이터 세트에서 이동 기간이 설정된 예측을 수정했습니다.
    + 누락된 데이터의 처리 개선
    + 데이터 세트에 대해 예측하는 동안 시간순으로 정렬되지 않은 시계열을 포함하는 예측 간격 문제를 해결했습니다.
    + 예측 작업에서 데이터 셰이프에 대한 향상된 유효성 검사를 추가했습니다.
    + 빈도 검색이 향상되었습니다.
    + 예측 작업에 대한 교차 유효성 검사 단계를 생성할 수 없는 경우, 더 나은 오류 메시지를 생성합니다.
    + 누락 값 가드 레일을 올바르게 인쇄하도록 콘솔 인터페이스를 수정합니다.
    + AutoMLConfig에서 cv_split_indices 입력에 대한 데이터 형식 검사 적용.
  + **azureml-cli-common**
    + 버그 수정: 프로파일링하는 동안 클라이언트에 부분 오류를 알립니다.
  + **azureml-contrib-mir**
    + 현재 배포된 MIR 수정 버전 및 사용자가 지정한 최신 버전에 대한 정보를 릴레이하는 azureml.contrib.mir.RevisionStatus 클래스를 추가합니다. 이 클래스는 MirWebservice 개체의 'deployment_status' 특성에 포함되어 있습니다.
    + MirWebservice 유형의 Webservices 및 해당 자식 클래스 SingleModelMirWebservice에 대한 업데이트를 지원합니다.
  + **azureml-contrib-reinforcementlearning**
    + Ray 0.8.3에 대한 지원 추가
    + AmlWindowsCompute는 탑재된 스토리지로 Azure Files만 지원합니다.
    + health_check_timeout 이름이 health_check_timeout_seconds로 변경됨
    + 일부 클래스/메서드 설명을 수정했습니다.
  + **azureml-core**
    + Azure Government 및 중국 클라우드에서 WASB -> Blob 변환을 지원합니다.
    + 읽기 권한자 역할에서 az ml run CLI 명령을 사용하여 실행 정보를 얻을 수 있도록 하는 버그 수정
    + 입력 데이터 세트를 사용한 Azure ML 원격 실행 중에 발생하는 불필요한 로깅을 제거했습니다.
    + 이제 RCranPackage는 CRAN 패키지 버전에 대한 "version" 매개 변수를 지원합니다.
    + 버그 수정: 프로파일링하는 동안 클라이언트에 부분 오류를 알립니다.
    + azureml-core에 대한 유럽 스타일의 부동 처리를 추가했습니다.
    + Azure ml sdk에서 작업 영역 프라이빗 링크 기능을 지원합니다.
    + `from_delimited_files`를 사용하여 TabularDataset를 만들 때 부울 인수 `empty_as_string`을 설정하여 빈 값을 None으로 로드할지, 빈 문자열로 로드할지 지정할 수 있습니다.
    + 데이터 세트에 대한 유럽 스타일의 부동 처리를 추가했습니다.
    + 데이터 세트 탑재 오류에 대한 오류 메시지가 개선되었습니다.
  + **azureml-datadrift**
    + SDK의 데이터 드리프트 결과 쿼리에는 최솟값, 최댓값 및 평균 기능 메트릭을 구분하지 않아 중복 값이 발생하는 버그가 있었습니다. 메트릭 이름에 대상 또는 기준을 접두사로 사용하여 이 버그를 수정했습니다. 이전: 중복된 min, max, mean. 이후: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + 데이터 배달에 필요한 .NET 종속성을 보장할 때 쓰기 제한 Python 환경의 처리를 개선합니다.
    + 선행 빈 레코드가 있는 파일에서 데이터 흐름 만들기가 수정되었습니다.
    + `to_partition_iterator`에 `to_pandas_dataframe`과 유사한 오류 처리 옵션이 추가되었습니다.
  + **azureml-interpret**
    + Windows 제한을 초과할 가능성을 줄이기 위해 설명 경로 길이 제한을 줄였습니다.
    + 선형 서로게이트 모델을 사용하는 모방 설명자를 사용하여 만든 스파스 설명에 대한 버그 수정.
  + **azureml-opendatasets**
    + MNIST의 열이 int여야 하는데 문자열로 구문 분석되는 문제를 해결합니다.
  + **azureml-pipeline-core**
    + ModuleStep에 포함된 모듈을 사용할 때 regenerate_outputs 옵션 허용.
  + **azureml-train-automl-client**
    + AutoML에 TensorFlow 모델이 사용되지 않습니다.
    + 사용자가 로컬 모드에서 지원되지 않는 알고리즘을 나열할 수 있는 문제 수정
    + AutoMLConfig에 대한 문서 수정.
    + AutoMLConfig에서 cv_split_indices 입력에 대한 데이터 형식 검사 적용.
    + show_output에서 AutoML 실행이 실패하는 문제 해결
  + **azureml-train-automl-runtime**
    + 모델 다운로드 시간 초과가 성공적으로 발생하지 않도록 하는 Ensemble 반복의 버그 수정.
  + **azureml-train-core**
    + azureml.train.dnn.Nccl 클래스의 오타 수정.
    + PyTorch 예측 도구에서 PyTorch 버전 1.5 지원
    + 학습 프레임워크 예측 도구를 사용할 때 Azure Government 지역에서 프레임워크 이미지를 페치할 수 없는 문제 해결

  
## <a name="2020-05-04"></a>2020-05-04
**새 Notebook 환경**

이제 Azure Machine Learning 스튜디오 웹 환경 내에서 직접 기계 학습 Notebook 및 파일을 만들고, 편집하고, 공유할 수 있습니다. [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)에서 제공되는 모든 클래스 및 메서드를 이러한 Notebook 내에서 사용할 수 있습니다. [여기](./how-to-run-jupyter-notebooks.md)에서 시작하세요.

**새로 도입된 기능:**

+ VS Code에서 사용하는 편집기 개선(Monaco 편집기) 
+ UI/UX 개선 사항
+ 셀 도구 모음
+ 새 Notebook 도구 모음 및 컴퓨팅 컨트롤
+ Notebook 상태 표시줄 
+ 인라인 커널 전환
+ R 지원
+ 접근성 및 지역화 개선 사항
+ 명령 팔레트
+ 추가 바로 가기 키
+ 자동 저장
+ 성능 및 안정성 향상

스튜디오에서 다음 웹 기반 제작 도구에 액세스합니다.
    
| 웹 기반 도구  |     설명  |
|---|---|
| Azure ML Studio Notebook   |     동급 최초의 Notebook 파일 제작 및 Azure ML Python SDK에서 사용할 수 있는 모든 작업을 지원합니다. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Python용 Azure Machine Learning SDK v1.4.0

+ **새로운 기능**
  + 이제 AmlCompute 클러스터를 프로비저닝할 때 클러스터에 관리 ID를 설정할 수 있습니다. 시스템 할당 ID를 사용할지, 아니면 사용자 할당 ID를 사용할지 지정하고 후자의 경우 identityId를 전달합니다. 그런 다음, 현재 AmlCompute에서 사용하는 토큰 기반 접근법 대신 컴퓨팅 ID를 사용하여 데이터에 안전하게 액세스하는 방식으로 스토리지나 ACR과 같은 다양한 리소스에 액세스할 수 있는 권한을 설정할 수 있습니다. 매개 변수에 대한 자세한 내용은 SDK 참조를 확인하세요.
  

+ **주요 변경 내용**
  + AmlCompute 클러스터는 실행 기반 만들기에 대한 미리 보기 기능을 지원했습니다. 이 기능은 2주 후 사용이 중단될 예정입니다. Amlcompute 클래스를 사용하여 평소처럼 영구적인 컴퓨팅 대상을 계속 만들 수 있지만 "amlcompute" 식별자를 실행 구성의 컴퓨팅 대상으로 지정하는 구체적인 접근법은 조만간 지원되지 않을 예정입니다. 

+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + 열의 고유 값 수를 계산할 때 해시할 수 없는 형식에 대한 지원이 구현되었습니다.
  + **azureml-core**
    + TabularDataset를 사용하여 Azure Blob Storage에서 읽을 때의 안정성이 향상되었습니다.
    + `Datastore.register_azure_blob_store`의 `grant_workspace_msi` 매개 변수에 대한 설명서가 개선되었습니다.
    + `/` 또는 `\`로 끝나는 `src_dir` 인수를 지원하도록 `datastore.upload` 관련 버그가 수정되었습니다.
    + 액세스 키 또는 SAS 토큰이 없는 Azure Blob Storage 데이터 저장소에 업로드하려고 할 때 표시되는 실행 가능한 오류 메시지가 추가되었습니다.
  + **azureml-interpret**
    + 업로드된 설명에서 시각화 데이터의 파일 크기에 상한이 추가되었습니다.
  + **azureml-train-automl-client**
    + AutoMLConfig에 대한 label_column_name 및 weight_column_name 매개 변수가 문자열 형식인지 명시적으로 확인.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep은 이제 데이터 세트를 파이프라인 매개 변수로 지원합니다. 사용자는 샘플 데이터 세트를 통해 파이프라인을 생성할 수 있으며, 동일한 형식(파일 또는 테이블 형식)의 입력 데이터 세트를 변경하여 새 파이프라인 실행에 사용할 수 있습니다.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Python용 Azure Machine Learning SDK v1.3.0

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 학습 후 작업에 대한 추가 원격 분석이 추가되었습니다.
    + 100보다 긴 일련의 길이에 대해 CSS(조건부 제곱합) 학습을 사용하여 자동 ARIMA 학습 속도를 높입니다. 사용된 길이는 /src/azureml-automl-core/azureml/automl/core/shared/constants.py의 TimeSeriesInternal 클래스에 상수 ARIMA_TRIGGER_CSS_TRAINING_LENGTH로 저장됩니다.
    + 예측 실행의 사용자 로깅이 향상되었습니다. 이제 현재 실행 중인 단계에 대한 자세한 정보가 로그에 표시됩니다.
    + target_rolling_window_size를 2보다 작은 값으로 설정할 수 없습니다.
  + **azureml-automl-runtime**
    + 중복된 타임스탬프가 발견될 때 표시되는 오류 메시지가 개선되었습니다.
    + target_rolling_window_size를 2보다 작은 값으로 설정할 수 없습니다.
    + 구간 대체 오류를 수정했습니다. 이 문제는 계절별로 계열을 분해하는 데 필요한 관찰 수가 부족하기 때문에 발생했습니다. "계절성이 제거된" 데이터는 구간 길이를 확인하기 위해 PACF(Partial Autocorrelation Function)를 계산하는 데 사용됩니다.
    + 기능화 구성별 예측 작업에 열 용도 기능화 사용자 지정이 지원됩니다. 이제 예측 작업의 열 용도로 숫자 및 범주가 지원됩니다.
    + 기능화 구성별 예측 작업에 삭제 열 기능화 사용자 지정이 지원됩니다.
    + 기능화 구성별 예측 작업에 대체 사용자 지정이 지원됩니다. 대상 열의 상수 값 대체와 학습 데이터의 mean, median, most_frequent 및 constant 값 대체가 지원됩니다.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunConfig에 문자열 계산 이름 전달 허용
  + **azureml-core**
    +  Environment 개체의 복사본을 만드는 Environment.clone(new_name) API 추가
    +  Environment.docker.base_dockerfile이 filepath를 허용합니다. 파일을 확인할 수 있는 경우 base_dockerfile 환경 속성에서 콘텐츠를 읽어들입니다.
    + 사용자가 Environment.docker에서 수동으로 값을 설정하는 경우 base_image 및 base_dockerfile에 상호 배타적인 값을 자동으로 다시 설정
    + 환경이 사용자에 의해 관리되는지, AzureML에 의해 관리되는지를 나타내는 user_managed 플래그를 RSection에 추가했습니다.
    + 데이터 세트: 데이터 경로에 유니코드 문자가 포함된 경우에 발생하는 데이터 세트 다운로드 오류가 해결되었습니다.
    + 데이터 세트: 데이터 세트 탑재 캐싱 메커니즘이 Azure Machine Learning Compute의 최소 디스크 공간 요구 사항을 준수하도록 개선되어 노드를 사용할 수 없게 되고 작업이 취소되는 문제가 방지됩니다.
    + 데이터 세트: 사용자가 시계열 데이터 세트에 액세스할 때 시계열 열에 대한 인덱스가 pandas 데이터 프레임으로 추가되며, 이는 시계열 기반 데이터 액세스의 속도를 향상시키는 데 사용됩니다.  이전에는 인덱스에 타임스탬프 열과 동일한 이름이 지정되었으며, 이로 인해 사용자에게 실제 타임스탬프 열과 인덱스에 대한 혼동을 주었습니다. 이제는 인덱스를 열로 사용할 수 없으므로 인덱스에 특정 이름이 지정되지 않습니다. 
    + 데이터 세트: 소버린 클라우드에서 데이터 세트 인증 문제를 수정했습니다.
    + 데이터 세트: Azure PostgreSQL 데이터 저장소에서 만든 데이터 세트에 대한 `Dataset.to_spark_dataframe` 오류를 수정했습니다.
  + **azureml-interpret**
    + 로컬 중요도 값이 스파스인 경우 시각화에 글로벌 점수 추가
    + azureml-interpret가 interpret-community 0.9를 사용하도록 업데이트되었습니다.*
    + 스파스 평가 데이터가 있는 설명을 다운로드할 때 발생하는 문제 해결
    + AutoML에서 설명 개체의 스파스 형식에 대한 지원이 추가됨
  + **azureml-pipeline-core**
    + 파이프라인에서 ComputeInstance를 컴퓨팅 대상으로 지원
  + **azureml-train-automl-client**
    + 학습 후 작업에 대한 추가 원격 분석이 추가되었습니다.
    + 조기 중지 시 회귀 수정
    + azureml.dprep.Dataflow가 입력 데이터에 유효한 형식으로 사용되지 않습니다.
    +  기본 AutoML 실험 시간 제한을 6일로 변경.
  + **azureml-train-automl-runtime**
    + 학습 후 작업에 대한 추가 원격 분석이 추가되었습니다.
    + 스파스 AutoML 엔드투엔드 지원 추가
  + **azureml-opendatasets**
    + 서비스 모니터에 대한 추가 원격 분석이 추가되었습니다.
    + 안정성 향상을 위해 Blob에 Front Door 지원 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Python용 Azure Machine Learning SDK v1.2.0

+ **주요 변경 내용**
  + Python 2.7 지원 중단

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI의 `az ml model/computetarget/service` 명령에 "--subscription-id" 추가
    + ACI 배포를 위한 CMK(고객 관리형 키) vault_url, key_name 및 key_version 전달에 대한 지원 추가
  + **azureml-automl-core** 
    + X 및 y 데이터 예측 작업 모두에 상수 값을 사용한 사용자 지정 대체가 지원됩니다.
    + 사용자에게 오류 메시지를 표시할 때 발생하는 문제를 해결했습니다.    
  + **azureml-automl-runtime**
    + 행이 하나만 있는 지역이 포함된 데이터 세트에 대한 예측과 관련된 문제 해결
    + 예측 작업에 필요한 메모리 양이 감소했습니다.
    + 시간 열에 잘못된 형식이 있는 경우에 표시되는, 향상된 오류 메시지가 추가되었습니다.
    + X 및 y 데이터 예측 작업 모두에 상수 값을 사용한 사용자 지정 대체가 지원됩니다.
  + **azureml-core**
    + AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID 및 AZUREML_SERVICE_PRINCIPAL_PASSWORD 환경 변수에서 ServicePrincipal을 로드하도록 지원하는 기능 추가
    + `Dataset.Tabular.from_delimited_files`에 새 매개 변수 `support_multi_line` 도입: 기본적으로(`support_multi_line=False`) 따옴표로 묶인 필드 값의 줄 바꿈을 포함한 모든 줄 바꿈은 레코드 중단으로 해석됩니다. 이러한 방식으로 데이터를 읽으면 더 빨리 읽을 수 있으며, 여러 CPU 코어에서 병렬 실행에 더욱 최적화됩니다. 그러나 이로 인해 잘못 정렬된 필드 값이 있는 레코드가 더 많이 자동으로 생성될 수 있습니다. 구분 기호로 분리된 파일이 따옴표로 묶인 줄 바꿈을 포함하는 것으로 알려진 경우 이를 `True`로 설정해야 합니다.
    + Azure Machine Learning CLI에 ADLS Gen2를 등록하는 기능이 추가됨
    + 매개 변수의 사용법을 보다 잘 반영하기 위해 TabularDataset의 with_timestamp_columns() 메서드에서 'fine_grain_timestamp' 매개 변수 이름이 'timestamp'로, 'coarse_grain_timestamp' 매개 변수 이름이 'partition_timestamp'로 각각 변경되었습니다.
    + 최대 실험 이름 길이가 255자로 늘어났습니다.
  + **azureml-interpret**
    + azureml-interpret가 interpret-community 0.7로 업데이트되었습니다.*
  + **azureml-sdk**
    + 시험판 및 안정적인 릴리스에 대한 패치 지원에 호환되는 버전 Tilde를 사용하는 종속성으로 변경.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Python용 Azure Machine Learning SDK v1.1.5

+ **기능 사용 중단**
  + **Python 2.7**
    + Python 2.7을 지원하는 마지막 버전

+ **주요 변경 내용**
  + **유의적 버전 2.0.0**
    + Azure ML Python SDK 1.1 버전부터 의미 체계 버전 관리 2.0.0이 도입되었습니다. [여기에서 자세히 알아보세요](https://semver.org/). 모든 후속 버전은 새 번호 매기기 체계 및 의미 체계 버전 관리 계약을 따릅니다. 

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 일관성을 위해 엔드포인트 CLI 명령 이름을 'az ml endpoint aks'에서 'az ml endpoint real time'으로 변경합니다.
    + 안정적이고 실험적인 분기 CLI에 대한 CLI 설치 지침 업데이트
    + 단일 인스턴스 프로파일링이 권장 사항을 생성하도록 수정되었으며 핵심 sdk에서 제공됩니다.
  + **azureml-automl-core**
    + AutoML ONNX 모델에 일괄 처리 모드 유추(여러 행을 한 번 사용)를 사용하도록 지원
    + 데이터 세트에 대한 빈도 검색, 데이터 부족 또는 불규칙한 데이터 포인트 포함 개선
    + 주요 빈도를 준수하지 않는 데이터 포인트를 제거하는 기능이 추가되었습니다.
    + 해당 열에 대체 옵션을 적용하는 옵션 목록을 가져오도록 생성자의 입력을 변경했습니다.
    + 오류 로깅이 개선되었습니다.
  + **azureml-automl-runtime**
    + 테스트 집합에 표시된 학습 집합에 조직이 없는 경우 throw되는 오류와 관련된 문제를 해결했습니다.
    + 예측 서비스에 대한 채점 중 y_query 요구 사항 제거
    + 데이터 세트에 긴 시간 간격이 있는 짧은 조직이 포함된 경우 예측과 관련하여 발생하는 문제를 해결했습니다.
    + 자동 최대 구간이 설정되어 있고 날짜 열에 문자열 형식의 날짜가 포함되어 있는 경우에 발생하는 문제를 해결했습니다. 날짜로 변환할 수 없는 경우에 대한 적절한 변환 및 오류 메시지가 추가되었습니다.
    + FileCacheStore에 대한 중간 데이터를 직렬화 및 역렬화하는 데 네이티브 NumPy 및 SciPy 사용(로컬 AutoML 실행에 사용)
    + 실패한 자식 실행이 실행 중 상태에서 멈출 수 있는 버그가 수정되었습니다.
    + 기능화 속도가 향상되었습니다.
    + 채점 중 빈도 검사를 수정했습니다. 이제 예측 작업에서 학습 및 테스트 집합 간에 엄격한 빈도 동등을 요구하지 않습니다.
    + 해당 열에 대체 옵션을 적용하는 옵션 목록을 가져오도록 생성자의 입력을 변경했습니다.
    + 구간 유형 선택과 관련된 오류가 해결되었습니다.
    + 단일 행에 지역이 있는 데이터 세트에서 발생하는 분류되지 않은 오류가 해결되었습니다.
    + 빈도 검색 속도 저하 문제를 해결했습니다.
    + 학습 실패의 실제 원인을 AttributeError로 대체하게 하는 AutoML 예외 처리의 버그를 수정합니다.
  + **azureml-cli-common**
    + 단일 인스턴스 프로파일링이 권장 사항을 생성하도록 수정되었으며 핵심 sdk에서 제공됩니다.
  + **azureml-contrib-mir**
    + MirWebservice 클래스에 액세스 토큰을 검색하는 기능을 추가합니다.
    + MirWebservice.run() 호출을 수행하는 동안 기본적으로 MirWebservice에 토큰 인증을 사용합니다. 호출이 실패할 경우에만 새로 고침됩니다.
    + 이제 Mir 웹 서비스 배포에 [Ds2v2, A2v2 및 F16] 대신 적절한 Sku [Standard_DS2_v2, Standard_F16, Standard_A2_v2]가 각각 필요합니다.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep에 선택적 매개 변수 side_inputs가 추가되었습니다. 이 매개 변수를 사용하여 컨테이너에 폴더를 탑재할 수 있습니다. 현재 지원되는 형식은 DataReference 및 PipelineData입니다.
    + 이제 파이프라인 매개 변수를 전달하여 ParallelRunConfig에 전달된 매개 변수를 덮어쓸 수 있습니다. 새 파이프라인 매개 변수 aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout이 지원됩니다(aml_node_count 및 aml_process_count_per_node는 이미 이전 릴리스에 포함됨).
  + **azureml-core**
    + 배포된 AzureML 웹 서비스는 이제 기본적으로 `INFO` 로깅으로 설정됩니다. 이는 배포된 서비스에 `AZUREML_LOG_LEVEL` 환경 변수를 설정하는 방식으로 제어할 수 있습니다.
    + Python sdk는 검색 서비스를 사용하여 'pipelines' 대신 'api' 엔드포인트를 사용합니다.
    + 모든 SDK 호출에서 새 경로로 바꿉니다.
    + ModelManagementService에 대한 호출 라우팅이 새 통합 구조로 변경되었습니다.
      + 작업 영역 업데이트 메서드를 공개적으로 사용할 수 있게 되었습니다.
      + 사용자가 이미지 빌드에 대한 컴퓨팅을 업데이트할 수 있도록 작업 영역 업데이트 메서드에 image_build_compute 매개 변수를 추가했습니다.
    + 이전 프로파일링 워크플로에 사용 중단 메시지를 추가했습니다. 프로파일링 cpu 및 메모리 제한을 수정했습니다.
    + R 작업을 실행하는 Environment의 일부로 RSection을 추가했습니다.
    + 데이터 세트의 원본에 액세스할 수 없거나 데이터가 포함되지 않은 경우 오류를 발생시키는 유효성 검사가 `Dataset.mount`에 추가되었습니다.
    + Azure Blob 컨테이너를 등록하기 위한 데이터 저장소 CLI의 추가 매개 변수로, VNet 뒤에 있는 Blob 컨테이너를 등록하는 데 사용할 수 있는 `--grant-workspace-msi-access`가 추가되었습니다.
    + 단일 인스턴스 프로파일링이 권장 사항을 생성하도록 수정되었으며 핵심 sdk에서 제공됩니다.
    + aks.py _deploy의 문제를 해결했습니다.
    + 자동 스토리지 오류를 방지하기 위해 업로드되는 모델의 무결성을 검사합니다.
    + 이제 사용자는 웹 서비스용 키를 다시 생성할 때 인증 키의 값을 지정할 수 있습니다.
    + 대문자를 데이터 세트의 입력 이름으로 사용할 수 없는 버그가 수정되었습니다.
  + **azureml-defaults**
    + `azureml-dataprep`가 이제 `azureml-defaults`의 일부로 설치됩니다. 더 이상 데이터 세트를 탑재하기 위해 컴퓨팅 대상에 데이터 준비[fuse]를 수동으로 설치할 필요가 없습니다.
  + **azureml-interpret**
    + azureml-interpret가 interpret-community 0.6으로 업데이트되었습니다.*
    + azureml-interpret가 interpret-community 0.5.0에 종속되도록 업데이트됨
    + azureml-interpret에 azureml-style 예외 추가
    + keras 모델에 대한 DeepScoringExplainer 직렬화 수정
  + **azureml.mlflow**
    + azureml.mlflow에 소버린 클라우드에 대한 지원 추가
  + **azureml-pipeline-core**
    + 파이프라인 일괄 처리 채점 Notebook이 이제 ParallelRunStep을 사용합니다.
    + 인수 목록을 변경해도 PythonScriptStep 결과가 잘못 재사용될 수 있는 버그가 수정되었습니다.
    + `PipelineOutputFileDataset`에서 parse_* 메서드를 호출할 때 열 형식을 설정하는 기능 추가
  + **azureml-pipeline-steps**
    + `AutoMLStep`을 `azureml-pipeline-steps` 패키지로 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.
    + 데이터 세트에 대한 문서 예제를 PythonScriptStep 입력으로 추가했습니다.
  + **azureml-tensorboard**
    + TensorFlow 2.0을 지원하도록 azureml-tensorboard 업데이트
    + 컴퓨팅 인스턴스에서 사용자 지정 TensorBoard 포트를 사용하는 경우 올바른 포트 번호 표시
  + **azureml-train-automl-client**
    + 특정 패키지가 원격 실행 시 잘못된 버전에 설치될 수 있는 문제를 해결했습니다.
    + 사용자 지정 기능화 구성을 필터링하는 FeaturizationConfig 재정의 문제를 수정했습니다.
  + **azureml-train-automl-runtime**
    + 원격 실행에서 빈도 감지와 관련된 문제를 해결했습니다.
    + `azureml-pipeline-steps` 패키지에서 `AutoMLStep`을 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.
  + **azureml-train-core**
    + PyTorch 예측 도구에서 PyTorch 버전 1.4 지원
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Python용 Azure Machine Learning SDK v1.1.2rc0(시험판)

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoML ONNX 모델에 일괄 처리 모드 유추(여러 행을 한 번 사용)를 사용하도록 지원
    + 데이터 세트에 대한 빈도 검색, 데이터 부족 또는 불규칙한 데이터 포인트 포함 개선
    + 주요 빈도를 준수하지 않는 데이터 포인트를 제거하는 기능이 추가되었습니다.
  + **azureml-automl-runtime**
    + 테스트 집합에 표시된 학습 집합에 조직이 없는 경우 throw되는 오류와 관련된 문제를 해결했습니다.
    + 예측 서비스에 대한 채점 중 y_query 요구 사항 제거
  + **azureml-contrib-mir**
    + MirWebservice 클래스에 액세스 토큰을 검색하는 기능을 추가합니다.
  + **azureml-core**
    + 배포된 AzureML 웹 서비스는 이제 기본적으로 `INFO` 로깅으로 설정됩니다. 이는 배포된 서비스에 `AZUREML_LOG_LEVEL` 환경 변수를 설정하는 방식으로 제어할 수 있습니다.
    + 작업 영역에 등록된 모든 데이터 세트를 반환하도록 `Dataset.get_all`에 대한 반복을 수정합니다.
    + 데이터 세트 만들기 API의 `path` 인수에 잘못된 형식이 전달되면 표시되는 오류 메시지를 개선합니다.
    + Python sdk는 검색 서비스를 사용하여 'pipelines' 대신 'api' 엔드포인트를 사용합니다.
    + 모든 SDK 호출에서 새 경로로 바꿉니다.
    + ModelManagementService에 대한 호출 라우팅이 새 통합 구조로 변경되었습니다.
      + 작업 영역 업데이트 메서드를 공개적으로 사용할 수 있게 되었습니다.
      + 사용자가 이미지 빌드에 대한 컴퓨팅을 업데이트할 수 있도록 작업 영역 업데이트 메서드에image_build_compute 매개 변수를 추가했습니다.
    +  이전 프로파일링 워크플로에 사용 중단 메시지를 추가했습니다. 프로파일링 cpu 및 메모리 제한을 수정했습니다.
  + **azureml-interpret**
    + azureml-interpret가 interpret-community 0.6으로 업데이트되었습니다.*
  + **azureml.mlflow**
    + azureml.mlflow에 소버린 클라우드에 대한 지원 추가
  + **azureml-pipeline-steps**
    + `AutoMLStep`이 `azureml-pipeline-steps package`로 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.
  + **azureml-train-automl-client**
    + 특정 패키지가 원격 실행 시 잘못된 버전에 설치될 수 있는 문제를 해결했습니다.
  + **azureml-train-automl-runtime**
    + 원격 실행에서 빈도 감지와 관련된 문제를 해결했습니다.
    + `AutoMLStep`이 `azureml-pipeline-steps package`로 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.
  + **azureml-train-core**
    + `AutoMLStep`이 `azureml-pipeline-steps package`로 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Python용 Azure Machine Learning SDK v1.1.1rc0(시험판)

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 단일 인스턴스 프로파일링이 권장 사항을 생성하도록 수정되었으며 핵심 sdk에서 제공됩니다.
  + **azureml-automl-core**
    + 오류 로깅이 개선되었습니다.
  + **azureml-automl-runtime**
    + 데이터 세트에 긴 시간 간격이 있는 짧은 조직이 포함된 경우 예측과 관련하여 발생하는 문제를 해결했습니다.
    + 자동 최대 구간이 설정되어 있고 날짜 열에 문자열 형식의 날짜가 포함되어 있는 경우에 발생하는 문제를 해결했습니다. 날짜로 변환할 수 없는 경우에 대한 적절한 변환 및 오류 메시지가 추가되었습니다.
    + FileCacheStore에 대한 중간 데이터를 직렬화 및 역렬화하는 데 네이티브 NumPy 및 SciPy 사용(로컬 AutoML 실행에 사용)
    + 실패한 자식 실행이 실행 중 상태에서 멈출 수 있는 버그가 수정되었습니다.
  + **azureml-cli-common**
    + 단일 인스턴스 프로파일링이 권장 사항을 생성하도록 수정되었으며 핵심 sdk에서 제공됩니다.
  + **azureml-core**
    + Azure Blob 컨테이너를 등록하기 위한 데이터 저장소 CLI의 추가 매개 변수로, VNet 뒤에 있는 Blob 컨테이너를 등록하는 데 사용할 수 있는 `--grant-workspace-msi-access`가 추가되었습니다.
    + 단일 인스턴스 프로파일링이 권장 사항을 생성하도록 수정되었으며 핵심 sdk에서 제공됩니다.
    + aks.py _deploy의 문제를 해결했습니다.
    + 자동 스토리지 오류를 방지하기 위해 업로드되는 모델의 무결성을 검사합니다.
  + **azureml-interpret**
    + azureml-interpret에 azureml-style 예외 추가
    + keras 모델에 대한 DeepScoringExplainer 직렬화 수정
  + **azureml-pipeline-core**
    + 파이프라인 일괄 처리 채점 Notebook이 이제 ParallelRunStep을 사용합니다.
  + **azureml-pipeline-steps**
    + `azureml-pipeline-steps` 패키지에서 `AutoMLStep`을 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep에 선택적 매개 변수 side_inputs가 추가되었습니다. 이 매개 변수를 사용하여 컨테이너에 폴더를 탑재할 수 있습니다. 현재 지원되는 형식은 DataReference 및 PipelineData입니다.
  + **azureml-tensorboard**
    + TensorFlow 2.0을 지원하도록 azureml-tensorboard 업데이트
  + **azureml-train-automl-client**
    + 사용자 지정 기능화 구성을 필터링하는 FeaturizationConfig 재정의 문제를 수정했습니다.
  + **azureml-train-automl-runtime**
    + `azureml-pipeline-steps` 패키지에서 `AutoMLStep`을 이동했습니다. `AutoMLStep` 내에서 `azureml-train-automl-runtime`이 사용되지 않습니다.
  + **azureml-train-core**
    + PyTorch 예측 도구에서 PyTorch 버전 1.4 지원
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Python용 Azure Machine Learning SDK v1.1.0rc0(시험판)

+ **주요 변경 내용**
  + **유의적 버전 2.0.0**
    + Azure ML Python SDK 1.1 버전부터 의미 체계 버전 관리 2.0.0이 도입되었습니다. [여기에서 자세히 알아보세요](https://semver.org/). 모든 후속 버전은 새 번호 매기기 체계 및 의미 체계 버전 관리 계약을 따릅니다. 
  
+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + 기능화 속도가 향상되었습니다.
    + 채점 중 빈도 검사를 수정했습니다. 이제 예측 작업에서 학습 및 테스트 집합 간에 엄격한 빈도 동등을 요구하지 않습니다.
  + **azureml-core**
    + 이제 사용자는 웹 서비스용 키를 다시 생성할 때 인증 키의 값을 지정할 수 있습니다.
  + **azureml-interpret**
    + azureml-interpret가 interpret-community 0.5.0에 종속되도록 업데이트됨
  + **azureml-pipeline-core**
    + 인수 목록을 변경해도 PythonScriptStep 결과가 잘못 재사용될 수 있는 버그가 수정되었습니다.
  + **azureml-pipeline-steps**
    + 데이터 세트에 대한 문서 예제를 PythonScriptStep 입력으로 추가했습니다.
  + **azureml-contrib-pipeline-steps**
    + 이제 파이프라인 매개 변수를 전달하여 ParallelRunConfig에 전달된 매개 변수를 덮어쓸 수 있습니다. 새 파이프라인 매개 변수 aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout이 지원됩니다(aml_node_count 및 aml_process_count_per_node는 이미 이전 릴리스에 포함됨).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Python용 Azure Machine Learning SDK v1.0.85

+ **새로운 기능**
  + **azureml-core**
    + 지정된 작업 영역 및 구독에서 AmlCompute 리소스에 대한 현재 코어 사용량 및 할당량 제한을 가져옵니다.
  
  + **azureml-contrib-pipeline-steps**
    + 사용자가 이전 단계의 중간 결과인 표 형식 데이터 세트를 parallelrunstep으로 전달할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + 배포된 예측 서비스에 대한 요청에서 y_query 열의 요구 사항이 제거되었습니다. 
    + Dominick's Orange Juice Notebook 서비스 요청 섹션에서 'y_query'가 제거되었습니다.
    + 배포된 모델에 대한 예측을 방지하고 날짜 시간 열이 있는 데이터 세트에서 작동하는 버그를 수정했습니다.
    + 매튜 상관관계 계수를 이진 및 다중 클래스 분류에 대한 분류 메트릭으로 추가했습니다.
  + **azureml-contrib-interpret**
    + 텍스트 설명이 곧 출시될 interpret-text 리포지토리로 이동되었으므로 azureml-contrib-interpret에서 텍스트 설명자가 제거되었습니다.
  + **azureml-core**
    + 데이터 세트: 파일 데이터 세트 사용이 더 이상 python env에 설치되는 numpy 및 pandas에 종속되지 않습니다.
    + 상태 엔드포인트에 대한 ping을 시도하기 전에 로컬 Docker 컨테이너의 상태를 확인하는 LocalWebservice.wait_for_deployment()가 변경되었으므로 실패한 배포를 보고하는 데 걸리는 시간이 크게 줄어듭니다.
    + LocalWebservice() 생성자를 사용하여 기존 배포에서 서비스 개체를 만들 때 LocalWebservice.reload()에서 사용되는 내부 속성의 초기화가 수정되었습니다.
    + 설명에 대한 오류 메시지를 편집했습니다.
    + 액세스 토큰, 타임스탬프 이후 새로 고침, 타임 스탬프 만료 및 토큰 형식을 포함하는 AksServiceAccessToken 개체를 반환하는 get_access_token()이라는 새 메서드가 AksWebservice에 추가되었습니다. 
    + 이 메서드가 반환하는 모든 정보를 새 메서드가 반환하므로 AksWebservice에서 기존 get_token() 메서드가 사용되지 않습니다.
    + az ml service get-access-token 명령의 출력을 수정했습니다. 토큰 이름이 accessToken으로 바뀌고 refreshBy 이름이 refreshAfter로 바뀌었습니다. expiryOn 및 tokenType 속성을 추가했습니다.
    + get_active_runs가 수정되었습니다.
  + **azureml-explain-model**
    + shap를 0.33.0으로 업데이트하고 interpret-community를 0.4로 업데이트했습니다.*
  + **azureml-interpret**
    + shap를 0.33.0으로 업데이트하고 interpret-community를 0.4로 업데이트했습니다.*
  + **azureml-train-automl-runtime**
    + 매튜 상관관계 계수를 이진 및 다중 클래스 분류에 대한 분류 메트릭으로 추가했습니다.
    + 코드의 전처리 플래그가 사용되지 않고 기능화로 대체됩니다. 기능화는 기본적으로 설정됩니다.

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Python용 Azure Machine Learning SDK v1.0.83

+ **새로운 기능**
  + 데이터 세트: 데이터에 오류 값이 포함된 경우 `None`으로 채우지 않고 `to_pandas_dataframe`이 실패하도록 두 옵션 `on_error` 및 `out_of_range_datetime`을 추가합니다.
  + 작업 영역: 작업 영역에서 추가 암호화를 사용하고 고급 진단을 사용하지 않도록 설정하는 중요한 데이터가 포함된 작업 영역에 대한 `hbi_workspace` 플래그가 추가되었습니다. 또한 작업 영역을 만들 때 `cmk_keyvault` 및 `resource_cmk_uri` 매개 변수를 지정하여 연결된 Cosmos DB 인스턴스에 대한 고유한 키를 가져오는 지원을 추가했습니다. 그러면 작업 영역을 프로비저닝하는 동안 구독에 Cosmos DB 인스턴스가 만들어집니다. [여기서 자세히 알아보세요.](./concept-data-encryption.md#azure-cosmos-db)

+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + Python 3.5.4 미만 버전에서 AutoML을 실행할 때 TypeError를 유발하는 회귀가 수정되었습니다.
  + **azureml-core**
    + `datastore.upload_files`에서 상대 경로였지만 `./`로 시작되지 않아 사용할 수 없는 버그가 수정되었습니다.
    + 모든 Image 클래스 코드 경로에 대한 사용 중단 메시지가 추가되었습니다.
    + Azure 중국 21Vianet 지역에 대한 모델 관리 URL 구성이 수정되었습니다.
    + source_dir을 사용하는 모델을 Azure Functions에 패키징할 수 없는 문제가 해결되었습니다.    
    + AzureML 작업 영역 컨테이너 레지스트리에 이미지를 밀어넣는 [Environment.build_local()](/python/api/azureml-core/azureml.core.environment.environment) 옵션이 추가되었습니다.
    + Azure synapse에서 이전 버전과 호환되는 방식으로 새 토큰 라이브러리를 사용하도록 SDK를 업데이트했습니다.
  + **azureml-interpret**
    + 다운로드할 수 있는 설명이 없을 때 None이 반환되는 버그가 수정되었습니다. 이제 예외가 발생합니다(다른 경우에는 일치하는 동작).
  + **azureml-pipeline-steps**
    + `EstimatorStep`에서 `Estimator`가 사용될 때 `Estimator`의 `inputs` 매개 변수에 `DatasetConsumptionConfig`를 전달할 수 없습니다.
  + **azureml-sdk**
    + azureml-sdk 패키지에 AutoML 클라이언트가 추가되어 전체 AutoML 패키지를 설치하지 않고도 원격 AutoML 실행을 제출할 수 있습니다.
  + **azureml-train-automl-client**
    + AutoML 실행에 대한 콘솔 출력의 맞춤이 수정되었습니다.
    + 잘못된 버전의 pandas가 원격 amlcompute에 설치될 수 있는 버그가 수정되었습니다.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Python용 Azure Machine Learning SDK v1.0.81

+ **버그 수정 및 개선 사항**
  + **azureml-contrib-interpret**
    + azureml-interpret에서 interpret-community로 shap 종속성 지연
  + **azureml-core**
    + 이제 컴퓨팅 대상을 해당 배포 구성 개체에 대한 매개 변수로 지정할 수 있습니다. 이는 구체적으로 SDK 개체가 아니라 배포할 컴퓨팅 대상의 이름입니다.
    + Model 및 Service 개체에 CreatedBy 정보가 추가되었습니다. through.created_by에 액세스할 수 있습니다.
    + Docker 컨테이너의 HTTP 포트를 올바르게 설정하지 않은 ContainerImage.run()을 수정했습니다.
    + `az ml dataset register` CLI 명령에서 `azureml-dataprep`가 선택 사항이 되었습니다.
    + `TabularDataset.to_pandas_dataframe`이 대체 읽기 권한자로 잘못 대체되고 경고를 출력하는 버그를 수정했습니다.
  + **azureml-explain-model**
    + azureml-interpret에서 interpret-community로 shap 종속성 지연
  + **azureml-pipeline-core**
    + 로컬 Notebook을 파이프라인의 한 단계로 실행하는 새 파이프라인 단계 `NotebookRunnerStep`이 추가되었습니다.
    + PublishedPipelines, Schedules 및 PipelineEndpoints에서 사용되지 않는 get_all 함수를 제거했습니다.
  + **azureml-train-automl-client**
    + data_script가 더 이상 AutoML에 대한 입력으로 사용되지 않습니다.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Python용 Azure Machine Learning SDK v1.0.79

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 로그되는 featurizationConfig를 제거했습니다.
      + "auto"/"off"/"customized"만 로그하도록 로깅을 업데이트했습니다.
  + **azureml-automl-runtime**
    + pandas 지원이 추가되었습니다. 계열 및 pandas. 열 데이터 형식을 검색하는 데 사용되는 범주입니다. 이전에는 numpy.ndarray만 지원되었습니다.
      + 범주 dtype을 올바르게 처리하도록 관련 코드 변경 내용이 추가되었습니다.
    + 예측 함수 인터페이스가 개선되었습니다. y_pred 매개 변수가 선택 사항이 되었습니다. -docstring이 개선되었습니다.
  + **azureml-contrib-dataset**
    + 레이블이 지정된 데이터 세트를 탑재할 수 없는 버그를 수정했습니다.
  + **azureml-core**
    + `Environment.from_existing_conda_environment(name, conda_environment_name)`에 대한 버그 수정. 사용자가 로컬 환경의 정확한 복제본인 Environment 인스턴스를 만들 수 있습니다.
    + 시계열 관련 Datasets 메서드가 기본적으로 `include_boundary=True`로 변경되었습니다.
  + **azureml-train-automl-client**
    + 출력 표시가 false로 설정된 경우 유효성 검사 결과가 인쇄되지 않는 문제가 해결되었습니다.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Python용 Azure Machine Learning SDK v1.0.76

+ **주요 변경 내용**
  + Azureml-Train-AutoML 업그레이드 문제
    + azureml-train-automl<1.0.76에서 azureml-train-automl>=1.0.76으로 업그레이드하면 부분 설치가 발생하여 일부 AutoML 가져오기가 실패할 수 있습니다. 이 문제를 해결하려면 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd 에 있는 설치 스크립트를 실행하면 됩니다. 또는 pip를 직접 사용하는 경우 다음을 수행할 수 있습니다.
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + 또는 업그레이드하기 전에 이전 버전을 제거할 수 있습니다.
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **버그 수정 및 개선 사항**
  + **azureml-automl-runtime**
    + 이제 AutoML은 이진 분류 작업에 대한 평균 스칼라 메트릭을 계산할 때 true 및 false 클래스를 모두 고려합니다.
    + AzureML-AutoML-Core의 기계 학습 및 학습 코드가 새 패키지 AzureML-AutoML-Runtime으로 이동했습니다.
  + **azureml-contrib-dataset**
    + 레이블이 지정된 데이터 세트에서 다운로드 옵션을 사용하여 `to_pandas_dataframe`을 호출할 때 이제 기존 파일을 덮어쓸지 여부를 지정할 수 있습니다.
    + 시계열, 레이블 또는 이미지 열이 삭제되도록 하는 `keep_columns` 또는 `drop_columns`를 호출하면 데이터 세트의 해당 기능도 삭제됩니다.
    + 개체 검색 작업에 대한 pytorch 로더 문제를 해결했습니다.
  + **azureml-contrib-interpret**
    + azureml-contrib-interpret에서 설명 대시보드 위젯이 제거되고, interpret_community의 새 항목을 참조하도록 패키지가 변경되었습니다.
    + interpret-community 버전이 0.2.0으로 업데이트됨
  + **azureml-core**
    + `workspace.datasets`의 성능을 개선합니다.
    + 사용자 이름 및 암호 인증을 사용하여 Azure SQL Database 데이터 저장소를 등록하는 기능이 추가되었습니다.
    + 상대 경로에서 RunConfigurations를 로드하는 문제를 수정합니다.
    + 시계열 열이 삭제되도록 하는 `keep_columns` 또는 `drop_columns`를 호출하면 데이터 세트의 해당 기능도 삭제됩니다.
  + **azureml-interpret**
    + interpret-community 버전이 0.2.0으로 업데이트됨
  + **azureml-pipeline-steps**
    + Azure 기계 학습 파이프라인 단계에서 `runconfig_pipeline_params`에 지원되는 값이 문서화되었습니다.
  + **azureml-pipeline-core**
    + 파이프라인 명령에 대한 json 형식의 출력을 다운로드하는 CLI 옵션이 추가되었습니다.
  + **azureml-train-automl**
    + AzureML-Train-AutoML을 AzureML-Train-AutoML-Client라는 클라이언트 패키지와 AzureML-Train-AutoML-Runtime이라는 ML 학습 패키지로 분할함
  + **azureml-train-automl-client**
    + 로컬에서 기계 학습 종속성을 설치할 필요 없이 AutoML 실험을 제출하기 위한 씬 클라이언트를 추가했습니다.
    + 원격 실행에서 자동으로 검색된 구간, 이동 기간 크기 및 최대 구간의 로깅이 수정되었습니다.
  + **azureml-train-automl-runtime**
    + 클라이언트에서 기계 학습 및 런타임 구성 요소를 격리하는 새 AutoML 패키지를 추가했습니다.
  + **azureml-contrib-train-rl**
    + SDK에 보충 학습 지원이 추가되었습니다.
    + RL SDK에 AmlWindowsCompute 지원이 추가되었습니다.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Python용 Azure Machine Learning SDK v1.0.74

  + **미리 보기 기능**
    + **azureml-contrib-dataset**
      + azureml-contrib-dataset를 가져온 후에는 `._Labeled` 대신 `Dataset.Labeled.from_json_lines`를 호출하여 레이블이 지정된 데이터 세트를 만들 수 있습니다.
      + 레이블이 지정된 데이터 세트에서 다운로드 옵션을 사용하여 `to_pandas_dataframe`을 호출할 때 이제 기존 파일을 덮어쓸지 여부를 지정할 수 있습니다.
      + 시계열, 레이블 또는 이미지 열이 삭제되도록 하는 `keep_columns` 또는 `drop_columns`를 호출하면 데이터 세트의 해당 기능도 삭제됩니다.
      + `dataset.to_torchvision()`을 호출할 때 PyTorch 로더와 관련된 문제가 해결되었습니다.

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 미리 보기 CLI에 모델 프로파일링을 추가했습니다.
    + Azure Storage에서 AzureML CLI가 실패하게 만드는 호환성이 손상되는 변경을 수정합니다.
    + AKS 형식에 대한 Load Balancer 형식을 MLC에 추가했습니다.
  + **azureml-automl-core**
    + 누락된 값과 여러 조직이 있는 시계열에 대한 최대 구간 검색 문제를 해결했습니다.
    + 교차 유효성 검사 분할을 생성하는 동안 오류를 유발하는 문제를 해결했습니다.
    + 이 섹션을 릴리스 정보에 표시되는 markdown 형식의 메시지로 교체: -예측 데이터 세트에서 더 짧은 조직의 처리가 개선되었습니다.
    + 로깅하는 동안 일부 사용자 정보의 마스킹과 관련된 문제를 해결했습니다. -예측 실행 중 오류 로깅이 향상되었습니다.
    + psutil을 자동 생성 yml 배포 파일에 conda 종속성으로 추가.
  + **azureml-contrib-mir**
    + Azure Storage에서 AzureML CLI가 실패하게 만드는 호환성이 손상되는 변경을 수정합니다.
  + **azureml-core**
    + Azure Functions에 배포된 모델이 500을 생성하는 버그를 수정합니다.
    + 스냅샷에 amlignore 파일이 적용되지 않는 문제를 해결했습니다.
    + 지정된 amlcompute에서 진행 중인 실행 및 대기 중인 실행에 대한 생성기를 반환하는 새 API amlcompute.get_active_runs가 추가되었습니다.
    + AKS 형식에 대한 Load Balancer 형식을 MLC에 추가했습니다.
    + run.py의 download_files 및 artifacts_client의 download_artifacts_from_prefix에 append_prefix bool 매개 변수가 추가되었습니다. 이 플래그는 output_directory에 파일 또는 폴더 이름만 추가되도록 원본 파일 경로를 선택적으로 평면화하는 데 사용됩니다.
    + `run_config.yml`의 데이터 세트 사용에 관한 역직렬화 문제를 해결합니다.
    + 시계열 열이 삭제되도록 하는 `keep_columns` 또는 `drop_columns`를 호출하면 데이터 세트의 해당 기능도 삭제됩니다.
  + **azureml-interpret**
    + interpret-community 버전이 0.1.0.3으로 업데이트됨
  + **azureml-train-automl**
    + automl_step이 유효성 검사 문제를 인쇄하지 못할 수 있는 문제를 해결했습니다.
    + 모델의 환경에 종속성이 누락된 경우에도 register_model이 로컬에서 성공하도록 수정되었습니다.
    + 일부 원격 실행에서 Docker가 사용되지 않는 문제를 해결했습니다.
    + 로컬 실행이 조기에 실패하게 만드는 예외의 로깅을 추가합니다.
  + **azureml-train-core**
    + 자동화된 하이퍼 매개 변수 튜닝의 최적 자식 실행 계산에서 resume_from 실행을 고려합니다.
  + **azureml-pipeline-core**
    + 파이프라인 인수 생성 시 매개 변수 처리가 수정되었습니다.
    + 파이프라인 설명 및 단계 유형 yaml 매개 변수가 추가되었습니다.
    + 파이프라인 단계의 새 yaml 형식과 이전 형식에 대한 사용 중단 경고가 추가되었습니다.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>웹 환경

[https://ml.azure.com](https://ml.azure.com)의 공동 작업 영역 방문 페이지가 향상되었으며 Azure Machine Learning 스튜디오로 다시 브랜딩되었습니다.

이 스튜디오에서 데이터 세트, 파이프라인, 모델, 엔드포인트 등의 Azure Machine Learning 자산을 학습, 테스트, 배포 및 관리할 수 있습니다.

스튜디오에서 다음 웹 기반 제작 도구에 액세스합니다.

| 웹 기반 도구 | 설명 | 
|-|-|-|
| Notebook VM(미리 보기) | 완전 관리형 클라우드 기반 워크스테이션 | 
| [자동화된 Machine Learning](tutorial-first-experiment-automated-ml.md)(미리 보기) | 코드 없는 환경으로 기계 학습 모델 개발 자동화 | 
| [Designer](concept-designer.md) | 끌어서 놓기 방식의 기계 학습 모델링 도구(이전 명칭: 시각적 인터페이스) | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning 디자이너 향상 기능

+ 이전 명칭: 시각적 인터페이스 
+    기능 엔지니어링, 교차 유효성 검사 및 데이터 변환 같은 추천자, 분류자 및 학습 유틸리티를 포함한 새로운 [모듈](algorithm-module-reference/module-reference.md) 11개.

### <a name="r-sdk"></a>R SDK 
 
데이터 과학자 및 AI 개발자는 [R용 Azure Machine Learning SDK](https://github.com/Azure/azureml-sdk-for-r)를 사용하여 Azure Machine Learning에서 기계 학습 워크플로를 빌드 및 실행합니다.

R용 Azure Machine Learning SDK는 `reticulate` 패키지를 사용하여 Python SDK에 바인딩합니다. R용 SDK를 사용하면 Python에 직접 바인딩하여 선택한 R 환경에서 Python SDK에 구현된 핵심 개체 및 메서드에 액세스할 수 있습니다.

SDK의 주요 기능에는 다음이 포함됩니다.

+    기계 학습 실험을 모니터링, 로깅 및 구성하는 클라우드 리소스를 관리합니다.
+    클라우드 리소스를 사용하여 모델을 학습시킵니다(GPU 가속 모델 학습 포함).
+    ACI(Azure Container Instances) 및 AKS(Azure Kubernetes Service)에 모델을 웹 서비스로 배포합니다.

전체 설명서는 [패키지 웹 사이트](https://azure.github.io/azureml-sdk-for-r)를 참조하세요.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Event Grid와 Azure Machine Learning 통합 

Azure Machine Learning은 현재 Event Grid의 리소스 공급자이며, Azure Portal 또는 Azure CLI를 통해 기계 학습 이벤트를 구성할 수 있습니다. 사용자는 검색된 실행 완료, 모델 등록, 모델 배포 및 데이터 드리프트에 대한 이벤트를 만들 수 있습니다. 이러한 이벤트는 소비를 위해 Event Grid에서 지원하는 이벤트 처리기로 라우팅될 수 있습니다. 자세한 내용은 기계 학습 이벤트 [스키마](../event-grid/event-schema-machine-learning.md) 및 [자습서](how-to-use-event-grid.md) 문서를 참조하세요.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Python용 Azure Machine Learning SDK v1.0.72

+ **새로운 기능**
  + [**azureml-datadrift**](/python/api/azureml-datadrift) 패키지를 통해 데이터 세트 모니터가 추가되어 시간별 데이터 드리프트 또는 기타 통계 변경에 대한 시계열 데이터 세트를 모니터링할 수 있습니다. 드리프트가 감지되거나 데이터에 대한 다른 조건이 충족되면 경고 및 이벤트가 트리거될 수 있습니다. 자세한 내용은 [설명서](how-to-monitor-datasets.md)를 참조하세요.
  + Azure Machine Learning에서 두 개의 새로운 버전(SKU라고도 함) 발표. 이 릴리스에서는 이제 Basic 또는 Enterprise Azure Machine Learning 작업 영역을 만들 수 있습니다. 모든 기존 작업 영역은 기본적으로 Basic 버전으로 설정되며, Azure Portal 또는 스튜디오로 이동하여 언제든지 작업 영역을 업그레이드할 수 있습니다. Azure Portal에서 Basic 또는 Enterprise 작업 영역을 만들 수 있습니다. 자세히 알아보려면 [설명서](./how-to-manage-workspace.md)를 참조하세요. SDK에서 작업 영역의 버전은 작업 영역 개체의 "sku" 속성을 사용하여 확인할 수 있습니다.
  + 또한 Azure Machine Learning 컴퓨팅이 향상되었습니다. 이제 Azure Monitor에서 디버깅을 위해 진단 로그를 보는 것 외에도 클러스터에 대한 메트릭(예: 총 노드, 실행 중인 노드, 총 코어 할당량)을 볼 수 있습니다. 또한 클러스터에서 현재 실행 중이거나 큐에 대기 중인 실행과 클러스터에 있는 다양한 노드의 IP와 같은 세부 정보를 볼 수도 있습니다. 이러한 정보는 포털에서 또는 SDK 또는 CLI에서 해당 함수를 사용하여 볼 수 있습니다.

  + **미리 보기 기능**
    + Azure Machine Learning Compute에서 로컬 SSD의 디스크 암호화에 대한 미리 보기 지원을 릴리스하고 있습니다. 이 기능을 사용하기 위해 구독이 나열되는 것을 허용하려면 기술 지원 티켓을 제출하세요.
    + Azure Machine Learning Batch Inference의 공개 미리 보기입니다. Azure Machine Learning Batch Inference는 시간에 민감하지 않은 대규모 유추 작업을 대상으로 합니다. Batch Inference는 비동기 애플리케이션에 대한 최상의 처리량으로 비용 효율적인 유추 컴퓨팅 크기 조정을 제공합니다. 대규모 데이터 컬렉션을 위한 높은 처리량, 발사 후 망각형 유추에 최적화되어 있습니다.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + 레이블이 지정된 데이터 세트에 대한 기능 지원
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI는 이제 모델 패키징을 지원합니다.
    + 데이터 세트 CLI가 추가되었습니다. 자세한 내용은 `az ml dataset --help`를 참조하세요.
    + InferenceConfig 인스턴스 없이 지원되는 모델(ONNX, scikit 및 TensorFlow)을 배포하고 패키징하도록 지원하는 기능이 추가되었습니다.
    + SDK 및 CLI에서 서비스 배포(ACI 및 AKS)에 대한 덮어쓰기 플래그가 추가되었습니다. 제공되는 경우 이름을 가진 서비스가 이미 있으면 기존 서비스를 덮어씁니다. 서비스가 없는 경우에는 새 서비스를 만듭니다.
    + Onnx 및 TensorFlow라는 두 가지 새로운 프레임워크에 모델을 등록할 수 있습니다. - 모델 등록은 샘플 입력 데이터, 샘플 출력 데이터 및 모델에 대한 리소스 구성을 허용합니다.
  + **azureml-automl-core**
    + 반복 학습은 런타임 제약 조건이 설정되는 경우에만 자식 프로세스에서 실행됩니다.
    + 지정된 max_horizon이 지정된 컴퓨터에서 메모리 문제를 발생시키는지 여부를 확인하기 위해 예측 작업에 대한 가드 레일을 추가했습니다. 문제를 발생시키는 경우 가드 레일 메시지가 표시됩니다.
    + 2년 및 1개월과 같은 복잡한 빈도에 대한 지원이 추가되었습니다. -빈도를 확인할 수 없는 경우에 표시되는 자세한 오류 메시지가 추가되었습니다.
    + 모델 배포 오류를 해결하기 위해 자동 생성되는 conda env에 azureml-defaults 추가
    + Azure Machine Learning 파이프라인의 중간 데이터를 테이블 형식의 데이터 세트로 변환하고 `AutoMLStep`에서 사용할 수 있습니다.
    + 스트리밍에 대한 열 용도 업데이트를 구현했습니다.
    + 스트리밍에 대한 Imputer 및 HashOneHotEncoder의 변환기 매개 변수 업데이트를 구현했습니다.
    + 유효성 검사 오류 메시지에 현재 데이터 크기 및 필요한 최소 데이터 크기를 추가했습니다.
    + 각 유효성 검사 단계에서 최소 두 개의 샘플을 보장하기 위해 교차 유효성 검사에 필요한 최소 데이터 크기를 업데이트했습니다.
  + **azureml-cli-common**
    + CLI는 이제 모델 패키징을 지원합니다.
    + Onnx 및 TensorFlow라는 두 가지 새로운 프레임워크에 모델을 등록할 수 있습니다.
    + 모델 등록은 샘플 입력 데이터, 샘플 출력 데이터 및 모델에 대한 리소스 구성을 허용합니다.
  + **azureml-contrib-gbdt**
    + Notebook에 대한 릴리스 채널을 수정했습니다.
    + 지원되지 않는 비 AmlCompute 컴퓨팅 대상에 대한 경고가 추가되었습니다.
    + azureml-contrib-gbdt 패키지에 LightGMB 예측 도구가 추가되었습니다.
  + [**azureml-core**](/python/api/azureml-core)
    + CLI는 이제 모델 패키징을 지원합니다.
    + 사용되지 않은 데이터 세트 API에 대한 사용 중단 경고를 추가합니다. https://aka.ms/tabular-dataset 에서 데이터 세트 API 변경 알림을 참조하세요.
    + 데이터 세트가 등록된 경우 등록 이름 및 버전을 반환하도록 [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-)를 변경합니다.
    + 데이터 세트를 인수로 사용하는 ScriptRunConfig를 반복적으로 사용하여 실험 실행을 제출할 수 없는 버그를 수정합니다.
    + 실행 중에 검색된 데이터 세트는 추적되며 실행 세부 정보 페이지에서 또는 실행이 완료된 후 [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)를 호출하여 볼 수 있습니다.
    + Azure Machine Learning 파이프라인의 중간 데이터를 테이블 형식의 데이터 세트로 변환하고 [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)에서 사용할 수 있습니다.
    + InferenceConfig 인스턴스 없이 지원되는 모델(ONNX, scikit 및 TensorFlow)을 배포하고 패키징하도록 지원하는 기능이 추가되었습니다.
    + SDK 및 CLI에서 서비스 배포(ACI 및 AKS)에 대한 덮어쓰기 플래그가 추가되었습니다. 제공되는 경우 이름을 가진 서비스가 이미 있으면 기존 서비스를 덮어씁니다. 서비스가 없는 경우에는 새 서비스를 만듭니다.
    +  Onnx 및 TensorFlow라는 두 가지 새로운 프레임워크에 모델을 등록할 수 있습니다. 모델 등록은 샘플 입력 데이터, 샘플 출력 데이터 및 모델에 대한 리소스 구성을 허용합니다.
    + Azure Database for MySQL에 대한 새 데이터 저장소가 추가되었습니다. Azure Machine Learning 파이프라인의 DataTransferStep에서 Azure Database for MySQL을 사용하는 예제가 추가되었습니다.
    + 실험에서 태그를 추가하고 제거하는 기능이 추가되고, 실행에서 태그를 제거하는 기능이 추가되었습니다.
    + SDK 및 CLI에서 서비스 배포(ACI 및 AKS)에 대한 덮어쓰기 플래그가 추가되었습니다. 제공되는 경우 이름을 가진 서비스가 이미 있으면 기존 서비스를 덮어씁니다. 서비스가 없는 경우에는 새 서비스를 만듭니다.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + `azureml-contrib-datadrift`에서 `azureml-datadrift`로 이동
    + 드리프트 및 기타 통계 측정값에 대한 시계열 데이터 세트 모니터링을 지원하는 기능이 추가됨
    + [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) 클래스에 대한 새 메서드 `create_from_model()` 및 `create_from_dataset()`. `create()` 메서드는 사용되지 않습니다.
    + Python 및 Azure Machine Learning 스튜디오의 UI에서 시각화 조정.
    + 데이터 세트 모니터에 일별 모니터 일정은 물론, 주별, 월별 모니터 일정도 지원됩니다.
    + 데이터 세트 모니터의 기록 데이터를 분석하기 위해 데이터 모니터 메트릭의 백필이 지원됩니다.
    + 다양한 버그 수정
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + 파이프라인 `yaml` 파일에서 Azure Machine Learning 파이프라인 실행을 제출할 때 azureml-dataprep가 더 이상 필요하지 않습니다.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + 모델 배포 오류를 해결하기 위해 자동 생성되는 conda env에 azureml-defaults 추가
    + 이제 AutoML 원격 학습에 추론에 대한 학습 env를 다시 사용할 수 있도록 azureml-defaults가 포함됩니다.
  + **azureml-train-core**
    + [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) 예측 도구에 PyTorch 1.3 지원이 추가됨

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>시각적 인터페이스(미리 보기)

+ Azure Machine Learning 시각적 인터페이스(미리 보기)가 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)에서 실행하기 위해 재정비되었습니다. 시각적 인터페이스에서 제작된 파이프라인(이전에는 실험이라고 함)은 이제 핵심 Azure Machine Learning 환경과 완벽하게 통합됩니다.
  + SDK 자산과 통합된 관리 환경
  + 시각적 인터페이스 모델, 파이프라인 및 엔드포인트에 대한 버전 관리 및 추적
  + 새롭게 디자인된 UI
  + 일괄 처리 유추 배포가 추가됨
  + 유추 컴퓨팅 대상에 대한 AKS(Azure Kubernetes Service) 지원이 추가됨
  + 새로운 Python 단계 파이프라인 제작 워크플로
  + 시각적 제작 도구에 대한 새 [방문 페이지](https://ml.azure.com)

+ **새 모듈**
  + 수학 연산 적용
  + SQL 변환 적용
  + 값 잘라내기
  + 데이터 요약
  + SQL Database에서 가져오기

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Python용 Azure Machine Learning SDK v1.0.69

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + 모든 실행에 대한 설명을 계산하는 대신 모델 설명을 최적 실행으로 제한. 로컬, 원격 및 ADB에 대해 이 동작 변경.
    + UI에 대한 주문형 모델 설명 지원이 추가됨
    + psutil을 `automl`의 종속성으로 추가하고 psutil을 amlcompute에 conda 종속성으로 포함했습니다.
    + 선형 대수 오류를 일으킬 수 있는 몇 가지 계열의 예측 데이터 세트에 대한 추론 구간 및 이동 기간 크기 문제를 해결함
      + 예측 실행에서 추론적으로 결정된 매개 변수에 대한 출력이 추가되었습니다.
  + **azureml-contrib-datadrift**
    + 출력 메트릭을 만드는 동안 첫 번째 섹션에 데이터 세트 수준 드리프트가 없는 경우의 보호 기능이 추가되었습니다.
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model 패키지의 이름이 azureml-contrib-interpret로 변경됨
  + **azureml-core**
    + 데이터 세트를 등록 취소하는 API가 추가되었습니다. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model 패키지의 이름이 azureml-contrib-interpret로 변경되었습니다.
  + **[azureml-core](/python/api/azureml-core)**
    + 데이터 세트를 등록 취소하는 API가 추가되었습니다. dataset.[unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + 데이터 변경 시간을 확인하는 데이터 세트 API가 추가되었습니다. `dataset.data_changed_time`.
    + Azure Machine Learning 파이프라인에서 `FileDataset` 및 `TabularDataset`를 `PythonScriptStep`, `EstimatorStep` 및 `HyperDriveStep`에 대한 입력으로 사용할 수 있습니다.
    + 파일 수가 많은 폴더의 `FileDataset.mount` 성능이 향상됨
    + [FileDataset](/python/api/azureml-core/azureml.data.filedataset) 및 [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset)를 Azure Machine Learning 파이프라인의 [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) 및 [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep)에 대한 입력으로 사용할 수 있습니다.
    + 파일 수가 많은 폴더의 FileDataset.[mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) 성능이 향상됨
    + 실행 세부 정보에 알려진 오류 권장 사항에 대한 URL이 추가되었습니다.
    + 실행에 너무 많은 자식이 있는 경우 요청이 실패하는 run.get_metrics의 버그가 수정되었습니다.
    + 실행에 너무 많은 자식이 있는 경우 요청이 실패하는 [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-)의 버그가 수정되었습니다.
    + Arcadia 클러스터에서 인증에 대한 지원이 추가되었습니다.
    + Experiment 개체가 생성되면 실행 기록 추적을 위해 Azure Machine Learning 작업 영역에서 실험을 가져오거나 만듭니다. 실험 ID 및 보관된 시간은 생성 시 Experiment 개체에 채워집니다. 예: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() and reactivate()는 UX에 표시되거나 기본적으로 실험을 나열하는 호출에서 반환되지 않게 실험을 숨기고 숨긴 실험을 복원하는 데 호출할 수 있는 함수입니다. 보관된 실험과 동일한 이름으로 새 실험을 만든 경우 새 이름을 전달하여 다시 활성화할 때 보관된 실험의 이름을 바꿀 수 있습니다. 지정된 이름을 가진 활성 실험은 하나뿐이어야 합니다. 예: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Create new active experiment with the same name as the archived. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") Experiment에 대한 정적 메서드 list()는 이름 필터와 ViewType 필터를 사용할 수 있습니다. ViewType 값은 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL"입니다. 예: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + 모델 배포 및 서비스 업데이트에 환경 사용 지원
  + **azureml-datadrift**
    + DataDriftDector 클래스의 show 특성이 선택적 인수 'with_details'를 더 이상 지원하지 않습니다. show 특성은 기능 열의 데이터 드리프트 계수 및 데이터 드리프트 기여만 제공합니다.
    + DataDriftDetector 특성 'get_output' 동작 변경:
      + 입력 매개 변수 start_time, end_time은 필수가 아닌 선택 사항입니다.
      + 동일한 호출에서 특정 run_id가 있는 입력 관련 start_time 및/또는 end_time은 상호 배타적이므로 값 오류 예외가 발생합니다.
      + 입력 관련 start_time 및/또는 end_time을 사용하면 예약된 실행의 결과만 반환됩니다.
      + 'daily_latest_only' 매개 변수는 사용되지 않습니다.
    + 데이터 세트 기반 데이터 드리프트 출력 검색을 지원합니다.
  + **azureml-explain-model**
    + AzureML-explain-model 패키지의 이름을 AzureML-interpret로 변경하며, 현재 이전 패키지는 이전 버전과의 호환성을 위해 유지됩니다.
    + ExplanationClient에서 다운로드할 때 기본적으로 회귀 대신 분류 작업으로 설정된 원시 설명과 관련된 `automl` 버그 수정
    + `MimicWrapper`를 사용하여 직접 만들 수 있도록 `ScoringExplainer` 지원 추가
  + **azureml-pipeline-core**
    + 대규모 파이프라인 만들기의 성능 향상
  + **azureml-train-core**
    + TensorFlow 예측 도구에 TensorFlow 2.0 지원이 추가되었습니다.
  + **azureml-train-automl**
    + [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment) 개체가 생성되면 실행 기록 추적을 위해 Azure Machine Learning 작업 영역에서 실험을 가져오거나 만듭니다. 실험 ID 및 보관된 시간은 생성 시 Experiment 개체에 채워집니다. 예:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) 및 [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-)는 UX에 표시되거나 기본적으로 실험을 나열하는 호출에서 반환되지 않게 실험을 숨기고 숨긴 실험을 복원하는 데 호출할 수 있는 함수입니다. 보관된 실험과 동일한 이름으로 새 실험을 만든 경우 새 이름을 전달하여 다시 활성화할 때 보관된 실험의 이름을 바꿀 수 있습니다. 지정된 이름을 가진 활성 실험은 하나뿐이어야 합니다. 예:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Experiment의 정적 메서드 [list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-)는 이름 필터 및 ViewType 필터를 사용할 수 있습니다. ViewType 값은 "ACTIVE_ONLY", "ARCHIVED_ONLY" 및 "ALL"입니다. 예:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + 모델 배포 및 서비스 업데이트에 환경 사용 지원.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) 클래스의 show 특성이 선택적 인수 'with_details'를 더 이상 지원하지 않습니다. show 특성은 기능 열의 데이터 드리프트 계수 및 데이터 드리프트 기여만 제공합니다.
    + DataDriftDetector 함수 [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) 동작 변경:
      + 입력 매개 변수 start_time, end_time은 필수가 아닌 선택 사항입니다.
      + 동일한 호출에서 특정 run_id가 있는 입력 관련 start_time 및/또는 end_time은 상호 배타적이므로 값 오류 예외가 발생합니다.
      + 입력 관련 start_time 및/또는 end_time을 사용하면 예약된 실행의 결과만 반환됩니다.
      + 'daily_latest_only' 매개 변수는 사용되지 않습니다.
    + 데이터 세트 기반 데이터 드리프트 출력 검색을 지원합니다.
  + **azureml-explain-model**
    + MimicWrapper를 사용하여 [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer)를 직접 만들도록 지원하는 기능 추가
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + 대규모 파이프라인 만들기의 성능이 향상되었습니다.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) 예측 도구에 TensorFlow 2.0 지원이 추가되었습니다.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + 오케스트레이션이 이미 처리하고 있어서 설치 반복에 실패하면 부모 실행이 더 이상 실패하지 않습니다.
    + AutoML 실험에 대한 local-docker 및 local-conda 지원 추가
    + AutoML 실험에 대한 local-docker 및 local-conda 지원이 추가되었습니다.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역의 새로운 웹 환경(미리 보기)

데이터 과학자가 실험을 보다 효율적으로 모니터링할 수 있도록 [새 작업 영역 포털](https://ml.azure.com)의 실험 탭이 업데이트되었습니다. 다음 기능을 살펴볼 수 있습니다.
+ 실험 목록을 쉽게 필터링하고 정렬할 수 있게 해주는 실험 메타데이터
+ 실행을 시각화하고 비교할 수 있게 해주는 간소화되고 효율적인 실험 세부 정보 페이지
+ 학습 실행을 이해하고 모니터링할 수 있는 실행 세부 정보 페이지에 대한 새로운 디자인

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Python용 Azure Machine Learning SDK v1.0.65

  + **새로운 기능**
    + 큐레이팅된 환경이 추가되었습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성되었으며, 더 빠른 실행을 위해 Docker 이미지로 미리 빌드되고 캐시되었습니다. 기본적으로 작업 영역의 환경 목록에 "AzureML" 접두사를 사용하여 표시됩니다.
    + 큐레이팅된 환경이 추가되었습니다. 이러한 환경은 일반적인 기계 학습 작업을 위한 라이브러리로 미리 구성되었으며, 더 빠른 실행을 위해 Docker 이미지로 미리 빌드되고 캐시되었습니다. 기본적으로 [작업 영역](/python/api/azureml-core/azureml.core.workspace%28class%29)의 환경 목록에 "AzureML" 접두사를 사용하여 표시됩니다.

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + ADB 및 HDI에 대한 ONNX 변환 지원이 추가됨

+ **미리 보기 기능**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT 및 BiLSTM이 텍스트 기능화 도구로 지원됨(미리 보기 전용)
    + 열 용도 및 변환기 매개 변수에 기능화 사용자 지정이 지원됨(미리 보기 전용)
    + 사용자가 학습 중에 모델 설명을 사용하도록 설정하는 경우 원시 설명이 지원됨(미리 보기 전용)
    + `timeseries` 예측용 Prophet이 학습 가능한 파이프라인으로 추가됨(미리 보기 전용)

  + **azureml-contrib-datadrift**
    + azureml-contrib-datadrift에서 azureml-datadrift로 패키지 이동. `contrib` 패키지는 이후 릴리스에서 제거될 예정입니다.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoMLConfig 및 AutoMLBaseSettings에 FeaturizationConfig가 도입되었습니다.
    + [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 및 AutoMLBaseSettings에 FeaturizationConfig가 도입되었습니다.
      + 지정된 열 및 기능 유형에서 기능화에 대한 열 용도 재정의
      + 변환기 매개 변수 재정의
    + explain_model() 및 retrieve_model_explanations()에 대한 사용 중단 메시지 추가
    + Prophet이 학습 가능한 파이프라인으로 추가됨(미리 보기 전용)
    + explain_model() 및 retrieve_model_explanations()에 대한 사용 중단 메시지가 추가되었습니다.
    + Prophet이 학습 가능한 파이프라인으로 추가되었습니다(미리 보기 전용).
    + 대상 구간, 이동 기간 크기 및 최대 구간의 자동 검색에 대한 지원이 추가되었습니다. target_lags, target_rolling_window_size 또는 max_horizon 중 하나가 'auto'로 설정된 경우, 학습 데이터를 기반으로 해당 매개 변수의 값을 예측하기 위해 추론이 적용됩니다.
    + 데이터 세트에 하나의 조직 열이 포함된 경우 이 조직이 숫자 형식이며 학습 및 테스트 집합 사이에 격차가 있는 사례에서 예측 수정
    + 예측 작업의 원격 실행에서 중복된 인덱스에 대한 오류 메시지 수정
    + 데이터 세트에 하나의 조직 열이 포함된 경우 이 조직이 숫자 형식이며 학습 및 테스트 집합 사이에 격차가 있는 사례에서 예측을 수정했습니다.
    + 예측 작업의 원격 실행에서 중복된 인덱스에 대한 오류 메시지를 수정했습니다.
    + 데이터 세트가 불균형한지 여부를 확인하는 가드 레일이 추가되었습니다. 불균형할 경우 가드 레일 메시지가 콘솔에 기록됩니다.
  + **azureml-core**
    + 모델 개체를 통해 스토리지에서 모델의 SAS URL을 검색하는 기능을 추가했습니다. 예: model.get_sas_url()
    + 제출된 실행과 연결된 데이터 세트를 가져오는 `run.get_details()['datasets']` 도입
    + JSON Lines 파일로 TabularDataset를 만드는 API `Dataset.Tabular.from_json_lines_files` 추가. TabularDataset의 JSON Lines 파일에서 이 표 형식 데이터에 대해 알아보려면 [이 설명서](how-to-create-register-datasets.md)를 참조하세요.
    + supported_vmsizes () 함수에 추가 VM 크기 필드(OS 디스크, GPU 수)가 추가됨
    + list_nodes () 함수에 실행, 개인 및 공용 IP, 포트 등을 표시하는 추가 필드를 추가했습니다.
    + 클러스터 프로비저닝 중에 새 필드 --remotelogin_port_public_access를 지정하는 기능 - 클러스터를 만들 때 SSH 포트를 열어 둘지 여부에 따라 사용 또는 사용 안 함으로 설정할 수 있습니다. 이를 지정하지 않으면 VNet 내에 클러스터를 배포하는지 여부에 따라 서비스가 스마트하게 포트를 열거나 닫습니다.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + 모델 개체를 통해 스토리지에서 모델의 SAS URL을 검색하는 기능을 추가했습니다. 예: model.[get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + 제출된 실행과 연결된 데이터 세트를 가져오는 run.[get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] 도입
    + JSON Lines 파일로 TabularDataset를 만드는 API `Dataset.Tabular`.[from_json_lines_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 추가. TabularDataset의 JSON Lines 파일에서 이 표 형식 데이터에 대해 알아보려면 https://aka.ms/azureml-data 설명서를 참조하세요.
    + [supported_vmsizes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) 함수에 추가 VM 크기 필드(OS 디스크, GPU 수)가 추가됨
    + [list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) 함수에 실행, 개인 및 공용 IP, 포트 등을 표시하는 추가 필드를 추가했습니다.
    + [클러스터 프로비저닝](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) 중에 새 필드를 지정하는 기능 - 클러스터를 만들 때 SSH 포트를 열어 둘지 여부에 따라 사용 또는 사용 안 함으로 설정할 수 있습니다. 이를 지정하지 않으면 VNet 내에 클러스터를 배포하는지 여부에 따라 서비스가 스마트하게 포트를 열거나 닫습니다.
  + **azureml-explain-model**
    + 분류 시나리오의 설명 출력에 대한 설명서가 개선되었습니다.
    + 평가 예제에 대한 설명에서 예측된 y 값을 업로드하는 기능이 추가되었습니다. 더 유용한 시각화의 잠금을 해제합니다.
    + 기본 MimicExplainer를 가져올 수 있도록 MimicWrapper에 설명자 속성이 추가되었습니다.
  + **azureml-pipeline-core**
    + Module, ModuleVersion 및 ModuleStep을 설명하는 Notebook이 추가되었습니다.
  + **azureml-pipeline-steps**
    + AML 파이프라인을 통해 R 스크립트 실행을 지원하는 RScriptStep이 추가되었습니다.
    + "SubscriptionId 매개 변수에 대한 할당이 지정되지 않았습니다"라는 오류 메시지를 발생시키는 AzureBatchStep의 메타데이터 매개 변수 구문 분석이 수정되었습니다.
  + **azureml-train-automl**
    + training_data, validation_data, label_column_name weight_column_name이 데이터 입력 형식으로 지원됨
    + explain_model() 및 retrieve_model_explanations()에 대한 사용 중단 메시지 추가
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + [Module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion 및 [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)을 설명하는 [Notebook](https://aka.ms/pl-modulestep)이 추가되었습니다.
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + AML 파이프라인을 통해 R 스크립트 실행을 지원하는 [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep)이 추가되었습니다.
    + "SubscriptionId 매개 변수에 대한 할당이 지정되지 않았습니다"라는 오류 메시지를 발생시키는 [AzureBatchStep의 메타데이터 매개 변수 구문 분석이 수정되었습니다.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + training_data, validation_data, label_column_name weight_column_name이 데이터 입력 형식으로 지원됩니다.
    + [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) 및 [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)에 대한 사용 중단 메시지가 추가되었습니다.


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Python용 Azure Machine Learning SDK v1.0.62

+ **새로운 기능**
  + TabularDataset에 대한 `timeseries` 특성이 도입되었습니다. 이러한 특성을 사용하여 TabularDataset 데이터의 타임스탬프를 쉽게 필터링할 수 있습니다. 예를 들어 특정 시간 범위의 데이터 또는 최신 데이터를 가져올 수 있습니다.  Notebook 예제는 https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb 를 참조하세요.
  + TabularDataset 및 FileDataset를 사용한 학습을 지원합니다. 

  + **azureml-train-core**
      + PyTorch 예측 도구에서 `Nccl` 및 `Gloo` 지원이 추가됨

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoML 설정 'lag_length' 및 LaggingTransformer가 사용되지 않습니다.
    + Dataflow 형식으로 지정된 경우 입력 데이터의 올바른 유효성 검사가 수정되었습니다.
    + fit_pipeline.py가 그래프 json을 생성하고 아티팩트에 업로드하도록 수정되었습니다.
    + `Cytoscape`를 사용하여 `userrun` 아래에 그래프를 렌더링했습니다.
  + **azureml-core**
    + ADB 코드의 예외 처리가 다시 논의되었으며 새 오류 처리에 따라 변경 사항을 적용합니다.
    + Notebook VM에 대한 자동 MSI 인증을 추가했습니다.
    + 실패한 재시도로 인해 손상되거나 비어 있는 모델을 업로드할 수 있는 버그를 수정합니다.
    + `DataReference` 모드가 변경될 때(예: `as_upload`, `as_download` 또는 `as_mount`를 호출하는 경우) `DataReference` 이름이 변경되는 버그를 수정했습니다.
    + `FileDataset.mount` 및 `FileDataset.download`에서 `mount_point` 및 `target_path`가 선택 사항이 되었습니다.
    + 세부적인 타임스탬프 열을 할당하지 않고 시계열 관련 API를 호출하거나 할당된 타임스탬프 열을 삭제한 경우에는 타임스탬프 열을 찾을 수 없다는 예외가 throw됩니다.
    + 시계열 열에는 Date 형식의 열을 할당해야 합니다. 그렇지 않으면 예외가 발생합니다.
    + API를 할당하는 시계열 열 'with_timestamp_columns'는 None 값, 세분화된/성긴 타임스탬프 열 이름을 사용할 수 있으며, 그러면 이전에 할당된 타임스탬프 열이 지워집니다.
    + 삭제 목록에서 타임스탬프 열을 제외하거나 None 값이 있는 with_time_stamp를 호출하여 타임스탬프 열을 해제한 후 사용자에게 삭제할 수 있음을 표시하여 성긴 수준 또는 세분화된 타임스탬프 열이 삭제되면 예외가 throw됩니다.
    + 열 유지 목록에 timestamp 열을 포함하거나 None 값이 있는 with_time_stamp를 호출하여 타임스탬프 열을 해제한 후 사용자에게 유지를 수행할 수 있음을 표시하여 성긴 수준 또는 세분화된 타임스탬프 열이 열 유지 목록에 포함되지 않은 경우 예외가 throw됩니다.
    + 등록된 모델의 크기에 대한 로깅이 추가되었습니다.
  + **azureml-explain-model**
    + "packaging" python 패키지가 설치되지 않은 경우, 콘솔에 인쇄되는 경고가 수정됨: "지원되는 버전보다 오래된 버전의 lightgbm을 사용하는 경우 2.2.1 이후 버전으로 업그레이드하세요."
    + 여러 기능이 있는 글로벌 설명에 대한 분할을 통해 모델 다운로드 설명 수정
    + 출력 설명에서 초기화 예제가 누락된 모방 설명자가 수정됨
    + 두 가지 유형의 모델을 사용하는 설명 클라이언트를 사용하여 업로드할 때 set 속성에 대한 불변 오류 해결
    + 채점 explainer.explain()에 get_raw 매개 변수가 추가되어 하나의 채점 설명자가 엔지니어링된 값과 원시 값을 모두 반환할 수 있습니다.
  + **azureml-train-automl**
    + `automl` 설명 SDK의 설명을 지원하기 위해 AutoML에서 도입된 공용 API - AutoML 기능화 및 설명 SDK를 분리하여 AutoML 설명을 지원하는 새로운 방법 - AutoML 모델에 대한 azureml 설명 SDK의 통합 원시 설명 지원.
    + 원격 학습 환경에서 azureml-defaults 제거.
    + Azure Databricks 코드 경로에서 AutoML의 기본 캐시 저장소 위치가 FileCacheStore 기반 위치에서 AzureFileCacheStore 기반 위치로 변경되었습니다.
    + Dataflow 형식으로 지정된 경우 입력 데이터의 올바른 유효성 검사가 수정되었습니다.
  + **azureml-train-core**
    + source_directory_data_store 사용 중단을 되돌렸습니다.
    + azureml 설치 패키지 버전을 재정의하는 기능이 추가되었습니다.
    + 예측 도구의 `environment_definition` 매개 변수에 dockerfile 지원이 추가되었습니다.
    + 예측 도구에서 분산 학습 매개 변수가 간소화되었습니다.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Azure Machine Learning 작업 영역의 새로운 웹 환경(미리 보기)
새로운 웹 환경을 통해 데이터 과학자 및 데이터 엔지니어는 데이터 준비 및 시각화부터 모델 학습 및 배포에 이르는 엔드투엔드 기계 학습 수명 주기를 단일 위치에서 완료할 수 있습니다.

![Azure Machine Learning 작업 영역 UI(미리 보기)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**주요 기능:**

이제 이 새로운 Azure Machine Learning 인터페이스를 사용하여 다음을 수행할 수 있습니다.
+ Notebook 관리 또는 Jupyter에 연결
+ [자동화된 ML 실험 실행](tutorial-first-experiment-automated-ml.md)
+ [로컬 파일, 데이터 저장소 및 웹 파일로 데이터 세트 만들기](how-to-create-register-datasets.md)
+ 모델 생성에 사용할 데이터 세트 탐색 및 준비
+ 모델에 대한 데이터 드리프트 모니터링
+ 대시보드에서 최근 리소스 보기

현재 이 릴리스에서는 Chrome, Firefox, Safari 및 Microsoft Edge Preview와 같은 브라우저가 지원됩니다.

**알려진 문제:**

1. 배포가 진행 중일 때 "오류가 발생했습니다. 청크 파일을 로드하는 동안 오류가 발생했습니다"가 표시되면 브라우저를 새로 고치세요.

1. Notebooks 및 Files에서 파일을 삭제하거나 이름을 바꿀 수 없습니다. 공개 미리 보기 중에는 Notebook VM에서 Jupyter UI 또는 터미널을 사용하여 파일 업데이트 작업을 수행할 수 있습니다. 탑재된 네트워크 파일 시스템이므로 Notebook VM에서 변경한 모든 내용이 Notebook 작업 영역에 즉시 반영됩니다.

1. SSH를 통해 Notebook VM에 연결하려면 다음을 수행합니다.
   1. VM을 설치하는 동안 생성된 SSH 키를 찾습니다. 또는 Azure Machine Learning 작업 영역 > 컴퓨팅 탭 열기 > 목록에서 Notebook VM 찾기 > 해당 속성 열기: 대화 상자에서 키를 복사하는 방법으로 키를 찾습니다.
   1. 이러한 퍼블릭 및 프라이빗 SSH 키를 로컬 머신으로 가져옵니다.
   1. 이를 사용하여 SSH를 통해 Notebook VM에 연결합니다.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Python용 Azure Machine Learning SDK v1.0.60

+ **새로운 기능**
  + 데이터 저장소 또는 퍼블릭 URL에 있는 하나 또는 여러 개의 파일을 참조하는 FileDataset가 도입되었습니다. 파일은 모든 형식일 수 있습니다. FileDataset는 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다. 
  + PythonScript Step, Adla Step, Databricks Step, DataTransferStep 및 AzureBatch Step에 대한 파이프라인 Yaml 지원이 추가되었습니다.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + AutoArima는 이제 미리 보기 전용으로 제안 가능한 파이프라인입니다.
    + 예측에 대한 오류 보고가 향상되었습니다.
    + 예측 작업에서 제네릭 대신 사용자 지정 예외를 사용하여 로깅을 개선했습니다.
    + max_concurrent_iterations에 대한 검사가 총 반복 횟수보다 적도록 제거되었습니다.
    + AutoML 모델은 이제 AutoMLExceptions를 반환합니다.
    + 이 릴리스는 자동화된 Machine Learning 로컬 실행의 실행 성능을 향상시킵니다.
  + **azureml-core**
    + 등록 이름으로 키가 지정되는 `TabularDataset` 및 `FileDataset` 개체의 사전을 반환하는 Dataset.get_all(workspace)을 도입했습니다.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + `Dataset.Tabular.from_delimited_files` 및 `Dataset.Tabular.from_parquet.files`에 대한 인수로 `parition_format`을 도입했습니다. 각 데이터 경로의 파티션 정보는 지정된 형식에 따라 열로 추출됩니다. '{column_name}'은 문자열 열을 만들고, '{column_name:yyyy/MM/dd/HH/mm/ss}'는 datetime 열을 만듭니다. 여기서 'yyyy', 'MM', 'dd', 'HH', 'mm' 및 'ss'는 datetime 형식의 연도, 월, 일, 시, 분 및 초를 추출하는 데 사용됩니다. partition_format은 첫 번째 파티션 키의 위치에서 시작하여 파일 경로의 끝 부분에서 종료되어야 합니다. 예를 들어 '../USA/2019/01/01/data.csv' 경로가 지정된 경우 파티션이 국가 및 시간별로 분할되며, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv'는 값이 'USA'인 'Country' 문자열 열과 '2019-01-01' 값이 포함된 'PartitionDate'라는 datetime 열을 만듭니다.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + `Dataset.Tabular.from_delimited_files` 및 `Dataset.Tabular.from_parquet.files`에 대한 인수로 `partition_format`을 도입했습니다. 각 데이터 경로의 파티션 정보는 지정된 형식에 따라 열로 추출됩니다. '{column_name}'은 문자열 열을 만들고, '{column_name:yyyy/MM/dd/HH/mm/ss}'는 datetime 열을 만듭니다. 여기서 'yyyy', 'MM', 'dd', 'HH', 'mm' 및 'ss'는 datetime 형식의 연도, 월, 일, 시, 분 및 초를 추출하는 데 사용됩니다. partition_format은 첫 번째 파티션 키의 위치에서 시작하여 파일 경로의 끝 부분에서 종료되어야 합니다. 예를 들어 '../USA/2019/01/01/data.csv' 경로가 지정된 경우 파티션이 국가 및 시간별로 분할되며, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv'는 값이 'USA'인 'Country' 문자열 열과 '2019-01-01' 값이 포함된 'PartitionDate'라는 datetime 열을 만듭니다.
    + `to_csv_files` 및 `to_parquet_files` 메서드가 `TabularDataset`에 추가되었습니다. 이러한 메서드는 데이터를 지정된 형식의 파일로 변환하여 `TabularDataset`와 `FileDataset` 간의 변환을 지원합니다.
    + Model.package()에서 생성한 Dockerfile을 저장하면 기본 이미지 레지스트리에 자동으로 로그인됩니다.
    + 'gpu_support'는 더 이상 필요하지 않습니다. AML은 이제 nvidia docker 확장을 자동으로 검색하고 사용합니다(사용할 수 있는 경우). 후속 릴리스에서 제거될 예정입니다.
    + PipelineDrafts를 만들고, 업데이트하고, 사용하도록 지원하는 기능이 추가되었습니다.
    + 이 릴리스는 자동화된 Machine Learning 로컬 실행의 실행 성능을 향상시킵니다.
    + 사용자는 실행 기록에서 이름으로 메트릭을 쿼리할 수 있습니다.
    + 예측 작업에서 제네릭 대신 사용자 지정 예외를 사용하여 로깅을 개선했습니다.
  + **azureml-explain-model**
    + 사용자가 원시 기능 설명을 가져올 수 있도록 feature_maps 매개 변수를 새 MimicWrapper에 추가했습니다.
    + 이제 설명 업로드에 대한 데이터 세트 업로드는 기본적으로 해제되어 있으며, upload_datasets=True를 사용하여 다시 사용하도록 설정할 수 있습니다.
    + 설명 목록 및 다운로드 함수에 "is_law" 필터링 매개 변수가 추가되었습니다.
    + 글로벌 및 로컬 설명 개체 모두에 `get_raw_explanation(feature_maps)` 메서드를 추가합니다.
    + 지원되는 아래 버전의 경우 경고가 인쇄되고 lightgbm에 대한 버전 검사가 추가됨
    + 설명 일괄 처리 시 메모리 사용량이 최적화됨
    + AutoML 모델은 이제 AutoMLExceptions를 반환합니다.
  + **azureml-pipeline-core**
    + PipelineDrafts 만들기, 업데이트 및 사용에 대한 지원이 추가됨 - 변경 가능한 파이프라인 정의를 유지 관리하고 대화형으로 사용하여 실행할 수 있습니다.
  + **azureml-train-automl**
    + 원격 Python 런타임 환경에서 BERT/XLNet을 사용하도록 설정하는 데 필요한 특정 버전의 gpu 지원 pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, pytorch-transformer를 설치하는 기능을 만들었습니다.
  + **azureml-train-core**
    + 일부 하이퍼 매개 변수 공간 정의 오류의 초기 오류는 서버 쪽이 아닌 sdk에서 직접 발생합니다.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **버그 수정 및 개선 사항**
  + 원시 경로 및 자격 증명을 사용하여 ADLS/ADLSGen2에 쓸 수 있습니다.
  + `read_parquet`에서 `include_path=True`가 작동하지 않는 버그가 수정되었습니다.
  + "잘못된 속성 값: hostSecret" 예외로 인한 `to_pandas_dataframe()` 오류를 해결했습니다.
  + Spark 모드의 DBFS에서 파일을 읽을 수 없는 버그가 수정되었습니다.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Python용 Azure Machine Learning SDK v1.0.57
+ **새로운 기능**
  + AutomatedML에서 사용할 수 있는 `TabularDataset` 지원. `TabularDataset`에 대해 자세히 알아보려면 https://aka.ms/azureml/howto/createdatasets 를 방문하세요.

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + 이제 AKS 클러스터에 배포된 채점 엔드포인트의 TLS/SSL 인증서(Microsoft에서 생성한 인증서 및 고객 인증서 모두)를 업데이트할 수 있습니다.
  + **azureml-automl-core**
    + AutoML에서 레이블이 누락된 행이 제대로 제거되지 않는 문제가 해결되었습니다.
    + AutoML의 오류 로깅 개선. 이제 전체 오류 메시지가 항상 로그 파일에 기록됩니다.
    + AutoML에서 `azureml-defaults`, `azureml-explain-model` 및 `azureml-dataprep`를 포함하도록 패키지 고정을 업데이트했습니다. AutoML이 패키지 불일치에 대해 더 이상 경고하지 않습니다(`azureml-train-automl` 패키지 제외).
    + `timeseries`에서 cv 분할의 크기가 같지 않아 bin 계산이 실패하는 문제가 해결되었습니다.
    + Cross-Validation 학습 유형에 대한 앙상블 반복을 실행할 때, 전체 데이터 세트에 대해 학습된 모델을 다운로드하는 데 문제가 발생하면 모델 가중치와 투표 앙상블에 공급된 모델 간에 불일치가 발생한 것입니다.
    + 학습 및/또는 유효성 검사 레이블(y 및 y_valid)이 numpy 배열이 아닌 pandas 데이터 프레임 형식으로 제공될 때 발생하는 오류가 해결되었습니다.
    + 입력 테이블의 부울 열에서 None이 발견된 경우 예측 작업에서 발생하는 문제가 해결되었습니다.
    + AutoML 사용자가 예측 시 충분히 길지 않은 학습 시리즈를 삭제할 수 있습니다. - AutoML 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직을 삭제할 수 있습니다.
  + **azureml-core**
    + blob_cache_timeout 매개 변수 순서 지정 문제를 해결했습니다.
    + 외부 맞춤 및 변환 예외 유형을 시스템 오류에 추가했습니다.
    + 원격 실행에 Key Vault 비밀에 대한 지원이 추가되었습니다. 작업 영역과 연결된 keyvault의 비밀을 추가 또는 나열하거나 가져오려면 azureml.core.keyvault.Keyvault 클래스를 추가합니다. 지원되는 작업은 다음과 같습니다.
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(name, value)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(name)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + 원격 실행 중에 기본 키 자격 증명 모음을 가져오고 비밀을 가져오는 추가 메서드:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(name)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + submit-hyperdrive CLI 명령에 추가 재정의 매개 변수가 추가되었습니다.
    + 일반적인 요청 라이브러리 예외로 재시도를 확장하도록 API 호출의 안정성 개선.
    + 제출된 실행에서 실행을 제출하기 위한 지원 추가.
    + 초기 토큰이 만료된 후 파일이 업로드되지 않도록 하는 FileWatcher의 만료 SAS 토큰 문제가 해결되었습니다.
    + 데이터 세트 python SDK에서 HTTP csv/tsv 파일을 가져올 수 있습니다.
    + Workspace.setup() 메서드가 사용되지 않습니다. 사용자에게 표시되는 경고 메시지에서 create() 또는 get()/from_config()를 대신 사용할 것을 권장합니다.
    + 프라이빗 사용자 지정 python 패키지 `whl`을 작업 영역에 업로드하고 안전하게 사용하여 환경을 빌드/구체화할 수 있게 해주는 Environment.add_private_pip_wheel()이 추가되었습니다.
    + 이제 AKS 클러스터에 배포된 채점 엔드포인트의 TLS/SSL 인증서(Microsoft에서 생성한 인증서 및 고객 인증서 모두)를 업데이트할 수 있습니다.
  + **azureml-explain-model**
    + 업로드에 대한 설명에 모델 ID를 추가하는 매개 변수가 추가되었습니다.
    + 메모리 및 업로드의 설명에 `is_raw` 태그 지정이 추가되었습니다.
    + azureml-explain-model 패키지에 대한 pytorch 지원 및 테스트가 추가되었습니다.
  + **azureml-opendatasets**
    + 자동 테스트 환경 검색 및 로깅을 지원합니다.
    + 카운티 및 우편 번호별로 미국 인구를 얻기 위한 클래스가 추가되었습니다.
  + **azureml-pipeline-core**
    + 입력 및 출력 포트 정의에 레이블 속성이 추가되었습니다.
  + **azureml-telemetry**
    + 잘못된 원격 분석 구성을 수정했습니다.
  + **azureml-train-automl**
    + 설치 실패 시 설치 실행에 대한 "오류" 필드에 오류가 로그되지 않아 부모 실행 "오류"에 저장되지 않는 버그가 수정되었습니다.
    + AutoML에서 레이블이 누락된 행이 제대로 제거되지 않는 문제가 해결되었습니다.
    + AutoML 사용자가 예측 시 충분히 길지 않은 학습 시리즈를 삭제할 수 있습니다.
    + AutoML 사용자가 예측 시 학습 집합에 없는 테스트 집합에서 조직을 삭제할 수 있습니다.
    + 이제 AutoMLStep은 새 구성 매개 변수의 변경 또는 추가에 대한 문제를 방지하기 위해 `automl` 구성을 백 엔드로 전달합니다.
    + AutoML 데이터 가드 레일이 현재 공개 미리 보기로 제공됩니다. 사용자는 학습 후 데이터 가드 레일 보고서(분류/회귀 작업 관련)를 볼 수 있으며 SDK API를 통해 액세스할 수도 있습니다.
  + **azureml-train-core**
    + PyTorch 예측 도구에 torch 1.2 지원을 추가했습니다.
  + **azureml-widgets**
    + 분류 학습을 위한 혼동 행렬 차트가 개선되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **새로운 기능**
  + 이제 문자열 목록을 `read_*` 메서드에 대한 입력으로 전달할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + Spark에서 실행 중인 `read_parquet`의 성능이 개선되었습니다.
  + 모호한 날짜 형식의 단일 열로 인해 `column_type_builder`가 실패하는 문제가 해결되었습니다.

### <a name="azure-portal"></a>Azure portal
+ **미리 보기 기능**
  + 이제 실행 정보 페이지에 대한 로그 및 출력 파일 스트리밍이 제공됩니다. 미리 보기 토글이 설정된 경우 파일이 실시간으로 업데이트를 스트리밍합니다.
  + 작업 영역 수준에서 할당량을 설정하는 기능은 미리 보기로 릴리스되었습니다. AmlCompute 할당량은 구독 수준에서 할당되지만 이제 작업 영역 간에 해당 할당량을 분산하고 공평한 공유 및 거버넌스를 위해 할당할 수 있습니다. 작업 영역의 왼쪽 탐색 모음에서 **사용량 + 할당량** 블레이드를 클릭하고 **할당량 구성** 탭을 선택하면 됩니다. 작업 영역 간 작업이므로 작업 영역 수준에서 할당량을 설정할 수 있으려면 구독 관리자여야 합니다.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Python용 Azure Machine Learning SDK v1.0.55

+ **새로운 기능**
  + 이제 AKS에 배포된 채점 엔드포인트에 대한 호출에 토큰 기반 인증이 지원됩니다. 현재 키 기반 인증은 계속 지원될 예정이며, 사용자는 이러한 인증 메커니즘을 한 번에 하나씩 사용할 수 있습니다.
  + VNet(가상 네트워크) 뒤에 있는 Blob Storage를 데이터 저장소로 등록하는 기능.

+ **버그 수정 및 개선 사항**
  + **azureml-automl-core**
    + CV 분할에 대한 유효성 검사 크기가 작고 회귀 및 예측에 대한 잘못된 예측 및 실제 비교 차트를 생성하는 버그를 수정합니다.
    + 원격 실행에 대한 예측 작업 로깅이 향상되었습니다. 이제 실행이 실패할 경우 사용자에게 포괄적인 오류 메시지가 제공됩니다.
    + 전처리 플래그가 True일 경우에 발생하는 `Timeseries` 오류를 해결했습니다.
    + 일부 예측 데이터 유효성 검사 오류 메시지의 실용성이 높아졌습니다.
    + 특히 프로세스 생성 사이에 데이터 세트를 삭제 및/또는 지연 로드하여 AutoML 실행의 메모리 사용량 감소
  + **azureml-contrib-explain-model**
    + 사용자가 모델 형식에 대한 기본 자동 유추 논리를 재정의할 수 있도록 설명자에 model_task 플래그가 추가됨
    + 위젯 변경 내용: `contrib`를 사용하여 자동으로 설치됨, 더 이상 `nbextension` install/enable 사용 안 함 - 글로벌 기능 중요도(예: 순열)를 사용한 설명 지원
    + 대시보드 변경 내용: - 요약 페이지에 `beeswarm` 플롯 외에 상자 플롯 및 바이올린 플롯 추가 - 'Top -k' 슬라이더 변경 시 `beeswarm` 플롯을 훨씬 더 빠르게 다시 렌더링 - top-k가 계산되는 방식을 설명하는 유용한 메시지 - 데이터가 제공되지 않을 때 차트 대신 유용한 사용자 지정 가능한 메시지
  + **azureml-core**
    + 모델 및 해당 종속성을 캡슐화하는 Docker 이미지 및 Dockerfile을 만드는 Model.package() 메서드가 추가되었습니다.
    + 환경 개체가 포함된 InferenceConfig를 허용하도록 로컬 웹 서비스가 업데이트됨
    + 현재 디렉터리의 '.'가 model_path 매개 변수로 전달될 경우 잘못된 모델을 생성하는 Model.register()가 수정되었습니다.
    + Run.submit_child 추가. 이 기능은 제출된 자식 실행의 부모로 실행을 지정할 때 Experiment.submit를 미러링합니다.
    + Run.register_model에서 Model.register의 구성 옵션을 지원합니다.
    + 기존 클러스터에서 JAR 작업을 실행하는 기능.
    + 이제 instance_pool_id 및 cluster_log_dbfs_path 매개 변수 지원.
    + Webservice에 Model을 배포할 때 Environment 개체를 사용하도록 지원하는 기능이 추가되었습니다. 이제 Environment 개체를 InferenceConfig 개체의 일부로 제공할 수 있습니다.
    + 새 지역 - centralus - westus - northcentralus에 대한 appinsifht 매핑 추가
    + 모든 Datastore 클래스의 모든 특성에 대한 설명서가 추가되었습니다.
    + `Datastore.register_azure_blob_container`에 blob_cache_timeout 매개 변수가 추가되었습니다.
    + azureml.core.environment.Environment에 save_to_directory 및 load_from_directory 메서드가 추가되었습니다.
    + CLI에 "az ml environment download" 및 "az ml environment register" 명령이 추가되었습니다.
    + Environment.add_private_pip_wheel 메서드가 추가되었습니다.
  + **azureml-explain-model**
    + Dataset 서비스(미리 보기)를 사용한 데이터 세트 추적이 Explanations에 추가되었습니다.
    + 글로벌 설명을 스트리밍할 때 기본 일괄 처리 크기가 10k에서 100으로 줄었습니다.
    + 사용자가 모델 형식에 대한 기본 자동 유추 논리를 재정의할 수 있도록 설명자에 model_task 플래그가 추가되었습니다.
  + **azureml.mlflow**
    + 중첩 디렉터리가 무시되는 mlflow.azureml.build_image의 버그가 수정되었습니다.
  + **azureml-pipeline-steps**
    + 기존 Azure Databricks 클러스터에서 JAR 작업을 실행하는 기능이 추가되었습니다.
    + DatabricksStep 단계에 대한 instance_pool_id 및 cluster_log_dbfs_path 매개 변수 지원 기능이 추가되었습니다.
    + DatabricksStep 단계에서 파이프라인 매개 변수에 대한 지원이 추가되었습니다.
  + **azureml-train-automl**
    + Ensemble 관련 파일에 대한 `docstrings`가 추가되었습니다.
    + `max_cores_per_iteration` 및 `max_concurrent_iterations`에 대한 문서가 보다 적절한 언어로 업데이트되었습니다.
    + 원격 실행에 대한 예측 작업 로깅이 향상되었습니다. 이제 실행이 실패할 경우 사용자에게 포괄적인 오류 메시지가 제공됩니다.
    + 파이프라인 `automlstep` Notebook에서 get_data가 제거되었습니다.
    + `automlstep`에서 `dataprep` 지원이 시작되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **새로운 기능**
  + 이제 특정 열에 대해 특정 검사기(예: 히스토그램, 산점도 등)를 실행하도록 요청할 수 있습니다.
  + `append_columns`에 병렬화 인수가 추가되었습니다. True이면 데이터가 메모리에 로드되지만 병렬로 실행됩니다. False이면 실행은 스트리밍되지만 단일 스레드입니다.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Python용 Azure Machine Learning SDK v1.0.53

+ **새로운 기능**
  + 이제 자동화된 Machine Learning에서 원격 컴퓨팅 대상에 대한 ONNX 모델 학습 지원
  + 이제 Azure Machine Learning이 이전 실행, 검사점 또는 모델 파일에서 학습을 다시 시작할 수 있는 기능을 제공합니다.
    + [예측 도구를 사용하여 이전 실행에서 학습을 다시 시작](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)하는 방법 알아보기

+ **버그 수정 및 개선 사항**
  + **azure-cli-ml**
    + CLI 명령 "model deploy" 및 "service update"는 이제 매개 변수, 구성 파일 또는 둘의 조합을 허용합니다. 매개 변수는 파일의 특성보다 우선합니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있음
  + **azureml-automl-core**
    + NimbusML 종속성을 1.2.0 버전(현재 최신)으로 업데이트.
    + AutoML 예측 도구 내에서 NimbusML 예측 도구 및 파이프라인을 사용할 수 있는 지원 기능 추가.
    + 점수가 일정하게 유지되더라도 결과 앙상블을 불필요하게 증가시킨 Ensemble 선택 절차의 버그 해결.
    + 예측 작업에서 CV 분할에 일부 기능화의 재사용 지원. 이렇게 하면 구간 및 이동 기간과 같은 비용이 많이 드는 기능화에서 대략 n_cross_validations의 배수로 설치 실행의 런타임 속도가 증가합니다.
    + 시간이 pandas 지원 시간 범위를 벗어나는 경우의 문제 해결. 이제 시간이 pd.Timestamp.min보다 작거나 pd.Timestamp.max보다 크면 DataException이 발생합니다.
    + 이제 예측에서 학습 및 테스트 집합에 서로 다른 빈도를 허용합니다(맞출 수 있는 경우). 예를 들어 "1월에 분기별로 시작"과 "10월에 분기별로 시작"을 맞출 수 있습니다.
    + "parameters" 속성이 TimeSeriesTransformer에 추가되었습니다.
    + 이전 예외 클래스 제거.
    + 예측 작업에서 `target_lags` 매개 변수가 이제 단일 정수 값 또는 정수 목록을 허용합니다. 정수가 제공된 경우 하나의 구간만 생성됩니다. 목록이 제공된 경우 구간의 고유한 값이 사용됩니다. target_lags=[1, 2, 2, 4]는 1, 2 및 4개 마침표의 구간을 만듭니다.
    + 변환 후 열 형식 손실에 대한 버그 수정(버그 연결)
    + `model.forecast(X, y_query)`에서 y_query가 처음에 None을 포함하는 개체 형식이 되도록 허용(#459519).
    + `automl` 출력에 필요한 값 추가
  + **azureml-contrib-datadrift**
    +  azureml-contrib-opendatasets 대신 azureml-opendatasets로의 전환 및 데이터 보강 시 성능 향상 등 예제 Notebook 개선
  + **azureml-contrib-explain-model**
    + azureml-contrib-explain-model 패키지에서 원시 기능 중요도에 대한 LIME 설명자의 변환 인수가 수정됨
    + AzureML-contrib-explain-model 패키지에 대한 이미지 설명자의 이미지 설명에 구분이 추가되었습니다.
    + LimeExplainer에 대한 scipy 스파스 지원 추가
    + `include_local=False`일 경우 모방 설명자에 `batch_size` 추가(글로벌 설명을 일괄적으로 스트리밍하여 DecisionTreeExplainableModel의 실행 시간을 개선하기 위해)
  + **azureml-contrib-featureengineering**
    + set_featurizer_timeseries_params() 호출 수정: dict 값 형식 변경 및 null 검사 - `timeseries` 기능화 도구에 대한 Notebook 추가
    + NimbusML 종속성을 1.2.0 버전(현재 최신)으로 업데이트.
  + **azureml-core**
    + AzureML CLI에서 DBFS 데이터 저장소를 연결하는 기능이 추가됨
    + `target_path`가 `/`로 시작하는 경우 빈 폴더가 만들어지는 데이터 저장소 업로드 버그 수정
    + ServicePrincipalAuthentication의 `deepcopy` 문제를 해결했습니다.
    + CLI에 "az ml environment show" 및 "az ml environment list" 명령이 추가되었습니다.
    + 이제 환경에서 이미 빌드된 base_image 대신 base_dockerfile 지정을 지원합니다.
    + 사용되지 않는 RunConfiguration 설정 auto_prepare_environment가 더 이상 사용되지 않는 것으로 표시되었습니다.
    + 이제 등록 후 모델 설명을 업데이트할 수 있음
    + 버그 수정: 이제 모델 및 이미지 삭제에서 업스트림 종속성으로 인해 삭제가 실패할 경우 종속된 업스트림 개체를 검색하는 방법에 대한 추가 정보를 제공합니다.
    + 일부 환경에 대한 작업 영역을 만들 때 발생하는 배포에 대해 빈 기간을 인쇄한 버그가 수정되었습니다.
    + 작업 영역 만들기에 대한 오류 예외가 개선되었습니다. 따라서 사용자에게 "작업 영역을 만들 수 없습니다. 다음 항목을 찾을 수 없습니다... " 메시지가 표시되지 않고 대신 실제 생성 오류가 표시됩니다.
    + AKS 웹 서비스 내 토큰 인증에 대한 지원 추가.
    + `Webservice` 개체에 `get_token()` 메서드 추가.
    + 기계 학습 데이터 세트를 관리하는 CLI 지원이 추가되었습니다.
    + `Datastore.register_azure_blob_container`는 이제 이 데이터 저장소에 대한 캐시 만료를 사용하도록 blobfuse의 탑재 매개 변수를 구성하는 `blob_cache_timeout` 값(초)을 필요에 따라 사용합니다. 기본값은 Blob을 읽을 때와 같이 제한 시간 없음이므로 작업이 완료될 때까지 로컬 캐시에 유지됩니다. 대부분의 작업은 이 설정을 선호하지만 일부 작업은 노드에 맞는 것보다 더 큰 데이터 세트에서 더 많은 데이터를 읽어야 합니다. 이러한 작업의 경우 이 매개 변수를 튜닝하면 성공하는 데 도움이 됩니다. 이 매개 변수를 튜닝할 때는 주의하세요. 값을 너무 낮게 설정하면 Epoch에 사용된 데이터가 다시 사용되기 전에 만료될 수 있기 때문에 성능이 저하될 수 있습니다. 모든 읽기는 학습 시간에 부정적인 영향을 미치는 로컬 캐시가 아닌 Blob Storage/네트워크에서 수행됩니다.
    + 이제 등록 후 모델 설명을 적절히 업데이트할 수 있음
    + 이제 모델 및 이미지 삭제가 실패를 유발하는 종속된 업스트림 개체에 대한 자세한 정보를 제공합니다.
    + azureml.mlflow를 사용하여 원격 실행의 리소스 사용률 개선.
  + **azureml-explain-model**
    + azureml-contrib-explain-model 패키지에서 원시 기능 중요도에 대한 LIME 설명자의 변환 인수가 수정됨
    + LimeExplainer에 대한 scipy 스파스 지원 추가
    + 셰이프 선형 설명자 래퍼와 선형 모델을 설명하기 위한 테이블 형식 설명자의 또 다른 수준이 추가됨
    + 모델 설명 라이브러리의 모방 설명자에서 스파스 데이터 입력이 include_local=False일 경우 발생하는 오류가 해결됨
    + `automl` 출력에 필요한 값 추가
    + 원시 기능 중요도를 얻기 위해 변환 인수가 제공된 경우 순열 기능 중요도 수정
    + `include_local=False`일 경우 모방 설명자에 `batch_size` 추가(글로벌 설명을 일괄적으로 스트리밍하여 DecisionTreeExplainableModel의 실행 시간을 개선하기 위해)
    + 모델 설명력 라이브러리에서 예측에 pandas 데이터 프레임 입력이 필요한 블랙박스 설명자가 수정됨
    + `explanation.expected_values`가 float를 포함하는 목록이 아닌 float를 종종 반환하는 버그를 수정했습니다.
  + **azureml.mlflow**
    + mlflow.set_experiment(experiment_name)의 성능 향상
    + mlflow tracking_uri에 InteractiveLoginAuthentication 사용 시 발생하는 버그 수정
    + azureml.mlflow를 사용하여 원격 실행의 리소스 사용률 개선.
    + azureml-mlflow 패키지의 설명서 개선
    + mlflow.log_artifacts("my_dir")이 `<artifact-paths>` 대신 `my_dir/<artifact-paths>`에 아티팩트를 저장하는 버그 패치
  + **azureml-opendatasets**
    + 새로 도입된 메모리 문제로 인해 `opendatasets`의 `pyarrow`를 이전 버전(0.14.0 미만)으로 고정.
    + azureml-contrib-opendatasets를 azureml-opendatasets로 이동.
    + 개방형 데이터 세트 클래스를 Azure Machine Learning 작업 영역에 등록하고 AML 데이터 세트 기능을 원활하게 활용할 수 있도록 허용.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능 크게 개선.
  + **azureml-pipeline-steps**
    + 이제 DatabricksStep의 입력 및 출력에 DBFS 데이터 저장소가 지원됩니다.
    + 입력/출력과 관련하여 Azure Batch Step의 설명서가 업데이트되었습니다.
    + AzureBatchStep에서 *delete_batch_job_after_finish* 기본값이 *true* 로 변경되었습니다.
  + **azureml-telemetry**
    +  azureml-contrib-opendatasets를 azureml-opendatasets로 이동.
    + 개방형 데이터 세트 클래스를 Azure Machine Learning 작업 영역에 등록하고 AML 데이터 세트 기능을 원활하게 활용할 수 있도록 허용.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능 크게 개선.
  + **azureml-train-automl**
    + get_output에 대한 설명서가 실제 반환 형식을 반영하고 키 속성을 검색하는 방법에 대한 추가 정보를 제공하도록 업데이트되었습니다
    + NimbusML 종속성을 1.2.0 버전(현재 최신)으로 업데이트.
    + `automl` 출력에 필요한 값 추가
  + **azureml-train-core**
    + 이제 문자열이 자동화된 하이퍼 매개 변수 튜닝의 컴퓨팅 대상으로 허용됩니다.
    + 사용되지 않는 RunConfiguration 설정 auto_prepare_environment가 더 이상 사용되지 않는 것으로 표시되었습니다.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **새로운 기능**
  + http 또는 https URL에서 직접 파일을 읽도록 지원하는 기능이 추가되었습니다.

+ **버그 수정 및 개선 사항**
  + 원격 원본(현재 지원되지 않음)에서 Parquet 데이터 세트를 읽으려고 할 때 발생하는 오류 메시지가 개선되었습니다.
  + ADLS Gen 2에서 Parquet 파일 형식에 쓰고 경로에서 ADLS Gen 2 컨테이너 이름을 업데이트할 때 발생하는 버그가 수정되었습니다.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>시각적 인터페이스
+ **미리 보기 기능**
  + 시각적 인터페이스에 "R 스크립트 실행" 모듈이 추가되었습니다.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Python용 Azure Machine Learning SDK v1.0.48

+ **새로운 기능**
  + **azureml-opendatasets**
    + 이제 **azureml-contrib-opendatasets** 가 **azureml-opendatasets** 로 제공됩니다. 이전 패키지는 계속 작동할 수 있지만 더 다양한 기능과 향상된 기능을 위해 앞으로 **azureml-opendatasets** 를 사용하는 것이 좋습니다.
    + 이 새 패키지를 사용하면 개방형 데이터 세트를 Azure Machine Learning 작업 영역의 데이터 세트로 등록하고, 이 데이터 세트가 제공하는 모든 기능을 활용할 수 있습니다.
    + 여기에는 또한 개방형 데이터 세트를 Pandas/SPARK 데이터 프레임으로 사용하는 것과 같은 기존 기능과 날씨와 같은 일부 데이터 세트에 대한 위치 조인도 포함됩니다.

+ **미리 보기 기능**
    + 이제 HyperDriveConfig는 파이프라인 개체를 매개 변수로 사용하여 파이프라인을 사용한 하이퍼 매개 변수 튜닝을 지원할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + **azureml-train-automl**
    + 변환 후 열 형식 손실에 대한 버그가 수정되었습니다.
    + y_query가 처음에 None을 포함하는 개체 형식이 되도록 허용하는 버그를 수정했습니다.
    + 점수가 일정하게 유지되더라도 결과 앙상블을 불필요하게 증가시킨 Ensemble 선택 절차의 문제를 해결했습니다.
    + AutoMLStep에서 list_models 허용 및 list_models 차단 설정 문제를 해결했습니다.
    + AutoML이 Azure ML 파이프라인의 컨텍스트에서 사용될 때 전처리를 사용하지 못하게 하는 문제를 해결했습니다.
  + **azureml-opendatasets**
    + azureml-contrib-opendatasets를 azureml-opendatasets로 이동.
    + 개방형 데이터 세트 클래스를 Azure Machine Learning 작업 영역에 등록하고 AML 데이터 세트 기능을 원활하게 활용할 수 있도록 허용.
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능 크게 개선.
  + **azureml-explain-model**
    + 해석력 개체에 대한 온라인 설명서가 업데이트되었습니다.
    + 모델 설명력 라이브러리에서 `include_local=False`일 경우 모방 설명자에 `batch_size` 추가(글로벌 설명을 일괄적으로 스트리밍하여 DecisionTreeExplainableModel의 실행 시간을 개선하기 위해)
    + `explanation.expected_values`가 float가 포함된 목록이 아닌 float를 종종 반환하는 문제를 해결했습니다.
    + `automl` 출력에 모델 설명 라이브러리의 모방 설명자에 필요한 값을 추가했습니다.
    + 원시 기능 중요도를 얻기 위해 변환 인수가 제공된 경우 순열 기능 중요도 수정.
  + **azureml-core**
    + AzureML CLI에서 DBFS 데이터 저장소를 연결하는 기능이 추가되었습니다.
    + `target_path`가 `/`로 시작하는 경우 빈 폴더가 만들어지는 데이터 저장소 업로드 문제를 해결했습니다.
    + 두 데이터 세트의 비교 지원.
    + 이제 모델 및 이미지 삭제에서 업스트림 종속성으로 인해 삭제가 실패할 경우 종속된 업스트림 개체를 검색하는 방법에 대한 추가 정보를 제공합니다.
    + auto_prepare_environment에서 사용되지 않는 RunConfiguration 설정의 사용이 중단되었습니다.
  + **azureml.mlflow**
    + azureml.mlflow를 사용하는 원격 실행의 리소스 사용률이 개선되었습니다.
    + azureml-mlflow 패키지의 설명서가 개선되었습니다.
    + mlflow.log_artifacts("my_dir")가 "artifact-paths" 대신 "my_dir/artifact-paths"로 아티팩트를 저장하는 문제가 해결되었습니다.
  + **azureml-pipeline-core**
    + 모든 파이프라인 단계에 대한 매개 변수 hash_paths는 더 이상 사용되지 않으며 나중에 제거될 예정입니다. 기본적으로 source_directory의 콘텐츠는 해시됩니다(`.amlignore` 또는 `.gitignore`에 나열된 파일 제외).
    + 파이프라인에서 컴퓨팅 유형별 모듈 사용을 잠금 해제하기 위해 컴퓨팅 유형별 모듈을 지원하고, RunConfiguration 통합 및 기타 변경 내용을 준비하도록 Module 및 ModuleStep을 지속적으로 개선했습니다.
  + **azureml-pipeline-steps**
    + AzureBatchStep: 입력/출력과 관련하여 설명서가 향상되었습니다.
    + AzureBatchStep: delete_batch_job_after_finish 기본값을 true로 변경.
  + **azureml-train-core**
    + 이제 문자열이 자동화된 하이퍼 매개 변수 튜닝의 컴퓨팅 대상으로 허용됩니다.
    + auto_prepare_environment에서 사용되지 않는 RunConfiguration 설정의 사용이 중단되었습니다.
    + `conda_dependencies_file_path` 및 `pip_requirements_file_path` 매개 변수가 각각 `conda_dependencies_file` 및 `pip_requirements_file`을 위해 더 이상 사용되지 않습니다.
  + **azureml-opendatasets**
    + 비 SPARK 버전에서 NoaaIsdWeather 보강 성능 크게 개선.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Python용 Azure Machine Learning SDK v1.0.33이 출시되었습니다.

+ [FPGA](how-to-deploy-fpga-web-service.md) 기반 Azure ML 하드웨어 가속 모델이 일반 공급됩니다.
  + 이제 [azureml-accel-models 패키지를 사용](how-to-deploy-fpga-web-service.md)하여 다음을 수행할 수 있습니다.
    + 지원되는 심층 신경망의 가중치 학습(ResNet 50, ResNet 152, DenseNet-121, VGG-16 및 SSD-VGG)
    + 지원되는 DNN에서 전이 학습 사용
    + 모델 관리 서비스에 모델을 등록하고 모델 컨테이너화
    + AKS(Azure Kubernetes Service) 클러스터에서 FPGA를 사용하여 Azure VM에 모델 배포
  + [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) 서버 디바이스에 컨테이너 배포
  + 이 [샘플](https://github.com/Azure-Samples/aml-hardware-accelerated-models)을 사용하여 gRPC 엔드포인트를 통해 데이터 채점

### <a name="automated-machine-learning"></a>자동화된 Machine Learning

+ 기능 비우기로 :::no-loc text="featurizers":::를 동적으로 추가하여 성능 최적화 새로운 :::no-loc text="featurizers":::: 작업 포함, 증거 가중치, 대상 인코딩, 텍스트 대상 인코딩, 클러스터 거리
+ 자동화된 ML 내에서 학습/유효한 분할을 처리하는 스마트 CV
+ 몇 가지 메모리 최적화 변경 사항 및 런타임 성능 향상
+ 모델 설명의 성능 향상
+ 로컬 실행을 위한 ONNX 모델 변환
+ 하위 샘플링 지원이 추가됨
+ 종료 기준이 정의되지 않은 경우 지능형 중지
+ 누적 앙상블

+ 시계열 예측
  + 새 예측 함수
  + 이제 시계열 데이터에 대한 이동 원본 교차 유효성 검사를 사용할 수 있습니다.
  + 시계열 구간 구성을 위해 새로운 기능이 추가됨
  + 이동 기간 집계 기능을 지원하기 위해 새 기능이 추가됨
  + 실험 설정에 국가 코드가 정의된 경우 새 휴일 검색 및 기능화 도구

+ Azure Databricks
  + 시계열 예측 및 모델 설명력/해석력 기능 사용
  + 이제 자동화된 ML 실험을 취소하고 다시 시작(계속)할 수 있습니다.
  + 다중 코어 처리에 대한 지원이 추가됨

### <a name="mlops"></a>MLOps
+ **채점 컨테이너 로컬 배포 및 디버깅**<br/> 이제 로컬에서 ML 모델을 배포하고 채점 파일 및 종속성을 빠르게 반복하여 예상대로 동작하는지 확인할 수 있습니다.

+ **InferenceConfig 및 Model.deploy() 도입**<br/> 모델 배포에서 이제 RunConfig와 동일한 항목 스크립트가 포함된 원본 폴더를 지정하도록 지원합니다.  또한 모델 배포가 단일 명령으로 간소화되었습니다.

+ **Git 참조 추적**<br/> 전체 감사 내역을 유지하는 데 도움이 되는 기본 Git 통합 기능에 대한 고객 요청이 오래 전부터 있었습니다. Git 관련 메타데이터(리포지토리, 커밋, 정리 상태)에 대한 Azure ML의 주요 엔터티 간 추적을 구현했습니다. 이 정보는 SDK 및 CLI에서 자동으로 수집합니다.

+ **모델 프로파일링 및 유효성 검사 서비스**<br/> 고객 여러분들이 유추 서비스와 관련된 컴퓨팅의 크기를 적절하게 조정하는 데 따른 어려움을 자주 토로했습니다. 당사의 모델 프로파일링 서비스는 고객이 샘플 입력을 제공하면 최적의 배포 규모를 결정하기 위한 16가지 CPU/메모리 구성을 프로파일링해 드립니다.

+ **유추를 위한 고유한 기본 이미지 가져오기**<br/> 고객의 또 다른 일반적인 불만 사항은 실험에서 RE 공유 종속성 유추로 전환할 때의 어려움이었습니다. 새로운 기본 이미지 공유 기능을 사용하면 이제 실험 기본 이미지, 종속성 및 모두를 유추에 다시 사용할 수 있습니다. 이렇게 하면 배포 속도가 빨라지고 내부 루프에서 외부 루프까지의 간격이 줄어듭니다.

+ **향상된 Swagger 스키마 생성 환경**<br/> 이전의 swagger 생성 방법은 오류가 발생하기 쉬우며 자동화할 수 없었습니다. 데코레이터를 통해 Python 함수에서 swagger 스키마를 생성하는 새로운 인라인 방법이 있습니다. 이 코드는 오픈 소스화되었으며 스키마 생성 프로토콜이 Azure ML 플랫폼에 결합되지 않았습니다.

+ **Azure ML CLI가 일반 공급됨(GA)**<br/> 이제 단일 CLI 명령을 통해 모델을 배포할 수 있습니다. Jupyter Notebook에서 ML 모델을 배포하는 사람이 없다는 공통된 고객 피드백을 받았습니다. [**CLI 참조 설명서**](./reference-azure-machine-learning-cli.md)가 업데이트되었습니다.


## <a name="2019-04-22"></a>2019-04-22

Python용 Azure Machine Learning SDK v1.0.30이 출시되었습니다.

[`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint)는 동일한 엔드포인트를 유지하면서 게시된 파이프라인의 새 버전을 추가하기 위해 도입되었습니다.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure portal
  + 이제 기존 원격 컴퓨팅 클러스터에서 실행되는 기존 스크립트를 다시 제출할 수 있습니다.
  + 이제 파이프라인 탭에서 새 매개 변수를 통해 게시된 파이프라인을 실행할 수 있습니다.
  + 이제 실행 세부 정보에서 새 스냅샷 파일 뷰어를 지원합니다. 특정 실행을 제출한 경우 디렉터리 스냅샷을 볼 수 있습니다. 또한 제출된 Notebook을 다운로드하여 실행을 시작할 수도 있습니다.
  + 이제 Azure Portal에서 부모 실행을 취소할 수 있습니다.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Python용 Azure Machine Learning SDK v1.0.23

+ **새로운 기능**
  + Azure Machine Learning SDK는 이제 Python 3.7을 지원합니다.
  + Azure Machine Learning DNN 예측 도구는 이제 기본 제공 다중 버전 지원 기능을 제공합니다. 예를 들어 `TensorFlow` 예측 도구는 이제 `framework_version` 매개 변수를 허용하고 사용자는 '1.10' 또는 '1.12' 버전을 지정할 수 있습니다. 현재 SDK 릴리스에서 지원되는 버전 목록을 보려면 원하는 프레임워크 클래스(예: `TensorFlow.get_supported_versions()`)에서 `get_supported_versions()`를 호출합니다.
  최신 SDK 릴리스에서 지원되는 버전 목록은 [DNN 예측 도구 설명서](/python/api/azureml-train-core/azureml.train.dnn)를 참조하세요.

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Python용 Azure Machine Learning SDK v1.0.21

+ **새로운 기능**
  + *azureml.core.Run.create_children* 메서드를 사용하면 단일 호출로 여러 자식 실행을 만들 수 있으며 대기 시간이 짧습니다.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Python용 Azure Machine Learning SDK v1.0.18

 + **변경**
   + azureml-tensorboard 패키지는 azureml-contrib-tensorboard를 대체합니다.
   + 이 릴리스에서는 관리형 컴퓨팅 클러스터(amlcompute)를 만드는 동안 사용자 계정을 설정할 수 있습니다. 프로비저닝 구성에 이러한 속성을 전달하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [SDK 참조 설명서](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)에서 확인할 수 있습니다.

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **새로운 기능**
  + 이제 식 언어를 사용하여 결과 열을 생성하는 두 개의 숫자 열을 추가할 수 있습니다.

+ **버그 수정 및 개선 사항**
  + random_split에 대한 설명서 및 매개 변수 확인이 개선되었습니다.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **버그 수정**
  + API 변경으로 인해 발생한 서비스 주체 인증 문제를 해결했습니다.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Python용 Azure Machine Learning SDK v1.0.17

+ **새로운 기능**
  + 이제 Azure Machine Learning은 인기 있는 DNN 프레임워크인 Chainer에 대한 첫 번째 클래스 지원을 제공합니다. 사용자는 [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) 클래스를 사용하여 Chainer 모델을 쉽게 학습시키고 배포할 수 있습니다.
    + [ChainerMN을 사용하여 분산 학습을 실행](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)하는 방법 알아보기
    + [Chainer에서 HyperDrive를 사용하여 하이퍼 매개 변수 튜닝을 실행](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)하는 방법 알아보기
  + Azure Machine Learning 파이프라인은 데이터 저장소 수정 내용에 따라 파이프라인 실행을 트리거하는 기능을 추가했습니다. [일정 Notebook](https://aka.ms/pl-schedule) 파이프라인이 이 기능을 소개하도록 업데이트되었습니다.

+ **버그 수정 및 개선 사항**
  + [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)에 제공되는 [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration)에서 source_directory_data_store 속성을 원하는 데이터 저장소(예: Blob Storage)로 설정하기 위한 지원 기능이 Azure Machine Learning 파이프라인에 추가되었습니다. 기본적으로 단계는 Azure 파일 저장소를 백업 데이터 저장소로 사용하며, 많은 수의 단계가 동시에 실행될 때 제한 문제가 발생할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

+ **새로운 기능**
  + 보고서에 적용할 수 있는 새로운 끌어서 놓기 방식의 테이블 편집기 환경. 사용자는 웰에서 테이블의 미리 보기가 표시될 테이블 영역으로 열을 끌 수 있습니다. 열을 다시 정렬할 수 있습니다.
  + 새 로그 파일 뷰어
  + 활동 탭에서 실험 실행, 컴퓨팅, 모델, 이미지 및 배포에 대한 링크

## <a name="next-steps"></a>다음 단계

[Azure Machine Learning](overview-what-is-azure-machine-learning.md)에 대한 개요를 참조하세요.
