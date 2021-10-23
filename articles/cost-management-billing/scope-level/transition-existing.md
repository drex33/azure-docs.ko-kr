---
title: 중앙에서 관리 되는 Azure 하이브리드 혜택로 전환
description: 이 문서에서는 중앙에서 관리 되는 Azure 하이브리드 혜택로 전환 하는 방법을 설명 하는 변경 내용 및 여러 전환 시나리오를 설명 합니다.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: be701e090a7fd993f476cd45194b73368c589dc5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223995"
---
# <a name="transition-to-centrally-managed-azure-hybrid-benefit"></a>중앙에서 관리 되는 Azure 하이브리드 혜택로 전환

중앙에서 관리 되는 Azure 하이브리드 혜택로 전환 하는 경우 리소스 수준에서 혜택을 구성 하지 않아도 됩니다. 이 문서에서는 결과를 설명 하는 변경 내용과 몇 가지 전환 시나리오에 대해 설명 합니다. 새 범위 수준 라이선스 관리 환경에서 리소스에 라이선스 및 할인을 적용 하는 방법에 대 한 자세한 내용은 [Azure 하이브리드 혜택 중앙에서 관리 되는 항목](overview-azure-hybrid-benefit-scope.md) 을 참조 하세요.

## <a name="changes-to-individual-resource-configuration"></a>개별 리소스 구성 변경

새 환경을 사용 하 여 구독에 라이선스를 할당 하는 경우 Azure Portal에 변경 내용이 표시 됩니다. 나중에 리소스 수준에서 이점을 관리할 수 없습니다. 범위 수준에서 변경한 내용은 개별 리소스 수준에서 설정을 재정의 합니다.

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="SQL 라이선스 재정의 정보를 보여 주는 스크린샷" lightbox="./media/transition-existing/sql-db-configure.png" :::

> [!NOTE]
> PowerShell, API 또는 Azure Portal 외부의 리소스를 사용 하 여 Azure 하이브리드 혜택 설정을 변경 하는 경우 설정 변경 내용이 저장 됩니다. 그러나 리소스의 구독 또는 청구 계정이 범위에 할당 된 라이선스에 적용 되는 경우에는 영향을 주지 않습니다. 모든 라이선스 할당을 제거 하 여 Azure 하이브리드 혜택의 범위 수준 관리를 옵트아웃 한 경우 라이선스 할인이 각 리소스의 Azure 하이브리드 혜택 설정에 따라 결정 됩니다.

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>전환 전에 사용 하는 SQL 라이선스 수 확인

Azure 하이브리드 혜택 환경의 범위 수준 관리를 등록할 때 개별 리소스에 대해 사용 하도록 설정 된 현재 Azure 하이브리드 혜택 사용이 표시 됩니다. 전체 환경에 대 한 자세한 내용은 [Azure 하이브리드 혜택에 대 한 라이선스 할당 SQL Server 만들기](create-sql-license-assignments.md)를 참조 하세요. 구독 참가자 이며 청구 관리자 역할이 필요 하지 않은 경우 PowerShell 스크립트를 사용 하 여 Azure에서 다양 한 유형의 SQL Server 라이선스 사용을 분석할 수 있습니다. 이 스크립트는 여러 구독 또는 전체 계정에서 사용 하는 스냅숏을 생성 합니다. 스크립트 사용에 대 한 자세한 내용 및 예제는 [sql 라이선스 사용 PowerShell 스크립트](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit) 예제 스크립트를 참조 하세요. 스크립트를 실행 한 후에는 청구 관리자에 게 Azure 하이브리드 혜택 관리를 구독 또는 청구 계정 범위 수준으로 이동할 기회에 대해 확인 하 고 참여 합니다.

> [!NOTE]
> 스크립트는 정규화 된 코어 라이선스 (NCL)에 대 한 지원을 포함 합니다. 

## <a name="hadr-benefit-for-sql-server-vms"></a>SQL Server vm에 대 한 HADR 혜택

