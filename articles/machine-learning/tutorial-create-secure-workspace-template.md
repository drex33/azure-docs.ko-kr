---
title: 템플릿을 사용 하 여 보안 작업 영역 만들기
titleSuffix: Azure Machine Learning
description: 템플릿을 사용 하 여 보안 가상 네트워크 내에서 Azure Machine Learning 작업 영역 및 필수 Azure 서비스를 만들 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: jhirono
ms.author: larryfr
author: blackmist
ms.date: 12/02/2021
ms.topic: tutorial
ms.openlocfilehash: 2f13ab3ce1a271a1f7fb71714df36912e1d2163a
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133546463"
---
# <a name="how-to-create-a-secure-workspace-by-using-template"></a>템플릿을 사용 하 여 보안 작업 영역을 만드는 방법

템플릿을 통해 재현 가능한 서비스 배포를 편리 하 게 만들 수 있습니다. 템플릿은 템플릿을 사용할 때 사용자가 제공 하는 일부 정보를 사용 하 여 생성 될 항목을 정의 합니다. 예를 들어 Azure Machine Learning 작업 영역에 대 한 고유 이름을 지정 합니다.

이 자습서에서는 [Microsoft Bicep](/azure/azure-resource-manager/bicep/overview) And [Hashicorp terraform](https://www.terraform.io/) 템플릿을 사용 하 여 다음과 같은 Azure 리소스를 만드는 방법에 대해 알아봅니다.

* Azure Virtual Network. 다음 리소스는이 VNet 뒤에서 보호 됩니다.
    * Azure Machine Learning 작업 영역
        * Azure Machine Learning 컴퓨팅 인스턴스
        * Azure Machine Learning 컴퓨팅 클러스터
    * Azure Storage 계정
    * Azure Key Vault
    * Azure Application Insights
    * Azure Container Registry
    * Azure 방호 호스트
    * Azure Machine Learning 가상 컴퓨터 (Data Science Virtual Machine)
    * 또한 __Bicep__ 템플릿은 Azure Kubernetes Service 클러스터와이에 대 한 별도의 리소스 그룹을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 사용 하기 전에 Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 생성합니다.

또한 Bash 또는 Azure PowerShell 명령줄이 있어야 합니다.

> [!TIP]
> 이 문서를 읽을 때 각 섹션의 탭을 사용 하 여 Bicep 또는 Terraform 템플릿 사용에 대 한 정보를 볼 지 여부를 선택 합니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

1. 명령줄 도구를 설치 하려면 [Bicep 개발 및 배포 환경 설정](/azure/azure-resource-manager/bicep/install)을 참조 하세요.

1. 이 문서에 사용 된 Bicep 템플릿은에 [https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure) 있습니다. 다음 명령을 사용 하 여 GitHub 리포지토리를 개발 환경에 복제 합니다.

    > [!TIP]
    > `git`개발 환경에 명령이 없는 경우에서 설치할 수 있습니다 [https://git-scm.com/](https://git-scm.com/) .

    ```azurecli
    git clone https://github.com/Azure/azure-quickstart-templates
    cd azure-quickstart-templates/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure
    ```  

# <a name="terraform"></a>[Terraform](#tab/terraform)

1. Azure 구독에 Terraform을 설치, 구성 및 인증 하려면 다음 문서 중 하나의 단계를 사용 합니다.

    * [Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell-bash)
    * [Bash를 사용 하 여 Windows](/azure/developer/terraform/get-started-windows-bash)
    * [Azure PowerShell Windows](/azure/developer/terraform/get-started-windows-powershell)

1. 이 문서에 사용 된 Terraform 템플릿 파일은에 있습니다 [https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) . 리포지토리를 로컬로 복제 하 고 템플릿 파일이 있는 위치로 디렉터리를 변경 하려면 명령줄에서 다음 명령을 사용 합니다.

    > [!TIP]
    > `git`개발 환경에 명령이 없는 경우에서 설치할 수 있습니다 [https://git-scm.com/](https://git-scm.com/) .

    ```azurecli
    git clone https://github.com/Azure/terraform
    cd terraform/quickstart/201-machine-learning-moderately-secure
    ```

---
## <a name="understanding-the-template"></a>템플릿 이해

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep 템플릿은 [Bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/main.bicep) 및 `.bicep` [modules](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules) 하위 디렉터리에 있는 파일로 구성 됩니다. 다음 표에서는 각 파일의 역할을 설명 합니다.

| 파일 | 설명 |
| ----- | ----- |
| [bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/main.bicep) | 매개 변수 및 변수. 매개 변수 & 변수를 하위 디렉터리의 다른 모듈에 전달 `modules` 합니다. |
| [bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/vnet.bicep) | Azure Virtual Network 및 서브넷을 정의 합니다. |
| [nsg. bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/nsg.bicep) | VNet에 대 한 네트워크 보안 그룹 규칙을 정의 합니다. |
| [bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/bastion.bicep) | Azure 방호 호스트 및 서브넷을 정의 합니다. Azure 방호를 사용 하면 웹 브라우저를 사용 하 여 VNet 내의 VM에 쉽게 액세스할 수 있습니다. |
| [dsvmjumpbox.bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/dsvmjumpbox.bicep) | Data Science Virtual Machine (DSVM)를 정의 합니다. Azure 방호는 웹 브라우저를 통해이 VM에 액세스 하는 데 사용 됩니다. |
| [bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/storage.bicep) | 기본 저장소에 대해 작업 영역에서 사용 하는 Azure Storage 계정을 정의 합니다. |
| [bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/keyvault.bicep) | 작업 영역에서 사용 하는 Azure Key Vault를 정의 합니다. |
| [microsoft.containerregistry. bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/containerregistry.bicep) | 작업 영역에서 사용 하는 Azure Container Registry를 정의 합니다. |
| [bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/applicationinsights.bicep) | 작업 영역에서 사용 하는 Azure 애플리케이션 Insights 인스턴스를 정의 합니다. |
| [machinelearningnetworking.bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/machinelearningnetworking.bicep) | Azure Machine Learning 작업 영역에 대 한 te 개인 끝점 및 DNS 영역을 정의 합니다. |
| [Machinelearning.bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/machinelearning.bicep) | Azure Machine Learning 작업 영역을 정의 합니다. |
| [machinelearningcompute.bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/machinelearningcompute.bicep) | Azure Machine Learning 계산 클러스터 및 계산 인스턴스를 정의 합니다. |
| [privateaks.bicep](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.machinelearningservices/machine-learning-end-to-end-secure/modules/privateaks.bicep) | Azure Kubernetes Services 클러스터 인스턴스를 정의 합니다. |

# <a name="terraform"></a>[Terraform](#tab/terraform)

템플릿은 여러 파일로 구성 됩니다. 다음 표에서는 각 파일의 역할을 설명 합니다.

| 파일 | 설명 |
| ----- | ----- |
| [variables.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/variables.tf) | 템플릿에 사용 되는 변수 및 기본값
| [main.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/main.tf) | Azure Resource Manager 공급자를 지정 하 고 리소스 그룹을 정의 합니다. |
| [network.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf) | Azure Virtual Network, 서브넷 및 네트워크 보안 그룹을 정의 합니다. |
| [bastion.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/bastion.tf) | Azure 방호 호스트 및 연결 된 NSG를 정의 합니다. Azure 방호를 사용 하면 웹 브라우저를 사용 하 여 VNet 내의 VM에 쉽게 액세스할 수 있습니다. |
| [dsvm.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/dsvm.tf) | Data Science Virtual Machine (DSVM)를 정의 합니다. Azure 방호는 웹 브라우저를 통해이 VM에 액세스 하는 데 사용 됩니다. |
| [workspace.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/workspace.tf) | Azure Machine Learning 작업 영역을 정의 합니다. Azure Storage, Key Vault, Application Insights 및 Container Registry에 대 한 종속성 리소스를 포함 합니다. |
| [compute.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/compute.tf) | Azure Machine Learning 계산 인스턴스와 클러스터를 정의 합니다. |

> [!TIP]
> [Terraform Azure 공급자](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs) 는이 자습서에서 사용 되지 않는 추가 인수를 지원 합니다. 예를 들어 [환경](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs#environment) 인수를 사용 하 여 Azure Government 및 Azure 중국 21vianet과 같은 클라우드 지역을 대상으로 지정할 수 있습니다.

---

> [!IMPORTANT]
> DSVM 및 Azure 방호는이 자습서의 보안 작업 영역에 연결 하는 쉬운 방법으로 사용 됩니다. 프로덕션 환경에서는 [AZURE VPN gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways) 또는 [azure express](/azure/expressroute/expressroute-introduction) 경로를 사용 하 여 온-프레미스 네트워크에서 직접 VNet 내의 리소스에 액세스 하는 것이 좋습니다.

## <a name="configure-the-template"></a>템플릿 구성

# <a name="bicep"></a>[Bicep](#tab/bicep)

Terraform 템플릿을 실행하려면 파일이 있는 위치에서 다음 명령을 사용합니다. `machine-learning-end-to-end-secure` `main.bicep`

1. 새 Azure 리소스 그룹을 만들려면 다음 명령을 사용합니다. `exampleRG`을 리소스 그룹 이름으로 바꾸고 `eastus` 을 사용하려는 Azure 지역으로 대체합니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/cli)

    ```azurecli
    az group create --name exampleRG --location eastus
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/ps1)

    ```azurepowershell
    New-AzResourceGroup -Name exampleRG -Location eastus
    ```

    ---

1. 템플릿을 실행하려면 다음 명령을 사용합니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/cli)

    ```azurecli
    az deployment group create \
        --resource-group exampleRG \
        --template-file main.bicep \
        --parameters \
        prefix=myprefix \
        dsvmJumpboxUsername=azureadmin \
        dsvmJumpboxPassword=securepassword
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/ps1)

    ```azurepowershell
    $dsvmPassword = ConvertTo-SecureString "mysecurepassword" -AsPlainText -Force
    New-AzResourceGroupDeployment -ResourceGroupName exampleRG `
        -TemplateFile ./main.bicep `
        -prefix "myprefix" `
        -dsvmJumpboxUsername "azureadmin" `
        -dsvmJumpboxPassword $dsvmPassword
    ```

    > [!WARNING]
    > 스크립트 또는 명령줄에서 일반 텍스트 문자열을 사용하지 않아야 합니다. 일반 텍스트는 이벤트 로그 및 명령 기록에 표시할 수 있습니다. 자세한 내용은 [ConvertTo-SecureString을 참조하세요.](/powershell/module/microsoft.powershell.security/convertto-securestring)

    ---

# <a name="terraform"></a>[Terraform](#tab/terraform)

Terraform 템플릿을 실행하려면 `201-machine-learning-moderately-secure` 템플릿 파일이 있는 디렉터리에서 다음 명령을 사용합니다.

1. Terraform 작업을 위한 디렉터리를 초기화하려면 다음 명령을 사용합니다.

    ```azurecli
    terraform init
    ```

1. 구성을 만들려면 다음 명령을 사용합니다. 매개 `-var` 변수를 사용하여 템플릿에서 사용하는 변수의 값을 설정합니다. 변수의 전체 목록은 [variables.tf](https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/variables.tf) 파일을 참조하세요.

    ```azurecli
    terraform plan \
        -var name=myworkspace \
        -var environment=dev \
        -var location=westus \
        -var dsvm_name=jumpbox \
        -var dsvm_host_password=secure_password \
        -out azureml.tfplan
    ```

    이 명령이 완료되면 구성이 터미널에 표시됩니다. 다시 표시하려면 명령을 `terraform show azureml.tfplan` 사용합니다.

1. 템플릿을 실행하고 저장된 구성을 Azure 구독에 적용하려면 다음 명령을 사용합니다.

    ```azurecli
    terraform apply azureml.tfplan
    ```

    템플릿이 처리될 때 진행률도 표시됩니다.

---

## <a name="connect-to-the-workspace"></a>작업 영역에 연결

템플릿이 완료되면 다음 단계를 사용하여 DSVM에 연결합니다.

1. [Azure Portal](https://portal.azure.com)템플릿에서 사용한 Azure 리소스 그룹을 선택합니다. 그런 다음 템플릿에서 만든 Data Science Virtual Machine 선택합니다. 찾는 데 문제가 있는 경우 필터 섹션을 사용하여 __형식을 가상 머신으로__ 필터링합니다. 

    :::image type="content" source="./media/tutorial-create-secure-workspace-template/select-vm.png" alt-text="VM 필터링 및 선택 스크린샷":::

1. Virtual Machine의 __개요__ 섹션에서 __커넥트__ 선택한 다음, 드롭다운에서 __Bastion을__ 선택합니다.

    :::image type="content" source="./media/tutorial-create-secure-workspace-template/connect-bastion.png" alt-text="Bastion을 사용하여 연결하도록 선택하는 스크린샷.":::

1. 메시지가 표시되면 템플릿을 구성할 때 지정한 __사용자 이름과__ __암호를__ 입력한 __다음, 커넥트__ 선택합니다.

    > [!IMPORTANT]
    > DSVM 데스크톱에 처음 연결하면 PowerShell 창이 열리고 스크립트 실행이 시작됩니다. 다음 단계를 계속하기 전에 이 작업을 완료하도록 허용합니다.

1. DSVM 데스크톱에서 __Microsoft Edge__ 시작하고 `https://ml.azure.com` 주소로 를 입력합니다. Azure 구독에 로그인한 다음 템플릿에서 만든 작업 영역을 선택합니다. 작업 영역에 대한 스튜디오가 표시됩니다.

## <a name="next-steps"></a>다음 단계

> [!IMPORTANT]
> DSVM(Data Science Virtual Machine) 및 모든 컴퓨팅 인스턴스 리소스는 실행 중인 매시간 요금이 청구됩니다. 과도한 요금을 방지하려면 사용하지 않을 때 이러한 리소스를 중지해야 합니다. 자세한 내용은 다음 문서를 참조하세요.
> 
> * [VM 만들기/관리(Linux)](/azure/virtual-machines/linux/tutorial-manage-vm).
> * [VM 만들기/관리(Windows)](/azure/virtual-machines/windows/tutorial-manage-vm).
> * [컴퓨팅 인스턴스를 만들/관리합니다.](how-to-create-manage-compute-instance.md)

DSVM에서 보안 작업 영역을 사용하는 방법을 계속 알아보려면 [자습서: Azure Machine Learning Python 스크립트 시작을 참조하세요.](tutorial-1st-experiment-hello-world.md)

일반적인 보안 작업 영역 구성 및 입/출력 요구 사항에 대한 자세한 내용은 [보안 작업 영역 트래픽 흐름 Azure Machine Learning](concept-secure-network-traffic-flow.md)참조하세요.