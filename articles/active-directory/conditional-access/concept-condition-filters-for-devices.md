---
title: 조건부 액세스 정책의 조건으로 사용되는 디바이스에 대한 필터 - Azure Active Directory
description: 조건부 액세스에서 디바이스에 대한 필터를 사용하여 보안 태세 개선
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dc7b4630d26c0f1b78df5a29a3210fd68f3d734
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012665"
---
# <a name="conditional-access-filter-for-devices"></a>조건부 액세스: 디바이스에 대한 필터

조건부 액세스 정책을 만들 때 관리자가 환경에서 특정 장치를 대상으로 지정하거나 제외하는 기능을 요청했습니다. 디바이스에 대한 필터 조건은 관리자에게 이 기능을 제공합니다. 이제 [디바이스 필터에 대해 지원되는 연산자 및 속성](#supported-operators-and-device-properties-for-filters)을 사용하여 특정 디바이스를 대상으로 지정할 수 있으며, 조건부 액세스 정책에서 사용 가능한 기타 할당 조건을 지정할 수 있습니다.

:::image type="content" source="media/concept-condition-filters-for-devices/create-filter-for-devices-condition.png" alt-text="조건부 액세스 정책 조건에서 디바이스에 대한 필터 만들기":::


## <a name="common-scenarios"></a>일반적인 시나리오

이제 조직에서 디바이스에 대한 필터 조건을 사용하여 여러 시나리오를 사용하도록 설정할 수 있습니다. 다음은 이 새 조건을 사용하는 방법에 대한 예제를 보여 주는 몇 가지 핵심 시나리오입니다.

- Microsoft Azure Management와 같은 권한 있는 리소스에 대한 액세스, [권한 있는 또는 보안 관리자 워크스테이션](/security/compass/privileged-access-devices)에서 액세스하는 권한 있는 사용자에 대한 액세스를 제한합니다. 이 시나리오에서 조직은 두 가지 조건부 액세스 정책을 만듭니다.
   - 정책 1: 전역 관리자의 디렉터리 역할을 하는 모든 사용자는 Microsoft Azure Management 클라우드 앱에 액세스하고, 액세스 제어와 액세스 권한 부여가 가능하지만 다단계 인증이 필요하고 디바이스가 규정을 준수하는 것으로 표시되어야 합니다.
   - 정책 2: 전역 관리자의 디렉터리 역할을 하는 모든 사용자는 Microsoft Azure Management 클라우드 앱에 액세스하고, 규칙 식 device.extensionAttribute1 equals SAW를 사용하여 디바이스에 대한 필터를 제외하고, 액세스 제어, 차단이 가능합니다.
- Windows 7과 같이 지원되지 않는 운영 체제 버전을 실행하는 디바이스에서 조직 리소스에 대한 액세스를 차단합니다. 이 시나리오에서 조직은 다음과 같은 두 가지 조건부 액세스 정책을 만듭니다.
   - 정책 1: 모든 사용자는 모든 클라우드 앱에 액세스하고, 액세스 제어, 액세스 권한 부여가 가능하지만 디바이스가 규정을 준수하는 것으로 표시되어야 하고 디바이스가 하이브리드 Azure AD 조인된 디바이스여야 합니다.
   - 정책 2: 모든 사용자는 모든 클라우드 앱에 액세스하고, 규칙 식 device.operatingSystem equals Windows 및 device.operatingSystemVersion startsWith ‘6.1’을 사용하여 디바이스에 대한 필터를 포함하고, 액세스 제어, 차단이 가능합니다.
- Teams 휴대폰 또는 Surface Hub 디바이스와 같은 특정 디바이스에서 사용하는 경우 서비스 계정과 같은 특정 계정에 대한 다단계 인증이 필요하지 않습니다. 이 시나리오에서 조직은 다음과 같은 두 가지 조건부 액세스 정책을 만듭니다.
   - 정책 1: 서비스 계정을 제외한 모든 사용자는 모든 클라우드 앱에 액세스하고, 액세스 제어, 액세스 권한 부여가 가능하지만 다단계 인증이 필요합니다.
   - 정책 2: 일부 사용자 및 그룹과 서비스 계정만을 포함하는 포함 그룹은 모든 클라우드 앱에 액세스하고, 규칙 식 device.extensionAttribute2 not equals TeamsPhoneDevice를 사용하여 디바이스에 대한 필터를 제외하고, 액세스 제어, 차단이 가능합니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

디바이스에 대한 필터는 Azure Portal의 조건부 액세스 정책을 만들거나 Microsoft Graph API를 사용할 때 사용할 수 있는 옵션입니다.

> [!IMPORTANT]
> 디바이스에 대한 디바이스 상태 및 필터는 조건부 액세스 정책에서 함께 사용할 수 없습니다.

다음 단계는 [일반적인 시나리오](#common-scenarios)에서 첫 번째 시나리오를 지원하는 두 가지 조건부 액세스 정책을 만드는 데 도움이 됩니다. 

정책 1: 전역 관리자의 디렉터리 역할을 하는 모든 사용자는 Microsoft Azure Management 클라우드 앱에 액세스하고, 액세스 제어와 액세스 권한 부여가 가능하지만 다단계 인증이 필요하고 디바이스가 규정을 준수하는 것으로 표시되어야 합니다.

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **디렉터리 역할** 을 선택하고 **전역 관리자** 를 선택합니다.
   
      > [!WARNING]
      > 조건부 액세스 정책은 기본 제공 역할을 지원합니다. 조건부 액세스 정책은 [관리 단위 범위](../roles/admin-units-assign-roles.md) 또는 [사용자 지정 역할](../roles/custom-create.md)을 비롯한 다른 역할 형식에는 적용되지 않습니다.

   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **앱 선택** 을 선택하고 **Microsoft Azure Management** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **다단계 인증 필요**, **디바이스를 규정 준수 상태로 표시해야 함** 을 선택한 다음 **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **만들기** 를 선택하여 정책을 만들어 사용하도록 설정합니다.

정책 2: 전역 관리자의 디렉터리 역할을 하는 모든 사용자는 Microsoft Azure Management 클라우드 앱에 액세스하고, 규칙 식 device.extensionAttribute1 equals SAW를 사용하여 디바이스에 대한 필터를 제외하고, 액세스 제어, 차단이 가능합니다.

1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **디렉터리 역할** 을 선택하고 **전역 관리자** 를 선택합니다.
   
      > [!WARNING]
      > 조건부 액세스 정책은 기본 제공 역할을 지원합니다. 조건부 액세스 정책은 [관리 단위 범위](../roles/admin-units-assign-roles.md) 또는 [사용자 지정 역할](../roles/custom-create.md)을 비롯한 다른 역할 형식에는 적용되지 않습니다.

   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **앱 선택** 을 선택하고 **Microsoft Azure Management** 를 선택합니다.
1. **조건**, **디바이스에 대한 필터**.
   1. **구성** 을 **예** 로 전환합니다.
   1. **규칙과 일치하는 디바이스** 를 **정책에서 필터링된 디바이스 제외** 로 설정합니다.
   1. 속성을 `ExtensionAttribute1`, 연산자를 `Equals`, 값을 `SAW`로 설정합니다.
   1. **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 차단** 을 선택하고 **선택** 을 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **만들기** 를 선택하여 정책을 만들어 사용하도록 설정합니다.

### <a name="filter-for-devices-graph-api"></a>디바이스에 대한 필터 Graph API

디바이스에 대한 필터 API는 Microsoft Graph v1.0 엔드포인트에서 사용할 수 있으며 https://graph.microsoft.com/v1.0/identity/conditionalaccess/policies/ 을 사용하여 액세스할 수 있습니다. 새 조건부 액세스 정책을 만들 때 디바이스에 대한 필터를 구성하거나 기존 정책을 업데이트하여 디바이스에 대한 필터 조건을 구성할 수 있습니다. 기존 정책을 업데이트하려면 기존 정책의 정책 ID를 추가하고 다음 요청 본문을 실행하여 위에서 언급한 Microsoft Graph v1.0 엔드포인트에 대한 패치 호출을 수행할 수 있습니다. 이 예에서는 SAW 디바이스로 표시되지 않은 디바이스를 제외하는 디바이스에 대한 필터 조건 구성을 보여 줍니다. 규칙 구문은 둘 이상의 단일 식으로 구성될 수 있습니다. 구문에 대한 자세한 내용은 [Azure Active Directory의 그룹에 대한 동적 멤버십 규칙](../enterprise-users/groups-dynamic-membership.md)을 참조하세요. 

```json
{
    "conditions": {
        "devices": {
            "deviceFilter": {
                "mode": "exclude",
                "rule": "device.extensionAttribute1 -ne \"SAW\""
            }
        }
    }
}
```

## <a name="supported-operators-and-device-properties-for-filters"></a>필터에 대한 지원되는 연산자 및 디바이스 속성

조건부 액세스에서 디바이스에 대한 필터 조건에 다음과 같은 디바이스 특성을 사용할 수 있습니다.

| 지원되는 디바이스 특성 | 지원되는 연산자 | 지원되는 값 | 예제 |
| --- | --- | --- | --- |
| deviceId | Equals, NotEquals, In, NotIn | GUID인 유효한 deviceId | (device.deviceid -eq “498c4de7-1aee-4ded-8d5d-000000000000”) |
| displayName | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | 모든 문자열 | (device.displayName -contains “ABC”) |
| deviceOwnership | Equals, NotEquals | 지원되는 값은 개인 디바이스 가져오기의 경우 "개인"이고 회사 소유 디바이스의 경우 "회사"입니다.  | (device.deviceOwnership -eq "Company") |
| isCompliant | Equals, NotEquals | 지원되는 값은 호환 디바이스의 경우 "True"이고 비호환 디바이스의 경우 "False"입니다.  | (device.isCompliant -eq "True") |
| 제조업체 | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | 모든 문자열 | (device.manufacturer -startsWith “Microsoft”) |
| mdmAppId | Equals, NotEquals, In, NotIn | 유효한 MDM 애플리케이션 ID | (device.mdmAppId -in [“0000000a-0000-0000-c000-000000000000”] |
| model | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | 모든 문자열 | (device.model -notContains “Surface”) |
| operatingSystem | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | 유효한 운영 체제(예: Windows, iOS 또는 Android) | (device.operatingSystem -eq “Windows”) |
| operatingSystemVersion | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | 유효한 운영 체제 버전(예: Windows 7의 경우 6.1, Windows 8의 경우 6.2 또는 Windows 10의 경우 10.0) | (device.operatingSystemVersion -in [“10.0.18363”, “10.0.19041”, “10.0.19042”]) |
| physicalIds | Contains, NotContains | 예를 들어 모든 Windows Autopilot 디바이스는 디바이스 physicalIds 속성에 ZTDId(가져온 모든 Windows Autopilot 디바이스에 할당된 고유 값)를 저장합니다. | (device.devicePhysicalIDs -contains "[ZTDId]:value") |
| profileType | Equals, NotEquals | 디바이스에 대해 설정된 유효한 프로필 형식 집합입니다. 지원되는 값은 RegisteredDevice(기본값), SecureVM(Azure AD 로그인으로 활성화된 Windows VM에 사용), Printer(프린터에 사용), Shared(공유 디바이스에 사용), IoT(IoT 디바이스에 사용)입니다. | (device.profileType -notIn [“Printer”, “Shared”, “IoT”] |
| systemLabels | Contains, NotContains | 시스템에서 디바이스에 적용한 레이블 목록입니다. 지원되는 값 중 일부는 AzureResource(Azure AD 로그인으로 활성화된 Windows VM에 사용), M365Managed(Microsoft Managed Desktop을 사용하여 관리되는 디바이스에 사용), MultiUser(공유 디바이스에 사용)입니다. | (device.systemLabels -contains "M365Managed") |
| trustType | Equals, NotEquals | 디바이스에 대해 유효한 등록된 상태입니다. 지원되는 값은 AzureAD(Azure AD 조인 디바이스에 사용), ServerAD(Hybrid Azure AD 조인 디바이스에 사용), Workplace(Azure AD 등록 디바이스에 사용)입니다. | (device.trustType -notIn ‘ServerAD, Workplace’) |
| extensionAttribute1-15 | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | extensionAttributes1-15는 고객이 디바이스 개체에 사용할 수 있는 특성입니다. 고객은 사용자 지정 값을 사용하여 extensionAttributes1부터 15 중에서 업데이트하고 이를 조건부 액세스의 디바이스에 대한 필터 조건에 사용할 수 있습니다. 모든 문자열 값을 사용할 수 있습니다. | (device.extensionAttribute1 -eq ‘SAW’) |

## <a name="policy-behavior-with-filter-for-devices"></a>디바이스에 대한 필터를 사용하는 정책 동작

조건부 액세스의 디바이스에 대한 필터 조건은 Azure AD에 등록된 디바이스의 디바이스 특성에 따라 정책을 평가하므로 정책이 적용되거나 적용되지 않는 상황을 이해하는 것이 중요합니다. 아래 표에서는 디바이스에 대한 필터 조건을 구성할 때의 동작을 보여 줍니다. 

| 디바이스에 대한 필터 조건 | 디바이스 등록 상태 | 적용된 디바이스 필터 
| --- | --- | --- |
| 양수 연산자(Equals, StartsWith, EndsWith, Contains, In)와 모든 특성 사용을 포함하는 포함/제외 모드 | 등록되지 않은 디바이스 | No |
| 양수 연산자(Equals, StartsWith, EndsWith, Contains, In)와 extensionAttributes1-15를 제외한 특성 사용을 포함하는 포함/제외 모드 | 등록된 디바이스 | 예, 조건이 충족되는 경우 |
| 양수 연산자(Equals, StartsWith, EndsWith, Contains, In)와 extensionAttributes1-15를 포함한 특성 사용을 포함하는 포함/제외 모드 | Intune에서 관리하는 등록된 디바이스 | 예, 조건이 충족되는 경우 |
| 양수 연산자(Equals, StartsWith, EndsWith, Contains, In)와 extensionAttributes1-15를 포함한 특성 사용을 포함하는 포함/제외 모드 | Intune에서 관리하지 않는 등록된 디바이스 | 예, 조건이 충족되고 디바이스가 규정을 준수하거나 Hybrid Azure AD 조인 디바이스인 경우 |
| 음수 연산자(NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn)와 모든 특성 사용을 포함하는 포함/제외 모드 | 등록되지 않은 디바이스 | 예 |
| 음수 연산자(NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn)와 extensionAttributes1-15를 제외한 모든 특성 사용을 포함하는 포함/제외 모드 | 등록된 디바이스 | 예, 조건이 충족되는 경우 |
| 음수 연산자(NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn)와 extensionAttributes1-15를 포함한 모든 특성 사용을 포함하는 포함/제외 모드 | Intune에서 관리하는 등록된 디바이스 | 예, 조건이 충족되는 경우 |
| 음수 연산자(NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn)와 extensionAttributes1-15를 포함한 모든 특성 사용을 포함하는 포함/제외 모드 | Intune에서 관리하지 않는 등록된 디바이스 | 예, 조건이 충족되고 디바이스가 규정을 준수하거나 Hybrid Azure AD 조인 디바이스인 경우 |

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 조건](concept-conditional-access-conditions.md)
- [일반 조건부 액세스 정책](concept-conditional-access-policy-common.md)
- [권한 있는 액세스 스토리의 일부로 디바이스 보안 설정](/security/compass/privileged-access-devices)
