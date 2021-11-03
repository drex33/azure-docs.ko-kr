---
title: '자습서: 서버리스 Apache Spark 풀에서 PREDICT를 통해 기계 학습 모델 채점하기'
description: 기계 학습 모델을 통해 점수를 예측하기 위해 서버리스 Apache Spark 풀에서 PREDICT 기능을 사용하는 방법을 알아봅니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/02/2021
author: AjAgr
ms.author: ajagarw
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1ab5933dfecd5143865423601d119d84d93700ee
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030926"
---
# <a name="tutorial-score-machine-learning-models-with-predict-in-serverless-apache-spark-pools"></a>자습서: 서버리스 Apache Spark 풀에서 PREDICT를 통해 기계 학습 모델 채점하기

점수 예측을 위해 Azure Synapse Analytics의 서버리스 Apache Spark 풀에서 PREDICT 기능을 사용하는 방법을 알아봅니다. Synapse 작업 영역의 Azure Machine Learning(AML) 또는 기본 Azure Data Lake Storage(ADLS)에 등록된, 학습된 모델을 사용할 수 있습니다.

Synapse PySpark Notebook의 PREDICT는 SQL 언어, UDF(사용자 정의 함수) 또는 변환기를 사용하여 기계 학습 모델을 채점하는 기능을 제공합니다. PREDICT를 사용하면 Synapse 외부에서 학습되고 Azure Data Lake Storage Gen2 또는 Azure Machine Learning에 등록된 기존 기계 학습 모델을 가져와 Azure Synapse Analytics 보안 경계 내에서 기록 데이터를 채점할 수 있습니다. PREDICT 함수는 모델 및 데이터를 입력으로 사용합니다. 이 기능은 채점을 위해 Synapse 외부로 중요한 데이터를 이동하는 단계를 제거합니다. 목표는 모델 소비자가 Synapse에서 기계 학습 모델을 쉽게 유추하고 해당 작업에 적합한 프레임워크를 사용하여 작업하는 모델 생산자와 원활하게 협업할 수 있도록 하는 것입니다.


이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> - Synapse 외부에서 학습되고 Azure Machine Learning 또는 Azure Data Lake Storage Gen2에 등록된 기계 학습 모델을 사용하여 서버리스 Apache Spark 풀의 데이터에 대한 점수를 예측합니다.

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- Azure Synapse Analytics 작업 영역의 서버리스 Apache Spark 풀 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../get-started-analyze-spark.md)를 참조하세요.
- Azure Machine Learning의 모델을 학습하거나 등록하려면 Azure Machine Learning 작업 영역이 필요합니다. 자세한 내용은 [포털에서 또는 Python SDK를 사용하여 Azure Machine Learning 작업 영역 관리](../../machine-learning/how-to-manage-workspace.md)를 참조하세요.
- 모델이 Azure Machine Learning에 등록된 경우 연결된 서비스가 필요합니다. Azure Synapse Analytics에서 연결된 서비스는 서비스에 대한 연결 정보를 정의합니다. 이 자습서에서는 Azure Synapse Analytics 및 Azure Machine Learning 연결된 서비스를 추가합니다. 자세한 내용은 [Synapse에서 새로운 Azure Machine Learning 연결된 서비스 만들기](quickstart-integrate-azure-machine-learning.md)를 참조하세요.
- PREDICT 기능을 사용하려면 Azure Machine Learning에 등록되거나 Azure Data Lake Storage Gen2에 업로드된 학습된 모델이 있어야 합니다.

> [!NOTE]
> - PREDICT 기능은 Azure Synapse Analytics의 **Spark3** 서버리스 Apache Spark 풀에서 지원됩니다. **Python 3.8** 은 모델 생성 및 학습에 권장되는 버전입니다. 
> - PREDICT는 **MLflow** 형식의 대부분의 기계 학습 모델 패키지를 지원합니다. **TensorFlow, ONNX, PyTorch, SkLearn 및 pyfunc** 는 이 미리 보기에서 지원됩니다.
> - PREDICT는 **AML 및 ADLS** 모델 원본을 지원합니다. 여기서 ADLS 계정은 **기본 Synapse 작업 영역 ADLS 계정** 을 참조합니다. 


## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.


## <a name="use-predict-for-mlflow-packaged-models"></a>MLFLOW 패키지 모델에 PREDICT 사용

PREDICT를 사용하기 위해 다음 단계를 수행하기 전에 모든 필수 구성 조건이 있는지 확인합니다.

1. **라이브러리 가져오기:** Spark 세션에서 PREDICT를 사용하려면 다음 라이브러리를 가져옵니다.

   ```PYSPARK
      #Import libraries
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   ```

