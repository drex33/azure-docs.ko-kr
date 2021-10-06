---
title: Azure 하이브리드 혜택 범위 수준 관리로 전환
description: 이 문서에서는 Azure 하이브리드 혜택 범위 수준 관리로 전환하는 방법을 보여 줍니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: a813a8934ff66b10e0f3c7adce65887acebba6f8
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547862"
---
# <a name="transition-to-scope-level-management-of-azure-hybrid-benefit"></a>Azure 하이브리드 혜택 범위 수준 관리로 전환

Azure 하이브리드 혜택 범위 수준 관리로 전환하면 리소스 수준에서 혜택을 구성할 필요가 없습니다. 이 문서에서는 결과를 설명하기 위한 변경 내용 및 몇 가지 전환 시나리오를 설명합니다. 새 범위 수준 라이선스 관리 환경이 리소스에 라이선스 및 할인을 적용하는 방법에 대한 자세한 내용은 [Azure 하이브리드 혜택 범위 수준 관리란?을 참조하세요.](overview-azure-hybrid-benefit-scope.md)

## <a name="changes-to-individual-resource-configuration"></a>개별 리소스 구성에 대한 변경 내용

새 환경을 사용하여 구독에 라이선스를 할당하면 변경 내용이 Azure Portal 표시됩니다. 나중에 리소스 수준에서 혜택을 관리할 수 없습니다. 범위 수준에서 변경한 내용은 개별 리소스 수준에서 설정을 재정의합니다.

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="라이선스 재정의 정보를 SQL 스크린샷" lightbox="./media/transition-existing/sql-db-configure.png" :::

> [!NOTE]
> PowerShell, API 또는 Azure Portal 외부에서 리소스로 Azure 하이브리드 혜택 설정을 변경하면 설정 변경이 저장됩니다. 그러나 해당 리소스의 구독 또는 청구 계정에 범위에 할당된 라이선스가 적용되는 한 아무런 효과가 없습니다. 모든 라이선스 할당을 제거하여 Azure 하이브리드 혜택 범위 수준 관리를 옵트아웃하는 경우 라이선스 할인은 각 리소스의 Azure 하이브리드 혜택 설정에 따라 결정됩니다.

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>전환하기 전에 사용하는 SQL 라이선스 수 확인

Azure 하이브리드 혜택 환경의 범위 수준 관리에 등록하면 개별 리소스에 대해 사용하도록 설정된 현재 Azure 하이브리드 혜택 사용량이 표시됩니다. 전체 경험에 대한 자세한 내용은 [Azure 하이브리드 혜택 대한 SQL Server 라이선스 할당 만들기를 참조하세요.](create-sql-license-assignments.md) 구독 기여자이고 필요한 청구 관리자 역할이 없는 경우 PowerShell 스크립트를 사용하여 Azure에서 다양한 유형의 SQL Server 라이선스 사용을 분석할 수 있습니다. 스크립트는 여러 구독 또는 전체 계정에서 사용량의 스냅샷을 생성합니다. 스크립트 사용에 대한 자세한 내용과 예제는 [sql-license-usage PowerShell 스크립트](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit) 예제 스크립트를 참조하세요. 스크립트를 실행한 후에는 청구 관리자에게 Azure 하이브리드 혜택 관리를 구독 또는 청구 계정 범위 수준으로 전환할 수 있는 기회를 파악하고 참여합니다.

> [!NOTE]
> 스크립트에는 NCL(정규화된 코어 라이선스)에 대한 지원이 포함됩니다. 

## <a name="hadr-benefit-for-sql-server-vms"></a>SQL Server VM에 대한 HADR 혜택

새로운 Azure Portal 환경은 SQL Server VM에 대한 HADR(고가용성 및 재해 복구) 혜택을 완벽하게 지원합니다. SQL Server VM이 HADR 복제본으로 구성된 경우 다른 작업이 필요하지 않습니다. SQL Server VM HADR 혜택의 작동 방식에 대한 자세한 내용은 [SQL SERVER HADR 및 범위 수준 Azure 하이브리드 혜택 공존을 참조하세요.](sql-server-hadr-licenses.md)

## <a name="transition-scenario-examples"></a>전환 시나리오 예제

상황과 가장 일치하는 다음 전환 시나리오 예제를 검토합니다.

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>Azure 하이브리드 혜택 SQL Database 사용하여 온-프레미스에서 클라우드로 SQL 워크로드 마이그레이션

- **온-프레미스 SQL Server 워크로드 -** 중요 SQL Server Enterprise 버전 데이터베이스를 호스트하는 16코어 컴퓨터 두 대는 클라우드로 마이그레이션됩니다.
- **워크로드 마이그레이션을 위한 Azure 대상 -** 분석 후 워크로드가 두 개의 16코어 Azure SQL Managed Instance에서 실행하기로 결정됩니다.
- **Azure에 할당할 라이선스 -** 위의 사항을 고려할 때 Software Assurance 있는 32개의 SQL Server Enterprise 버전 핵심 라이선스가 Azure에 할당됩니다.
- **권장 작업 -** 새 Azure Portal 환경을 사용하여 적절한 구독 또는 전체 청구 계정에 32개의 SQL Server Enterprise 버전 코어 라이선스를 할당합니다.
- **결과 -** 온-프레미스에서 클라우드로 SQL Server 워크로드를 관리하기 쉽고 비용 최적화된 마이그레이션입니다.

