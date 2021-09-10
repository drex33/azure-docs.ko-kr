---
title: 공유 갤러리를 사용하여 VM 이미지 공유
description: 공유 이미지 갤러리를 사용하여 조직 전반에서 Linux VM 이미지를 공유하는 방법을 알아봅니다.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 6/8/2021
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: c94e040d5047b25b8d260792fd362e50a29b9601
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696293"
---
# <a name="shared-image-galleries-overview"></a>공유 이미지 갤러리 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Shared Image Gallery는 이미지를 기준으로 구조와 조직을 빌드하는 데 도움이 되는 서비스입니다. Shared Image Galleries는 다음을 제공합니다.

- 글로벌 이미지 복제
- 보다 쉽게 관리할 수 있도록 이미지 버전 관리 및 그룹화
- 가용성 영역을 지원하는 지역의 ZRS(영역 중복 스토리지) 계정이 포함된 고가용성 이미지. ZRS는 영역 장애 발생 시 보다 나은 복원력을 제공합니다.
- 프리미엄 스토리지 지원(Premium_LRS)
- Azure RBAC를 사용하여 구독 간은 물론 AD(Active Directory) 테넌트 간에도 공유합니다.
- 각 지역에서 이미지 복제본으로 배포 스케일링

공유 이미지 갤러리를 사용하면 조직 내부 및 외부의 다른 사용자, 서비스 주체 또는 AD 그룹과 이미지를 공유할 수 있습니다. 배포의 크기를 더 빠르게 조정하기 위해 여러 지역에 공유 이미지를 복제할 수 있습니다.

이미지는 생성 방식에 따라 완전한 VM(연결된 데이터 디스크 포함)의 복사본일 수도 있고 단순한 OS 디스크의 복사본일 수도 있습니다. 이미지에서 VM을 만드는 경우 이미지의 VHD 복사본을 사용하여 새 VM의 디스크를 만듭니다. 이미지는 스토리지에 유지되며 새 VM을 만들 때 계속 반복해서 사용할 수 있습니다.

유지할 이미지가 굉장히 많고 회사 전체에서 이미지를 사용할 수 있게 만들려면 Shared Image Gallery를 리포지토리로 사용하면 됩니다. 

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다.

