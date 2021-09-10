---
title: Azure AD 자격 관리에서 액세스 패키지에 대한 요청 다시 처리 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 요청을 보는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a85d796f8b66cca16d4d3c01ecbfc5f6c43c79f9
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129467"
---
# <a name="reprocess-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대한 요청 다시 처리

액세스 패키지 관리자는 재처리 기능을 사용하여 액세스 패키지에 대한 사용자의 액세스 요청을 언제든지 자동으로 재시도할 수 있습니다. 재처리를 사용하면 리소스에 대한 액세스가 성공적으로 프로비저닝되지 않은 경우 사용자가 액세스 패키지 요청 프로세스를 반복할 필요가 없습니다.

> [!NOTE]
> 원래 요청이 완료된 시점부터 최대 14일 동안 요청을 재처리할 수 있습니다. 14 일 이전에 완료 된 요청의 경우 사용자는 MyAccess에서 취소 하 고 새 요청을 만들어야 합니다.

이 문서에서는 기존 액세스 패키지에 대한 요청 설정을 변경하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 권한 관리를 사용하고 액세스 패키지에 사용자를 할당하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

## <a name="open-an-existing-access-package-and-reprocess-user-requests"></a>기존 액세스 패키지를 열고 사용자 요청을 다시 처리 합니다.

**필수 역할** 은 글로벌 관리자, ID 거버넌스 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

요청이 ‘부분적으로 전달 됨’ 또는 ‘실패’ 상태에 있는 사용자 집합이 있는 경우 해당 요청 중 일부를 다시 처리 해야 할 수 있습니다. 기존 액세스 패키지에 대 한 요청을 다시 처리 하려면 다음 단계를 수행 합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

1.  **Azure Active Directory** 를 클릭한 다음 **Identity Governance** 를 클릭합니다.

1.  왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1.  왼쪽의 **관리** 아래에서 **요청** 을 클릭합니다.

1.  요청을 재처리하려는 모든 사용자를 선택하십시오.

1.  **재처리** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 요청 보기](entitlement-management-access-package-requests.md)
- [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md)