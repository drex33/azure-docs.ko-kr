---
title: Azure AD Connect를 위한 선택적 암호 해시 동기화
description: 이 문서에서는 Azure AD Connect에서 사용할 선택적 암호 해시 동기화를 설정 및 구성하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbdc6a28af9bab40ede4f6a3a065b3ee776cf29
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235405"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Azure AD Connect를 위한 선택적 암호 해시 동기화 구성

[암호 해시 동기화](whatis-phs.md)는 하이브리드 ID를 설정하기 위해 사용되는 로그인 방법 중 하나입니다. Azure AD Connect는 사용자 암호 해시의 해시를 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 동기화합니다.  기본적으로 암호 해시 동기화가 설정되면 동기화하는 모든 사용자에 대해 암호 해시 동기화가 발생합니다.

일부 사용자의 암호 해시가 Azure AD에 동기화되지 못하게 제외하려면 이 문서에 제공된 단계별 안내에 따라 선택적 암호 해시 동기화를 구성하면 됩니다.

> [!IMPORTANT]
> Microsoft는 공식적으로 문서화된 구성 또는 작업 외의 Azure AD Connect 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 이러한 구성 또는 작업으로 인해 Azure AD Connect 동기화의 불일치 또는 지원되지 않는 상태가 발생할 수 있습니다. 따라서 Microsoft는 해당 배포에 대해 효율적인 기술 지원을 제공할 수 있다고 보장할 수 없습니다.

## <a name="consider-your-implementation"></a>구현 고려

