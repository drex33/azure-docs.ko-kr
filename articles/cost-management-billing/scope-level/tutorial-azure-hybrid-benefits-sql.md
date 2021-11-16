---
title: 자습서 - SQL Server에 대해 중앙에서 관리되는 Azure 하이브리드 혜택 최적화
description: 이 자습서에서는 Azure 하이브리드 혜택을 관리하고 최적화하기 위해 Azure에서 SQL Server 라이선스를 사전에 할당하는 과정을 안내합니다.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 6031bd83a5a32ffc5e76a4a967e305324f8d7ee8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218866"
---
# <a name="tutorial-optimize-centrally-managed-azure-hybrid-benefit-for-sql-server"></a>자습서: SQL Server에 대해 중앙에서 관리되는 Azure 하이브리드 혜택 최적화

이 자습서에서는 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 중앙에서 관리하고 최적화하기 위해 Azure에서 SQL Server 라이선스를 사전에 할당하는 과정을 안내합니다. 혜택을 최적화하면 Azure SQL을 실행하는 비용이 절감됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 라이선스 사용 현황 및 가용성 정보 수집
> * 필요한 경우 더 많은 라이선스 구입
> * Azure에 라이선스 할당
> * 사용량 모니터링 및 조정
> * 관리 일정 설정

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 사항을 확인합니다.

[중앙에서 관리되는 Azure 하이브리드 혜택이란?](overview-azure-hybrid-benefit-scope.md) 문서를 읽고 이해합니다. 이 문서에서는 Azure 하이브리드 혜택에 해당하는 SQL Server 라이선스 유형을 설명하고, 구독 또는 전체 청구 계정 수준에서 선택한 범위에 Azure 하이브리드 혜택을 설정하고 사용하는 방법을 알려줍니다.

> [!NOTE]
> 중앙에서 범위 수준으로 Azure 하이브리드 혜택을 관리하는 것은 현재 퍼블릭 미리 보기로 제공되며 기업 고객으로 제한됩니다.

