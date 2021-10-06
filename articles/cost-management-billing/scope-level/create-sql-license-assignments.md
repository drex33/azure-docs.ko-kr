---
title: Azure 하이브리드 혜택 대한 SQL Server 라이선스 할당 만들기
description: 이 문서에서는 Azure 하이브리드 혜택 대한 SQL Server 라이선스 할당을 만드는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: 48b68260e07d6e69e41daeacae631a9415f48a65
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547860"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>Azure 하이브리드 혜택 대한 SQL Server 라이선스 할당 만들기

Azure Portal 새로운 Azure 하이브리드 혜택 환경은 계정 수준 또는 특정 구독 수준에서 SQL Server 라이선스 할당을 지원합니다. 계정 수준에서 할당을 만들면 Azure 하이브리드 혜택 할인이 할당에 지정된 라이선스 수까지 계정의 모든 구독에서 SQL 리소스에 자동으로 적용됩니다.

각 라이선스 할당에 대해 범위가 선택되고 라이선스가 범위에 할당됩니다. 각 범위에는 여러 라이선스 항목이 있을 수 있습니다.

## <a name="prerequisites"></a>필수 조건

SQL Server 라이선스 할당을 만들려면 다음 필수 조건이 충족되어야 합니다.

- 조직에 지원되는 계약 유형 및 지원되는 제안이 있습니다.
- SQL 라이선스를 할당할 수 있는 권한이 있는 역할의 멤버입니다.
- 조직에는 Azure에 할당할 수 있는 Software Assurance 또는 핵심 구독 라이선스가 있는 SQL Server 핵심 라이선스가 있습니다.
- 조직이 IaaS 확장을 사용하여 Azure SQL VM의 자동 등록에 등록됩니다. 자세한 내용은 [SQL IaaS 에이전트 확장을 사용하여 자동 등록을 참조하세요.](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md)
  > [!IMPORTANT]
  > 이 필수 구성을 충족하지 못하면 Azure에서 현재 Azure 하이브리드 혜택 사용량에 대한 불완전한 데이터를 생성하게 됩니다. 이 경우 라이선스 할당이 잘못될 수 있으며 SQL Server 라이선스에 대한 불필요한 종량제 요금이 발생할 수 있습니다.

필수 구성 조건 역할은 계약 유형에 따라 다릅니다.

