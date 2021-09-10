---
title: Horizon on Azure VMware Solution 배포
description: VMware Horizon on Azure VMware Solution을 배포하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: d99c8dc76dcab1866d0c536be7fc505c2eec0cb6
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567967"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Horizon on Azure VMware Solution 배포 

>[!NOTE]
>이 문서에서는 이전에 Horizon 7이라고 한 VMware Horizon 제품에 중점을 둡니다. Horizon은 일부 공유 구성 요소가 있지만 Horizon Cloud on Azure와 다른 솔루션입니다. Azure VMware Solution의 주요 이점은 더 간단한 크기 조정 방법과 VMware Cloud Foundation 관리를 Azure Portal에 통합하는 것입니다.

가상 데스크톱 및 애플리케이션 플랫폼인 [VMware Horizon](https://www.vmware.com/products/horizon.html)®은 데이터 센터에서 실행되며 간편한 중앙 집중식 관리를 제공합니다. 어디서나 모든 디바이스에서 가상 데스크톱 및 애플리케이션을 제공합니다. Horizon을 사용하면 Windows 및 Linux 가상 데스크톱, RDS(원격 데스크톱 서버) 호스팅 애플리케이션, 데스크톱 및 물리적 머신에 대한 연결을 생성하고 중개할 수 있습니다.

여기에서는 특히 Horizon on Azure VMware Solution을 배포하는 데 중점을 둡니다. VMware Horizon에 대한 일반적인 내용은 Horizon 프로덕션 설명서를 참조하세요.

* [VMware Horizon이란?](https://www.vmware.com/products/horizon.html)

* [VMware Horizon에 대한 자세한 정보](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizon 참조 아키텍처](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Azure VMware Solution에 Horizon을 도입함으로써 이제 Azure 플랫폼에 두 개의 VDI(가상 데스크톱 인프라) 솔루션이 있습니다. 다음 다이어그램에서는 상위 수준에서 주요 차이점을 요약합니다.

:::image type="content" source="media/vmware-horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizon on Azure VMware Solution과 Horizon Cloud on Azure 간의 차이점을 보여 주는 다이어그램" border="false":::

Horizon 8 릴리스 라인의 Horizon 2006 이상 버전은 온-프레미스 및 Azure VMware Solution 배포를 모두 지원합니다. 온-프레미스에서 지원되지만 Azure VMware Solution에서는 지원되지 않는 몇 가지 Horizon 기능이 있습니다. Horizon 에코시스템의 다른 제품도 지원됩니다. 자세한 내용은 [기능 패리티 및 상호 운용성](https://kb.vmware.com/s/article/80850)을 참조하세요.

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>하이브리드 클라우드에 Horizon 배포

Horizon CPA(Cloud Pod Architecture)로 온-프레미스와 Azure 데이터 센터를 상호 연결하여 하이브리드 클라우드 환경에 Horizon을 배포할 수 있습니다. CPA는 배포를 스케일 업하고 하이브리드 클라우드를 구축하며 비즈니스 연속성 및 재해 복구를 위한 중복도를 제공합니다.  자세한 내용은 [기존 Horizon 7 환경 확장](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)을 참조하세요.

>[!IMPORTANT]
>CPA는 확장된 배포가 아닙니다. 각 Horizon Pod는 고유하며, 개별 Pod에 속하는 모든 연결 서버는 단일 위치에 있어야 하고, 네트워크 관점에서 동일한 브로드캐스트 도메인에서 실행되어야 합니다.

온-프레미스 또는 프라이빗 데이터 센터와 마찬가지로 Azure VMware Solution 프라이빗 클라우드에 Horizon을 배포할 수 있습니다. 다음 섹션에서 Horizon 온-프레미스 및 Azure VMware Solution을 배포하는 주요 차이점에 대해 설명합니다.

_Azure 프라이빗 클라우드_ 는 일반적으로 Horizon 설명서에서 사용되는 용어인 _VMware SDDC_ 와 개념적으로 동일합니다. 이 문서의 나머지 부분에서는 두 용어를 같은 의미로 사용합니다.

Horizon on Azure VMware Solution에서 구독 라이선스를 관리하려면 Horizon Cloud Connector가 필요합니다. Horizon Connection Server와 함께 Azure Virtual Network에 Cloud Connector를 배포할 수 있습니다.

>[!IMPORTANT]
>Horizon on Azure VMware Solution에 대한 Horizon 컨트롤 플레인 지원은 아직 제공되지 않습니다. Horizon Cloud Connector의 VHD 버전을 다운로드해야 합니다.

## <a name="vcenter-cloud-admin-role"></a>vCenter Cloud 관리자 역할

Azure VMware Solution은 SDDC 서비스이고 Azure에서는 Azure VMware Solution에서 SDDC의 수명 주기를 관리하므로 Azure VMware Solution의 vCenter 권한 모델은 설계상 제한됩니다.

고객은 제한된 vCenter 권한 세트가 있는 클라우드 관리자 역할을 사용해야 합니다. Horizon 제품은 Azure VMware Solution의 클라우드 관리자 역할로 작동하도록 수정되었습니다. 특히 다음과 같습니다.

* Azure VMware Solution에서 실행되도록 즉시 복제 프로비저닝이 수정되었습니다.

* 특정 vSAN 정책(VMware_Horizon)은 Horizon과 함께 작동하도록 Azure VMware Solution에 생성되었으므로, Horizon용으로 배포된 SDDC에서 사용 가능하고 사용해야 합니다.

* View Storage Accelerator라고도 하는 vSphere CBRC(콘텐츠 기반 읽기 캐시)는 Azure VMware Solution에서 실행할 때 사용하지 않게 설정됩니다.

>[!IMPORTANT]
>CBRC를 다시 설정해서는 안 됩니다.

>[!NOTE]
>Azure VMware Solution은 Horizon 8 분기에 Horizon 2006(Horizon 8이라고도 함) 이상을 배포하고 Horizon Connection Server 설치 프로그램에서 **Azure** 옵션을 선택하는 한 특정 Horizon 설정을 자동으로 구성합니다.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Horizon on Azure VMware Solution 배포 아키텍처

일반적인 Horizon 아키텍처 디자인은 Pod와 블록 전략을 사용합니다. 블록은 단일 vCenter이지만 여러 블록을 결합하여 Pod를 만듭니다. Horizon Pod는 Horizon 스케일링 한도에 따라 결정되는 조직 단위입니다. 각 Horizon Pod에는 별도의 관리 포털이 있으므로 표준 디자인 방법은 Pod 수를 최소화하는 것입니다.

모든 클라우드에는 자체 네트워크 연결 체계가 있습니다. VMware SDDC 네트워킹/NSX Edge와 결합된 Azure VMware Solution 네트워크 연결은 온-프레미스와 다른 Horizon을 배포하기 위한 고유한 요구 사항을 제공합니다.

각 Azure 프라이빗 클라우드 및 SDDC는 다음을 가정하여 4,000개의 데스크톱 또는 애플리케이션 세션을 처리할 수 있습니다.

* 워크로드 트래픽은 LoginVSI 작업 작업자 프로필에 맞춰 조정됩니다.

* 프로토콜 트래픽만 고려하고 사용자 데이터는 고려하지 않습니다.

* NSX Edge는 대형으로 구성됩니다.

>[!NOTE]
>워크로드 프로필과 요구 사항은 다를 수 있으므로 사용 사례에 따라 결과가 달라질 수 있습니다. 사용자 데이터 볼륨은 워크로드 컨텍스트에서 스케일링 한도를 낮출 수 있습니다. 적절하게 배포 크기를 조정하고 계획합니다. 자세한 내용은 [Horizon 배포를 위한 Azure VMware Solution 호스트 크기 조정](#size-azure-vmware-solution-hosts-for-horizon-deployments) 섹션에서 크기 조정 지침을 참조하세요.

Azure 프라이빗 클라우드와 SDDC 최대한도를 고려했을 때 Horizon Connection Server와 VMware UAG(Unified Access Gateway)가 Azure Virtual Network 내에서 실행되는 배포 아키텍처를 권장합니다. 각 Azure 프라이빗 클라우드 및 SDDC를 효과적으로 블록으로 전환합니다. 차례로 Azure VMware Solution에서 실행 중인 Horizon의 스케일링 성능을 극대화합니다.

Azure Virtual Network에서 Azure 프라이빗 클라우드/SDDC로의 연결은 ExpressRoute FastPath를 사용하여 구성해야 합니다. 다음 다이어그램에서는 기본 Horizon Pod 배포를 보여 줍니다.

:::image type="content" source="media/vmware-horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="ExpressPath Fast Path를 사용하는 일반적인 Horizon Pod 배포를 보여 주는 다이어그램" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Horizon on Azure VMware Solution을 스케일링하는 네트워크 연결

이 섹션에서는 Horizon on Azure VMware Solution을 스케일링하는 데 도움이 되는 몇 가지 일반적인 배포 예제를 통해 네트워크 아키텍처를 개략적으로 설명합니다. 특히 중요한 네트워킹 요소에 중점을 두고 있습니다. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Azure VMware Solution의 단일 Horizon Pod

:::image type="content" source="media/vmware-horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware Solution의 단일 Horizon Pod를 보여 주는 다이어그램" border="false":::

단일 Horizon Pod는 미국 동부 지역에 하나의 Horizon Pod만 배포하기 때문에 가장 단순한 배포 시나리오입니다.  각 프라이빗 클라우드와 SDDC는 4,000개의 데스크톱 세션을 처리할 것으로 예상되므로 최대 Horizon Pod 크기를 배포합니다.  최대 3개의 프라이빗 클라우드/SDDC 배포를 계획할 수 있습니다.

Azure Virtual Network에 배포된 Horizon 인프라 VM(가상 머신)을 사용하면 Horizon Pod당 12,000개의 세션에 도달할 수 있습니다. Azure Virtual Network에 대한 각 프라이빗 클라우드와 SDDC 간의 연결은 ExpressRoute 빠른 경로입니다.  프라이빗 클라우드 간에는 동-서 트래픽이 필요하지 않습니다. 

이 기본 배포 예제에서는 다음과 같은 주요 사항을 가정합니다.

* CPA(Cloud Pod Architecture)를 사용하여 이 새 Pod에 연결하려는 온-프레미스 Horizon Pod가 없습니다.

* 최종 사용자는 인터넷을 통해 가상 데스크톱에 연결합니다(온-프레미스 데이터 센터를 통해 연결).

VPN 또는 ExpressRoute 회로를 통해 Azure Virtual Network의 AD 도메인 컨트롤러를 온-프레미스 AD에 연결합니다.

기본 예제의 변형은 온-프레미스 리소스에 대한 연결을 지원하는 것일 수 있습니다. 예를 들어 사용자는 데스크톱에 액세스하고 가상 데스크톱 애플리케이션 트래픽을 생성하거나 CPA를 사용하여 온-프레미스 Horizon Pod에 연결합니다.

이 다이어그램은 온-프레미스 리소스에 대한 연결을 지원하는 방법을 보여 줍니다. Azure Virtual Network에 회사 네트워크를 연결하려면 ExpressRoute 회로가 필요합니다.  또한 ExpressRoute Global Reach를 사용하여 각 프라이빗 클라우드 및 SDDC와 회사 네트워크를 연결해야 합니다.  SDDC에서 ExpressRoute 회로 및 온-프레미스 리소스로의 연결을 허용합니다. 

:::image type="content" source="media/vmware-horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Azure Virtual Network에 대한 회사 네트워크의 연결을 보여 주는 다이어그램" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>여러 지역에 걸친 Azure VMware Solution의 여러 Horizon Pod

또 다른 시나리오는 여러 Pod에서 Horizon을 스케일링하는 것입니다.  이 시나리오에서는 서로 다른 두 지역에 두 개의 Horizon Pod를 배포하고 CPA를 사용하여 연합합니다. 이전 예제의 네트워크 구성과 비슷하지만 몇 가지 추가 지역 간 링크가 있습니다. 

각 지역의 Azure Virtual Network를 다른 지역의 프라이빗 클라우드/SDDC에 연결합니다. 이를 통해 CPA 연결의 Horizon 연결 서버가 관리 중인 모든 데스크톱에 연결할 수 있습니다. 이 구성에 프라이빗 클라우드/SDDC를 추가하면 전체 24,000개의 세션으로 스케일링할 수 있습니다. 

동일한 지역에 두 개의 Horizon Pod를 배포하는 경우에도 동일한 원칙이 적용됩니다.  *별도의 Azure Virtual Network* 에 두 번째 Horizon Pod를 배포해야 합니다. 단일 Pod 예제와 마찬가지로 ExpressRoute와 Global Reach 사용하여 회사 네트워크 및 온-프레미스 Pod를 이 다중 Pod/지역 예제에 연결할 수 있습니다. 

:::image type="content" source="media/vmware-horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" 여러 지역에 걸쳐 Azure VMware Solution의 여러 Horizon Pod를 보여 주는 다이어그램" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Horizon 배포를 위한 Azure VMware Solution 호스트 크기 조정 

Azure VMware Solution에서 실행 중인 호스트에서 Horizon의 크기 조정 방법은 Horizon 온-프레미스보다 간단합니다.  Azure VMware Solution 호스트가 표준화되어 있기 때문입니다.  정확하게 호스트의 크기를 조정하면 VDI 요구 사항을 지원하는 데 필요한 호스트 수를 결정할 수 있습니다.  데스크톱당 비용을 결정하는 데 핵심입니다.

### <a name="sizing-tables"></a>테이블 크기 조정

Horizon 가상 데스크톱의 특정 vCPU/vRAM 요구 사항은 고객의 특정 워크로드 프로필에 따라 달라집니다.   가상 데스크톱의 vCPU/vRAM 요구 사항을 확인하려면 MSFT와 VMware 영업 팀에 요청하세요. 

| VM당 vCPU | VM당 vRAM(GB) | 인스턴스 | 100개의 VM | 200개의 VM | 300개의 VM | 400개의 VM | 500개의 VM | 600개의 VM | 700개의 VM | 800개의 VM | 900개의 VM | 1,000개의 VM | 2,000개의 VM | 3,000개의 VM | 4,000개의 VM | 5,000개의 VM | 6,000개의 VM | 6,400개의 VM |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Horizon 크기 조정 입력

계획된 워크로드에 대해 수집해야 하는 사항은 다음과 같습니다.

* 동시 데스크톱 수

* 데스크톱당 필수 vCPU

* 데스크톱당 필수 vRAM

* 데스크톱당 필요한 스토리지

일반적으로 VDI 배포는 호스트 크기를 결정하는 CPU 또는 RAM의 제약을 받습니다. 성능 테스트로 유효성이 검사된 LoginVSI Knowledge Worker 형식의 워크로드에 대한 다음과 같은 예제를 살펴보겠습니다.

* 2,000개 동시 데스크톱 배포

* 데스크톱당 2vCPU.

* 데스크톱당 4GB vRAM.

* 데스크톱당 50GB 스토리지

이 예에서 호스트의 총수는 18로 계산되어 호스트당 VM 밀도는 111이 됩니다.

>[!IMPORTANT]
>고객 워크로드는 LoginVSI Knowledge Worker의 이 예제와 다릅니다. 배포를 계획하는 과정에서 특정 크기 조정 및 성능 요구 사항에 맞게 VMware EUC SE와 작업합니다. 호스트 크기 조정을 완료하고 그에 따라 조정하기 전에 실제 계획된 워크로드를 사용하여 고유한 성능 테스트를 실행해야 합니다.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horizon on Azure VMware Solution 라이선스 

Horizon on Azure VMware Solution을 실행하는 전체 비용의 4가지 구성 요소입니다. 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware Solution 용량 비용

가격 책정에 대한 정보는 [Azure VMware Solution 가격 책정](https://azure.microsoft.com/pricing/details/azure-vmware/) 페이지를 참조하세요.

### <a name="horizon-licensing-cost"></a>Horizon 라이선스 비용

CCU(동시 사용자) 또는 NU(명명된 사용자)일 수 있는 Azure VMware Solution과 함께 사용할 수 있는 두 가지 라이선스가 있습니다.

* Horizon 구독 라이선스

* Horizon 유니버설 구독 라이선스

가까운 미래에 Horizon on Azure VMware Solution만 배포하는 경우 비용이 더 저렴한 Horizon 구독 라이선스를 사용합니다.

Azure VMware Solution 및 온-프레미스에 배포된 경우 재해 복구 사용 사례로 Horizon 유니버설 구독 라이선스를 선택합니다. 그러나 온-프레미스 배포를 위한 vSphere 라이선스가 포함되어 있으므로 비용이 더 많이 듭니다.

VMware EUC 영업 팀과 협력하여 요구 사항에 따라 Horizon 라이선스 비용을 결정합니다.

### <a name="azure-instance-types"></a>Azure Instance Types

Horizon 인프라에 필요한 Azure 가상 머신 크기를 파악하려면 [Azure VMware 솔루션에 Horizon 설치](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)를 참조하세요.

## <a name="references"></a>참조
[Linux용 Horizon 에이전트의 시스템 요구 사항](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>다음 단계
VMware Horizon on Azure VMware Solution에 대한 자세한 내용은 [VMware Horizon FAQ](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)를 참조하세요.
