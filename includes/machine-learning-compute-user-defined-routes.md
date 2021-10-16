---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 6ccdeea7b283ab4e4674e2a7959ead4553c43f2f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077918"
---
Azure Machine Learning __계산 인스턴스__ (공용 IP 사용) 또는 __계산 클러스터__ 를 사용 하는 경우 Azure Batch 관리 및 Azure Machine Learning 서비스에서 인바운드 트래픽을 허용 합니다. 공용 IP가 없는 계산 인스턴스 (미리 보기)에는이 인바운드 통신이 필요 하지 않습니다. 이 트래픽을 허용 하는 네트워크 보안 그룹이 동적으로 생성 되지만 방화벽이 있는 경우 UDR (사용자 정의 경로)도 만들어야 할 수 있습니다. 이 트래픽에 대해 UDR을 만들 때 **IP 주소** 또는 **서비스 태그** 를 사용 하 여 트래픽을 라우팅할 수 있습니다.

> [!IMPORTANT]
> 사용자 정의 경로에 서비스 태그를 사용하는 것은 현재 미리 보기로 제공되며 완전히 지원되지 않을 수 있습니다. 자세한 내용은 [Virtual Network 라우팅](../articles/virtual-network/virtual-networks-udr-overview.md#service-tags-for-user-defined-routes-preview)을 참조하세요.

> [!TIP]
> 공용 IP가 없는 계산 인스턴스 (미리 보기 기능)는이 인바운드 트래픽에 대해 UDR이 필요 하지 않지만 계산 클러스터 또는 공용 IP를 사용 하는 계산 인스턴스도 사용 하는 경우에도 이러한 UDRs가 필요 합니다.


# <a name="ip-address-routes"></a>[IP 주소 경로](#tab/ipaddress)

Azure Machine Learning Service의 경우 __주__ 및 __보조__ 지역 모두의 IP 주소를 추가해야 합니다. 보조 지역을 찾으려면 [Azure 쌍을 이루는 지역을 사용하여 비즈니스 연속성 및 재해 복구 확인](../articles/best-practices-availability-paired-regions.md#azure-regional-pairs)을 참조하세요. 예를 들어 Azure Machine Learning Service가 미국 동부 2에 있는 경우 보조 지역은 미국 중부입니다. 

Batch 서비스와 Azure Machine Learning 서비스의 IP 주소 목록을 가져오려면 [azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519) 를 다운로드 하 고 파일에서 `BatchNodeManagement.<region>` 및를 검색 합니다 `AzureMachineLearning.<region>` `<region>` . 여기서는 azure 지역입니다.

> [!IMPORTANT]
> IP 주소는 시간이 지남에 따라 변경될 수 있습니다.

UDR을 만들 때 __다음 홉 유형__ 을 __인터넷__ 으로 설정합니다. 다음 이미지는 Azure Portal의 IP 주소 기반 UDR 예제를 보여줍니다.

:::image type="content" source="./media/machine-learning-compute-user-defined-routes/user-defined-route.png" alt-text="사용자 정의 경로 구성 이미지":::

# <a name="service-tag-preview-routes"></a>[서비스 태그 (미리 보기) 경로](#tab/servicetag)

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
