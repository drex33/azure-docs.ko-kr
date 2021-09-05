---
title: Azure AD Domain Services에서 Azure Monitor 통합 문서 사용 | Microsoft Docs
description: Azure Monitor 통합 문서를 사용하여 보안 감사를 검토하고 Azure Active Directory Domain Services 관리되는 도메인의 문제를 이해하는 방법을 알아봅니다.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: a5aab12a0d3c24ef7bef9eee6c02b244e33c11e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535706"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Azure Monitor 통합 문서를 사용하여 Azure Active Directory Domain Services의 보안 감사 이벤트 검토

Azure Active Directory Domain Services(Azure AD DS) 관리되는 도메인의 상태를 이해하는 데 도움이 되도록 보안 감사 이벤트를 사용하도록 설정할 수 있습니다. 그런 다음 텍스트, 분석 쿼리 및 매개 변수를 다양한 대화형 보고서에 결합하는 Azure Monitor 통합 문서를 사용하여 이러한 보안 감사 이벤트를 검토할 수 있습니다. Azure AD DS에는 감사 이벤트를 살펴보고 환경을 관리할 수 있는 보안 개요 및 계정 작업을 위한 통합 문서 템플릿이 포함되어 있습니다.

이 문서에서는 Azure Monitor 통합 문서를 사용하여 Azure AD DS에서 보안 감사 이벤트를 검토하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory Domain Services의 관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* Log Analytics 작업 영역에 데이터를 스트리밍하는 관리되는 도메인에 대해 사용하도록 설정된 보안 감사 이벤트입니다.
    * 필요한 경우 [Azure AD DS에 대한 보안 감사를 사용하도록 설정][enable-security-audits]합니다.

## <a name="azure-monitor-workbooks-overview"></a>Azure Monitor 통합 문서 개요

Azure AD DS에서 보안 감사 이벤트가 설정되면 관리되는 도메인의 문제를 분석하고 식별하기 어려울 수 있습니다. Azure Monitor를 사용하여 이러한 보안 감사 이벤트를 집계하고 데이터를 쿼리할 수 있습니다. Azure Monitor 통합 문서를 사용하면 이 데이터를 시각화하여 더 빠르고 쉽게 문제를 식별할 수 있습니다.

통합 문서 템플릿은 여러 사용자 및 팀에서 유연하게 다시 사용할 수 있도록 큐레이팅된 보고서입니다. 통합 문서 템플릿을 열면 Azure Monitor 환경의 데이터가 로드됩니다. 조직의 다른 사용자에게 영향을 미치지 않고 템플릿을 사용할 수 있으며 템플릿에 따라 고유한 통합 문서를 저장할 수 있습니다.

Azure AD DS에는 다음과 같은 두 개의 통합 문서 템플릿이 있습니다.

* 보안 개요 보고서
* 계정 작업 보고서

통합 문서를 편집 및 관리하는 방법에 대한 자세한 내용은 [Azure Monitor 통합 문서 개요](../azure-monitor/visualize/workbooks-overview.md)를 참조하세요.

## <a name="use-the-security-overview-report-workbook"></a>보안 개요 보고서 통합 문서 사용

사용량을 보다 잘 이해하고 잠재적 보안 위협을 식별하는 데 도움이 되도록 보안 개요 보고서는 로그인 데이터를 요약하고 확인하고자 하는 계정을 식별합니다. 특정 날짜 범위의 이벤트를 확인하고, 잘못된 암호 시도 또는 계정을 사용하지 않도록 설정된 경우와 같이 특정 로그인 이벤트를 드릴다운할 수 있습니다.