구성 관리 부담을 줄이려면 먼저 암호 해시 동기화에서 제외하려는 사용자 개체 수를 고려해야 합니다. 아래 시나리오 중 상호 배타적인 시나리오, 요구 사항에 부합하는 시나리오를 확인하여 적합한 구성 옵션을 선택합니다.
- **제외할** 사용자 수가 **포함할** 사용자 수보다 **적은** 경우 이 [섹션의](#excluded-users-is-smaller-than-included-users) 단계를 따릅니다.
- **제외할** 사용자 수가 **포함할** 사용자 수보다 **많은** 경우 이 [섹션의](#excluded-users-is-larger-than-included-users) 단계를 따릅니다.

> [!IMPORTANT]
> 구성 옵션 중 하나를 선택하면 변경 내용을 적용하는 데 필요한 초기 동기화(전체 동기화)가 다음 동기화 주기 동안 자동으로 수행됩니다.

> [!IMPORTANT]
> 선택적 암호 해시 동기화를 구성하면 비밀번호 쓰기 저장에 직접적인 영향이 있습니다. Azure Active Directory에서 시작된 암호 변경 또는 암호 재설정은 사용자가 암호 해시 동기화 범위에 있는 경우에만 온-프레미스 Active Directory에 쓰기 저장됩니다.

### <a name="the-admindescription-attribute"></a>adminDescription 특성

두 시나리오는 모두 사용자의 adminDescription 특성을 특정 값으로 설정하는 방법을 사용합니다.  이렇게 하면 규칙이 적용되며, 선택적 PHS가 작동합니다.

|시나리오|adminDescription 값|
|-----|-----|
|제외할 사용자가 포함할 사용자보다 적음|PHSFiltered|
|제외할 사용자가 포함할 사용자보다 많음|PHSIncluded|

이 특성은 다음과 같은 방법으로 설정할 수 있습니다.

- Active Directory 사용자 및 컴퓨터 UI 사용
- `Set-ADUser` PowerShell cmdlet 사용  자세한 내용은 [Set-ADUser](/powershell/module/activedirectory/set-aduser)를 참조하세요.

### <a name="disable-the-synchronization-scheduler"></a>동기화 스케줄러를 사용하지 않도록 설정합니다.

두 시나리오를 시작하기 전에 동기화 규칙을 변경하는 동안 동기화 스케줄러를 사용하지 않도록 설정해야 합니다.
 1. Windows PowerShell을 시작하고 다음을 입력합니다.

     `set-adsyncscheduler-synccycleenabled$false`

2. 다음 cmdlet을 실행하여 스케줄러를 사용하지 않도록 설정했는지 확인합니다.

    `get-adsyncscheduler`

스케줄러에 대한 자세한 내용은 [Azure AD Connect 동기화 스케줄러](how-to-connect-sync-feature-scheduler.md)를 참조하세요.

## <a name="excluded-users-is-smaller-than-included-users"></a>제외할 사용자가 포함할 사용자보다 적음

다음 섹션에서는 **제외할** 사용자 수가 **포함할** 사용자 수보다 **적은** 경우 선택적 암호 해시 동기화를 사용하도록 설정하는 방법을 설명합니다.

> [!IMPORTANT]
> 계속하기 전에 위에 설명된 대로 동기화 스케줄러를 사용하지 않도록 설정해야 합니다.

- **암호 해시 동기화 사용 옵션을 선택 취소** 하고 **AD에서 들어오기 – User AccountEnabled** 의 편집 가능한 복사본을 만든 후 범위 지정 필터를 정의합니다.
- **암호 해시 동기화 사용 옵션을 선택** 하고 기본 **AD에서 들어오기 – User AccountEnabled** 의 또 다른 편집 가능한 복사본을 만든 후 범위 지정 필터를 정의합니다.
- 동기화 스케줄러를 다시 사용하도록 설정
- Active Directory에서 암호 해시 동기화에 허용하려는 사용자의 범위 특성으로 정의된 특성 값을 설정합니다.

> [!IMPORTANT]
> 선택적 암호 해시 동기화를 구성하기 위해 제공된 단계는 Active Directory에서 **PHSFiltered** 값으로 채워진 **adminDescription** 특성을 가진 사용자 개체에만 적용됩니다.
이 특성이 채워지지 않거나 값이 **PHSFiltered** 이외의 값이면 이러한 규칙이 사용자 개체에 적용되지 않습니다.

### <a name="configure-the-necessary-synchronization-rules"></a>필요한 동기화 규칙을 구성합니다.

 1. 동기화 규칙 편집기를 시작하고 **암호 동기화** 필터를 **켬** 으로 설정하고 **규칙 유형을** **표준** 으로 설정합니다.
     ![동기화 규칙 편집기 시작](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. 선택적 암호 해시 동기화를 구성하려는 Active Directory 포리스트 커넥터에 대해 **AD에서 들어오기 – User AccountEnabled** 규칙을 선택하고 **편집** 을 클릭합니다. 다음 대화 상자에서 **예** 를 선택하여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![규칙 선택](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. 첫 번째 규칙은 암호 해시 동기화를 사용하지 않도록 설정합니다. 새 사용자 지정 규칙에 **AD에서 들어오기 - User AccountEnabled - PHS에서 사용자 필터링** 이라는 이름을 지정합니다.
 우선 순위 값을 100보다 낮은 숫자(예: **90** 또는 사용자 환경에서 사용할 수 있는 가장 낮은 값)로 변경합니다.
 **암호 동기화 사용** 및 **사용 안 함** 확인란이 선택 취소되어 있는지 확인합니다.
 **다음** 을 클릭합니다.
  ![인바운드 편집](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. **범위 지정 필터** 에서 **절 추가** 를 클릭합니다.
 특성 열에서 **adminDescription** 을 선택하고 연산자 열에서 **EQUAL** 을 선택한 후 값으로 **PHSFiltered** 를 입력합니다.
     ![범위 지정 필터](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. 추가 변경은 필요하지 않습니다. 지금 **저장** 을 클릭할 수 있도록 **조인 규칙** 및 **변환** 을 복사된 기본 설정으로 유지해야 합니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행될 것임을 알리는 경고 대화 상자에서 **확인** 을 클릭합니다.
     ![규칙 저장](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. 다음으로, 암호 해시 동기화가 설정된 다른 사용자 지정 규칙을 만듭니다. 선택적 암호 해시 동기화를 구성하려는 Active Directory 포리스트에 대해 기본 규칙 **AD에서 들어오기 – User AccountEnabled** 를 다시 선택하고 **편집** 을 클릭합니다. 다음 대화 상자에서 **예** 를 선택하여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![사용자 지정 규칙](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. 새 사용자 지정 규칙에 **AD에서 들어오기 - User AccountEnabled - PHS에 포함할 사용자** 라는 이름을 지정합니다.
 우선 순위 값을 이전에 만든 규칙보다 적은 숫자로 변경합니다(이 예제에서는 **89**).
 **암호 동기화 사용** 확인란을 선택하고 **사용 안 함** 확인란을 선택 취소해야 합니다.
 **다음** 을 클릭합니다.  
     ![새 규칙 편집](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. **범위 지정 필터** 에서 **절 추가** 를 클릭합니다.
 특성 열에서 **adminDescription** 을 선택하고 연산자 열에서 **NOTEQUAL** 을 선택한 후 값으로 **PHSFiltered** 를 입력합니다.
     ![범위 규칙](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. 추가 변경은 필요하지 않습니다. 지금 **저장** 을 클릭할 수 있도록 **조인 규칙** 및 **변환** 을 복사된 기본 설정으로 유지해야 합니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행될 것임을 알리는 경고 대화 상자에서 **확인** 을 클릭합니다.
     ![조인 규칙](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. 규칙 만들기를 확인합니다. **암호 동기화** **켬** 및 **규칙 유형** **표준** 필터를 제거합니다. 방금 만든 새 규칙이 모두 표시되어야 합니다.
     ![규칙 확인](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png)

### <a name="re-enable-synchronization-scheduler"></a>동기화 스케줄러를 다시 사용하도록 설정합니다.

필요한 동기화 규칙을 구성하는 단계를 완료한 후에는 다음 단계에 따라 동기화 스케줄러를 다시 사용하도록 설정합니다.
 1. Windows PowerShell에서 다음을 실행합니다.

     `set-adsyncscheduler-synccycleenabled$true`

 2. 그리고 다음을 실행하여 성공적으로 설정되었는지 확인합니다.

     `get-adsyncscheduler`

스케줄러에 대한 자세한 내용은 [Azure AD Connect 동기화 스케줄러](how-to-connect-sync-feature-scheduler.md)를 참조하세요.

### <a name="edit-users-admindescription-attribute"></a>사용자 **adminDescription** 특성을 편집합니다.

모든 구성이 완료되면 Active Directory의 암호 해시 동기화에서 **제외** 하려는 모든 사용자의 **adminDescription** 특성을 편집하고, 범위 지정 필터 **PHSFiltered** 에 사용되는 문자열을 추가해야 합니다.

  ![특성 편집](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

또한 다음 PowerShell 명령을 사용하여 사용자의 **adminDescription** 특성을 편집할 수도 있습니다.

`set-adusermyuser-replace@{adminDescription="PHSFiltered"}`

## <a name="excluded-users-is-larger-than-included-users"></a>제외할 사용자가 포함할 사용자보다 많음

다음 섹션에서는 **제외할** 사용자 수가 **포함할** 사용자 수보다 **많은** 경우 선택적 암호 해시 동기화를 사용하도록 설정하는 방법을 설명합니다.

> [!IMPORTANT]
> 계속하기 전에 위에 설명된 대로 동기화 스케줄러를 사용하지 않도록 설정해야 합니다.

다음은 아래 단계에서 수행할 작업에 대한 요약입니다.

- **암호 해시 동기화 사용 옵션을 선택 취소** 하고 **AD에서 들어오기 – User AccountEnabled** 의 편집 가능한 복사본을 만든 후 범위 지정 필터를 정의합니다.
- **암호 해시 동기화 사용 옵션을 선택** 하고 기본 **AD에서 들어오기 – User AccountEnabled** 의 또 다른 편집 가능한 복사본을 만든 후 범위 지정 필터를 정의합니다.
- 동기화 스케줄러를 다시 사용하도록 설정
- Active Directory에서 암호 해시 동기화에 허용하려는 사용자의 범위 특성으로 정의된 특성 값을 설정합니다.

> [!IMPORTANT]
> 선택적 암호 해시 동기화를 구성하기 위해 제공된 단계는 Active Directory에서 **PHSIncluded** 값으로 채워진 **adminDescription** 특성을 가진 사용자 개체에만 적용됩니다.
이 특성이 채워지지 않거나 값이 **PHSIncluded** 이외의 값이면 이러한 규칙이 사용자 개체에 적용되지 않습니다.

### <a name="configure-the-necessary-synchronization-rules"></a>필요한 동기화 규칙을 구성합니다.

 1. 동기화 규칙 편집기를 시작하고 **암호 동기화** 필터를 **켬** 으로 설정하고 **규칙 유형** 을 **표준** 으로 설정합니다.
     ![규칙 유형](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. 선택적 암호 해시 동기화를 구성하려는 Active Directory 포리스트에 대해 **AD에서 들어오기 – User AccountEnabled** 규칙을 선택하고 **편집** 을 클릭합니다. 다음 대화 상자에서 **예** 를 선택하여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![AD에서 들어오기](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. 첫 번째 규칙은 암호 해시 동기화를 사용하지 않도록 설정합니다. 새 사용자 지정 규칙에 **AD에서 들어오기 - User AccountEnabled - PHS에서 사용자 필터링** 이라는 이름을 지정합니다.
 우선 순위 값을 100보다 낮은 숫자(예: **90** 또는 사용자 환경에서 사용할 수 있는 가장 낮은 값)로 변경합니다.
 **암호 동기화 사용** 및 **사용 안 함** 확인란이 선택 취소되어 있는지 확인합니다.
 **다음** 을 클릭합니다.
  ![우선 순위 설정](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. **범위 지정 필터** 에서 **절 추가** 를 클릭합니다.
특성 열에서 **adminDescription** 을 선택하고 연산자 열에서 **NOTEQUAL** 을 선택한 후 값으로 **PHSIncluded** 를 입력합니다.
     ![절 추가](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. 추가 변경은 필요하지 않습니다. 지금 **저장** 을 클릭할 수 있도록 **조인 규칙** 및 **변환** 을 복사된 기본 설정으로 유지해야 합니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행될 것임을 알리는 경고 대화 상자에서 **확인** 을 클릭합니다.
     ![변환](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. 다음으로, 암호 해시 동기화가 설정된 다른 사용자 지정 규칙을 만듭니다. 선택적 암호 해시 동기화를 구성하려는 Active Directory 포리스트에 대해 기본 규칙 **AD에서 들어오기 – User AccountEnabled** 를 다시 선택하고 **편집** 을 클릭합니다. 다음 대화 상자에서 **예** 를 선택하여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![User AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. 새 사용자 지정 규칙에 **AD에서 들어오기 - User AccountEnabled - PHS에 포함할 사용자** 라는 이름을 지정합니다.
 우선 순위 값을 이전에 만든 규칙보다 적은 숫자로 변경합니다(이 예제에서는 **89**).
 **암호 동기화 사용** 확인란을 선택하고 **사용 안 함** 확인란을 선택 취소해야 합니다.
 **다음** 을 클릭합니다.
     ![암호 동기화 사용](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. **범위 지정 필터** 에서 **절 추가** 를 클릭합니다.
 특성 열에서 **adminDescription** 을 선택하고 연산자 열에서 **EQUAL** 을 선택한 후 값으로 **PHSIncluded** 를 입력합니다.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. 추가 변경은 필요하지 않습니다. 지금 **저장** 을 클릭할 수 있도록 **조인 규칙** 및 **변환** 을 복사된 기본 설정으로 유지해야 합니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행될 것임을 알리는 경고 대화 상자에서 **확인** 을 클릭합니다.
     ![지금 저장](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10. 규칙 만들기를 확인합니다. **암호 동기화** **켬** 및 **규칙 유형** **표준** 필터를 제거합니다. 방금 만든 새 규칙이 모두 표시되어야 합니다.
     ![동기화 켜기](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>동기화 스케줄러를 다시 사용하도록 설정합니다.

필요한 동기화 규칙을 구성하는 단계를 완료한 후에는 다음 단계에 따라 동기화 스케줄러를 다시 사용하도록 설정합니다.

1. Windows PowerShell에서 다음을 실행합니다.

   `set-adsyncscheduler-synccycleenabled$true`

2. 그리고 다음을 실행하여 성공적으로 설정되었는지 확인합니다.

   `get-adsyncscheduler`

스케줄러에 대한 자세한 내용은 [Azure AD Connect 동기화 스케줄러](how-to-connect-sync-feature-scheduler.md)를 참조하세요.

### <a name="edit-users-admindescription-attribute"></a>사용자 **adminDescription** 특성을 편집합니다.

모든 구성이 완료되면 Active Directory의 암호 해시 동기화에서 **포함** 하려는 모든 사용자의 **adminDescription** 특성을 편집하고, 범위 지정 필터 **PHSIncluded** 에 사용되는 문자열을 추가해야 합니다.

  ![특성 편집](media/how-to-connect-selective-password-hash-synchronization/include-11.png)

또한 다음 PowerShell 명령을 사용하여 사용자의 **adminDescription** 특성을 편집할 수도 있습니다.

`Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}`

## <a name="next-steps"></a>다음 단계

- [암호 해시 동기화란?](whatis-phs.md)
- [암호 해시 동기화 작동 방법](how-to-connect-password-hash-synchronization.md)