| 리소스 | Description|
|----------|------------|
| **이미지 원본** | 이는 이미지 갤러리에서 **이미지 버전** 을 만드는 데 사용할 수 있는 리소스입니다. 이미지 소스는 [일반화 또는 특수화](#generalized-and-specialized-images)된 기존 Azure VM, 관리형 이미지, 스냅샷, VHD 또는 다른 이미지 갤러리의 이미지 버전일 수 있습니다. |
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리** 는 이미지를 관리하고 공유하는 데 사용되는 리포지토리이지만 액세스할 수 있는 사람을 제어할 수 있습니다. |
| **이미지 정의** | 이미지 정의는 갤러리 내에 생성되고, 내부적으로 사용하기 위해 충족해야 할 요구 사항과 이미지에 대한 정보를 전달합니다. 여기에는 이미지가 Windows인지, Linux인지 여부, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. |
| **이미지 버전** | **이미지 버전** 은 갤러리를 사용하는 경우 VM을 만들 때 사용합니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 관리되는 이미지와 마찬가지로 **이미지 버전** 을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다. |

<br>

![갤러리에서 한 이미지의 여러 버전을 보유하는 방법을 보여주는 그래픽](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>이미지 정의

이미지 정의는 이미지 버전의 논리적 그룹화입니다. 이미지 정의에는 이미지를 만든 이유, 이미지가 사용되는 OS, 이미지 사용에 대한 기타 정보가 포함되어 있습니다. 이미지 정의는 이미지 만들기와 관련하여 모든 세부 정보에 대한 계획과 비슷합니다. 이미지 정의가 아닌 정의에서 만든 이미지 버전의 VM을 배포합니다.

각 이미지 정의에는 **Publisher**, **Offer**, **SKU** 의 세 가지 매개 변수가 있으며 이러한 매개 변수를 조합해서 사용합니다. 세 매개 변수는 특정 이미지 정의를 찾는 데 사용됩니다. 3개 값을 모두 공유하지는 않으면서 1개 또는 2개의 값을 공유하는 이미지 버전을 사용할 수 있습니다.  예를 들어 다음은 3개의 이미지 정의와 해당 값입니다.

|이미지 정의|게시자|제안|SKU|
|---|---|---|---|
|myImage1|Contoso|재무|백 엔드|
|myImage2|Contoso|재무|프런트 엔드|
|myImage3|테스트|재무|프런트 엔드|

이러한 세 가지 정의는 모두 고유한 값 세트를 갖습니다. 형식은 최신 버전의 Marketplace 이미지를 가져오기 위해 Azure PowerShell에서 [Azure Marketplace 이미지](./windows/cli-ps-findimage.md)의 게시자, 제품 및 SKU를 지정하는 방법과 비슷합니다. 각 이미지 정의에는 이와 같이 고유한 값 세트가 필요합니다.

다음 매개 변수는 포함할 수 있는 이미지 버전 유형을 결정합니다.

- 운영 체제 상태 - OS 상태를 [일반화 또는 특수화](#generalized-and-specialized-images)로 설정할 수 있습니다. 이 필드는 필수 필드입니다.
- 운영 체제 - Windows 또는 Linux입니다. 이 필드는 필수 필드입니다.
- Hyper-V 생성 - 이미지가 1세대 또는 [2세대](generation-2.md) Hyper-V VHD 중 무엇으로 만들었는지 여부를 지정합니다. 기본값은 1세대입니다.


다음은 리소스를 보다 쉽게 추적할 수 있도록 이미지 정의에 대해 설정할 수 있는 다른 매개 변수입니다.

- 설명 - 설명을 사용하여 이미지 정의가 존재하는 이유를 자세히 알려줍니다. 예를 들어 애플리케이션이 미리 설치된 프런트 엔드 서버의 이미지 정의가 있을 수 있습니다.
- Eula - 이미지 정의와 관련된 최종 사용자 사용권 계약을 가리키는 데 사용할 수 있습니다.
- 개인정보처리방침 및 릴리스 정보 - 릴리스 정보와 개인정보처리방침을 Azure 스토리지에 저장하고 이미지 정의 과정에서 액세스할 수 있도록 URI를 제공합니다.
- 수명 종료 날짜 - 이미지 정의의 모든 이미지 버전에 대한 기본 수명 종료 날짜를 설정합니다. 수명 종료 날짜는 정보 제공용입니다. 사용자는 수명 종료 날짜가 지난 이미지와 버전에서 VM을 계속 만들 수 있습니다.
- 태그 - 이미지 정의를 만들 때 태그를 추가할 수 있습니다. 태그에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.
- 최소/최대 vCPU 및 메모리 권장 사항 - 이미지에 대한 vCPU 및 메모리 권장 사항이 있는 경우 해당 정보를 이미지 정의에 연결할 수 있습니다.
- 허용되지 않는 디스크 유형 - VM의 스토리지 요구 사항에 대한 정보를 제공할 수 있습니다. 예를 들어 이미지가 표준 HDD 디스크에 적합하지 않은 경우 허용되지 않는 유형 목록에 추가합니다.
- Marketplace 이미지(`-PurchasePlanPublisher`, `-PurchasePlanName` 및 `-PurchasePlanProduct`)에 대한 구매 계획 정보입니다. 구매 계획 정보에 대한 자세한 내용은 [Azure Marketplace에서 이미지 찾기](./windows/cli-ps-findimage.md) 및 [이미지를 만들 때 Azure Marketplace 구매 계획 정보 제공](marketplace-images.md)을 참조하세요.


## <a name="image-versions"></a>이미지 버전

VM을 만드는 데 사용하는 것은 **이미지 버전** 입니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. **이미지 버전** 을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다.

이미지 버전의 속성은 다음과 같습니다.

- 버전 번호. 이는 이미지 버전의 이름으로 사용됩니다. 항상 다음과 같은 형식입니다. MajorVersion.MinorVersion.Patch. VM을 만들 때 **최신** 을 사용하도록 지정하면 가장 높은 MajorVersion, 다음으로 MinorVersion, 다음으로 패치를 기준으로 최신 이미지가 선택됩니다. 
- 원본. 원본은 VM, 관리 디스크, 스냅샷, 관리형 이미지 또는 다른 이미지 버전일 수 있습니다. 
- 최신 항목에서 제외. 버전을 최신 이미지 버전으로 사용하지 않도록 할 수 있습니다. 
- 수명 주기 끝. 이미지 버전의 수명 종료 날짜를 나타냅니다. 수명 종료 날짜는 정보 제공용입니다. 사용자는 수명 종료 날짜가 지난 버전에서 VM을 계속 만들 수 있습니다.


## <a name="generalized-and-specialized-images"></a>일반화 이미지와 특수화 이미지

Shared Image Gallery는 두 가지 운영 체제 상태를 지원합니다. 일반적으로 이미지를 만드는 데 사용되는 VM을 일반화한 후 이미지를 가져와야 합니다. 일반화는 VM에서 머신 및 사용자 관련 정보를 제거하는 프로세스입니다. Windows의 경우 Sysprep 도구가 사용됩니다. Linux의 경우 [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` 또는 `-deprovision+user` 매개 변수를 사용할 수 있습니다.

특수화 VM은 머신 관련 정보와 계정을 제거하는 프로세스를 거치지 않았습니다. 또한 특수화 이미지로 만든 VM에는 `osProfile`이 연결되지 않습니다. 즉, 특수화 이미지는 이점도 있지만 몇 가지 제한이 있습니다.

- 특수화 이미지로 만든 VM과 확장 집합은 더 빠르게 작동할 수 있습니다. 이러한 이미지는 이미 첫 번째 부팅을 거친 원본으로 만들기 때문에 이러한 이미지로 만든 VM은 더 빠르게 부팅됩니다.
- VM에 로그인하는 데 사용할 수 있는 계정은 해당 VM이 생성된 특수화 이미지를 사용하여 만드는 VM에서도 사용할 수 있습니다.
- VM은 이미지를 가져온 VM의 **컴퓨터 이름** 을 사용합니다. 충돌을 방지하려면 컴퓨터 이름을 변경해야 합니다.
- `osProfile`은 `secrets`를 사용하여 중요한 정보를 VM에 전달하는 방식을 결정합니다. 이로 인해 KeyVault, WinRM 및 `osProfile`의 `secrets`를 사용하는 기타 기능을 사용할 때 문제가 발생할 수 있습니다. 경우에 따라 MSI(관리되는 서비스 ID)를 사용하여 이러한 제한을 해결할 수도 있습니다.

## <a name="regional-support"></a>국가별 지원

모든 퍼블릭 지역은 대상 지역이 될 수 있지만, 특정 지역에서는 고객이 액세스 권한을 얻기 위해 요청 프로세스를 거쳐야 합니다. 오스트레일리아 중부 또는 오스트레일리아 중부 2와 같은 지역의 허용 목록에 구독을 추가하도록 요청하려면 [액세스 요청](/troubleshoot/azure/general/region-access-request-process)을 제출합니다.

## <a name="limits"></a>제한 

Shared Image Gallery를 사용하여 리소스를 배포할 때 구독당 제한이 있습니다.
- 구독마다, 지역마다 공유 이미지 갤러리 100개
- 구독마다, 지역마다 이미지 정의 1,000개
- 구독마다, 지역마다 이미지 버전 10,000개
- 구독마다, 지역마다 이미지 버전 복제본 10개
- 이미지에 연결된 모든 디스크는 크기가 1TB 이하여야 합니다.

자세한 내용은 현재 사용량을 확인하는 방법에 대한 [제한을 기준으로 리소스 사용량 확인](../networking/check-usage-against-limits.md) 예제를 참조하세요.
 
## <a name="scaling"></a>확장
공유 이미지 갤러리를 사용하면 Azure에서 이미지를 보관하도록 하려는 복제본 수를 지정할 수 있습니다. 이렇게 하면 VM 배포를 여러 복제본으로 확대함으로써 단일 복제본 과부하로 인해 인스턴스 생성 처리가 제한될 가능성을 줄일 수 있으므로 다중 VM 배포 시나리오에 도움이 됩니다.

이제 Shared Image Gallery를 사용하면 가상 머신 확장 집합에 최대 1,000개의 VM 인스턴스를 배포할 수 있습니다(관리형 이미지는 600개까지). 이미지 복제본은 보다 나은 배포 성능, 안정성 및 일관성을 제공합니다.   지역에 필요한 규모에 따라 각 대상 지역에 서로 다른 복제본 수를 설정할 수 있습니다. 각 복제본은 이미지의 전체 복사본이기 때문에 이렇게 하면 복제본을 추가할 때마다 선형적으로 배포를 확장할 수 있습니다. 두 개의 이미지 또는 지역이 똑같지는 않지만, 지역에서 복제본을 사용하는 방법에 대한 일반적인 지침은 다음과 같습니다.

- 가상 머신 확장 집합이 아닌 배포의 경우 - 동시에 20개의 VM을 만들 때마다 복제본 하나를 유지하는 것이 좋습니다. 예를 들어 한 지역에서 동일한 이미지를 사용하여 동시에 120개의 VM을 만드는 경우 이미지 복제본을 6개 이상 유지하는 것이 좋습니다. 
- 가상 머신 확장 집합 배포의 경우 - 최대 600개 인스턴스를 배포하는 확장 집합마다 하나 이상의 복제본을 유지하는 것이 좋습니다. 예를 들어 한 지역에서 동일한 이미지를 사용하여 동시에 5개의 확장 집합을 만들고 확장 집합마다 VM 600개를 배포하는 경우 이미지 복제본을 5개 이상 유지하는 것이 좋습니다. 

이미지 크기, 콘텐츠, OS 형식 등의 요소를 감안하여 항상 복제본 수를 초과 프로비저닝하는 것이 좋습니다.

![이미지를 확장하는 방법을 보여 주는 그래픽](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>고가용성 이미지 만들기

[Azure ZRS(영역 중복 스토리지)](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/)는 지역에서 가용성 영역에 문제가 발생할 때 복원력을 제공합니다. 이제 Shared Image Gallery가 일반 공급되므로, 가용성 영역을 지원하는 지역의 ZRS 계정에 이미지를 저장하도록 선택할 수 있습니다. 

각 대상 지역의 계정 유형을 선택할 수도 있습니다. 기본 스토리지 계정 유형은 Standard_LRS지만, 가용성 영역이 지원되는 지역에는 Standard_ZRS를 선택할 수 있습니다. ZRS의 지역별 가용성에 대한 자세한 내용은 [데이터 중복성](../storage/common/storage-redundancy.md)을 참조하세요.

![ZRS를 보여주는 그래픽](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>복제
공유 이미지 갤러리를 사용하여 다른 Azure 지역에 이미지를 자동으로 복제할 수도 있습니다. 조직에 적합한 방식으로 각 공유 이미지 버전을 여러 지역에 복제할 수 있습니다. 한 가지 예는 다중 지역에서 최신 이미지를 항상 복제하는 것이지만 이전 버전은 모두 1개 지역에서만 사용 가능합니다. 공유 이미지 버전의 스토리지 비용을 절약하는 데 도움이 될 수 있습니다. 

공유 이미지 버전이 복제되는 지역은 생성 시간 후 업데이트할 수 있습니다. 여러 지역에 복제하는 데 걸리는 시간은 복사되는 데이터의 양과 버전이 복제되는 지역 수에 따라 다릅니다. 경우에 따라 몇 시간이 걸릴 수 있습니다. 복제가 진행 중일 때 지역별 복제 상태를 볼 수 있습니다. 한 지역에서 이미지 복제가 완료되면 해당 지역에서 해당 이미지 버전을 사용하여 VM 또는 확장 집합을 배포할 수 있습니다.

![이미지를 복제하는 방법을 보여 주는 그래픽](./media/shared-image-galleries/replication.png)

## <a name="access"></a>액세스 권한

Shared Image Gallery, 이미지 정의 및 이미지 버전은 모두 리소스이므로 기본 제공되는 네이티브 Azure RBAC 컨트롤을 사용하여 공유할 수 있습니다. Azure RBAC를 사용하면 다른 사용자, 서비스 주체, 그룹에 리소스를 공유할 수 있습니다. 리소스가 생성된 테넌트 외부의 개별 리소스에 대한 액세스를 공유할 수도 있습니다. 사용자에게 공유 이미지 버전에 대한 액세스 권한이 부여되면 사용자는 VM 또는 Virtual Machine Scale Set를 배포할 수 있습니다.  사용자가 액세스할 수 있는 항목을 이해하는 데 도움이 되는 공유 행렬은 다음과 같습니다.

| 공유한 항목     | 공유 이미지 갤러리 | 이미지 정의 | 이미지 버전 |
|----------------------|----------------------|--------------|----------------------|
| 공유 이미지 갤러리 | 예                  | 예          | 예                  |
| 이미지 정의     | 예                   | 예          | 예                  |

최상의 환경을 위해 갤러리 수준에서 공유하는 것이 좋습니다. 개별 이미지 버전을 공유하는 것은 좋지 않습니다. Azure RBAC에 대한 자세한 내용은 [Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

다중 테넌트 앱 등록을 사용하여 테넌트 간에도 이미지를 대규모로 공유할 수 있습니다. 테넌트 간에 이미지를 공유하는 방법에 대한 자세한 내용은 [Azure CLI](./linux/share-images-across-tenants.md) 또는 [PowerShell](./windows/share-images-across-tenants.md)을 사용하여 “Azure 테넌트 간 갤러리 VM 이미지 공유”를 참조하세요.

## <a name="billing"></a>결제
공유 이미지 갤러리 서비스 사용에 대한 추가 비용은 없습니다. 다음 리소스에 대한 비용이 청구됩니다.
- 각 복제본을 저장하는 데 드는 스토리지 비용. 스토리지 비용은 스냅샷으로 청구되며 이미지 버전의 사용되는 크기, 이미지 버전의 복제본 수 및 버전이 복제되는 지역 수를 기반으로 합니다. 
- 이미지 버전을 원본 지역에서 복제된 지역으로 처음 복제할 때 발생하는 네트워크 송신 요금. 후속 복제본은 지역 내에서 처리되므로 추가 비용이 없습니다. 

예를 들어 10GB의 스토리지만 사용하는 127GB OS 디스크의 이미지와 하나의 빈 32GB 데이터 디스크가 있다고 가정해 보겠습니다. 각 이미지의 사용 크기는 10GB에 불과합니다. 이미지는 3개 지역에 복제되고 각 지역에는 2개의 복제본이 있습니다. 각각 10GB를 사용하는 총 6개의 스냅샷이 있습니다. 10GB의 사용 크기를 기준으로 각 스냅샷에 대한 스토리지 비용이 청구됩니다. 추가 두 지역에 복사할 첫 번째 복제본에 대한 네트워크 송신 요금을 지불합니다. 각 지역의 스냅샷 가격에 대한 자세한 내용은 [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요. 네트워크 송신에 대한 자세한 내용은 [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/bandwidth/)을 참조하세요.


## <a name="updating-resources"></a>리소스 업데이트

이미지 갤러리 리소스를 만든 후 변경할 수 있습니다. 다음과 같은 제한이 있습니다.
 
공유 이미지 갤러리:
- Description

이미지 정의:
- 권장 vCPU
- 권장 메모리
- Description
- 수명 주기 끝

이미지 버전:
- 지역 복제본 수
- 대상 지역
- 최신 항목에서 제외
- 수명 주기 끝

## <a name="sdk-support"></a>SDK 지원

다음 SDK를 사용하면 공유 이미지 갤러리를 만들 수 있습니다.

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.JS](/javascript/api/overview/azure/arm-compute-readme)
- [Python](/python/api/overview/azure/virtualmachines)
- [Go](/azure/go/)

## <a name="templates"></a>템플릿

템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

## <a name="frequently-asked-questions"></a>질문과 대답 

* [구독 간에 모든 Shared Image Gallery 리소스를 나열하는 방법은 무엇인가요?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [기존 이미지를 Shared Image Gallery로 이동할 수 있나요?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [특수화 디스크로 이미지 버전을 만들 수 있나요?](#can-i-create-an-image-version-from-a-specialized-disk)
* [Shared Image Gallery 리소스를 만든 후 다른 구독으로 이동할 수 있나요?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [Azure 중국 21Vianet, Azure 독일, Azure Government Cloud 등의 클라우드 간에 이미지 버전을 복제할 수 있나요?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [이미지 버전을 구독 간에 복제할 수 있나요?](#can-i-replicate-my-image-versions-across-subscriptions)
* [Azure AD 테넌트 간에 이미지 버전을 공유할 수 있나요?](#can-i-share-image-versions-across-azure-ad-tenants)
* [대상 지역 간에 이미지 버전을 복제하려면 얼마나 걸리나요?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [원본 지역과 대상 지역의 차이점은 무엇인가요?](#what-is-the-difference-between-source-region-and-target-region)
* [이미지 버전을 만드는 동안 원본 영역을 지정하는 방법은 무엇인가요?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 무엇인가요?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [이미지 정의 및 이미지 버전을 만드는 위치와 다른 위치에 Shared Image Gallery를 만들 수 있나요?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [Shared Image Gallery 사용 요금은 얼마인가요?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [Shared Image Gallery와 이미지 정의 및 이미지 버전을 만들려면 어떤 API 버전을 사용해야 하나요?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [이미지 버전으로 공유 VM 또는 Virtual Machine Scale Set를 만들려면 어떤 API 버전을 사용해야 하나요?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [관리형 이미지를 사용하여 만든 Virtual Machine Scale Set가 Shared Image Gallery 이미지를 사용하도록 업데이트할 수 있나요?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>구독 간에 모든 공유 이미지 갤러리 리소스를 나열하는 방법은 무엇인가요?

Azure Portal에서 액세스할 수 있는 구독 간에 Shared Image Gallery 리소스를 모두 나열하려면 아래 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 페이지를 아래로 스크롤하고 **모든 리소스** 를 선택합니다.
1. 모든 리소스를 나열하려는 모든 구독을 선택합니다.
1. **Shared Image Gallery** 형식의 리소스를 찾습니다.
  
사용 권한이 있는 구독 간에 공유 이미지 갤러리 리소스를 나열하려면 Azure CLI에서 다음 명령을 사용하세요.

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

자세한 내용은 [Azure CLI](update-image-resources-cli.md) 또는 [PowerShell](update-image-resources-powershell.md)을 사용하여 **갤러리 리소스 관리** 를 참조하세요.

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>기존 이미지를 공유 이미지 갤러리로 이동할 수 있나요?
 
예. 가지고 있는 이미지의 유형에 따라 3가지 시나리오가 있습니다.

 시나리오 1: 관리형 이미지가 있는 경우 해당 이미지에서 이미지 정의와 이미지 버전을 만들 수 있습니다. 자세한 내용은 [Azure CLI](image-version-managed-image-cli.md) 또는 [PowerShell](image-version-managed-image-powershell.md)을 사용하여 **관리형 이미지를 이미지 버전으로 마이그레이션** 을 참조하세요.

 시나리오 2: 비관리형 이미지가 있는 경우 해당 이미지에서 관리형 이미지를 만든 다음, 관리형 이미지에서 이미지 정의와 이미지 버전을 만들 수 있습니다. 

 시나리오 3: 로컬 파일 시스템에 VHD가 있는 경우 VHD를 관리형 이미지에 업로드한 다음, 해당 이미지로 이미지 정의와 이미지 버전을 만들 수 있습니다.

- Windows VM의 VHD인 경우 [VHD 업로드](./windows/upload-generalized-managed.md)를 참조하세요.
- Linux VM용 VHD일 경우 [VHD 업로드](./linux/upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>특수한 디스크에서 이미지 버전을 만들 수 있나요?

예, [CLI](vm-specialized-image-version-cli.md), [PowerShell](vm-specialized-image-version-powershell.md) 또는 API를 사용하여 특수화된 이미지에서 VM을 만들 수 있습니다. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>Shared Image Gallery 리소스를 만든 후 다른 구독으로 이동할 수 있나요?

아니요, Shared Image Gallery 리소스를 다른 구독으로 이동할 수는 없습니다. [Azure CLI](image-version-another-gallery-cli.md) 또는 [PowerShell](image-version-another-gallery-powershell.md)을 사용하여 갤러리의 이미지 버전을 다른 지역에 복제하거나 다른 갤러리의 이미지를 복사할 수 있습니다.

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>Azure 중국 21Vianet, Azure 독일, Azure Government Cloud 등의 클라우드 간에 이미지 버전을 복제할 수 있나요?

아니요, 클라우드 간에 이미지 버전을 복제할 수는 없습니다.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>내 이미지 버전을 구독 간에 복제할 수 있나요?

아니요. 구독에서 지역 간에 이미지 버전을 복제하고 RBAC를 통해 다른 구독에서 사용할 수 있습니다.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>Azure AD 테넌트 간에 이미지 버전을 공유할 수 있나요? 

예, RBAC를 사용하여 테넌트 간에 개별 버전을 공유할 수 있습니다. 그러나 대규모로 공유하려면 [PowerShell](./windows/share-images-across-tenants.md) 또는 [CLI](./linux/share-images-across-tenants.md)를 사용하여 "Azure 테넌트 간에 갤러리 이미지 공유"를 참조하세요.

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>대상 지역 간에 이미지 버전을 복제하려면 얼마나 걸리나요?

이미지 버전 복제 시간은 이미지의 크기와 이미지가 복제되는 지역 수에 따라 크게 달라집니다. 그러나 최상의 결과를 얻으려면 이미지를 작게 유지하고 원본 지역과 대상 지역을 가깝게 유지하는 것이 가장 좋습니다. -ReplicationStatus 플래그를 사용하여 복제의 상태를 확인할 수 있습니다.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>원본 지역과 대상 지역 사이의 차이점은 무엇인가요?

원본 지역은 이미지 버전이 생성되는 지역이며, 대상 지역은 이미지 버전의 복사본이 저장되는 지역입니다. 각 이미지 버전에 하나의 원본 지역만 사용할 수 있습니다. 또한 이미지 버전을 만들 때 원본 지역 위치를 대상 지역 중 하나로 전달하세요.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>이미지 버전을 만드는 동안 원본 영역을 지정하는 방법은 무엇인가요?

이미지 버전을 만들 때 CLI의 **--location** 태그와 PowerShell의 **-Location** 태그를 사용하여 원본 지역을 지정할 수 있습니다. 이미지 버전을 만들려면 기본 이미지로 사용 중인 관리되는 이미지가 이미지 버전을 만들려는 위치와 동일한 위치에 있는지 확인하세요. 또한 이미지 버전을 만들 때 원본 지역 위치를 대상 지역 중 하나로 전달하세요.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 무엇인가요?

각 지역에 만들 이미지 버전 복제본 수를 지정하는 방법은 두 가지가 있습니다.
 
1. 지역당 만들려는 복제본의 수를 지정하는 지역당 복제본 수. 
2. 지역당 복제본 수가 지정되지 않은 경우 지역당 기본 개수인 일반적인 복제본 수입니다. 

지역당 복제본 수를 지정하려면 다음과 같이 해당 지역에 만들려는 복제본 수와 위치를 전달합니다. 예를 들어 "미국 중남부=2"를 전달할 수 있습니다. 

각 위치에 지역당 복제본 수가 지정되지 않은 경우 기본 복제본 수가 일반적인 복제본 수로 지정됩니다. 

CLI에서 일반적인 복제본 수를 지정하려면 `az sig image-version create` 명령에 **--replica-count** 인수를 사용합니다.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>이미지 정의 및 이미지 버전을 만드는 위치와 다른 위치에 Shared Image Gallery를 만들 수 있나요?

예, 구성할 수 있습니다. 하지만 리소스 그룹, Shared Image Gallery, 이미지 정의 및 이미지 버전을 같은 위치에 유지하는 것이 좋습니다.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>공유 이미지 갤러리 사용 요금은 얼마인가요?

공유 이미지 갤러리 서비스 사용 요금은 없습니다(단, 이미지 버전을 저장할 때 발생하는 스토리지 요금과 원본 지역에서 대상 지역으로 이미지 버전을 복제할 때 발생하는 네트워크 송신 요금 제외).

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>Shared Image Gallery와 이미지 정의 및 이미지 버전을 만들려면 어떤 API 버전을 사용해야 하나요?

공유 이미지 갤러리, 이미지 정의 및 이미지 버전으로 작업하려면 API 버전 2018-06-01을 사용하는 것이 좋습니다. ZRS(영역 중복 스토리지)에는 버전 2019-03-01 이상이 필요합니다.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>이미지 버전으로 공유 VM 또는 Virtual Machine Scale Set를 만들려면 어떤 API 버전을 사용해야 하나요?

이미지 버전을 사용한 VM 및 Virtual Machine Scale Set 배포의 경우 API 버전 2018-04-01 이상을 사용하는 것이 좋습니다.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>관리형 이미지를 사용하여 만든 Virtual Machine Scale Set가 Shared Image Gallery 이미지를 사용하도록 업데이트할 수 있나요?

예, OS 유형, Hyper-V 생성 및 데이터 디스크 레이아웃이 이미지 간에 일치한다면 확장 집합 이미지 참조를 관리형 이미지에서 Shared Image Gallery 이미지로 업데이트할 수 있습니다.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Shared Image Gallery 문제 해결
Shared Image Gallery 리소스에 대한 작업을 수행하는 동안 문제가 발생한 경우 [문제 해결 가이드](troubleshooting-shared-images.md)에서 일반적인 오류 목록을 참조하세요.

또한 [Q&A](/answers/topics/azure-virtual-machines-images.html)에서 `azure-virtual-machines-images`에 대한 질문을 게시하고 태그를 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure CLI](shared-images-cli.md) 또는 [PowerShell](shared-images-powershell.md)을 사용하여 공유 이미지를 배포하는 방법을 알아봅니다.
