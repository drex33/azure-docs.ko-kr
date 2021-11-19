---
title: 중앙에서 관리되는 Azure 하이브리드 혜택이란 무엇인가요?
description: Azure 하이브리드 혜택은 활성 Software Assurance(또는 구독)가 있는 온-프레미스 코어 기반 Windows Server 및 SQL Server 라이선스를 Azure로 가져올 수 있는 라이선스 혜택입니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/11/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: fdf2bbae7ec9a7c20e79298561509d0d820103ab
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708352"
---
# <a name="what-is-centrally-managed-azure-hybrid-benefit"></a>중앙에서 관리되는 Azure 하이브리드 혜택이란 무엇인가요?

Azure 하이브리드 혜택은 클라우드에서 워크로드를 실행하는 비용을 대폭 줄일 수 있는 라이선스 혜택입니다. 해당 혜택은 Azure에서 온-프레미스 Software Assurance 또는 구독 사용 가능 Windows Server 및 SQL Server 라이선스를 사용하도록 하여 작동합니다. 자세한 내용은 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 참조하세요.

중앙에서 전체 Azure 구독 또는 전체 청구 계정의 범위에서 SQL Server의 Azure 하이브리드 혜택을 관리할 수 있습니다. 개략적으로 작동 방식은 다음과 같습니다.

1. 먼저 IaaS 확장을 사용하여 자동 설치된 SQL 서버 이미지의 자동 등록을 사용하도록 설정하여 모든 SQL Server VM이 사용자와 Azure에 표시되는지 확인합니다. 자세한 내용은 [SQL IaaS 에이전트 확장을 통해 Azure에 여러 SQL VM 등록](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)을 참조하세요.
1. Azure Portal의 **Cost Management + Billing** 에서 사용자(청구 관리자)는 범위 및 범위의 리소스를 처리하기 위해 할당하려는 적격 라이선스 수를 선택합니다.  
    :::image type="content" source="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" alt-text="범위 설정과 라이선스 할당을 보여 주는 스크린샷" lightbox="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" :::