2. **변수를 사용하여 매개 변수 설정:** Synapse ADLS 데이터 경로 및 모델 URI는 입력 변수를 사용하여 설정해야 합니다. 또한 "mlflow"인 런타임과 모델 출력 반환의 데이터 형식을 정의해야 합니다. PySpark에서 지원되는 모든 데이터 형식은 PREDICT를 통해서도 지원됩니다.

   > [!NOTE]
   > 이 스크립트를 실행하기 전에 모델 출력 반환 데이터 형식 및 모델 파일에 대한 ADLS/AML URI와 함께 ADLS Gen2 데이터 파일에 대한 URI로 업데이트합니다.

   ```PYSPARK
      #Set input data path
      DATA_FILE = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<file path>"
   
      #Set model URI
          #Set AML URI, if trained model is registered in AML
             AML_MODEL_URI = "<aml model uri>" #In URI ":x" signifies model version in AML. You can   choose which model version you want to run. If ":x" is not provided then by default   latest version will be picked.
   
          #Set ADLS URI, if trained model is uploaded in ADLS
             ADLS_MODEL_URI = "abfss://<filesystemname>@<account name>.dfs.core.windows.net/<model   mlflow folder path>"
   
      #Define model return type
      RETURN_TYPES = "<data_type>" # for ex: int, float etc. PySpark data types are supported
   
      #Define model runtime. This supports only mlflow
      RUNTIME = "mlflow"
   ```

3. **AML 작업 영역을 인증하는 방법:** 모델이 Synapse 작업 영역의 기본 ADLS 계정에 저장된 경우 추가 인증 설정이 필요하지 않습니다. 모델이 Azure Machine Learning에 등록된 경우 지원되는 다음 두 가지 인증 방법 중 하나를 선택할 수 있습니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트에서 테넌트, 클라이언트, 구독, 리소스 그룹, AML 작업 영역 및 연결된 서비스 세부 정보를 업데이트합니다.

   - **서비스 주체를 통해:** 서비스 주체 클라이언트 ID 및 비밀을 직접 사용하여 AML 작업 영역에 인증할 수 있습니다. 서비스 주체는 AML 작업 영역에 대한 "기여자" 액세스 권한이 있어야 합니다.

      ```PYSPARK
         #AML workspace authentication using service principal
         AZURE_TENANT_ID = "<tenant_id>"
         AZURE_CLIENT_ID = "<client_id>"
         AZURE_CLIENT_SECRET = "<client_secret>"
  
         AML_SUBSCRIPTION_ID = "<subscription_id>"
         AML_RESOURCE_GROUP = "<resource_group_name>"
         AML_WORKSPACE_NAME = "<aml_workspace_name>"
  
         svc_pr = ServicePrincipalAuthentication( 
             tenant_id=AZURE_TENANT_ID,
             service_principal_id=AZURE_CLIENT_ID,
             service_principal_password=AZURE_CLIENT_SECRET
         )
  
         ws = Workspace(
             workspace_name = AML_WORKSPACE_NAME,
             subscription_id = AML_SUBSCRIPTION_ID,
             resource_group = AML_RESOURCE_GROUP,
             auth=svc_pr
         )
      ```

   - **연결된 서비스를 통해:** 연결된 서비스를 사용하여 AML 작업 영역에 인증할 수 있습니다. 연결된 서비스는 인증에 "서비스 주체" 또는 Synapse 작업 영역의 "MSI(관리 서비스 ID)"를 사용할 수 있습니다. "서비스 주체" 또는 "MSI(관리 서비스 ID)"에는 AML 작업 영역에 대한 "기여자" 액세스 권한이 있어야 합니다.

      ```PYSPARK
         #AML workspace authentication using linked service
         from notebookutils.mssparkutils import azureML
         ws = azureML.getWorkspace("<linked_service_name>") #   "<linked_service_name>" is the linked service name, not AML workspace name. Also, linked   service supports MSI and service principal both
      ```

