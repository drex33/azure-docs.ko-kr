---
title: Azure AD Connect 클라우드 동기화를 통해 UserType 매핑 사용
description: 이 문서에서는 클라우드 동기화를 사용하여 UserType 특성을 매핑하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b06381472549fd7d9b3f3b5dcd222fcd96f4f15
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506208"
---
# <a name="map-usertype-with-cloud-sync"></a>클라우드 동기화를 사용하여 UserType 매핑

클라우드 동기화는 사용자 개체에 대한 **UserType** 특성의 동기화를 지원합니다.

기본적으로 해당하는 **UserType** 특성이 온-프레미스 Active Directory에 없기 때문에 동기화에 대해 **UserType** 특성을 사용하도록 설정되지 않습니다. 동기화를 위해 이 매핑을 수동으로 추가해야 합니다. 이 단계를 수행하기 전에 Azure AD(Azure Active Directory)에서 적용한 다음 동작을 기록해야 합니다.

- Azure AD는 **UserType** 특성에 대해 멤버 및 게스트라는 두 값만 허용합니다.
- **UserType** 특성이 클라우드 동기화에 매핑되지 않은 경우 디렉터리 동기화를 통해 만든 Azure AD 사용자는 **UserType** 특성이 멤버로 설정됩니다.

**UserType** 특성에 대한 매핑을 추가하기 전에 먼저 이 특성이 온-프레미스 Active Directory에서 파생되는 방법을 결정해야 합니다. 다음 방식이 가장 일반적입니다.

 - 사용하지 않는 온-프레미스 Active Directory 특성(예: extensionAttribute1)을 원본 특성으로 사용하도록 지정합니다. 지정된 온-프레미스 Active Directory 특성은 문자열 형식이고, 단일 값이며, 값 멤버 또는 게스트를 포함해야 합니다.
 - 이 방식을 선택한 경우 **UserType** 특성의 동기화를 사용하도록 설정하기 전에 지정된 특성이 Azure AD에 동기화된 온-프레미스 Active Directory의 모든 기존 사용자 개체에 대해 올바른 값으로 채워져 있는지 확인해야 합니다.

## <a name="add-the-usertype-mapping"></a>UserType 매핑 추가
**UserType** 매핑을 추가하려면 다음을 수행합니다.

 1. Azure Portal에서 **Azure Active Directory** 를 선택합니다.
 1. **Azure AD Connect** 를 선택합니다.
 1. **클라우드 동기화 관리** 를 선택합니다.
 1. **구성** 아래에서 구성을 선택합니다.
 1. **특성 관리** 에서 **매핑을 편집하려면 클릭** 을 선택합니다.
 
    ![특성 매핑 편집을 보여주는 스크린샷.](media/how-to-map-usertype/usertype-1.png) 

 1. **특성 매핑 추가** 를 선택합니다.
 
    ![새 특성 매핑 추가를 보여주는 스크린샷.](media/how-to-map-usertype/usertype-2.png) 
1. 매핑 유형을 선택합니다. 다음 세 가지 방법 중 하나로 매핑을 수행할 수 있습니다.
   - 예를 들어 Active Directory 특성의 직접 매핑
   - IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member")와 같은 식
   - 예를 들어 상수는 모든 사용자 개체를 게스트로 지정합니다.
 
     ![UserType 특성 추가를 보여주는 스크린샷.](media/how-to-map-usertype/usertype-3.png)

1. **대상 특성** 드롭다운 상자에서 **UserType** 을 선택합니다.
1. 페이지 하단에서 **적용** 을 선택하여 Azure AD **UserType** 특성에 대한 매핑을 만듭니다.

## <a name="next-steps"></a>다음 단계 

- [Azure Active Directory에서 특성 매핑에 대한 식 작성](reference-expressions.md)
- [클라우드 동기화 구성](how-to-configure.md)
