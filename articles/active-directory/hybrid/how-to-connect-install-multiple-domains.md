---
title: Azure AD Connect 복수 도메인
description: 이 문서에서는 Microsoft 365 및 Azure AD를 사용하여 여러 최상위 도메인을 설정하고 구성하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af2a466dc5906f752970cbc6b8898aeeea39475
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477500"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Azure AD로 페더레이션에 대한 여러 도메인 지원
다음 설명서에서는 Microsoft 365 또는 Azure AD 도메인으로 페더레이션하는 경우 여러 최상위 도메인 및 하위 도메인을 사용하는 방법에 대한 지침을 제공합니다.

## <a name="multiple-top-level-domain-support"></a>여러 최상위 도메인 지원
Azure AD로 여러 최상위 도메인을 페더레이션하려면 하나의 최상위 도메인으로 페더레이션하는 경우 필요하지 않은 몇 가지 추가 구성이 필요합니다.

도메인이 Azure AD로 페더레이션되는 경우 Azure의 도메인에서 여러 속성이 설정됩니다.  중요한 한가지 속성은 IssuerUri입니다.  이 속성은 Azure AD에서 토큰이 연결된 도메인을 식별하는 데 사용되는 URI입니다.  URI는 아무 것도 확인할 필요가 없지만 유효한 URI여야 합니다.  기본적으로 Azure AD는 URI를 온-프레미스 AD FS 구성에서 페더레이션 서비스 식별자의 값으로 설정합니다.

> [!NOTE]
> 페더레이션 서비스 식별자는 페더레이션 서비스를 고유하게 식별하는 URI입니다.  페더레이션 서비스는 보안 토큰 서비스로 작동하는 AD FS의 인스턴스입니다.
>
>

`Get-MsolDomainFederationSettings -DomainName <your domain>` PowerShell 명령을 사용하여 IssuerUri를 볼 수 있습니다.