4. **Spark 세션에서 PREDICT를 사용하도록 설정:** Spark 구성 `spark.synapse.ml.predict.enabled`를 `true`로 설정하여 라이브러리를 사용하도록 설정합니다.

   ```PYSPARK
      #Enable SynapseML predict
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

5. **Spark 세션에서 모델 바인딩:** Spark 세션에서 모델을 참조할 수 있도록 모델을 필수 입력과 바인딩합니다. 또한 PREDICT 호출에서 동일한 별칭을 사용할 수 있도록 별칭을 정의합니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트에서 모델 별칭 및 모델 URI를 업데이트합니다.

   ```PYSPARK
      #Bind model within Spark session
       model = pcontext.bind_model(
        return_types=RETURN_TYPES, 
        runtime=RUNTIME, 
        model_alias="<random_alias_name>", #This alias will be used in PREDICT call to refer  this   model
        model_uri=ADLS_MODEL_URI, #In case of AML, it will be AML_MODEL_URI
        aml_workspace=ws #This is only for AML. In case of ADLS, this parameter can be removed
        ).register()
   ```

6. **ADLS에서 데이터 읽기:** ADLS에서 데이터를 읽습니다. Spark 데이터 프레임 및 데이터 프레임 위에 뷰를 만듭니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트의 뷰 이름을 업데이트합니다.

   ```PYSPARK
      #Read data from ADLS
      df = spark.read \
       .format("csv") \
       .option("header", "true") \
       .csv(DATA_FILE,
           inferSchema=True)
      df.createOrReplaceTempView('<view_name>')
   ```

7. **PREDICT를 사용하여 점수 생성:** Spark SQL API, UDF(사용자 정의 함수) 및 변환기 API를 사용하는 세 가지 방법으로 PREDICT를 호출할 수 있습니다. 예제는 다음과 같습니다.

   > [!NOTE]
   > 실행하기 전에 이 스크립트에서 모델 별칭 이름, 뷰 이름 및 쉼표로 구분된 모델 입력 열 이름을 업데이트합니다. 쉼표로 구분된 모델 입력 열은 모델을 학습하는 동안 사용된 열과 동일합니다.

   ```PYSPARK
      #Call PREDICT using Spark SQL API
   
      predictions = spark.sql(
                     """
                         SELECT PREDICT('<random_alias_name>',
                                   <comma_separated_model_input_column_name>) AS predict 
                         FROM <view_name>
                     """
                 ).show()
   ```

   ```PYSPARK
      #Call PREDICT using user defined function (UDF)
   
      df = df[<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
   
      df.withColumn("PREDICT",model.udf(lit("<random_alias_name>"),*df.columns)).show()
   ```

   ```PYSPARK
      #Call PREDICT using Transformer API
      
      columns = [<comma_separated_model_input_column_name>] # for ex. df["empid","empname"]
      
      tranformer = model.create_transformer().setInputCols(columns).setOutputCol("PREDICT")
   
      tranformer.transform(df).show()
   ```

## <a name="sklearn-example-using-predict"></a>PREDICT를 사용하는 Sklearn 예제

1. 라이브러리를 가져오고 ADLS에서 학습 데이터 세트를 읽습니다.

   ```PYSPARK
      # Import libraries and read training dataset from ADLS
      
      import fsspec
      import pandas
      from fsspec.core import split_protocol
      
      adls_account_name = 'xyz' #Provide exact ADLS account name
      adls_account_key = 'xyz' #Provide exact ADLS account key
      
      fsspec_handle = fsspec.open('abfs[s]://<container>/<path-to-file>',      account_name=adls_account_name, account_key=adls_account_key)
      
      with fsspec_handle.open() as f:
          train_df = pandas.read_csv(f)
   ```

1. 모델을 학습시키고 mlflow 아티팩트를 생성합니다.

   ```PYSPARK
      # Train model and generate mlflow artifacts
   
      import os
      import shutil
      import mlflow
      import json
      from mlflow.utils import model_utils
      import numpy as np
      import pandas as pd
      from sklearn.linear_model import LinearRegression
      
      
      class LinearRegressionModel():
        _ARGS_FILENAME = 'args.json'
        FEATURES_KEY = 'features'
        TARGETS_KEY = 'targets'
        TARGETS_PRED_KEY = 'targets_pred'
      
        def __init__(self, fit_intercept, nb_input_features=9, nb_output_features=1):
          self.fit_intercept = fit_intercept
          self.nb_input_features = nb_input_features
          self.nb_output_features = nb_output_features
      
        def get_args(self):
          args = {
              'nb_input_features': self.nb_input_features,
              'nb_output_features': self.nb_output_features,
              'fit_intercept': self.fit_intercept
          }
          return args
      
        def create_model(self):
          self.model = LinearRegression(fit_intercept=self.fit_intercept)
      
        def train(self, dataset):
      
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
      
          targets = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.TARGETS_KEY])], axis=0)
      
      
          self.model.fit(features, targets)
      
        def predict(self, dataset):
          features = np.stack([sample for sample in iter(
              dataset[LinearRegressionModel.FEATURES_KEY])], axis=0)
          targets_pred = self.model.predict(features)
          return targets_pred
      
        def save(self, path):
          if os.path.exists(path):
            shutil.rmtree(path)
      
          # save the sklearn model with mlflow
          mlflow.sklearn.save_model(self.model, path)
      
          # save args
          self._save_args(path)
      
        def _save_args(self, path):
          args_filename = os.path.join(path, LinearRegressionModel._ARGS_FILENAME)
          with open(args_filename, 'w') as f:
            args = self.get_args()
            json.dump(args, f)
      
      
      def train(train_df, output_model_path):
        print(f"Start to train LinearRegressionModel.")
      
        # Initialize input dataset
        dataset = train_df.to_numpy()
        datasets = {}
        datasets['targets'] = dataset[:, -1]
        datasets['features'] = dataset[:, :9]
      
        # Initialize model class obj
        model_class = LinearRegressionModel(fit_intercept=10)
        with mlflow.start_run(nested=True) as run:
          model_class.create_model()
          model_class.train(datasets)
          model_class.save(output_model_path)
          print(model_class.predict(datasets))
      
      
      train(train_df, './artifacts/output')
   ```

1. 모델 MLFLOW 아티팩트를 ADLS에 저장하거나 AML에 등록합니다.

   ```PYSPARK
      # Store model MLFLOW artifacts in ADLS
      
      STORAGE_PATH = 'abfs[s]://<container>/<path-to-store-folder>'
      
      protocol, _ = split_protocol(STORAGE_PATH)
      print (protocol)
   
      storage_options = {
          'account_name': adls_account_name,
          'account_key': adls_account_key
      }
      fs = fsspec.filesystem(protocol, **storage_options)
      fs.put(
          './artifacts/output',
          STORAGE_PATH, 
          recursive=True, overwrite=True)
   ```

   ```PYSPARK
      # Register model MLFLOW artifacts in AML
      
      from azureml.core import Workspace, Model
      from azureml.core.authentication import ServicePrincipalAuthentication
      
      AZURE_TENANT_ID = "xyz"
      AZURE_CLIENT_ID = "xyz"
      AZURE_CLIENT_SECRET = "xyz"
      
      AML_SUBSCRIPTION_ID = "xyz"
      AML_RESOURCE_GROUP = "xyz"
      AML_WORKSPACE_NAME = "xyz"
      
      svc_pr = ServicePrincipalAuthentication( 
          tenant_id=AZURE_TENANT_ID,
          service_principal_id=AZURE_CLIENT_ID,
          service_principal_password=AZURE_CLIENT_SECRET
      )
      
      ws = Workspace(
          workspace_name = AML_WORKSPACE_NAME,
          subscription_id = AML_SUBSCRIPTION_ID,
          resource_group = AML_RESOURCE_GROUP,
          auth=svc_pr
      )
      
      model = Model.register(
          model_path="./artifacts/output",
          model_name="xyz",
          workspace=ws,
      )
   ```

1. 변수를 사용하여 필수 매개 변수를 설정합니다.

   ```PYSPARK
      # If using ADLS uploaded model
   
      import pandas as pd
      from pyspark.sql import SparkSession
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      ADLS_MODEL_URI_SKLEARN = "abfss://xyz@xyz.dfs.core.windows.net/mlflow/sklearn/     e2e_linear_regression/"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

   ```PYSPARK
      # If using AML registered model
   
      from pyspark.sql.functions import col, pandas_udf,udf,lit
      from azureml.core import Workspace
      from azureml.core.authentication import ServicePrincipalAuthentication
      import azure.synapse.ml.predict as pcontext
      import azure.synapse.ml.predict.utils._logger as synapse_predict_logger
   
      DATA_FILE = "abfss://xyz@xyz.dfs.core.windows.net/xyz.csv"
      AML_MODEL_URI_SKLEARN = "aml://xyz"
      RETURN_TYPES = "INT"
      RUNTIME = "mlflow"
   ```

