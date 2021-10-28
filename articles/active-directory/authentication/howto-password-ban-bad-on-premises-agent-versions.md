---
title: 암호 보호 에이전트 릴리스 내역 - Azure Active Directory
description: 문서 버전 릴리스 및 동작 변경 기록
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 06/04/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa4c7625637c84404b5ed7074c078717127ee1f
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545362"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Azure AD 암호 보호 에이전트에 대한 버전 기록

## <a name="121760"></a>1.2.176.0

릴리스 날짜: 2021년 6월 4일

* 특정 환경에서 프록시 및 DC 에이전트가 실행되지 않도록 하는 문제에 대한 사소한 버그를 수정합니다.

## <a name="121720"></a>1.2.172.0

릴리스 날짜: 2021년 2월 22일

온-프레미스 Azure AD 암호 보호 에이전트의 GA 버전이 릴리스된 지 거의 2년입니다. 이제 새 업데이트를 사용할 수 있습니다. 아래의 변경 내용 설명을 참조하세요. 제품에 대한 의견을 제공해주신 모든 분들께 감사드립니다. 

* DC 에이전트와 프록시 에이전트 소프트웨어 모두 이제 .NET 4.7.2가 설치되어 있어야 합니다.
  * .NET 4.7.2가 아직 설치되지 않은 경우 [Windows용 .NET Framework 4.7.2 오프라인 설치 관리자](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)에 있는 설치 관리자를 다운로드하여 실행하세요.
* 이제 AzureADPasswordProtection PowerShell 모듈은 DC 에이전트 소프트웨어에 의해서도 설치됩니다.
* 두 가지 새로운 상태 관련 PowerShell cmdlet이 추가되었습니다. Test-AzureADPasswordProtectionDCAgent 및 Test-AzureADPasswordProtectionProxy.
* 이제 lsass.exe가 PPL 모드로 실행되도록 구성된 컴퓨터에서 AzureADPasswordProtection DC 에이전트 암호 필터 dll이 로드되고 실행됩니다.
* 길이가 5자 미만인 금지된 암호를 잘못 허용한 암호 알고리즘에 대한 버그 수정.
  * 이 버그는 온-프레미스 AD 최소 암호 길이 정책이 처음에 5자 미만의 암호를 허용하도록 구성된 경우에만 적용됩니다.
* 기타 사소한 버그 수정.

새 설치 관리자가 이전 버전의 소프트웨어를 자동으로 업그레이드합니다. 단일 컴퓨터에 DC 에이전트와 프록시 소프트웨어를 모두 설치한 경우(테스트 환경에서만 권장됨) 둘 모두를 동시에 업그레이드해야 합니다.

도메인 또는 포리스트 내에서 DC 에이전트 및 프록시 소프트웨어의 이전 버전과 최신 버전을 실행하는 것이 지원됩니다. 하지만 모든 에이전트를 최신 버전으로 업그레이드하는 것이 좋습니다. 에이전트 업그레이드 순서가 지원됩니다. 새 DC 에이전트는 이전 프록시 에이전트를 통해 통신할 수 있고 이전 DC 에이전트는 최신 프록시 에이전트를 통해 통신할 수 있습니다.

## <a name="121250"></a>1.2.125.0

릴리스 날짜: 2019년 3월 2일

* 이벤트 로그 메시지에서 사소한 오타 수정
* EULA 계약을 최종 일반 공급 버전으로 업데이트

> [!NOTE]
> 빌드 1.2.125.0은 일반 공급 빌드입니다. 제품에 대한 의견을 제공해주신 모든 분들께 다시 한번 감사드립니다.

## <a name="121160"></a>1.2.116.0

릴리스 날짜: 2019년 3월 3일

* 이제 Get-AzureADPasswordProtectionProxy 및 Get-AzureADPasswordProtectionDCAgent cmdlet은 소프트웨어 버전 및 현재 Azure 테넌트를 보고하며 다음과 같은 제한 사항이 있습니다.
  * 소프트웨어 버전 및 Azure 테넌트 데이터는 버전 1.2.116.0 이상을 실행하는 DC 에이전트 및 프록시에만 사용할 수 있습니다.
  * Azure 테넌트 데이터는 프록시 또는 포리스트를 다시 등록(또는 갱신)할 때까지 보고되지 않을 수 있습니다.
* 이제 프록시 서비스는 .NET 4.7이 설치되어 있어야 합니다.
  * .NET 4.7이 아직 설치되지 않은 경우 [Windows용 .NET Framework 4.7 오프라인 설치 관리자](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)에 있는 설치 관리자를 다운로드하여 실행하세요.
  * Server Core 시스템에서는 /q 플래그를 .NET 4.7 설치 관리자에 전달하여 성공해야 할 수 있습니다.
