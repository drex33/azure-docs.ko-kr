---
title: 사용자 지정 DNS 서버 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 작업 영역 및 프라이빗 엔드포인트와 함께 작동하도록 사용자 지정 DNS 서버를 구성하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/03/2021
ms.topic: how-to
ms.custom: contperf-fy21q3, devx-track-azurepowershell
ms.openlocfilehash: 6395f88f4841ef5447b8dfef5310fba6e0440e32
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771457"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법

프라이빗 엔드포인트와 함께 Azure Machine Learning 작업 영역을 사용하는 경우 [DNS 이름 확인을 처리하는 여러 방법](../private-link/private-endpoint-dns.md)이 있습니다. 기본적으로 Azure는 작업 영역 및 프라이빗 엔드포인트에 대한 이름 확인을 자동으로 처리합니다. __자체 사용자 지정 DNS 서버__ 를 대신 사용하는 경우 수동으로 DNS 항목을 만들거나 작업 영역에 대해 조건부 전달자를 사용해야 합니다.

> [!IMPORTANT]
> 이 문서에서는 DNS 솔루션에 DNS 레코드를 수동으로 등록하려는 경우 해당 항목의 FQDN(정규화된 도메인 이름) 및 IP 주소를 찾는 방법을 설명합니다. 또한 이 문서에서는 FQDN을 올바른 IP 주소로 자동으로 확인하도록 사용자 지정 DNS 솔루션을 구성하는 방법의 아키텍처 권장 사항을 제공합니다. 이 문서에서는 해당 항목의 DNS 레코드를 구성하는 방법에 관한 정보를 제공하지 않습니다. 레코드를 추가하는 방법에 대한 자세한 내용은 DNS 소프트웨어에 대한 문서를 참조하세요.

