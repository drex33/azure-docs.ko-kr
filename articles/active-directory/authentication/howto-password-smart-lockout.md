---
title: 스마트 잠금을 사용하여 공격 방지 - Azure Active Directory
description: 스마트 잠금을 Azure Active Directory 사용자 암호를 추측하려는 무차별 암호 대입 공격으로부터 조직을 보호하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77a0a8f1a02fa970965d3393dada2a7720ab3e4
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821377"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Azure Active Directory 스마트 잠금을 사용하여 공격으로부터 사용자 계정 보호

스마트 잠금을 사용하면 사용자의 암호를 추측하거나 무차별 암호 대입 공격 방법을 사용하여 로그인하려는 잘못된 행위자를 잠글 수 있습니다. 스마트 잠금을 사용하면 유효한 사용자의 로그인을 인식하고 공격자 및 기타 알 수 없는 원본 중 하나와 다르게 취급할 수 있습니다. 공격자는 잠기지만 사용자는 계속해서 계정에 액세스하여 생산성을 유지할 수 있습니다.

## <a name="how-smart-lockout-works"></a>스마트 잠금 작동 방법

기본적으로 스마트 잠금은 Azure 공용 테넌트에서 10번의 시도가 실패한 후 1분 동안 로그인 시도에서 계정을 잠그고, Azure 미국 정부 테넌트에서는 3번 로그인을 시도하지 못하도록 합니다. 후속 로그인 시도가 실패할 때마다 계정이 다시 잠기는데, 처음에는 1분간 잠기고 그 이후에는 더 길게 잠깁니다. 공격자가 이 동작을 해결할 수 있는 방법을 최소화하기 위해 추가 로그인 시도 실패에 대해 잠금 기간이 증가하는 속도를 공개하지 않습니다.

스마트 잠금 기능은 동일한 암호에 대해 잠금 카운터가 증가하는 것을 방지하기 위해 마지막 세 개의 잘못된 암호 해시를 추적합니다. 누군가가 동일한 잘못된 암호를 여러 번 입력하는 경우 이 동작으로 인해 계정이 잠기지 않습니다.

> [!NOTE]
> 통과 인증을 사용하는 고객은 클라우드에서 온-프레미스에서 인증을 수행하지 않으므로 해시 추적 기능을 사용할 수 없습니다.

AD FS 2016 및 AF FS 2019를 사용하는 페더레이션된 배포는 [AD FS 엑스트라넷 잠금 및 엑스트라넷 스마트 잠금](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)을 사용하여 유사한 이점을 제공할 수 있습니다.

스마트 잠금은 보안 및 유용성의 적절한 혼합을 제공하는 기본 설정으로 모든 Azure AD 고객에 대해 항상 활성 상태입니다. 조직에 특수한 값이 있는 스마트 잠금 설정의 사용자 지정에는 Azure AD Premium P1 또는 사용자에 대한 높은 라이선스가 필요합니다.

스마트 잠금 사용은 정상적 사용자가 절대 잠기지 않는다고 보장하지 않습니다. 스마트 잠금이 사용자 계정을 잠그는 경우 Azure는 정상적 사용자가 잠기지 않도록 최선을 다합니다. 잠금 서비스는 악의적 행위자가 진정한 사용자 계정에 액세스할 수 없도록 합니다. 고려 사항은 다음과 같습니다.

* 각 Azure AD 데이터 센터는 잠금을 독립적으로 추적합니다. 사용자가 각 데이터 센터에 방문하면 사용자는 (*threshold_limit * datacenter_count*) 시도 횟수를 갖게 됩니다.
* 스마트 잠금은 익숙한 위치 및 낯선 위치를 사용하여 악의적 행위자와 진정한 사용자를 구별합니다. 낯선 위치 및 익숙한 위치에는 별도의 잠금 카운터가 있습니다.

스마트 잠금은 암호 해시 동기화 또는 통과 인증을 사용하여 공격자가 온-프레미스 AD DS(Active Directory Domain Services) 계정을 잠그지 않도록 보호하는 하이브리드 배포와 통합될 수 있습니다. Azure AD에서 스마트 잠금 정책을 적절하게 설정하여 공격자가 온-프레미스 AD DS에 도달하기 전에 필터링할 수 있습니다.

[통과 인증](../hybrid/how-to-connect-pta.md)을 사용하는 경우 다음 사항을 고려해야 합니다.

* Azure AD 잠금 임계값이 AD DS 계정 잠금 임계값 **보다 작습니다**. AD DS의 계정 잠금 임계값을 Azure AD의 잠금 임계값보다 최소 2~3배 이상 크게 설정합니다.
* Azure AD 잠금 기간은 AD DS 초기화 계정 잠금 해제 카운터 기간보다 길게 설정해야 합니다. Azure AD 기간은 초 단위로 설정되고 AD 기간은 분 단위로 설정됩니다.