* 이제 프록시 서비스에서 자동 업그레이드를 지원합니다. 자동 업그레이드는 프록시 서비스와 나란히 설치되는 Microsoft Azure AD Connect 에이전트 업데이트 서비스를 사용합니다. 자동 업그레이드는 기본적으로 '사용함'으로 설정되어 있습니다.
* Set-AzureADPasswordProtectionProxyConfiguration cmdlet을 사용하여 자동 업그레이드를 사용하거나 사용하지 않도록 설정할 수 있습니다. 현재 설정은 Get-AzureADPasswordProtectionProxyConfiguration cmdlet을 사용하여 쿼리할 수 있습니다.
* DC 에이전트 서비스의 서비스 이진 파일은 이름이 AzureADPasswordProtectionDCAgent.exe로 바뀌었습니다.
* 프록시 서비스의 서비스 이진 파일은 이름이 AzureADPasswordProtectionProxy.exe로 바뀌었습니다. 타사 방화벽을 사용하는 경우 방화벽 규칙을 적절하게 수정해야 할 수 있습니다.
  * 참고: 이전 프록시 설치에서 http 프록시 구성 파일을 사용하는 경우 이 업그레이드 후에는 이름을 *proxyservice.exe.config* 에서 *AzureADPasswordProtectionProxy.exe.config* 로 변경해야 합니다.
* DC 에이전트에서 모든 한시적 기능 검사가 제거되었습니다.
* 사소한 버그 수정 및 로깅 기능 향상.

## <a name="12650"></a>1.2.65.0

릴리스 날짜: 2019년 2월 1일

변경 내용:

* DC 에이전트 및 프록시 서비스는 이제 Server Core에서 지원됩니다. 최소 OS 요구 사항은 변경되지 않았습니다. DC 에이전트는 Windows Server 2012이고, 프록시는 Windows Server 2012 R2입니다.
* Register-AzureADPasswordProtectionProxy 및 Register-AzureADPasswordProtectionForest cmdlet은 이제 디바이스 코드 기반 Azure 인증 모드를 지원합니다.
* Get-AzureADPasswordProtectionDCAgent cmdlet은 심하게 훼손되었거나 또는 잘못된 서비스 연결 지점을 무시합니다. 이 변경으로 도메인 컨트롤러가 출력에 여러 번 표시되는 버그가 수정되었습니다.
* Get-AzureADPasswordProtectionSummaryReport cmdlet은 심하게 훼손되었거나 또는 잘못된 서비스 연결 지점을 무시합니다. 이 변경으로 도메인 컨트롤러가 출력에 여러 번 표시되는 버그가 수정되었습니다.
* 프록시 PowerShell 모듈은 이제 %ProgramFiles%\WindowsPowerShell\Modules에 등록됩니다. 머신의 PSModulePath 환경 변수가 더 이상 수정되지 않습니다.
* 새 Get-AzureADPasswordProtectionProxy cmdlet이 추가되어 포리스트 또는 도메인에서 등록된 프록시를 쉽게 검색할 수 있습니다.
* DC 에이전트에서 sysvol 공유의 새 폴더를 사용하여 암호 정책 및 기타 파일을 복제합니다.

   이전 폴더 위치:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   새 폴더 위치:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (이 변경은 "분리된 GPO" 거짓 긍정 경고를 방지하기 위해 수행되었습니다.)

   > [!NOTE]
   > 이전 폴더와 새 폴더 간에는 데이터의 마이그레이션 또는 공유가 수행되지 않습니다. 이전 DC 에이전트 버전에서는 이 버전으로 업그레이드될 때까지 이전 위치를 계속 사용합니다. 모든 DC 에이전트에서 1.2.65.0 버전 이상을 실행하면 이전 sysvol 폴더를 수동으로 삭제할 수 있습니다.

* 이제 DC 에이전트 및 프록시 서비스에서 해당 서비스 연결 지점의 훼손된 복사본을 검색 및 삭제합니다.
* 각 DC 에이전트는 DC 에이전트 및 프록시 서비스 연결 지점 모두에 대해 해당 도메인에서 훼손된 부실 서비스 연결 지점을 정기적으로 삭제합니다. 하트비트 타임스탬프가 7일을 초과하여 오래된 경우 DC 에이전트 및 프록시 서비스 연결 지점은 모두 부실한 것으로 간주됩니다.
* DC 에이전트는 이제 필요에 따라 포리스트 인증서를 갱신합니다.
* 프록시 서비스는 이제 필요에 따라 프록시 인증서를 갱신합니다.
* 암호 유효성 검사 알고리즘 업데이트: 암호 유효성을 검사하기 전에 글로벌로 금지된 암호 목록 및 고객 관련 금지된 암호 목록(구성된 경우)이 결합됩니다. 글로벌 및 고객 관련 목록에 있는 토큰이 포함되어 있으면 이제 지정된 암호를 거부할 수 있습니다(실패 또는 감사 전용). 이를 반영하도록 이벤트 로그 문서가 업데이트되었습니다. [Azure AD 암호 보호 모니터링](howto-password-ban-bad-on-premises-monitor.md)을 참조하세요.
* 성능 및 견고성 수정
* 향상된 로깅

