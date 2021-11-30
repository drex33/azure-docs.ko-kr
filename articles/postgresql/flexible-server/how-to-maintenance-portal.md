---
title: Azure Database for PostgreSQL - 유연한 서버 - 예약된 유지 관리 - Azure Portal
description: Azure Portal에서 Azure Database for PostgreSQL - 유연한 서버의 예약된 유지 관리 설정을 구성하는 방법을 알아봅니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: 396490c615ab32f1526b17f61485d296c6b6c656
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266299"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 예약된 유지 관리 설정 관리
 
Azure 구독의 각 유연한 서버에 대해 유지 관리 옵션을 지정할 수 있습니다. 옵션에는 예정된 유지 관리 이벤트와 완료된 유지 관리 이벤트에 대한 유지 관리 일정 및 알림 설정이 포함됩니다.



## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL - 유연한 서버](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>유지 관리 일정 옵션 지정
 
1. PostgreSQL 서버 페이지의 **설정** 제목 아래에서 **유지 관리** 를 선택하여 예약된 유지 관리 옵션을 엽니다.
2. 기본(시스템 관리) 일정은 임의 요일과 60분 유지 관리 기간 시작 시간(현지 서버 시간으로 오후 11시에서 오전 7시 사이)입니다. 이 일정을 사용자 지정하려면 **사용자 지정 일정** 을 선택합니다. 그런 다음, 원하는 요일과 60분 유지 관리 기간 시작 시간을 선택할 수 있습니다.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>예약된 유지 관리 이벤트에 대한 알림
 
Azure Service Health를 사용하여 유연한 서버에서 임박한 예약된 유지 관리와 수행한 예약된 유지 관리에 대한 [알림을 확인](../../service-health/service-notifications.md)할 수 있습니다. Azure Service Health에서 경고를 [설정](../../service-health/resource-health-alert-monitor-guide.md)하여 유지 관리 이벤트에 대한 알림을 받을 수도 있습니다.
 
## <a name="next-steps"></a>다음 단계  
 
* [Azure Database for PostgreSQL - 유연한 서버의 예약된 유지 관리](concepts-maintenance.md)에 대해 알아봅니다.
* [Azure Service Health](../../service-health/overview.md)에 대해 알아봅니다.
