---
title: 관리자 가이드 | Microsoft Docs
description: 이 가이드는 Azure Lab Services를 사용하여 랩 계정을 만들고 관리하는 관리자를 지원합니다.
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 698b6ddb6474031e7d19f2029156197b7d6a7741
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177273"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services - 관리자 가이드
대학 클라우드 리소스를 관리하는 IT(정보 기술) 관리자는 일반적으로 학교의 랩 계정을 설정합니다. 랩 계정을 설정하고 나면 관리자 또는 교육자가 해당 계정 내에 포함된 랩을 만듭니다. 이 문서에서는 관련 Azure 리소스에 대한 대략적인 개요 및 해당 리소스를 만들기 위한 지침을 제공합니다.

![랩 계정의 Azure 리소스를 대략적으로 보여 주는 다이어그램.](./media/administrator-guide/high-level-view.png)

- 랩은 Azure Lab Services가 소유한 Azure 구독 내에서 호스트됩니다.
- 랩 계정, Shared Image Gallery 및 이미지 버전은 구독 내에서 호스트됩니다.
- 동일한 리소스 그룹에서 랩 계정 및 공유 이미지 갤러리를 사용할 수 있습니다. 이 다이어그램에서는 서로 다른 리소스 그룹에 있습니다.

아키텍처에 대한 자세한 내용은 [랩 아키텍처 기본 사항](./classroom-labs-fundamentals.md)을 참조하세요.

## <a name="subscription"></a>Subscription
대학에는 하나 이상의 Azure 구독이 있을 수 있습니다. 구독은 랩 계정을 포함하여 구독 내에서 사용되는 모든 Azure 리소스 및 서비스에 대한 청구 및 보안을 관리하는 데 사용할 수 있습니다.

랩 계정과 해당 구독 간의 관계는 다음과 같은 이유로 중요합니다.

- 청구는 랩 계정이 포함된 구독을 통해 보고됩니다.
- 구독의 Azure AD(Active Directory) 테넌트에 있는 사용자에게 Azure Lab Services에 대한 액세스 권한을 부여할 수 있습니다. 사용자를 랩 계정 소유자 또는 참가자로 추가하거나 랩 작성자 또는 랩 소유자로 추가할 수 있습니다.

랩 및 VM(가상 머신)은 Azure Lab Services에서 소유한 구독 내에서 관리 및 호스트됩니다.

## <a name="resource-group"></a>Resource group
구독은 하나 이상의 리소스 그룹을 포함합니다. 리소스 그룹은 동일한 솔루션 내에서 함께 사용되는 Azure 리소스의 논리 그룹을 만드는 데 사용됩니다.  

랩 계정을 만들 때 랩 계정이 포함된 리소스 그룹을 구성해야 합니다. 

