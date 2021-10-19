---
title: 장애 조치(failover) 및 재해 복구
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대한 재해 복구를 계획하고 비즈니스 연속성을 유지 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 05/05/2021
ms.openlocfilehash: fc13ac868f2abb1ccd466b245c22682af79ba915
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161276"
---
# <a name="failover-for-business-continuity-and-disaster-recovery"></a>비즈니스 연속성 및 재해 복구를 위한 장애 조치(failover)

작동 시간을 최대화하려면 Azure Machine Learning을 사용하여 비즈니스 연속성을 유지 관리하고 재해 복구를 준비하도록 미리 계획합니다. 

Microsoft는 Azure 서비스를 항상 사용할 수 있도록 하기 위해 노력합니다. 그러나 계획되지 않은 서비스 중단이 발생할 수 있습니다. 지역 서비스 중단을 처리하기 위한 재해 복구 계획을 세우는 것이 좋습니다. 이 문서에서는 다음을 수행하는 방법을 알아봅니다.

* Azure Machine Learning 및 연결된 리소스에 대한 다중 지역 배포를 계획합니다.
* 솔루션의 고가용성을 보장하도록 디자인합니다.
* 다른 지역으로 장애 조치를 시작합니다.

> [!NOTE]
> Azure Machine Learning 자체는 자동 장애 조치 또는 재해 복구를 제공하지 않습니다.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Azure Machine Learning에 대한 Azure 서비스 이해

Azure Machine Learning은 여러 Azure 서비스를 사용하며 여러 계층을 포함합니다. 해당 서비스 중 일부는 (고객) 구독에서 프로비저닝됩니다. 사용자는 이 서비스의 고가용성 구성을 수행해야 합니다. 다른 서비스는 Microsoft 구독에서 만들어지고 Microsoft에서 관리됩니다. 

Azure 서비스는 다음을 포함합니다.

* **Azure Machine Learning 인프라**: Azure Machine Learning 작업 영역에 대한 Microsoft 관리형 환경입니다.

* **연결된 리소스**: Azure Machine Learning 작업 영역을 만드는 동안 구독에서 프로비저닝된 리소스입니다. 이러한 리소스로는 Azure Storage, Azure Key Vault, Azure Container Registry, Application Insights 등이 있습니다. 사용자는 이 리소스의 고가용성 설정을 구성해야 합니다.
  * 기본 스토리지에는 모델, 학습 로그 데이터, 데이터 세트와 같은 데이터가 있습니다.
  * Key Vault에는 Azure Storage, Container Registry, 데이터 저장소의 자격 증명이 있습니다.
  * Container Registry에는 학습 및 추론 환경의 Docker 이미지가 있습니다.
  * Application Insights는 Azure Machine Learning 모니터링에 사용됩니다.

* **컴퓨팅 리소스**: 작업 영역 배포 후에 만드는 리소스입니다. 예를 들어, 컴퓨팅 인스턴스 또는 컴퓨팅 클러스터를 만들어 Machine Learning 모델을 학습시킬 수 있습니다.
  * 컴퓨팅 인스턴스 및 컴퓨팅 클러스터: Microsoft 관리형 모델 개발 환경입니다.
  * 기타 리소스: AKS(Azure Kubernetes Service), Azure Databricks, Azure Container Instances 및 Azure HDInsight와 같이 Azure Machine Learning에 연결할 수 있는 Microsoft 컴퓨팅 리소스입니다. 사용자는 이 리소스의 고가용성 설정을 구성해야 합니다.

* **기타 데이터 저장소**: Azure Machine Learning은 학습 데이터에 대한 Azure Storage, Azure Data Lake Storage 및 Azure SQL Database 같은 다른 데이터 저장소를 탑재할 수 있습니다.  이 데이터 저장소는 구독 내에서 프로비저닝됩니다. 사용자는 고가용성 설정을 구성해야 합니다.

다음 표에서는 Microsoft 및 사용자가 관리하는 Azure 서비스를 보여 줍니다. 또한 기본적으로 가용성이 높은 서비스를 나타냅니다.

| 서비스 | 관리자 | 기본적으로 고가용성 |
| ----- | ----- | ----- |
| **Azure Machine Learning 인프라** | Microsoft | |
| **연결된 리소스** |
| Azure Storage | 사용자 | |
| Key Vault | 사용자 | ✓ |
| Container Registry | 사용자 | |
| Application Insights | 사용자 | 해당 없음 |
| **컴퓨팅 리소스** |
| 컴퓨팅 인스턴스 | Microsoft |  |
| 컴퓨팅 클러스터 | Microsoft |  |
| AKS와 같은 기타 컴퓨팅 리소스 <br>Azure Databricks, Container Instances, HDInsight | 사용자 |  |
| **기타 데이터 저장소**, 예: Azure Storage, SQL Database,<br> Azure Database for PostgreSQL, Azure Database for MySQL, <br>Azure Databricks File System | 사용자 | |

