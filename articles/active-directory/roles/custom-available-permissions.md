---
title: 앱 등록에 대한 사용자 지정 역할 권한 - Azure AD | Microsoft Docs
description: 앱 등록을 관리하기 위한 사용자 지정 관리자 역할 권한을 위임합니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a2d152e18f039b88957a0dab32f5d451642d6bd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058717"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory의 사용자 지정 역할에 대한 애플리케이션 등록 권한

이 문서에는 Azure AD(Azure Active Directory)의 사용자 지정 역할 정의에 대해 현재 사용할 수 있는 앱 등록 권한이 포함되어 있습니다.

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="permissions-for-managing-single-tenant-applications"></a>단일 테넌트 애플리케이션 관리를 위한 권한

사용자 지정 역할에 대한 권한을 선택할 때 단일 테넌트 애플리케이션만 관리하기 위한 액세스 권한을 부여할 수 있는 옵션이 있습니다. 단일 테넌트 애플리케이션은 애플리케이션이 등록된 Azure AD 조직의 사용자만 사용할 수 있습니다. 단일 테넌트 애플리케이션은 **지원되는 계정 유형** 이 “이 조직 디렉터리의 계정만”으로 설정된 것으로 정의됩니다. Graph API에서 단일 테넌트 애플리케이션은 signInAudience 속성을 “AzureADMyOrg”로 설정합니다.

단일 테넌트 애플리케이션만 관리하기 위한 액세스 권한을 부여하려면 하위 유형 **applications.myOrganization** 에서 아래 권한을 사용합니다. 예를 들면 microsoft.directory/applications.myOrganization/basic/update와 같습니다.

일반 용어 하위 유형, 권한 및 속성 집합의 의미에 대한 설명은 [사용자 지정 역할 개요](custom-overview.md)를 참조하세요. 다음 정보는 애플리케이션 등록에만 적용됩니다.

## <a name="create-and-delete"></a>만들기 및 삭제

애플리케이션 등록을 만들 수 있는 기능을 부여하는 데 사용할 수 있는 두 가지 권한은 각기 다른 동작입니다.

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

이 권한을 할당하면 생성된 앱 등록의 첫 번째 소유자로 작성자가 추가되고 생성된 앱 등록은 작성자의 250개 생성된 개체 할당량에 대해 계산됩니다.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

이 권한을 할당하면 생성된 앱 등록의 첫 번째 소유자로 작성자가 추가되지 않으며 생성된 앱 등록이 작성자의 250개 생성된 개체 할당량에 대해 계산되지 않습니다. 디렉터리 수준 할당량에 도달할 때까지는 담당자가 앱 등록 생성을 막을 수 없으므로 이 권한을 신중하게 사용하세요.

두 권한이 모두 할당된 경우 /create 권한이 우선으로 적용됩니다. /createAsOwner 권한은 만든 이를 자동으로 첫 번째 소유자로 추가하지 않지만, Graph API 또는 PowerShell cmdlet을 사용하는 경우 앱 등록을 만드는 동안 소유자를 지정할 수 있습니다.

**새 등록** 명령에 대한 권한 부여 액세스를 만듭니다.

[해당 권한은 새 등록 포털 명령에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/new-custom-role.png)

앱 등록을 삭제할 수 있는 기능을 부여하는 데 사용할 수 있는 두 가지 권한이 있습니다.

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

하위 유형과 관계없이 앱 등록을 삭제할 수 있는 기능을 부여합니다. 즉, 단일 테넌트 및 다중 테넌트 애플리케이션에 모두 적용됩니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

조직 또는 단일 테넌트 애플리케이션(myOrganization 하위 유형)의 계정에만 액세스할 수 있는 앱 등록만을 삭제할 수 있는 기능을 부여합니다.

