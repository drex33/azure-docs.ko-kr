---
title: Privileged Identity Management를 사용하기 위한 라이선스 요구 사항 - Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 사용하기 위한 라이선스 요구 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6570e378aa75843c525cb21ed58c9da5fd4fb574
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531182"
---
# <a name="license-requirements-to-use-privileged-identity-management"></a>Privileged Identity Management를 사용하기 위한 라이선스 요구 사항

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하려면 디렉터리에 유효한 라이선스가 있어야 합니다. 또한 관리자 및 해당 사용자에게 라이선스를 할당해야 합니다. 이 문서에서는 Privileged Identity Management를 사용하기 위한 라이선스 요구 사항을 설명합니다.

## <a name="valid-licenses"></a>유효한 라이선스

PIM 및 PIM의 모든 설정을 사용하려면 Azure AD 라이선스가 필요합니다. 현재 테넌트에서 활성화된 Azure Active Directory Premium P2 버전을 사용하여 Azure AD 및 Azure 리소스 역할(미리 보기)에 액세스할 수 있는 서비스 주체에 대한 액세스 검토의 범위를 지정할 수 있습니다. 서비스 주체에 대한 라이선스 모델은 이 기능의 일반 공급용으로 최종 결정되며 추가 라이선스가 필요할 수 있습니다. [!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="licenses-you-must-have"></a>보유해야 하는 라이선스

디렉터리에 다음 작업을 수행할 직원 수만큼 Azure AD Premium P2 라이선스가 있어야 합니다.

- PIM을 사용하여 관리되는 Azure AD 또는 Azure 역할에 적격하게 할당된 사용자
- 적격 구성원 또는 권한 있는 액세스 그룹의 소유자로 할당된 사용자
- PIM에서 활성화 요청을 승인하거나 거부할 수 있는 사용자
- 액세스 검토에 할당된 사용자
- 액세스 검토를 수행하는 사용자

Azure AD Premium P2 라이선스가 필요하지 **않은** 작업은 다음과 같습니다.

- PIM을 설정하고, 정책을 구성하고, 경고를 수신하고, 액세스 검토를 설정하는 사용자에게는 라이선스가 필요하지 않습니다.

라이선스에 대한 자세한 내용은 [Azure Active Directory 포털을 사용하여 라이선스 할당 또는 제거](../fundamentals/license-users-groups.md)를 참조하세요.

## <a name="example-license-scenarios"></a>라이선스 시나리오 예

필요한 라이선스 수를 결정하는 데 도움이 되는 몇 가지 라이선스 시나리오 예는 다음과 같습니다.

| 시나리오 | 계산 | 라이선스 수 |
| --- | --- | --- |
| Woodgrove Bank에는 PIM을 구성하고 관리하는 2명의 전역 관리자와 서로 다른 부서의 관리자 10명이 있습니다. 5명의 관리자를 적격하게 만듭니다. | 적격한 관리자를 위한 5개의 라이선스 | 5 |
| 그래픽 디자인 협회에는 25명의 관리자가 있으며, 이 중 14명은 PIM을 통해 관리됩니다. 역할 활성화에는 승인이 필요하며, 조직에는 활성화를 승인할 수 있는 세 명의 사용자가 있습니다. | 적격 역할에 대한 14개의 라이선스 + 3명의 승인자 | 17 |
| Contoso에는 50명의 관리자가 있으며, 이 중 42명은 PIM을 통해 관리됩니다. 역할 활성화에는 승인이 필요하며, 조직에는 활성화를 승인할 수 있는 다섯 명의 사용자가 있습니다. 또한 Contoso는 관리자 역할에 할당된 사용자에 대한 월별 검토를 수행하며 검토자는 사용자의 관리자이며, 여섯 명은 PIM에서 관리하는 관리자 역할에 속하지 않습니다. | 적격 역할의 라이선스 42개 + 5명의 승인자 + 6명의 검토자 | 53 |

## <a name="when-a-license-expires"></a>라이선스가 만료되는 경우

Azure AD Premium P2, EMS E5 또는 평가판 라이선스가 만료되면 디렉터리에서 Privileged Identity Management 기능을 더 이상 사용할 수 없습니다.

- Azure AD 역할에 대한 영구 역할 할당은 영향을 받지 않습니다.
- 사용자가 권한 있는 역할을 활성화하거나, 권한 있는 액세스를 관리하거나, 권한 있는 역할의 액세스 검토를 수행하기 위해 더 이상 Azure Portal의 Privileged Identity Management 서비스와 Privileged Identity Management의 Graph API cmdlet 및 PowerShell 인터페이스를 사용할 수 없습니다.
- 사용자가 더 이상 권한 있는 역할을 활성화할 수 없으므로 Azure AD 역할의 적격 역할 할당이 제거됩니다.
- Azure AD 역할에 대해 진행 중인 모든 액세스 검토가 종료되고 Privileged Identity Management 구성 설정이 제거됩니다.
- Privileged Identity Management는 더 이상 역할 할당 변경에 대한 이메일을 전송하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 배포](pim-deployment-plan.md)
- [Privileged Identity Management 사용 시작](pim-getting-started.md)
- [Privileged Identity Management에서 관리할 수 없는 역할](pim-roles.md)
- [PIM에서 Azure 리소스 역할에 대한 액세스 검토 만들기](pim-resource-roles-start-access-review.md)
- [PIM에서 Azure AD 역할에 대한 액세스 검토 만들기](pim-how-to-start-security-review.md)
