---
title: Azure AD Multifactor Authentication 데이터 보존
description: Azure AD Multifactor Authentication에서 귀하와 귀하의 사용자에 대해 저장하는 개인 및 조직 데이터와 영역의 국가/지역 내에 유지되는 데이터에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa8098192340505ecb7555f407375690c36ecdfb
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525807"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication에 관한 데이터 보존 및 고객 데이터

Azure AD(Azure Active Directory)는 Microsoft 365 또는 Azure와 같은 Microsoft 온라인 서비스를 구독할 때 조직에서 제공하는 주소에 따라 지리적 위치에 고객 데이터를 저장합니다. 고객 데이터를 저장하는 위치에 관한 정보는 Microsoft 보안 센터의 [데이터가 있는 위치](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)를 참조하세요.

클라우드 기반 Azure AD 다단계 인증과 MFA 서버는 개인 데이터와 조직 데이터를 처리하고 저장합니다. 이 문서에서는 저장되는 데이터 및 위치를 간략하게 설명합니다.

Azure AD Multifactor Authentication 서비스는 미국, 유럽 및 아시아 태평양에 데이터 센터가 있습니다. 다음 작업은 명시된 경우를 제외하고 지역 데이터 센터에서 시작됩니다.

* 다단계 인증 전화 통화는 고객 지역의 데이터 센터에서 발생하고 글로벌 공급자를 통해 라우팅됩니다. 사용자 지정 인사말을 사용하는 전화 통화는 항상 미국의 데이터 센터에서 발생합니다.
* 다른 지역의 범용 사용자 인증 요청은 현재 사용자의 위치를 ​​기반으로 처리됩니다.
* Microsoft Authenticator 앱을 사용하는 푸시 알림은 현재 사용자의 위치를 기반으로 지역 데이터 센터에서 처리됩니다. Apple Push Notification Service와 같은 공급업체별 디바이스 서비스는 사용자 위치 외부에 있을 수 있습니다.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication에서 저장한 개인 데이터

개인 데이터는 특정 사용자와 연결된 사용자 수준 정보입니다. 다음 데이터 저장소에는 개인 정보가 포함되어 있습니다.

* 차단된 사용자
* 바이패스된 사용자
* Microsoft Authenticator 디바이스 토큰 변경 요청
* 다단계 인증 작업 보고서-다단계 인증 온-프레미스 구성 요소(NPS 확장, AD FS 어댑터 및 MFA 서버)에서 다단계 인증 작업을 저장합니다.
* Microsoft Authenticator 활성화

이 정보는 90일 동안 보존됩니다.

Azure AD Multifactor Authentication은 사용자 이름, 전화번호 또는 IP 주소와 같은 개인 데이터를 로그하지 않습니다. 그러나 *UserObjectId* 는 사용자에 대한 인증 시도를 식별합니다. 로그 데이터는 30일 동안 저장됩니다.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication에서 저장된 데이터

Azure AD B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS (Active Directory Federation Services) 어댑터를 제외한 Azure 퍼블릭 클라우드의 경우 다음 개인 데이터가 저장됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | 다단계 인증 로그     |
| 단방향 SMS                          | 다단계 인증 로그     |
| 음성 통화                           | 다단계 인증 로그<br/>다단계 인증 작업 보고서 데이터 저장소<br/>차단된 사용자(사기 행위를 보고한 경우) |
| Microsoft Authenticator 알림 | 다단계 인증 로그<br/>다단계 인증 작업 보고서 데이터 저장소<br/>차단된 사용자(사기 행위를 보고한 경우)<br/>Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

Microsoft Azure Government, Microsoft Azure 독일, 21Vianet에서 운영하는 Microsoft Azure, Azure AD B2C 인증, NPS 확장 및 Windows Server 2016 또는 2019 AD FS 어댑터의 경우 다음 개인 데이터가 저장됩니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | 다단계 인증 로그<br/>다단계 인증 작업 보고서 데이터 저장소 |
| 단방향 SMS                          | 다단계 인증 로그<br/>다단계 인증 작업 보고서 데이터 저장소 |
| 음성 통화                           | 다단계 인증 로그<br/>다단계 인증 작업 보고서 데이터 저장소<br/>차단된 사용자(사기 행위를 보고한 경우) |
| Microsoft Authenticator 알림 | 다단계 인증 로그<br/>다단계 인증 작업 보고서 데이터 저장소<br/>차단된 사용자(사기 행위를 보고한 경우)<br/>Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

### <a name="data-stored-by-mfa-server"></a>MFA 서버에서 저장하는 데이터

MFA 서버를 사용하는 경우 다음 개인 데이터가 저장됩니다.

> [!IMPORTANT]
> 2019년 7월 1일부터 Microsoft는 더 이상 새 배포를 위한 MFA 서버를 제공하지 않습니다. 사용자의 다단계 인증이 필요한 신규 고객은 클라우드 기반 Azure AD Multifactor Authentication을 사용해야 합니다. 2019년 7월 1일 이전에 다단계 인증 서버를 활성화한 기존 고객은 종전과 같이 최신 버전 및 이후 업데이트를 다운로드하고 활성화 자격 증명을 생성할 수 있습니다.

