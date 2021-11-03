---
title: 학습 및 추론 컴퓨팅 대상 설정
titleSuffix: Azure Machine Learning
description: 기계 학습 학습 및 유추에 사용할 컴퓨팅 리소스(컴퓨팅 대상)를 작업 영역에 추가합니다.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.custom: devx-track-python, contperf-fy21q1, ignite-fall-2021
ms.openlocfilehash: 118276b2f455d54a7cc87d1336d6c3c5ea12f09a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056493"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>모델 학습 및 배포를 위한 컴퓨팅 대상 설정

Azure 컴퓨팅 리소스를 Azure Machine Learning 작업 영역에 연결하는 방법을 알아봅니다.  그런 다음 머신 러닝 작업에서 이러한 리소스를 학습 및 추론 [컴퓨팅 대상](concept-compute-target.md)으로 사용할 수 있습니다.

이 문서에서는 이러한 컴퓨팅 리소스를 사용하도록 작업 영역을 설정하는 방법에 대해 알아봅니다.

* 로컬 컴퓨터
* 원격 가상 머신
* Apache Spark 풀(Azure Synapse Analytics에서 제공)
* Azure HDInsight
* Azure Batch
* Azure Databricks - [기계 학습 파이프라인](how-to-create-machine-learning-pipelines.md)에서만 학습 컴퓨팅 대상으로 사용됨
* Azure 데이터 레이크 분석
* Azure Container Instance
* Azure Kubernetes Service & Azure Arc 지원 Kubernetes(미리 보기)

Azure Machine Learning에서 관리하는 컴퓨팅 대상을 사용하려면 다음을 참조하세요.

