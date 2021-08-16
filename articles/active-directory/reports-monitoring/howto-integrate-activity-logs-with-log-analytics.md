---
title: Azure Monitor 로그에 Azure Active Directory 로그 스트리밍 | Microsoft Docs
description: Azure Monitor 로그와 Azure Active Directory 로그를 통합하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/11/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c29b631d3002f0c79fb2dd1b1f26dfa65051368c
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112018970"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Azure AD 로그를 Azure Monitor 로그와 통합

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure Monitor 로그를 사용하면 데이터를 쿼리하여 특정 이벤트를 찾고, 추세를 분석하고, 다양한 데이터 원본 간의 상관 관계를 수행할 수 있습니다. 이제 Azure Monitor 로그에 Azure AD 활동 로그를 통합함으로써 다음과 같은 작업을 수행할 수 있습니다.

 * Azure Security Center에서 게시한 보안 로그와 Azure AD 로그인 로그를 비교합니다.

 * Azure Application Insights의 애플리케이션 성능 데이터를 서로 연결하여 애플리케이션의 로그인 페이지에서 성능 병목 현상을 해결합니다.  

Ignite 세션의 다음 비디오는 실제 사용자 시나리오에서 Azure AD 로그에 대해 Azure Monitor 로그를 사용할 때의 이점을 보여줍니다.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

이 문서에서는 Azure Monitor와 Azure AD(Azure Active Directory) 로그를 통합하는 방법을 알아봅니다.

## <a name="supported-reports"></a>지원되는 보고서

추가 분석을 위해 감사 활동 로그와 로그인 활동 로그를 Azure Monitor 로그로 라우팅할 수 있습니다. 

* **감사 로그**: [감사 로그 활동 보고서](concept-audit-logs.md)는 테넌트에서 수행된 모든 작업 기록에 대한 액세스를 제공합니다.
* **로그인 로그**: [로그인 활동 보고서](concept-sign-ins.md)를 사용하면 감사 로그에 보고된 작업을 누가 수행했는지 확인할 수 있습니다.
* **프로비저닝 로그**: [프로비저닝 로그](../app-provisioning/application-provisioning-log-analytics.md)를 사용하면 모든 타사 애플리케이션에서 생성, 업데이트 및 삭제된 사용자를 모니터링할 수 있습니다. 

> [!NOTE]
> B2C 관련 감사 및 로그인 활동 로그는 현재 지원되지 않습니다.
>

## <a name="prerequisites"></a>사전 요구 사항 

이 기능을 사용하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure AD 테넌트.
* Azure AD 테넌트의 *글로벌 관리자* 또는 *보안 관리자* 인 사용자.
* Azure 구독의 Log Analytics 작업 영역. [Log Analytics 작업 영역을 만드는 방법](../../azure-monitor/logs/quick-create-workspace.md)을 알아봅니다.

## <a name="licensing-requirements"></a>라이선싱 요구 사항

이 기능을 사용하려면 Azure AD Premium P1 또는 P2 테넌트가 필요합니다. **Azure Active Directory** 의 **[개요](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)** 페이지에서 테넌트의 라이선스 유형을 찾을 수 있습니다.

![테넌트 정보](./media/howto-integrate-activity-logs-with-log-analytics/tenant-information.png)
 
작업 데이터가 프리미엄 테넌트에 저장되는 기간을 확인하려면 [Azure AD에서 데이터를 저장하는 기간](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)을 참조하세요.

## <a name="send-logs-to-azure-monitor"></a>Azure Monitor로 로그 보내기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory** > **진단 설정** -> **진단 설정 추가** 를 차례로 선택합니다. 또한 **감사 로그** 또는 **로그인 페이지** 에서 **설정 내보내기** 를 선택하여 진단 설정 구성 페이지로 이동할 수도 있습니다.  
    
3. **진단 설정** 메뉴에서 **Log Analytics 작업 영역에 보내기** 확인란을 선택한 다음, **구성** 을 선택합니다.

4. 로그를 보내려는 Log Analytics 작업 영역을 선택하거나, 제공된 대화 상자에서 새 작업 영역을 만듭니다.  

5. 다음 중 하나 또는 둘 모두를 수행합니다.
    * Log Analytics 작업 영역에 감사 로그를 보내려면 **AuditLogs** 확인란을 선택합니다. 
    * Log Analytics 작업 영역에 로그인 로그를 보내려면 **SignInLogs** 확인란을 선택합니다.

6. **저장** 을 선택하여 설정을 저장합니다.

    ![진단 설정](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. 약 15분 후에 이벤트가 Log Analytics 작업 영역으로 스트림되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그를 사용하여 Azure AD 활동 로그 분석](howto-analyze-activity-logs-log-analytics.md)
* [Azure Active Directory용 로그 분석 보기 설치 및 사용](howto-install-use-log-analytics-views.md)