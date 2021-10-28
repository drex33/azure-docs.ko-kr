---
title: PIM(Privileged Identity Management)의 이메일 알림 - Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)의 이메일 알림에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9cd2b72b58899ce37843d6117f975f74ef97493
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667329"
---
# <a name="email-notifications-in-pim"></a>PIM에서 이메일 알림

PIM(Privileged Identity Management)을 사용하면 역할이 할당되거나 활성화되는 경우와 같이 Azure AD(Azure Active Directory) 조직에서 중요한 이벤트가 발생할 때 알 수 있습니다. Privileged Identity Management는 사용자 및 다른 참가자에게 이메일 알림을 보내 계속해서 정보를 제공합니다. 이러한 이메일에는 역할 활성화 또는 갱신과 같은 관련 작업에 대한 링크도 포함될 수 있습니다. 이 문서에서는 이러한 이메일이 어떻게 구성되고 언제 전송되며 누가 이메일을 받는지에 대해 설명합니다.

## <a name="sender-email-address-and-subject-line"></a>보낸 사람 이메일 주소 및 제목 줄

Azure AD 및 Azure 리소스 역할 모두에 대해 Privileged Identity Management에서 보낸 이메일은 다음과 같은 보낸 사람 이메일 주소를 포함합니다.

- 이메일 주소:  **azure-noreply\@microsoft.com**
- 표시 이름: Microsoft Azure

이러한 이메일은 제목 줄에 **PIM** 접두사를 포함합니다. 예를 들면 다음과 같습니다.

- PIM: Alain Charon에게는 Backup 읽기 권한자 역할이 할당되었습니다.

## <a name="email-timing-for-activation-approvals"></a>활성화 승인을 위한 이메일 시간

사용자가 역할을 활성화하고 역할 설정에 승인이 필요한 경우 승인자는 각 승인에 대해 두 개의 이메일을 받게 됩니다.

- 사용자의 활성화 요청 승인 또는 거부 요청(요청 승인 엔진에서 전송)
- 사용자의 요청이 승인됨(요청 승인 엔진에서 전송)

또한 전역 관리자 및 권한 있는 역할 관리자는 각 승인에 대한 이메일을 받습니다.

- 사용자 역할이 활성화됨(Privileged Identity Management에서 전송)

요청 승인 엔진에서 보낸 처음 두 개의 이메일은 지연될 수 있습니다. 현재 이메일의 90%는 3~10분이 소요되지만 1% 고객의 경우 최대 15분까지 훨씬 더 오래 걸릴 수 있습니다.

첫 번째 이메일이 전송되기 전에 Azure Portal에서 승인 요청이 승인되면 첫 번째 이메일이 더 이상 트리거되지 않으며 다른 승인자에게 승인 요청 이메일 알림이 전송되지 않습니다. 이메일을 받지 못한 것처럼 보일 수 있지만 이는 예상된 동작입니다.

## <a name="notifications-for-azure-ad-roles"></a>Azure AD 역할에 대한 알림

Azure AD 역할에 대해 다음 이벤트가 발생하면 Privileged Identity Management가 이메일을 보냅니다.

- 권한 있는 역할 활성화가 승인 보류 중인 경우
- 권한 있는 역할 활성화 요청이 완료된 경우
- Azure AD Privileged Identity Management가 사용하도록 설정된 경우

Azure AD 역할에 대한 이러한 이메일을 받는 사람은 역할, 이벤트 및 알림 설정에 따라 달라집니다.

| 사용자 | 역할 활성화가 승인 보류 중임 | 역할 활성화 요청이 완료됨 | PIM이 사용하도록 설정됨 |
| --- | --- | --- | --- |
| 권한 있는 역할 관리자</br>(활성화/적격) | 예</br>(명시적인 승인자가 지정되지 않은 경우만) | 예* | 예 |
| 보안 관리자</br>(활성화/적격) | 예 | 예* | 예 |
| 전역 관리자</br>(활성화/적격) | 예 | 예* | 예 |

\*[**알림** 설정](pim-how-to-change-default-settings.md)이 **사용** 으로 설정된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure AD 역할을 활성화할 때 전송되는 예제 이메일을 보여줍니다.