이 문서의 나머지 부분에서는 이러한 각 서비스의 가용성을 높이기 위해 수행해야 하는 작업을 설명합니다.

## <a name="plan-for-multi-regional-deployment"></a>다중 지역 배포 계획

다중 지역 배포를 사용하려면 두 개의 Azure 지역에서 Azure Machine Learning 및 기타 리소스(인프라)를 만들어야 합니다. 지역 중단이 발생하는 경우 다른 지역으로 전환할 수 있습니다. 리소스를 배포할 위치를 계획하는 경우 다음을 고려합니다.

* __지역별 가용성__: 사용자에게 가까운 지역을 사용합니다. Azure Machine Learning의 지역별 가용성을 확인하려면 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.
* __Azure 쌍을 이루는 지역__: 쌍을 이루는 지역은 플랫폼 업데이트를 조정하고 필요한 경우 복구 작업의 우선 순위를 지정합니다. 자세한 내용은 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요.
* __서비스 가용성__: 솔루션에서 사용하는 리소스가 핫/핫, 핫/웜 또는 핫/콜드여야 하는지 여부를 결정합니다.
    
    * __핫/핫__: 두 지역이 동시에 모두 활성 상태이며 한 지역이 즉시 사용할 준비가 되었습니다.
    * __핫/웜__: 주 지역이 활성 상태이며 보조 지역에는 중요한 리소스(예: 배포된 모델)를 시작할 준비가 되었습니다. 중요하지 않은 리소스는 보조 지역에서 수동으로 배포해야 합니다.
    * __핫/콜드__: 주 지역이 활성 상태이며 보조 지역에는 필요한 데이터와 함께 Azure Machine Learning 및 기타 리소스가 배포되어 있습니다. 모델, 모델 배포 또는 파이프라인과 같은 리소스는 수동으로 배포해야 합니다.

> [!TIP]
> 비즈니스 요구 사항에 따라 다양한 Azure Machine Learning 리소스를 다르게 처리하도록 결정할 수 있습니다. 예를 들어, 배포된 모델(유추)에 핫/핫을 사용하고 실험(학습)에 핫/콜드를 사용하는 것이 좋습니다.

Azure Machine Learning은 다른 서비스를 기반으로 빌드됩니다. 일부 서비스는 다른 지역에 복제되도록 구성될 수 있습니다. 다른 서비스의 경우 여러 지역에서 수동으로 만들어야 합니다. 다음 표에서는 복제를 수행해야 하는 서비스 목록과 구성의 개요를 제공합니다.

| Azure 서비스 | 지역에서 복제 수행 | 구성 |
| ----- | ----- | ----- |
| Machine Learning 작업 영역 | 사용자 | 선택한 지역에 작업 영역을 만듭니다. |
| Machine Learning 컴퓨팅 | 사용자 | 선택한 지역에 컴퓨팅 리소스를 만듭니다. 동적으로 스케일링할 수 있는 컴퓨팅 리소스의 경우 두 지역이 모두 요구 사항에 충분한 컴퓨팅 할당량을 제공하는지 확인합니다. |
| Key Vault | Microsoft | 두 지역에서 모두 Azure Machine Learning 작업 영역과 리소스가 있는 동일한 Key Vault 인스턴스를 사용합니다. Key Vault는 보조 지역으로 자동으로 장애 조치됩니다. 자세한 내용은 [Azure Key Vault 가용성 및 중복성](../key-vault/general/disaster-recovery-guidance.md)을 참조하세요.|
| Container Registry | Microsoft | Azure Machine Learning의 쌍을 이루는 지역에 레지스트리를 지역에서 복제하도록 Container Registry 인스턴스를 구성합니다. 두 작업 영역 인스턴스에 모두 동일한 인스턴스를 사용합니다. 자세한 내용은 [Azure Container Registry의 지역에서 복제](../container-registry/container-registry-geo-replication.md)를 참조하세요. |
| 스토리지 계정 | 사용자 | Azure Machine Learning은 GRS(지역 중복 스토리지), GZRS(지역 영역 중복 스토리지), RA-GRS(읽기 액세스 지역 중복 스토리지) 또는 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)를 사용하는 __기본 스토리지 계정__ 장애 조치를 지원하지 않습니다. 각 작업 영역의 기본 스토리지에 대해 별도의 스토리지 계정을 만듭니다. </br>다른 데이터 스토리지에 대해 별도의 스토리지 계정 또는 서비스를 만듭니다. 자세한 내용은 [Azure Storage 중복성](../storage/common/storage-redundancy.md)을 참조하세요. |
| Application Insights | 사용자 | 두 지역에서 모두 작업 영역에 대해 Application Insights를 만듭니다. 데이터 보존 기간과 세부 정보를 조정하려면 [Application Insights의 데이터 수집, 보존 및 스토리지](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)를 참조하세요. |

