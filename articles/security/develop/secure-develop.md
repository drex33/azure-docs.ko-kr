---
title: Microsoft Azure에서 보안 애플리케이션 개발
description: 이 문서에서는 웹 애플리케이션 프로젝트의 구현 및 확인 단계에서 고려할 모범 사례를 설명합니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 03/21/2021
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 2f81cd3e37b6fefea1a76a8a4bb50c756ff1fca4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463528"
---
# <a name="develop-secure-applications-on-azure"></a>Azure에서 보안 애플리케이션 개발

이 문서에서는 클라우드용 애플리케이션을 개발할 때 고려해야 할 보안 및 제어 작업에 대해 설명합니다. Microsoft [SDL(보안 개발 수명 주기)](/previous-versions/windows/desktop/cc307891(v=msdn.10))의 구현 및 확인 단계에서 고려해야 하는 보안 질문과 개념을 다룹니다. 보다 안전한 애플리케이션을 개발하는 데 사용할 수 있는 작업과 Azure 서비스를 정의하는 데 도움을 주는 것이 이 강좌의 목적입니다.

이 문서에서 다루는 SDL 단계는 다음과 같습니다.

- 구현
- 확인

## <a name="implementation"></a>구현

구현 단계의 초점은 문제를 초기에 방지하기 위한 모범 사례를 설정하고 코드에서 보안 문제를 발견하여 제거하는 것입니다. 이렇게 하면 애플리케이션이 개발자의 의도와 다른 방향으로 사용될 때 우발적인 또는 의도적인 오용으로부터 애플리케이션을 보호할 수 있습니다.

### <a name="perform-code-reviews"></a>코드 검토 수행

코드를 체크 인하기 전에, 코드 검토를 수행하여 전체적인 코드 품질을 높이고 버그 생성의 위험을 줄일 수 있습니다. [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs)를 사용하여 코드 검토 프로세스를 관리할 수 있습니다.

### <a name="perform-static-code-analysis"></a>정적 코드 분석 수행

