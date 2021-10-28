---
title: TLS 1.2 적용 - Azure Active Directory Registration Service
description: Azure AD Device Registration Service에 대한 TLS 1.0 및 1.1 지원 제거
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a36d856252f3ff63fd909d63f254a15930666e8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616045"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Azure AD Registration Service에 대해 TLS 1.2 적용

Azure AD(Azure Active Directory) Device Registration Service는 디바이스 ID를 사용하여 디바이스를 클라우드에 연결하는 데 사용됩니다. Azure AD Device Registration Service는 현재 Azure와의 통신에 TLS(전송 계층 보안) 1.2를 사용할 수 있도록 지원합니다. 보안 및 최상의 암호화를 보장하려면 TLS 1.0 및 1.1을 사용하지 않도록 설정하는 것이 좋습니다. 이 문서에서는 등록을 완료하고 Azure AD Device Registration Service와 통신하는 데 사용되는 머신이 TLS 1.2를 사용하는지 확인하는 방법을 설명합니다.

TLS 프로토콜 버전 1.2는 보안 통신을 제공하도록 설계된 암호화 프로토콜입니다. TLS 프로토콜은 주로 개인 정보 보호 및 데이터 무결성을 제공하는 것을 목표로 합니다. TLS는 [RFC 5246(외부 링크)](https://tools.ietf.org/html/rfc5246)에서 정의된 버전 1.2를 사용하여 여러 번 반복되었습니다.

현재 연결 분석에는 TLS 1.1 및 1.0 사용이 거의 표시되지 않지만 TLS 1.1 및 1.0 지원이 종료되기 전에 영향을 받는 클라이언트 또는 서버를 필요에 따라 업데이트할 수 있도록 이 정보를 제공하고 있습니다. 하이브리드 시나리오 또는 AD FS(Active Directory Federation Services)에 온-프레미스 인프라를 사용하는 경우 인프라에서 TLS 1.2를 사용하는 인바운드 및 아웃바운드 연결을 모두 지원할 수 있는지 확인합니다.

## <a name="update-windows-servers"></a>Windows 서버 업데이트

Azure AD Device Registration Service를 사용하거나 프록시 역할을 하는 Windows 서버의 경우 다음 단계를 사용하여 TLS 1.2를 사용할 수 있는지 확인합니다.

> [!IMPORTANT]
> 레지스트리를 업데이트한 후 변경 내용을 적용하려면 Windows 서버를 다시 시작해야 합니다.

### <a name="enable-tls-12"></a>TLS 1.2 사용

다음 레지스트리 문자열이 아래와 같이 구성되어 있는지 확인합니다.

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client
  - “DisabledByDefault”=dword:00000000
  - “Enabled”=dword:00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server
  - “DisabledByDefault”=dword:00000000
  - “Enabled”=dword:00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
  - "SchUseStrongCrypto"=dword:00000001

## <a name="update-non-windows-proxies"></a>비 Windows 프록시 업데이트

디바이스와 Azure AD Device Registration Service 간 프록시 역할을 하는 모든 머신에서 TLS 1.2를 사용할 수 있는지 확인해야 합니다. 공급업체의 지침에 따라 지원을 확인합니다.

## <a name="update-ad-fs-servers"></a>AD FS 서버 업데이트

Azure AD Device Registration Service와 통신하는 데 사용되는 모든 AD FS 서버에서 TLS 1.2를 사용할 수 있는지 확인해야 합니다. 이 구성을 사용하도록 설정/확인하는 방법에 대한 자세한 내용은 [SSL/TLS 프로토콜 및 AD FS용 암호 그룹 관리](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)를 참조하세요.

## <a name="client-updates"></a>클라이언트 업데이트

모든 클라이언트-서버 및 브라우저-서버 조합에서 TLS 1.2를 사용하여 Azure AD Device Registration Service에 연결해야 하므로 해당 디바이스를 업데이트해야 할 수 있습니다.

다음 클라이언트는 TLS 1.2를 지원할 수 없는 것으로 알려져 있습니다. 액세스가 중단되지 않도록 클라이언트를 업데이트합니다.

- Android 버전 4.3 및 이전 버전
- Firefox 버전 5.0 및 이전 버전
- Windows 7의 Internet Explorer 버전 8~10 및 이전 버전
- Windows Phone 8.0의 Internet Explorer 10
- OS X 10.8.4의 Safari 버전 6.0.4 및 이전 버전

## <a name="next-steps"></a>다음 단계

[TLS/SSL 개요(Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)