새 Azure Portal 환경은 SQL Server vm에 대 한 고가용성 및 재해 복구 (HADR) 혜택을 완벽 하 게 지원 합니다. SQL Server VM이 HADR 복제본으로 구성 된 경우 다른 작업은 필요 하지 않습니다. SQL Server VM HADR 혜택의 작동 방식에 대 한 자세한 내용은 [SQL Server HADR 및 중앙 관리형 Azure 하이브리드 혜택 공존 성](sql-server-hadr-licenses.md)을 참조 하세요.

## <a name="transition-scenario-examples"></a>전환 시나리오 예

상황과 가장 일치 하는 다음 전환 시나리오 예를 검토 합니다.

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>Azure 하이브리드 혜택에서 SQL Database를 사용 하 여 온-프레미스에서 클라우드로 SQL 워크 로드 마이그레이션

- **온-프레미스 SQL Server 워크 로드-** 2 16-중요 업무용 SQL Server Enterprise 버전 데이터베이스를 호스트 하는 핵심 컴퓨터는 클라우드로 마이그레이션됩니다.
- **워크 로드 마이그레이션을 위한 Azure 대상-** 분석 후에는 워크 로드가 2 16 코어 Azure SQL 관리 되는 인스턴스에서 실행 되도록 결정 됩니다.
- **Azure에 할당할 라이선스-** 위의 사항을 고려 하 여 소프트웨어 보증이 포함 된 32 SQL Server Enterprise 버전 코어 라이선스가 Azure에 할당 됩니다.
- **권장 작업-** 새 Azure Portal 환경을 사용 하 여 32 SQL Server Enterprise 버전 코어 라이선스를 적절 한 구독 또는 전체 청구 계정에 할당 합니다.
- **결과-** 온-프레미스에서 클라우드로 SQL Server 작업을 쉽게 관리 하 고, 최적화 된 상태로 마이그레이션할 수 있습니다.

> [!NOTE] 
> Azure 하이브리드 혜택를 사용 하면 워크 로드가 마이그레이션, 테스트 및 배포 됨에 따라 Azure에 할당 된 라이선스를 최대 180 일 동안에도 계속 해 서 사용할 수 있습니다.

### <a name="simplify-license-management-by-transitioning-to-centralized-scope-level-management-of-azure-hybrid-benefit"></a>Azure 하이브리드 혜택의 중앙 집중식 범위 수준 관리로 전환 하 여 라이선스 관리를 간소화 합니다.

- **SQL Server 리소스 실행 중-** 1 64-코어 Azure SQL Database 중요 비즈니스용을 (를) 실행 하 고 Azure 하이브리드 혜택를 선택 했습니다.
- **Azure에 할당할 수 있는 라이선스-** 조달 팀은 온-프레미스에서 사용 하지 않는 소프트웨어 보증이 포함 된 64 SQL Server Enterprise 버전 이상의 코어 라이선스를 확인 합니다.
- **권장 작업-** Azure 하이브리드 혜택 환경의 새로운 범위 수준 관리를 사용 하 여 Azure에 64 SQL Server Enterprise 버전 코어 라이선스를 할당 합니다. 또는 사용량이 곧 증가할 것으로 예상 되는 경우 필요에 따라 더 많은 라이선스를 할당 합니다.
- **결과 –** 전환에는 SQL Server 소프트웨어 비용이 포함 되어 있으므로 관련 청구에 대 한 변경 내용이 없어야 합니다.

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>더 많은 SQL Server 라이선스를 할당 하 여 더 많은 Azure SQL 리소스를 처리할 수 있습니다.