보안 개요 보고서의 통합 문서 템플릿에 액세스하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다.
1. 관리형 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 메뉴에서 **모니터링 > 통합 문서** 를 선택합니다.

    ![보안 개요 보고서와 계정 작업 보고서를 선택할 수 있는 위치를 강조 표시하는 스크린샷.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. **보안 개요 보고서** 를 선택합니다.
1. 통합 문서의 상단에 있는 드롭다운 메뉴에서 Azure 구독을 선택하고 Azure Monitor 작업 영역을 선택합니다.

    다음 예제 스크린샷에 표시된 것과 같이 **시간 범위** 를 지난 7일과 같이 선택합니다.

    ![Azure Portal에서 통합 문서 메뉴 옵션을 선택합니다.](./media/use-azure-monitor-workbooks/select-query-filters.png)

    원하는 대로 데이터를 분석하고 시각화할 수 있도록 **타일 보기** 및 **차트 보기** 옵션을 변경할 수도 있습니다.

1. 특정 이벤트 유형을 드릴다운하려면 다음 예와 같이 계정 잠금 등과 같은 **로그인 결과** 카드 중 하나를 선택 합니다.

    ![Azure Monitor 통합 문서에 시각화된 보안 개요 보고서 데이터 예제](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 차트 아래에 있는 보안 개요 보고서의 하단 부분은 선택한 작업 유형을 분류합니다. 다음 예제 보고서에 표시된 것처럼 오른쪽에서 관련 사용자 이름을 기준으로 필터링할 수 있습니다.

    [![Azure Monitor 통합 문서의 계정 잠금 세부 정보.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>계정 작업 보고서 통합 문서 사용

특정 사용자 계정 문제를 해결하는 데 도움이 되도록 계정 작업 보고서는 구체적인 감사 이벤트 로그 정보를 세분화합니다. 로그인 도중 잘못된 사용자 이름 또는 암호가 입력된 경우와 로그인 시도의 출처를 검토할 수 있습니다.

계정 작업 보고서의 통합 문서 템플릿에 액세스하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다.
1. 관리형 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 메뉴에서 **모니터링 > 통합 문서** 를 선택합니다.
1. **계정 작업 보고서** 를 선택합니다.
1. 통합 문서의 상단에 있는 드롭다운 메뉴에서 Azure 구독을 선택하고 Azure Monitor 작업 영역을 선택합니다.

    **시간 범위**(예: 지난 30일)를 선택하고 **타일 보기** 에서 데이터를 표시하는 방법을 선택합니다.

    다음 예제 보고서에 표시된 것처럼 **계정 사용자 이름**(예: felix)을 기준으로 필터링할 수 있습니다.

    [![Azure Monitor 통합 문서의 계정 작업 보고서.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    차트 아래 영역에는 작업 결과 및 원본 워크스테이션과 같은 정보와 함께 개별 로그인 이벤트가 표시됩니다. 이 정보는 계정 잠금을 유발하거나 잠재적인 공격을 나타낼 수 있는 반복되는 로그인 이벤트의 출처를 확인하는 데 도움이 됩니다.

보안 개요 보고서와 마찬가지로 보고서 상단의 다른 타일로 드릴다운하여 필요에 따라 데이터를 시각화하고 분석할 수 있습니다.

## <a name="save-and-edit-workbooks"></a>통합 문서 저장 및 편집

Azure AD DS에서 제공하는 두 개의 템플릿 통합 문서는 사용자 고유의 데이터 분석을 시작하는 데 적합합니다. 데이터 쿼리 및 조사에서 더 세부적인 정보를 얻으려면 사용자 고유의 통합 문서를 저장하고 쿼리를 편집하면 됩니다.

1. 통합 문서 템플릿 중 하나의 복사본을 저장하려면 **편집 > 다른 이름으로 저장 > 공유 보고서** 를 선택한 다음 이름을 입력하고 저장합니다.
1. 템플릿의 고유한 복사본에서 **편집** 을 선택하여 편집 모드를 시작합니다. 보고서의 모든 부분 옆에 있는 파란색 **편집** 단추를 선택하고 변경할 수 있습니다.

Azure Monitor 통합 문서에 있는 모든 차트와 테이블은 Kusto 쿼리를 사용하여 생성됩니다. 사용자 고유의 쿼리를 만드는 방법에 대한 자세한 내용은 [Azure Monitor 로그 쿼리][azure-monitor-queries] 및 [Kusto 쿼리 자습서][kusto-queries]를 참조하세요.

## <a name="next-steps"></a>다음 단계

암호 및 잠금 정책을 조정해야 하는 경우 [관리되는 도메인에 대한 암호 및 잠금 정책][password-policy]을 참조하세요.

사용자와 관련된 문제를 해결하려면 [계정 로그인 문제][troubleshoot-sign-in] 또는 [계정 잠금 문제][troubleshoot-account-lockout]를 해결하는 방법을 알아보세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer
