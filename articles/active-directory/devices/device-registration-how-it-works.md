---
title: Azure AD 디바이스 등록의 작동 방식
description: 관리되는 도메인 및 페더레이션 도메인의 Azure AD 디바이스 등록 흐름
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc24ecd387c011b5a8a36f380916ab815c1f0ab
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538342"
---
# <a name="how-it-works-device-registration"></a>작동 방식: 디바이스 등록

디바이스 등록은 클라우드 기반 인증을 사용하기 위한 필수 구성 요소입니다. 일반적으로 디바이스는 디바이스 등록을 완료하기 위해 Azure AD 또는 하이브리드 Azure AD에 조인됩니다. 이 문서에서는 관리형 환경과 페더레이션 환경에서 Azure AD 조인 및 하이브리드 Azure Ad 조인이 작동하는 방식을 자세히 설명합니다. 이러한 디바이스에서 Azure AD 인증이 작동하는 방식에 대한 자세한 내용은 [기본 새로 고침 토큰](concept-primary-refresh-token.md#detailed-flows) 문서를 참조하세요.

## <a name="azure-ad-joined-in-managed-environments"></a>관리형 환경에서 조인된 Azure AD

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-managed.png" alt-text="관리형 환경에서 조인된 Azure AD 조인 디바이스의 흐름" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-managed.png":::

| 단계 | 설명 |
| :----: | :----------- |
| A | 대부분의 Azure AD 조인 디바이스는 Azure AD 조인 웹 애플리케이션을 CXH(클라우드 환경 호스트) 애플리케이션에서 로드하는 OOBE(첫 실행 경험) 동안 등록됩니다. 애플리케이션에서 Azure AD OpenID 구성 엔드포인트에 GET 요청을 보내 권한 부여 엔드포인트를 검색합니다. Azure AD는 인증 엔드포인트가 포함된 OpenID 구성을 JSON 문서로 애플리케이션에 반환합니다. |
| b | 애플리케이션에서 권한 부여 엔드포인트에 대한 로그인 요청을 작성하고 사용자 자격 증명을 수집합니다. |
| C | 사용자가 UPN 형식의 사용자 이름을 제공하면 애플리케이션이 Azure AD에 GET 요청을 보내 해당 사용자의 영역 정보를 검색합니다. 이 정보는 환경이 관리형 환경인지 아니면 페더레이션 환경인지 확인합니다. Azure AD가 JSON 개체의 정보를 반환합니다. 애플리케이션은 환경이 관리형(페더레이션되지 않은) 환경이라는 것을 확인합니다.<br><br>이 단계의 마지막 과정에서는 애플리케이션이 인증 버퍼를 만들고, OOBE인 경우 OOBE가 끝나면 자동 로그인되도록 인증 버퍼를 임시로 캐시합니다. 애플리케이션이 자격 증명을 Azure AD에 게시하고, 여기서 자격 증명의 유효성이 검사됩니다. Azure AD는 클레임을 사용하여 ID 토큰을 반환합니다. |
| D | 애플리케이션이 MDM 사용 약관(mdm_tou_url 클레임)을 찾습니다. 사용 약관이 있으면 애플리케이션은 클레임 값에서 사용 약관을 검색하고, 그 내용을 사용자에게 표시하고, 사용자가 사용 약관에 동의할 때까지 기다립니다. 이 단계는 선택 사항이며 클레임이 없거나 클레임 값이 비어 있는 경우 생략됩니다. |
| E | 애플리케이션이 ADRS(Azure Device Registration Service)에 디바이스 등록 검색 요청을 보냅니다. Azure DRS는 디바이스 등록을 완료하기 위해 테넌트 관련 URI를 반환하는 검색 데이터 문서를 반환합니다. |
| F | 애플리케이션은 디바이스 키(dkpub/dkpub)라고 하는 TPM 바인딩(기본 설정) RSA 2048 비트 키 쌍을 만듭니다. 애플리케이션은 dkpub 및 공개 키를 사용하여 인증서 요청을 만들고 dkpriv를 사용하여 인증서 요청에 서명합니다. 다음으로, 애플리케이션은 TPM의 스토리지 루트 키에서 두 번째 키 쌍을 파생합니다. 이 키가 전송 키(tkpub/tkpub)입니다. |
| G | 애플리케이션은 ID 토큰, 인증서 요청, tkpub 및 증명 데이터가 포함된 디바이스 등록 요청을 Azure DRS에 보냅니다. Azure DRS는 ID 토큰의 유효성을 검사하고, 디바이스 ID를 만들고, 포함된 인증서 요청을 기반으로 인증서를 만듭니다. 그러면 Azure DRS가 Azure AD에 디바이스 개체를 작성하고 디바이스 ID 및 디바이스 인증서를 클라이언트에 보냅니다. |
| H | Azure DRS에서 디바이스 ID 및 디바이스 인증서를 수신하는 것으로 디바이스 등록이 완료됩니다. 디바이스 ID는 나중에 참조할 수 있도록 저장되고(`dsregcmd.exe /status`에서 볼 수 있음), 디바이스 인증서는 컴퓨터의 개인 저장소에 설치됩니다. 디바이스 등록이 완료되면 MDM 등록 프로세스가 계속 진행됩니다. |

## <a name="azure-ad-joined-in-federated-environments"></a>페더레이션 환경에서 조인된 Azure AD

:::image type="content" source="media/device-registration-how-it-works/device-registration-azure-ad-federated.png" alt-text="페더레이션 환경에서 조인된 Azure AD 조인 디바이스의 흐름" lightbox="media/device-registration-how-it-works/device-registration-azure-ad-federated.png":::

| 단계 | 설명 |
| :----: | :----------- |
| A | 대부분의 Azure AD 조인 디바이스는 Azure AD 조인 웹 애플리케이션을 CXH(클라우드 환경 호스트) 애플리케이션에서 로드하는 OOBE(첫 실행 경험) 동안 등록됩니다. 애플리케이션에서 Azure AD OpenID 구성 엔드포인트에 GET 요청을 보내 권한 부여 엔드포인트를 검색합니다. Azure AD는 인증 엔드포인트가 포함된 OpenID 구성을 JSON 문서로 애플리케이션에 반환합니다. |
| b | 애플리케이션에서 권한 부여 엔드포인트에 대한 로그인 요청을 작성하고 사용자 자격 증명을 수집합니다. |
| C | 사용자가 UPN 형식의 사용자 이름을 제공하면 애플리케이션이 Azure AD에 GET 요청을 보내 해당 사용자의 영역 정보를 검색합니다. 이 정보는 환경이 관리형 환경인지 아니면 페더레이션 환경인지 확인합니다. Azure AD가 JSON 개체의 정보를 반환합니다. 애플리케이션은 환경이 페더레이션 환경이라는 것을 확인합니다.<br><br>애플리케이션은 반환된 JSON 영역 개체의 AuthURL 값(온-프레미스 STS 로그인 페이지)으로 리디렉션합니다. 애플리케이션은 STS 웹 페이지를 통해 자격 증명을 수집합니다. |
| D | 애플리케이션은 온-프레미스 STS에 자격 증명을 게시합니다. 이 과정에서 추가 인증 요소가 필요할 수 있습니다. 온-프레미스 STS는 사용자를 인증하고 토큰을 반환합니다. 애플리케이션은 인증을 위해 Azure AD에 토큰을 게시합니다. Azure AD는 토큰의 유효성을 검사하고 클레임을 사용하여 ID 토큰을 반환합니다. |
| E | 애플리케이션이 MDM 사용 약관(mdm_tou_url 클레임)을 찾습니다. 사용 약관이 있으면 애플리케이션은 클레임 값에서 사용 약관을 검색하고, 그 내용을 사용자에게 표시하고, 사용자가 사용 약관에 동의할 때까지 기다립니다. 이 단계는 선택 사항이며 클레임이 없거나 클레임 값이 비어 있는 경우 생략됩니다. |
| F | 애플리케이션이 ADRS(Azure Device Registration Service)에 디바이스 등록 검색 요청을 보냅니다. Azure DRS는 디바이스 등록을 완료하기 위해 테넌트 관련 URI를 반환하는 검색 데이터 문서를 반환합니다. |
| G | 애플리케이션은 디바이스 키(dkpub/dkpub)라고 하는 TPM 바인딩(기본 설정) RSA 2048 비트 키 쌍을 만듭니다. 애플리케이션은 dkpub 및 공개 키를 사용하여 인증서 요청을 만들고 dkpriv를 사용하여 인증서 요청에 서명합니다. 다음으로, 애플리케이션은 TPM의 스토리지 루트 키에서 두 번째 키 쌍을 파생합니다. 이 키가 전송 키(tkpub/tkpub)입니다. |
| H | 애플리케이션은 ID 토큰, 인증서 요청, tkpub 및 증명 데이터가 포함된 디바이스 등록 요청을 Azure DRS에 보냅니다. Azure DRS는 ID 토큰의 유효성을 검사하고, 디바이스 ID를 만들고, 포함된 인증서 요청을 기반으로 인증서를 만듭니다. 그러면 Azure DRS가 Azure AD에 디바이스 개체를 작성하고 디바이스 ID 및 디바이스 인증서를 클라이언트에 보냅니다. |
| I | Azure DRS에서 디바이스 ID 및 디바이스 인증서를 수신하는 것으로 디바이스 등록이 완료됩니다. 디바이스 ID는 나중에 참조할 수 있도록 저장되고(`dsregcmd.exe /status`에서 볼 수 있음), 디바이스 인증서는 컴퓨터의 개인 저장소에 설치됩니다. 디바이스 등록이 완료되면 MDM 등록 프로세스가 계속 진행됩니다. |

## <a name="hybrid-azure-ad-joined-in-managed-environments"></a>관리형 환경에서 조인된 하이브리드 Azure AD 조인

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png" alt-text="관리형 환경에서 조인된 하이브리드 Azure AD 조인 디바이스의 흐름" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-managed.png":::

| 단계 | 설명 |
| :----: | ----------- |
| A | 사용자가 도메인 자격 증명을 사용하여 Windows 10 컴퓨터에 가입된 도메인에 로그인합니다. 이 자격 증명은 사용자 이름과 암호 또는 스마트 카드 인증이어도 됩니다. 사용자 로그인이 자동 디바이스 조인 작업을 트리거합니다. 자동 디바이스 조인 작업은 도메인 가입 시 트리거되고 매시간 다시 시도됩니다. 사용자 로그인에 전적으로 의존하지는 않습니다. |
| b | 이 작업은 Active Directory의 구성 파티션에 저장된 서비스 연결 지점(`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`)의 keywords 특성에 대한 LDAP 프로토콜을 사용하여 Active Directory를 쿼리합니다. 키워드 특성에 반환된 값에 따라 디바이스 등록이 ADRS(Azure Device Registration Service)로 전송되거나 온-프레미스에서 호스팅되는 엔터프라이즈 디바이스 등록 서비스로 전송됩니다. |
| C | 관리형 환경의 경우 이 작업은 자체 서명된 인증서의 형태로 초기 인증 자격 증명을 만듭니다. 이 작업은 LDAP를 사용하여 Active Directory의 컴퓨터 개체에 있는 userCertificate 특성에 인증서를 씁니다. |
| D | userCertificate 특성의 인증서가 포함된 컴퓨터를 나타내는 디바이스 개체가 Azure AD에 생성되기 전에는 컴퓨터에서 Azure DRS에 인증할 수 없습니다. Azure AD Connect는 특성이 바뀐 것을 탐지합니다. 다음 동기화 주기에 Azure AD Connect는 userCertificate, 개체 GUID 및 컴퓨터 SID를 Azure DRS로 보냅니다. Azure DRS는 특성 정보를 사용하여 Azure AD에 디바이스 개체를 만듭니다. |
| E | 자동 디바이스 조인 작업은 사용자가 로그인할 때마다 또는 매시간 트리거되며, userCertificate 특성 중에서 공개 키의 해당 프라이빗 키를 사용하여 컴퓨터를 Azure AD에 인증하려고 시도합니다. Azure AD는 컴퓨터를 인증하고 컴퓨터에 ID 토큰을 발행합니다. |
| F | 이 작업은 디바이스 키(dkpub/dkpub)라고 하는 TPM 바인딩(기본 설정) RSA 2048 비트 키 쌍을 만듭니다. 애플리케이션은 dkpub 및 공개 키를 사용하여 인증서 요청을 만들고 dkpriv를 사용하여 인증서 요청에 서명합니다. 다음으로, 애플리케이션은 TPM의 스토리지 루트 키에서 두 번째 키 쌍을 파생합니다. 이 키가 전송 키(tkpub/tkpub)입니다. |
| G | 이 작업은 ID 토큰, 인증서 요청, tkpub 및 증명 데이터가 포함된 디바이스 등록 요청을 Azure DRS에 보냅니다. Azure DRS는 ID 토큰의 유효성을 검사하고, 디바이스 ID를 만들고, 포함된 인증서 요청을 기반으로 인증서를 만듭니다. 그러면 Azure DRS가 Azure AD의 디바이스 개체를 업데이트하고 디바이스 ID 및 디바이스 인증서를 클라이언트에 보냅니다. |
| H | Azure DRS에서 디바이스 ID 및 디바이스 인증서를 수신하는 것으로 디바이스 등록이 완료됩니다. 디바이스 ID는 나중에 참조할 수 있도록 저장되고(`dsregcmd.exe /status`에서 볼 수 있음), 디바이스 인증서는 컴퓨터의 개인 저장소에 설치됩니다. 디바이스 등록이 완료되면 작업이 종료됩니다. |

## <a name="hybrid-azure-ad-joined-in-federated-environments"></a>페더레이션 환경에서 조인된 하이브리드 Azure AD

:::image type="content" source="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png" alt-text="관리형 환경에서 조인된 하이브리드 Azure AD 조인 디바이스의 흐름" lightbox="media/device-registration-how-it-works/device-registration-hybrid-azure-ad-federated.png":::

| 단계 | 설명 |
| :----: | :----------- |
| A | 사용자가 도메인 자격 증명을 사용하여 Windows 10 컴퓨터에 가입된 도메인에 로그인합니다. 이 자격 증명은 사용자 이름과 암호 또는 스마트 카드 인증이어도 됩니다. 사용자 로그인이 자동 디바이스 조인 작업을 트리거합니다. 자동 디바이스 조인 작업은 도메인 가입 시 트리거되고 매시간 다시 시도됩니다. 사용자 로그인에 전적으로 의존하지는 않습니다. |
| b | 이 작업은 Active Directory의 구성 파티션에 저장된 서비스 연결 지점(`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=corp,DC=contoso,DC=com`)의 keywords 특성에 대한 LDAP 프로토콜을 사용하여 Active Directory를 쿼리합니다. 키워드 특성에 반환된 값에 따라 디바이스 등록이 ADRS(Azure Device Registration Service)로 전송되거나 온-프레미스에서 호스팅되는 엔터프라이즈 디바이스 등록 서비스로 전송됩니다. |
| C | 페더레이션 환경의 경우 컴퓨터는 Windows 통합 인증을 사용하여 엔터프라이즈 디바이스 등록 엔드포인트를 인증합니다. 엔터프라이즈 디바이스 등록 서비스는 개체 GUID, 컴퓨터 SID 및 도메인 가입 상태에 대한 클레임이 포함된 토큰을 만들고 반환합니다. 이 작업은 Azure AD에 토큰과 클레임을 제출하고, Azure AD에서 토큰과 클레임의 유효성이 검사됩니다. Azure AD는 실행 중인 작업에 ID 토큰을 반환합니다. |
| D | 애플리케이션은 디바이스 키(dkpub/dkpub)라고 하는 TPM 바인딩(기본 설정) RSA 2048 비트 키 쌍을 만듭니다. 애플리케이션은 dkpub 및 공개 키를 사용하여 인증서 요청을 만들고 dkpriv를 사용하여 인증서 요청에 서명합니다. 다음으로, 애플리케이션은 TPM의 스토리지 루트 키에서 두 번째 키 쌍을 파생합니다. 이 키가 전송 키(tkpub/tkpub)입니다. |
| E | 온-프레미스 페더레이션 애플리케이션에 SSO를 제공하기 위해 이 작업은 온-프레미스 STS로부터 엔터프라이즈 PRT를 요청합니다. Active Directory Federation Services 역할을 실행하는 Windows Server 2016이 요청의 유효성을 검사하고 이 요청을 실행 중인 작업에 반환합니다. |
| F | 이 작업은 ID 토큰, 인증서 요청, tkpub 및 증명 데이터가 포함된 디바이스 등록 요청을 Azure DRS에 보냅니다. Azure DRS는 ID 토큰의 유효성을 검사하고, 디바이스 ID를 만들고, 포함된 인증서 요청을 기반으로 인증서를 만듭니다. 그러면 Azure DRS가 Azure AD에 디바이스 개체를 작성하고 디바이스 ID 및 디바이스 인증서를 클라이언트에 보냅니다. Azure DRS에서 디바이스 ID 및 디바이스 인증서를 수신하는 것으로 디바이스 등록이 완료됩니다. 디바이스 ID는 나중에 참조할 수 있도록 저장되고(`dsregcmd.exe /status`에서 볼 수 있음), 디바이스 인증서는 컴퓨터의 개인 저장소에 설치됩니다. 디바이스 등록이 완료되면 작업이 종료됩니다. |
| G | Azure AD Connect 디바이스 쓰기 저장을 사용하면 Azure AD Connect는 다음 동기화 주기에 Azure AD의 업데이트를 요청합니다(인증서 신뢰를 사용하는 하이브리드 배포에는 디바이스 쓰기 저장이 필요함). Azure AD는 디바이스 개체와 일치하는 동기화된 컴퓨터 개체 간의 상관 관계를 지정합니다. Azure AD Connect는 개체 GUID 및 컴퓨터 SID가 포함된 디바이스 개체를 수신하고 디바이스 개체를 Active Directory에 씁니다. |

## <a name="next-steps"></a>다음 단계

- [Azure AD 조인 디바이스](concept-azure-ad-join.md)
- [Azure AD 등록 디바이스](concept-azure-ad-register.md)
- [하이브리드 Azure AD 가입 디바이스](concept-azure-ad-join-hybrid.md)
- [주 새로 고침 토큰이란?](concept-primary-refresh-token.md)
- [Azure AD Connect: 디바이스 옵션](../hybrid/how-to-connect-device-options.md)
