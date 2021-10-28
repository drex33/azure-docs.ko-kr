---
title: 조건부 액세스 정책 마이그레이션 - Azure Active Directory
description: Azure Portal에서 클래식 정책을 마이그레이션하기 위해 알아야 하는 작업을 자세히 살펴봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88244a527825a8cbaaa81dea64dd0c2f49a5fb72
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618175"
---
# <a name="conditional-access-classic-policy-migration"></a>조건부 액세스 클래식 정책 마이그레이션

조건부 액세스는 Azure Active Directory에서 신호를 주고 결정을 내리고 조직 정책을 적용하기 위해 사용하는 도구입니다. 조건부 액세스는 새로운 ID 중심 제어 평면의 핵심입니다. 목적은 동일하지만 조건부 액세스가 작동하는 방식이 크게 개선된 새로운 Azure Portal 릴리스가 도입되었습니다.

Azure Portal에서 만들지 않은 정책을 마이그레이션하는 것을 고려해야 하는 이유는 다음과 같습니다.

- 이전에는 처리할 수 없었던 시나리오를 이제 처리할 수 있습니다.
- 통합하여 관리해야 하는 정책의 수를 줄일 수 있습니다.
- 중앙의 단일 위치에서 모든 조건부 액세스 정책을 관리할 수 있습니다.
- Azure 클래식 포털의 사용이 중지됩니다.

이 문서에서는 기존 조건부 액세스 정책을 새 프레임워크로 마이그레이션하기 위해 알아야 하는 작업에 대해 설명합니다.

## <a name="classic-policies"></a>클래식 정책

