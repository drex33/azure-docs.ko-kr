---
title: Kerberos 제한 위임 문제 해결 - 앱 프록시
description: 애플리케이션 프록시에 대한 Kerberos 제한 위임 구성 문제 해결
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen, ashishj
ms.openlocfilehash: bec7c2113ccf09da83e8dd998fba2c083bc661fb
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990447"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>애플리케이션 프록시에 대한 Kerberos 제한 위임 구성 문제 해결

게시된 애플리케이션에 SSO를 구축하는 데 사용할 수 있는 방법은 애플리케이션마다 다를 수 있습니다. Azure AD(Azure Active Directory) 애플리케이션 프록시에서 기본적으로 제공하는 옵션 중 하나는 KCD(Kerberos 제한된 위임)입니다. 사용자를 위해 백 엔드 애플리케이션에 대해 제한된 Kerberos 인증을 실행하도록 커넥터를 구성할 수 있습니다.

KCD를 사용하도록 설정하는 절차는 간단합니다. SSO를 지원하는 다양한 구성 요소 및 인증 흐름에 대한 일반적인 이해만 있으면 됩니다. 그러나 KCD SSO가 예상대로 작동하지 않는 경우도 있습니다. 이러한 시나리오의 문제를 해결하려면 좋은 정보 소스가 필요합니다.

이 문서에서는 가장 일반적인 문제 중 일부를 해결하고 자체 수정하는 데 도움이 되는 단일 참조 지점을 제공합니다. 또한 더욱 복잡한 구현 문제의 진단에 대해서도 다룹니다.

이 문서에서는 다음과 같이 가정합니다.

