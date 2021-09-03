---
title: 'Azure AD Connect: 설계 개념 | Microsoft Docs'
description: 이 항목에서는 특정 구현 설계 영역을 자세히 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d55c8541bc0ef3fc73b9848c2fd51feb1ea99e9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535631"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: 설계 개념
이 문서의 목적은 Azure AD Connect를 구성하는 동안 고려해야 하는 영역을 설명하는 것입니다. 이 문서는 특정 영역을 심층 분석하고 이 개념을 다른 문서에서처럼 간단히 설명합니다.

## <a name="sourceanchor"></a>sourceAnchor
sourceAnchor 특성은 *개체의 수명 동안 변경할 수 없는 속성* 으로 정의됩니다. 온-프레미스와 Azure AD의 동일한 개체처럼 개체를 고유하게 식별합니다. 이 특성은 **immutableId** 라고도 하며 두 이름을 서로 바꿔 사용할 수 있습니다.

변경이 불가능한 말(즉, “변경할 수 없음”)은 이 문서에서 중요합니다. 이 특성의 값은 한번 설정된 후에는 변경할 수 없기 때문에 시나리오를 지원하는 설계를 선택하는 것이 중요합니다.

이 특성은 다음 시나리오에서 사용됩니다.

* 새 동기화 엔진 서버를 구축하거나 재해 복구 시나리오를 다시 구축할 때,이 특성은 개체 온-프레미스의 Azure AD 내의 기존 개체에 연결됩니다.
* 클라우드 전용 ID에서 동기화 ID 모델로 이동하면 이 특성은 온-프레미스 개체를 포함한 Azure AD의 “완전 일치”하는 기존 개체에 개체를 허용합니다.
* 페더레이션을 사용하면 **userPrincipalName** 을 포함한 특성은 사용자를 고유하게 식별하는 클레임에서 사용됩니다.

이 항목에서는 사용자와 관련이 있는 sourceAnchor만 설명합니다. 동일한 규칙이 모든 개체 형식에 적용되나 이 문제가 주로 관련이 있는 사용자에게만 해당됩니다.

### <a name="selecting-a-good-sourceanchor-attribute"></a>알맞은 sourceAnchor 특성을 선택합니다.
특성 값은 다음 규칙을 따라야 합니다.

* 길이는 60자 미만이어야 합니다.
  * 문자가 a-z, A-Z 또는 0-9가 아니면 인코드되고 3개의 문자로 계산됩니다.
* &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " \@ _
* 전역적으로 고유해야 합니다.
* 문자열, 정수 또는 이진이어야 합니다.
* 사용자 이름은 변경되므로 이를 기반으로 할 수 없습니다.
* 대/소문자를 구분할 수 없으며 대/소문자에 따라 달라질 수 있는 값을 사용하지 않도록 해야 합니다.
* 개체를 만들 때 할당되어야 합니다.

선택된 sourceAnchor가 문자열 형식이 아닌 경우 Azure AD Connect는 특성 값에 특수 문자가 나타나지 않도록 Base64로 인코딩합니다. ADFS 이외의 다른 페더레이션 서버를 사용할 경우 서버의 특성이 Base64Encode일 수 있는지 확인하세요.

SourceAnchor 특성은 대/소문자를 구분합니다. "JohnDoe"의 값은 "johndoe"와 다릅니다. 하지만 대/소문자만 다른 두 개체가 있어서는 안 됩니다.

단일 포리스트 온-프레미스가 있는 경우 사용해야 할 특성은 **objectGUID** 입니다. Azure AD Connect에서 사용되는 express 설정을 사용할 때 사용되는 특성이기도 하며 DirSync가 사용하는 특성이기도 합니다.

여러 포리스트가 있고 사용자를 포리스트 및 도메인 간에 이동하지 않는 경우 **objectGUID** 특성을 사용하는 것이 좋습니다.

포리스트 및 도메인 간에 사용자를 이동하는 경우 변경되지 않거나 이동 중 사용자와 함께 이동할 수 있는 특성을 찾아야 합니다. 가상 특성을 도입하는 것이 좋습니다. GUID처럼 보이는 것을 포함할 수 있는 특성이 적합합니다. 개체가 생성되는 동안 새 GUID는 생성되고 사용자에게 표시됩니다. 동기화 엔진 서버에서 사용자 지정 동기화 규칙을 만들어 **objectGUID** 에 따라 이 값을 만들고 ADDS에서 선택한 특성을 업데이트할 수 있습니다. 개체를 이동할 때, 이 값의 콘텐츠도 복사했는지 확인합니다.

