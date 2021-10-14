---
title: Azure Migrate를 사용하여 AVS 평가 만들기 | Microsoft Docs
description: Azure Migrate에서 AVS 평가를 만드는 방법을 설명합니다.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 89c7c903d382d67d9be64829118bdddf8ee4bd31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005998"
---
# <a name="create-an-azure-vmware-solution-assessment"></a>Azure VMware Solution 평가 만들기

이 문서에서는 Azure Migrate: 검색 및 평가를 사용하여 VMware 환경에서 온-프레미스 서버에 대한 Azure VMware Solution 평가를 만드는 방법을 설명합니다.

[Azure Migrate](migrate-services-overview.md)는 Azure로 마이그레이션하는 데 도움이 됩니다. Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터의 검색, 평가 및 Azure로의 마이그레이션을 추적할 수 있는 중앙 허브를 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate 프로젝트를 [만들었는지](./create-manage-projects.md) 확인합니다.
- 프로젝트를 이미 만든 경우 Azure Migrate: 검색 및 평가 도구를 [추가](how-to-assess.md)했는지 확인합니다.
- 평가를 만들려면 온-프레미스 서버를 검색하고 메타데이터 및 성능 데이터를 Azure Migrate: 검색 및 평가로 전송하는 [VMware](how-to-set-up-appliance-vmware.md)용 Azure Migrate 어플라이언스를 설정해야 합니다. [자세히 알아보기](migrate-appliance.md).
- CSV(쉼표로 구분된 값) 형식으로 [서버 메타데이터를 가져올 수도 있습니다](./tutorial-discover-import.md).


## <a name="azure-vmware-solution-avs-assessment-overview"></a>AVS(Azure VMware Solution) 평가 개요

Azure Migrate: 검색 및 평가를 사용하여 만들 수 있는 평가에는 세 가지 유형이 있습니다.