| 이벤트 유형                           | 데이터 저장소 유형 |
|--------------------------------------|-----------------|
| OATH 토큰                           | 다단계 인증 로그<br />다단계 인증 작업 보고서 데이터 저장소 |
| 단방향 SMS                          | 다단계 인증 로그<br />다단계 인증 작업 보고서 데이터 저장소 |
| 음성 통화                           | 다단계 인증 로그<br />다단계 인증 작업 보고서 데이터 저장소<br />차단된 사용자(사기 행위를 보고한 경우) |
| Microsoft Authenticator 알림 | 다단계 인증 로그<br />다단계 인증 작업 보고서 데이터 저장소<br />차단된 사용자(사기 행위를 보고한 경우)<br />Microsoft Authenticator 디바이스 토큰이 변경될 때 변경 요청 |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Azure AD Multifactor Authentication에서 저장된 조직 데이터

조직 데이터는 구성 또는 환경 설치를 노출할 수 있는 테넌트 수준 정보입니다. 다음 Azure Portal 다단계 인증 페이지에서 테넌트 설정은 수신 전화 인증 요청에 대한 잠금 임계값 또는 호출자 ID 정보와 같은 조직 데이터를 저장할 수 있습니다.

* 계정 잠금
* 사기 행위 경고
* 공지
* 전화 통화 설정

MFA 서버의 경우 다음 Azure Portal 페이지에 조직 데이터가 포함될 수 있습니다.

* 서버 설정
* 일회성 바이패스
* 캐싱 규칙
* 다단계 인증 서버 상태

## <a name="multifactor-authentication-activity-reports-for-public-cloud"></a>퍼블릭 클라우드에 대한 다단계 인증 활동 보고서

다단계 인증 작업 보고서는 온-프레미스 구성 요소(NPS 확장, AD FS 어댑터 및 MFA 서버)에서 작업을 저장합니다. 다단계 인증 서비스 로그는 서비스를 작동하는 데 사용됩니다.
다음 섹션에서는 다양한 고객 지역의 각 구성 요소에 대한 특정 인증 방법에 대해 활동 보고서 및 서비스 로그가 저장되는 위치를 보여 줍니다. 표준 음성 통화는 다른 지역으로 장애 조치(failover)될 수 있습니다.

>[!NOTE]
>다단계 인증 작업 보고서에는 UPN(사용자 계정 이름)과 전체 전화번호와 같은 개인 데이터가 포함됩니다.

### <a name="nps-extension-and-ad-fs-adapter"></a>NPS 확장 및 AD FS 어댑터

| 인증 방법                                                             | 고객 지역                      | 활동 보고서 위치 | 서비스 로그 위치 |
|-----------------------------------------------------------------------------------|--------------------------------------|--------------------------|----------------------|
| OATH 소프트웨어 및 하드웨어 토큰                                                 | 오스트레일리아 및 뉴질랜드            | 오스트레일리아/뉴질랜드    | 지역 내 클라우드      |
| OATH 소프트웨어 및 하드웨어 토큰                                                 | 오스트레일리아 및 뉴질랜드 외부 | 미국            | 지역 내 클라우드      |
| OATH 소프트웨어 및 하드웨어 토큰을 제외한 사용자 지정 인사말 및 기타 모든 인증 방법 없이 음성 통화  | 모두                               | 미국            | 지역 내 클라우드      |
| 사용자 지정 인사말을 사용하여 음성 통화                                         | 모두                                  | 미국            | 미국의 MFA 백 엔드 |

### <a name="mfa-server-and-cloud-based-mfa"></a>MFA 서버 및 클라우드 기반 MFA

| 구성 요소  | 인증 방법                          | 고객 지역                      | 활동 보고서 위치        | 서비스 로그 위치         |
|------------|------------------------------------------------|--------------------------------------|---------------------------------|------------------------------|
| MFA 서버 | 모든 방법                                    | 모두                                  | 미국                   | 미국의 MFA 백 엔드 |
| 클라우드 MFA  | 표준 음성 통화 및 기타 모든 방법     | 모두                                  | 지역 내 Azure AD 로그인 로그 | 지역 내 클라우드              |
| 클라우드 MFA  | 사용자 지정 인사말을 사용하여 음성 통화              | 모두                                  | 지역 내 Azure AD 로그인 로그 | 미국의 MFA 백 엔드 |

## <a name="multifactor-authentication-activity-reports-for-sovereign-clouds"></a>소버린 클라우드에 대한 다단계 인증 활동 보고서

다음 테이블에서는 소버린 클라우드의 서비스 로그 위치를 보여 줍니다.

| 소버린 클라우드                      | 로그인 로그                         | 다단계 인증 작업 보고서 | 다단계 인증 서비스 로그 |
|--------------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------|
| Microsoft Azure Germany              | 독일                              | 미국                              | 미국                           |
| Azure 중국 21Vianet                 | 중국                                | 미국                              | 미국                           |
| Microsoft Government Cloud           | 미국                        | 미국                              | 미국                           |

## <a name="next-steps"></a>다음 단계

클라우드 기반 Azure AD 다단계 인증 및 MFA 서버에서 수집하는 사용자 정보에 관한 자세한 내용은 [Azure AD 다단계 인증 사용자 데이터 수집](howto-mfa-reporting-datacollection.md)을 참조하세요.
