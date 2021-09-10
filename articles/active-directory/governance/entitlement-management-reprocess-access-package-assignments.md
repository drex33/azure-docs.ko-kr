---
title: Azure AD 자격 관리에서 액세스 패키지에 대한 할당 다시 처리 - Azure Active Directory
description: Azure Active Directory 자격 관리에서 액세스 패키지에 대한 할당을 다시 처리하는 방법을 알아봅니다.
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
ms.openlocfilehash: b1b488f8c9331932b82ab0ab55db9c7dcb652add
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129474"
---
# <a name="reprocess-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대한 할당 다시 처리

액세스 패키지 관리자는 재처리 기능을 사용하여 액세스 패키지에서 사용자의 원래 할당을 자동으로 재평가하고 시행할 수 있습니다. 재처리를 사용하면 권한 관리 외부의 변경 사항으로 인해 리소스에 대한 액세스가 영향을 받은 경우 사용자가 액세스 패키지 요청 프로세스를 반복할 필요가 없습니다.

예를 들어, 사용자가 그룹에서 수동으로 제거되어 해당 사용자가 필요한 리소스에 액세스할 수 없게 되었을 수 있습니다. 

권한 관리는 액세스 패키지의 리소스에 대한 외부 업데이트를 차단하지 않으므로 권한 관리 UI는 이 변경 사항을 정확하게 표시하지 않습니다. 따라서 사용자가 더 이상 리소스에 액세스할 수 없는 경우에도 사용자의 할당 상태가 ‘전달됨’으로 표시됩니다. 그러나 사용자의 할당이 다시 처리되면 액세스 패키지의 리소스에 다시 추가됩니다. 재처리를 통해 액세스 패키지 할당이 최신 상태이고 사용자가 필요한 리소스에 액세스할 수 있으며 할당이 UI에 정확하게 반영되도록 합니다.

이 문서에서는 기존 액세스 패키지에서 할당을 다시 처리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 권한 관리를 사용하고 액세스 패키지에 사용자를 할당하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

## <a name="open-an-existing-access-package-and-reprocess-user-assignments"></a>기존 액세스 패키지 열기 및 사용자 할당 재처리

**필수 역할** 은 글로벌 관리자, ID 거버넌스 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

‘전달됨’ 상태에 있지만 액세스 패키지의 일부인 리소스에 대한 액세스 권한이 없는 사용자가 있는 경우 해당 사용자를 액세스 패키지의 리소스에 재할당하기 위해 할당을 다시 처리해야 할 수 있습니다. 기존 액세스 패키지에 대한 할당을 재처리하려면 다음 단계를 따르십시오.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

1.  **Azure Active Directory** 를 클릭한 다음 **Identity Governance** 를 클릭합니다.

1.  왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 재처리할 사용자 할당이 있는 액세스 패키지를 엽니다.

1.  왼쪽의 **관리** 아래에서 **할당** 을 클릭합니다.

    ![Azure Portal에서 권한 관리](./media/entitlement-management-reprocess-access-package-assignments/reprocess-access-package-assignment.png)

1.  할당을 재처리하려는 모든 사용자를 선택합니다.

1.  **재처리** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지에 대한 할당 보기, 추가, 제거](entitlement-management-access-package-assignments.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