* [Azure Machine Learning 컴퓨팅 인스턴스](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning 컴퓨팅 클러스터](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service 클러스터](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

* [Machine Learning 서비스에 대한 Azure CLI 확장](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-setup-vs-code.md).

## <a name="limitations"></a>제한 사항

* 작업 영역에서 **동일한 컴퓨팅에 대한 여러 개의 동시 첨부 파일을 만들지 마세요**. 예를 들어 두 개의 다른 이름을 사용하여 하나의 Azure Kubernetes 서비스 클러스터를 작업 영역에 연결합니다. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.

    컴퓨팅 대상을 다시 연결하려는 경우(예: TLS 또는 다른 클러스터 구성 설정을 변경) 먼저 기존 첨부 파일을 제거해야 합니다.

## <a name="whats-a-compute-target"></a>컴퓨팅 대상이란?

Azure Machine Learning을 사용하여 다양한 리소스 또는 환경(총체적으로 [__컴퓨팅 대상__](concept-azure-machine-learning-architecture.md#compute-targets)이라고 함)에서 모델을 학습시킬 수 있습니다. 컴퓨팅 대상은 로컬 컴퓨터 또는 클라우드 리소스(예: Azure Machine Learning 컴퓨팅, Azure HDInsight 또는 원격 가상 머신)입니다.  [“모델 배포 위치 및 방법”](how-to-deploy-and-where.md)에 설명된 대로 모델 배포를 위해 컴퓨팅 대상을 만들 수도 있습니다.


## <a name="local-computer"></a><a id="local"></a>로컬 컴퓨터

로컬 컴퓨터를 **학습** 에 사용하는 경우 컴퓨팅 대상을 만들 필요가 없습니다.  로컬 머신에서 [학습 실행을 제출](how-to-set-up-training-targets.md)하기만 하면 됩니다.

**추론** 을 위해 로컬 컴퓨터를 사용하는 경우 Docker가 설치되어 있어야 합니다. 배포를 수행하려면 [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-)을 사용하여 웹 서비스에서 사용하는 포트를 정의합니다. 그런 다음 [Azure Machine Learning을 사용하여 모델 배포](how-to-deploy-and-where.md)에 설명된 대로 일반적인 배포 프로세스를 사용합니다.

## <a name="remote-virtual-machines"></a><a id="vm"></a>원격 가상 머신

Azure Machine Learning은 Azure 가상 머신 연결도 지원합니다. VM은 Azure DSVM(Data Science Virtual Machine)이어야 합니다. 이 VM은 전체 수명 주기 기계 학습을 위해 큐레이팅된 도구 및 프레임워크 옵션을 제공합니다. Azure Machine Learning과 함께 DSVM을 사용하는 방법에 관한 자세한 내용은 [개발 환경 구성](./how-to-configure-environment.md#dsvm)을 참조하세요.

> [!TIP]
> 원격 VM 대신 [Azure Machine Learning 컴퓨팅 인스턴스](concept-compute-instance.md)를 사용하는 것이 좋습니다. Azure Machine Learning에 특화된 완전 관리형 클라우드 기반 컴퓨팅 솔루션입니다. 자세한 내용은 [Azure Machine Learning 컴퓨팅 인스턴스 만들기 및 관리](how-to-create-manage-compute-instance.md)를 참조하세요.

1. **만들기**: Azure Machine Learning은 원격 VM을 만들 수 없습니다. 대신 VM을 만든 다음 이를 Azure Machine Learning 작업 영역에 연결해야 합니다. DSVM을 만드는 데 대한 자세한 내용은 [Linux(Ubuntu)용 Data Science Virtual Machine 프로비저닝](./data-science-virtual-machine/dsvm-ubuntu-intro.md)을 참조하세요.

    > [!WARNING]
    > Azure Machine Learning은 **Ubuntu** 를 실행하는 가상 머신만 지원합니다. VM을 만들거나 기존 VM을 선택하는 경우 Ubuntu를 사용하는 VM을 선택해야 합니다.
    > 
    > 또한 Azure Machine Learning을 사용하려면 가상 머신에 __공용 IP 주소__ 가 있어야 합니다.

1. **연결**: 기존 가상 머신을 컴퓨팅 대상으로 연결하려면 가상 머신의 리소스 ID, 사용자 이름 및 암호를 입력해야 합니다. VM의 리소스 ID는 구독 ID, 리소스 그룹 이름 및 VM 이름을 사용하여 `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>` 문자열 형식으로 생성할 수 있습니다.

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Machine Learning 스튜디오를 사용](how-to-create-attach-compute-studio.md#attached-compute)하여 작업 영역에 DSVM을 연결할 수도 있습니다.

    > [!WARNING]
    > 작업 영역에서 동일한 DSVM에 대한 여러 개의 동시 첨부 파일을 만들지 마세요. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.

1. **구성**: DSVM 컴퓨팅 대상에 대한 실행 구성을 만듭니다. Docker 및 conda는 DSVM에서 학습 환경을 만들고 구성하는 데 사용됩니다.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

> [!TIP]
> 작업 영역에서 VM을 __제거__(분리)하려면 [RemoteCompute.detach()](/python/api/azureml-core/azureml.core.compute.remotecompute#detach--) 메서드를 사용합니다.
>
> Azure Machine Learning은 VM을 자동으로 삭제하지 않습니다. Azure Portal, CLI 또는 Azure VM 용 SDK를 사용하여 VM을 수동으로 삭제해야 합니다.

## <a name="apache-spark-pools"></a><a id="synapse"></a>Apache Spark 풀

Azure Machine Learning과 Azure Synapse Analytics를 통합(미리 보기)하면 대화형 데이터 탐색 및 준비를 위해 Azure Synapse에서 지원하는 Apache Spark 풀을 연결할 수 있습니다. 이러한 통합을 통해 대규모 데이터 랭글링을 위한 전용 컴퓨팅을 사용할 수 있습니다. 자세한 내용은 [Azure Synapse Analytics에서 제공하는 Apache Spark 풀을 연결하는 방법](how-to-link-synapse-ml-workspaces.md#attach-synapse-spark-pool-as-a-compute)을 참조하세요.

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight는 빅 데이터 분석을 위한 인기 있는 플랫폼입니다. 플랫폼은 모델을 학습하는 데 사용할 수 있는 Apache Spark를 제공합니다.

1. **만들기**: Azure Machine Learning은 자동으로 HDInsight 클러스터를 만들 수 없습니다. 대신 클러스터를 만든 다음 이를 Azure Machine Learning 작업 영역에 연결해야 합니다. 자세한 내용은 [HDInsight에서 Spark 클러스터 만들기](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)를 참조하세요. 

    > [!WARNING]
    > Azure Machine Learning을 사용하려면 HDInsight 클러스터에 __공용 IP 주소__ 가 있어야 합니다.

    클러스터를 만들 때 SSH 사용자 이름 및 암호를 지정해야 합니다. 이러한 값은 HDInsight를 컴퓨팅 대상으로 사용할 때 필요하므로 기록해 둡니다.
    
    클러스터를 만든 후에 호스트 이름 \<clustername>-ssh.azurehdinsight.net을 사용하여 연결합니다. 여기서 \<clustername>은 클러스터에 제공한 이름입니다. 

1. **연결**: HDInsight 클러스터를 컴퓨팅 대상으로 연결하려면 HDInsight 클러스터의 리소스 ID, 사용자 이름 및 암호를 입력해야 합니다. HDInsight 클러스터의 리소스 ID는 구독 ID, 리소스 그룹 이름 및 HDInsight 클러스터 이름을 사용하여 `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>` 문자열 형식으로 생성할 수 있습니다.

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   또는 [Azure Machine Learning 스튜디오를 사용](how-to-create-attach-compute-studio.md#attached-compute)하여 작업 영역에 HDInsight 클러스터를 연결할 수도 있습니다.

    > [!WARNING]
    > 작업 영역에서 동일한 HDInsight에 대한 여러 개의 동시 첨부 파일을 만들지 마세요. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.

1. **구성**: HDI 컴퓨팅 대상에 대한 실행 구성을 만듭니다. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]

> [!TIP]
> 작업 영역에서 HDInsight 클러스터를 __제거__(분리)하려면 [HDInsightCompute.detach()](/python/api/azureml-core/azureml.core.compute.hdinsight.hdinsightcompute#detach--) 메서드를 사용합니다.
>
> Azure Machine Learning은 HDInsight 클러스터를 자동으로 삭제하지 않습니다. Azure Portal, CLI 또는 Azure HDInsight용 SDK를 사용하여 수동으로 삭제해야 합니다.

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch는 클라우드에서 대규모 병렬 및 HPC(고성능 컴퓨팅) 애플리케이션을 효율적으로 실행하는 데 사용됩니다. AzureBatchStep은 Azure Machine Learning 파이프라인에 사용하여 머신의 Azure Batch 풀에 작업을 제출할 수 있습니다.

Azure Batch를 컴퓨팅 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

-    **Azure Batch 컴퓨팅 이름**: 작업 영역 내에서 컴퓨팅에 사용되는 식별 이름입니다.
-    **Azure Batch 계정 이름**: Azure Batch 계정의 이름
-    **리소스 그룹**: Azure Batch 계정을 포함하는 리소스 그룹입니다.

다음 코드는 컴퓨팅 대상으로 Azure Batch를 연결하는 방법을 보여줍니다.

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> 작업 영역에서 동일한 Azure Batch에 대한 여러 개의 동시 첨부 파일을 만들지 마세요. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.

## <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks는 Azure 클라우드의 Apache Spark 기반 환경입니다. 이 환경은 Azure Machine Learning 파이프라인 사용 시 컴퓨팅 대상으로 사용할 수 있습니다.

> [!IMPORTANT]
> Azure Machine Learning은 Azure Databricks 컴퓨팅 대상을 만들 수 없습니다. 대신 Azure Databricks 작업 영역을 만든 다음 이를 Azure Machine Learning 작업에 연결해야 합니다. 작업 영역 리소스를 만들려면 [Azure Databricks에서 Spark 작업 실행](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) 문서를 참조하세요.
> 
> __다른 Azure 구독__ 에서 Azure Databricks 작업 영역을 연결하려면 사용자(Azure AD 계정)에게 Azure Databricks 작업 영역에 대한 **기여자** 역할이 부여되어야 합니다. [Azure Portal](https://ms.portal.azure.com/)에서 액세스를 확인합니다.

Azure Databricks를 컴퓨팅 대상으로 연결하려면 다음 정보를 제공합니다.

* __Databricks 컴퓨팅 이름__: 해당 컴퓨팅 리소스에 할당하려는 이름입니다.
* __Databricks 작업 영역 이름__: Azure Databricks 작업 영역의 이름입니다.
* __Databricks 액세스 토큰__: Azure Databricks에서 인증을 받는 데 사용하는 액세스 토큰입니다. 액세스 토큰을 생성하려면 [인증](/azure/databricks/dev-tools/api/latest/authentication) 문서를 참조하세요.

다음 코드는 Azure Machine Learning SDK를 사용하여 Azure Databricks를 컴퓨팅 대상으로 연결하는 방법을 보여줍니다.

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

자세한 예제는 GitHub의 [예제 노트북](https://aka.ms/pl-databricks)을 참조하세요.

> [!WARNING]
> 작업 영역에서 동일한 Azure Databricks에 대한 여러 개의 동시 첨부 파일을 만들지 마세요. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.

## <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure 데이터 레이크 분석

Azure Data Lake Analytics는 Azure 클라우드의 빅 데이터 분석 플랫폼입니다. 이 환경은 Azure Machine Learning 파이프라인 사용 시 컴퓨팅 대상으로 사용할 수 있습니다.

사용하기 전에 Azure Data Lake Analytics 계정을 만듭니다. 이 리소스를 만들려면 [Azure Data Lake Analytics 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md) 문서를 참조하세요.

Data Lake Analytics를 컴퓨팅 대상으로 연결하려면 Azure Machine Learning SDK를 사용하고 다음 정보를 제공해야 합니다.

* __컴퓨팅 이름__: 이 컴퓨팅 리소스에 할당하려는 이름입니다.
* __리소스 그룹__: Data Lake Analytics 계정을 포함하는 리소스 그룹입니다.
* __계정 이름__: Data Lake Analytics 계정 이름입니다.

다음 코드는 컴퓨팅 대상으로 Data Lake Analytics에 연결하는 방법을 보여 줍니다.

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

자세한 예제는 GitHub의 [예제 노트북](https://aka.ms/pl-adla)을 참조하세요.

> [!WARNING]
> 작업 영역에서 동일한 ADLA에 대한 여러 개의 동시 첨부 파일을 만들지 마세요. 각 새 첨부 파일은 이전의 기존 첨부 파일을 중단합니다.

> [!TIP]
> Azure Machine Learning 파이프라인은 Data Lake Analytics 계정의 기본 데이터 저장소에 저장된 데이터에만 작동할 수 있습니다. 사용해야 하는 데이터가 기본이 아닌 저장소에 있으면 학습 전에 [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep)을 사용하여 데이터를 복사할 수 있습니다.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instance

ACI(Azure Container Instances)는 모델을 배포할 때 동적으로 생성됩니다. 다른 방법으로는 ACI를 만들거나 작업 영역에 연결할 수 없습니다. 자세한 내용은 [Azure Container Instances에 모델 배포](how-to-deploy-azure-container-instance.md)를 참조하세요.

## <a name="kubernetes-preview"></a><a id="kubernetes"></a>Kubernetes(미리 보기)

Azure Machine Learning 학습 및 추론을 위해 사용자 고유의 Kubernetes 클러스터를 연결하는 다음 옵션을 제공합니다.

* [Azure Kubernetes Service](../aks/intro-kubernetes.md). Azure Kubernetes Service는 Azure에서 관리형 클러스터를 제공합니다.
* [Azure Arc Kubernetes](../azure-arc/kubernetes/overview.md) 클러스터가 Azure 외부에서 호스트되는 경우 Azure Arc 지원 Kubernetes 클러스터를 사용합니다.

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

작업 영역에서 Kubernetes 클러스터를 분리하려면 다음 방법을 사용합니다.

```python
compute_target.detach()
```

> [!WARNING]
> 클러스터를 분리해도 **클러스터는 삭제되지 않습니다**. Azure Kubernetes Service 클러스터를 삭제하려면 [AKS에서 Azure CLI 사용](../aks/kubernetes-walkthrough.md#delete-the-cluster)을 참조하세요. Azure Arc 지원 Kubernetes 클러스터를 삭제하려면 [Azure Arc 빠른 시작 을 참조하세요.](../azure-arc/kubernetes/quickstart-connect-cluster.md#7-clean-up-resources)

## <a name="notebook-examples"></a>Notebook 예제

이러한 Notebook에서 다양한 컴퓨팅 대상으로 학습하는 예를 참조하세요.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계

* 컴퓨팅 리소스를 사용하여 [학습 실행을 구성하고 제출](how-to-set-up-training-targets.md)합니다.
* [자습서: 모델 학습](tutorial-train-models-with-aml.md)은 모델 학습에 관리되는 컴퓨팅 대상을 사용합니다.
* [하이퍼 매개 변수를 효율적으로 튜닝](how-to-tune-hyperparameters.md)하여 보다 나은 모델을 빌드하는 방법을 알아봅니다.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [Azure Virtual Networks에서 Azure Machine Learning 사용](./how-to-network-security-overview.md)
