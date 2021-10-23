---
title: Azure 하이브리드 혜택에 대 한 SQL Server 라이선스 할당 만들기
description: 이 문서에서는 Azure 하이브리드 혜택에 대 한 SQL Server 라이선스 할당을 만드는 방법을 설명 합니다.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: e97c4a51366dbfd2c9016de4684129a91df0cbe9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218942"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>Azure 하이브리드 혜택에 대 한 SQL Server 라이선스 할당 만들기

Azure Portal의 새로운 중앙 집중식 Azure 하이브리드 혜택 환경은 계정 수준 또는 특정 구독 수준에서 SQL Server 라이선스 할당을 지원 합니다. 계정 수준에서 할당을 만들 때 계정에 있는 모든 구독의 SQL 리소스에 할당에 지정 된 라이선스 수까지 Azure 하이브리드 혜택 할인이 자동으로 적용 됩니다.

각 라이선스 할당에 대해 범위를 선택 하 고 라이선스를 범위에 할당 합니다. 각 범위에는 여러 개의 라이선스 항목이 있을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

SQL Server 라이선스 할당을 만들려면 다음 필수 구성 요소를 충족 해야 합니다.

- 조직에 지원 되는 계약 유형 및 지원 되는 제품이 있습니다.
- 사용자는 SQL 라이선스를 할당할 수 있는 권한이 있는 역할의 멤버입니다.
- 조직에는 Azure에 할당 하는 데 사용할 수 있는 소프트웨어 보증 또는 코어 구독 라이선스가 있는 SQL Server 코어 라이선스가 있습니다.
- 조직이 IaaS 확장을 사용 하 여 Azure SQL vm의 자동 등록에 등록 됩니다. 자세히 알아보려면 [SQL IaaS 에이전트 확장을 사용 하 여 자동 등록](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md)을 참조 하세요.
  > [!IMPORTANT]
  > 이 필수 구성 요소를 충족 하지 못하면 Azure에서 현재 Azure 하이브리드 혜택 사용에 대 한 불완전 한 데이터를 생성 합니다. 이 경우 잘못 된 라이선스 할당이 발생할 수 있으며 SQL Server 라이선스에 대해 불필요 한 종 량 제 요금이 발생할 수 있습니다.

필수 구성 요소 역할은 규약 유형에 따라 달라 집니다.