새 환경을 사용하기 전에, Azure에서 SQL Server를 실행하는 자체 설치된 가상 머신이 등록 되어 있는지 확인합니다. 이렇게 하면 SQL Server를 실행하는 Azure 리소스가 사용자와 Azure에 표시됩니다. Azure에서 SQL VM을 등록하는 방법을 더 알아보려면 [SQL IaaS 에이전트 확장을 사용하여 SQL Server VM 등록](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 및 [Azure에서 SQL IaaS 에이전트 확장을 사용하여 여러 SQL VM 등록](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)을 참조하세요.

## <a name="gather-license-usage-and-availability-details"></a>라이선스 사용 현황 및 가용성 정보 수집

_첫 번째 단계는 준비입니다._ 조직의 다른 부서와 협력하여 다음 두 가지를 이해합니다.

- 현재 및 계획된 Azure SQL은 무엇이고 돌아오는 연도의 SQL Server 사용량은 얼마나 되나요?
- Azure에 할당할 수 있는 SQL Server 코어 라이선스 수는 몇 개인가요?

그리고, 돌아오는 연도 또는 다른 연장된 기간(적어도 한 달) 동안 현재 _및 예정된_ Azure SQL 사용량을 확인합니다.

[Azure 하이브리드 혜택에 대한 SQL Server 라이선스 할당을 만들 때](create-sql-license-assignments.md) Azure SQL 사용량 세부 정보가 표시됩니다.

위의 정보에 대한 유효성을 검사하려면 조직의 적절한 사람에게 문의하는 것이 좋습니다. 여기에는 _계획된_ 사용량과 예상되는 SQL Server 사용량 증가가 포함됩니다. 이러한 계획을 소유하는 중앙 집중식 개인 또는 그룹이 있거나 여러 팀에 배포될 수 있습니다.

선택적이지만 Azure SQL 사용(리소스 수준에서 Azure 하이브리드 혜택 사용 포함)을 조사하는 데 유용한 한 가지 방법은 Azure 하이브리드 혜택 [sql-license-usage PowerShell 스크립트](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit)를 사용하는 것입니다. 특정 구독 또는 전체 계정에 있는 모든 SQL 리소스의 결합된 SQL Server 라이선스 사용을 분석하고 추적합니다.

### <a name="determine-the-number-of-sql-server-core-licenses-available-to-assign-to-azure"></a>Azure에 할당할 수 있는 SQL Server 코어 라이선스 수 결정

수량은 구매한 라이선스 수 및 온-프레미스 서버와 Azure VM 간에 이미 사용 중인 라이선스 수에 따라 달라집니다.

마이그레이션이 원활하게 실행되도록 하려면 180일 동안 SQL Server 라이선스에 대한 이중 사용 권한이 있어야 합니다. Azure의 SQL Server 라이선스는 180일의 기간이 지난 후에만 사용할 수 있습니다. 라이선스 가용성을 계획할 때 이 시점을 고려해야 합니다. 예를 들어 라이선스 마이그레이션은 할당할 수 있는 라이선스인 것으로 간주할 수 있습니다.

소프트웨어 조달 또는 소프트웨어 자산 관리 부서에서 이 정보를 사용할 수 있습니다.

## <a name="buy-more-licenses-if-needed"></a>필요한 경우 더 많은 라이선스 구입

수집된 정보를 검토한 후에 사용 가능한 SQL Server 라이선스 수가 계획된 Azure SQL 사용량을 포괄하기에 부족한 것으로 확인되면 조달 부서에 문의하여 소프트웨어 보증(또는 구독 라이선스)이 있는 SQL Server 코어 라이선스를 더 구입합니다.

SQL Server 라이선스를 구매하고 Azure 하이브리드 혜택을 적용하는 것이 Azure에서 시간당 SQL Server 비용을 지불하는 것보다 비용이 저렴합니다. 조직에서는 계획된 모든 Azure SQL 사용량을 포괄하는 데 충분한 라이선스를 구매하여 비용 절감 효과를 극대화합니다.

## <a name="assign-licenses-to-azure"></a>Azure에 라이선스 할당

1. Azure Portal 및 설명서의 지침에 따라 하나 이상의 범위를 선택하고 거기에 SQL Server 라이선스를 할당합니다. 자세한 내용은 [Azure 하이브리드 혜택에 대한 SQL Server 라이선스 할당 만들기](create-sql-license-assignments.md)를 참조하세요.
2. 라이선스를 할당할 때 검색된 Azure SQL 사용량을 다시 검토하여 수집된 다른 정보와 일치하는 정보가 있는지 확인합니다.

## <a name="monitor-usage-and-adjust"></a>사용량 모니터링 및 조정

1. **Cost Management + Billing** > **예약 + 하이브리드 혜택** 으로 이동합니다.
1. 여러분이 만든 Azure 하이브리드 혜택 라이선스 할당 및 각 항목의 사용률을 보여 주는 테이블이 표시됩니다.
1. 사용률이 100%면 조직에서 일부 SQL Server 리소스에 대해 시간당 요금을 지불하고 있는 것입니다. 조직에서 다른 그룹을 다시 사용하여 현재 사용 수준이 임시적인 것인지 계속되는 것인지 확인합니다. 후자의 경우, 비용을 줄이려면 조직에서 더 많은 라이선스를 구입하여 Azure에 할당하는 것을 고려해야 합니다.
1. 사용률이 100%에 가깝지만 이를 초과하지 않는 경우에는 가까운 미래에 사용량이 증가할 것으로 예상되는지 결정합니다. 그렇다면 더 많은 라이선스를 사전에 확보하고 할당할 수 있습니다.

## <a name="establish-a-management-schedule"></a>관리 일정 설정

이전 섹션에서는 지속적인 모니터링에 대해 설명합니다. 또한 반복적으로 수행하는 연간 또는 분기별 일정을 설정하는 것이 좋습니다. 이 일정은 문서에서 설명하는 주요 단계를 포함합니다.

- 라이선스 사용량 및 가용성 정보를 수집합니다.
- 필요한 경우 추가 라이선스를 구입하여 예정된 사용량 증가를 충족해야 합니다.
- Azure에 라이선스 할당
- 사용량을 모니터링하고 필요에 따라 즉시 조정합니다.
- 매년 또는 요구 사항에 가장 적합한 빈도로 프로세스를 반복합니다.

## <a name="example-walkthrough"></a>연습 예

다음 예제에서는 여러분이 Contoso 보험 회사의 대금 청구 관리자라고 가정해보겠습니다. Contoso의 SQL Server에 대한 Azure 하이브리드 혜택을 관리합니다.

조달 부서에서 여러분이 전체 계정 수준에서 SQL 서버에 대한 Azure 하이브리드 혜택을 중앙에서 관리할 수 있다는 것을 알려주었습니다. 조달 부서는 Microsoft 계정 팀으로부터 이 점을 들었습니다. 최근 Azure 하이브리드 혜택을 관리하기가 어려웠기 때문에 여기에 관심이 갑니다. 또한 개발자는 서로에게 스크립트를 공유할 때 리소스에 대한 혜택을 사용하도록(또는 사용하지 않도록) 임의로 설정하기 때문이기도 합니다.

Azure Portal의 Cost Management + Billing 영역에서 새로운 Azure 하이브리드 혜택 환경을 찾을 수 있습니다.

문서의 이전 지침을 읽은 후 다음을 이해합니다.

  - Contoso는 다른 작업을 수행하기 전에 SQL Server VM을 등록해야 합니다.
  - 새 기능을 사용하는 가장 좋은 방법은 필요한 사용량을 포괄하기 위해 사전에 라이선스를 할당하는 것입니다.

그런 후 다음 단계를 수행합니다.

1. 위의 지침을 사용하여 자체 설치된 SQL VM이 등록되었는지 확인합니다. 여기에는 충분한 사용 권한이 없는 구독에 대한 등록을 완료하기 위해 구독 소유자에게 알리는 것이 포함됩니다.
1. 최근 몇 달 동안의 Azure 자원 배정 현황 데이터를 검토하고 Contoso의 다른 사용자와 의견을 교환합니다. 다음 해에 예상되는 Azure SQL 사용량을 충족하려면 2000 SQL Server Enterprise Edition 및 750 SQL Server Standard Edition 코어 라이선스 또는 8750 정규화된 코어 라이선스가 필요하다고 판단합니다. 예상 사용량에는 워크로드(1500 SQL Server Enterprise Edition + 750 SQL Server Standard Edition = 6750 정규화) 및 새로운 Azure SQL 순 워크로드(다른 500 SQL Server Enterprise Edition 또는 2000 정규화된 코어 라이선스)를 마이그레이션하는 작업도 포함됩니다.
1. 다음으로, 필요한 라이선스를 이미 사용할 수 있거나 곧 구매할 수 있는지를 조달 팀과 함께 확인합니다. 확인을 통해 라이선스를 Azure에 할당할 수 있습니다.
   - 연결된 워크로드를 Azure로 마이그레이션하는 경우 온-프레미스에서 사용하는 라이선스를 Azure에 할당하는 데 사용할 수 있는 것으로 간주할 수 있습니다. 앞에서 설명한 것처럼 Azure 하이브리드 혜택은 최대 180일 동안 이중 사용을 허용합니다.
   - Azure에 할당할 수 있는 1800 SQL Server Enterprise Edition 라이선스 및 2000 SQL Server Standard Edition 라이선스가 있는지 확인합니다. 사용 가능한 라이선스는 9200 정규화된 코어 라이선스와 동일합니다. 필요한 8750보다 약간 더 높습니다(2000 x 4 + 750 = 8750).
1. 그런 다음 1800 SQL Server Enterprise Edition 및 2000 SQL Server Standard Edition을 Azure에 할당합니다. 이 작업을 수행하면, 각 시간마다 실행되는 Azure SQL 리소스에 적용할 수 있는 9200 정규화된 코어 라이선스가 생성됩니다. 필요한 것보다 더 많은 라이선스를 할당하면 사용량이 예상보다 더 빠르게 증가하는 경우에 버퍼를 제공합니다.

이후에는 할당된 라이선스 사용을 주기적으로 모니터링하는 것이 가장 좋습니다. 10개월 후에는 사용량이 거의 95%에 달합니다. Azure SQL 사용량이 예상보다 더 빠르게 증가한 것입니다. 조달 팀과 대화하면서 더 많은 라이선스를 얻고 할당할 수 있습니다.

마지막으로 연간 라이선스 검토 일정을 채택합니다. 검토 프로세스에서 다음을 수행합니다.

- 라이선스 사용량 데이터를 수집하고 분석합니다.
- 라이선스 가용성 확인
- 필요한 경우 조달 팀과 협력하여 더 많은 라이선스를 받으세요.
- 라이선스 할당 업데이트
- 시간별 상태

## <a name="next-steps"></a>다음 단계

- [중앙에서 관리되는 Azure 하이브리드 혜택으로 전환](transition-existing.md)하는 방법을 알아봅니다.
- [중앙에서 관리되는 Azure 하이브리드 혜택 FAQ](faq-azure-hybrid-benefit-scope.yml)를 검토하세요.