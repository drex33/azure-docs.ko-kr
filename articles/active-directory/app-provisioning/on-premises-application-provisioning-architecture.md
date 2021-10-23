---
title: Azure AD 온-프레미스 애플리케이션 프로비저닝 아키텍처 | Microsoft Docs
description: 온-프레미스 애플리케이션 프로비저닝 아키텍처의 개요를 제공합니다.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd3b716cd754c26c0a3fe0b1f95183c86bac941f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991207"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Azure AD 온-프레미스 애플리케이션 프로비저닝 아키텍처

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. GA(일반 공급)를 준비 중이므로 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

## <a name="overview"></a>개요

다음 다이어그램에서는 온-프레미스 애플리케이션 프로비저닝의 작동 방식을 보여 줍니다.

![온-프레미스 애플리케이션 프로비저닝을 위한 아키텍처를 보여 주는 다이어그램.](.\media\on-premises-application-provisioning-architecture\arch-3.png)

사용자를 온-프레미스 애플리케이션으로 프로비저닝하는 세 가지 기본 구성 요소가 있습니다.

- 프로비저닝 에이전트는 Azure AD(Azure Active Directory)와 온-프레미스 환경 간에 연결을 제공합니다.
- ECMA 호스트는 Azure AD의 프로비저닝 요청을 대상 애플리케이션에 대한 요청으로 변환합니다. Azure AD와 애플리케이션 간에 게이트웨이 역할을 합니다. Microsoft Identity Manager에서 사용되는 기존 ECMA2 커넥터를 가져올 수 있습니다. SCIM 애플리케이션 또는 SCIM 게이트웨이를 빌드한 경우에는 ECMA 호스트가 필요하지 않습니다.
- Azure AD 프로비저닝 서비스는 동기화 엔진 역할을 합니다.

>[!NOTE]
> Microsoft Identity Manager 동기화는 필요하지 않습니다. 그러나 ECMA 호스트로 가져오기 전에 ECMA 커넥터를 빌드하고 테스트하는 데 사용할 수 있습니다.


### <a name="firewall-requirements"></a>방화벽 요구 사항

회사 네트워크에 대한 인바운드 연결을 열 필요가 없습니다. 프로비저닝 에이전트는 프로비저닝 서비스에 대한 아웃바운드 연결만 사용하므로 들어오는 연결에 대한 방화벽 포트를 열지 않아도 됩니다. 모든 연결은 아웃바운드이고 보안 채널을 통해 발생하기 때문에 경계(DMZ) 네트워크가 필요하지 않습니다.

## <a name="ecma-connector-host-architecture"></a>ECMA 커넥터 호스트 아키텍처
ECMA 커넥터 호스트에는 온-프레미스 프로비전을 달성하는 데 사용하는 여러 영역이 있습니다.  아래 다이어그램은 이러한 개별 영역을 나타내는 개념도입니다.  해당 영역은 아래 표에 더 자세히 설명되어 있습니다.

