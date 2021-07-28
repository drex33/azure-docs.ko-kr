---
title: Azure AD 인증을 사용하여 VPN 사용자에 대한 MFA 사용 설정
description: Azure AD 인증을 사용하여 VPN 사용자에 대해 Azure AD MFA(Multi-Factor Authentication)를 사용하도록 설정하는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e8d90653372b78aad78fad66e4cde21bd2ab81ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995620"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>Azure AD 인증을 사용하여 VPN 사용자에 대한 Azure AD MFA(Multi-Factor Authentication) 사용 설정

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>인증 사용

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>로그인 설정 구성

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>옵션 1 - 사용자당 액세스

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>옵션 2 - 조건부 액세스

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>다음 단계

가상 네트워크에 연결하려면 VPN 클라이언트 프로필을 만들고 구성해야 합니다. [Azure에 대한 지점 및 사이트 간 연결을 위한 Azure AD 인증 구성](virtual-wan-point-to-site-azure-ad.md)을 참조하세요.