- **SQL Server 리소스 실행 중-** 2 16-코어 Azure SQL Database 중요 비즈니스용이 (가) 실행 중 이며, 하나의 Azure 하이브리드 혜택만 선택 됩니다.
- **Azure에 할당할 수 있는 라이선스** 조달 팀에 따라 소프트웨어 보증이 포함 된 48 SQL Server Enterprise 버전 코어 라이선스는 온-프레미스 또는 Azure에서 사용 되지 않습니다.
- **권장 작업-** Azure 하이브리드 혜택 환경의 범위 수준 관리를 사용 하 여 모든 48 SQL Server Enterprise 버전 코어 라이선스를 할당할 수 있습니다. 이는 리소스 수준 Azure 하이브리드 혜택 선택에 비해 16의 증가입니다.
- **결과-** Azure 외부에서 구매한 라이선스를 더 많이 사용 하 고 있으므로 Azure에서 청구 되는 금액을 줄여야 합니다. 또한 소프트웨어 보증의 연간 비용은 종 량 제 요금으로 SQL Server 연간 비용 보다 작으므로 SQL Server 사용량에 대 한 총 비용이 줄어듭니다.

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>과도 한 Azure 하이브리드 혜택 사용량이 발견 되 면 복원 준수

- **SQL Server 리소스 실행 중-** 3 8-core SQL database 범용 및 1 16-core SQL Server VM Enterprise 실행 중 이며, 모든 Azure 하이브리드 혜택를 선택 합니다. 이를 포함 하는 데 필요한 Azure 하이브리드 혜택 라이선스의 수는 24 개 Standard edition 코어 + 16 SQL Server Enterprise 코어입니다. 또는 88 SQL Server Standard edition (+ 0 SQL Server Enterprise edition)-또는-22 SQL Server Enterprise (+ 0 SQL Server Standard edition)도 포함 될 수 있습니다. 이는 한 SQL Server Enterprise 버전 코어 라이선스와 4 SQL Server Standard 버전 코어 라이선스가 모든 Azure SQL 리소스 유형에 서 동일한 Azure 하이브리드 혜택 사용을 처리할 수 있기 때문입니다. 이 유연성에 대 한 자세한 내용은 [중앙에서 관리 되는 항목 Azure 하이브리드 혜택?](overview-azure-hybrid-benefit-scope.md) 문서에서 Azure 하이브리드 혜택 규칙을 참조 하세요.
- **Azure에 할당할 수 있는 라이선스** 조달 팀에 따라 소프트웨어 보증이 포함 된 64 SQL Server Standard 버전 코어 라이선스는 온-프레미스 또는 Azure에서 사용 되지 않습니다. 필요한 크기 22 SQL Server Enterprise 또는 88 SQL Server Standard 버전 코어 라이선스 보다 낮습니다.
- **권장 작업-** 규정 준수를 복원 하려면 Azure 하이브리드 혜택 환경의 범위 수준 관리를 사용 하 여 6 개의 SQL Server Enterprise 또는 24 SQL Server Standard 버전의 코어 라이선스를 확인 하 고이를 Azure에 할당 하 고 이미 확인 된 64 SQL Server Standard 코어 라이선스를 할당 합니다.
- **결과-** 비준수는 제거 되 고 Azure 하이브리드 혜택는 비용을 최소화 하기 위해 최적으로 사용 됩니다.
- **대체 작업-** 사용 가능한 64 SQL Server Standard 버전 코어 라이선스만 Azure에 할당 합니다. 규정을 준수 하는 것은 아니지만 이러한 라이선스는 모든 Azure SQL 사용량을 포괄 하는 데 충분 하지 않기 때문에 종 량 제 요금이 발생 합니다.
## <a name="next-steps"></a>다음 단계

- [SQL Server의 중앙에서 관리 되는 Azure 하이브리드 혜택 최적화](tutorial-azure-hybrid-benefits-sql.md) 자습서를 따르세요.
- [SQL Server 라이선스 할당을 만들어](create-sql-license-assignments.md)범위 수준 라이선스 관리로 이동 합니다.
- [중앙에서 관리 되는 AZURE 하이브리드 혜택 FAQ](faq-azure-hybrid-benefit-scope.yml)를 검토 합니다.