![PowerShell에서 “Get-MsolDomainFederationSettings” 명령을 입력한 후의 결과 스크린샷](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

두 개 이상의 최상위 도메인을 추가하는 경우에 문제가 발생합니다.  예를 들어 Azure AD와 온-프레미스 환경 간 페더레이션을 설정했다고 가정합니다.  이 문서의 경우 도메인, bmcontoso.com이 사용 중입니다.  두 번째 최상위 도메인 bmfabrikam.com이 추가되었습니다.

![여러 최상위 도메인을 보여주는 스크린샷](./media/how-to-connect-install-multiple-domains/domains.png)

bmfabrikam.com 도메인을 페더레이션되도록 변환할 때 오류가 발생합니다.  Azure AD가 IssuerUri 속성에서 둘 이상의 도메인에 같은 값을 허용하지 않는 제약 조건을 갖는 것이 이유입니다.  

![PowerShell의 페더레이션 오류 스크린샷](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain 매개 변수
이 제약 조건을 해결하려면 `-SupportMultipleDomain` 매개 변수를 사용하여 수행할 수 있는 다른 IssuerUri를 추가해야 합니다.  이 매개 변수는 다음 cmdlet과 함께 사용됩니다.

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

이 매개 변수를 사용하면 Azure AD가 도메인의 이름에 기반하도록 IssuerUri를 구성합니다.  IssuerUri는 Azure AD의 디렉터리에서 고유합니다.  매개 변수를 사용하여 PowerShell 명령을 성공적으로 완료할 수 있습니다.

![PowerShell 명령이 성공적으로 완료되었음을 보여 주는 스크린샷](./media/how-to-connect-install-multiple-domains/convert.png)

bmfabrikam.com 도메인의 설정을 보면 다음을 확인할 수 있습니다.

![“bmfabrikam.com” 도메인의 설정 스크린샷](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain`은 여전히 adfs.bmcontoso.com의 페더레이션 서비스를 가리키도록 구성된 다른 엔드포인트를 변경하지 않습니다.

`-SupportMultipleDomain`은 또한 AD FS 시스템이 Azure AD용으로 발급된 토큰에 적절한 발급자 값을 포함하도록 합니다. 이 값은 사용자 UPN의 도메인 부분을 IssuerUri, 즉 `https://{upn suffix}/adfs/services/trust`의 도메인으로 사용하여 설정됩니다.

따라서 Azure AD 또는 Microsoft 365에 인증하는 동안 사용자 토큰의 IssuerUri 요소는 Azure AD에서 도메인을 찾는 데 사용됩니다. 일치하는 항목이 없는 경우 인증이 실패합니다.

예를 들어 사용자의 UPN이 bsimon@bmcontoso.com인 경우 토큰의 IssuerUri 요소인 AD FS 발급자는 `http://bmcontoso.com/adfs/services/trust`로 설정됩니다. 이 요소는 Azure AD 구성에 일치하며 인증이 성공합니다.

다음은 이 논리를 구현하는 사용자 지정된 클레임 규칙입니다.

```
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));
```


> [!IMPORTANT]
> 새 것을 추가하거나 기존 도메인을 변환하려고 하는 경우 SupportMultipleDomain 스위치를 사용하려면 지원하도록 페더레이션된 트러스트를 설정해야 합니다.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>AD FS와 Azure AD 간의 트러스트를 업데이트하는 방법
AD FS와 Azure AD의 인스턴스 간의 페더레이션된 트러스트를 설정하지 않았을 경우 이 트러스트를 다시 만들어야 할 수 있습니다.  이는 `-SupportMultipleDomain` 매개 변수 없이 원래 설치될 때 IssuerUri가 기본 값으로 설정되기 때문입니다.  아래 스크린샷에서 IssuerUri는 `https://adfs.bmcontoso.com/adfs/services/trust`로 설정되어 있습니다.

Azure AD 포털에 새 도메인을 성공적으로 추가한 다음, `Convert-MsolDomaintoFederated -DomainName <your domain>`을 사용하여 변환하려고 하는 경우 다음과 같은 오류가 발생합니다.

![“Convert-MsolDomaintoFederated” 명령을 사용하여 새 도메인을 변환하려고 시도한 후 PowerShell의 페더레이션 오류 스크린샷](./media/how-to-connect-install-multiple-domains/trust1.png)

`-SupportMultipleDomain` 스위치를 추가하려는 경우 다음 오류를 수신합니다.

![“-SupportMultipleDomain” 스위치를 추가한 후의 페더레이션 오류 스크린샷](./media/how-to-connect-install-multiple-domains/trust2.png)

원본 도메인에서 `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` 을(를) 실행하려고 하면 또한 오류가 발생합니다.

![페더레이션 오류](./media/how-to-connect-install-multiple-domains/trust3.png)

아래 단계를 사용하여 추가 최상위 도메인을 추가합니다.  이미 도메인을 추가하고 `-SupportMultipleDomain` 매개 변수를 사용하지 않은 경우 원본 도메인 제거 및 업데이트에 대한 단계를 시작합니다.  최상위 도메인을 아직 추가하지 않은 경우 Azure AD Connect의 PowerShell을 사용하여 도메인을 추가하기 위한 단계를 시작할 수 있습니다.

다음 단계를 사용하여 Microsoft Online 트러스트를 제거하고 원본 도메인을 업데이트합니다.

1. AD FS 페더레이션 서버에서 **AD FS 관리**
2. 왼쪽에서 **트러스트 관계** 및 **신뢰 당사자 트러스트** 를 확장합니다.
3. 오른쪽에서 **Microsoft Office 365 ID 플랫폼** 항목을 삭제합니다.
   ![Microsoft 온라인 제거](./media/how-to-connect-install-multiple-domains/trust4.png)
4. [Windows PowerShell용 Azure Active Directory 모듈](/previous-versions/azure/jj151815(v=azure.100))이 설치된 컴퓨터에서 `$cred=Get-Credential`을(를) 실행합니다.  
5. 페더레이션하는 Azure AD 도메인에 대한 전역 관리자의 사용자 이름 및 암호를 입력합니다.
6. PowerShell에서 `Connect-MsolService -Credential $cred`을 입력합니다.
7. PowerShell에서 `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`을 입력합니다.  이 업데이트는 원본 도메인에 대한 것입니다.  따라서 위의 도메인을 사용하는 것은 `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`입니다.

PowerShell을 사용하여 새 최상위 도메인을 추가하려면 다음 단계를 사용합니다.

1. [Windows PowerShell용 Azure Active Directory 모듈](/previous-versions/azure/jj151815(v=azure.100))이 설치된 컴퓨터에서 `$cred=Get-Credential`을(를) 실행합니다.  
2. 페더레이션하는 Azure AD 도메인에 대한 전역 관리자의 사용자 이름 및 암호를 입력합니다.
3. PowerShell에서 `Connect-MsolService -Credential $cred`을 입력합니다.
4. PowerShell에서 `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`을 입력합니다.

Azure AD Connect를 사용하여 새 최상위 도메인을 추가하려면 다음 단계를 사용합니다.

1. 바탕 화면 또는 시작 메뉴에서 Azure AD Connect 시작
2. “추가 Azure AD 도메인 추가”를 선택합니다. ![“추가 Azure AD 도메인 추가”가 선택된 “추가 작업” 페이지 스크린샷](./media/how-to-connect-install-multiple-domains/add1.png)
3. Azure AD 및 Active Directory 자격 증명 입력
4. 페더레이션에 대해 구성하려는 두 번째 도메인을 선택합니다.
   ![추가 Azure AD 도메인 추가](./media/how-to-connect-install-multiple-domains/add2.png)
5. 설치 클릭

### <a name="verify-the-new-top-level-domain"></a>새 최상위 도메인 확인
PowerShell 명령을 사용하여 `Get-MsolDomainFederationSettings -DomainName <your domain>`업데이트된 IssuerUri를 볼 수 있습니다.  아래 스크린샷은 페더레이션 설정이 원본 도메인 `http://bmcontoso.com/adfs/services/trust`에 업데이트된 것을 보여줍니다.

![원본 도메인에서 페더레이션 설정이 업데이트되었음을 보여 주는 스크린샷](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

새 도메인의 IssuerUri가 `https://bmfabrikam.com/adfs/services/trust`로 설정됨

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>하위 도메인에 대한 지원
하위 도메인을 추가할 때 Azure AD가 도메인을 처리하는 방식으로 인해 부모의 설정을 상속합니다.  따라서 IssuerUri가 부모와 일치해야 합니다.

따라서 예를 들어 bmcontoso.com이 있고 corp.bmcontoso.com을 추가한다고 가정합니다.  corp.bmcontoso.com에서 사용자의 IssuerUri는 **`http://bmcontoso.com/adfs/services/trust`** 여야 합니다.  하지만 위에서 Azure AD에 대해 구현한 표준 규칙은 **`http://corp.bmcontoso.com/adfs/services/trust`** 와 같은 발급자로 토큰을 생성합니다. 로 발급자를 사용하여 토큰을 생성하고 인증에 실패합니다.

### <a name="how-to-enable-support-for-subdomains"></a>하위 도메인에 대한 지원을 활성화하는 방법
이 동작을 해결하기 위해 Microsoft 온라인에 대한 AD FS 신뢰 당사자 트러스트를 업데이트해야 합니다.  이를 위해 사용자 지정 클레임 규칙이 사용자 지정 발급자 값을 생성할 때 사용자의 UPN 접미사에서 모든 하위 도메인을 제거하도록 구성해야 합니다.

다음 클레임이 작업을 수행합니다.

```    
c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
```

[!NOTE]
정규식에서 마지막 숫자는 루트 도메인에 있는 상위 도메인 수를 설정합니다. 여기서 bmcontoso.com이 사용되므로 2개의 상위 도메인이 필요합니다. 3개의 상위 도메인을 유지해야 한다면(예: corp.bmcontoso.com) 숫자는 3이 되었을 것입니다. 결과적으로 범위를 나타낼 수 있으며, 항상 최대 도메인 수와 일치하도록 매칭됩니다. "{2,3}"은 2~3개의 도메인(예: bmfabrikam.com 및 corp.bmcontoso.com)이 매칭됩니다.

하위 도메인을 지원하기 위해 사용자 지정 클레임을 추가하려면 다음 단계를 사용합니다.

1. AD FS 관리 열기
2. Microsoft 온라인 RP 트러스트를 마우스 오른쪽 단추로 클릭하고 편집 클레임 규칙 선택
3. 세 번째 클레임 규칙을 선택하고 ![클레임 편집](./media/how-to-connect-install-multiple-domains/sub1.png) 대체
4. 현재 클레임을 바꿉니다.

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   ```
    다음과 같이 바꿉니다.

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));
   ```

    ![클레임 바꾸기](./media/how-to-connect-install-multiple-domains/sub2.png)

5. 확인을 클릭합니다.  적용을 클릭합니다.  확인을 클릭합니다.  AD FS 관리를 닫습니다.

## <a name="next-steps"></a>다음 단계
Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](how-to-connect-post-installation.md)할 수 있습니다.

[자동 업그레이드](how-to-connect-install-automatic-upgrade.md), [실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](how-to-connect-health-sync.md)를 설치하여 사용할 수 있는 이러한 기능에 대해 자세히 알아봅니다.

공통 항목인 [스케줄러 및 동기화를 트리거하는 방법](how-to-connect-sync-feature-scheduler.md)에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