보조 지역에서 빠른 복구 및 다시 시작을 사용하려면 다음 개발 사례를 권장합니다.

* Azure Resource Manager 템플릿을 사용합니다. 템플릿은 ‘코드 제공 인프라’이며 이를 사용하여 두 지역에서 모두 서비스를 빠르게 배포할 수 있습니다.
* 두 지역 간에 드리프트를 방지하려면 두 지역에 모두 배포하도록 연속 통합 및 배포 파이프라인을 업데이트합니다.
* 배포를 자동화하는 경우 Azure Kubernetes Service와 같은 작업 영역 연결 컴퓨팅 리소스의 구성을 포함합니다.
* 두 지역에서 모두 사용자의 역할 할당을 만듭니다.
* 두 지역에 대해 모두 Azure Virtual Network와 같은 네트워크 리소스 및 프라이빗 엔드포인트를 만듭니다. 사용자에게 두 네트워크 환경에 대한 액세스 권한이 있는지 확인합니다. 예를 들면 두 가상 네트워크의 VPN 및 DNS 구성이 있습니다.

### <a name="compute-and-data-services"></a>컴퓨팅 및 데이터 서비스

요구 사항에 따라 Azure Machine Learning에서 사용하는 더 많은 컴퓨팅 또는 데이터 서비스가 있을 수 있습니다. 예를 들어, Azure Kubernetes Services 또는 Azure SQL Database를 사용할 수 있습니다. 다음 정보를 사용하여 고가용성을 제공하도록 이 서비스를 구성하는 방법을 알아봅니다.

__컴퓨팅 리소스__

* **Azure Kubernetes Service**: [AKS(Azure Kubernetes Service)의 비즈니스 연속성 및 재해 복구 모범 사례](../aks/operator-best-practices-multi-region.md)와 [가용성 영역을 사용하는 AKS(Azure Kubernetes Service) 클러스터 만들기](../aks/availability-zones.md)를 참조하세요. Azure Machine Learning 스튜디오, SDK 또는 CLI를 사용하여 AKS 클러스터를 만든 경우 지역 간 고가용성이 지원되지 않습니다.
* **Azure Databricks**: [Azure Databricks 클러스터의 지역 재해 복구](/azure/databricks/scenarios/howto-regional-disaster-recovery)를 참조하세요.
* **Container Instances**: 오케스트레이터는 장애 조치를 수행해야 합니다. [Azure Container Instances 및 컨테이너 오케스트레이터](../container-instances/container-instances-orchestrator-relationship.md)를 참조하세요.
* **HDInsight**: [Azure HDInsight에서 지원하는 고가용성 서비스](../hdinsight/hdinsight-high-availability-components.md)를 참조하세요.

__데이터 서비스__

* **Azure Blob 컨테이너/Azure Files/Data Lake Storage Gen2**: [Azure Storage 중복성](../storage/common/storage-redundancy.md)을 참조하세요.
* **Data Lake Storage Gen1**: [Data Lake Storage Gen1의 고가용성 및 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)을 참조하세요.
* **SQL Database**: [Azure SQL Database 및 SQL Managed Instance의 고가용성](../azure-sql/database/high-availability-sla.md)을 참조하세요.
* **Azure Database for PostgreSQL**: [Azure Database for PostgreSQL의 고가용성 개념 - 단일 서버](../postgresql/concepts-high-availability.md)를 참조하세요.
* **Azure Database for MySQL**: [Azure Database for MySQL의 비즈니스 연속성 이해](../mysql/concepts-business-continuity.md)를 참조하세요.
* **Azure Databricks 파일 시스템**: [Azure Databricks 클러스터의 지역 재해 복구](/azure/databricks/scenarios/howto-regional-disaster-recovery)를 참조하세요.