1. Spark 세션에서 SynapseML PREDICT 기능을 사용하도록 설정합니다.

   ```PYSPARK
      spark.conf.set("spark.synapse.ml.predict.enabled","true")
   ```

1. Spark 세션에서 모델을 바인딩합니다.

   ```PYSPARK
      # If using ADLS uploaded model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=ADLS_MODEL_URI_SKLEARN,
       ).register()
   ```

   ```PYSPARK
      # If using AML registered model
   
      model = pcontext.bind_model(
       return_types=RETURN_TYPES, 
       runtime=RUNTIME, 
       model_alias="sklearn_linear_regression",
       model_uri=AML_MODEL_URI_SKLEARN,
       aml_workspace=ws
       ).register()
   ```

1. ADLS에서 테스트 데이터를 로드합니다.

   ```PYSPARK
      # Load data from ADLS
   
      df = spark.read \
          .format("csv") \
          .option("header", "true") \
          .csv(DATA_FILE,
              inferSchema=True)
      df = df.select(df.columns[:9])
      df.createOrReplaceTempView('data')
      df.show(10)
   ```

1. PREDICT를 호출하여 점수를 생성합니다.

   ```PYSPARK
      # Call PREDICT
      
      predictions = spark.sql(
                        """
                            SELECT PREDICT('sklearn_linear_regression', *) AS predict FROM data
                        """
                    ).show()
   ```


## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)