다른 솔루션은 변경되지 않는 것을 알고 있는 기존 특성을 선택하는 것입니다. 일반적으로 사용되는 특성에는 **employeeID** 가 있습니다. 문자를 포함하는 특성을 고려하는 경우 특성 값의 대/소문자가 변경될 수 있는 가능성이 없는지 확인합니다. 나쁜 속성은 사용자 이름의 해당 속성을 포함하여 사용할 수 없습니다. 결혼 또는 이혼해서 이름을 바꿔야 하는 경우에도 이 특성에서는 허용되지 않습니다. **userPrincipalName**, **mail** 및 **targetAddress** 와 같은 특성을 Azure AD Connect 설치 마법사에서 선택할 수 없는 이유이기도 합니다. 이러한 특성은 sourceAnchor에서 허용되지 않는 “\@” 문자도 포함하고 있습니다.

### <a name="changing-the-sourceanchor-attribute"></a>SourceAnchor 특성 변경
sourceAnchor 특성값은 개체가 Azure AD에 생성되고 ID가 동기화된 후에는 값을 변경할 수 없습니다.

이러한 이유로 Azure AD Connect에는 다음과 같은 제한사항이 적용됩니다.

* SourceAnchor 특성은 오직 초기 설치 동안에만 설정할 수 있습니다. 설치 마법사를 다시 실행하는 경우 이 옵션은 읽기 전용입니다. 이 설정을 변경해야 할 경우 제거하고 다시 설치해야 합니다.
* 다른 Azure AD Connect 서버를 설치한 경우 이전에 사용했던 동일한 sourceAnchor 특성을 선택해야만 합니다. 이전부터 DirSync를 사용했으며 Azure AD Connect로 이동한 경우 DirSync에서 사용했던 특성이므로 **objectGUID** 를 사용해야 합니다.
* 개체를 Azure AD로 내보낸 후 sourceAnchor 값이 변경된 경우 Azure AD Connect 동기화는 오류가 발생하고 문제가 해결되거나 sourceAnchor를 소스 디렉터리에 원상 복귀시키기 전까지는 어떠한 변경도 허용하지 않습니다.

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>ms-DS-ConsistencyGuid를 sourceAnchor로 사용
기본적으로 Azure AD Connect(버전 1.1.486.0 이상)는 objectGUID를 sourceAnchor 특성으로 사용합니다. ObjectGUID는 시스템에서 생성됩니다. 온-프레미스 AD 개체를 만들 때는 해당 값을 지정할 수 없습니다. [sourceAnchor](#sourceanchor) 섹션에서 설명한 대로 sourceAnchor 값을 지정해야 하는 시나리오가 있습니다. 시나리오를 적용할 수 있는 경우 구성 가능한 AD 특성(예: ms-DS-ConsistencyGuid)을 sourceAnchor 특성으로 사용해야 합니다.

Azure AD Connect(버전 1.1.524.0 이상)는 이제 ms-DS-ConsistencyGuid를 sourceAnchor 특성으로 더 쉽게 사용할 수 있게 합니다. 이 기능을 사용하면 Azure AD Connect에서 동기화 규칙을 다음과 같이 자동으로 구성합니다.

1. User 개체에 대한 sourceAnchor 특성으로 ms-DS-ConsistencyGuid를 사용합니다. ObjectGUID는 다른 개체 형식에 사용됩니다.

2. ms-DS-ConsistencyGuid 특성이 채워지지 않은 지정된 모든 온-프레미스 AD User 개체의 경우 Azure AD Connect는 해당 objectGUID 값을 온-프레미스 Active Directory의 ms-DS-ConsistencyGuid 특성에 다시 씁니다. ms-DS-ConsistencyGuid 특성이 채워지면 Azure AD Connect는 개체를 Azure AD로 내보냅니다.

>[!NOTE]
> 온-프레미스 AD 개체를 Azure AD Connect로 가져오면(즉, AD 커넥터 공간으로 가져와서 메타버스에 투영하면) sourceAnchor 값을 더 이상 변경할 수 없습니다. 지정된 온-프레미스 AD 개체에 대한 sourceAnchor 값을 지정하려면 Azure AD Connect로 가져오기 전에 해당 ms-DS-ConsistencyGuid 특성을 구성합니다.

### <a name="permission-required"></a>필요한 권한
이 기능을 사용하려면 온-프레미스 Active Directory와 동기화하는 데 사용되는 AD DS 계정에 온-프레미스 Active Directory의 ms-DS-ConsistencyGuid 특성에 대한 쓰기 권한을 부여해야 합니다.

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>ConsistencyGuid 기능을 사용하도록 설정하는 방법 - 새로 설치
새로 설치하는 동안 sourceAnchor로 ConsistencyGuid 사용을 설정할 수 있습니다. 이 섹션에서는 기본 설치 및 사용자 지정 설치를 자세히 설명합니다.

  >[!NOTE]
  > 최신 버전의 Azure AD Connect(1.1.524.0 이상)만 새로 설치하는 동안 sourceAnchor로 ConsistencyGuid 사용을 지원합니다.

### <a name="how-to-enable-the-consistencyguid-feature"></a>ConsistencyGuid 기능을 사용하도록 설정하는 방법

#### <a name="express-installation"></a>기본 설치
Azure AD Connect를 기본 모드로 설치하는 경우 Azure AD Connect 마법사에서 다음 논리를 사용하여 sourceAnchor 특성으로 사용할 가장 적절한 AD 특성을 자동으로 결정합니다.

* 먼저 Azure AD Connect 마법사에서 Azure AD 테넌트를 쿼리하여 이전 Azure AD Connect 설치(있는 경우)에서 sourceAnchor 특성으로 사용된 AD 특성을 검색합니다. 이 정보를 사용할 수 있으면 Azure AD Connect는 동일한 AD 특성을 사용합니다.

  >[!NOTE]
  > 최신 버전의 Azure AD Connect(1.1.524.0 이상)만 설치하는 동안 사용된 sourceAnchor 특성에 대한 정보를 Azure AD 테넌트에 저장합니다. 이전 버전의 Azure AD Connect는 그렇지 않습니다.

* 사용되는 sourceAnchor 특성에 대한 정보를 사용할 수 없는 경우 마법사는 온-프레미스 Active Directory의 ms-DS-ConsistencyGuid 특성의 상태를 확인합니다. 특성이 디렉터리에 있는 모든 개체에 구성되어 있지 않으면, 마법사는 ms-DS-ConsistencyGuid를 sourceAnchor 특성으로 사용합니다. 특성이 디렉터리에 있는 하나 이상의 개체에 구성되어 있으면, 마법사에서 특성이 다른 애플리케이션에서 사용되고 있으며 sourceAnchor 특성으로 적합하지 않다고 결정합니다.

* 이 경우 마법사는 objectGUID를 sourceAnchor 특성으로 사용합니다.

* sourceAnchor 특성이 결정되면 마법사는 Azure AD 테넌트에 해당 정보를 저장합니다. 이 정보는 나중에 Azure AD Connect를 설치할 때 사용됩니다.

기본 설치가 완료되면 마법사에서 원본 앵커 특성으로 선택된 특성을 사용자에게 알려줍니다.

![sourceAnchor로 선택된 AD 특성을 알려주는 마법사](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>사용자 지정 설치
Azure AD Connect를 사용자 지정 모드로 설치하는 경우 Azure AD Connect 마법사에서 sourceAnchor 특성을 구성할 때 다음 두 가지 옵션을 제공합니다.

![사용자 지정 설치 - sourceAnchor 구성](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| 설정 | Description |
| --- | --- |
| Let Azure manage the source anchor for me(Azure에서 원본 앵커를 대신 관리) | Azure AD에서 이 특성을 선택하게 하려면 이 옵션을 선택합니다. 이 옵션을 선택하면 Azure AD Connect 마법사에서 [기본 설치에서 사용된 것과 동일한 sourceAnchor 특성 선택 논리](#express-installation)를 적용합니다. 기본 설치와 마찬가지로 사용자 정의 설치가 완료되면 마법사에서 원본 앵커 특성으로 선택된 특성을 사용자에게 알려줍니다. |
| 특정 특성 | 기존 AD 특성을 sourceAnchor 특성으로 지정하려면 이 옵션을 선택합니다. |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>ConsistencyGuid 기능을 사용하도록 설정하는 방법 - 기존 배포
Source Anchor 특성으로 objectGUID를 사용하는 기존 Azure AD Connect 배포가 있는 경우 ConsistencyGuid를 대신 사용하도록 전환할 수 있습니다.

>[!NOTE]
> 최신 버전의 Azure AD Connect(1.1.552.0 이상)만 Source Anchor 특성으로 ObjectGuid에서 ConsistencyGuid로의 전환을 지원합니다.

Source Anchor 특성으로 objectGUID에서 ConsistencyGuid로 전환하려면:

1. Azure AD Connect 마법사를 시작하고 **구성** 을 클릭하여 작업 화면으로 이동합니다.

2. **Source Anchor 구성** 작업 옵션을 선택하고 **다음** 을 클릭합니다.

   ![기존 배포에 대해 ConsistencyGuid 사용 - 2단계](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Azure AD 관리자 자격 증명을 입력하고 **다음** 을 클릭합니다.

4. Azure AD Connect 마법사는 온-프레미스 Active Directory의 ms-DS-ConsistencyGuid 특성 상태를 분석합니다. 특성이 디렉터리의 개체에서 구성되지 않는 경우 Azure AD Connect는 다른 애플리케이션이 현재 특성을 사용하지 않고 있으며 Source Anchor 특성으로 사용하는 데 안전하다고 판단합니다. **다음** 을 클릭하여 계속합니다.

   ![기존 배포에 대해 ConsistencyGuid 사용 - 4단계](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. **구성할 준비 완료** 화면에서 **구성** 을 클릭하여 구성을 변경합니다.

   ![기존 배포에 대해 ConsistencyGuid 사용 - 5단계](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. 구성이 완료되면 마법사는 ms-DS-ConsistencyGuid가 Source Anchor 특성으로 사용되고 있음을 나타냅니다.

   ![기존 배포에 대해 ConsistencyGuid 사용 - 6단계](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

분석하는 동안(4단계) 특성이 디렉터리에 있는 하나 이상의 개체에 구성되어 있으면, 마법사에서 특성이 다른 애플리케이션에서 사용되고 있다고 결정하고 아래 그림과 같은 오류를 반환합니다. 주 Azure AD Connect 서버에서 ConsistencyGuid 기능을 이전에 설정하고 준비 서버에서 동일한 작업을 수행하려고 하는 경우 이 오류가 발생할 수 있습니다.

![기존 배포에 대해 ConsistencyGuid 사용 - 오류](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 특성이 다른 기존 애플리케이션에서 사용되지 않는 것이 확실한 경우 지정된 **/SkipLdapSearch** 스위치를 통해 Azure AD Connect 마법사를 다시 시작하여 오류를 억제할 수 있습니다. 이렇게 하려면 명령 프롬프트 창에서 다음 명령을 실행합니다.

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>AD FS 또는 타사 페더레이션 구성에 미치는 영향
Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우, Azure AD Connect에서 클레임 규칙을 자동으로 업데이트하여 sourceAnchor와 동일한 AD 특성을 사용합니다. 이렇게 하면 AD FS에서 생성된 ImmutableID 클레임이 Azure AD로 내보낸 sourceAnchor 값과 일치하게 됩니다.

Azure AD Connect 외부에서 AD FS를 관리하거나 타사 페더레이션 서버를 사용하여 인증하는 경우, [AD FS 클레임 규칙 수정](./how-to-connect-fed-management.md#modclaims) 문서 섹션에서 설명한 대로 Azure AD로 내보낸 sourceAnchor 값과 일치하도록 ImmutableID 클레임에 대한 클레임 규칙을 수동으로 업데이트해야 합니다. 설치가 완료되면 마법사에서 다음과 같은 경고를 반환합니다.

![타사 페더레이션 구성](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>기존 배포에 새 디렉터리 추가
활성화된 ConsistencyGuid 기능으로 Azure AD Connect를 배포한 후에 이제 다른 디렉터리를 배포에 추가하려고 한다고 가정합니다. 디렉터리를 추가하려고 하는 경우 Azure AD Connect 마법사에서 디렉터리의 ms-DS-ConsistencyGuid 특성 상태를 확인합니다. 특성이 디렉터리에 있는 하나 이상의 개체에 구성되어 있으면, 마법사에서 특성이 다른 애플리케이션에서 사용되고 있다고 결정하고 아래 그림과 같은 오류를 반환합니다. 기존 애플리케이션에서 특성을 사용하지 않는 것이 확실한 경우 위에서 설명한 **/SkipLdapSearch** 스위치를 통해 Azure AD Connect 마법사를 다시 시작하여 오류를 억제할 수 있습니다. 자세한 내용은 지원팀에 문의하세요.

![기존 배포에 새 디렉터리 추가](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD 로그인
Azure AD와 온-프레미스 디렉터리를 통합하는 동안 동기화 설정이 사용자가 인증하는 방식에 어떤 영향을 주는지를 이해해야 합니다. Azure AD는 UPN(userPrincipalName)을 사용하여 사용자를 인증합니다. 그러나 사용자를 동기화할 경우 신중하게 userPrincipalName의 값에 사용할 특성을 선택해야 합니다.

### <a name="choosing-the-attribute-for-userprincipalname"></a>userPrincipalName의 특성 선택
Azure에서 사용할 UPN의 값을 제공하기 위해 특성을 선택하는 경우 다음을 확인해야 합니다.

* 특성 값은 UPN 구문(RFC 822)을 맞춥니다. 즉, 사용자 이름\@도메인 형식이어야 합니다.
* 값의 접미사는 Azure AD에서 확인된 사용자 지정 도메인 중 하나에 일치합니다.

Express 설정에서 특성에 userPrincipalName을 선택할 것입니다. userPrincipalName 특성에 사용자가 Azure에 로그인하는 데 사용하려는 값이 포함되지 않은 경우 **사용자 지정 설치** 를 선택해야 합니다.

### <a name="custom-domain-state-and-upn"></a>사용자 지정 도메인 상태 및 UPN
UPN 접미사에 확인된 도메인이 있는지 확인해야 합니다.

John은 contoso.com의 사용자입니다. 사용자를 Azure AD 디렉터리 contoso.onmicrosoft.com에 동기화한 후에 John이 Azure에 로그인하는 데 온-프레미스 UPN john\@contoso.com을 사용하려고 합니다. 이렇게 하려면 사용자를 동기화하기 전에 Azure AD에서 contoso.com을 사용자 지정 도메인으로 추가하고 확인해야 합니다. John의 UPN 접미사(예: contoso.com)가 Azure AD에서 확인된 도메인과 일치하지 않으면 Azure AD는 UPN 접미사를 contoso.onmicrosoft.com으로 바꿉니다.

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>라우팅할 수 있는 온-프레미스가 아닌 도메인 및 Azure AD에 대한 UPN
일부 조직에는 라우팅할 수 없는 도메인(예: contoso.local) 또는 간단한 단일 레이블 도메인(예: contoso)이 있습니다. Azure AD에서 라우팅할 수 없는 도메인을 확인할 수 없습니다. Azure AD Connect는 Azure AD에서 확인된 도메인에 동기화할 수 있습니다. Azure AD 디렉터리를 만들면 라우팅 가능한 도메인을 만들고 다시 Azure AD에 대한 기본 도메인(예: contoso.onmicrosoft.com)이 됩니다. 따라서 기본 도메인(onmicrosoft.com)에 동기화하지 않으려는 경우 이 시나리오에서 라우팅할 수 있는 다른 도메인을 확인하는 데 필요하게 됩니다.

도메인을 추가하고 확인하는 방법에 대한 자세한 내용은 [Azure Active Directory에 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md) 를 참조하세요.

Azure AD Connect는 라우팅할 수 없는 도메인 환경에서 실행하는지를 검색하고 Express 설정을 계속 진행하는 경우 적절하게 경고합니다. 라우팅할 수 없는 도메인에서 작업하는 경우 사용자의 UPN이 라우팅할 수 없는 접미사를 포함할 가능성이 있습니다. 예를 들어 contoso.local에서 실행하는 경우 Azure AD Connect는 Express 설정을 사용하지 않고 사용자 지정 설정을 사용하는 것이 좋습니다. 사용자 지정 설정을 사용하면 사용자가 Azure AD에 동기화된 후에 Azure에 로그인하기 위해 UPN으로 사용해야 하는 특성을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
