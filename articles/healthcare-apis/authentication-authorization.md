---
title: Azure Healthcare API 인증 및 권한 부여
description: 이 문서에서는 의료 API의 인증 및 권한 부여에 대한 개요를 제공합니다.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 53f31790d5b92234bfd35e723e377e682cd13a4d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252512"
---
# <a name="authentication--authorization-for-the-healthcare-apis-preview"></a>의료 API에 대한 인증 & 권한 부여(미리 보기)

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 의료 API 서비스에 액세스하기 위한 인증 및 권한 부여 프로세스의 개요를 제공합니다.

## <a name="authentication"></a>인증

의료 API는 [OAuth 2.0을](https://oauth.net/2/)지원하는 글로벌 ID 공급자인 [Azure AD(Azure Active Directory)를](../active-directory/index.yml)사용하는 보안 관리형 서비스의 컬렉션입니다.

의료 API 서비스가 스토리지 계정 및 이벤트 허브와 같은 Azure 리소스에 액세스하려면 **시스템 관리 ID를 사용하도록 설정하고 관리 ID에** **적절한 권한을 부여해야** 합니다. 자세한 내용은 [Azure 관리 ID 를 참조하세요.](../active-directory/managed-identities-azure-resources/overview.md)

의료 API는 다른 ID 공급자를 지원하지 않습니다. 그러나 고객은 자신의 ID 공급자를 사용하여 애플리케이션을 보호하고 클라이언트 애플리케이션 및 사용자 데이터 액세스 제어를 관리하여 의료 API와 상호 작용할 수 있습니다.

클라이언트 애플리케이션은 Azure AD에 등록되며 의료 API에 액세스하는 데 사용할 수 있습니다. 사용자 데이터 액세스 제어는 비즈니스 논리를 구현하는 애플리케이션 또는 서비스에서 수행됩니다.

### <a name="application-roles"></a>애플리케이션 역할

의료 API의 인증된 사용자 및 클라이언트 애플리케이션에는 적절한 애플리케이션 역할을 부여해야 합니다.

의료 API의 FHIR 서비스는 다음 역할을 제공합니다.

* **FHIR 데이터 판독기:** FHIR 데이터를 읽고 검색할 수 있습니다.
* **FHIR 데이터 기록기:** FHIR 데이터를 읽고 쓰고 일시 삭제할 수 있습니다.
* **FHIR 데이터 내보내기:** 데이터를 읽고 내보낼 수 있습니다($export 연산자).
* **FHIR 데이터 기여자:** 모든 데이터 평면 작업을 수행할 수 있습니다.
* **FHIR 데이터 변환기:** 변환기를 사용하여 데이터 변환을 수행할 수 있습니다.

의료 API의 DICOM 서비스는 다음 역할을 제공합니다.

* **DICOM 데이터 소유자: DICOM** 데이터를 읽고 쓰고 삭제할 수 있습니다.
* **DICOM 데이터 읽기:** DICOM 데이터를 읽을 수 있습니다.

IoT Connector 애플리케이션 역할이 필요하지 않지만 "Azure Event Hubs Data Receiver"를 사용하여 고객 구독의 이벤트 허브에 저장된 데이터를 검색합니다.

## <a name="authorization"></a>권한 부여

적절한 애플리케이션 역할이 부여된 후 인증된 사용자 및 클라이언트 애플리케이션은 Azure AD에서 발급한 **유효한 액세스 토큰을** 획득하여 의료 API 서비스에 액세스하고 애플리케이션 역할에 정의된 특정 작업을 수행할 수 있습니다.
 
* FHIR 서비스의 경우 액세스 토큰은 서비스 또는 리소스에만 해당됩니다.
* DICOM 서비스의 경우 특정 서비스가 아닌 리소스에 액세스 `dicom.healthcareapis.azure.com` 토큰이 부여됩니다.
* IoT Connector 경우 액세스 토큰은 사용자 또는 클라이언트 애플리케이션에 노출되지 않으므로 필요하지 않습니다.

### <a name="steps-for-authorization"></a>권한 부여 단계

Azure AD 설명서에 자세히 설명된 액세스 토큰을 가져오는 두 가지 일반적인 방법은 권한 부여 코드 흐름 및 [클라이언트 자격 증명 흐름입니다.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) [](../active-directory/develop/v2-oauth2-auth-code-flow.md)

의료 API에 대한 액세스 토큰을 가져오는 경우 **권한 부여 코드 흐름을** 사용하는 단계는 다음과 같습니다.

1. **클라이언트는 Azure AD 권한 부여 엔드포인트에 요청을 보냅니다.** Azure AD는 사용자가 적절한 자격 증명(예: 사용자 이름 및 암호 또는 2단계 인증)을 사용하여 인증하는 로그인 페이지로 클라이언트를 리디렉션합니다. **인증에 성공하면 권한 부여 코드가 클라이언트에 반환됩니다.** Azure AD에서는 이 권한 부여 코드만 클라이언트 애플리케이션 등록에 구성된 등록된 회신 URL로 반환할 수 있습니다.

2. **클라이언트 애플리케이션은 Azure AD 토큰 엔드포인트에서 액세스 토큰에 대한 권한 부여 코드를 교환합니다.** 토큰을 요청할 때 클라이언트 애플리케이션은 클라이언트 비밀을 제공해야 할 수 있습니다(애플리케이션 등록 중에 추가할 수 있음).
 
3. **클라이언트는 의료 API(예:** `GET` FHIR 서비스의 모든 환자 검색 요청)를 요청합니다. 요청을 만들 때 요청 **`HTTP` 헤더 에 액세스 토큰이 포함됩니다(예:** **`Authorization: Bearer xxx`** ).

4. **의료 API 서비스는 토큰에 적절한 클레임(토큰의 속성)이 포함되어 있음을 확인합니다.** 유효한 경우 요청을 완료하고 클라이언트에 데이터를 반환합니다.

클라이언트 **자격 증명 흐름에서** 권한은 애플리케이션 자체에 직접 부여됩니다. 애플리케이션이 리소스에 토큰을 제공하는 경우 리소스는 인증에 관련된 사용자가 없으므로 애플리케이션 자체에 작업을 수행할 수 있는 권한이 있음을 적용합니다. 따라서 다음과 같은 방법으로 **권한 부여 코드 흐름과** 다릅니다.

- 사용자 또는 클라이언트가 대화형으로 로그인할 필요가 없습니다.
- 권한 부여 코드는 필요하지 않습니다.
- 액세스 토큰은 애플리케이션 권한을 통해 직접 얻습니다.

### <a name="access-token"></a>액세스 토큰

액세스 토큰은 사용자 또는 클라이언트에 부여된 클라이언트의 ID, 역할 및 권한에 대한 정보를 전달하는 서명된 [Base64로](https://en.wikipedia.org/wiki/Base64) 인코딩된 속성(클레임) 컬렉션입니다.

의료 API는 일반적으로 [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token)예상합니다. 다음 세 부분으로 구성됩니다. 
* 헤더
* 페이로드(클레임)
* 아래 이미지에 표시된 것처럼 서명입니다. 자세한 내용은 [Azure 액세스 토큰을 참조하세요.](../active-directory/develop/active-directory-configurable-token-lifetimes.md)

[![JASON 웹 토큰 서명. ](media/azure-access-token.png) ](media/azure-access-token.png#lightbox)

와 같은 온라인 도구를 사용하여 토큰 콘텐츠를 볼 수 [https://jwt.ms](https://jwt.ms/) 있습니다. 예를 들어 클레임 세부 정보를 볼 수 있습니다.

|**클레임 유형**          |**값**               |**참고 사항**                               |
|------------------------|------------------------|----------------------------------------|
|aud                     |https://xxx.fhir.azurehealthcareapis.com|토큰의 의도한 수신자를 식별합니다. `id_tokens`에서 액세스 토큰에서 대상은 Azure Portal에서 앱에 할당된 앱의 애플리케이션 ID입니다. 앱은 이 값의 유효성을 검사하고 값이 일치하지 않을 경우 토큰을 거부해야 합니다.|
|iss                     |https://sts.windows.net/{tenantid}/|토큰을 생성하고 반환하는 STS(보안 토큰 서비스) 및 사용자가 인증된 Azure AD 테넌트를 식별합니다. v2.0 엔드포인트에서 토큰을 발급한 경우 URI는 `/v2.0`에서 종료됩니다. 사용자가 Microsoft 계정의 소비자 사용자임을 나타내는 GUID는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. 앱은 클레임의 GUID 부분을 사용하여 앱에 로그인할 수 있는 테넌트 집합을 제한해야 합니다(해당하는 경우).|
|iat                     |(타임스탬프를)            |"발급 시간"은 이 토큰에 대한 인증이 발생한 시간을 나타냅니다.|
|nbf                     |(타임스탬프를)            |"nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다.|
|exp                     |(타임스탬프를)            |"exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. 예를 들어 인증 변경이 필요하거나 토큰 해지가 검색된 경우 리소스가 이 시간 전에 토큰을 거부할 수 있다는 점에 유의해야 합니다.|
|aio                     |E2ZgYxxx                |Azure AD에서 토큰을 다시 사용하기 위해 데이터를 기록하는 데 사용하는 내부 클레임입니다. 무시됩니다.|
|appId                   |e97e1b8c-xxx            |토큰을 사용하는 클라이언트의 애플리케이션 ID입니다. 애플리케이션은 자체적으로 작동할 수도 있고 사용자를 대신하여 작동할 수도 있습니다. 애플리케이션 ID는 일반적으로 애플리케이션 개체를 나타내지만 Azure AD의 서비스 사용자 개체를 나타낼 수도 있습니다.|
|appidacr                |1                       |클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값은 "0"입니다. 클라이언트 ID 및 클라이언트 비밀이 사용되는 경우 값은 "1"입니다. 클라이언트 인증서가 인증에 사용된 경우 값은 "2"입니다.|
|idp                     |https://sts.windows.net/{tenantid}/|토큰의 주체를 인증한 ID 공급자를 기록합니다. 이 값은 예를 들어 사용자 계정이 발급자- 게스트와 동일한 테넌트에 있지 않은 경우 발급자 클레임의 값과 동일합니다. 클레임이 없으면 iss 값을 대신 사용할 수 있음을 의미합니다. 조직 컨텍스트(예: Azure AD 테넌트로 초대된 개인 계정)에서 사용되는 개인 계정의 경우 idp 클레임은 'live.com' 또는 Microsoft 계정 테넌트 9188040d-6c67-4c5b-b112-36a304b66dad를 포함하는 STS URI일 수 있습니다.|
|oid                     |예: tenantid         |이 식별자는 Microsoft ID 시스템의 개체(이 경우 사용자 계정)에 대한 불변 식별자입니다. 이 ID는 애플리케이션에서 사용자를 고유하게 식별합니다. 동일한 사용자에 로그인하는 서로 다른 두 애플리케이션은 oid 클레임에서 동일한 값을 받습니다. Microsoft Graph 이 ID를 지정된 사용자 계정의 ID 속성으로 반환합니다. oid를 사용하면 여러 앱이 사용자를 상호 연관시킬 수 있으므로 이 클레임을 받으려면 프로필 범위가 필요합니다. 참고: 단일 사용자가 여러 테넌트에서 존재하는 경우 사용자는 각 테넌트마다 다른 개체 ID를 포함하게 되며, 사용자가 동일한 자격 증명을 사용하여 각 계정에 로그인하더라도 서로 다른 계정으로 간주됩니다.|
|Rh                       |0.ARoxxx              |Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 무시해야 합니다.|
|sub                      |예: tenantid        |앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 주체는 쌍으로 된 식별자이며 특정 애플리케이션 ID에 고유합니다. 따라서 단일 사용자가 두 개의 다른 클라이언트 ID를 사용하여 두 개의 다른 앱에 로그인하는 경우 해당 앱은 주체 클레임에 두 개의 다른 값을 받게 됩니다. 아키텍처 및 개인 정보 보호 요구 사항에 따라 적합할 수도 있고 적합하지 않을 수도 있습니다.|
|tid                      |예: tenantid        |사용자가 속해 있는 Azure AD 테넌트를 나타내는 GUID입니다. 회사 및 학교 계정의 경우 GUID는 사용자가 속해 있는 조직의 변경이 불가능한 테넌트 ID입니다. 개인 계정의 경우 값은 9188040d-6c67-4c5b-b112-36a304b66dad입니다. 이 클레임을 받으려면 프로필 범위가 필요합니다.
|uti                      |bY5glsxxx             |Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 무시해야 합니다.|
|ver                      |1                     |토큰의 버전을 나타냅니다.|
 
**액세스 토큰은 기본적으로 1시간 동안 유효합니다. 만료되기 전에 새로 고침 토큰을 사용하여 새 토큰을 가져오거나 갱신할 수 있습니다.**

액세스 토큰을 얻으려면 Postman, Visual Studio Code Rest Client 확장, PowerShell, CLI, curl 및 [Azure AD 인증 라이브러리와](../active-directory/develop/reference-v2-libraries.md)같은 도구를 사용할 수 있습니다.

## <a name="encryption"></a>암호화

Azure Healthcare API의 새 서비스를 만들 때 데이터는 기본적으로 **Microsoft 관리형 키를** 사용하여 암호화됩니다. 

* FHIR 서비스는 데이터가 데이터 저장소에 유지될 때 미사용 데이터의 암호화를 제공합니다.
* DICOM 서비스는 포함된 메타데이터를 포함한 이미징 데이터가 데이터 저장소에 유지될 때 미사용 데이터의 암호화를 제공합니다. 메타데이터가 FHIR 서비스에서 추출되고 유지되면 자동으로 암호화됩니다.
* IoT Connector 데이터 매핑 및 정규화 후에 자동으로 암호화되는 FHIR 서비스에 디바이스 메시지를 유지합니다. Azure Storage 사용하여 데이터를 저장하는 Azure Event Hubs로 디바이스 메시지가 전송되는 경우 데이터는 Azure SSE(Azure Storage 서비스 암호화)로 자동으로 암호화됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 의료 API의 인증 및 권한 부여에 대해 배웠습니다. 의료 API 서비스의 인스턴스를 배포하는 방법을 알아보려면 다음을 참조하세요.

>[!div class="nextstepaction"]
>[Azue 포털을 사용하여 의료 API(미리 보기) 작업 영역 배포](healthcare-apis-quickstart.md)