| 규약 유형 | 필수 역할 | 지원되는 제안 |
| --- | --- | --- |
| 기업 계약 | _엔터프라이즈 관리자_<p> 역할의 멤버가 되는 방법에 대한 자세한 내용은 [다른 엔터프라이즈 관리자 추가를 참조하세요.](../manage/ea-portal-administration.md#add-a-department-administrator) | - MS-AZR-0017P(Microsoft Azure Enterprise)<br>- MS-AZR-USGOV-0017P(Azure Government Enterprise) |
| Microsoft 고객 계약 | *청구 계정 소유자*<br> *청구 계정 기여자* <br> *청구 프로필 소유자*<br> *청구 프로필 기여자*<br> 역할의 멤버가 되는 방법에 대한 자세한 내용은 [청구 역할 관리를 참조하세요.](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal) | MS-AZR-0017G(Microsoft Azure 계획)|
| WebDirect/종량제 | 사용할 수 없음 | 없음 |
| CSP/파트너 주도 고객 | 사용할 수 없음 | 없음 |

> [!NOTE]
> 범위 수준 라이선스 환경은 스폰서, MSDN 크레딧 구독 또는 MPN 구독에 사용할 수 없습니다. SQL 소프트웨어 사용량은 개발/테스트 구독(MS-AZR-0148P 또는 MS-AZR-0023P 제품 유형)에 대해 무료입니다.

## <a name="create-a-sql-license-assignment"></a>SQL 라이선스 할당 만들기

다음 절차에서는 **Cost Management + Billing** 예약 + 하이브리드 **혜택** 으로 이동합니다. Azure 홈페이지에서 **예약으로** 이동하지 마세요. 이렇게 하면 라이선스 할당 환경을 보는 데 필요한 범위가 없습니다. 

1. Azure Portal 로그인하고 **Cost Management + Billing** 으로 이동합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="Cost Management + Billing Azure Portal 탐색을 보여 Cost Management + Billing 스크린샷" lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
 2. 기업계약 있는 경우 청구 범위를 선택합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="EA 청구 범위 선택을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
 3. Microsoft 고객 계약 있는 경우 청구 프로필을 선택합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="청구 프로필 선택을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
 4. 왼쪽 메뉴에서 예약 **+ 하이브리드 혜택을** 선택합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="예약 + 하이브리드 혜택 선택을 보여 주는 스크린샷"  :::
 5. **추가를** 선택한 다음, 목록에서 **Azure 하이브리드 혜택(미리 보기)** 를 선택합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="Azure 하이브리드 혜택 선택을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
 6. 다음 화면에서 시작 **을 선택하여 라이선스를 할당합니다.**  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="SQL 하이브리드 혜택 선택 추가를 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
 7. 범위를 선택한 다음 각 SQL Server 버전에 사용할 라이선스 수를 입력합니다. 특정 버전에 할당할 라이선스가 없는 경우 0을 입력합니다.  
    > [!NOTE]
    > 범위 수준 관리 라이선스 경험에서 만드는 항목이 정확하고 라이선스 의무를 충족하는지 확인할 책임이 있습니다. 라이선스를 할당할 때 도움이 되는 라이선스 사용 정보가 표시됩니다. 그러나 표시된 정보는 다양한 요인으로 인해 불완전하거나 부정확할 수 있습니다.
    >
    > 입력한 라이선스 수가 현재 사용 중인 라이선스 수보다 적으면 _이 범위에서 현재 Azure 하이브리드 혜택 사용하는 것보다 적은 라이선스를 입력했습니다.라는 경고 메시지가 표시됩니다. 이 범위에 대한 청구서가 증가합니다._  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="범위 선택 및 라이선스 수를 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
 8. 필요에 따라 **사용량 세부 정보** 탭을 선택하여 리소스 범위에서 활성화된 현재 Azure 하이브리드 혜택 사용량을 확인합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="사용량 탭 세부 정보를 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
 9. **추가** 를 선택합니다.
10. 필요에 따라 기본 라이선스 할당 이름을 변경합니다. 검토 날짜는 자동으로 1년 앞으로 설정되며 변경할 수 없습니다. 그 목적은 라이선스 할당을 주기적으로 검토하도록 알려주는 것입니다.  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="기본 라이선스 할당 이름을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
11. 선택 항목을 검토한 후 **다음: 검토 + 적용을** 선택합니다.
12. 증명 **&quot; &quot; 적용을 선택하여** Azure 하이브리드 혜택 적용할 권한이 있는지, 충분한 SQL Server 라이선스가 있는지, 라이선스가 할당된 한 라이선스를 유지 관리해야 하는지 확인합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="증명 옵션을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
13. **적용을** 선택한 다음, **예를 선택합니다.**
14. 할당된 라이선스 목록이 표시됩니다.  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="할당된 라이선스 목록을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>할당된 라이선스 사용 추적

**Cost Management + Billing** 이동한 **다음, 예약 + 하이브리드 혜택을** 선택합니다.

모든 예약 및 라이선스 할당 목록이 표시됩니다. 결과를 라이선스 할당으로만 필터링하려면 **SQL 하이브리드 혜택에** 대한 필터를 설정합니다.

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="예약 및 라이선스 목록을 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

**마지막 날 사용률** 또는 **7일 사용률** 에서 빈 또는 0일 수 있는 백분율을 선택하여 할당 사용 기록을 자세히 확인합니다.

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="할당 사용량 세부 정보를 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

라이선스 할당 사용량이 100%이면 범위 내의 일부 리소스에서 SQL Server 대한 종량제 요금이 발생할 수 있습니다. 라이선스 할당 환경을 다시 사용하여 할당된 라이선스에서 적용되는 사용량을 검토하는 것이 좋습니다. 그런 다음 조달 또는 소프트웨어 자산 관리 부서에 문의하고, 더 많은 라이선스를 사용할 수 있는지 확인하고, 라이선스를 할당하는 등 이전과 동일한 프로세스를 진행합니다.

## <a name="changes-after-license-assignment"></a>라이선스 할당 후 변경 내용

SQL 라이선스 할당을 만든 후에는 Azure Portal Azure 하이브리드 혜택 변경된 경험이 있습니다.

- 개별 SQL 리소스에 대해 구성된 기존 Azure 하이브리드 혜택 더 이상 적용되지 않습니다. 구독 또는 계정 수준에서 만든 SQL 라이선스 할당으로 대체됩니다.
- 하이브리드 혜택 옵션은 SQL 리소스 구성에서와 같이 표시되지 않습니다.
- 하이브리드 혜택을 프로그래밍 방식으로 구성하는 애플리케이션 또는 스크립트는 계속 작동하지만 설정에는 영향을 주지 않습니다.
- SQL 소프트웨어 할인은 범위의 SQL 리소스에 적용됩니다. 범위는 리소스가 만들어진 계정의 구독에 대해 생성된 라이선스 할당의 라이선스 수를 기반으로 합니다.
- 하이브리드 혜택에 대해 구성된 특정 리소스는 다른 리소스가 모든 라이선스를 사용하는 경우 할인을 받지 못할 수 있습니다. 그러나 라이선스 수에 따라 최대 할인이 범위에 적용됩니다. 할인이 적용되는 방법에 대한 자세한 내용은 [Azure 하이브리드 혜택 범위 수준 관리란?을 참조하세요.](overview-azure-hybrid-benefit-scope.md)

## <a name="cancel-a-license-assignment"></a>라이선스 할당 취소

라이선스 할당을 취소하기 전에 라이선스 상황을 검토합니다. 라이선스 할당을 취소하면 더 이상 할인이 수신되지 않습니다. 따라서 Azure 청구서가 증가할 수 있습니다. 마지막 남은 라이선스 할당을 취소 하면 Azure 하이브리드 혜택 관리가 개별 리소스 수준으로 되돌아갑니다.

### <a name="to-cancel-a-license-assignment"></a>라이선스 할당을 취소 하려면

1. 예약 및 라이선스 할당 목록에서 라이선스 할당을 선택 합니다.
1. 라이선스 할당 정보 페이지에서 **취소** 를 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="취소 옵션을 보여 주는 스크린샷" :::
1. 취소 페이지에서 알림을 검토 하 고 **예, 취소를** 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="취소 페이지를 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>다음 단계

- [범위 수준 Azure 하이브리드 혜택 관리 FAQ](faq-azure-hybrid-benefit-scope.yml)를 검토 합니다.
- [Azure 하이브리드 혜택의 범위 수준 관리 란?](overview-azure-hybrid-benefit-scope.md)에서 할인이 적용 되는 방법에 대해 알아봅니다.