---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/12/2021
ms.author: larryfr
ms.openlocfilehash: c2ad7408f00d8abf4cb5afdbdba44af5e0779380
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122603973"
---
Azure Machine Learning __컴퓨팅 인스턴스__ 또는 __컴퓨팅 클러스터__ 를 사용하는 경우 Azure Batch 관리 및 Azure Machine Learning Service의 인바운드 트래픽을 허용합니다. 이 트래픽에 대한 사용자 정의 경로를 만들 때 **IP 주소** 또는 **서비스 태그** 를 사용하여 트래픽을 라우팅할 수 있습니다.

> [!IMPORTANT]
> 사용자 정의 경로에 서비스 태그를 사용하는 것은 현재 미리 보기로 제공되며 완전히 지원되지 않을 수 있습니다. 자세한 내용은 [Virtual Network 라우팅](/azure/virtual-network/virtual-networks-udr-overview#service-tags-for-user-defined-routes-preview)을 참조하세요.

# <a name="ip-address-routes"></a>[IP 주소 경로](#tab/ipaddress)

Azure Machine Learning Service의 경우 __주__ 및 __보조__ 지역 모두의 IP 주소를 추가해야 합니다. 보조 지역을 찾으려면 [Azure 쌍을 이루는 지역을 사용하여 비즈니스 연속성 및 재해 복구 확인](/azure/best-practices-availability-paired-regions#azure-regional-pairs)을 참조하세요. 예를 들어 Azure Machine Learning Service가 미국 동부 2에 있는 경우 보조 지역은 미국 중부입니다. 

Batch 서비스 및 Azure Machine Learning Service의 IP 주소 목록을 가져오려면 다음 방법 중 하나를 사용합니다.

* [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드하고 파일에서 `BatchNodeManagement.<region>` 및 `AzureMachineLearning.<region>`을 검색합니다. 여기서 `<region>`은 Azure 지역입니다.

* [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 정보를 다운로드합니다. 다음 예제는 IP 주소 정보를 다운로드하고 미국 동부 2 지역(주) 및 미국 중부 지역(보조)에 대한 정보를 필터링합니다.

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > 미국 버지니아, 미국 아리조나 지역 또는 중국 동부 2 지역을 사용하는 경우 이러한 명령은 IP 주소를 반환하지 않습니다. 대신 다음 링크 중 하나를 사용하여 IP 주소 목록을 다운로드합니다.
    >
    > * [Azure Government에 대한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure 중국에 대한 Azure IP 범위 및 서비스 태그](https://www.microsoft.com//download/details.aspx?id=57062)

> [!IMPORTANT]
> IP 주소는 시간이 지남에 따라 변경될 수 있습니다.

UDR을 만들 때 __다음 홉 유형__ 을 __인터넷__ 으로 설정합니다. 다음 이미지는 Azure Portal의 IP 주소 기반 UDR 예제를 보여줍니다.

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="사용자 정의 경로 구성 이미지":::

# <a name="service-tag-routes"></a>[서비스 태그 경로](#tab/servicetag)

다음 서비스 태그에 대한 사용자 정의 경로를 만듭니다.

* `AzureMachineLearning`
* `BatchNodeManagement.<region>`, 여기서 `<region>`은 Azure 지역입니다.

다음 명령은 이러한 서비스 태그의 경로를 추가하는 방법을 보여줍니다.

```azurecli
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n AzureMLRoute --address-prefix AzureMachineLearning --next-hop-type Internet
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n BatchRoute --address-prefix BatchNodeManagement.westus2 --next-hop-type Internet
```

---

UDR을 구성하는 방법에 대한 자세한 내용은 [라우팅 테이블을 사용하여 네트워크 트래픽 라우팅](/azure/virtual-network/tutorial-create-route-table-portal)을 참조하세요.