---
title: 계획된 유지 관리 이벤트에 대한 고급 알림(미리 보기)
description: Azure SQL Database에 대한 계획된 유지 관리 전에 알림을 받습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: scott-kim-sql
ms.author: scottkim
ms.reviewer: mathoma, wiassaf
ms.date: 09/14/2021
ms.openlocfilehash: 89a3f86995d054573897202d2269e4f1f38821e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631194"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>계획된 유지 관리 이벤트에 대한 고급 알림(미리 보기)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

기본값이 아닌 유지 관리 기간(미리 보기)을 사용하도록 구성된 데이터베이스에 대해 고급 [알림(미리 보기)을](maintenance-window.md)사용할 수 있습니다. 사전 알림을 통해 고객은 예정된 이벤트보다 최대 24시간 전에 알림을 전송하도록 구성할 수 있습니다.

향후 24시간 내에 계획된 유지 관리가 시작될 예정인 경우 텍스트, 이메일, Azure 푸시 알림 및 음성 메일을 가져오도록 알림을 구성할 수 있습니다. 추가 알림은 유지 관리가 시작될 때와 종료될 때 전송됩니다.

**시스템 기본** 유지 관리 기간 옵션에 대해 고급 알림을 구성할 수 없습니다. **시스템 기본값** 이외의 유지 관리 기간을 선택하여 고급 알림을 구성하고 사용하도록 설정합니다.

> [!NOTE]
> Azure SQL Managed Instance에는 유지 관리 기간을 선택하는 기능을 사용할 수 있지만 현재 Azure SQL Managed Instance에는 사전 알림을 사용할 수 없습니다. 

## <a name="create-an-advance-notification"></a>사전 알림 만들기

유지 관리 기간이 구성된 Azure SQL 데이터베이스에 대해 고급 알림을 사용할 수 있습니다. 

알림을 사용하도록 설정하려면 다음 단계를 완료합니다.  

1. [계획된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance) 페이지로 이동하고 **상태 경고**, **서비스 상태 경고 추가** 를 차례로 선택합니다.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="새 상태 경고 만들기 메뉴 옵션":::

2. **작업** 섹션에서 **작업 그룹 추가** 를 선택합니다. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="작업 그룹 추가 메뉴 옵션":::

3. **작업 그룹 만들기** 양식을 완료하고 **다음: 알림** 을 선택합니다.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="작업 그룹 양식 만들기":::

1. **알림** 탭에서 **알림 유형** 을 선택합니다. **이메일/SMS 메시지/푸시/음성** 옵션은 가장 뛰어난 유연성을 제공하며 권장되는 옵션입니다. 알림을 구성하려면 펜을 선택합니다.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="알림 구성":::



   1. 열리는 *알림 추가 또는 편집* 양식을 완료하고 **확인** 을 선택합니다. 

   2. 작업 및 태그는 선택 사항입니다. 여기에서 트리거할 추가 작업을 구성하거나 태그를 사용하여 Azure 리소스를 범주화하고 구성할 수 있습니다. 

   4. **검토 + 만들기** 탭의 세부 정보를 확인하고 **만들기** 를 선택합니다. 

7. 만들기를 선택하면 경고 규칙 구성 화면이 열리고 작업 그룹이 선택됩니다. 새 경고 규칙에 이름을 지정하고, 리소스 그룹을 선택하고, **경고 규칙 만들기** 를 선택합니다. 

8. **상태 경고** 메뉴 항목을 다시 클릭하면 경고 목록에 새 경고가 포함됩니다. 


모든 설정을 완료했습니다. 다음에 계획된 Azure SQL 유지 관리 이벤트가 있으면 미리 알림이 표시됩니다.

## <a name="receiving-notifications"></a>알림 수신

다음 표에서는 받을 수 있는 일반 정보 알림을 보여 줍니다. 

|상태|Description|
|:---|:---|
|**계획된 배포**| 유지 관리 이벤트 24시간 전에 수신됩니다. 유지 관리는 DB xyz에 대해 오후 5시-오전 8시(현지 시간) 사이의 날짜에 계획됩니다.|
|**진행 중** | 데이터베이스 ‘xyz’의 유지 관리가 *xyz* 시작되고 있습니다.| 
|**완료** | *xyz* 데이터베이스의 유지 관리가 완료되었습니다. |

다음 표에서는 유지 관리가 진행되는 동안 전송될 수 있는 추가 알림을 보여 줍니다. 

|상태|Description|
|:---|:---|
|**확장** | 유지 관리가 진행 중이지만 ‘xyz’ 데이터베이스에 대해 완료되지 않았습니다. 유지 관리는 다음 유지 관리 기간에 계속됩니다.| 
|**Canceled**| ‘xyz’ 데이터베이스에 대한 유지 관리가 취소되고 나중에 다시 예약됩니다. |
|**차단**|‘xyz’ 데이터베이스를 유지 관리하는 동안 문제가 발생했습니다. 다시 시작할 때 알려 드립니다.| 
|**다시 시작됨**|문제가 해결되었으며 다음 유지 관리 기간에 유지 관리를 계속합니다.|


## <a name="next-steps"></a>다음 단계

- [유지 관리 기간](maintenance-window.md)
- [유지 관리 기간 FAQ](maintenance-window-faq.yml)
- [Microsoft Azure의 경고 개요](../../azure-monitor/alerts/alerts-overview.md)
- [Azure Resource Manager 역할에 메일 보내기](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)