예를 들어 Azure AD 스마트 잠금 기간이 AD DS보다 높은 경우, 온-프레미스 AD는 1분(60초)으로 설정되고, Azure AD는 120초(2분)가 됩니다. Azure AD 잠금 임계값을 5로 설정하려면 온-프레미스 AD 잠금 임계값은 10으로 설정해야 합니다.  이 구성은 스마트 잠금이 Azure AD 계정에 대한 무차별 암호 대입 공격에 의해 온-프레미스 AD 계정이 잠기지 않도록 합니다.

> [!IMPORTANT]
> 현재로서는 사용자의 클라우드 계정이 스마트 잠금 기능에 의해 잠긴 경우 관리자가 잠금을 해제할 수 없습니다. 잠금 기간이 만료될 때까지 기다려야 합니다. 그러나 사용자는 신뢰할 수 있는 디바이스 또는 위치에서 SSPR(셀프 서비스 암호 재설정)을 사용하여 잠금을 해제할 수 있습니다.

## <a name="verify-on-premises-account-lockout-policy"></a>온-프레미스 계정 잠금 정책 확인

온-프레미스 AD DS 계정 잠금 정책을 확인하려면 관리자 권한으로 도메인에 가입된 시스템에서 다음 단계를 완료합니다.

1. 그룹 정책 관리 도구를 엽니다.
2. 조직의 계정 잠금 정책(예: **기본 도메인 정책**)을 포함하는 그룹 정책을 편집합니다.
3. **컴퓨터 구성** > **정책** > **Windows 설정** > **보안 설정** > **계정 정책** > **계정 잠금 정책** 으로 이동합니다.
4. **계정 잠금 임계값** 과 **다음 시간 후 계정 잠금 수를 원래대로 설정** 값을 확인합니다.

![온-프레미스 Active Directory 계정 잠금 정책 수정](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD 스마트 잠금 값 관리

조직의 요구 사항에 따라 Azure AD 스마트 잠금 값을 사용자 지정할 수 있습니다. 조직에 특수한 값이 있는 스마트 잠금 설정의 사용자 지정에는 Azure AD Premium P1 또는 사용자에 대한 높은 라이선스가 필요합니다.

조직의 스마트 잠금 값을 확인하거나 수정하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. *Azure Active Directory* 를 검색하여 선택한 다음 **보안** > **인증 방법** > **암호 보호** 를 선택합니다.
1. 첫 번째 잠금 전에 계정에서 허용되는 실패한 로그인 횟수에 따라 **잠금 임계값** 을 설정합니다.

    기본값은 Azure 공용 테넌트 10개, Azure 미국 정부 테넌트 3개입니다.

1. **잠금 지속 기간(초)** 을 각 잠금의 길이(초)로 설정합니다.

    기본값은 60초(1분)입니다.

> [!NOTE]
> 잠금 후 첫 번째 로그인도 실패하는 경우 계정은 다시 잠깁니다. 계정이 반복적으로 잠기는 경우 잠금 지속 시간이 증가합니다.

![Azure Portal에서 Azure AD 스마트 잠금 정책 사용자 지정](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>스마트 잠금 기능이 작동하는지 여부를 확인하는 방법

스마트 잠금 임계값이 트리거되면 계정이 잠겨 있는 동안 다음 메시지가 표시됩니다.

*무단 사용을 방지하기 위해 회원님의 계정을 일시적으로 잠갔습니다. 나중에 다시 시도하세요. 이 문제가 계속되면 관리자에게 문의하세요.*

스마트 잠금을 테스트하는 경우 Azure AD 인증 서비스의 지리적으로 분산되고 부하가 분산된 특성으로 인해 로그인 요청은 다른 데이터 센터에서 처리될 수 있습니다. 이 시나리오에서 각 Azure AD 데이터 센터가 독립적으로 잠금을 추적하기 때문에 잠금을 유발하는 잠금 임계값 시도 횟수를 정의하는 것보다 더 많은 시간이 걸릴 수 있습니다. 사용자는 완전히 잠기기 전에 최대 (*threshold_limit * datacenter_count*)번의 잘못된 시도를 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

환경을 추가로 사용자 지정하려면 [Azure AD 암호 보호를 위해 금지 암호를 사용자 지정하여 구성](tutorial-configure-custom-password-protection.md)할 수 있습니다.

사용자가 웹 브라우저에서 암호를 재설정하거나 변경할 수 있도록 [Azure AD 셀프 서비스 암호 재설정을 구성](tutorial-enable-sspr.md)할 수 있습니다.