[정적 코드 분석](https://owasp.org/www-community/controls/Static_Code_Analysis)(*소스 코드 분석* 이라고도 함)은 일반적으로 코드 검토의 일부로 수행됩니다. 정적 코드 분석은 일반적으로 정적 코드 분석 도구를 실행하고 [오류 검사](https://en.wikipedia.org/wiki/Taint_checking) 및 [데이터 흐름 분석](https://en.wikipedia.org/wiki/Data-flow_analysis) 같은 기술을 사용하여 비-실행 코드의 잠재적 취약성을 찾는 것을 의미합니다.

Azure Marketplace는 정적 코드 분석을 수행하 고 코드 검토를 지원하는 [개발자 도구](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)를 제공합니다.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>애플리케이션에 대한 모든 입력의 유효성을 검사하고 정리

모든 입력을 신뢰할 수 없는 것으로 간주하여 가장 일반적인 웹 애플리케이션 취약성으로부터 애플리케이션을 보호하세요. 신뢰할 수 없는 데이터는 주입 공격에 사용되는 수단입니다. 애플리케이션의 입력으로는 URL의 매개 변수, 사용자의 입력, 데이터베이스 또는 API의 데이터, 전달되는 항목 중 사용자가 잠재적으로 조작할 수 있는 모든 항목이 포함됩니다. 애플리케이션에서 데이터를 어떤 방식으로든 사용하기 전에(사용자에게 데이터를 다시 표시하는 것까지 포함) 데이터가 구문적으로, 의미적으로 올바른지 [유효성을 검사](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs)해야 합니다.

올바른 형식의 데이터만 워크플로에 진입하도록 데이터 흐름 초기에 입력의 유효성을 검사해야 합니다. 잘못된 형식의 데이터를 데이터베이스에 유지하거나 다운스트림 구성 요소에서 오작동을 트리거하도록 두면 안 됩니다.

입력 구문 유효성 검사에 일반적으로 사용되는 두 가지 방법은 차단 목록과 허용 목록입니다.

  - 차단 목록은 특정 사용자 입력에 "악의적인 것으로 알려진" 콘텐츠가 포함되지 않았는지 확인하려고 시도합니다.

  - 허용 목록은 특정 사용자 입력이 "알려진 양호한" 입력 세트와 일치하는지 확인하려고 시도합니다. 문자 기반 허용 목록은 애플리케이션에서 사용자 입력에 "알려진 양호한" 문자만 들어 있거나 입력이 알려진 형식과 일치하는지 확인하는 형식의 허용 목록입니다.

    예를 들어 사용자 이름에 영숫자만 포함되어 있거나 정확히 두 개의 숫자가 포함되어 있는지 확인할 수 있습니다.

허용 목록은 안전한 소프트웨어를 빌드하려는 개발자들이 선호하는 방법입니다. 차단 목록은 잠재적인 악성 입력을 하나도 놓치지 않고 완전한 목록을 생각한다는 것이 불가능하므로 오류가 발생하기 쉽습니다.

이 방법은 서버에만 사용하고 클라이언트 쪽(또는 서버와 클라이언트 쪽)에는 사용하지 마세요.

### <a name="verify-your-applications-outputs"></a>애플리케이션의 출력 확인

시각적으로 또는 문서 내에서 표시하는 모든 출력은 항상 인코딩 및 이스케이프되어야 합니다. *출력 인코딩* 이라고도 하는 [이스케이프](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29)는 신뢰할 수 없는 데이터가 삽입 공격의 수단으로 사용되지 않도록 하는 데 사용됩니다. 이스케이프를 데이터 유효성 검사와 함께 사용하면 계층형 방어를 통해 시스템 보안을 전체적으로 강화할 수 있습니다.

이스케이프를 사용하면 모든 항목이 *출력* 으로 표시됩니다. 또한 이스케이프를 통해 실행용 데이터가 아니라는 것을 인터프리터에 알릴 수 있으며, 이렇게 하면 공격이 작동하지 못하게 차단됩니다. 이것을 *XSS(사이트 간 스크립팅*)라고 하며 또 다른 형태의 일반적인 공격 기술입니다.

타사 웹 프레임워크를 사용하는 경우 [OWASP XSS 방지 참고 자료](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)를 사용하여 웹 사이트에서 출력 인코딩에 대한 옵션을 확인할 수 있습니다.

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>데이터베이스에 연결할 때 매개 변수가 있는 쿼리 사용

절대로 코드에 인라인 데이터베이스 쿼리를 "즉석에서" 만들어 데이터베이스에 직접 보내지 마세요. 애플리케이션에 삽입된 악성 코드가 데이터베이스를 훔치거나, 지우거나, 수정할 가능성이 있습니다. 애플리케이션이 데이터베이스를 호스트하는 운영 체제에서 악의적 운영 체제 명령을 실행하는 데 이용될 수도 있습니다.

매개 변수가 있는 쿼리 또는 저장 프로시저를 대신 사용하세요. 매개 변수가 있는 쿼리를 사용하면 쿼리 명령문의 일부로 처리될 걱정 없이 코드에서 안전하게 프로시저를 호출하고 문자열을 전달할 수 있습니다.

### <a name="remove-standard-server-headers"></a>표준 서버 헤더 제거

Server, X-Powered-By, X-AspNet-Version 등의 헤더는 서버 및 기본 기술에 대한 정보를 표시합니다. 애플리케이션의 지문을 채취하지 못하게 이러한 헤더를 표시하지 않는 것이 좋습니다.
[Azure 웹 사이트에서 표준 서버 헤더 제거](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)를 참조하세요.

### <a name="segregate-your-production-data"></a>프로덕션 데이터 분리

프로덕션 데이터 또는 "실제" 데이터를 개발, 테스트 또는 회사의 의도와 다른 용도로 사용하면 안 됩니다. 모든 개발 및 테스트에는 마스킹된([익명화된](https://en.wikipedia.org/wiki/Data_anonymization)) 데이터 세트를 사용해야 합니다.

이렇게 하면 실제 데이터에 액세스할 수 있는 사용자가 별로 없으므로 공격 노출 영역을 줄일 수 있습니다. 또한 개인 데이터를 볼 수 있는 직원이 별로 없으므로 기밀 위반 가능성이 줄어듭니다.

### <a name="implement-a-strong-password-policy"></a>강력한 암호 정책 구현

무차별 암호 대입 및 사전 기반 추측 공격을 방어하려면 사용자가 복잡한 암호(예: 영숫자 및 특수 문자로 구성된 12자 이상의 암호)를 만들도록 강력한 암호 정책을 구현해야 합니다.

Azure Active Directory B2C는 [셀프 서비스 암호 재설정](../../active-directory-b2c/add-password-reset-policy.md), [강제 암호 재설정](../../active-directory-b2c/force-password-reset.md) 등을 제공하여 암호 관리를 도와줍니다.

기본 계정에 대한 공격을 방어하려면 모든 키와 암호를 바꿀 수 있는지, 리소스를 설치한 후 키와 암호가 생성되거나 바뀌는지 확인합니다.

애플리케이션에서 암호를 자동으로 생성해야 하는 경우 암호가 무작위로 생성되고 암호의 엔트로피가 높은지 확인합니다.

### <a name="validate-file-uploads"></a>파일 업로드 확인

애플리케이션에서 [파일 업로드](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)를 허용하는 경우 이 위험한 작업에 대해 수행할 수 있는 예방 조치를 생각해야 합니다. 많은 공격의 첫 번째 단계는 공격을 받는 시스템에 악성 코드를 심는 것입니다. 파일 업로드를 사용하면 공격자가 이 첫 번째 단계를 달성하도록 도와주는 꼴입니다. OWASP는 파일의 유효성을 검사하여 업로드하는 파일을 안전하게 보호하는 솔루션을 제공합니다.

맬웨어 방지 보호 프로그램은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별하고 제거하는 데 도움이 됩니다. [Microsoft Antimalware](../fundamentals/antimalware.md) 또는 Microsoft 파트너의 엔드포인트 보호 솔루션([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 및 [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection))을 설치할 수 있습니다.

[Microsoft Antimalware](../fundamentals/antimalware.md)에는 실시간 보호, 예약된 검색, 맬웨어 치료, 서명 업데이트, 엔진 업데이트, 샘플 보고 및 제외 이벤트 컬렉션과 같은 기능이 포함되어 있습니다. 배포 및 기본 제공 검색(경고 및 인시던트)을 용이하게 하기 위해 [Azure Security Center](../../security-center/security-center-partner-integration.md)와 Microsoft 맬웨어 방지 프로그램 및 파트너 솔루션을 통합할 수 있습니다.

### <a name="dont-cache-sensitive-content"></a>중요한 콘텐츠를 캐시하지 말 것

브라우저에서 중요한 콘텐츠를 캐시하지 마세요. 브라우저는 캐싱 및 기록에 대한 정보를 저장할 수 있습니다. 캐시된 파일은 Internet Explorer의 경우 임시 인터넷 파일 폴더 같은 폴더에 저장됩니다. 이러한 페이지를 다시 참조할 때 브라우저는 캐시의 페이지를 표시합니다. 중요한 정보(주소, 신용 카드 세부 정보, 사회 보장 번호, 사용자 이름)가 사용자에게 표시되는 경우 브라우저의 캐시에 정보를 저장하고 브라우저의 캐시를 검사하거나 브라우저의 **뒤로** 단추를 눌러 검색할 수 있습니다.

## <a name="verification"></a>확인

확인 단계에는 코드가 이전 단계에서 설정한 보안 및 개인 정보 교리를 충족하게 만들기 위한 포괄적인 노력이 포함됩니다.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>애플리케이션 종속성의 취약점을 찾아서 수정

애플리케이션과 종속 라이브러리를 검사하여 알려진 취약한 구성 요소를 파악합니다. 이 검사에 사용할 수 있는 제품으로는 [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/) 및 [Black Duck](https://www.blackducksoftware.com/)이 있습니다.

[Tinfoil Security](https://www.tinfoilsecurity.com/)에서 제공하는 취약성 검사는 Azure App Service Web Apps에 사용할 수 있습니다. [App Service를 통한 Tinfoil Security 검사](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)는 악의적인 행위자가 취약점을 이용하기 전에 미리 찾아서 해결할 수 있는 빠르고 통합적이고 경제적인 수단을 개발자와 관리자에게 제공합니다.

> [!NOTE]
> [Tinfoil Security를 Azure AD와 통합](../../active-directory/saas-apps/tinfoil-security-tutorial.md)할 수도 있습니다. Tinfoil Security를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.
>  - Tinfoil Security에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
>  - 사용자가 자신의 Azure AD 계정을 사용하여 Tinfoil Security에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
>  - 중앙의 단일 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

### <a name="test-your-application-in-an-operating-state"></a>애플리케이션이 작동하는 상태에서 테스트

DAST(동적 애플리케이션 보안 테스트)는 애플리케이션이 작동하는 상태에서 테스트하여 보안 취약성을 찾는 프로세스입니다. DAST 도구는 프로그램이 실행 중인 상태에서 프로그램을 분석하여 메모리 손상, 안전하지 않은 서버 구성, 사이트 간 스크립팅, 사용자 권한 문제, SQL 삽입, 기타 중요한 보안 문제 등의 보안 취약성을 찾습니다.

DAST는 SAST(정적 애플리케이션 보안 테스트)와는 다릅니다. SAST 도구는 코드가 실행되지 않을 때 소스 코드 또는 컴파일된 코드 버전을 분석하여 보안 결함을 찾습니다.

가급적이면 보안 전문가([침투 테스터](../fundamentals/pen-testing.md) 또는 취약성 평가자)의 도움을 받아 DAST를 수행하세요. 보안 전문가의 도움을 받을 수 없으면 약간의 학습을 통해 웹 프록시 스캐너를 사용하여 DAST를 직접 수행할 수 있습니다. 코드에 명백한 보안 문제가 발생하지 않도록 DAST 스캐너를 일찍 연결하세요. 웹 애플리케이션 취약성 스캐너 목록은 [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) 사이트를 참조하세요.

### <a name="perform-fuzz-testing"></a>퍼지 테스트 수행

[퍼지 테스트](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)에서는 애플리케이션에 잘못된 데이터 또는 임의의 데이터를 의도적으로 삽입하여 프로그램 오류를 유도합니다. 프로그램 오류를 유도하면 애플리케이션을 릴리스하기 전에 잠재적 보안 문제를 발견하는 데 도움이 됩니다.

[Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/)은 소프트웨어에서 보안에 중요한 버그를 찾는 Microsoft 고유의 퍼지 테스트 서비스입니다.

### <a name="conduct-attack-surface-review"></a>공격 노출 영역 검토 수행

코드 완성 후 공격 노출 영역을 검토하면 애플리케이션 또는 시스템의 디자인 또는 구현 변경이 고려되었는지 확인할 수 있습니다. 위협 모델을 포함하여 변경의 결과로 생성된 새로운 공격 벡터를 검토하여 완화하는 데 도움이 됩니다.

애플리케이션을 검사하여 공격 노출 영역 그림을 만들 수 있습니다. Microsoft는 [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=58105)라고 하는 공격 노출 영역 분석 도구를 제공합니다. [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/) 및 [w3af](http://w3af.sourceforge.net/)를 비롯한 여러 상용 동적 테스트 및 취약성 검사 도구 또는 서비스 중에 선택할 수 있습니다. 이러한 검사 도구는 앱을 탐색하고 웹을 통해 액세스할 수 있는 애플리케이션의 일부를 매핑합니다. Azure Marketplace에서 비슷한 [개발자 도구](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)를 검색할 수도 있습니다.

### <a name="perform-security-penetration-testing"></a>보안 침투 테스트 수행

애플리케이션의 보안 상태를 유지하는 것은 다른 기능을 테스트하는 것만큼 중요합니다. [침투 테스트](../fundamentals/pen-testing.md)를 빌드 및 배포 프로세스의 표준 파트로 설정하세요. 배포된 애플리케이션에서 보안 테스트 및 취약성 검사를 주기적으로 실시하고 열린 포트, 엔드포인트 및 공격을 모니터링하세요.

### <a name="run-security-verification-tests"></a>보안 확인 테스트 실행

[AzSK(Secure DevOps Kit for Azure)](https://azsk.azurewebsites.net/index.html)에는 Azure 플랫폼의 여러 서비스에 대한 SVT가 포함되어 있습니다. 이러한 SVT를 주기적으로 실행하여 Azure 구독과 애플리케이션을 구성하는 여러 리소스를 안전한 상태로 유지해야 합니다. SVT를 Visual Studio 확장으로 사용할 수 있는 AzSK의 CI/CD(연속 통합/지속적인 배포) 확장 기능을 사용하여 이러한 테스트를 자동화할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 보안 애플리케이션을 디자인하고 배포하는 데 도움이 되는 보안 제어 및 작업을 권장합니다.

- [보안 애플리케이션 디자인](secure-design.md)
- [보안 애플리케이션 배포](secure-deploy.md)
