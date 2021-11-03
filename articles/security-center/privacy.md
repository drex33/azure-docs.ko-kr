---
title: Microsoft Defender for Cloud의 사용자 데이터 관리 | Microsoft Docs
description: Microsoft Defender for Cloud에서 사용자 데이터를 관리 하는 방법에 대해 알아봅니다. 사용자 데이터 관리에는 데이터에 액세스하거나, 데이터를 삭제하거나, 내보내는 기능이 포함됩니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 69130a9d3804ffcf118dcd13aa2a40422fa01469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428371"
---
# <a name="manage-user-data-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud의 사용자 데이터 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft Defender for Cloud에서 사용자 데이터를 관리 하는 방법에 대 한 정보를 제공 합니다. 사용자 데이터 관리에는 데이터에 액세스하거나, 데이터를 삭제하거나, 내보내는 기능이 포함됩니다.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

클라우드 사용자에 대 한 Defender는 독자, 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 도구 내에서 고객 데이터에 액세스할 수 있습니다. 계정 관리자 역할에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하여 읽기 권한자, 소유자 및 참가자 역할에 대해 자세히 알아보세요. [Azure 구독 관리자](../cost-management-billing/manage/add-change-subscription-administrator.md)를 참조하세요.

## <a name="searching-for-and-identifying-personal-data"></a>개인 데이터에 대한 검색 및 식별
클라우드 사용자를 위한 Defender는 Azure Portal을 통해 자신의 개인 데이터를 볼 수 있습니다. 클라우드에 대 한 Defender는 전자 메일 주소 및 전화 번호와 같은 보안 연락처 세부 정보만 저장 합니다. 자세한 내용은 [Microsoft Defender For Cloud의 보안 연락처 세부 정보 제공](configure-email-notifications.md)을 참조 하세요.

Azure Portal에서 사용자는 클라우드의 just-in-time VM 액세스 기능에 대해 Defender를 사용 하 여 허용 된 IP 구성을 볼 수 있습니다. 자세한 내용은 [Just-In-Time을 사용하여 가상 머신 액세스 관리](just-in-time-access-usage.md)를 참조하세요.

Azure Portal에서 사용자는 IP 주소 및 공격자 세부 정보를 포함 하 여 클라우드에 대해 Defender에서 제공 하는 보안 경고를 볼 수 있습니다. 자세한 내용은 [Microsoft Defender For Cloud에서 보안 경고 관리 및 대응을](managing-and-responding-alerts.md)참조 하세요.

## <a name="classifying-personal-data"></a>개인 데이터 분류
클라우드의 보안 연락 기능에 대해 Defender에 있는 개인 데이터를 분류할 필요가 없습니다. 저장된 데이터는 이메일 주소(또는 여러 이메일 주소)와 전화 번호입니다. 클라우드 용 Defender에서 [연락처 데이터](configure-email-notifications.md) 의 유효성을 검사 합니다.

클라우드의 [just-in-time](just-in-time-access-usage.md) 기능에 대해 Defender에서 저장 한 IP 주소 및 포트 번호를 분류할 필요가 없습니다.

관리자 역할이 할당 된 사용자만 Defender for Cloud에서 [경고](managing-and-responding-alerts.md) 를 확인 하 여 개인 데이터를 분류할 수 있습니다.

## <a name="securing-and-controlling-access-to-personal-data"></a>개인 데이터에 대한 액세스 보안 및 제어
클라우드 사용자에 대 한 Defender는 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 [보안 연락처 데이터](configure-email-notifications.md)에 액세스할 수 있습니다.

클라우드 사용자에 대 한 Defender는 독자, 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 [just-in-time 정책에](just-in-time-access-usage.md) 액세스할 수 있습니다.

클라우드 사용자에 대 한 Defender는 사용자의 독자, 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 해당 [경고](managing-and-responding-alerts.md)를 볼 수 있습니다.

## <a name="updating-personal-data"></a>개인 데이터 업데이트
클라우드 사용자에 대 한 Defender는 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 Azure Portal 통해 [보안 연락처 데이터](configure-email-notifications.md) 를 업데이트할 수 있습니다.

클라우드 사용자에 대 한 Defender는 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 [just-in-time 정책을](just-in-time-access-usage.md)업데이트할 수 있습니다.

계정 관리자는 경고 인시던트를 편집할 수 없습니다. [경고 인시던트](managing-and-responding-alerts.md)는 보안 데이터로 간주되며 읽기 전용입니다.

## <a name="deleting-personal-data"></a>개인 데이터 삭제
클라우드 사용자의 Defender는 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 Azure Portal 통해 [보안 연락처 데이터](configure-email-notifications.md) 를 삭제할 수 있습니다.

클라우드 사용자의 Defender는 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 Azure Portal 통해 [just-in-time 정책을](just-in-time-access-usage.md) 삭제할 수 있습니다.

클라우드 사용자 용 Defender는 경고 인시던트를 삭제할 수 없습니다. 보안상의 이유로 [경고 인시던트](managing-and-responding-alerts.md) 는 읽기 전용 데이터로 간주됩니다.

## <a name="exporting-personal-data"></a>개인 데이터 내보내기
클라우드 사용자에 대 한 Defender는 독자, 소유자, 참가자 또는 계정 관리자의 역할을 할당 하 여 다음과 같은 방법으로 [보안 연락처 데이터](configure-email-notifications.md) 를 내보낼 수 있습니다.

- Azure Portal에서 복사
- Azure REST API 호출, GET HTTP 실행:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

계정 관리자 역할이 할당 된 클라우드 사용자의 Defender는 다음을 통해 IP 주소를 포함 하는 [just-in-time 정책을](just-in-time-access-usage.md) 내보낼 수 있습니다.

- Azure Portal에서 복사
- Azure REST API 호출, GET HTTP 실행:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

계정 관리자는 다음을 수행하여 경고 세부 정보를 내보낼 수 있습니다.

- Azure Portal에서 복사
- Azure REST API 호출, GET HTTP 실행:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

자세한 내용은 [보안 경고 가져오기(컬렉션 가져오기)](/previous-versions/azure/reference/mt704050(v=azure.100))를 참조하세요.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>동의 없이 프로파일링 또는 마케팅을 위해 개인 데이터 사용 제한
클라우드 사용자 용 Defender는 [보안 연락처 데이터](configure-email-notifications.md)를 삭제 하 여 옵트아웃 (opt out) 하도록 선택할 수 있습니다.

[Just-In-Time 데이터](just-in-time-access-usage.md)는 식별할 수 없는 데이터로 간주되고 30일 동안 유지됩니다.

[경고 데이터](managing-and-responding-alerts.md)는 보안 데이터로 간주되고 2년 동안 유지됩니다.

## <a name="auditing-and-reporting"></a>감사 및 보고
보안 연락처, Just-In-Time, 경고 업데이트의 감사 로그는 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에서 유지 관리됩니다.
