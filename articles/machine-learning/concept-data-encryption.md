---
title: Azure Machine Learning을 사용하여 데이터 암호화
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 컴퓨팅 및 데이터 저장소가 미사용 암호화와 전송 중 암호화를 제공하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 08/02/2021
ms.openlocfilehash: 2a838d2c1206cbc1a73e00d3ff41337400a08676
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528971"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 데이터 암호화

Azure Machine Learning은 모델을 학습하고 유추를 수행할 때 다양한 Azure 데이터 스토리지 서비스 및 컴퓨팅 리소스를 사용합니다. 각 서비스 및 리소스에는 미사용 데이터 및 전송 중 데이터에 대한 암호화를 제공하는 고유한 방법이 있습니다. 이 문서에서는 각 항목에 대해 살펴보고 시나리오에 가장 적합한 항목이 무엇인지 알아봅니다.

> [!IMPORTANT]
> __학습__ 중의 프로덕션 등급 암호화의 경우 Azure Machine Learning 컴퓨팅 클러스터를 사용하는 것이 좋습니다. __유추__ 중 프로덕션 등급 암호화의 경우 Azure Kubernetes Service를 사용하는 것이 좋습니다.
>
> Azure Machine Learning 컴퓨팅 인스턴스는 개발/테스트 환경입니다. 이를 사용하는 경우 파일 공유에 Notebook 및 스크립트와 같은 파일을 저장하는 것이 좋습니다. 데이터는 데이터 저장소에 저장해야 합니다.

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

> [!IMPORTANT]
> 작업 영역에 중요한 데이터가 포함된 경우 작업 영역을 만드는 동안 [hbi_workspace 플래그](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)를 설정하는 것이 좋습니다. `hbi_workspace` 플래그는 작업 영역을 만들 때만 설정할 수 있습니다. 기존 작업 영역의 경우 변경할 수 없습니다.

