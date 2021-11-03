---
title: PaaS 웹 및 모바일 애플리케이션 보안
titleSuffix: Azure App Service
description: 'PaaS 웹 및 모바일 애플리케이션 보안을 위한 Azure App Service 보안 모범 사례에 대해 알아봅니다. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: 7b1d4b2b35dedf041129ff13399735ef59f42dcd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075508"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Azure App Service를 사용하여 PaaS 웹 및 모바일 애플리케이션 보안을 유지하는 모범 사례

이 문서에서는 PaaS 웹 및 모바일 애플리케이션 보안을 위한 [Azure App Services](../../app-service/overview.md) 보안 모범 사례에 대해 설명합니다. 이러한 모범 사례는 Azure에 대한 Microsoft와 고객의 경험에서 비롯된 것입니다.

Azure App Service는 플랫폼이나 디바이스를 위한 웹 및 모바일 앱을 만들고 어디서나 클라우드 또는 온-프레미스에 있는 데이터에 연결할 수 있는 PaaS(Platform as a Service) 서비스 제품군입니다. 앱 서비스는 이전에 개별적으로 Azure 웹 사이트 및 Azure Mobile Services로 전달한 웹 및 모바일 기능을 포함합니다. 또한 비즈니스 프로세스를 자동화하고 클라우드 API를 호스팅하는 새 기능을 포함합니다. App Service는 단일 통합 서비스로서 웹, 모바일 및 통합 시나리오에 풍부한 기능 집합을 제공합니다.

## <a name="authenticate-through-azure-active-directory-ad"></a>Azure AD(Active Directory)를 통해 인증
App Service는 ID 공급자를 위한 OAuth 2.0 서비스를 제공합니다. OAuth 2.0은 클라이언트 개발자의 단순성에 기반하여 웹 애플리케이션, 데스크톱 애플리케이션 및 휴대폰에 대한 특정 권한 부여 흐름을 제공합니다. Azure AD는 OAuth 2.0을 사용하여 모바일 및 웹 애플리케이션에 대한 액세스 권한을 부여합니다. 자세히 알아보려면 [Azure App Service에서 인증 및 권한 부여](../../app-service/overview-authentication-authorization.md)를 참조하세요.

## <a name="restrict-access-based-on-role"></a>역할 기반 액세스 제한
데이터 액세스를 위한 보안 정책을 시행하려는 조직에서는 액세스를 반드시 제한해야 합니다. Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 특정 범위에서 사용자, 그룹, 애플리케이션에 권한을 할당할 수 있습니다. 예를 들어 꼭 알아야 할 필요가 있는 경우에 한하여 권한을 할당하거나 최소 권한 보안 원칙에 따라 권한을 할당할 수 있습니다. 사용자에게 애플리케이션 액세스 권한을 부여하는 방법에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.

## <a name="protect-your-keys"></a>키 보호
보안이 아무리 훌륭하더라도 구독 키를 분실하면 안됩니다. Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Key Vault를 사용하면 HSM(하드웨어 보안 모듈)을 통해 보호되는 키를 통해 키와 비밀(예: 인증 키, 스토리지 계정 키, 데이터 암호화 키, .PFX 파일 및 암호)를 암호화할 수 있습니다. 추가된 보증을 위해, HSM에서 키를 생성하거나 가져올 수 있습니다. 또한 Key Vault를 사용하여 자동 갱신으로 TLS 인증서를 관리할 수도 있습니다. 자세히 알아보려면 [Azure Key Vault란](../../key-vault/general/overview.md)을 참조하세요.

## <a name="restrict-incoming-source-ip-addresses"></a>들어오는 원본 IP 주소 제한
[App Service 환경](../../app-service/environment/intro.md)에는 NSG(네트워크 보안 그룹)를 통해 들어오는 원본 IP 주소를 제한하는 데 도움이 되는 가상 네트워크 통합 기능이 있습니다. Azure VNets(Virtual Networks)에 익숙하지 않은 사용자는 라우팅할 수 있는 비인터넷 네트워크에 대다수의 Azure 리소스를 배치하여 액세스를 제어할 수 있습니다. 자세한 내용은 [Azure Virtual Network에 앱 통합](../../app-service/overview-vnet-integration.md)을 참조하세요.

Windows에 App Service가 있는 경우 web.config를 구성하여 IP 주소를 동적으로 제한할 수도 있습니다. 자세한 내용은 [동적 IP 보안](/iis/configuration/system.webServer/security/dynamicIpSecurity/)을 참조하세요.


## <a name="next-steps"></a>다음 단계
이 문서에서는 PaaS 웹 및 모바일 애플리케이션 보안을 위한 App Service 보안 모범 사례 모음을 소개했습니다. PaaS 배포 보안 유지에 대한 자세한 내용은 다음을 참조하세요.

- [PaaS 배포 보안](paas-deployments.md)
- [Azure에서 PaaS 데이터베이스 보안 유지](paas-applications-using-sql.md)