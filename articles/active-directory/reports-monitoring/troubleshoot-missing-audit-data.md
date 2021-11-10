---
title: 활동 로그의 데이터 누락 문제 해결 | Microsoft Docs
description: Azure Active Directory 활동 로그의 데이터 누락 문제 해결 방법을 제공합니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a41d51c6cf5b723f4bbb7a94d0af87d5c3f67758
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995695"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>문제 해결: Azure Active Directory 활동 로그의 데이터 누락 | Microsoft Docs 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Azure Portal에서 최근 작업의 감사 로그를 찾을 수 없습니다.

### <a name="symptoms"></a>증상

Azure Portal에서 일부 작업을 수행했고 `Activity logs > Audit Logs` 블레이드에서 해당 작업에 대한 감사 로그가 표시될 것을 예상했지만 찾을 수 없습니다.

 ![스크린샷에 감사 로그 항목이 표시됩니다.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>원인

작업이 활동 로그에 즉시 나타나지 않습니다. 아래 표에는 활동 로그의 대기 시간 숫자가 나열되어 있습니다. 

| 보고서 | 대기 시간(P95) | 대기 시간(P99) |
|--------|---------------|---------------|
| 디렉터리 감사 | 2분 | 5분 |
| 로그인 작업 | 2분 | 5분 |

### <a name="resolution"></a>해결 방법

15분에서 2시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 2시간 후에도 로그가 보이지 않으면 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)해 주시면 저희 쪽에서 살펴보겠습니다.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Azure Active Directory 로그인 활동 로그에서 최근 사용자 로그인을 찾을 수 없습니다.

### <a name="symptoms"></a>증상

최근에 Azure Portal에 로그인했고 `Activity logs > Sign-ins` 블레이드에 이 작업에 대한 로그인 로그가 표시될 것으로 예상했지만 찾을 수 없습니다.

 ![스크린샷에 활동 로그의 로그인이 표시됩니다.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>원인

작업이 활동 로그에 즉시 나타나지 않습니다. 아래 표에는 활동 로그의 대기 시간 숫자가 나열되어 있습니다. 

| 보고서 | 대기 시간(P95) | 대기 시간(P99) |
|--------|---------------|---------------|
| 디렉터리 감사 | 2분 | 5분 |
| 로그인 작업 2분 | 5분 |

### <a name="resolution"></a>해결 방법

15분에서 2시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 2시간 후에도 로그가 보이지 않으면 [지원 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)해 주시면 저희 쪽에서 살펴보겠습니다.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Azure Portal에서 30일이 초과된 보고서 데이터를 볼 수 없습니다.

### <a name="symptoms"></a>증상

Azure Portal에서 30일이 초과된 로그인 및 감사 데이터를 볼 수 없습니다. 그 이유는 

 ![날짜 메뉴를 보여 주는 스크린샷.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>원인

Azure Active Directory 작업은 사용자 라이선스에 따라 다음 기간 동안 작업 보고서를 저장합니다.

| 보고서           | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---                 |
| 디렉터리 감사  |  7 일       | 30일             | 30일             |
| 로그인 작업 | 사용할 수 없습니다. 로그인 작업은 개별 사용자 프로필 블레이드에서 7일 동안 액세스할 수 있습니다. | 30일 | 30일             |

자세한 내용은 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)을 참조하세요.  

### <a name="resolution"></a>해결 방법

데이터를 30일 넘게 보존하는 두 가지 옵션이 있습니다. [Azure AD Reporting API](concept-reporting-api.md)를 사용하여 프로그래밍 방식으로 데이터를 검색하고 데이터베이스에 저장합니다. 또는 감사 로그를 Splunk 또는 SumoLogic 같은 타사 SIEM 시스템에 통합합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 보고서 보존](reference-reports-data-retention.md).
* [Azure Active Directory 보고 대기 시간](reference-reports-latencies.md).
* [Azure Active Directory 보고 FAQ](reports-faq.yml).

