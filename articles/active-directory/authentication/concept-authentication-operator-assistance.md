---
title: Azure Active Directory의 운영자 지원
description: Azure Active Directory의 운영자 지원 기능 사용 중단에 대해 알아보기
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: justinha
author: rckyplln
manager: daveba
ms.reviewer: ripull
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c00fb3c68955a888bbc383fe59aef8f7ed75835
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273635"
---
# <a name="how-to-enable-and-disable-operator-assistance"></a>운영자 지원을 사용하거나 사용하지 않도록 설정하는 방법

운영자 지원은 운영자가 자동 전송 대신 전화 통화를 수동으로 전송할 수 있도록 하는 Azure AD 내의 기능입니다. 이 설정을 사용하도록 설정하면 사무실 전화 번호로 전화를 걸고 응답하면 시스템에서 운영자에게 지정된 내선 번호로 통화를 전환하도록 요청합니다.

운영자 지원은 전체 테넌트 또는 개별 사용자에 대해 사용하도록 설정할 수 있습니다. 설정이 **On** 이면 운영자 지원을 위해 전체 테넌트가 활성화됩니다. **전화 통화** 를 기본 방법으로 선택하고 내선 번호가 사무실 전화 번호(**x** 로 설명)의 일부로 지정된 경우 운영자가 수동으로 전화 통화를 전송할 수 있습니다.

예를 들어 미국에 있는 고객이 사무실 전화 번호 425-555-1234x5678을 가지고 있다고 가정해 보겠습니다. 운영자 지원을 사용하도록 설정하면 시스템에서 425-555-1234로 전화를 거는 것입니다. 응답하면 고객(운영자라고도 함)에게 내선 번호 5678로 통화를 전환해야 합니다. 전송되고 응답되면 시스템은 일반 MFA 프롬프트를 받아 승인을 기다립니다.

설정이 **Off** 인 경우 시스템은 자동으로 전화 번호의 일부인 내선 번호로 거는 것입니다. 관리자는 확장자에 '@'를 접두사로 지정하여 운영자 지원을 사용하도록 설정해야 하는 개별 사용자를 계속 지정할 수 있습니다. 예를 들어 425-555-1234x@5678은 설정이 **Off** 인 경우에도 연산자 지원을 사용해야 함을 나타냅니다.

[Azure AD 포털](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade)로 이동한 다음, 왼쪽 창에서 **보안** > **MFA** > **전화 통화 설정** 을 클릭하여 자체 테넌트에서 이 기능의 상태를 확인할 수 있습니다. **내선 번호를 전송하는 데 필요한 운영자** 를 확인하여 설정이 **On** 또는 **Off** 인지 확인합니다. 

![운영자 지원 설정의 스크린샷](./media/concept-authentication-operator-assistance/settings.png)

다음 지침을 사용하여 안정성, 보안을 개선하고 원활한 MFA 환경을 만들 수 있습니다.

- Multi-Factor Authentication 또는 셀프 서비스 암호 재설정(사용하도록 설정한 경우)에 사용할 [직접 전화 번호](https://aka.ms/mfasetup)(내선 없음) 또는 [기타 방법](concept-authentication-methods.md)을 등록했습니다. 
- 관리자가 사용자를 대신하여 [Multi-Factor Authentication](howto-mfa-userdevicesettings.md#add-authentication-methods-for-a-user) 또는 [셀프 서비스 암호 재설정](tutorial-enable-sspr.md)(사용하도록 설정한 경우)에 사용할 직접 전화 번호(내선 없음)를 등록했습니다. 
- 전화 시스템은 자동화된 교환 기능을 지원합니다. 
 