> [!WARNING]
> 시간 제한 기능: 이 릴리스의 DC 에이전트 서비스(1.2.65.0)는 2019년 9월 1일부터 암호 유효성 검사 요청 처리를 중지합니다.  이전 릴리스의 DC 에이전트 서비스(아래 목록 참조)는 2019년 7월 1일부터 처리를 중지합니다. 모든 버전의 DC 에이전트 서비스는 이러한 마감 이전 2개월부터 10021 이벤트를 관리 이벤트 로그에 기록합니다. 다음 GA 릴리스에서는 모든 시간 제한이 제거됩니다. 프록시 에이전트 서비스는 모든 버전에서 시간 제한이 없지만, 이후의 모든 버그 수정 및 기타 향상된 기능을 활용하려면 최신 버전으로 계속 업그레이드해야 합니다.

## <a name="12250"></a>1.2.25.0

릴리스 날짜: 2018년 11월 1일

수정 사항:

* DC 에이전트 및 프록시 서비스가 인증서 트러스트 오류로 인해 더 이상 실패하지 않아야 합니다.
* DC 에이전트 및 프록시 서비스에는 FIPS 규격 컴퓨터에 대한 수정이 있습니다.
* 프록시 서비스는 이제 TLS 1.2 전용 네트워킹 환경에서 제대로 작동합니다.
* 사소한 성능 및 견고성 수정
* 향상된 로깅

변경 내용:

* 이제 프록시 서비스의 최소 필수 OS 수준은 Windows Server 2012 R2입니다. DC 에이전트 서비스의 최소 필수 OS 수준은 Windows Server 2012에서 유지됩니다.
* 프록시 서비스에는 이제 .NET 버전 4.6.2가 필요합니다.
* 암호 유효성 검사 알고리즘은 확장된 문자 정규화 테이블을 사용합니다. 이 변경으로 이전 버전에서 허용되던 암호가 거부될 수 있습니다.

## <a name="12100"></a>1.2.10.0

릴리스 날짜: 2018년 8월 17일

수정 사항:

* Register-AzureADPasswordProtectionProxy 및 Register-AzureADPasswordProtectionForest는 이제 다단계 인증을 지원합니다.
* Register-AzureADPasswordProtectionProxy에는 암호화 오류를 방지하기 위해 도메인에서 WS2012 이상의 도메인 컨트롤러가 필요합니다.
* DC 에이전트 서비스는 시작 시 Azure에서 새 암호 정책을 요청하는 경우 더 안정적입니다.
* DC 에이전트 서비스는 필요한 경우 1시간마다 Azure의 새 암호 정책을 요청하지만 이제 임의로 선택된 시작 시간에 수행됩니다.
* DC 에이전트 서비스가 복제본으로 승격되기 전에 서버에 설치되는 경우 더 이상 새 DC 알림에서 무한 지연을 발생시키지 않습니다.
* 이제 DC 에이전트 서비스는 "Windows Server Active Directory에 대한 암호 보호를 사용하도록 설정" 구성 설정을 유지합니다.
* 이제 DC 에이전트 및 프록시 설치 관리자는 모두 이후 버전으로 업그레이드하는 경우 현재 위치 업그레이드를 지원합니다.

> [!WARNING]
> 1.1.10.3 버전에서 현재 위치 업그레이드는 지원되지 않으며 설치 오류가 발생합니다. 1.2.10 버전 이상으로 업그레이드하려면 먼저 DC 에이전트 및 프록시 서비스 소프트웨어를 완전히 제거한 다음, 처음부터 새 버전을 설치합니다. Azure AD 암호 보호 프록시 서비스를 다시 등록해야 합니다.  포리스트를 다시 등록할 필요가 없습니다.

> [!NOTE]
> DC 에이전트 소프트웨어의 현재 위치 업그레이드에는 다시 부팅이 필요합니다.

* 이제 DC 에이전트 및 프록시 서비스는 FIPS 규격 알고리즘만 사용하도록 구성된 서버에서 실행되도록 지원합니다.
* 사소한 성능 및 견고성 수정
* 향상된 로깅

## <a name="11103"></a>1.1.10.3

릴리스 날짜: 2018년 6월 15일

초기 공개 미리 보기 릴리스

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호 배포](howto-password-ban-bad-on-premises-deploy.md)
