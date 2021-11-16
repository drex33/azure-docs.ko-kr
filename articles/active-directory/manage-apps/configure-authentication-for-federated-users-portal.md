---
title: 홈 영역 검색을 사용하여 로그인 자동 가속 구성
titleSuffix: Azure AD
description: 자동 가속 및 도메인 힌트를 비롯하여 페더레이션된 사용자의 Azure Active Directory 인증을 위한 홈 영역 검색 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/13/2021
ms.author: davidmu
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.reviewer: hirsin
ms.openlocfilehash: a266e60ef3079ecb12b227b047519abad5d26bf1
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619731"
---
# <a name="configure-sign-in-behavior-in-azure-active-directory"></a>Azure Active Directory에서 로그인 동작 구성

이 문서에서는 HRD(홈 영역 검색) 정책을 사용하여 페더레이션된 사용자의 Azure AD(Azure Active Directory) 인증 동작을 구성하는 방법을 소개합니다.  또한 자동 가속을 사용하여 사용자 이름 입력 화면을 건너뛰고 자동으로 사용자를 페더레이션된 로그인 엔드포인트로 전달하는 방법을 다룹니다.  자동 가속을 더 이상 구성하지 않는 것이 좋습니다. FIDO(First Identity Online)와 같은 더 강력한 인증 방법을 사용하지 못할 수 있고 협업을 저해하기 때문입니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD에서 애플리케이션에 대한 HRD 정책을 구성하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- 최신 Azure AD PowerShell cmdlet 미리 보기입니다.

## <a name="set-up-an-hrd-policy-on-an-application"></a>애플리케이션에 HRD 정책 설정

Azure AD PowerShell cmdlet을 사용하여 다음을 포함한 몇 가지 시나리오를 연습해 보겠습니다.

- 페더레이션된 단일 도메인이 있는 테넌트의 애플리케이션에 대해 자동 가속을 수행하도록 HRD 정책을 설정합니다.

- 테넌트에 대해 확인된 여러 도메인 중 하나에 애플리케이션 자동 가속을 수행하도록 HRD 정책을 설정합니다.

- 레거시 애플리케이션에서 페더레이션 사용자에 대한 사용자 이름/암호 인증을 Azure AD에 직접 수행할 수 있도록 HRD 정책을 설정합니다.

- 정책이 구성된 애플리케이션 나열

다음 예제에서는 Azure AD에서 애플리케이션 서비스 주체에 대해 정책을 생성, 업데이트, 연결 및 삭제합니다.

1. 시작하기 전에 Connect 명령을 실행하여 관리자 계정으로 Azure AD에 로그인합니다.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 다음 명령을 실행하여 조직의 모든 정책을 확인합니다.

    ```powershell
    Get-AzureADPolicy
    ```

아무 정책도 반환되지 않으면 테넌트에서 만들어진 정책이 없는 것입니다.

다음 예제에서는 애플리케이션에 할당되는 다음 정책 중 하나를 만듭니다.

- 테넌트에 단일 도메인이 있는 경우 애플리케이션에 로그인할 때 사용자를 AD FS 로그인 화면으로 자동 가속합니다.
- 테넌트에 페더레이션된 도메인이 둘 이상 있는 경우 사용자를 AD FS 로그인 화면으로 자동 가속합니다.
- 정책이 할당된 애플리케이션의 페더레이션 사용자에 대한 비대화형 사용자 이름/암호 로그인을 Azure AD에 직접 사용하도록 설정합니다.

## <a name="create-an-hrd-policy"></a>HRD 정책 만들기

테넌트에 단일 도메인이 있는 경우 다음 정책은 애플리케이션에 로그인할 때 사용자를 AD FS 로그인 화면으로 자동 가속합니다.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

테넌트에 페더레이션된 도메인이 둘 이상 있는 경우 다음 정책은 사용자를 AD FS 로그인 화면으로 자동 가속합니다. 애플리케이션에 대한 사용자를 인증하는 페더레이션된 도메인이 둘 이상 있는 경우 자동 가속할 도메인을 지정해야 합니다.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

페더레이션 사용자에 대한 사용자 이름/암호 인증을 Azure AD를 통해 직접 사용하도록 설정하는 특정 애플리케이션에 대한 정책을 만들려면 다음 명령을 실행합니다.

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

새 정책을 확인하고 해당 **ObjectID** 를 가져오려면 다음 명령을 실행합니다.

```powershell
Get-AzureADPolicy
```

HRD 정책을 만든 후에 적용하려면 여러 애플리케이션 서비스 주체에 할당하면 됩니다.

## <a name="locate-the-service-principal-to-which-to-assign-the-policy"></a>정책을 할당할 서비스 주체 찾기

정책을 할당할 서비스 주체의 **ObjectID** 가 필요합니다. 여러 가지 방법으로 서비스 주체의 **ObjectID** 를 찾을 수 있습니다.

[Azure Portal](https://portal.azure.com)을 사용하거나 [Microsoft Graph](/graph/api/resources/serviceprincipal)를 쿼리할 수 있습니다. [Graph 탐색기 도구](https://developer.microsoft.com/graph/graph-explorer)로 이동하고 Azure AD 계정에 로그인하여 조직의 모든 서비스 주체를 확인할 수 있습니다.

PowerShell을 사용하기 때문에 다음 cmdlet을 사용하여 서비스 주체 및 해당 ID를 나열할 수 있습니다.

```powershell
Get-AzureADServicePrincipal
```

## <a name="assign-the-policy-to-your-service-principal"></a>서비스 주체에 정책 할당

자동 가속을 구성하려는 애플리케이션의 서비스 주체 **ObjectID** 를 찾은 후 다음 명령을 실행합니다. 이 명령은 1단계에서 만든 HRD 정책을 2단계에서 찾은 서비스 주체에 연결합니다.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

정책을 추가하려는 각 서비스 주체에 대해 이 명령을 반복할 수 있습니다.

이미 애플리케이션에 HomeRealmDiscovery 정책이 할당되어 있는 경우 두 번째 정책은 추가할 수 없습니다.  이 경우 추가 매개 변수를 추가하기 위해 애플리케이션에 할당된 홈 영역 검색 정책의 정의를 변경합니다.

### <a name="check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>HRD 정책에 할당된 애플리케이션 서비스 주체 확인

HRD 정책이 구성되어 있는 애플리케이션을 확인하려면 **Get-AzureADPolicyAppliedObject** cmdlet을 사용합니다. 확인하려는 정책의 **ObjectID** 를 전달합니다.

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>

Try the application to check that the new policy is working.

### List the applications for which HRD policy is configured

1. List all policies that were created in your organization

```powershell
Get-AzureADPolicy
```

할당을 나열하려는 정책의 **ObjectID** 를 적어둡니다.

2. 정책이 할당된 서비스 주체 나열

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="remove-an-hrd-policy-from-an-application"></a>애플리케이션에서 HRD 정책 제거

1. ObjectID 가져오기

앞의 예제를 사용하여 제거하려는 애플리케이션 서비스 주체의 정책 **ObjectID** 를 가져옵니다.

2. 애플리케이션 서비스 주체에서 정책 할당 제거

```powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

3. 정책이 할당된 서비스 주체를 나열하여 제거 확인

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>다음 단계

- Azure AD에서 홈 영역 검색이 작동하는 방식에 대한 자세한 내용은 [Azure AD의 애플리케이션에 대한 홈 영역 검색](home-realm-discovery-policy.md)을 참조하세요.