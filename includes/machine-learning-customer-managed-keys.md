---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 06/24/2021
ms.author: larryfr
ms.openlocfilehash: 99d885ab35eb04963d9d2b0c6c9c14dbd10f2e2c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559732"
---
> [!IMPORTANT]
> 이 Cosmos DB 인스턴스는 __구독__ 의 Microsoft 관리되는 리소스 그룹에 생성됩니다. 다음 서비스는 이 리소스 그룹에도 만들어지며 고객 관리형 키 구성에서 사용됩니다.
> * Azure Storage 계정
> * Azure Search
>
> 이러한 서비스는 Azure 구독에서 만들어지기 때문에 이러한 서비스 인스턴스에 대한 요금이 청구됩니다. 
>
> 관리형 리소스 그룹의 이름은 `<AML Workspace Resource Group Name><GUID>` 형식으로 지정됩니다. Azure Machine Learning 작업 영역에서 프라이빗 엔드포인트를 사용하는 경우 가상 네트워크도 이 리소스 그룹에 만들어집니다. 이 VNet은 이 리소스 그룹의 서비스와 Azure Machine Learning 작업 영역 간의 통신을 보호하는 데 사용됩니다.
> 
> * 이 Cosmos DB 인스턴스 또는 이 그룹에서 자동으로 생성된 리소스를 포함하는 __리소스 그룹을 삭제하지 마세요__. 리소스 그룹, Cosmos DB 인스턴스 등을 삭제해야 하는 경우에는 이를 사용하는 Azure Machine Learning 작업 영역을 삭제해야 합니다. 연결된 작업 영역이 삭제되면 리소스 그룹, Cosmos DB 인스턴스 및 자동으로 만들어진 기타 리소스가 삭제됩니다.
> * 이 Cosmos DB 계정에서 사용하는 [__요청 단위__](../articles/cosmos-db/request-units.md)는 필요에 따라 자동으로 확장됩니다. __최소__ RU는 __1200__ 입니다. __최대__ RU는 __12000__ 입니다.
> * 생성된 __Cosmos DB 인스턴스에서 사용할 자체 VNet을 제공할 수 없습니다__. __가상 네트워크를 수정할 수도 없습니다__. 예를 들어 사용하는 IP 주소 범위를 변경할 수 없습니다.
> 
> Azure Cosmos DB 인스턴스의 추가 비용을 예측하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용합니다.