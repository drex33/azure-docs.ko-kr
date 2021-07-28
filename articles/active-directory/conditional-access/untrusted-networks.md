---
title: 신뢰할 수 없는 네트워크에서 MFA 필요 - Azure Active Directory
description: Azure AD(Azure Active Directory)에서 신뢰할 수 없는 네트워크에서의 액세스 시도에 대한 조건부 액세스 정책을 구성하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077679"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>방법: 조건부 액세스를 사용하는 신뢰할 수 없는 네트워크에서의 액세스에 대한 MFA 필요   

Azure AD(Azure Active Directory)에서는 어디에서든지 디바이스, 앱 및 서비스에 대해 Single Sign-On을 수행할 수 있습니다. 사용자는 조직의 네트워크뿐만 아니라 신뢰할 수 없는 인터넷 위치에서도 클라우드 앱에 액세스할 수 있습니다. 신뢰할 수 없는 네트워크에서의 액세스에 대한 일반적인 모범 사례는 MFA(다단계 인증)를 요구하는 것입니다.

이 문서에서는 신뢰할 수 없는 네트워크에서의 액세스에 대한 MFA가 필요하도록 조건부 액세스 정책을 구성하는 데 필요한 정보를 제공합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 조건부 액세스의 [기본 개념](overview.md)을 잘 알고 있다고 가정합니다. 

## <a name="scenario-description"></a>시나리오 설명

보안과 생산성 간의 균형을 유지하기 위해 조직의 네트워크에서 로그인하는 경우에만 암호를 요구하는 것으로 충분할 수 있습니다. 그러나 신뢰할 수 없는 네트워크 위치에서 액세스하는 경우 로그인이 합법적인 사용자에 의해 수행되지 않을 위험이 증가합니다. 이 문제를 해결하기 위해 신뢰할 수 없는 네트워크의 액세스 시도를 차단할 수 있습니다. 또는 시도가 합법적인 계정의 소유자에 의해 이루어졌다는 추가 보증을 다시 얻기 위해 MFA(다단계 인증)를 요구할 수도 있습니다. 

Azure AD 조건부 액세스를 사용하면 액세스 권한을 부여하는 단일 정책으로 이 요구 사항을 처리할 수 있습니다. 

- 선택한 클라우드 앱으로
- 선택한 사용자 및 그룹에 대해  
- 다단계 인증 요구 
- 다음 위치에서 액세스하는 경우: 
   - 신뢰할 수 없는 위치

## <a name="implementation"></a>구현

이 시나리오의 과제는 ‘신뢰할 수 없는 네트워크 위치에서의 액세스’를 조건부 액세스 조건으로 변환하는 것입니다. 조건부 액세스 정책에서 네트워크 위치와 관련된 시나리오를 처리하도록 [위치 조건](location-condition.md)을 구성할 수 있습니다. 위치 조건을 통해 IP 주소 범위, 국가 및 지역의 논리적 그룹화인 명명된 위치를 선택할 수 있습니다.  

일반적으로 조직은 하나 이상의 주소 범위(예: 199.30.16.0~199.30.16.15)를 소유합니다.
다음으로 명명된 위치를 구성할 수 있습니다.

- 해당 범위(199.30.16.0/28) 지정 
- **회사 네트워크** 와 같은 설명이 포함된 이름 할당 

신뢰할 수 없는 모든 위치를 지정하도록 시도하는 대신 다음을 수행할 수 있습니다.

- 모든 위치 포함 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="구성이 예로 설정되고, 포함 탭이 표시되며, 모든 위치 옵션이 선택되어 강조 표시된 Azure AD 위치 창 스크린샷" border="false":::

- 신뢰할 수 있는 모든 위치 제외 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="구성이 예로 설정되고, 제외 탭이 표시되며, 신뢰할 수 있는 모든 위치 옵션이 선택된 Azure AD 위치 창 스크린샷" border="false":::

## <a name="policy-deployment"></a>정책 배포

이 문서에 설명된 방법을 사용하여 이제 신뢰할 수 없는 위치에 대한 조건부 액세스 정책을 구성할 수 있습니다. 정책이 예상대로 작동하는지 확인하는 데 권장되는 모범 사례는 프로덕션에 배포하기 전에 테스트하는 것입니다. 테스트 테넌트를 사용하여 새 정책이 의도한 대로 작동하는지 확인하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

조건부 액세스에 대한 자세한 내용은 [Azure Active Directory의 조건부 액세스란?](./overview.md)을 참조하세요.