| 규약 유형 | 필수 역할 | 지원 되는 제품 |
| --- | --- | --- |
| 기업 계약 | _엔터프라이즈 관리자_<p> 역할의 멤버가 되는 방법에 대 한 자세한 내용은 [다른 엔터프라이즈 관리자 추가](../manage/ea-portal-administration.md#add-a-department-administrator)를 참조 하세요. | -ms-azr-0017p-0017p (Microsoft Azure Enterprise)<br>-MS-AZR-0017P-미국 정부-0017P (Azure Government Enterprise) |
| Microsoft 고객 계약 | *청구 계정 소유자*<br> *청구 계정 기여자* <br> *청구 프로필 소유자*<br> *청구 프로필 기여자*<br> 역할의 구성원이 되는 방법에 대 한 자세한 내용은 [청구 역할 관리](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조 하세요. | ms-azr-0017p-0017g (Microsoft Azure 계획)|
| WebDirect/종 량 제 | 사용할 수 없음 | 없음 |
| CSP/파트너 led 고객 | 사용할 수 없음 | 없음 |

> [!NOTE]
> 범위에 라이선스를 중앙에서 할당 하는 것은 후원, MSDN 크레딧 구독 또는 MPN 구독에서 사용할 수 없습니다. SQL 소프트웨어 사용은 개발/테스트 구독 (ms-azr-0017p-ms-azr-0148p 또는 ms-azr-0017p-0023p 제품 유형)에 대해 무료입니다.

## <a name="create-a-sql-license-assignment"></a>SQL 라이선스 할당 만들기

다음 절차에서는 **Cost Management + 청구** 에서 **예약 + 하이브리드 혜택** 으로 이동 합니다. Azure 홈 페이지에서 **예약** 으로 이동 하지 마세요. 이렇게 하면 라이선스 할당 환경을 확인 하는 데 필요한 범위가 없습니다. 

1. Azure Portal에 로그인 하 여 **Cost Management + 청구** 로 이동 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="Cost Management + 청구로 Azure Portal 탐색을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
 2. 기업계약 있는 경우 청구 범위를 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="EA 청구 범위 선택을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
 3. Microsoft 고객 계약이 있는 경우 청구 프로필을 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="청구 프로필 선택을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
 4. 왼쪽 메뉴에서 **예약 + 하이브리드 혜택** 을 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="예약 + 하이브리드 혜택 선택 항목을 보여 주는 스크린샷"  :::
 5. **추가** 를 선택한 다음 목록에서 **Azure 하이브리드 혜택 (미리 보기)** 를 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="선택 Azure 하이브리드 혜택을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
 6. 다음 화면에서 **시작을 선택 하 여 라이선스를 할당** 합니다 .를 선택 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="하이브리드 혜택 선택 추가 SQL를 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
 7. 범위를 선택한 다음 각 SQL Server 버전에 사용할 라이선스 수를 입력 합니다. 특정 버전에 할당할 라이선스가 없는 경우 0을 입력 합니다.  
    > [!NOTE]
    > 범위 수준 관리 라이선스 환경에서 수행 하는 항목이 정확 하 고 사용자의 라이선스 의무를 충족 하는지 확인 해야 합니다. 라이선스를 할당할 때 도움을 주는 라이선스 사용 정보가 표시 됩니다. 그러나 표시 되는 정보는 다양 한 요인으로 인해 불완전 하거나 정확 하지 않을 수 있습니다.
    >
    > 입력 하는 라이선스 수가 현재 사용 하 고 있는 것 보다 적으면 _이 범위에서 현재 Azure 하이브리드 혜택 사용 중인 것 보다 더 낮은 라이선스를 입력 했다는 경고 메시지가 표시 됩니다. 이 범위에 대 한 청구서가 증가 합니다._  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="범위 선택 및 라이선스 수를 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
 8. 필요에 따라 **사용 세부 정보** 탭을 선택 하 여 리소스 범위에서 사용 하도록 설정 된 현재 Azure 하이브리드 혜택 사용량을 확인 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="사용 탭 정보를 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
 9. **추가** 를 선택합니다.
10. 필요에 따라 기본 라이선스 할당 이름을 변경 합니다. 검토 날짜는 1 년 전에 자동으로 설정 되며 변경할 수 없습니다. 이는 주기적으로 라이선스 할당을 검토 하 라는 알림을 표시 하기 위한 것입니다.  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="기본 라이선스 할당 이름을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
11. 선택 항목을 검토 한 후 **다음: 검토 + 적용** 을 선택 합니다.
12. 증명 **&quot; 적용 &quot;** 옵션을 선택 하 여 Azure 하이브리드 혜택, 충분 한 SQL Server 라이선스를 적용할 권한이 있는지 확인 하 고 할당 된 라이선스를 유지 관리 합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="증명 옵션을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
13. **적용** 을 선택한 다음, **예를 선택 합니다.**
14. 할당 된 라이선스 목록이 표시 됩니다.  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="할당 된 라이선스 목록을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>할당 된 라이선스 사용 추적

**Cost Management + 청구** 로 이동한 다음 **예약 + 하이브리드 혜택** 을 선택 합니다.

모든 예약 및 라이선스 할당 목록이 표시 됩니다. 라이선스 할당에 대해서만 결과를 필터링 하려면 **하이브리드 혜택 SQL** 필터를 설정 합니다.

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="예약 및 라이선스 목록을 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

**마지막 일 사용률** 또는 **7 일 사용률** 에서 할당 사용 기록을 자세히 보기 위해 비어 있거나 0 일 수 있는 백분율을 선택 합니다.

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="할당 사용량 정보를 보여 주는 스크린샷" lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

라이선스 할당의 사용량이 100% 이면 범위 내의 일부 리소스가 SQL Server에 대해 종 량 제 요금이 부과 될 수 있습니다. 라이선스 할당 환경을 다시 사용 하 여 할당 된 라이선스에 따라 적용 되는 사용량의 양을 검토 하는 것이 좋습니다. 그런 다음, 조달 또는 소프트웨어 자산 관리 부서 컨설팅, 더 많은 라이선스를 사용할 수 있는지 확인 및 라이선스 할당을 비롯 하 여 이전과 동일한 프로세스를 진행 합니다.

## <a name="changes-after-license-assignment"></a>라이선스 할당 후의 변경 내용

SQL 라이선스 할당을 만든 후에는 Azure Portal에서 Azure 하이브리드 혜택를 변경 하는 경험이 있습니다.

- 개별 SQL 리소스에 대해 구성 된 기존 Azure 하이브리드 혜택 선거는 더 이상 적용 되지 않습니다. 구독 또는 계정 수준에서 만든 SQL 라이선스 할당으로 대체 됩니다.
- 하이브리드 혜택 옵션은 SQL 리소스 구성에 표시 되지 않습니다.
- 하이브리드 혜택을 구성 하는 응용 프로그램 또는 스크립트는 프로그래밍 방식으로 계속 작동 하지만 설정은 적용 되지 않습니다.
- SQL 소프트웨어 할인이 범위의 SQL 리소스에 적용 됩니다. 범위는 리소스를 만든 계정에 대 한 구독에 대해 만들어진 라이선스 할당의 라이선스 수를 기반으로 합니다.
- 하이브리드 혜택에 대해 구성 된 특정 리소스는 다른 리소스가 모든 라이선스를 사용 하는 경우 할인을 받지 않을 수 있습니다. 그러나 라이선스 개수에 따라 최대 할인이 범위에 적용 됩니다. 할인이 적용 되는 방법에 대 한 자세한 내용은 [Azure 하이브리드 혜택 중앙에서 관리 되는 항목](overview-azure-hybrid-benefit-scope.md)을 참조 하세요.

## <a name="cancel-a-license-assignment"></a>라이선스 할당 취소

라이선스 할당을 취소 하기 전에 라이선스 상태를 검토 합니다. 라이선스 할당을 취소 하면 더 이상 해당 라이선스에 대 한 할인이 수신 되지 않습니다. 따라서 Azure 청구서가 증가할 수 있습니다. 마지막으로 남은 라이선스 할당을 취소하면 Azure 하이브리드 혜택 관리가 개별 리소스 수준으로 되돌아갑니다.

### <a name="to-cancel-a-license-assignment"></a>라이선스 할당을 취소하려면

1. 예약 및 라이선스 할당 목록에서 라이선스 할당을 선택합니다.
1. 라이선스 할당 세부 정보 페이지에서 **취소를** 선택합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="취소 옵션을 보여주는 스크린샷." :::
1. 취소 페이지에서 알림을 검토한 다음 **예, 취소를** 선택합니다.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="취소 페이지를 보여주는 스크린샷." lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>다음 단계

- [중앙에서 관리되는 Azure 하이브리드 혜택 FAQ를 검토합니다.](faq-azure-hybrid-benefit-scope.yml)
- [중앙에서 관리되는 Azure 하이브리드 혜택 무엇인가요?에서](overview-azure-hybrid-benefit-scope.md)할인이 적용되는 방법에 대해 알아봅니다.