> [!TIP]
> 고유한 고객 관리형 키를 제공하여 Azure Machine Learning 작업 영역을 배포하는 경우 Azure Cosmos DB도 구독 내에서 프로비저닝됩니다. 이 경우 사용자가 고가용성 설정을 구성해야 합니다. [Azure Cosmos DB의 고가용성](../cosmos-db/high-availability.md)을 참조하세요.

## <a name="design-for-high-availability"></a>고가용성을 위한 디자인

### <a name="deploy-critical-components-to-multiple-regions"></a>여러 지역에 중요한 구성 요소 배포

목표로 하는 비즈니스 연속성 수준을 결정합니다. 해당 수준은 솔루션 구성 요소 간에 서로 다를 수 있습니다. 예를 들어, 프로덕션 파이프라인 또는 모델 배포에 핫/핫 구성을 사용하고 실험에 핫/콜드 구성을 사용하는 것이 좋습니다.

### <a name="manage-training-data-on-isolated-storage"></a>격리된 스토리지에서 학습 데이터 관리

작업 영역에서 로그에 사용하는 기본 스토리지에서 데이터 스토리지를 격리된 상태로 유지하면 다음을 수행할 수 있습니다.

* 데이터 저장소와 동일한 스토리지 인스턴스를 주 작업 영역과 보조 작업 영역에 연결합니다.
* 데이터 스토리지 계정에 대해 지역에서 복제를 사용하고 작동 시간을 최대화합니다.

### <a name="manage-machine-learning-artifacts-as-code"></a>기계 학습 아티팩트를 코드로 관리

Azure Machine Learning의 실행은 실행 사양에서 정의됩니다. 이 사양에는 환경, 데이터 세트, 컴퓨팅을 포함하여 작업 영역 인스턴스 수준에서 관리되는 입력 아티팩트의 종속성이 포함됩니다. 다중 지역 실행 제출 및 배포의 경우 다음 사례를 권장합니다.

* Git 리포지토리에서 지원되는 코드베이스를 로컬에서 관리합니다.
    * Azure Machine Learning 스튜디오에서 중요한 Notebook을 내보냅니다.
    * 스튜디오에서 작성된 파이프라인을 코드로 내보냅니다.

      > [!NOTE]
      > 스튜디오 디자이너에서 만든 파이프라인은 현재 코드로 내보낼 수 없습니다.