![해당 권한은 앱 등록 삭제 명령에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> 만들기 권한이 포함된 역할을 할당하는 경우 역할 할당은 디렉터리 범위에서 수행되어야 합니다. 리소스 범위에서 만들기 권한을 할당하면 앱 등록을 만드는 기능이 부여되지 않습니다.

## <a name="read"></a>읽기

조직의 모든 구성원 사용자가 기본적으로 앱 등록 정보를 읽을 수 있습니다. 그러나 게스트 사용자 및 애플리케이션 서비스 사용자는 앱 등록 정보를 읽을 수 없습니다. 게스트 사용자 또는 애플리케이션에 역할을 할당하려면 적절한 읽기 권한을 포함해야 합니다.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

자격 증명과 같은 상황에서 읽을 수 없는 속성의 외부에서 단일 테넌트 및 다중 테넌트 애플리케이션의 모든 속성을 읽을 수 있는 기능입니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/allProperties/read와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

단일 테넌트 및 다중 테넌트 애플리케이션에서 소유자 속성을 읽을 수 있는 기능을 부여합니다. 애플리케이션 등록 소유자 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 소유자 페이지에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

표준 애플리케이션 등록 속성을 읽을 수 있는 액세스 권한을 부여합니다. 여기에는 애플리케이션 등록 페이지 전반의 속성이 포함됩니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/standard/read와 동일한 권한을 부여합니다.

## <a name="update"></a>업데이트

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

단일 테넌트 및 다중 테넌트 애플리케이션에서 모든 속성을 업데이트할 수 있는 기능입니다.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/allProperties/update와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

단일 테넌트 및 다중 테넌트 애플리케이션에서 지원되는 계정 유형(signInAudience) 속성을 업데이트하는 기능입니다.

![이 권한은 인증 페이지에서 앱 등록이 지원되는 계정 유형 속성에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/audience/update와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

단일 테넌트 및 다중 테넌트 애플리케이션에서 회신 URL, 로그아웃 URL, 암시적 흐름 및 게시자 도메인 속성을 업데이트하는 기능입니다. 지원되는 계정 유형을 제외한 애플리케이션 등록 인증 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![앱 등록 인증에 대한 액세스 권한을 부여하지만 지원되는 계정 유형에 대한 액세스 권한은 부여하지 않습니다.](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/authentication/update와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

단일 테넌트 및 다중 테넌트 애플리케이션의 이름, 로고, 홈페이지 URL, 서비스 약관 URL 및 개인정보처리방침 URL 속성을 업데이트하는 기능입니다. 애플리케이션 등록 브랜딩 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 브랜딩 페이지에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/basic/update와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

단일 테넌트 및 다중 테넌트 애플리케이션에서 인증서 및 클라이언트 암호 속성을 업데이트할 수 있는 기능입니다. 애플리케이션 등록 인증서 및 암호 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 인증서 및 암호 페이지에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/credentials/update와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

단일 테넌트 및 다중 테넌트의 소유자 속성을 업데이트하는 기능입니다. 애플리케이션 등록 소유자 페이지의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 소유자 페이지에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/owners/update와 동일한 권한을 부여합니다.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

단일 테넌트 및 다중 테넌트 애플리케이션의 위임된 권한, 애플리케이션 권한, 권한 있는 클라이언트 애플리케이션, 필요한 권한 및 동의 허용 속성을 업데이트할 수 있습니다. 동의를 수행할 수 있는 기능은 부여하지 않습니다. 애플리케이션 등록 API 권한 및 API 표시의 모든 필드에 대한 액세스 권한을 부여합니다.

![이 권한은 앱 등록 API 권한 페이지에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/app-registration-api-permissions.png)

![이 권한은 앱 등록 API 표시 페이지에 대한 액세스 권한을 부여합니다.](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

단일 테넌트 애플리케이션에 한해 microsoft.directory/applications/permissions/update와 동일한 권한을 부여합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal, Azure AD PowerShell 및 Graph API](custom-create.md)를 사용하여 사용자 지정 역할 만들기
- [역할 할당 나열](view-assignments.md)
