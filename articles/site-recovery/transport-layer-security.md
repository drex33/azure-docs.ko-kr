---
title: Azure Site Recovery의 전송 계층 보안
description: 암호화 프로토콜 TLS (전송 계층 보안)를 사용 하 여 네트워크를 통해 전송 될 때 데이터를 안전 하 게 유지 하도록 Azure Site Recovery 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 3937fd9f88a844c0257bc6cb66b4c3f97a112987
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634196"
---
# <a name="transport-layer-security-in-azure-site-recovery"></a>Azure Site Recovery의 전송 계층 보안

TLS(전송 계층 보안)는 네트워크를 통해 전송될 때 데이터를 안전하게 유지하는 암호화 프로토콜입니다. Azure Site Recovery는 TLS를 사용 하 여 전송 되는 데이터의 개인 정보를 보호 합니다. 이제 Azure Site Recovery는 보안 향상을 위해 TLS 1.2 프로토콜을 사용 합니다.

## <a name="enable-tls-on-older-versions-of-windows"></a>이전 버전의 Windows에서 TLS를 사용 하도록 설정

컴퓨터에서 이전 버전의 Windows를 실행 하는 경우 아래에 설명 된 대로 해당 업데이트를 설치 하 고 해당 KB 문서에 설명 된 대로 레지스트리를 변경 해야 합니다.

|운영 체제  |기술 자료 문서 |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>업데이트는 프로토콜에 필요한 구성 요소를 설치 합니다. 설치 후 필요한 프로토콜을 사용 하도록 설정 하려면 위의 KB 문서에 설명 된 대로 레지스트리 키를 업데이트 해야 합니다.

## <a name="verify-windows-registry"></a>Windows 레지스트리 확인

### <a name="configure-schannel-protocols"></a>SChannel 프로토콜 구성

다음 레지스트리 키는 SChannel 구성 요소 수준에서 TLS 1.2 프로토콜을 사용하도록 설정했는지 확인합니다.

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>기본적으로 위의 레지스트리 키는 Windows Server 2012 R2 이상 버전에서 설정 된 값으로 설정 됩니다. 이러한 버전의 Windows에 대해 레지스트리 키가 없으면 레지스트리 키를 만들 필요가 없습니다.

### <a name="configure-net-framework"></a>.NET Framework 구성

다음 레지스트리 키를 사용 하 여 강력한 암호화를 지 원하는 .NET Framework를 구성 합니다. [여기에서 .NET Framework 구성](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry)에 대해 자세히 알아보세요.

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-enable-tls-12"></a>왜 TLS 1.2를 사용해야 하나요?

TLS 1.2는 SSL 2.0, SSL 3.0, TLS 1.0 및 TLS 1.1 등 이전의 암호화 프로토콜에 비해 더욱 안전합니다. Azure Site Recovery 서비스는 TLS 1.2을 완벽 하 게 지원 합니다.

### <a name="what-determines-the-encryption-protocol-used"></a>사용되는 암호화 프로토콜을 결정하는 요소는 무엇인가요?

클라이언트와 서버에서 모두 지원되는 가장 높은 프로토콜 버전은 암호화된 대화를 설정하기 위해 협상합니다. TLS 핸드셰이크 프로토콜에 대한 자세한 내용은 [TLS를 사용한 보안 세션 설정](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls)을 참조하세요.

### <a name="what-is-the-impact-if-tls-12-is-not-enabled"></a>TLS 1.2을 사용 하지 않는 경우 미치는 영향은 무엇입니까?

프로토콜 다운 그레이드 공격 으로부터 보안을 개선 하기 위해 Azure Site Recovery 1.2 이전 버전의 TLS 버전을 사용 하지 않도록 설정 하는 것을 시작 합니다. 이는 레거시 프로토콜 및 암호 그룹 연결을 허용하지 않도록 서비스 전반에 걸친 장기 이동의 일환입니다. Azure Site Recovery 서비스와 구성 요소는 TLS 1.2을 완벽 하 게 지원 합니다. 하지만 필수 업데이트 또는 특정 사용자 지정된 구성이 없는 Windows 버전으로 인해 여전히 TLS 1.2 프로토콜이 제공하지 못할 수 있습니다. 이로 인해 다음 중 하나 이상을 포함한 오류가 발생할 수 있습니다.

- 원본에서 복제가 실패할 수 있습니다.
- Azure Site Recovery 구성 요소 연결 오류 10054 (원격 호스트에서 기존 연결을 강제로 닫음) 오류.
- Azure Site Recovery 관련 된 서비스는 정상적으로 중지 되거나 시작 되지 않습니다.

## <a name="additional-resources"></a>추가 리소스

- [전송 계층 보안 프로토콜](/windows/win32/secauthn/transport-layer-security-protocol)
- [배포된 운영 체제에서 TLS 1.2 지원 보장](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [.NET Framework에 대한 TLS(전송 계층 보안) 모범 사례](/dotnet/framework/network-programming/tls)