- [애플리케이션 프록시 시작](application-proxy-add-on-premises-application.md)에 따라 Azure AD 애플리케이션 프록시가 배포되었으며 비 KCD 애플리케이션에 대한 일반 액세스가 예상대로 작동합니다.
- 게시된 대상 애플리케이션은 IIS(인터넷 정보 서비스) 및 Microsoft의 Kerberos 구현을 기반으로 합니다.
- 서버 및 애플리케이션 호스트는 단일 Azure Active Directory 도메인에 상주합니다. 도메인 및 포리스트 간 시나리오에 대한 자세한 내용은 [KCD 백서](https://aka.ms/KCDPaper)를 참조하세요.
- 주체 애플리케이션은 사전 인증을 사용하도록 설정하여 Azure 테넌트에 게시됩니다. 사용자는 양식 기반 인증을 통해 Azure에 인증해야 합니다. 리치 클라이언트 인증 시나리오는 이 문서에서 다루지 않습니다. 나중에 특정 시점에 추가될 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 애플리케이션 프록시는 여러 유형의 인프라 또는 환경에 배포할 수 있습니다. 아키텍처는 조직마다 다릅니다. KCD 관련 문제의 가장 일반적인 원인은 환경이 아닙니다. 대부분의 문제가 단순한 구성 오류나 일반적인 실수로 인해 발생합니다.

이러한 이유 때문에, 문제 해결을 시작하기 전에 [애플리케이션 프록시와 함께 KCD SSO 사용](application-proxy-configure-single-sign-on-with-kcd.md)의 모든 필수 조건을 충족했는지 확인하는 것이 좋습니다. 2012R2에서 Kerberos 제한된 위임을 구성하는 방법에 대한 섹션을 참조하세요. 이 프로세스는 이전 버전의 Windows에서 KCD를 구성하는 다른 방법을 사용합니다. 또한, 다음 사항에 유의하세요.

- 도메인 구성원 서버가 특정 DC(도메인 컨트롤러)와 보안 채널 대화를 여는 것은 흔한 일입니다. 그런 다음, 서버는 언제든지 다른 대화로 이동할 수 있습니다. 따라서 커넥터 호스트는 특정 로컬 사이트 DC하고만 통신하도록 제한되지 않습니다.
- 도메인 간 시나리오는 커넥터 호스트를 로컬 네트워크 경계 외부에 있는 DC에 연결하는 참조를 사용합니다. 이러한 경우, 다른 각각의 도메인을 나타내는 DC까지 계속 트래픽을 보내는 것도 중요합니다. 그러지 않으면 위임이 실패합니다.
- 가능한 경우, 커넥터 호스트와 DC 사이에 활성 IPS 또는 IDS 디바이스를 배치하지 마세요. 이와 같은 디바이스는 과도하게 방해가 될 수 있으며 코어 RPC 트래픽을 간섭합니다.

간단한 시나리오에서 위임을 테스트합니다. 변수가 많을수록 더 많이 고민해야 할 수 있습니다. 시간을 절약하려면 테스트를 단일 커넥터로 제한합니다. 문제가 해결된 후 커넥터를 더 추가합니다.

몇 가지 환경 요인도 문제에 영향을 줄 수 있습니다. 이러한 요인을 방지하려면 테스트 중에 가능한 한 아키텍처를 최소화합니다. 예를 들어, 잘못 구성된 내부 방화벽 ACL이 일반적입니다. 가능한 경우, 커넥터의 모든 트래픽을 DC 및 백 엔드 애플리케이션으로 직접 전송합니다.

커넥터를 배치하는 가장 좋은 장소는 가능한 한 대상과 가까운 위치입니다. 테스트할 때 방화벽이 인라인에 있을 경우 불필요하게 복잡해져 조사 시간이 길어질 수 있습니다.

KCD 문제를 보여 주는 것은 무엇일까요? KCD SSO 오류를 나타내는 여러 가지 일반적인 표시가 있습니다. 문제의 첫 번째 증상은 브라우저에서 나타납니다.

![잘못된 KCD 구성 오류의 예시를 보여 주는 스크린샷. “잘못된 Kerberos 제한 위임” 오류가 강조 표시되어 있음.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![예: 누락된 권한으로 인해 권한 부여 실패](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

두 이미지 모두 동일한 증상인 SSO 실패를 보여 줍니다. 애플리케이션에 대한 사용자 액세스가 거부되었습니다.

## <a name="troubleshooting"></a>문제 해결

문제를 해결하는 방법은 문제와 관찰된 증상에 따라 달라집니다. 더 진행하기 전에 다음 문서를 살펴보세요. 이 문서에서는 유용한 문제 해결 정보를 제공합니다.

- [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)
- [Kerberos 오류 및 증상](application-proxy-troubleshoot.md#kerberos-errors)
- [온-프레미스 및 클라우드 ID가 동일하지 않은 경우 SSO를 사용하여 작업](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

이 지점에 도달했다면 주요 문제가 있는 것입니다. 먼저 문제 해결이 가능하도록 흐름을 다음 세 단계로 구분합니다.

### <a name="client-pre-authentication"></a>클라이언트 사전 인증

외부 사용자가 브라우저를 통해 Azure에 인증합니다. KCD SSO가 작동하려면 Azure에 사전 인증할 수 있어야 합니다. 이 기능을 테스트하고, 문제가 있으면 해결합니다. 사전 인증 단계는 KCD 또는 게시된 애플리케이션과 관련이 없습니다. Azure에 주체 계정이 있는지 확인하는 온전성 검사를 통해 불일치를 쉽게 수정할 수 있습니다. 사용하지 않도록 설정되거나 차단되지 않았는지도 확인합니다. 브라우저의 오류 응답은 원인을 설명하기에 충분합니다. 확실하지 않은 경우 다른 Microsoft 문제 해결 문서를 통해 확인하세요.

### <a name="delegation-service"></a>위임 서비스

Kerberos KCD(키 배포 센터)에서 사용자에 대한 Kerberos 서비스 티켓을 가져오는 Azure 프록시 커넥터입니다.

클라이언트와 Azure 프런트 엔드 간의 외부 통신은 KCD에 아무런 영향을 미치지 않습니다. 이러한 통신은 KCD가 작동하는지 확인하는 역할만 합니다. Azure 프록시 서비스에는 Kerberos 티켓을 가져오는 데 사용되는 유효한 사용자 ID가 제공됩니다. 이 ID가 없으면 KCD가 불가능하며 실패합니다.

앞에서 언급했듯이 브라우저 오류 메시지는 실패하는 이유에 대한 단서를 제공합니다. 응답에 포함된 활동 ID 및 타임스탬프를 적어 두세요. 이 정보는 Azure 프록시 이벤트 로그의 실제 이벤트와 동작을 상호 연결하는 데 도움이 됩니다.

![예: 잘못된 KCD 구성 오류](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

이벤트 로그에 표시된 해당 항목은 이벤트 13019 또는 12027로 표시됩니다. 커넥터 이벤트 로그는 **애플리케이션 및 서비스 로그** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **Admin** 에서 확인할 수 있습니다.

![애플리케이션 프록시 이벤트 로그의 이벤트 13019](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![애플리케이션 프록시 이벤트 로그의 이벤트 12027](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. 애플리케이션의 주소에 대해 내부 DNS의 **A** 레코드를 사용하고 **CName** 은 사용하지 마세요.
1. 커넥터 호스트에 지정된 대상 계정의 SPN에 위임할 수 있는 권한이 부여되었는지 다시 확인합니다. **인증 프로토콜 사용** 이 선택되었는지 다시 확인합니다. 자세한 내용은 [SSO 구성 문서](application-proxy-configure-single-sign-on-with-kcd.md)를 참조하세요.
1. Azure AD에 SPN 인스턴스가 하나만 있는지 확인합니다. 도메인 구성원 호스트의 명령 프롬프트에서 `setspn -x`를 실행합니다.
1. [실행된 Kerberos 토큰의 최대 크기](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012)를 제한하는 도메인 정책이 적용되는지 확인합니다. 이 정책은 과도한 경우 커넥터의 토큰 가져오기를 중지합니다.

문제에 대한 보다 구체적인 정보를 얻기 위한 차선책은 커넥터 호스트와 도메인 KDC 간의 교환을 캡처하는 네트워크 추적입니다. 자세한 내용은 [심층 분석 문제 해결 문서](https://aka.ms/proxytshootpaper)를 참조하세요.

티켓팅이 잘된 경우 애플리케이션이 401을 반환하여 인증에 실패했음을 알리는 이벤트가 로그에 표시됩니다. 이 이벤트는 대상 애플리케이션이 티켓을 거부했음을 나타냅니다. 다음 단계로 이동합니다.

### <a name="target-application"></a>대상 애플리케이션

커넥터가 제공한 Kerberos 티켓의 소비자입니다. 이 단계에서는 커넥터가 Kerberos 서비스 티켓을 백 엔드로 전송했을 것입니다. 이 티켓은 첫 번째 애플리케이션 요청의 헤더입니다.

1. 포털에 정의된 애플리케이션의 내부 URL을 사용하여 커넥터 호스트의 브라우저에서 애플리케이션에 직접 액세스할 수 있는지 확인합니다. 그러면 성공적으로 로그인할 수 있습니다. 자세한 내용은 커넥터 **문제 해결** 페이지에서 확인할 수 있습니다.
1. 커넥터 호스트에서 브라우저와 애플리케이션 간의 인증이 Kerberos를 사용하는지 확인합니다. 다음 작업 중 하나를 수행합니다.
1. Internet Explorer에서 DevTools(**F12**)를 실행하거나, 커넥터 호스트에서 [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/)를 사용합니다. 내부 URL을 사용하여 애플리케이션으로 이동합니다. 애플리케이션의 응답에 반환된, 제공된 WWW 인증 헤더를 검사하여 협상 또는 Kerberos가 있는지 확인합니다.

   - 애플리케이션에 대한 브라우저의 응답에 반환되는 다음 Kerberos Blob은 **YII** 로 시작합니다. 이 문자는 Kerberos가 실행 중임을 알려 줍니다. 반면에 Microsoft NTLM(NT LAN Manager)은 항상 **TlRMTVNTUAAB** 로 시작합니다. 이 경우 Base64에서 디코드될 때 NTLMSSP(NTLM Security Support Provider)를 읽습니다. Blob 시작 부분에 **TlRMTVNTUAAB** 가 표시되면 Kerberos를 사용할 수 없습니다. **TlRMTVNTUAAB** 가 표시되지 않으면 Kerberos를 사용할 수 있을 가능성이 큽니다.

      > [!NOTE]
      > Fiddler를 사용하는 경우, 이 방법을 사용하려면 IIS에서 애플리케이션의 구성에 대한 확장된 보호를 일시적으로 해제해야 합니다.

      ![브라우저 네트워크 검사 창](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - 이 그림의 Blob은 **TIRMTVNTUAAB** 로 시작하지 않습니다. 따라서 이 예제에서는 Kerberos를 사용할 수 있으며, Kerberos Blob이 **YII** 로 시작하지 않습니다.

1. IIS 사이트의 공급자 목록에서 NTLM을 일시적으로 제거합니다. 커넥터 호스트의 Internet Explorer에서 직접 앱에 액세스합니다. 이제 NTLM이 공급자 목록에 없습니다. Kerberos만 사용하여 애플리케이션에 액세스할 수 있습니다. 액세스가 실패하면 애플리케이션 구성에 문제가 있는 것입니다. Kerberos 인증이 작동하지 않습니다.

   - Kerberos를 사용할 수 없는 경우, IIS에서 애플리케이션의 인증 설정을 확인합니다. **Negotiate**(협상)가 맨 위에 나열되고, 바로 아래에 NTLM이 있는지 확인합니다. **Not Negotiate**(협상 안 함), **Kerberos or Negotiate**(Kerberos 또는 협상), **PKU2U** 가 표시되면 Kerberos가 작동하는 경우에만 계속 진행합니다.

     ![Windows 인증 공급자](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Kerberos 및 NTLM이 구현되면 포털에서 애플리케이션에 대한 사전 인증을 일시적으로 해제합니다. 외부 URL을 사용하여 인터넷에서 액세스를 시도합니다. 인증하라는 메시지가 표시됩니다. 이전 단계에서 사용한 것과 동일한 계정으로 이 작업을 수행할 수 있습니다. 수행할 수 없는 경우 KCD가 아니라 백 엔드 애플리케이션에 문제가 있는 것입니다.
   - 포털에서 사전 인증을 다시 사용하도록 설정합니다. 외부 URL을 통해 애플리케이션에 연결을 시도하여 Azure를 통해 인증합니다. SSO가 실패하면 브라우저에 금지됨 오류 메시지가 표시되고, 로그에 이벤트 13022가 표시됩니다.

     *백 엔드 서버가 HTTP 401 오류와 함께 Kerberos 인증 시도에 응답하기 때문에 Microsoft AAD Application Proxy Connector에서 사용자를 인증할 수 없습니다.*

      ![HTTTP 401 사용할 수 없음 오류 표시](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - IIS 애플리케이션을 확인합니다. 구성된 애플리케이션 풀과 SPN이 Azure AD에서 동일한 계정을 사용하도록 구성되었는지 확인합니다. 다음 그림에 표시된 대로 IIS를 탐색합니다.

      ![IIS 애플리케이션 구성 창](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      ID를 알게 되었으면, 이 계정이 해당 SPN으로 구성되었는지 확인합니다. 예제는 `setspn –q http/spn.wacketywack.com`입니다. 명령 프롬프트에 다음 텍스트를 입력합니다.

      ![SetSPN 명령 창 표시](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - 포털에서 애플리케이션 설정에 대해 정의된 SPN을 확인합니다. 대상 Azure AD 계정에 대해 구성된 것과 동일한 SPN이 애플리케이션 앱 풀에서 사용되는지 확인합니다.

      ![Azure Portal의 SPN 구성](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - IIS로 이동하여 애플리케이션에 대한 **구성 편집기** 옵션을 선택합니다. **system.webServer/security/authentication/windowsAuthentication** 으로 이동합니다. **UseAppPoolCredentials** 값이 **True** 인지 확인합니다.

      ![IIS 구성 앱 풀 자격 증명 옵션](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      이 값을 **True** 로 변경합니다. 다음 명령을 실행하여 백 엔드 서버에서 캐시된 Kerberos 티켓을 모두 제거합니다.

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

자세한 내용은 [모든 세션에 대해 Kerberos 클라이언트 티켓 캐시 제거](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)를 참조하세요.

커널 모드를 사용하도록 유지하면 Kerberos 작업 성능이 향상됩니다. 그러나 요청된 서비스에 대한 티켓이 머신 계정을 사용하여 암호 해독됩니다. 이 계정을 로컬 시스템이라고도 합니다. 애플리케이션이 팜의 여러 서버에 호스트된 경우, KCD를 중단하려면 이 값을 **True** 로 설정합니다.

- 추가 확인을 위해 **확장** 보호도 사용하지 않도록 설정합니다. 일부 시나리오에서는, **확장** 보호를 특정 구성에서 사용하도록 설정할 경우 KCD가 중단되었습니다. 이 경우, 애플리케이션이 기본 웹 사이트의 하위 폴더로 게시된 것입니다. 이 애플리케이션은 익명 인증에 대해서만 구성되었습니다. 모든 대화 상자가 회색으로 표시되며, 이는 자식 개체가 활성 설정을 상속하지 않음을 나타냅니다. 테스트 후에는 이 값을 **사용** 으로 복원하는 것이 좋습니다(가능한 경우).

  이 추가 확인을 통해 게시된 애플리케이션을 사용할 수 있게 됩니다. 역시 위임하도록 구성된 추가 커넥터를 실행할 수 있습니다. 자세한 내용은 심층 기술 연습인 [Azure AD 애플리케이션 프록시 문제 해결](https://aka.ms/proxytshootpaper)을 참조하세요.

그래도 진행할 수 없는 경우 Microsoft 고객 지원 팀에서 도와 드립니다. 포털 내에서 바로 지원 티켓을 만드세요. 엔지니어가 연락을 드릴 것입니다.

## <a name="other-scenarios"></a>기타 시나리오

- Azure 애플리케이션 프록시는 애플리케이션에 요청을 보내기 전에 Kerberos 티켓을 요청합니다. 일부 타사 애플리케이션은 이 인증 방법을 선호하지 않습니다. 이러한 애플리케이션은 보다 일반적인 협상이 수행될 것으로 예상합니다. 첫 번째 요청은 익명으로 처리되므로 애플리케이션이 401을 통해 지원하는 인증 유형으로 응답할 수 있습니다. 이 유형의 Kerberos 협상은 [Single Sign-On에 대한 Kerberos 제한 위임](application-proxy-configure-single-sign-on-with-kcd.md)문서에 설명된 단계를 사용하여 설정할 수 있습니다.
- 다중 홉 인증은 일반적으로 SQL Server Reporting Services와 같이 둘 다 인증이 필요한 백 엔드 및 프런트 엔드를 사용하여 애플리케이션이 계층화된 시나리오에서 사용됩니다. 자세한 내용은 [웹 등록 프록시 페이지에 대한 Kerberos 제한 위임을 구성하는 방법](/troubleshoot/windows-server/identity/configure-kerberos-constrained-delegation)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[관리되는 도메인에서 KCD를 구성](../../active-directory-domain-services/deploy-kcd.md)합니다.