> [!NOTE] 
> Azure 하이브리드 혜택 사용하면 워크로드가 마이그레이션, 테스트 및 배포될 때 Azure에 할당된 라이선스를 최대 180일 동안 온-프레미스에서도 계속 사용할 수 있습니다.

### <a name="simplify-license-management-by-transitioning-to-scope-level-management-of-azure-hybrid-benefit"></a>의 범위 수준 관리로 전환하여 라이선스 관리를 간소화합니다Azure 하이브리드 혜택

- **실행 중인 SQL Server 리소스 -** Azure 하이브리드 혜택 선택한 64코어 Azure SQL Database 중요 비즈니스용 하나가 실행되고 있습니다.
- **Azure에 할당할 수 있는 라이선스 -** 조달 팀은 온-프레미스에서 사용하지 않는 Software Assurance 64개 이상의 SQL Server Enterprise 버전 핵심 라이선스가 있음을 확인합니다.
- **권장 작업 -** Azure 하이브리드 혜택 환경의 새로운 범위 수준 관리를 사용하여 Azure에 64개의 SQL Server Enterprise 버전 코어 라이선스를 할당합니다. 또는 사용량이 곧 증가할 것으로 예상되는 경우 필요에 따라 더 많은 라이선스를 할당합니다.
- **결과 –** 전환은 SQL Server 소프트웨어 비용을 다루므로 관련 청구를 변경하지 않아야 합니다.

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>더 많은 Azure SQL 리소스를 포함하는 더 많은 SQL Server 라이선스를 할당하여 더 많은 비용 절감

- **실행 중인 SQL Server 리소스 -** 두 개의 16코어 Azure SQL Database 중요 비즈니스용 실행 중이며, Azure 하이브리드 혜택 하나만 선택되어 있습니다.
- **Azure에 할당할 수 있는 라이선스 -** 조달 팀에 따르면 Software Assurance 있는 48개의 SQL Server Enterprise 버전 코어 라이선스가 온-프레미스 또는 Azure에서 사용되고 있지 않습니다.
- **권장 작업 -** Azure 하이브리드 혜택 환경의 범위 수준 관리를 사용하여 48개의 SQL Server Enterprise 버전 핵심 라이선스를 모두 할당합니다. 이는 리소스 수준 Azure 하이브리드 혜택 선택에 비해 16의 증가입니다.
- **결과 -** Azure 외부에서 구매한 라이선스가 더 많이 사용되므로 Azure에서 청구되는 금액이 줄어듭니다. 또한 Software Assurance 연간 비용이 종량제 요금으로 SQL Server 연간 비용보다 적기 때문에 SQL Server 사용량에 대한 총 비용이 줄어듭니다.

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>과도한 Azure 하이브리드 혜택 사용량이 발견되면 규정 준수 복원

- **실행 중인 SQL Server 리소스 -** 3개의 8코어 SQL 데이터베이스 범용 1개의 16코어 SQL Server VM Enterprise 실행되고 있으며, Azure 하이브리드 혜택 모두 선택되어 있습니다. 24개의 Standard Edition 코어 + 16개의 SQL Server Enterprise 코어를 포함하는 데 필요한 Azure 하이브리드 혜택 라이선스 수입니다. 또는 88 SQL Server Standard 버전(+ 0 SQL Server Enterprise 버전) -또는- 22 SQL Server Enterprise(+ 0 SQL Server Standard 버전)도 이를 다룰 수 있습니다. SQL Server Enterprise 버전 코어 라이선스 1개와 SQL Server Standard 버전 코어 라이선스 4개가 모든 Azure SQL 리소스 종류에서 동일한 Azure 하이브리드 혜택 사용량을 다룰 수 있기 때문입니다. 이러한 유연성에 대해 자세히 알아보려면 [Azure 하이브리드 혜택 범위 수준 관리란?](overview-azure-hybrid-benefit-scope.md) 문서에서 Azure 하이브리드 혜택 규칙을 검토하세요.
- **Azure에 할당할 수 있는 라이선스 -** 조달 팀에 따르면 Software Assurance 있는 64개의 SQL Server Standard 버전 코어 라이선스가 온-프레미스 또는 Azure에서 사용되지 않습니다. 이는 필요한 SQL Server Enterprise 22개 또는 SQL Server Standard 버전 코어 라이선스 88개보다 적습니다.
- **권장 작업 -** 규정 준수를 복원하려면 Software Assurance 6개 SQL Server Enterprise 또는 24개의 SQL Server Standard 버전 코어 라이선스를 식별하고 Azure 하이브리드 혜택 환경의 범위 수준 관리를 사용하여 이미 확인된 64개의 SQL Server Standard 코어 라이선스를 Azure에 할당합니다.
- **결과 -** 비준수는 제거되고 Azure 하이브리드 혜택 비용을 최소화하기 위해 최적으로 사용됩니다.
- **대체 작업 -** 사용 가능한 64 SQL Server Standard 버전 코어 라이선스만 Azure에 할당합니다. 규정을 준수하지만 해당 라이선스가 모든 Azure SQL 사용량을 다루기에 충분하지 않기 때문에 일부 종량제 요금이 발생합니다.
## <a name="next-steps"></a>다음 단계

- SQL Server [Azure 하이브리드 혜택 관리 및 최적화 자습서를](tutorial-azure-hybrid-benefits-sql.md) 따릅니다.
- SQL Server 라이선스 [할당을 만들어](create-sql-license-assignments.md)범위 수준 라이선스 관리로 이동합니다.
- 범위 [수준 Azure 하이브리드 혜택 관리 FAQ를 검토합니다.](faq-azure-hybrid-benefit-scope.yml)