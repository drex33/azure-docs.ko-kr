---
title: 다단계 인증을 사용하여 조건부 액세스 정책 마이그레이션 - Azure Active Directory
description: 이 문서에서는 Azure Portal에서 다단계 인증이 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43c11ba308a05238fc8403340cb5437191f61560
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128568363"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Azure Portal에서 클래식 정책 마이그레이션

이 문서에서는 클라우드 앱에 대한 **다단계 인증** 이 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다. 필수 구성 요소는 아니지만 클래식 정책 마이그레이션을 시작하기 전에 [Azure Portal에서 클래식 정책 마이그레이션](policy-migration.md)을 읽어 보는 것이 좋습니다.

![Salesforce 앱에 대한 MFA를 요구하는 클래식 정책 세부 정보](./media/policy-migration/33.png)

마이그레이션 프로세스는 다음 단계로 구성됩니다.

1. [클래식 정책을 열어](#open-a-classic-policy) 구성 설정을 가져옵니다.
1. 클래식 정책을 대체할 새 Azure AD 조건부 액세스 정책을 만듭니다. 
1. 클래식 정책을 사용하지 않도록 설정합니다.

## <a name="open-a-classic-policy"></a>클래식 정책 열기

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. **클래식 정책** 을 선택합니다.

   ![클래식 정책 보기](./media/policy-migration-mfa/12.png)

1. 클래식 정책 목록에서 마이그레이션하려는 정책을 선택합니다. 새 조건부 액세스 정책을 사용하여 다시 만들 수 있도록 구성 설정을 문서화합니다.

## <a name="create-a-new-conditional-access-policy"></a>새 조건부 액세스 정책 만들기

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** > **보안** > **조건부 액세스** 로 이동합니다.
1. 새 조건부 액세스 정책을 만들려면 **새 정책** 을 선택합니다.
1. **새로 만들기** 페이지의 **이름** 텍스트 상자에서 정책 이름을 입력합니다.
1. **할당** 섹션에서 **사용자 및 그룹** 을 클릭합니다.
   1. 클래식 정책에서 모든 사용자를 선택한 경우 **모든 사용자** 를 클릭합니다. 
   1. 클래식 정책에서 그룹을 선택한 경우 **사용자 및 그룹 선택** 을 클릭한 다음 필요한 사용자 및 그룹을 선택합니다.
   1. 제외된 그룹이 있는 경우 **제외** 탭을 클릭한 다음 필요한 사용자 및 그룹을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **할당** 섹션에서 **클라우드 앱 또는 작업** 을 클릭합니다.
1. **클라우드 앱 또는 작업** 페이지에서 다음 단계를 수행합니다.
   1. **앱 선택** 을 클릭합니다.
   1. **선택** 을 클릭합니다.
   1. **선택** 페이지에서 클라우드 앱을 선택한 다음 **선택** 을 클릭합니다.
   1. **클라우드 앱** 페이지에서 **완료** 를 클릭합니다.
1. **다단계 인증 필요** 를 선택한 경우:
   1. **액세스 제어** 섹션에서 **허용** 을 클릭합니다.
   1. **허용** 페이지에서 **액세스 허용** 을 클릭하고 **다단계 인증 필요** 를 클릭합니다.
   1. **선택** 을 클릭합니다.
1. **켜기** 를 클릭하여 정책을 사용하도록 설정한 다음, **저장** 을 선택합니다.

   ![조건부 액세스 정책 만들기](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>클래식 정책을 사용하지 않도록 설정

클래식 정책을 사용하지 않도록 설정하려면 **세부 정보** 보기에서 **사용 안 함** 을 클릭하세요.

![클래식 정책 사용 안 함](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>다음 단계

- 클래식 정책 마이그레이션에 대한 자세한 내용은 [Azure Portal에서 클래식 정책 마이그레이션](policy-migration.md)을 참조하세요.
- [조건부 액세스에 보고 전용 모드를 사용하여 새 정책 결정의 영향을 확인합니다.](concept-conditional-access-report-only.md)