[![ECMA 커넥터 호스트](.\media\on-premises-application-provisioning-architecture\ecma-2.png)](.\media\on-premises-application-provisioning-architecture\ecma-2.png#lightbox)



|영역|설명|
|-----|-----|
|엔드포인트|Azure AD 프로비전 서비스와의 통신 및 데이터 전송을 담당합니다.|
|메모리 내 캐시|온-프레미스 데이터 원본에서 가져온 데이터를 저장하는 데 사용됩니다.|
|자동 동기화|ECMA 커넥터 호스트와 온-프레미스 데이터 원본 간의 비동기 데이터 동기화를 제공합니다.|
|비즈니스 논리|모든 ECMA 커넥터 호스트 활동을 조정하는 데 사용됩니다.  자동 동기화 시간은 ECMA 호스트에서 구성할 수 있습니다. 이것은 속성 페이지에 있습니다.|

### <a name="about-anchor-attributes-and-distinguished-names"></a>앵커 특성 및 고유 이름 정보
다음 정보는 특히 genericSQL 커넥터에서 사용되는 앵커 특성 및 고유 이름을 더 잘 설명하기 위해 제공됩니다.

앵커 특성은 변경되지 않고 ECMA 커넥터 호스트 메모리 내 캐시의 해당 개체를 나타내는 개체 형식의 고유한 특성입니다.

DN(고유 이름)은 디렉터리 계층 구조에서 현재 위치를 표시하여 개체를 고유하게 식별하는 이름입니다.  또는 SQL 경우 파티션에 표시합니다. 이 이름은 앵커 특성을 디렉터리 파티션의 루트에 연결하여 형성됩니다. 

예를 들어 Active Directory 또는 LDAP와 같은 기존 형식의 기존 DN을 생각할 때 다음과 비슷한 것을 생각합니다.

  CN=Lola Jacobson,CN=Users,DC=contoso,DC=com

하지만 계층 구조가 아닌 평평한 SQL과 같은 데이터 원본의 경우에는 DN이 테이블 중 하나에 이미 있거나 ECMA 커넥터 호스트에 제공하는 정보에서 만들어져야 합니다.  

이 작업은 genericSQL 커넥터를 구성할 때 확인란에서 **자동 생성됨** 을 선택하여 수행할 수 있습니다. DN이 자동으로 생성되도록 선택하면 ECMA 호스트는 DN을 LDAP 형식(CN=&lt;anchorvalue&gt;,OBJECT=&lt;type&gt;)으로 생성합니다. 또한 연결 페이지에서 DN is Anchor(DN이 앵커입니다)가 **선택 취소되어 있다** 고 가정합니다. 
 
 [![DN is Anchor가 선택 안 됨](.\media\on-premises-application-provisioning-architecture\user-2.png)](.\media\on-premises-application-provisioning-architecture\user-2.png#lightbox)

genericSQL 커넥터는 DN이 LDAP 형식을 사용하여 채워질 것으로 예상합니다.  일반 SQL 커넥터는 구성 요소 이름이 "OBJECT="인 LDAP 스타일을 사용합니다. 이렇게 하면 파티션을 사용할 수 있습니다(각 개체 형식이 파티션임).

ECMA 커넥터 호스트는 현재 USER 개체 형식만 지원하기 때문에 OBJECT=&lt;type&gt;은 OBJECT=USER입니다.  따라서 앵커 값이 ljacobson인 사용자의 DN은 다음과 같습니다.

  CN=ljacobson,OBJECT=USER


### <a name="user-creation-workflow"></a>사용자 만들기 워크플로

1.  Azure AD 프로비전 서비스는 ECMA 커넥터 호스트를 쿼리하여 사용자가 있는지 확인합니다.  **일치하는 특성** 을 필터로 사용합니다.  이 특성은 엔터프라이즈 애플리케이션 -> 온-프레미스 프로비전 -> 프로비전 -> 특성 일치 아래 Azure AD 포털에서 정의됩니다.  일치 우선 순위가 1로 표시됩니다.
하나 이상의 일치하는 특성을 정의하고 우선 순위를 지정할 수 있습니다.  일치하는 특성을 변경하려는 경우 그렇게 할 수 있습니다.
 [![일치하는 특성](.\media\on-premises-application-provisioning-architecture\match-1.png)](.\media\on-premises-application-provisioning-architecture\match-1.png#lightbox)

2.  ECMA 커넥터 호스트는 GET 요청을 수신하고 내부 캐시를 쿼리하여 사용자가 존재하는지 여부와 가져온 기반이 있는지 여부를 확인합니다.  이 작업은 **쿼리 특성** 을 사용하여 수행됩니다. 쿼리 특성은 개체 유형 페이지에서 정의됩니다.  
 [![쿼리 특성](.\media\on-premises-application-provisioning-architecture\match-2.png)](.\media\on-premises-application-provisioning-architecture\match-2.png#lightbox)


3. 사용자가 없으면 Azure AD는 사용자를 만들기 위한 POST 요청을 수행합니다.  ECMA 커넥터 호스트는 HTTP 201을 사용하여 Azure AD에 응답하고 사용자에 대한 ID를 제공합니다. 이 ID는 개체 유형 페이지에 정의된 앵커 값에서 파생됩니다. 이 앵커는 Azure AD에서 향후 및 후속 요청에 대해 ECMA 커넥터 호스트를 쿼리하는 데 사용됩니다. 
4. Azure AD에서 사용자가 변경되면 Azure AD는 1단계의 일치하는 특성이 아닌 이전 단계의 앵커를 사용하여 사용자를 검색하도록 GET 요청을 수행합니다. 그러면 예를 들어 Azure AD의 사용자와 앱 사이의 링크를 끊지 않고 UPN을 변경할 수 있습니다.  


## <a name="agent-best-practices"></a>에이전트 모범 사례
- 자동 Azure AD Connect 프로비저닝 에이전트 자동 업데이트 서비스가 실행 중인지 확인합니다. 에이전트를 설치할 때 기본적으로 사용하도록 설정됩니다. Microsoft가 배포를 지원하려면 자동 업데이트가 필요합니다.
- 에이전트와 Azure 간 아웃바운드 TLS 통신에 대한 모든 형태의 인라인 검사를 방지합니다. 이 유형의 인라인 검사를 수행하면 통신 흐름이 저하됩니다.
- 에이전트는 Azure 및 애플리케이션과 둘 다 통신해야 하므로 에이전트의 배치는 두 연결의 대기 시간에 영향을 줍니다. 각 네트워크 연결을 최적화하여 엔드투엔드 트래픽의 대기 시간을 최소화할 수 있습니다. 각 연결은 다음을 통해 최적화할 수 있습니다.
  - 홉의 두 끝 사이의 거리 줄이기
  - 트래버스하기 적합한 네트워크 선택. 예를 들어 공용 인터넷보다 프라이빗 네트워크를 트래버스하는 것이 전용 링크가 있으므로 더 빠를 수 있습니다.



## <a name="provisioning-agent-questions"></a>프로비저닝 에이전트 질문
몇 가지 일반적인 질문에 대한 답변이 여기에 있습니다.

### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>프로비저닝 에이전트의 GA 버전은 무엇인가요?

프로비저닝 에이전트의 최신 GA 버전은 [Azure AD 연결 프로비저닝 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)을 참조하세요.

### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>내 프로비저닝 에이전트의 버전을 어떻게 알 수 있나요?

 1. 프로비저닝 에이전트가 설치된 Windows Server에 로그인합니다.
 2. **제어판** > **프로그램 제거/변경** 으로 이동합니다.
 3. **Microsoft Azure AD Connect 프로비저닝 에이전트** 에 대한 항목에 해당하는 버전을 찾습니다.

### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Microsoft에서 프로비저닝 에이전트 업데이트를 자동으로 푸시하나요?

예. Microsoft Azure AD Connect 에이전트 업데이트 프로그램 Windows 서비스를 시작하여 실행하는 경우 Microsoft에서 프로비저닝 에이전트를 자동으로 업데이트합니다. 문제 해결을 지원하려면 에이전트가 최신 상태인지 확인해야 합니다.

### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect-or-microsoft-identity-manager"></a>Azure AD Connect 또는 Microsoft Identity Manager를 실행하는 동일한 서버에 프로비저닝 에이전트를 설치할 수 있나요?

예. Azure AD Connect 또는 Microsoft Identity Manager를 실행하는 동일한 서버에 프로비저닝 에이전트를 설치할 수 있지만 필수는 아닙니다.

### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>아웃바운드 HTTP 통신에 프록시 서버를 사용하도록 프로비저닝 에이전트를 구성하려면 어떻게 할까요?

프로비저닝 에이전트는 아웃바운드 프록시 사용을 지원합니다. 에이전트 구성 파일(**C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**)을 편집하여 구성할 수 있습니다. 해당 파일의 끝 부분에서 닫는 `</configuration>` 태그 바로 앞에 다음 행을 추가합니다. `[proxy-server]` 및 `[proxy-port]` 변수를 프록시 서버 이름 및 포트 값으로 바꿉니다.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="how-do-i-ensure-the-provisioning-agent-can-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>프로비저닝 에이전트가 Azure AD 테넌트와 통신할 수 있고 에이전트에 필요한 포트를 차단하는 방화벽이 없는지 확인하려면 어떻게 할까요?

필요한 모든 포트가 열려 있는지 확인할 수도 있습니다.

### <a name="how-do-i-uninstall-the-provisioning-agent"></a>프로비저닝 에이전트를 제거하려면 어떻게 할까요?
 1. 프로비저닝 에이전트가 설치된 Windows Server에 로그인합니다.
 2. **제어판** > **프로그램 제거/변경** 으로 이동합니다.
 3. 다음 프로그램을 제거합니다.
     - Microsoft Azure AD Connect 프로비전 에이전트
     - Microsoft Azure AD Connect 에이전트 업데이트
     - Microsoft Azure AD Connect 프로비전 에이전트 패키지





## <a name="next-steps"></a>다음 단계

- [앱 프로비저닝](user-provisioning.md)