리소스 그룹은 [Shared Image Gallery](#shared-image-gallery)를 만들 때도 필요합니다. 랩 계정 및 Shared Image Gallery는 동일한 리소스 그룹 또는 별도의 두 리소스 그룹에 둘 수 있습니다. 여러 솔루션에서 이미지 갤러리를 공유하려는 경우 두 번째 방법을 사용하는 것이 좋을 수 있습니다.

랩 계정을 만들면 동시에 공유 이미지 갤러리를 자동으로 만들고 연결할 수 있습니다.  이 옵션을 선택하면 랩 계정 및 공유 이미지 갤러리가 별도의 리소스 그룹에 만들어집니다. 이 [랩 계정 생성 시 Shared Image Gallery 구성](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) 자습서에 설명된 단계를 사용하는 경우 이 동작이 표시됩니다. 이 문서의 처음에 표시되는 이미지 또한 이 구성을 사용합니다. 

랩 계정 또는 Shared Image Gallery의 리소스 그룹을 만든 후에는 변경할 수 *없으므로* 먼저 신중하게 리소스 그룹의 구조를 계획하는 것이 좋습니다. 이러한 리소스에 대한 리소스 그룹을 변경해야 하는 경우 랩 계정 또는 Shared Image Gallery를 삭제하고 다시 만들어야 합니다.

## <a name="lab-account"></a>랩 계정

랩 계정은 하나 이상의 랩 컨테이너 역할을 합니다. Azure Lab Services를 시작할 때는 단일 랩 계정을 사용하는 것이 가장 일반적입니다. 랩 사용량이 증가함에 따라 나중에 더 많은 랩 계정을 만들도록 선택할 수 있습니다.

다음 목록에서는 둘 이상의 랩 계정이 유용할 수 있는 주요 시나리오를 보여 줍니다.

- **여러 랩에서 다양한 정책 요구 사항 관리**

    랩 계정을 설정하는 경우 다음과 같이 랩 계정에서 *모든* 랩에 적용되는 정책을 설정합니다.
    - 랩에서 액세스할 수 있는 공유 리소스가 포함된 Azure 가상 네트워크. 예를 들어 가상 네트워크 내에서 공유 데이터 세트에 액세스해야 하는 랩 집합이 있을 수 있습니다.
    - 랩에서 VM을 만드는 데 사용할 수 있는 가상 머신 이미지. 예를 들어 [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Azure Marketplace 이미지에 액세스해야 하는 랩 집합이 있을 수 있습니다.

    각 랩에서 고유한 정책 요구 사항이 있는 경우 각 랩을 별도로 관리하기 위한 별도의 랩 계정을 만드는 것이 유용할 수 있습니다.

- **각 랩 계정에 별도의 예산 할당**
  
    단일 랩 계정을 통해 모든 랩 비용을 보고하는 대신 보다 명확하게 할당된 예산이 필요할 수 있습니다. 예를 들어 대학의 수학과, 컴퓨터 과학과 등에 대한 별도의 랩 계정을 만들어 학과 간에 예산을 분배할 수 있습니다.  그런 다음 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 사용하여 각 개별 랩 계정의 비용을 확인할 수 있습니다.

- **활성 또는 프로덕션 랩에서 파일럿 랩 격리**
  
    활성 또는 프로덕션 랩에 전혀 영향을 주지 않고 랩 계정에 대한 정책 변경을 파일럿하려는 경우가 있을 수 있습니다. 이러한 유형의 시나리오에서는 파일럿을 목적으로 별도의 랩 계정을 만들면 변경 내용을 분리시킬 수 있습니다. 

## <a name="lab"></a>랩

랩에는 학생마다 각각 할당된 VM(가상 머신)이 있습니다.  일반적으로 다음을 기대할 수 있습니다.

- 각 클래스당 하나의 랩을 보유합니다.
- 사용 중인 각 반기, 사분기 또는 기타 교육 시스템에 대해 새로운 랩 집합을 만듭니다. 동일한 이미지를 사용해야 하는 클래스의 경우 [Shared Image Gallery](#shared-image-gallery)를 사용해야 합니다. 이러한 방식으로 랩 및 교육 기간에 걸쳐 이미지를 다시 사용할 수 있습니다.

랩을 구조화하는 방법을 결정할 때 다음 사항을 고려합니다.

- **랩 내의 모든 VM은 게시된 것과 동일한 이미지로 배포됨**

    따라서 다른 랩 이미지를 동시에 게시해야 하는 클래스가 있는 경우 각 이미지에 대해 별도의 랩을 만들어야 합니다.
  
- **사용 할당량이 랩 수준에서 설정되며 랩 내의 모든 사용자에게 적용됨**

    사용자마다 다른 할당량을 설정하려면 별도의 랩을 만들어야 합니다. 그러나 할당량을 설정한 후에 특정 사용자에게 시간을 추가해 줄 수 있습니다.
  
- **시작 또는 종료 일정은 랩 수준에서 설정되고 랩 내의 모든 VM에 적용됨**

    할당량 설정과 마찬가지로 사용자마다 다른 일정을 설정해야 하는 경우 각 일정에 대해 별도의 랩을 만들어야 합니다.

기본적으로 각 랩에는 자체 가상 네트워크가 있습니다.  가상 네트워크 피어링을 사용하도록 설정한 경우 각 랩에는 지정된 가상 네트워크와 피어링된 자체 서브넷이 있습니다.

## <a name="shared-image-gallery"></a>공유 이미지 갤러리

공유 이미지 갤러리는 랩 계정에 연결되며 이미지를 저장하기 위한 중앙 리포지토리로 사용됩니다. 교육자가 랩의 템플릿 VM에서 이미지 내보내기를 선택하면 해당 이미지가 갤러리에 저장됩니다. 교육자가 템플릿 VM을 변경하고 내보낼 때마다 갤러리에 새 이미지 정의 및/또는 버전이 만들어집니다.  

강사는 새로운 랩을 만들 때 Shared Image Gallery에 있는 이미지 버전을 게시할 수 있습니다. 갤러리에는 여러 버전의 이미지가 저장되어 있지만 교육자는 랩 생성 중에 최신 버전만 선택할 수 있습니다.  최신 버전은 MajorVersion, MinorVersion, Patch의 순서로 가장 큰 값을 기준으로 선택됩니다.  버전 관리에 대한 자세한 내용은 [이미지 버전](../virtual-machines/shared-image-galleries.md#image-versions)을 참조하세요.

공유 이미지 갤러리 서비스는 몇 개의 랩만으로 시작하는 경우 당장 필요하지 않을 수 있는 선택적 리소스입니다. 그러나 공유 이미지 갤러리는 추가 랩으로 스케일 업할 때 유용한 여러 이점을 제공합니다.

- **템플릿 VM 이미지의 버전 저장 및 관리**

    사용자 지정 이미지를 만들거나 Azure Marketplace 갤러리의 이미지를 변경(소프트웨어, 구성 등)할 때 유용합니다.  예를 들어 일반적으로 교육자는 서로 다른 소프트웨어 또는 도구를 설치해야 합니다. 학생들에게 이러한 필수 구성 요소를 직접 설치하도록 요청할 필요 없이 여러 버전의 템플릿 VM 이미지를 Shared Image Gallery로 내보낼 수 있습니다. 그런 다음 새 랩을 만들 때 이러한 이미지 버전을 사용할 수 있습니다.

- **여러 랩에서 템플릿 VM 이미지 공유 및 다시 사용**

    새 랩을 만들 때마다 이미지를 처음부터 구성할 필요 없이 이미지를 저장하고 다시 사용할 수 있습니다. 예를 들어 여러 클래스가 동일한 이미지를 사용해야 하는 경우 이미지를 한 번만 만들어 Shared Image Gallery로 내보내면 여러 랩에서 공유할 수 있습니다.

- **랩 외부의 다른 환경에서 자체 사용자 지정 이미지 업로드**

    [랩 컨텍스트 외부의 다른 환경에서 사용자 지정 이미지를 업로드](how-to-attach-detach-shared-image-gallery.md)할 수 있습니다.  예를 들어 자체 물리적 랩 환경 또는 Azure VM에서 공유 이미지 갤러리로 이미지를 업로드할 수 있습니다.  이미지를 갤러리로 가져오면 이미지를 사용하여 랩을 만들 수 있습니다.

공유 이미지를 논리적으로 그룹화하려면 다음 중 하나를 수행할 수 있습니다.

- 여러 개의 공유 이미지 갤러리를 만듭니다. 각 랩 계정은 하나의 Shared Image Gallery에만 연결할 수 있으므로 이 옵션을 사용하려면 여러 랩 계정을 만들어야 합니다.
- 또는 여러 랩 계정에서 공유하는 단일 Shared Image Gallery를 사용합니다. 이 경우 각 랩 계정은 해당 계정의 랩에 적용되는 이미지만 사용할 수 있습니다.

## <a name="naming"></a>이름 지정

Azure Lab Services를 시작할 때 리소스 그룹, 랩 계정, 랩 및 Shared Image Gallery에 대한 명명 규칙을 설정하는 것이 좋습니다. 설정하는 명명 규칙은 조직의 요구 사항에 따라 고유하지만 다음 표에서는 일반적인 지침을 간략하게 설명합니다.

| 리소스 유형 | 역할 | 제안된 패턴 | 예 |
| ------------- | ---- | ----------------- | -------- | 
| Resource group | 하나 이상의 랩 계정과 하나 이상의 공유 이미지 갤러리 포함 | \<organization short name\>-\<environment\>-rg<ul><li>**조직 약식 이름** 은 리소스 그룹이 지원하는 조직의 이름을 식별합니다.</li><li>**환경** 은 *파일럿* 또는 *프로덕션* 과 같은 리소스 환경을 식별합니다.</li><li>**Rg** 는 리소스 종류인 *리소스 그룹* 을 의미합니다.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 랩 계정 | 하나 이상의 랩 포함 | \<organization short name\>-\<environment\>-la<ul><li>**조직 약식 이름** 은 리소스 그룹이 지원하는 조직의 이름을 식별합니다.</li><li>**환경** 은 *파일럿* 또는 *프로덕션* 과 같은 리소스 환경을 식별합니다.</li><li>**La** 는 리소스 종류인 *랩 계정* 을 의미합니다.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 랩 | 하나 이상의 VM 포함 |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**클래스 이름** 은 랩에서 지원하는 클래스의 이름을 식별합니다.</li><li>**시간 범위** 는 클래스가 제공되는 시간 범위를 식별합니다.</li>**교육자 식별자** 는 랩을 소유하는 교육자를 식별합니다.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 공유 이미지 갤러리 | 하나 이상의 VM 이미지 버전 포함 | \<organization short name\>갤러리 | contosouniversitylabsgallery |

다른 Azure 리소스의 이름 지정에 대한 자세한 내용은 [Azure 리소스의 명명 규칙](/azure/architecture/best-practices/naming-conventions)을 참조하세요.

## <a name="regionslocations"></a>지역\위치

Azure Lab Services 리소스를 설정하는 경우 리소스를 호스트할 데이터 센터의 지역 또는 위치를 제공해야 합니다. 다음 섹션에서는 지역 또는 위치가 랩 설정에 관련된 각 리소스에 미치는 영향을 설명합니다.

### <a name="resource-group"></a>Resource group

지역은 리소스 그룹에 대한 정보가 저장되는 데이터 센터를 지정합니다. 리소스 그룹 내에 포함된 Azure 리소스는 부모와 다른 지역에 있을 수 있습니다.

### <a name="lab-account"></a>랩 계정

랩 계정의 위치는 이 리소스가 있는 지역을 나타냅니다.  

### <a name="lab"></a>랩    

랩이 있는 위치는 다음과 같은 요인에 따라 달라집니다.

  - **랩 계정이 가상 네트워크와 피어링됨**
  
    동일한 지역에 있는 경우 [가상 네트워크와 랩 계정을 피어링](./how-to-connect-peer-virtual-network.md)할 수 있습니다.  랩 계정이 가상 네트워크와 피어링되면 랩 계정 및 가상 네트워크와 동일한 지역에 랩이 자동으로 만들어집니다.

    > [!NOTE]
    > 랩 계정이 가상 네트워크와 피어링되면 **랩 작성자가 랩 위치를 선택하도록 허용** 설정을 사용할 수 없습니다. 자세한 내용은 [랩 작성자가 랩의 위치를 선택하도록 허용](./allow-lab-creator-pick-lab-location.md)을 참조하세요.
    
  - **피어링된 가상 네트워크가 없음 *또한* 랩 작성자가 랩 위치를 선택할 수 없음**
  
    랩 계정과 피어링된 가상 네트워크가 *없고* [랩 작성자가 랩 위치를 선택할 수 *없으면*](./allow-lab-creator-pick-lab-location.md) 랩은 사용 가능한 VM 용량이 있는 지역에 자동으로 만들어집니다.  특히 Azure Lab Services는 [랩 계정과 동일한 지리적 위치 내에 있는 지역](https://azure.microsoft.com/global-infrastructure/regions)에서 가용성을 찾습니다.

  - **피어링된 가상 네트워크가 없음 *또한* 랩 작성자가 랩 위치를 선택할 수 있음**
       
    랩 계정과 피어링된 가상 네트워크가 *없고* [랩 작성자가 랩 위치를 선택할 수 *있으면*](./allow-lab-creator-pick-lab-location.md) 랩 작성자가 선택할 수 있는 위치는 사용 가능한 용량에 따라 달라집니다.

> [!NOTE]
> 지역에 충분한 VM 용량을 확보하려면 랩을 만들 때 먼저 랩 계정을 통해 용량을 요청해야 합니다.

일반적인 규칙은 리소스의 지역을 해당 사용자에게 가장 가까운 지역으로 설정하는 것입니다. 이는 랩의 경우 학생들에게 가장 가까운 랩을 만드는 것을 의미합니다. 학생들이 전 세계에 있는 온라인 과정의 경우 최선의 판단으로 중앙에 위치한 랩을 만듭니다. 또는 학생 지역에 따라 클래스를 여러 랩으로 분할할 수 있습니다.

## <a name="vm-sizing"></a>VM 크기 조정

관리자 또는 랩 작성자가 랩을 만들 때 클래스룸 요구에 따라 다음과 같은 다양한 VM 크기 중에서 선택할 수 있습니다. 사용 가능한 크기는 랩 계정이 있는 지역에 따라 다릅니다.

다음 표에서는 다양한 VM 크기가 두 개 이상의 VM 시리즈에 매핑됩니다.  사용 가능한 용량에 따라 VM 크기별로 나열된 VM 시리즈 중 일부를 Lab Services에서 사용할 수 있습니다.  예를 들어 ‘작은’ VM 크기는 [Standard_A2_v2](../virtual-machines/av2-series.md) 또는 [Standard_A2](../virtual-machines/sizes-previous-gen.md#a-series) VM 시리즈를 사용하여 매핑합니다.  랩용으로 ‘작은’ VM 크기를 선택하면 Lab Services는 먼저 *Standard_A2_v2* 시리즈를 사용하려고 시도합니다.  그러나 사용 가능한 용량이 충분하지 않은 경우 Lab Services는 *Standard_A2* 시리즈를 대신 사용합니다.  가격은 VM 크기에 따라 결정되므로 Lab Services가 해당 크기에 사용하는 VM 시리즈와 관계없이 동일합니다. VM 크기별 가격 책정에 대한 자세한 내용은 [Lab Services 가격 책정 가이드](https://azure.microsoft.com/pricing/details/lab-services/)를 참조하세요.


| 크기 | 최소 사양 | 계열 | 권장 사용 |
| ---- | ----- | ------ | ------------- |
| 작음| <ul><li>2&nbsp;코어</li><li>3.5GB(기가바이트) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md), [Standard_A2](../virtual-machines/sizes-previous-gen.md#a-series) | 명령줄, 웹 브라우저 열기, 낮은 트래픽 웹 서버, 중소규모 데이터베이스에 가장 적합합니다. |
| 중간 | <ul><li>4&nbsp;코어</li><li>7GB&nbsp;&nbsp;RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md), [Standard_A3](../virtual-machines/sizes-previous-gen.md#a-series) | 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다. |
| 중간(중첩된 가상화) | <ul><li>4&nbsp;코어</li><li>16GB&nbsp;&nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md#dsv3-series) | 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다.  이 크기는 중첩된 가상화도 지원합니다.
| 대 | <ul><li>8&nbsp;코어</li><li>16GB&nbsp;&nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md), [Standard_A7](../virtual-machines/sizes-previous-gen.md#a-series) | 더 빠른 CPU, 향상된 로컬 디스크 성능, 대형 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다. |
| 대형(중첩된 가상화) | <ul><li>8&nbsp;코어</li><li>32GB&nbsp;&nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md#dsv3-series) | 더 빠른 CPU, 향상된 로컬 디스크 성능, 대형 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다.  이 크기는 중첩된 가상화도 지원합니다. |
| 소형 GPU(시각화) | <ul><li>6&nbsp;코어</li><li>56GB&nbsp;&nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. |
| 소형 GPU(컴퓨팅) | <ul><li>6&nbsp;코어</li><li>56GB&nbsp;&nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md), [Standard_NC6s_v3](../virtual-machines/ncv3-series.md) |AI 및 심층 학습 등 컴퓨터를 많이 사용하는 애플리케이션에 가장 적합합니다. |
| 중간 GPU(시각화) | <ul><li>12&nbsp;코어</li><li>112GB&nbsp;&nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md), [Standard_NV12s_v3](../virtual-machines/nvv3-series.md), [Standard_NV12s_v2](../virtual-machines/sizes-previous-gen.md#nvv2-series)  | OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임 및 인코딩에 가장 적합합니다. |

## <a name="manage-identity"></a>ID 관리

[Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하여 랩 계정 및 랩에 액세스하는 경우 다음 역할을 할당할 수 있습니다.

- 랩 계정 **소유자**

    랩 계정을 만드는 관리자에게는 랩 계정 소유자 역할이 자동으로 할당됩니다. 소유자 역할은 다음을 수행할 수 있습니다.
     - 랩 계정의 설정을 변경합니다.
     - 다른 관리자에게 랩 계정에 대한 액세스 권한을 소유자 또는 참가자로 부여합니다.
     - 교육자에게 랩에 대한 액세스 권한을 작성자, 소유자 또는 참가자로 부여합니다.
     - 랩 계정의 모든 랩을 만들고 관리합니다.

- 랩 계정 **참가자**

    참가자 역할이 할당된 관리자는 다음을 수행할 수 있습니다.
    - 랩 계정의 설정을 변경합니다.
    - 랩 계정의 모든 랩을 만들고 관리합니다.

    그러나 참가자는 다른 사용자에게 랩 계정 또는 랩에 대한 액세스 권한을 부여할 수 *없습니다*.

- **랩 작성자**

    랩 계정 내에서 랩을 만들려면 교육자는 랩 작성자 역할의 멤버여야 합니다.  랩을 만드는 교육자는 자동으로 랩 소유자로 추가됩니다. 자세한 내용은 [랩 작성자 역할에 사용자 추가](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)를 참조하세요. 

- 랩 **소유자** 또는 **참가자**
  
    랩 소유자 또는 참가자 역할의 교육자는 랩의 설정을 보고 변경할 수 있습니다. 이 사용자는 랩 계정 읽기 권한자 역할의 멤버여야 합니다.

    랩 소유자와 참가자 역할의 주요 차이점은 소유자만 다른 사용자에게 랩을 관리하도록 액세스 권한을 부여할 수 있다는 것입니다. 참가자는 다른 사용자에게 랩을 관리하도록 액세스 권한을 부여할 수 *없습니다*.

- **공유 이미지 갤러리**

    Shared Image Gallery를 랩 계정에 연결하는 경우, 랩 계정 소유자 및 참가자, 랩 작성자, 랩 소유자, 랩 참가자에게 갤러리에서 이미지를 보고 저장할 수 있는 액세스 권한이 자동으로 부여됩니다.

역할을 할당할 때 다음 팁을 따르면 유용합니다.

   - 일반적으로 관리자만 랩 계정 소유자 또는 참가자 역할의 멤버여야 합니다. 랩 계정에 소유자 또는 참가자가 둘 이상 있을 수 있습니다.
   - 교육자에게 새로운 랩을 만들고 해당 랩을 관리할 수 있는 권한을 주려면 랩 작성자 역할만 할당하면 됩니다.
   - 교육자에게 특정 랩을 관리할 수 있는 권한만 주고 새 랩을 만들 수 있는 권한은 주지 *않으려면*, 해당 교육자가 관리할 각 랩에 대해 소유자 또는 참가자 역할 중 하나를 할당합니다. 예를 들어 교수 및 보조 교사가 랩을 공동 소유하도록 허용할 수 있습니다. 자세한 내용은 [랩에 소유자 추가](./how-to-add-user-lab-owner.md)를 참조하세요.

## <a name="content-filtering"></a>콘텐츠 필터링

학교에서는 학생들이 부적절한 웹 사이트에 액세스하지 못하도록 콘텐츠 필터링을 수행해야 할 수 있습니다.  예를 들어 [CIPA(Children’s Internet Protection Act)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act)를 준수하는 것입니다.  Lab Services는 콘텐츠 필터링을 기본 제공으로 지원하지 않습니다.

학교에서 일반적으로 콘텐츠 필터링에 대해 고려하는 두 가지 방법이 있습니다.
- 네트워크 수준에서 콘텐츠를 필터링하도록 방화벽을 구성합니다.
- 콘텐츠 필터링을 수행하는 컴퓨터마다 직접 타사 소프트웨어를 설치합니다.

첫 번째 방법은 현재 Lab Services에서 지원되지 않습니다.  Lab Services는 Microsoft 관리형 Azure 구독 내에서 각 랩의 가상 네트워크를 호스트합니다.  따라서 네트워크 수준에서 콘텐츠 필터링을 수행하는 데 필요한 기본 가상 네트워크에 액세스할 수 없습니다.  Lab Services의 아키텍처에 대한 자세한 내용은 [아키텍처 기본 사항](./classroom-labs-fundamentals.md)문서를 읽어보세요.

그 대신 각 랩의 템플릿 VM에 타사 소프트웨어를 설치하는 두 번째 방법을 사용하는 것이 좋습니다.  이 솔루션의 일환으로 중점적으로 살펴볼 몇 가지 요점은 다음과 같습니다.
- [자동 종료 설정](./cost-management-guide.md#automatic-shutdown-settings-for-cost-control)을 사용하려는 경우 타사 소프트웨어에서 사용하는 여러 Azure 호스트 이름을 차단 해제해야 합니다.  자동 종료 설정은 진단 확장을 사용하여 Lab Services와 다시 통신할 수 있어야 합니다.  그러지 않으면 랩에서 자동 종료 설정을 사용할 수 없습니다.
- 또한 각 학생이 콘텐츠 필터링 소프트웨어를 제거할 수 없도록 VM에서 관리자가 아닌 계정을 사용하도록 할 수도 있습니다.  기본적으로 Lab Services는 각 학생이 VM에 로그인하는 데 사용하는 관리자 계정을 만듭니다.  특수 이미지를 사용하여 관리자가 아닌 계정을 추가할 수 있지만 몇 가지 알려진 제한 사항이 있습니다.

학교에서 콘텐츠 필터링을 수행해야 하는 경우 자세한 내용은 [Azure Lab Services 포럼](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices)을 통해 문의하세요.

## <a name="endpoint-management"></a>엔드포인트 관리

[Microsoft Endpoint Manager](https://techcommunity.microsoft.com/t5/azure-lab-services/configuration-manager-azure-lab-services/ba-p/1754407)와 같은 많은 엔드포인트 관리 도구의 경우 Windows VM에 고유한 머신 SID(보안 식별자)가 있어야 합니다.  일반적으로 SysPrep을 사용하여 ‘일반화된’ 이미지를 만들면 VM이 이미지에서 부팅될 때 각 Windows 머신에 고유한 새 머신 SID가 생성됩니다.

Lab Services를 사용하면 ‘일반화된’ 이미지를 사용하여 랩을 만드는 경우에도 템플릿 VM과 학생 VM에서는 모두 동일한 머신 SID를 사용하게 됩니다.  학생 VM을 만들기 위해 템플릿 VM을 게시하는 경우 템플릿 VM의 이미지는 ‘특수한’ 상태이므로 VM에서는 동일한 SID를 사용하게 됩니다.

예를 들어 Azure Marketplace 이미지가 일반화됩니다.  Win 10 마켓플레이스 이미지로부터 랩을 만들고 템플릿 VM을 게시하는 경우 랩 내의 모든 학생 VM에는 템플릿 VM과 동일한 머신 SID가 있습니다.  머신 SID는 [PsGetSid](/sysinternals/downloads/psgetsid)와 같은 도구를 사용하여 확인할 수 있습니다.

엔드포인트 관리 도구 또는 유사한 소프트웨어를 사용하려면 랩 VM에서 테스트하여 머신 SID가 동일한 경우에도 제대로 작동하는지 확인하는 것이 좋습니다.  

## <a name="pricing"></a>가격 책정

### <a name="azure-lab-services"></a>Azure Lab Services

가격 책정에 대해 알아보려면 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

### <a name="shared-image-gallery"></a>공유 이미지 갤러리

Shared Image Gallery를 사용하여 이미지 버전을 저장하고 관리하려면 Shared Image Gallery 서비스의 가격 또한 고려해야 합니다. 

공유 이미지 갤러리를 만들고 랩 계정에 연결하는 것은 무료입니다. 갤러리에 이미지 버전을 저장하지 않으면 비용이 발생하지 않습니다. 일반적으로 Shared Image Gallery를 사용하는 가격은 매우 저렴하지만 Azure Lab Services 가격에 포함되지 않기 때문에 계산되는 방식을 잘 이해해야 합니다.  

#### <a name="storage-charges"></a>스토리지 요금

공유 이미지 갤러리는 기본적으로 표준 HDD(하드 디스크 드라이브) 관리 디스크를 사용하여 이미지 버전을 저장합니다.  Lab Services에서 공유 이미지 갤러리를 사용하는 경우 HDD 관리 디스크를 사용하는 것이 좋습니다.  사용되는 HDD 관리 디스크의 크기는 저장되는 이미지 버전의 크기에 따라 달라집니다.  Lab Services는 최대 128GB의 이미지 및 디스크 크기를 지원합니다.  가격 책정에 대해 알아보려면 [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

#### <a name="replication-and-network-egress-charges"></a>복제 및 네트워크 송신 요금

랩의 템플릿 VM을 사용하여 이미지 버전을 저장하는 경우 Azure Lab Services는 먼저 이미지 버전을 원본 지역에 저장한 다음 원본 이미지 버전을 하나 이상의 대상 지역에 자동으로 복제합니다. 

Azure Lab Services는 원본 이미지 버전을 랩이 위치한 [지리 내의 모든 대상 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 자동으로 복제한다는 점을 알아 두어야 합니다. 예를 들어 랩이 미국 지리에 있는 경우 미국 내에 있는 각 8개 지역에 모두 이미지 버전이 복제됩니다.

네트워크 송신 요금은 원본 지역에서 추가 대상 지역으로 이미지 버전이 복제될 때 발생합니다. 청구되는 금액은 처음에 이미지의 데이터가 원본 지역에서 아웃바운드로 전송될 때의 이미지 버전 크기를 기준으로 합니다.  가격 책정에 대한 자세한 내용은 [대역폭 가격 세부 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

[Education 솔루션](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) 고객은 송신 요금이 면제될 수 있습니다. 자세히 알아보려면 계정 관리자에게 문의하세요. 

자세한 내용은 [교육 기관 프로그램](https://azure.microsoft.com/pricing/details/bandwidth/) 페이지의 FAQ 섹션에서 “교육 기관 고객을 위한 데이터 전송 프로그램은 무엇이며 자격 요건은 무엇입니까?”를 참조하세요.

#### <a name="pricing-example"></a>가격 책정 예

Shared Image Gallery에 템플릿 VM 이미지를 저장하는 비용의 예를 살펴보겠습니다. 다음 시나리오를 가정합니다.

- 하나의 사용자 지정 VM 이미지가 있습니다.
- 두 버전의 이미지를 저장하고 있습니다.
- 랩은 미국 내 총 8개 지역에 있습니다.
- 각 이미지 버전의 크기는 32GB입니다. 따라서 HDD 관리 디스크의 가격은 월 $1.54입니다.

월별 총비용은 다음과 같이 추정됩니다.

* *이미지 수 &times; 버전 수 &times; 복제본 수 &times; 관리 디스크 가격 = 월별 총비용*

이 예에서 비용은 다음과 같습니다.

* 1개의 사용자 지정 이미지(32GB) &times; 2개의 버전 &times; 8개의 미국 지역 &times; $1.54 = $24.64/월

> [!NOTE]
> 위의 계산은 예시용입니다. Shared Image Gallery를 사용하는 경우와 관련된 스토리지 비용이 포함되며 송신 비용은 포함되지 *않습니다*. 스토리지의 실제 가격 책정은 [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

#### <a name="cost-management"></a>비용 관리

랩 계정 관리자는 갤러리에서 불필요한 이미지 버전을 정기적으로 삭제하여 비용을 관리해야 합니다. 

이 옵션은 Shared Image Gallery에 있지만 비용을 줄이기 위해 특정 지역에 대한 복제를 삭제해서는 안 됩니다. 복제본을 변경하면 Azure Lab Services가 Shared Image Gallery 내에 저장된 이미지에서 VM을 게시하는 기능에 부정적인 영향을 미칠 수 있습니다.

## <a name="next-steps"></a>다음 단계

랩 설정 및 관리에 대한 자세한 내용은 다음을 참조하세요.

- [랩 계정 설정 가이드](account-setup-guide.md)  
- [랩 설정 가이드](setup-guide.md)  
- [랩용 Cost Management](cost-management-guide.md)  
- [팀에서 Azure Lab Services 사용](lab-services-within-teams-overview.md)