> [!TIP]
> 이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.
>
> * [Virtual Network 개요](how-to-network-security-overview.md)
> * [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
> * [학습 환경 보호](how-to-secure-training-vnet.md)
> * [보안 유추 환경](how-to-secure-inferencing-vnet.md)
> * [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
> * [방화벽 사용](how-to-access-azureml-behind-firewall.md)
## <a name="prerequisites"></a>사전 요구 사항

- [자체 DNS 서버](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)를 사용하는 Azure Virtual Network입니다.

- 프라이빗 엔드포인트를 사용하는 Azure Machine Learning 작업 영역입니다. 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조하세요.

- [학습 및 유추 중 네트워크 격리](./how-to-network-security-overview.md)를 사용하는 방법 숙지

- [Azure 프라이빗 엔드포인트 DNS 영역 구성](../private-link/private-endpoint-dns.md) 방법 숙지

- [Azure 프라이빗 DNS](../dns/private-dns-privatednszone.md)에 관한 기본 지식

- 선택적으로는 [Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/install-az-ps)이 있습니다.

## <a name="automated-dns-server-integration"></a>자동화된 DNS 서버 통합

### <a name="introduction"></a>소개

Azure Machine Learning에 대한 자동화된 DNS 서버 통합을 사용하는 두 가지 일반적인 아키텍처가 있습니다.

* [Azure Virtual Network에서 호스트된 사용자 지정 DNS 서버](#dns-vnet).
* ExpressRoute를 통해 Azure Machine Learning에 연결된 [온-프레미스에서 호스트된 사용자 지정 DNS 서버](#dns-on-premises).

사용 중인 아키텍처가 이 예제와 다를 수 있지만 예제를 참조 지점으로 사용할 수 있습니다. 두 예제 아키텍처는 모두 잘못 구성될 수 있는 구성 요소를 식별하는 데 도움이 되는 문제 해결 단계를 제공합니다.

또 다른 옵션은 작업 영역이 포함된 Azure VNet(Virtual Network)에 연결하는 클라이언트에서 `hosts` 파일을 수정하는 것입니다. 자세한 내용은 [호스트 파일](#hosts) 섹션을 참조하세요.
### <a name="workspace-dns-resolution-path"></a>작업 영역 DNS 확인 경로

프라이빗 링크를 통해 지정된 Azure Machine Learning 작업 영역에 액세스하는 작업은 아래 나열된 다음 정규화된 도메인(작업 영역 FQDN이라고 함)과 통신하여 수행됩니다.

**Azure 퍼블릭 지역**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.azureml.ms```
- ```<compute instance name>.<region the workspace was created in>.instances.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.azure.net```

**Azure 중국 21Vianet 지역**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.cn```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.chinacloudapi.cn```

**Azure US Government 지역**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.cert.api.ml.azure.us```
- ```<compute instance name>.<region the workspace was created in>.instances.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.notebooks.usgovcloudapi.net```

정규화된 도메인은 작업 영역 프라이빗 링크 FQDN이라는 다음 정식 이름(CNAME)으로 확인됩니다.

**Azure 퍼블릭 지역**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.azureml.ms```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.azure.net```

**Azure 중국 지역**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.cn```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.chinacloudapi.cn```

**Azure US Government 지역**:
- ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.privatelink.api.ml.azure.us```
- ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>.privatelink.notebooks.usgovcloudapi.net```

FQDN은 해당 지역에서 Azure Machine Learning 작업 영역의 IP 주소로 확인됩니다. 그러나 가상 네트워크에서 호스팅되는 사용자 지정 DNS 서버를 사용하여 작업 영역 Private Link FQDN의 확인을 재정의할 수 있습니다. 이 아키텍처의 예는 [VNET에서 호스팅되는 사용자 지정 DNS 서버](#example-custom-dns-server-hosted-in-vnet) 예를 참조하세요.

## <a name="manual-dns-server-integration"></a>수동 DNS 서버 통합

이 섹션에서는 DNS 서버에서 A 레코드를 만드는 정규화된 도메인 및 A 레코드의 값을 설정하는 IP 주소에 관해 설명합니다.

### <a name="retrieve-private-endpoint-fqdns"></a>프라이빗 엔드포인트 FQDN 검색

#### <a name="azure-public-region"></a>Azure 퍼블릭 지역

다음 목록에는 Azure 퍼블릭 클라우드에 있는 경우 작업 영역에서 사용하는 FQDN(정규화된 도메인 이름)이 포함되어 있습니다.

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.azure.net`

    > [!NOTE]
    > 이 FQDN의 작업 영역 이름은 잘릴 수 있습니다. 잘림은 `ml-<workspace-name, truncated>-<region>-<workspace-guid>`를 63자 이하로 유지하기 위해 수행됩니다.
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > * 컴퓨팅 인스턴스는 가상 네트워크 내에서만 액세스할 수 있습니다.
    > * 이 FQDN의 IP 주소가 컴퓨팅 인스턴스의 IP가 **아닙니다**. 대신 작업 영역 프라이빗 엔드포인트(`*.api.azureml.ms` 항목의 IP)의 개인 IP 주소를 사용합니다.

#### <a name="azure-china-region"></a>Azure 중국 지역

다음 FQDN은 Azure 중국 지역을 위한 것입니다.

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.cn`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.cn`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.chinacloudapi.cn`

    > [!NOTE]
    > 이 FQDN의 작업 영역 이름은 잘릴 수 있습니다. 잘림은 `ml-<workspace-name, truncated>-<region>-<workspace-guid>`를 63자 이하로 유지하기 위해 수행됩니다.
    
* `<instance-name>.<region>.instances.ml.azure.cn`

   * 이 FQDN의 IP 주소가 컴퓨팅 인스턴스의 IP가 **아닙니다**. 대신 작업 영역 프라이빗 엔드포인트(`*.api.azureml.ms` 항목의 IP)의 개인 IP 주소를 사용합니다.

#### <a name="azure-us-government"></a>Azure 미국 정부

다음 FQDN은 Azure US Government 지역을 위한 것입니다.

* `<workspace-GUID>.workspace.<region>.cert.api.ml.azure.us`
* `<workspace-GUID>.workspace.<region>.api.ml.azure.us`
* `ml-<workspace-name, truncated>-<region>-<workspace-guid>.notebooks.usgovcloudapi.net`

    > [!NOTE]
    > 이 FQDN의 작업 영역 이름은 잘릴 수 있습니다. 잘림은 `ml-<workspace-name, truncated>-<region>-<workspace-guid>`를 63자 이하로 유지하기 위해 수행됩니다.
* `<instance-name>.<region>.instances.ml.azure.us`
    > * 이 FQDN의 IP 주소가 컴퓨팅 인스턴스의 IP가 **아닙니다**. 대신 작업 영역 프라이빗 엔드포인트(`*.api.azureml.ms` 항목의 IP)의 개인 IP 주소를 사용합니다.

### <a name="find-the-ip-addresses"></a>IP 주소 찾기

VNet에서 FQDN의 내부 IP 주소를 찾으려면 다음 방법 중 하나를 사용합니다.

> [!NOTE]
> 정규화된 도메인 이름 및 IP 주소는 구성에 따라 달라집니다. 예를 들어 도메인 이름의 GUID 값은 작업 영역에만 적용됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 프라이빗 엔드포인트 네트워크 인터페이스의 ID를 가져오려면 다음 명령을 사용합니다.

    ```azurecli
    az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'networkInterfaces[*].id' --output table
    ```

1. IP 주소 및 FQDN 정보를 얻으려면 다음 명령을 사용합니다. `<resource-id>`를 이전 단계의 ID로 바꿉니다.

    ```azurecli
    az network nic show --ids <resource-id> --query 'ipConfigurations[*].{IPAddress: privateIpAddress, FQDNs: privateLinkConnectionProperties.fqdns}'
    ```

    출력은 다음 텍스트와 비슷합니다.

    ```json
    [
        {
            "FQDNs": [
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms",
            "fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms"
            ],
            "IPAddress": "10.1.0.5"
        },
        {
            "FQDNs": [
            "ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net"
            ],
            "IPAddress": "10.1.0.6"
        }
    ]
    ```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 Azure Machine Learning __작업 영역__ 을 선택합니다.
1. __설정__ 섹션에서 __프라이빗 엔드포인트 연결__ 을 선택합니다.
1. 표시되는 __프라이빗 엔드포인트__ 열에서 링크를 선택합니다.
1. 작업 영역 프라이빗 엔드포인트에 대한 FQDN(정규화된 도메인 이름) 및 IP 주소 목록은 페이지 하단에 있습니다.

    :::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="포털의 FQDN 목록":::

    > [!TIP]
    > 페이지 아래쪽에 DNS 설정이 표시되지 않으면 페이지의 왼쪽에 있는 __DNS 구성__ 링크를 사용하여 FQDN을 확인합니다.

---

모든 메서드에서 반환된 정보는 동일합니다. 리소스의 FQDN 및 개인 IP 주소 목록입니다. Azure 퍼블릭 클라우드의 예제는 다음과 같습니다.

| FQDN | IP 주소 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

다음 표에는 Azure 중국 지역의 예제 IP가 나와 있습니다.

| FQDN | IP 주소 |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.cn` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.cn` | `10.1.0.5` |
| `ml-mype-pltest-chinaeast2-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.chinacloudapi.cn` | `10.1.0.6` |

다음 표에는 Azure US Government 지역의 예제 IP가 나와 있습니다.

| FQDN | IP 주소 |
| ----- | ----- |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.api.ml.azure.us` | `10.1.0.5` |
| `52882c08-ead2-44aa-af65-08a75cf094bd.workspace.chinaeast2.cert.api.ml.azure.us` | `10.1.0.5` |
| `ml-mype-plt-usgovvirginia-52882c08-ead2-44aa-af65-08a75cf094bd.notebooks.usgovcloudapi.net` | `10.1.0.6` |

<a id='dns-vnet'></a>

### <a name="create-a-records-in-custom-dns-server"></a>사용자 지정 DNS 서버에서 A 레코드 만들기

FQDN 및 해당 IP 주소 목록을 수집하고 나면 구성된 DNS 서버에서 A 레코드 만들기를 계속 진행합니다. A 레코드를 만드는 방법을 확인하려면 DNS 서버의 설명서를 참조하세요. 전체 FQDN에 고유한 영역을 만들고 영역 루트에 A 레코드를 만드는 것이 좋습니다.

## <a name="example-custom-dns-server-hosted-in-vnet"></a>예: VNet에서 호스트된 사용자 지정 DNS 서버

이 아키텍처는 일반적인 허브 및 스포크 가상 네트워크 토폴로지를 사용합니다. 한 가상 네트워크에는 DNS 서버가 포함되고 다른 가상 네트워크에는 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트 및 연결된 리소스가 포함됩니다. 양쪽 가상 네트워크 간에 유효한 경로가 있어야 합니다. 예를 들어 일련의 피어링된 가상 네트워크를 통한 경로입니다.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-topology.svg" alt-text="Azure 토폴로지에서 호스트된 사용자 지정 DNS의 다이어그램":::

다음 단계에서는 이 토폴로지의 작동 방식을 설명합니다.

1. **프라이빗 DNS 영역을 만들고 DNS 서버 가상 네트워크에 연결합니다**.

    사용자 지정 DNS 솔루션이 Azure Machine Learning 작업 영역에서 작동하는지 확인하는 첫 번째 단계는 다음 도메인에서 두 개의 프라이빗 DNS 영역을 만드는 것입니다.

    **Azure 퍼블릭 지역**:
    - ```privatelink.api.azureml.ms```
    - ```privatelink.notebooks.azure.net```
    
    **Azure 중국 지역**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government 지역**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    프라이빗 DNS 영역을 만든 다음, DNS 서버 가상 네트워크에 연결해야 합니다. DNS 서버를 포함하는 가상 네트워크입니다.

    프라이빗 DNS 영역은 영역 루트의 범위 내에 있는 모든 이름의 이름 확인을 재정의합니다. 이 재정의는 프라이빗 DNS 영역이 연결된 모든 가상 네트워크에 적용됩니다. 예를 들어, `privatelink.api.azureml.ms`를 기반으로 하는 프라이빗 DNS 영역이 가상 네트워크 foo에 연결된 경우 `bar.workspace.westus2.privatelink.api.azureml.ms`를 확인하는 가상 네트워크 foo의 모든 리소스는 `privatelink.api.azureml.ms` 영역에 나열된 모든 레코드를 수신합니다.

    그러나 프라이빗 DNS 영역에 나열된 레코드는 기본 Azure DNS 가상 서버 IP 주소를 사용하여 도메인을 확인하는 디바이스에만 반환됩니다. 따라서 사용자 지정 DNS 서버는 네트워크 토폴로지 전체에 분산된 디바이스의 도메인을 확인합니다. 그러나 사용자 지정 DNS 서버는 Azure DNS 가상 서버 IP 주소에 대해 Azure Machine Learning 관련 도메인을 확인해야 합니다.

2. **DNS 서버 가상 네트워크에 연결된 프라이빗 DNS 영역을 대상으로 하는 프라이빗 DNS 통합을 사용하여 프라이빗 엔드포인트를 만듭니다**.

    다음 단계는 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트를 만드는 것입니다. 프라이빗 엔드포인트는 1단계에서 만든 두 프라이빗 DNS 영역을 모두 대상으로 합니다. 이렇게 하면 작업 영역에 대한 모든 통신이 Azure Machine Learning 가상 네트워크에서 프라이빗 엔드포인트를 통해 수행됩니다.

    > [!IMPORTANT]
    > 이 예가 올바르게 작동하려면 프라이빗 엔드포인트에 프라이빗 DNS 통합이 사용하도록 설정되어 있어야 합니다.

3. **DNS 서버에서 Azure DNS에 전달할 조건부 전달자를 만듭니다**. 

    다음으로, Azure DNS 가상 서버에 대한 조건부 전달자를 만듭니다. 조건부 전달자는 DNS 서버가 항상 작업 영역과 관련된 FQDN에 대한 Azure DNS 가상 서버 IP 주소를 쿼리하는지 확인합니다. 이는 DNS 서버가 프라이빗 DNS 영역에서 해당 레코드를 반환할 것임을 의미합니다.

    조건부로 전달할 영역은 아래에 나열됩니다. Azure DNS 가상 서버 IP 주소는 168.63.129.16입니다.

    **Azure 퍼블릭 지역**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    **Azure 중국 지역**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Azure US Government 지역**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > DNS 서버의 구성 단계는 여기에 포함되지 않습니다. 사용자 지정 DNS 서버로 사용할 수 있는 많은 DNS 솔루션이 사용 가능하기 때문입니다. 조건부 전달을 적절하게 구성하는 방법은 DNS 솔루션에 관한 설명서를 참조하세요.

4. **작업 영역 도메인을 확인합니다**.

    이 시점에 모든 설정이 완료됩니다. 이제 이름 확인에 DNS 서버를 사용하고 Azure Machine Learning 프라이빗 엔드포인트의 경로를 포함하는 모든 클라이언트는 작업 영역에 계속 액세스할 수 있습니다.
    먼저 다음 FQDN의 주소에 대한 DNS 서버를 쿼리하여 클라이언트를 시작합니다.

    **Azure 퍼블릭 지역**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Azure 중국 지역**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Azure US Government 지역**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

5. **Azure DNS는 CNAME에 대한 작업 영역 도메인을 재귀적으로 확인합니다.**

    DNS 서버는 Azure DNS 4단계의 FQDN을 확인합니다. Azure DNS는 1단계에 나열된 도메인 중 하나로 응답합니다.

6. **DNS 서버는 Azure DNS에서 작업 영역 도메인 CNAME 레코드를 재귀적으로 확인합니다**.

    DNS 서버는 5단계에서 수신한 CNAME을 재귀적으로 확인합니다. 3단계에서 조건부 전달자 설정이 있었으므로 DNS 서버는 확인을 위해 Azure DNS 가상 서버 IP 주소로 요청을 보냅니다.

7. **Azure DNS는 프라이빗 DNS 영역에서 레코드를 반환합니다**.

    프라이빗 DNS 영역에 저장된 해당 레코드는 DNS 서버에 반환됩니다. 이는 Azure DNS 가상 서버가 프라이빗 엔드포인트의 IP 주소를 반환하는 것을 의미합니다.

8. **사용자 지정 DNS 서버는 작업 영역 도메인 이름을 프라이빗 엔드포인트 주소로 확인합니다**.

    궁극적으로 사용자 지정 DNS 서버는 이제 프라이빗 엔드포인트의 IP 주소를 4단계의 클라이언트에 반환합니다. 이렇게 하면 Azure Machine Learning 작업 영역에 대한 모든 트래픽이 프라이빗 엔드포인트를 통해 전달됩니다.

#### <a name="troubleshooting"></a>문제 해결

가상 머신에서 작업 영역에 액세스할 수 없거나 가상 네트워크에서 컴퓨팅 리소스에 대한 작업이 실패하는 경우 다음 단계를 사용하여 원인을 식별합니다.

1. **프라이빗 엔드포인트에서 작업 영역 FQDN을 찾습니다**.

    다음 링크 중 하나를 사용하여 Azure Portal로 이동합니다.
    - [Azure 퍼블릭 지역](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure 중국 지역](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure US Government 지역](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트로 이동합니다. 작업 영역 FQDN은 “개요” 탭에 나열됩니다.

1. **Virtual Network 토폴로지에서 컴퓨팅 리소스에 액세스합니다**.

    Azure Virtual Network 토폴로지에서 컴퓨팅 리소스에 대한 액세스를 계속합니다. 이렇게 하려면 허브 가상 네트워크와 피어링된 가상 네트워크의 가상 머신에 액세스해야 할 수 있습니다. 

1. **작업 영역 FQDN을 확인합니다**.

    명령 프롬프트, 셸 또는 PowerShell을 엽니다. 그런 다음, 각 작업 영역 FQDN에 대해 다음 명령을 실행합니다.

    ```nslookup <workspace FQDN>```
        
    각 nslookup의 결과는 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트의 두 개인 IP 주소 중 하나를 반환해야 합니다. 그렇지 않으면 사용자 지정 DNS 솔루션에는 잘못 구성된 항목이 있습니다.

    가능한 원인:
    - 문제 해결 명령을 실행하는 컴퓨팅 리소스가 DNS 확인에 DNS 서버를 사용하고 있지 않음
    - 프라이빗 엔드포인트를 만들 때 선택한 프라이빗 DNS 영역이 DNS 서버 VNet에 연결되어 있지 않음
    - Azure DNS 가상 서버 IP에 대한 조건부 전달자가 올바르게 구성되지 않음

<a id='dns-on-premises'></a>

## <a name="example-custom-dns-server-hosted-on-premises"></a>예: 온-프레미스에서 호스트된 사용자 지정 DNS 서버

이 아키텍처는 일반적인 허브 및 스포크 가상 네트워크 토폴로지를 사용합니다. ExpressRoute는 온-프레미스 네트워크에서 허브 가상 네트워크에 연결하는 데 사용됩니다. 사용자 지정 DNS 서버는 온-프레미스에서 호스트됩니다. 개별 가상 네트워크에는 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트 및 연결된 리소스가 포함됩니다. 이 토폴로지에서는 Azure DNS 가상 서버 IP 주소에 요청을 보낼 수 있는 DNS 서버를 호스트하는 또 다른 가상 네트워크가 있어야 합니다.

:::image type="content" source="./media/how-to-custom-dns/custom-dns-express-route.svg" alt-text="온-프레미스에서 호스트된 사용자 지정 DNS 토폴로지의 다이어그램":::

다음 단계에서는 이 토폴로지의 작동 방식을 설명합니다.

1. **프라이빗 DNS 영역을 만들고 DNS 서버 가상 네트워크에 연결합니다**.

    사용자 지정 DNS 솔루션이 Azure Machine Learning 작업 영역에서 작동하는지 확인하는 첫 번째 단계는 다음 도메인에서 두 개의 프라이빗 DNS 영역을 만드는 것입니다.
    
    **Azure 퍼블릭 지역**:
    - ``` privatelink.api.azureml.ms```
    - ``` privatelink.notebooks.azure.net```
    
    **Azure 중국 지역**:
    - ```privatelink.api.ml.azure.cn```
    - ```privatelink.notebooks.chinacloudapi.cn```
    
    **Azure US Government 지역**:
    - ```privatelink.api.ml.azure.us```
    - ```privatelink.notebooks.usgovcloudapi.net```

    프라이빗 DNS 영역을 만든 후 DNS 서버가 포함된 가상 네트워크인 DNS 서버 VNet에 연결해야 합니다. 

    > [!NOTE]
    > 가상 네트워크의 DNS 서버는 온-프레미스 DNS 서버와 별개입니다.

    프라이빗 DNS 영역은 영역 루트의 범위 내에 있는 모든 이름의 이름 확인을 재정의합니다. 이 재정의는 프라이빗 DNS 영역이 연결된 모든 가상 네트워크에 적용됩니다. 예를 들어, `privatelink.api.azureml.ms`를 기반으로 하는 프라이빗 DNS 영역이 가상 네트워크 foo에 연결된 경우 `bar.workspace.westus2.privatelink.api.azureml.ms`를 확인하는 가상 네트워크 foo의 모든 리소스는 privatelink.api.azureml.ms 영역에 나열된 모든 레코드를 수신합니다.

    그러나 프라이빗 DNS 영역에 나열된 레코드는 기본 Azure DNS 가상 서버 IP 주소를 사용하여 도메인을 확인하는 디바이스에만 반환됩니다. Azure DNS 가상 서버 IP 주소는 가상 네트워크의 컨텍스트 내에서만 유효합니다. 온-프레미스 DNS 서버를 사용하는 경우 Azure DNS 가상 서버 IP 주소를 쿼리하여 레코드를 검색할 수 없습니다.

    이 동작을 해결하려면 가상 네트워크에서 중간 DNS 서버를 만듭니다. 이 DNS 서버는 Azure DNS 가상 서버 IP 주소를 쿼리하여 가상 네트워크에 연결된 프라이빗 DNS 영역의 레코드를 검색할 수 있습니다.

    온-프레미스 DNS 서버는 네트워크 토폴로지 전체에 분산된 디바이스의 도메인을 확인하는 동안 DNS 서버에 대해 Azure Machine Learning 관련 도메인을 확인합니다. DNS 서버는 Azure DNS 가상 서버 IP 주소에서 해당 도메인을 확인합니다.

2. **DNS 서버 가상 네트워크에 연결된 프라이빗 DNS 영역을 대상으로 하는 프라이빗 DNS 통합을 사용하여 프라이빗 엔드포인트를 만듭니다**.

    다음 단계는 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트를 만드는 것입니다. 프라이빗 엔드포인트는 1단계에서 만든 두 프라이빗 DNS 영역을 모두 대상으로 합니다. 이렇게 하면 작업 영역에 대한 모든 통신이 Azure Machine Learning 가상 네트워크에서 프라이빗 엔드포인트를 통해 수행됩니다.

    > [!IMPORTANT]
    > 이 예가 올바르게 작동하려면 프라이빗 엔드포인트에 프라이빗 DNS 통합이 사용하도록 설정되어 있어야 합니다.

3. **DNS 서버에서 Azure DNS에 전달할 조건부 전달자를 만듭니다**.

    다음으로, Azure DNS 가상 서버에 대한 조건부 전달자를 만듭니다. 조건부 전달자는 DNS 서버가 항상 작업 영역과 관련된 FQDN에 대한 Azure DNS 가상 서버 IP 주소를 쿼리하는지 확인합니다. 이는 DNS 서버가 프라이빗 DNS 영역에서 해당 레코드를 반환할 것임을 의미합니다.

    조건부로 전달할 영역은 아래에 나열됩니다. Azure DNS 가상 서버 IP 주소는 168.63.129.16입니다.

    **Azure 퍼블릭 지역**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```     
    
    **Azure 중국 지역**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```

    **Azure US Government 지역**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > DNS 서버의 구성 단계는 여기에 포함되지 않습니다. 사용자 지정 DNS 서버로 사용할 수 있는 많은 DNS 솔루션이 사용 가능하기 때문입니다. 조건부 전달을 적절하게 구성하는 방법은 DNS 솔루션에 관한 설명서를 참조하세요.

4. **온-프레미스 DNS 서버에서 DNS 서버에 전달할 조건부 전달자를 만듭니다**.

    그런 다음, DNS 서버 가상 네트워크의 DNS 서버에 대한 조건부 전달자를 만듭니다. 이 전달자는 1단계에 나열된 영역에 사용됩니다. 이는 3단계와 유사하지만 Azure DNS 가상 서버 IP 주소로 전달하는 대신 온-프레미스 DNS 서버는 DNS 서버의 IP 주소를 대상으로 합니다. 온-프레미스 DNS 서버는 Azure에 있지 않으므로 프라이빗 DNS 영역에서 레코드를 직접 확인할 수 없습니다. 이 경우 DNS 서버는 온-프레미스 DNS 서버에서 Azure DNS 가상 서버 IP로 요청을 프록시합니다. 이렇게 하면 온-프레미스 DNS 서버가 DNS 서버 가상 네트워크에 연결된 프라이빗 DNS 영역에서 레코드를 검색할 수 있습니다. 

    조건부로 전달할 영역은 아래에 나열됩니다. 전달할 IP 주소는 DNS 서버의 IP 주소입니다.

    **Azure 퍼블릭 지역**:
    - ```api.azureml.ms```
    - ```notebooks.azure.net```
    - ```instances.ml.azure.us```
    
    **Azure 중국 지역**:
    - ```api.ml.azure.cn```
    - ```notebooks.chinacloudapi.cn```
    - ```instances.ml.azure.cn```
    
    **Azure US Government 지역**:
    - ```api.ml.azure.us```
    - ```notebooks.usgovcloudapi.net```
    - ```instances.ml.azure.us```

    > [!IMPORTANT]
    > DNS 서버의 구성 단계는 여기에 포함되지 않습니다. 사용자 지정 DNS 서버로 사용할 수 있는 많은 DNS 솔루션이 사용 가능하기 때문입니다. 조건부 전달을 적절하게 구성하는 방법은 DNS 솔루션에 관한 설명서를 참조하세요.

5. **작업 영역 도메인을 확인합니다**.

    이 시점에 모든 설정이 완료됩니다. 이름 확인에 온-프레미스 DNS 서버를 사용하고 Azure Machine Learning 프라이빗 엔드포인트의 경로를 포함하는 모든 클라이언트는 작업 영역에 계속 액세스할 수 있습니다.

    먼저 다음 FQDN의 주소에 대한 온-프레미스 DNS 서버를 쿼리하여 클라이언트를 시작합니다.

    **Azure 퍼블릭 지역**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.azureml.ms```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.azure.net```
    
    **Azure 중국 지역**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.cn```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.chinacloudapi.cn```
    
    **Azure US Government 지역**:
    - ```<per-workspace globally-unique identifier>.workspace.<region the workspace was created in>.api.ml.azure.us```
    - ```ml-<workspace-name, truncated>-<region>-<per-workspace globally-unique identifier>. notebooks.usgovcloudapi.net```

6. **온-프레미스 DNS 서버가 작업 영역 도메인을 재귀적으로 확인합니다.**

    온-프레미스 DNS 서버는 DNS 서버 5단계의 FQDN을 확인합니다. 조건부 전달자 설정(4단계)이 있었으므로 온-프레미스 DNS 서버는 확인을 위해 DNS 서버로 요청을 보냅니다.

7. **DNS 서버가 작업 영역 도메인을 Azure DNS에서 CNAME으로 해결합니다.**

    DNS 서버는 Azure DNS 5단계의 FQDN을 확인합니다. Azure DNS는 1단계에 나열된 도메인 중 하나로 응답합니다.

8. **온-프레미스 DNS 서버는 DNS 서버에서 작업 영역 도메인 CNAME 레코드를 재귀적으로 확인합니다**.

    온-프레미스 DNS 서버는 7단계에서 수신한 CNAME을 재귀적으로 확인합니다. 4단계에서 조건부 전달자 설정이 있었으므로 온-프레미스 DNS 서버는 확인을 위해 DNS 서버로 요청을 보냅니다.

9. **DNS 서버는 Azure DNS에서 작업 영역 도메인 CNAME 레코드를 재귀적으로 확인합니다**.

    DNS 서버는 7단계에서 수신한 CNAME을 재귀적으로 확인합니다. 3단계에서 조건부 전달자 설정이 있었으므로 DNS 서버는 확인을 위해 Azure DNS 가상 서버 IP 주소로 요청을 보냅니다.

10. **Azure DNS는 프라이빗 DNS 영역에서 레코드를 반환합니다**.

    프라이빗 DNS 영역에 저장된 해당 레코드는 DNS 서버에 반환됩니다. 이는 Azure DNS 가상 서버가 프라이빗 엔드포인트의 IP 주소를 반환하는 것을 의미합니다.

11. **온-프레미스 DNS 서버는 작업 영역 도메인 이름을 프라이빗 엔드포인트 주소로 확인합니다**.

    8단계에서 온-프레미스 DNS 서버에서 DNS 서버로 이루어진 쿼리는 궁극적으로 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트와 연결된 IP 주소를 반환합니다. 이 IP 주소는 원래 클라이언트에 반환되므로 이제 1단계에서 구성된 프라이빗 엔드포인트를 통해 Azure Machine Learning 작업 영역과 통신합니다.

<a id="hosts"></a>
## <a name="example-hosts-file"></a>예: 호스트 파일

`hosts` 파일은 Linux, macOS 및 Windows가 모두 로컬 컴퓨터의 이름 확인을 재정의하는 데 사용하는 텍스트 문서입니다. 파일에는 IP 주소 목록과 해당 호스트 이름이 포함되어 있습니다. 로컬 컴퓨터가 호스트 이름을 확인하려고 할 때 호스트 이름이 `hosts` 파일에 나열되어 있으면 이름이 해당 IP 주소로 확인됩니다.

> [!IMPORTANT]
> `hosts` 파일은 로컬 컴퓨터의 이름 확인만 재정의합니다. 여러 컴퓨터에서 `hosts` 파일을 사용하려면 각 컴퓨터에서 개별적으로 수정해야 합니다.

다음 표에는 `hosts` 파일의 위치가 나와 있습니다.

| 운영 체제 | 위치 |
| ----- | ----- |
| Linux | `/etc/hosts` |
| macOS | `/etc/hosts` |
| Windows | `%SystemRoot%\System32\drivers\etc\hosts` |

> [!TIP]
> 파일 이름은 확장자가 없는 `hosts`입니다. 파일 편집 시 관리자 권한을 사용하세요. 예를 들어 Linux 또는 macOS에서는 `sudo vi`를 사용할 수 있습니다. Windows에서 메모장을 관리자 권한으로 실행합니다.

다음은 Azure Machine Learning에 대한 `hosts` 파일 항목의 예입니다.

```
# For core Azure Machine Learning hosts
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms
10.1.0.5    fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.cert.api.azureml.ms
10.1.0.6    ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net

# For a compute instance named 'mycomputeinstance'
10.1.0.5    mycomputeinstance.eastus.instances.azureml.ms
```

`hosts` 파일에 대한 자세한 내용은 [https://wikipedia.org/wiki/Hosts_(file)](https://wikipedia.org/wiki/Hosts_(file))을 참조하세요.

#### <a name="troubleshooting"></a>문제 해결

위 단계를 수행한 후 가상 머신에서 작업 영역에 액세스할 수 없거나 Azure Machine learning 작업 영역에 대한 프라이빗 엔드포인트를 포함하는 가상 네트워크에서 컴퓨팅 리소스에 대한 작업이 실패하는 경우 아래 단계에 따라 원인을 파악합니다.

1. **프라이빗 엔드포인트에서 작업 영역 FQDN을 찾습니다**.

    다음 링크 중 하나를 사용하여 Azure Portal로 이동합니다.
    - [Azure 퍼블릭 지역](https://ms.portal.azure.com/?feature.privateendpointmanagedns=false)
    - [Azure 중국 지역](https://portal.azure.cn/?feature.privateendpointmanagedns=false)
    - [Azure US Government 지역](https://portal.azure.us/?feature.privateendpointmanagedns=false)

    Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트로 이동합니다. 작업 영역 FQDN은 “개요” 탭에 나열됩니다.

1. **Virtual Network 토폴로지에서 컴퓨팅 리소스에 액세스합니다**.

    Azure Virtual Network 토폴로지에서 컴퓨팅 리소스에 대한 액세스를 계속합니다. 이렇게 하려면 허브 가상 네트워크와 피어링된 가상 네트워크의 가상 머신에 액세스해야 할 수 있습니다. 

1. **작업 영역 FQDN을 확인합니다**.

    명령 프롬프트, 셸 또는 PowerShell을 엽니다. 그런 다음, 각 작업 영역 FQDN에 대해 다음 명령을 실행합니다.

    ```nslookup <workspace FQDN>```
        
    각 nslookup의 결과는 Azure Machine Learning 작업 영역에 대한 프라이빗 엔드포인트의 두 개인 IP 주소 중 하나를 생성해야 합니다. 그렇지 않으면 사용자 지정 DNS 솔루션에는 잘못 구성된 항목이 있습니다.

    가능한 원인:
    - 문제 해결 명령을 실행하는 컴퓨팅 리소스가 DNS 확인에 DNS 서버를 사용하고 있지 않음
    - 프라이빗 엔드포인트를 만들 때 선택한 프라이빗 DNS 영역이 DNS 서버 VNet에 연결되어 있지 않음
    - DNS 서버에서 Azure DNS 가상 서버 IP로 조건부 전달자가 올바르게 구성되지 않음
    - 온-프레미스 DNS 서버에서 DNS 서버로 조건부 전달자가 올바르게 구성되지 않음

## <a name="next-steps"></a>다음 단계

이 문서는 Azure Machine Learning 워크플로 보안에 대한 시리즈의 일부입니다. 이 시리즈의 다른 문서를 참조하세요.

* [Virtual Network 개요](how-to-network-security-overview.md)
* [작업 영역 리소스 보호](how-to-secure-workspace-vnet.md)
* [학습 환경 보호](how-to-secure-training-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [스튜디오 기능 사용](how-to-enable-studio-virtual-network.md)
* [방화벽 사용](how-to-access-azureml-behind-firewall.md)

프라이빗 엔드포인트를 DNS 구성에 통합하는 방법에 대한 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요.

사용자 지정 DNS 이름 또는 TLS 보안을 사용하여 모델을 배포하는 방법에 대한 자세한 내용은 [TLS를 사용하여 웹 서비스 보호](how-to-secure-web-service.md)를 참조하세요.