[Azure Portal](https://portal.azure.com)에서 조건부 액세스 정책은 **Azure Active Directory** > **보안** > **조건부 액세스** 아래에서 찾을 수 있습니다. 이 페이지를 사용하여 만들지 않은 이전 조건부 액세스 정책이 조직에 있을 수도 있습니다. 이러한 정책은 *클래식 정책* 이라고 합니다. 클래식 정책은 다음 위치에서 생성한 조건부 액세스 정책입니다.

- Azure 클래식 포털
- Intune 클래식 포털
- Intune 앱 보호 포털

**조건부 액세스** 페이지에서 **관리** 섹션의 [**클래식 정책**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)을 클릭하여 클래식 정책에 액세스할 수 있습니다. 

![클래식 정책 보기를 표시하는 Azure AD의 조건부 액세스](./media/policy-migration/71.png)

**클래식 정책** 보기는 다음과 같은 옵션을 제공합니다.

- 클래식 정책을 필터링합니다.
- 클래식 정책을 사용하지 않도록 설정합니다.
- 클래식 정책의 설정을 검토하고 비활성화합니다.

   ![기존 정책 구성을 포함한 클래식 정책 세부 정보](./media/policy-migration/74.png)

> [!WARNING]
> 비활성화된 클래식 정책은 다시 활성화할 수 없습니다.

클래식 정책의 자세히 보기를 사용하면 설정을 문서화하고, 포함되거나 제외된 그룹을 수정하고, 정책을 사용하지 않도록 설정할 수 있습니다.

![정책 세부 정보 - 포함하거나 제외할 그룹](./media/policy-migration/75.png)

모든 포함된 사용자 및 그룹에 대해 정책을 사용하지 않도록 설정하기 전에 선택한 그룹을 변경하거나 특정 그룹을 제외하여 일부 테스트 사용자에게 사용하지 않도록 설정된 클래식 정책의 효과를 테스트할 수 있습니다.
 
## <a name="migration-considerations"></a>마이그레이션 고려 사항

이 문서에서는 Azure AD 조건부 액세스 정책을 *새 정책* 이라고도 합니다.
클래식 정책도 사용하지 않도록 설정하거나 제거할 때까지 새 정책과 함께 계속 작동합니다. 

정책 통합 컨텍스트에서 중요한 사항은 다음과 같습니다.

- 클래식 정책의 경우 특정 클라우드 앱과 연결되어 있지만 새 정책에서는 필요한 만큼 많은 클라우드 앱을 선택할 수 있습니다.
- 클라우드 앱에 대해 클래식 정책 및 새 정책을 제어하는 경우 모든 제어(*AND*)가 이행되어야 합니다. 
- 새 정책에서는 다음을 수행할 수 있습니다.
   - 시나리오에 필요한 경우 여러 조건을 결합합니다. 
   - 여러 권한 부여 요구 사항을 액세스 제어로 선택하고 논리 *OR*(선택한 제어 중 하나가 필요함) 또는 논리 *AND*(선택한 모든 제어가 필요함)로 결합합니다.

### <a name="exchange-online"></a>Exchange Online

**Exchange Active Sync** 가 클라이언트 앱 조건으로 포함된 **Exchange Online** 의 클래식 정책을 마이그레이션하려는 경우 하나의 새 정책으로 통합하지 못할 수 있습니다. 

예를 들어 모든 클라이언트 앱 유형을 지원하려는 경우에 해당합니다. **Exchange Active Sync** 가 클라이언트 앱 조건으로 있는 새 정책에서는 다른 클라이언트 앱을 선택할 수 없습니다.

![클라이언트 앱을 선택하는 조건부 액세스](./media/policy-migration/64.png)

클래식 정책에 여러 조건이 포함되어 있는 경우에도 하나의 새 정책으로 통합할 수 없습니다. **Exchange Active Sync** 가 클라이언트 앱 조건으로 구성되어 있는 새 정책은 다른 조건을 지원하지 않습니다.   

![Exchange ActiveSync는 선택한 조건을 지원하지 않습니다.](./media/policy-migration/08.png)

**Exchange Active Sync** 가 클라이언트 앱 조건으로 구성된 새 정책이 있는 경우 다른 모든 조건이 구성되지 않도록 해야 합니다. 

![조건부 액세스 조건](./media/policy-migration/16.png)
 
**Exchange Active Sync** 가 클라이언트 앱 조건으로 포함된 Exchange Online의 앱 기반 클래식 정책에서는 **지원되는** 디바이스 플랫폼과 **지원되지 않는** 디바이스 플랫폼이 허용됩니다. 관련된 새 정책에서는 개별 디바이스 플랫폼을 구성할 수 없으며 [지원되는 디바이스 플랫폼](concept-conditional-access-conditions.md#device-platforms)으로만 지원을 제한할 수 있습니다. 

![조건부 액세스에서 Exchange ActiveSync를 선택합니다.](./media/policy-migration/65.png)

다음과 같은 경우에는 **Exchange Active Sync** 가 클라이언트 앱 조건으로 포함된 여러 클래식 정책을 통합할 수 있습니다.

- **Exchange Active Sync** 만 조건으로 포함된 경우 
- 여러 액세스 권한 부여 조건이 구성되어 있는 경우

한 가지 일반적인 시나리오는 다음과 같은 통합입니다.

- Azure 클래식 포털에서 디바이스 기반 클래식 정책 통합 
- Intune 앱 보호 포털에서 앱 기반 클래식 정책 통합 
 
이 경우 두 요구 사항이 모두 선택된 하나의 새 정책으로 클래식 정책을 통합할 수 있습니다.

![조건부 액세스 권한 부여 제어](./media/policy-migration/62.png)

### <a name="device-platforms"></a>디바이스 플랫폼

앱 기반 제어를 사용하는 클래식 정책은 iOS 및 Android에 디바이스 플랫폼 조건으로 미리 구성되어 있습니다. 

새 정책에서는 개별적으로 지원할 [디바이스 플랫폼](concept-conditional-access-conditions.md#device-platforms)을 선택해야 합니다.

![조건부 액세스 디바이스 플랫폼 선택](./media/policy-migration/41.png)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스에 보고 전용 모드를 사용하여 새 정책 결정의 영향을 확인합니다.](concept-conditional-access-report-only.md)
- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)을 참조하세요.
- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 되었으면 [방법: Azure Active Directory에서 조건부 액세스 배포 계획](plan-conditional-access.md) 문서를 참조하세요. 