* 구성을 코드로 관리합니다.

    * 작업 영역에 대한 하드 코드된 참조를 방지합니다. 대신, [구성 파일](how-to-configure-environment.md#workspace)을 사용하여 작업 영역 인스턴스에 대한 참조를 구성하고 [Workspace.from_config()](/python/api/azureml-core/azureml.core.workspace.workspace#remarks)를 사용하여 작업 영역을 초기화합니다. 프로세스를 자동화하려면 [기계 학습용 Azure CLI 확장](reference-azure-machine-learning-cli.md) 명령 [az ml folder attach](/cli/azure/ml(v1)/folder#ext_azure_cli_ml_az_ml_folder_attach)를 사용합니다.
    * [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) 및 [Pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class))과 같은 실행 제출 도우미를 사용합니다.
    * [Environments.save_to_directory()](/python/api/azureml-core/azureml.core.environment(class)#save-to-directory-path--overwrite-false-)를 사용하여 환경 정의를 저장합니다.
    * 사용자 지정 Docker 이미지를 사용하는 경우 Dockerfile을 사용합니다.
    * [Dataset](/python/api/azureml-core/azureml.core.dataset(class)) 클래스를 사용하여 솔루션에서 사용하는 데이터 [경로](/python/api/azureml-core/azureml.data.datapath)의 컬렉션을 정의합니다.
    * [Inferenceconfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) 클래스를 사용하여 모델을 유추 엔드포인트로 배포합니다.

## <a name="initiate-a-failover"></a>장애 조치(failover) 시작

### <a name="continue-work-in-the-failover-workspace"></a>장애 조치 작업 영역에서 작업 계속

주 작업 영역을 사용할 수 없게 되면 보조 작업 영역으로 전환하여 실험 및 개발을 계속할 수 있습니다. 중단이 발생하는 경우 Azure Machine Learning은 실행을 보조 작업 영역으로 자동으로 제출하지 않습니다. 새 작업 영역 리소스를 가리키도록 코드 구성을 업데이트합니다. 작업 영역 참조 하드 코딩을 방지하는 것이 좋습니다. 대신, [작업 영역 구성 파일](how-to-configure-environment.md#workspace)을 사용하여 작업 영역을 변경할 때 수동 사용자 단계를 최소화합니다. 새 작업 영역에 대한 연속 통합 및 배포 파이프라인과 같은 자동화도 업데이트해야 합니다.

Azure Machine Learning은 작업 영역 인스턴스 간에 아티팩트 또는 메타데이터를 동기화하거나 복구할 수 없습니다. 애플리케이션 배포 전략에 따라 실행 제출을 계속하려면 장애 조치 작업 영역에서 데이터 세트 개체와 같은 실험 입력을 다시 만들거나 아티팩트를 이동해야 할 수도 있습니다. 지역에서 복제를 사용하여 연결된 리소스를 공유하도록 주 작업 영역 및 보조 작업 영역 리소스를 구성한 경우 일부 개체는 장애 조치 작업 영역에서 직접 사용할 수 있습니다. 예를 들면 두 작업 영역이 모두 동일한 docker 이미지, 구성된 데이터 저장소, Azure Key Vault 리소스를 공유하는 경우가 있습니다. 다음 다이어그램에서는 두 작업 영역이 동일한 이미지(1), 데이터 저장소(2), Key Vault(3)를 공유하는 구성을 보여 줍니다.

![참조 리소스 구성](./media/how-to-high-availability-machine-learning/bcdr-resource-configuration.png)

> [!NOTE]
> 서비스 중단이 발생할 때 실행 중인 모든 작업이 자동으로 보조 작업 영역으로 전환되는 것은 아닙니다. 중단이 해결된 후 작업이 다시 시작되고 주 작업 영역에서 성공적으로 완료되지 않을 수도 있습니다. 대신, 이 작업은 보조 작업 영역 또는 주 작업 영역(중단이 해결된 후)에서 다시 제출되어야 합니다.

### <a name="moving-artifacts-between-workspaces"></a>작업 영역 간에 아티팩트 이동

복구 접근 방식에 따라 작업을 계속하기 위해 작업 영역 간에 데이터 세트 및 모델 개체와 같은 아티팩트를 복사해야 할 수 있습니다. 현재 작업 영역 간에는 아티팩트 이식성이 제한됩니다. 가능하면 장애 조치 인스턴스에서 아티팩트를 다시 만들 수 있도록 아티팩트를 코드로 관리하는 것이 좋습니다.

[기계 학습용 Azure CLI 확장](reference-azure-machine-learning-cli.md)을 사용하여 작업 영역 간에 다음 아티팩트를 내보내고 가져올 수 있습니다.

| 아티팩트 | 내보내기 | 가져오기 |
| ----- | ----- | ----- |
| 모델 | [az ml model download --model-id {ID} --target-dir {PATH}](/cli/azure/ext/azure-cli-ml/ml/model#ext_azure_cli_ml_az_ml_model_download) | [az ml model register –name {NAME} --path {PATH}](/cli/azure/ext/azure-cli-ml/ml/model) |
| 환경 | [az ml environment download -n {NAME} -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_download) | [az ml environment register -d {PATH}](/cli/azure/ext/azure-cli-ml/ml/environment#ext_azure_cli_ml_az_ml_environment_register) |
| Azure ML 파이프라인(코드에서 생성됨) | [az ml pipeline get --path {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_get) | [az ml pipeline create --name {NAME} -y {PATH}](/cli/azure/ml(v1)/pipeline#ext_azure_cli_ml_az_ml_pipeline_create)

> [!TIP]
> * __등록된 데이터 세트__ 는 다운로드하거나 이동할 수 없습니다. 여기에는 중간 파이프라인 데이터 세트와 같이 Azure ML에서 생성된 데이터 세트가 포함됩니다. 그러나 두 작업 영역이 모두 액세스할 수 있거나 기본 데이터 스토리지가 복제된 공유 파일 위치를 참조하는 데이터 세트는 두 작업 영역에 모두 등록할 수 있습니다. [az ml dataset register](/cli/azure/ml(v1)/dataset#ext_azure_cli_ml_az_ml_dataset_register)를 사용하여 데이터 세트를 등록합니다.
> * __실행 출력__ 은 작업 영역과 연결된 기본 스토리지 계정에 저장됩니다. 서비스 중단 시 스튜디오 UI에서는 실행 출력에 액세스할 수 없지만 스토리지 계정을 통해 데이터에 직접 액세스할 수 있습니다. Blob에 저장된 데이터를 사용하는 방법에 관한 자세한 내용은 [Azure CLI를 사용하여 Blob 생성, 다운로드 및 나열](../storage/blobs/storage-quickstart-blobs-cli.md)을 참조하세요.
## <a name="next-steps"></a>다음 단계

고가용성 설정을 사용하여 연결된 리소스와 함께 Azure Machine Learning을 배포하려면 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/)을 사용합니다.