이전 예제에서는 적격한 모든 Azure SQL 리소스를 포괄하려면 108개의 정규화된 코어 라이선스를 사용해야 한다고 감지되었습니다. 개별 리소스에 대해 감지된 사용량은 정규화된 코어 라이선스 56개입니다. 예제에서는 60개의 표준 코어 라이선스와 12개의 Enterprise 코어 라이선스(12 * 4 = 48)를 표시했습니다. 따라서 60 + 48 = 108개입니다. 정규화된 코어 라이선스 값은 다음 [Azure 리소스에 라이선스를 적용하는 방법](#how-licenses-apply-to-azure-resources) 섹션에서 자세히 설명합니다.

- 범위의 리소스가 실행될 때 1시간마다 Azure는 라이선스를 자동으로 할당하고 비용을 올바르게 할인합니다. 1시간마다 다른 리소스가 처리될 수 있습니다.
- 할당된 라이선스 수를 초과하는 사용량은 일반 종량제 가격으로 청구됩니다.
- 범위 수준에서 라이선스를 할당하여 혜택을 관리하도록 선택하면 해당 범위의 개별 리소스를 더 이상 관리할 수 없습니다.

원래 리소스 수준에서 Azure 하이브리드 혜택을 사용하는 방법은 SQL Server의 경우 계속 사용할 수 있으며 Windows Server의 경우 현재 유일한 옵션입니다. 이 방법에서는 DevOps 역할이 개별 리소스를 만들거나 관리할 때 각 리소스(예: SQL Database 또는 Windows Server VM)에 대한 혜택을 선택합니다. 이렇게 하면 해당 리소스의 시간당 비용이 할인됩니다. 자세한 내용은 Windows Server에 대한 [Azure 하이브리드 혜택](../../azure-sql/azure-hybrid-benefit.md)을 참조하세요.

구독 또는 계정 범위 수준에서 SQL Server의 Azure 하이브리드 혜택을 중앙에서 관리하는 기능은 현재 미리 보기 상태입니다. 엔터프라이즈 고객 및 Microsoft 고객 계약으로 Azure.com에서 직접 구매하는 고객에게 제공됩니다. 제공 대상을 Windows Server 및 더 많은 고객으로 확장할 수 있기를 바랍니다.

## <a name="qualifying-sql-server-licenses"></a>적격 SQL Server 라이선스

활성 Software Assurance가 포함된 SQL Server Enterprise 및 Standard 코어 라이선스는 이 혜택을 받을 수 있습니다. SQL Server 코어 라이선스 구독도 혜택을 받을 수 있습니다.

## <a name="qualifying-azure-resources"></a>적격 Azure 리소스

범위 수준에서 Azure 하이브리드 혜택을 중앙에서 관리할 때는 다음과 같은 일반적인 Azure SQL 리소스가 포함됩니다.

- SQL Database
- SQL Managed Instances
- SQL Elastic Pool
- Azure VM의 SQL Server

리소스 수준 Azure 하이브리드 혜택 관리에서는 위의 모든 항목도 다룹니다. 이는 현재 다음 리소스를 다루는 유일한 옵션입니다.

- Azure 전용 호스트
- Azure Data Factory SSIS(SQL Server Integration Services)

## <a name="centralized-scope-level-management-advantages"></a>범위 수준의 중앙 관리의 이점

다음과 같은 이점이 있습니다.

- **더 나은 제어를 통해 더 간단하고 확장 가능한 접근 방식** - 청구 관리자는 사용 가능한 라이선스를 하나 이상의 Azure 범위에 직접 할당합니다. 원래 접근 방식에서는 대규모로 많은 리소스와 DevOps 소유자 간에 Azure 하이브리드 혜택 사용을 조정합니다.
- **비용을 최적화하는 사용하기 쉬운 방법** - 관리자는 Azure 하이브리드 혜택 사용률을 모니터링하고 Azure에 할당된 라이선스를 직접 조정할 수 있습니다. 예를 들어, 관리자는 Azure에 더 많은 라이선스를 할당하여 더 많은 비용을 절감할 기회를 찾습니다. 그런 다음, 조달 부서와 논의하여 라이선스 가용성을 확인합니다. 마지막으로, 손쉽게 Azure에 라이선스를 할당하고 비용 절감을 시작할 수 있습니다.
- **사용 급증 시 비용을 관리하는 더 나은 방법** - 일시적인 급증 시 손쉽게 동일한 리소스를 스케일 업하거나 더 많은 리소스를 추가할 수 있습니다. 더 많은 SQL Server 라이선스를 할당할 필요가 없습니다(예: 마감 기간 또는 휴일 쇼핑 증가). 일시적인 워크로드 급증 시 추가 용량에 대한 종량제 요금은 용량에 Azure 하이브리드 혜택을 사용하기 위해 더 많은 라이선스를 구매하는 것보다 저렴할 수 있습니다. 리소스 수준이 아닌 범위에서 혜택을 관리하면 집계 사용량에 따라 결정하는 데 도움이 됩니다.
- **규정 준수를 유지하기 위한 명확한 업무 분담** - 리소스 수준 Azure 하이브리드 혜택 모델에서 리소스 소유자는 사용 가능한 라이선스가 없는 경우 Azure 하이브리드 혜택을 선택할 수 있습니다. 또는 사용 가능한 라이선스가 *있는* 경우 혜택을 선택하지 *않을* 수 있습니다. Azure 하이브리드 혜택의 범위 수준 관리를 통해 이러한 상황을 해결할 수 있습니다. 중앙에서 혜택을 관리하는 청구 관리자는 조달 및 소프트웨어 자산 관리 부서와 함께 Azure에 할당할 수 있는 라이선스 수를 확인하도록 배치됩니다. 이 내용은 다음 다이어그램에서 설명됩니다.

:::image type="content" source="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg" alt-text="의무 분리를 보여 주는 다이어그램" border="false" lightbox="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg":::

## <a name="how-licenses-apply-to-azure-resources"></a>Azure 리소스에 라이선스를 적용하는 방법

Software Assurance가 포함된 SQL Server Enterprise(코어) 및 SQL Server Standard(코어) 라이선스는 모두 자격이 있지만 [Microsoft 제품 약관](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)에 설명된 대로 Azure 하이브리드 혜택을 통해 Azure로 가져올 때 다양한 전환 비율이 적용됩니다.

이해해야 할 한 가지 규칙: 하나의 SQL Server Enterprise Edition 라이선스에는 모든 정규화된 Azure SQL 리소스 종류에서 ‘4개’의 SQL Server Standard Edition 라이선스와 동일한 적용 범위가 포함됩니다.

추가 작동 방식을 설명하기 위해 NCL(‘정규화된 코어 라이선스’)이라는 용어가 사용됩니다. 위 규칙에 따라 하나의 SQL Server Standard 코어 라이선스는 하나의 NCL을 생성합니다. 하나의 SQL Server Enterprise 코어 라이선스는 4개의 NCL을 생성합니다. 예를 들어, 4개의 SQL Server Enterprise 코어 라이선스와 7개의 SQL Server Standard 코어 라이선스를 할당하는 경우 총 적용 범위와 Azure 하이브리드 혜택 할인 규모는 23 NCL(4\*4+7\*1)과 같습니다.

다음 표에서는 다양한 리소스 종류에 대해 SQL Server 라이선스 비용을 완전히 할인하는 데 필요한 NCL 개수를 요약합니다. Azure 하이브리드 혜택의 범위 수준 관리는 아래에 요약된 제품 용어의 규칙을 엄격하게 적용합니다.

| **Azure Data Service** | **서비스 계층** | **필요한 NCL 수** |
| --- | --- | --- |
| SQL Managed Instance 또는 인스턴스 풀 | 중요 비즈니스용 | vCore당 4개 |
| SQL Managed Instance 또는 인스턴스 풀 | 범용 | vCore당 1개 |
| SQL Database 또는 탄력적 풀<sup>1</sup> | 중요 비즈니스용 | vCore당 4개 |
| SQL Database 또는 탄력적 풀<sup>1</sup> | 범용 | vCore당 1개 |
| SQL Database 또는 탄력적 풀<sup>1</sup> | 하이퍼스케일 | vCore당 1개 |
| Azure Data Factory SQL Server Integration Services | 엔터프라이즈 | vCore당 4개 |
| Azure Data Factory SQL Server Integration Services | Standard | vCore당 1개 |
| SQL Server Virtual Machines<sup>2</sup> | 엔터프라이즈 | vCPU당 4 |
| SQL Server Virtual Machines<sup>2</sup> | Standard | vCPU당 1 |

<sup>1</sup> Azure SQL Database의 서버리스 컴퓨팅 계층에서는 Azure 하이브리드 혜택을 사용할 수 없습니다.

<sup>2</sup> Virtual Machine당 최소 4개의 vCore 라이선스가 적용됩니다.

## <a name="ongoing-scope-level-management"></a>지속적인 범위 수준 관리

Azure 하이브리드 혜택을 중앙에서 관리하는 경우 다음 작업 및 순서와 유사하게 사전 예방적인 반복 절차를 설정하는 것이 좋습니다.

- 조직 내에 참여하여 다음 달, 분기 또는 연도에 사용될 Azure SQL 리소스 및 vCore 수를 파악합니다.
- 조달 및 소프트웨어 자산 관리 부서와 협력하여 Software Assurance가 포함된 충분한 SQL 핵심 라이선스를 사용할 수 있는지 확인합니다. 이 혜택을 통해 워크로드 마이그레이션을 지원하는 라이선스를 최대 180일 동안 온-프레미스와 Azure에서 모두 사용할 수 있습니다. 따라서 해당 라이선스는 사용 가능한 것으로 계산될 수 있습니다.
- 현재 사용량’과’ 향후 기간 동안 예상되는 사용량 증가를 처리할 수 있는 라이선스를 할당합니다.
- 할당된 라이선스 사용률을 모니터링합니다.
  - 100%에 근접하는 경우 조직의 다른 사용자에게 문의하여 예상되는 사용량을 파악합니다. 라이선스 가용성을 확인한 다음, 해당 범위에 더 많은 라이선스를 할당합니다.
  - 사용량이 100%이면 할당된 라이선스 수를 초과하는 리소스를 사용 중일 수 있습니다. [라이선스 할당 환경 만들기](create-sql-license-assignments.md)로 돌아가서 Azure에 표시되는 사용량을 검토합니다. 그런 다음, 더 많은 적용 범위에 사용 가능한 추가 라이선스를 할당합니다.
- 정기적으로 자동 관리 프로세스를 반복합니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server의 Azure 하이브리드 혜택 관리 및 최적화](tutorial-azure-hybrid-benefits-sql.md) 자습서를 따릅니다.
- [중앙에서 관리되는 Azure 하이브리드 혜택으로 전환](transition-existing.md)하는 방법을 알아봅니다.
- [중앙에서 관리되는 Azure 하이브리드 혜택 FAQ](faq-azure-hybrid-benefit-scope.yml)를 검토하세요.