---
title: 관리되지 않는 Azure AD 조직에서 회사 또는 학교 계정 사용 중지
description: 관리되지 않는 Azure Active Directory에서 회사 또는 학교 계정을 사용 중지하는 방법.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 05/04/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14a2dea0445a866f7c352eedf64d631d83b1b0f0
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864410"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>관리되지 않는 Azure AD 조직에서 회사 또는 학교 계정 사용 중지

관리되지 않는 Azure AD(Azure Active Directory) 조직의 사용자이고 더 이상 해당 조직의 앱을 사용하거나 연결을 유지할 필요가 없는 경우 언제든지 계정을 사용 중지할 수 있습니다. 관리되지 않는 조직에 전역 관리자가 없습니다. 관리되지 않는 조직의 사용자는 관리자에게 문의하지 않고도 계정을 직접 사용 중지할 수 있습니다.

관리되지 않는 조직의 사용자가 셀프 서비스 등록 중에 생성되는 경우가 많습니다. 무료 서비스에 등록한 조직의 정보 근로자를 예로 들 수 있습니다. 셀프 서비스 등록에 대한 자세한 내용은 [Microsoft Azure Active Directory의 셀프 서비스 등록이란?](directory-self-service-signup.md)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>시작하기 전에

계정을 사용 중지하려면 먼저 다음 항목을 확인해야 합니다.

* 관리되지 않는 Azure AD 조직의 사용자인지 확인합니다. 관리되는 조직에 속하는 경우 계정을 사용 중지할 수 없습니다. 관리되는 조직에 속하고 계정을 사용 중지하려면 관리자에게 문의해야 합니다. 관리되지 않는 조직에 속해 있는지 여부를 확인하는 방법에 대한 자세한 내용은 [관리되지 않는 테넌트에서 사용자 삭제](/power-automate/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)를 참조하세요.

* 유지할 데이터를 저장합니다. 내보내기 요청을 제출하는 방법에 대한 자세한 내용은 [관리되지 않는 테넌트에 대한 시스템 생성 로그 액세스 및 내보내기](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)를 참조하세요.

> [!WARNING]
> 계정 사용 중지는 취소할 수 없습니다. 계정을 사용 중지하면 모든 개인 데이터가 제거됩니다. 계정 및 계정과 연결된 데이터에 더 이상 액세스할 수 없습니다.

## <a name="close-your-account"></a>계정 닫기

관리되지 않는 회사 또는 학교 계정을 사용 중지하려면 다음 단계를 수행합니다.

1. [계정을 사용 중지하려면](https://portal.azure.com/#blade/Microsoft_AAD_IAM/PrivacyDataRequests) 해당 계정으로 로그인합니다.

1. **내 데이터 요청** 에서 **계정 사용 중지** 를 선택합니다.

    ![내 데이터 요청 - 계정 사용 중지](./media/users-close-account/close-account.png)

1. 확인 메시지를 검토하고 **예** 를 선택합니다.

    ![내 데이터 요청 - 닫기 확인](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory의 셀프 서비스 등록이란?](directory-self-service-signup.md)
- [관리되지 않는 테넌트에서 사용자 삭제](/power-automate/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [관리되지 않는 테넌트에 대한 시스템 생성 로그 액세스 및 내보내기](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)