---
title: Azure Active Directory Identity Protection 데이터 내보내기 및 사용
description: Azure Active Directory Identity Protection에서 장기 데이터를 조사하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/30/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ea682d65bde9748a4cd55ed066eb8f31eb418a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124784093"
---
# <a name="how-to-export-risk-data"></a>방법: 위험 데이터 내보내기

Azure AD는 보고서 및 보안 신호를 정의된 기간 동안 저장합니다. 위험 정보의 경우 충분히 길지 않을 수도 있습니다.

| 보고서/신호 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| --- | --- | --- | --- |
| 감사 로그 | 7 일 | 30일 | 30일 |
| 로그인 | 7 일 | 30일 | 30일 |
| Azure AD MFA 사용량 | 30일 | 30일 | 30일 |
| 위험한 로그인 | 7 일 | 30일 | 30일 |

조직은 Azure AD에서 **RiskyUsers** 및 **UserRiskEvents** 데이터를 Log Analytics 작업 영역에 전송하도록 진단 설정을 변경하여 데이터를 더 오랜 기간 저장하거나, 데이터를 스토리지 계정에 보관하거나, 데이터를 이벤트 허브로 스트리밍하거나, 파트너 솔루션에 전송하도록 선택할 수 있습니다. 이러한 옵션은 **Azure Portal** > **Azure Active Directory**, **진단 설정** > **설정 편집** 에서 확인할 수 있습니다. 진단 설정이 없는 경우 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../../azure-monitor/essentials/diagnostic-settings.md) 문서에 나온 지침에 따라 진단 설정을 만듭니다.

>[!NOTE]
>RiskyUsers 및 UserRiskEvents에 대한 진단 설정은 현재 공개 미리 보기로 제공됩니다.

[ ![기존 구성을 표시하는 Azure AD의 진단 설정 화면](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png) ](./media/howto-export-risk-data/change-diagnostic-setting-in-portal.png#lightbox)

## <a name="log-analytics"></a>Log Analytics

Log Analytics를 사용하면 조직에서 기본 제공 쿼리 또는 사용자 지정 생성된 Kusto 쿼리를 사용하여 데이터를 쿼리할 수 있습니다. 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../../azure-monitor/logs/get-started-queries.md)을 참조하세요.

사용하도록 설정하고 나면 **Azure Portal** > **Azure AD** > **Log Analytics** 에서 Log Analytics에 액세스할 수 있습니다. Identity Protection 관리자가 가장 관심을 갖는 테이블은 **AADRiskyUsers** 및 **AADUserRiskEvents** 입니다.

- AADRiskyUsers - Identity Protection에서 **위험 사용자** 보고서와 같은 데이터를 제공합니다.
- AADUserRiskEvents - Identity Protection에서 **위험 감지** 보고서와 같은 데이터를 제공합니다.

[ ![상위 5개 이벤트를 보여 주는 AADUserRiskEvents 테이블에 대한 쿼리를 보여 주는 Log Analytics 보기](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png) ](./media/howto-export-risk-data/log-analytics-view-query-user-risk-events.png#lightbox)

위의 이미지에서는 가장 최근에 트리거된 5개의 위험 검색을 보여 주기 위해 다음과 같은 쿼리가 실행되었습니다. 

```kusto
AADUserRiskEvents
| take 5
```

또 다른 옵션은 AADRiskyUsers 테이블을 쿼리하여 위험 사용자를 모두 확인하는 것입니다.

```kusto
AADRiskyUsers
```

> [!NOTE]
> Log Analytics는 스트리밍되는 데이터에 대한 가시성만 갖습니다. Azure AD에서 이벤트 보내기를 사용하도록 설정하기 전의 이벤트는 표시되지 않습니다.

## <a name="storage-account"></a>스토리지 계정

Azure 스토리지 계정으로 로그를 라우팅하면 기본 보존 기간보다 더 오래 보존할 수 있습니다. 자세한 내용은 [자습서: Azure 스토리지 계정에 Azure AD 로그 보관](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)을 참조하세요.

## <a name="azure-event-hubs"></a>Azure Event Hubs

Azure Event Hubs는 Azure AD Identity Protection과 같은 원본에서 들어오는 데이터를 확인하고 실시간 분석과 상관 관계를 제공할 수 있습니다. 자세한 내용은 [자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)을 참조하세요.

## <a name="other-options"></a>기타 옵션

조직은 추가 처리를 위해 [Azure Sentinel에 Azure AD 데이터를 연결하도록](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection) 선택할 수 있습니다.

조직은 [Microsoft Graph API를 사용하여 프로그래밍 방식으로 위험 이벤트와 상호 작용](howto-identity-protection-graph-api.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 모니터링이란?](../reports-monitoring/overview-monitoring.md)
- [Azure Active Directory용 로그 분석 보기 설치 및 사용](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [Azure AD(Azure Active Directory) Identity Protection에서 데이터 연결](../../sentinel/data-connectors-reference.md#azure-active-directory-identity-protection)
- [Azure Active Directory Identity Protection과 Microsoft Graph PowerShell SDK](howto-identity-protection-graph-api.md)
- [자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)