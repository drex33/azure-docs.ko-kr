---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 2cf09eea6a5f311b4bd5ff2d9fd5027969056c77
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133540374"
---
### <a name="identity-tier"></a>ID 계층 

Microsoft Oracle 파트너 관계를 통해 Azure, OCI 및 Oracle 애플리케이션에서 통합 ID를 설정할 수 있습니다. JD Edwards EnterpriseOne 또는 PeopleSoft 애플리케이션 제품군의 경우 Azure AD와 Oracle IDCS 간에 Single Sign-On을 설정하려면 OHS(Oracle HTTP Server) 인스턴스가 필요합니다.

OHS는 애플리케이션 계층에 대한 역방향 프록시 역할을 합니다. 즉, 최종 애플리케이션에 대한 모든 요청이 이를 통과합니다. Oracle Access Manager WebGate는 최종 애플리케이션으로 이동하는 모든 요청을 가로채는 OHS 웹 서버 플러그 인입니다. 액세스 중인 리소스가 보호되는 경우(인증된 세션 필요) WebGate는 사용자의 브라우저를 통해 ID 클라우드 서비스를 사용하여 OIDC 인증 흐름을 시작합니다. OpenID Connect WebGate에서 지원하는 흐름에 대한 자세한 내용은 [Oracle Access Manager 설명서](https://docs.oracle.com/cd/E52734_01/oam/AIAAG/GUID-1E927D1B-FB83-425B-8768-85DB441821A4.htm#AIAAG7327)를 참조하세요.

이 설정을 사용하면 Azure AD에 이미 로그인한 사용자는 Oracle Identity Cloud Service를 통해 다시 로그인하지 않고도 JD Edwards EnterpriseOne 또는 PeopleSoft 애플리케이션으로 이동할 수 있습니다. 이 솔루션을 배포하는 고객은 단일 자격 증명 세트, 향상된 로그온 환경, 향상된 보안 및 지원 센터 비용 절감을 포함하여 Single Sign-On의 이점을 얻을 수 있습니다.

Azure AD를 사용하여 JD Edwards EnterpriseOne 또는 PeopleSoft에 Single Sign-On을 설정하는 방법에 대한 자세한 내용은 관련 [Oracle 백서](https://www.oracle.com/a/ocom/docs/applications/jdedwards/jde-on-oci-strategy-updates-2020.pdf)를 참조하세요.