`hbi_workspace` 플래그는 [진단 목적으로 Microsoft가 수집하는 데이터](#microsoft-collected-data) 양을 제어하고 [Microsoft 관리형 환경에서 추가 암호화](../security/fundamentals/encryption-atrest.md)가 가능하도록 설정합니다. 또한 다음과 같은 작업도 수행할 수 있습니다.

* 해당 구독에서 이전 클러스터를 만들지 않은 경우 Azure Machine Learning 컴퓨팅 클러스터에서 로컬 스크래치 디스크 암호화를 시작합니다. 그렇지 않으면 컴퓨팅 클러스터의 스크래치 디스크를 암호화가 가능하도록 지원 티켓을 생성해야 합니다. 
* 실행 사이에 로컬 스크래치 디스크를 정리합니다
* 키 자격 증명 모음을 사용하여 스토리지 계정, 컨테이너 레지스트리 및 SSH 계정의 자격 증명을 실행 레이어에서 컴퓨팅 클러스터로 안전하게 전달합니다.

> [!TIP]
> `hbi_workspace` 플래그는 전송 중 암호화에는 영향을 미치지 않고 미사용 암호화에만 영향을 줍니다.

### <a name="azure-blob-storage"></a>Azure Blob 스토리지

Azure Machine Learning은 Azure Machine Learning 작업 영역 및 구독과 연결된 Azure Blob 스토리지 계정에 스냅샷, 출력 및 로그를 저장합니다. Azure Blob Storage에 저장된 모든 데이터는 Microsoft 관리형 키를 사용하여 미사용 상태에서 암호화됩니다.

Azure Blob Storage에 저장된 데이터에 자체 키를 사용하는 방법에 대한 자세한 내용은 [Azure Key Vault에서 고객 관리형 키를 사용하여 Azure Storage 암호화](../storage/common/customer-managed-keys-configure-key-vault.md)를 참조하세요.

일반적으로 학습 데이터는 Azure Blob Storage에 저장되므로 학습 컴퓨팅 대상에 액세스할 수 있습니다. 이 스토리지는 Azure Machine Learning으로 관리되지 않지만 컴퓨팅 대상에 원격 파일 시스템으로 탑재됩니다.

키를 __순환 또는 철회__ 해야 하는 경우 언제든지 그렇게 할 수 있습니다. 키를 순환하면 스토리지 계정은 새 키(최신 버전)를 사용하여 미사용 데이터를 암호화하기 시작합니다. 키를 철회(비활성화)하면 스토리지 계정이 실패한 요청을 처리합니다. 순환 또는 철회가 적용되려면 대개 1시간이 걸립니다.

액세스 키 다시 생성에 대한 자세한 내용은 [스토리지 액세스 키 다시 생성](how-to-change-storage-access-key.md)을 참조하세요.

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning은 Azure Cosmos DB 인스턴스에 메타데이터를 저장합니다. 이 인스턴스는 Azure Machine Learning으로 관리되는 Microsoft 구독과 연결되어 있습니다. Azure Cosmos DB에 저장된 모든 데이터는 Microsoft 관리형 키를 사용하여 미사용 상태에서 암호화됩니다.

자체(고객 관리형) 키를 사용하여 Azure Cosmos DB 인스턴스를 암호화하려면 작업 영역에서 사용할 전용 Cosmos DB 인스턴스를 만들면 됩니다. 실행 기록 정보와 같은 데이터를 Microsoft 구독에서 호스트되는 다중 테넌트 Cosmos DB 인스턴스 외부에 저장하려는 경우에는 이 방법을 사용하는 것이 좋습니다. 

고객 관리형 키를 사용하여 구독에서 Cosmos DB 인스턴스를 프로비저닝하려면 다음 작업을 수행합니다.

* 구독에 Microsoft.MachineLearning 및 Microsoft.DocumentDB 리소스 공급자가 아직 등록되지 않은 경우 등록합니다.

* Azure Machine Learning 작업 영역을 만들 때는 다음 매개 변수를 사용합니다. 두 매개 변수 모두 필수이며 SDK, Azure CLI, REST API, Resource Manager 템플릿에서 지원됩니다.

    * `resource_cmk_uri`: 이 매개 변수는 [키의 버전 정보](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)를 포함하여 키 자격 증명 모음에서 고객 관리형 키의 전체 리소스 URI입니다. 

    * `cmk_keyvault`: 이 매개 변수는 구독에 있는 키 자격 증명 모음의 리소스 ID입니다. 이 키 자격 증명 모음은 Azure Machine Learning 작업 영역에 사용할 동일한 지역 및 구독에 있어야 합니다. 
    
        > [!NOTE]
        > 이 키 자격 증명 모음 인스턴스는 작업 영역을 프로비저닝할 때 Azure Machine Learning에서 생성된 키 자격 증명 모음과 다를 수 있습니다. 작업 영역에 동일한 키 자격 증명 모음 인스턴스를 사용하려면 [key_vault parameter](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)를 사용하여 작업 영역을 프로비저닝하는 동안 동일한 키 자격 증명 모음을 전달합니다. 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

키를 __순환 또는 철회__ 해야 하는 경우 언제든지 그렇게 할 수 있습니다. 키를 순환하면 Cosmos DB는 새 키(최신 버전)를 사용하여 미사용 데이터를 암호화하기 시작합니다. 키를 철회(비활성화)하면 Cosmos DB가 실패한 요청을 처리합니다. 순환 또는 철회가 적용되려면 대개 1시간이 걸립니다.

Cosmos DB의 고객 관리형 키에 대한 자세한 내용은 [Azure Cosmos DB 계정에 대한 고객 관리형 키 구성](../cosmos-db/how-to-setup-cmk.md)을 참조하세요.

### <a name="azure-container-registry"></a>Azure Container Registry

레지스트리(Azure Container Registry)의 모든 컨테이너 이미지는 미사용 상태에서 암호화됩니다. Azure는 이미지를 저장하기 전에 이미지를 자동으로 암호화하고 Azure Machine Learning이 이미지를 끌어올 때 암호를 해독합니다.

자체(고객 관리형) 키를 사용하여 Azure Container Registry를 암호화하려면 작업 영역을 프로비저닝하는 동안 자체 ACR을 생성하여 첨부하거나 작업 영역을 프로비저닝할 때 생성되는 기본 인스턴스를 암호화해야 합니다.

> [!IMPORTANT]
> Azure Machine Learning의 경우 Azure Container Registry에서 관리자 계정을 사용하도록 설정해야 합니다. 기본적으로 컨테이너 레지스트리를 만들 때 이 설정은 사용하도록 설정되어 있지 않습니다. 관리자 계정을 사용하도록 설정하는 방법에 대한 자세한 내용은 [관리자 계정](../container-registry/container-registry-authentication.md#admin-account)을 참조하세요.
>
> 작업 영역에 대해 Azure Container Registry가 만들어지면 이를 삭제하지 마세요. 삭제하면 Azure Machine Learning 작업 영역이 중단됩니다.

기존 Azure Container Registry를 사용하여 작업 영역을 만드는 예는 다음 문서를 참조하세요.

* [Azure CLI를 사용하여 Azure Machine Learning의 작업 영역 만들기](how-to-manage-workspace-cli.md)
* [Python SDK을 사용하여 작업 영역 만들기](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning의 작업 영역 만들기](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Instance

고객 관리형 키를 사용하여 배포된 ACI(Azure Container Instance) 리소스를 암호화할 수 있습니다. ACI에 사용되는 고객 관리형 키는 작업 영역의 Azure Key Vault에 저장할 수 있습니다. 키 생성에 대한 자세한 내용은 [고객 관리형 키를 사용하여 데이터 암호화](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)를 참조하세요.

Azure Container Instance에 모델을 배포할 때 키를 사용하려면 `AciWebservice.deploy_configuration()`을 사용하여 새 배포 구성을 만듭니다. 다음 매개 변수를 사용하여 키 정보를 제공합니다.

* `cmk_vault_base_url`: 키가 포함된 키 자격 증명 모음의 URL입니다.
* `cmk_key_name`: 키의 이름입니다.
* `cmk_key_version`: 키 버전입니다.

배포 구성을 만들고 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 참조
* [배포 위치 및 방법](how-to-deploy-and-where.md)
* [Azure Container Instances에 모델 배포](how-to-deploy-azure-container-instance.md)

ACI에서 고객 관리형 키를 사용하는 방법에 대한 자세한 내용은 [고객 관리형 키를 사용하여 데이터 암호화](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)를 참조하세요.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

언제든지 고객 관리형 키를 사용하여 배포된 Azure Kubernetes Service 리소스를 암호화할 수 있습니다. 자세한 내용은 [Azure Kubernetes Service의 BYOK(Bring Your Own Key)](../aks/azure-disk-customer-managed-keys.md)를 참조하세요. 

이 프로세스를 통해 Kubernetes 클러스터에서 배포된 가상 머신의 데이터 및 OS 디스크를 모두 암호화할 수 있습니다.

> [!IMPORTANT]
> 이 프로세스는 AKS K8 버전 1.17 이상에서만 작동합니다. 2020년 1월 13일에 AKS 1.17에 대한 지원이 Azure Machine Learning에 추가되었습니다.

### <a name="machine-learning-compute"></a>Machine Learning 컴퓨팅

**컴퓨팅 클러스터** Azure Storage에 저장된 각 컴퓨팅 노드의 OS 디스크는 Azure Machine Learning 스토리지 계정에서 Microsoft 관리형 키로 암호화됩니다. 컴퓨팅 대상은 사용 후 삭제되며 대기 중인 실행이 없으면 일반적으로 클러스터가 축소됩니다. 기본 가상 머신의 프로비저닝이 해제되고 OS 디스크가 삭제됩니다. OS 디스크에는 Azure Disk Encryption이 지원되지 않습니다. 

각 가상 머신에는 OS 작업을 위한 로컬 임시 디스크도 있습니다. 필요하면 디스크를 사용하여 학습 데이터를 준비할 수 있습니다. `hbi_workspace` 매개 변수를 `TRUE`로 설정하여 작업 영역을 만든 경우 임시 디스크가 암호화됩니다. 이 환경은 실행 기간 동안만 수명이 지속되며 암호화 지원은 시스템 관리형 키로만 제한됩니다.

**컴퓨팅 인스턴스** 컴퓨팅 인스턴스용 OS 디스크는 Azure Machine Learning 스토리지 계정의 Microsoft 관리형 키로 암호화됩니다. `hbi_workspace` 매개 변수를 `TRUE`로 설정하여 작업 영역을 만든 경우 컴퓨팅 인스턴스의 로컬 임시 디스크는 Microsoft 관리형 키로 암호화됩니다. 고객 관리형 키 암호화는 OS 및 임시 디스크에 대해 지원되지 않습니다.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks는 Azure Machine Learning 파이프라인에서 사용할 수 있습니다. 기본적으로 Azure Databricks에 사용되는 DBFS(Databricks 파일 시스템)는 Microsoft 관리형 키를 사용하여 암호화됩니다. 고객 관리형 키를 사용하도록 Azure Databricks를 구성하려면 [기본(루트) DBFS에서 고객 관리형 키 구성](/azure/databricks/security/customer-managed-keys-dbfs)을 참조하세요.

### <a name="microsoft-generated-data"></a>Microsoft에서 생성된 데이터

자동화된 Machine Learning과 같은 서비스를 사용하는 경우 Microsoft는 여러 모델을 학습시키기 위해 전처리된 일시적 데이터를 생성할 수 있습니다. 이런 데이터는 사용자 작업 영역의 데이터 저장소에 저장되므로 액세스 제어 및 암호화를 적절하게 적용할 수 있습니다.

[배포된 엔드포인트에서 기록된 진단 정보](how-to-enable-app-insights.md)를 Azure Application Insights 인스턴스로 암호화할 수도 있습니다.

## <a name="encryption-in-transit"></a>전송 중 암호화

Azure Machine Learning은 TLS를 사용하여 다양한 Azure Machine Learning 마이크로서비스 간의 내부 통신을 보호합니다. 모든 Azure Storage 액세스는 보안 채널을 통해 수행됩니다.

채점 엔드포인트에 대한 외부 호출을 보호하기 위해 Azure Machine Learning은 TLS를 사용합니다. 자세한 내용은 [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](./how-to-secure-web-service.md)를 참조하세요.

## <a name="data-collection-and-handling"></a>데이터 수집 및 처리

### <a name="microsoft-collected-data"></a>Microsoft에 수집되는 데이터

Microsoft는 진단을 목적으로 리소스 이름(예: 데이터 세트 이름 또는 기계 학습 실험 이름)이나 작업 환경 변수와 같은 비 사용자 식별 정보를 수집할 수 있습니다. 이러한 모든 데이터는 Microsoft 소유 구독에서 호스트되는 스토리지에 Microsoft 관리형 키를 사용하여 저장되며 [Microsoft의 표준 개인 정보 취급 방침 및 데이터 처리 표준](https://privacy.microsoft.com/privacystatement)을 따릅니다. 이 데이터는 작업 영역과 동일한 지역 내에 유지됩니다.

또한 환경 변수에 중요한 정보(예: 계정 키 비밀)를 저장하지 않는 것이 좋습니다. 환경 변수는 당사가 기록, 암호화 및 저장합니다. 마찬가지로 [run_id](/python/api/azureml-core/azureml.core.run%28class%29) 이름을 지정할 때 사용자 이름 또는 비밀 프로젝트 이름과 같은 민감한 정보를 포함하지 마십시오. Microsoft 지원 엔지니어가 액세스할 수 있는 원격 분석 로그에 이런 정보가 나타날 수 있습니다.

작업 영역을 프로비저닝하는 동안 `hbi_workspace` 매개 변수를 `TRUE`로 설정하면 진단 데이터가 수집되지 않도록 옵트아웃할 수 있습니다. 이 기능은 AzureML Python SDK, Azure CLI, REST API 또는 Azure Resource Manager 템플릿을 사용할 때 지원됩니다.

## <a name="using-azure-key-vault"></a>Azure Key Vault 사용

Azure Machine Learning은 작업 영역과 연결된 Azure Key Vault 인스턴스를 사용하여 다양한 종류의 자격 증명을 저장합니다.

* 연결된 스토리지 계정 연결 문자열
* Azure Container Repository 인스턴스에 대한 암호
* 데이터 저장소에 대한 연결 문자열

Azure HDInsight 및 VM과 같은 컴퓨팅 대상에 대한 SSH 암호 및 키는 Microsoft 구독과 연결된 별도의 키 자격 증명 모음에 저장됩니다. Azure Machine Learning에는 사용자가 제공한 암호나 키가 저장되지 않습니다. 대신 자체 SSH 키를 생성하고, 권한을 부여하고, 저장한 후 실험을 실행할 VM 및 HDInsight에 연결합니다.

각 작업 영역에는 작업 영역과 이름이 동일하며 연결되어 있는 시스템이 할당한 관리 ID가 있습니다. 관리 ID는 키 자격 증명 모음의 모든 키, 비밀 및 인증서에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Storage에 연결](how-to-access-data.md)
* [데이터 저장소에서 데이터 가져오기](how-to-create-register-datasets.md)
* [데이터에 연결](how-to-connect-data-ui.md)
* [데이터 세트로 학습](how-to-train-with-datasets.md)