![Azure AD 역할에 대한 새로운 Privileged Identity Management 이메일](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD 역할에 대한 주간 Privileged Identity Management 다이제스트 이메일

Azure AD 역할에 대한 주간 Privileged Identity Management 요약 이메일은 Privileged Identity Management를 사용하도록 설정된 권한 있는 역할 관리자, 보안 관리자 및 글로벌 관리자에게 전송됩니다. 이 주간 이메일은 권한 있는 역할 할당뿐만 아니라 해당 주의 Privileged Identity Management 활동에 대한 스냅샷도 제공합니다. 퍼블릭 클라우드의 Azure AD 조직에만 제공됩니다. 예를 들면 다음과 같습니다.

![Azure AD 역할에 대한 주간 Privileged Identity Management 다이제스트 이메일](./media/pim-email-notifications/email-directory-weekly.png)

이메일에는 다음이 포함됩니다.

| Tile | 설명 |
| --- | --- |
| **Users activated(사용자가 활성화함)** | 사용자가 조직 내부에서 적격 역할을 활성화한 횟수입니다. |
| **Users made permanent(사용자 영구 지정)** | 적격 할당이 있는 사용자가 영구 지정된 횟수입니다. |
| **Privileged Identity Management의 역할 할당** | Privileged Identity Management 내에서 사용자에게 적합한 역할이 할당된 횟수입니다. |
| **Role assignments outside of PIM(PIM 외부 역할 할당)** | 사용자에게 Privileged Identity Management 외부(Azure AD 내부)의 영구 역할이 할당된 횟수입니다. 이 경고와 함께 제공되는 이메일은 경고 설정을 열어 사용 또는 사용하지 않도록 설정할 수 있습니다. |

**상위 역할 개요** 섹션에는 각 역할에 대한 총 영구 및 적격 관리자 수를 기준으로 조직에 있는 상위 5개의 역할이 나열됩니다. **작업 수행** 링크를 클릭하면 [검색 및 인사이트](pim-security-wizard.md)가 열리며 여기서 영구 관리자를 적격 관리자로 일괄 변환할 수 있습니다.

## <a name="notifications-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 알림

Privileged Identity Management는 Azure 리소스 역할에 대해 다음 이벤트가 발생할 때 소유자 및 사용자 액세스 관리자에게 이메일을 보냅니다.

- 역할 할당을 승인 보류 중인 경우
- 역할이 할당된 경우
- 역할이 곧 만료되는 경우
- 역할을 확장할 수 있는 경우
- 최종 사용자가 역할을 갱신하는 경우
- 역할 활성화 요청이 완료된 경우

Privileged Identity Management는 Azure 리소스 역할에 대해 다음 이벤트가 발생할 때 최종 사용자에게 이메일을 보냅니다.

- 사용자에게 역할이 할당된 경우
- 사용자의 역할이 만료된 경우
- 사용자의 역할이 확장된 경우
- 사용자의 역할 활성화 요청이 완료된 경우

다음에서는 사용자가 가상의 Contoso 조직에 대한 Azure 리소스 역할을 할당받을 때 전송되는 예제 이메일을 보여 줍니다.

![Azure 리소스 역할에 대한 새로운 Privileged Identity Management 이메일](./media/pim-email-notifications/email-resources-new.png)

## <a name="notifications-for-privileged-access-groups"></a>권한 있는 액세스 그룹에 대한 알림

Privileged Identity Management는 권한 있는 액세스 그룹 할당에 다음 이벤트가 발생하는 경우 소유자에게만 이메일을 보냅니다.

- 소유자 또는 구성원 역할 할당이 승인 보류 중인 경우
- 소유자 또는 구성원 역할이 할당된 경우
- 소유자 또는 구성원 역할이 곧 만료되는 경우
- 소유자 또는 구성원 역할을 확장할 수 있는 경우
- 최종 사용자가 소유자 또는 구성원 역할을 갱신하는 경우
- 소유자 또는 구성원 역할 활성화 요청이 완료된 경우

Privileged Identity Management는 권한 있는 액세스 그룹 역할 할당에 다음 이벤트가 발생하는 경우 최종 사용자에게 이메일을 보냅니다.

- 소유자 또는 구성원 역할이 사용자에게 할당된 경우
- 사용자의 소유자 또는 구성원 역할이 만료된 경우
- 사용자의 소유자 또는 구성원 역할이 확장된 경우
- 사용자의 소유자 또는 구성원 역할 활성화 요청이 완료된 경우


## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure AD 역할에 대한 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md)