***평가 형식** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. 이 평가 형식을 사용하여 Azure VM으로 마이그레이션하기 위한 [VMware](how-to-set-up-appliance-vmware.md) 및 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 환경과 [물리적 서버](how-to-set-up-appliance-physical.md)에서 의 온-프레미스 서버를 평가할 수 있습니다.
**Azure SQL** | 온-프레미스 SQL Server를 VMware 환경에서 Azure SQL Database 또는 Azure SQL Managed Instance로 마이그레이션하기 위한 평가.
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. 이 평가 유형을 사용하여 AVS(Azure VMware Solution)로 마이그레이션하기 위한 [VMware 환경](how-to-set-up-appliance-vmware.md)의 온-프레미스 서버를 평가할 수 있습니다. [자세한 정보](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> AVS(Azure VMware Solution) 평가는 VMware 환경의 서버에 대해서만 만들 수 있습니다.


AVS(Azure VMware Solution) 평가를 만드는 데 사용할 수 있는 두 가지 유형의 크기 조정 기준이 있습니다.

**평가** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 온-프레미스 서버의 수집된 성능 데이터를 기준으로 하는 평가 | **권장 노드 크기**: 평가를 위해 선택하는 노드 유형, 스토리지 유형 및 FFT 설정과 함께 CPU 및 메모리 사용률 데이터를 기준으로 합니다.
**온-프레미스로** | 온-프레미스 크기 조정을 기반으로 하는 평가 | **권장 노드 크기**: 평가를 위해 선택하는 노드 유형, 스토리지 유형 및 FTT 설정과 함께 온-프레미스 서버 크기를 기준으로 합니다.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>AVS(Azure VMware Solution) 평가 실행

1.  **개요** 페이지 > **Windows, Linux 및 SQL Server** 에서 **서버 평가 및 마이그레이션** 을 클릭합니다.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure Migrate 개요 페이지":::

1. **Azure Migrate: 검색 및 평가** 에서 **평가** 를 클릭합니다.

   ![평가 단추의 위치](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. **서버 평가** > **평가 유형** 에서 **AVS(Azure VMware Solution)** 를 선택합니다.

1. **검색 소스** 에서 다음을 수행합니다.

    - 어플라이언스를 사용하여 서버를 검색한 경우 **Azure Migrate 어플라이언스에서 검색된 서버** 를 선택합니다.
    - 가져온 CSV 파일을 사용하여 서버를 검색한 경우 **가져온 서버** 를 선택합니다. 
    
1. **편집** 을 클릭하여 평가 속성을 검토합니다.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="평가 설정 선택 페이지":::
 

1. **평가 속성** > **대상 속성** 에서 다음을 수행합니다.

    - **대상 지역** 에서 마이그레이션할 Azure 지역을 지정합니다.
       - 크기 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다.
   - **스토리지 유형** 은 **vSAN** 으로 기본 설정됩니다. 이는 AVS 프라이빗 클라우드의 기본 스토리지 유형입니다.
   - **예약된 인스턴스** 는 현재 AVS 노드에서 지원되지 않습니다.
1. **VM 크기** 에서 다음을 수행합니다.
    - **노드 유형** 은 **AV36** 으로 기본 설정됩니다. Azure Migrate는 서버를 AVS로 마이그레이션하는 데 필요한 노드 유형을 추천합니다.
    - **FTT 설정, RAID 수준** 에서 FTT(Failure to Tolerate) 및 RAID의 조합을 선택합니다.  선택한 FTT 옵션은 온-프레미스 서버 디스크 요구 사항과 결합되어 AVS에 필요한 총 vSAN 스토리지 크기를 결정합니다.
    - **CPU 초과 구독** 에서 AVS 노드에 있는 하나의 물리적 코어 하나와 연결되는 가상 코어의 비율을 지정합니다. 4:1보다 큰 초과 구독인 경우 성능이 저하될 수 있지만 웹 서버 유형 워크로드에 사용할 수 있습니다.
    - **메모리 오버 커밋 비율** 에서 클러스터의 메모리 오버 커밋 비율을 지정합니다. 값 1은 100% 메모리 사용을 나타내고(예를 들어 0.5은 50%), 2는 사용 가능한 메모리의 200%를 사용합니다. 0\.5부터 10까지의 값만 최대 소수점 이하 한 자리까지 추가할 수 있습니다.
    - **중복 제거 및 압축 비율** 에서 워크로드에 대해 예상되는 중복 제거와 압축 비율을 지정합니다. 실제 값은 온-프레미스 vSAN 또는 스토리지 구성에서 가져올 수 있으며 이는 워크로드에 따라 다를 수 있습니다. 값 3은 3배를 의미하므로 300GB 디스크의 경우 100GB 스토리지만 사용됩니다. 값 1은 중복 제거 또는 압축이 없음을 의미합니다. 1부터 10까지의 값만 최대 소수점 이하 한 자리까지 추가할 수 있습니다.
1. **노드 크기** 에서 다음을 수행합니다. 
    - **크기 조정 기준** 에서 정적 메타데이터 또는 성능 기반 데이터를 기반으로 하여 평가할지 여부를 선택합니다. 성능 데이터를 사용하는 경우 다음을 수행합니다.
        - **성능 기록** 에서 평가의 기준이 될 데이터 기간을 표시합니다.
        - **백분위수 사용률** 에서 성능 샘플에 사용하려는 백분위수 값을 지정합니다. 
    - **쾌적 인자** 에서 평가 중에 사용하려는 버퍼를 지정합니다. 이는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가와 같은 문제를 고려합니다. 예를 들어 2의 쾌적 인자를 사용하는 경우 다음과 같습니다.
    
        **구성 요소** | **유효 사용률** | **쾌적 인자(2.0) 추가**
        --- | --- | ---
        코어 | 2  | 4
        메모리 | 8GB | 16GB  

1. **가격 책정** 에서 다음을 수행합니다.
    - **제품** 에는 사용자가 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)이 표시됩니다. 평가는 해당 제품에 대한 비용을 추정합니다.
    - **통화** 에서 계정의 청구 통화를 선택합니다.
    - **할인(%)** 에서 Azure 제품에 적용되는 구독별 할인을 추가합니다. 기본 설정은 0%입니다.

1. 변경한 경우 **저장** 을 클릭합니다.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="평가 속성":::

1. **서버 평가** 에서 **다음** 을 클릭합니다.

1. **평가할 서버 선택** > **평가 이름** 에서 평가 이름을 지정합니다. 
 
1. **그룹 선택 또는 만들기** 에서 **새로 만들기** 를 선택하고 그룹 이름을 지정합니다. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="그룹에 서버 추가":::
 
1. 어플라이언스를 선택하고 그룹에 추가할 서버를 선택합니다. 그런 다음 **다음** 을 클릭합니다.

1. **검토 + 평가 만들기** 에서 평가 세부 정보를 검토하고, **평가 만들기** 를 클릭하여 그룹을 만들고 평가를 실행합니다.

    > [!NOTE]
    > 성능 기반 평가의 경우 평가를 만들기 전에 검색을 시작한 후 하루 이상 기다리는 것이 좋습니다. 이는 더 높은 신뢰도로 성능 데이터를 수집할 시간을 제공합니다. 검색을 시작한 후에는 높은 신뢰도 등급을 위해 지정한 성능 기간(일/주/월) 동안 기다리는 것이 좋습니다.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>AVS(Azure VMware Solution) 평가 검토

AVS(Azure VMware Solution) 평가는 다음을 설명합니다.

- **AVS(Azure VMware Solution) 준비**: 온-프레미스 서버가 AVS(Azure VMware Solution)로 마이그레이션하는 데 적합한지 여부입니다.
- **Azure Vmware 솔루션 노드 수**: 서버를 실행 하는 데 필요한 Azure vmware 솔루션 노드의 예상 개수입니다.
- **AVS 노드에서의 사용률**: 모든 노드에서 예상되는 CPU, 메모리 및 스토리지 사용률입니다.
    - 사용률에는 vCenter Server, NSX Manager(대규모), NSX Edge와 같은 다음 클러스터 관리 오버헤드의 초기 팩터링이 포함됩니다. HCX가 배포되는 경우 HCX Manager와 IX 어플라이언스도 압축 및 중복 제거 전에 ~ 44vCPU(11개 CPU), 75GB RAM 및 722GB의 스토리지를 사용합니다.
    - 제한 요소는 리소스를 수용 하는 데 필요한 호스트/노드 수를 결정 합니다.
- **월별 예상 비용**: 온-프레미스 VM을 실행하는 모든 AVS(Azure VMware Solution) 노드에 대한 월별 예상 비용입니다.


### <a name="view-an-assessment"></a>평가 보기

1. **Windows, Linux 및 SQL Server** > **Azure Migrate: 검색 및 평가** 에서 **Azure VMware Solution** 옆에 있는 숫자를 클릭합니다.

1. **평가** 에서 평가를 선택하여 엽니다. 예를 들면(예제에만 해당되는 추정 및 비용) 다음과 같습니다. 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="AVS 평가 요약":::

1. 평가 요약을 검토합니다. **크기 조정 가정을** 클릭 하 여 노드 크기 조정 및 리소스 사용률 계산에서 발생 한 가정을 이해할 수 있습니다. 평가 속성을 편집하거나 평가를 다시 계산할 수도 있습니다.

### <a name="review-azure-vmware-solution-avs-readiness"></a>AVS(Azure VMware Solution) 준비 상태 검토

1. **Azure 준비 상태** 에서 서버가 AVS로 마이그레이션할 준비가 되었는지 확인합니다.

2. 다음과 같이 서버 상태를 검토합니다.
    - **AVS 준비 완료**: 서버를 변경하지 않고 있는 그대로 Azure(AVS)로 마이그레이션할 수 있습니다. 서버는 AVS에서 시작되며, AVS가 완전히 지원됩니다.
    - **조건과 함께 준비 완료**: AVS에서 서버에 대한 모든 기능을 사용할 수 있으려면 서버에서 먼저 VMware 도구 및 또는 기타 설정이 필요할 뿐만 아니라 현재 vSphere 버전과의 호환성 문제가 발생할 수 있습니다.
    - **AVS를 사용할 준비 안 됨**: AVS에서 서버가 시작되지 않습니다. 예를 들어, 온-프레미스 서버에 cd-rom과 같은 연결된 외부 디바이스가 있는 경우 VMotion 작업은 실패합니다(VMware VMotion을 사용하는 경우).
    - **준비 상태 알 수 없음**: 온-프레미스 환경에서 수집된 메타데이터가 부족하여 Azure Migrate에서 서버 준비 상태를 확인할 수 없습니다.

3. 다음과 같이 제안된 도구를 검토합니다.
    - **VMware HCX 또는 Enterprise**: VMware 서버의 경우 VMware HCX(하이브리드 클라우드 확장) 솔루션은 온-프레미스 워크로드를 AVS(Azure VMware Solution) 프라이빗 클라우드로 마이그레이션하는 데 제안되는 마이그레이션 도구입니다. [자세한 정보](../azure-vmware/configure-vmware-hcx.md).
    - **알 수 없음**: CSV 파일을 통해 가져온 서버의 경우 기본 마이그레이션 도구를 알 수 없습니다. VMware 서버의 경우 VMware HCX(하이브리드 클라우드 확장) 솔루션을 사용하는 것이 좋습니다. 

4. **VS 준비** 상태를 클릭합니다. VM 준비 상태 세부 정보를 살펴보고, 드릴다운하여 컴퓨팅, 스토리지 및 네트워크 설정을 포함한 VM 세부 정보를 확인할 수 있습니다.

### <a name="review-cost-details"></a>비용 세부 정보 검토

이 보기는 AVS(Azure VMware Solution)에서 실행 중인 서버에 대한 예상 비용을 보여 줍니다.

1. 월별 총 비용을 검토합니다. 비용은 평가된 그룹의 모든 서버에 대해 집계됩니다. 

    - 예상 비용은 모든 서버의 리소스 요구 사항을 고려하여 필요한 AVS 노드 수를 기반으로 합니다.
    - AVS(Azure VMware Solution) 가격 책정은 노드당 기준이므로 총 비용에는 컴퓨팅 비용과 스토리지 비용이 할당되지 않습니다.
    - 예상 비용은 AVS에서 온-프레미스 서버를 실행하는 데 들어가는 비용입니다. AVS 평가는 PaaS 또는 SaaS 비용을 고려하지 않습니다.
    
2. 월간 예상 스토리지 비용을 검토할 수 있습니다. 이 보기에는 평가된 그룹에 대해 집계된 스토리지 비용이 표시되며, 해당 그룹은 여러 유형의 스토리지 디스크로 분할되어 있습니다.

3. 드릴다운하여 특정 서버에 대한 세부 정보를 확인할 수 있습니다.


### <a name="review-confidence-rating"></a>신뢰 등급 검토

성능 기반 평가를 실행하면 신뢰 등급이 평가에 할당됩니다.

- 별 1개(최저)에서 별 5개(최고)까지의 등급입니다.
- 신뢰 등급을 통해 평가에서 제공하는 크기 추천 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급은 평가를 계산하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.
- 성능 기반 크기 조정의 경우 AVS 평가에는 CPU 및 서버 메모리에 대한 사용률 데이터가 필요합니다. 다음 성능 데이터는 수집되지만, AVS 평가를 위한 크기 조정 권장 사항에는 사용되지 않습니다.
  - 서버에 연결된 모든 디스크에 대한 디스크 IOPS 및 처리량 데이터입니다.
  - 서버에 연결된 각 네트워크 어댑터에 대한 성능 기반 크기 조정을 처리하는 네트워크 I/O입니다.

평가에 대한 신뢰 등급은 다음과 같습니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

성능에 대한 [자세한 정보](concepts-azure-vmware-solution-assessment-calculation.md) 


## <a name="next-steps"></a>다음 단계

- [종속성 매핑](how-to-create-group-machine-dependencies.md)을 사용하여 높은 신뢰도 그룹을 만드는 방법을 알아봅니다.
- AVS 평가를 계산하는 방법에 대해 [자세히 알아보세요](concepts-azure-vmware-solution-assessment-calculation.md).
