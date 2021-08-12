---
title: Azure AD Connect Health 버전 내역
description: 이 문서는 Azure AD Connect Health에 대한 릴리스 및 해당 릴리스에서 포함된 항목을 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44ef48fe75d7070d3f471b06d548c06dc5d972b6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479790"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: 버전 릴리스 내역
Azure Active Directory 팀은 새로운 기능과 성능으로 Azure AD Connect Health를 정기적으로 업데이트합니다. 이 문서는 출시된 버전 및 기능을 나열합니다.  

> [!NOTE]
> 새 버전이 출시되면 Connect Health 에이전트가 자동으로 업데이트됩니다. Azure Portal에서 자동 업그레이드 설정을 사용하도록 설정했는지 확인하세요.
>

동기화용 Azure AD Connect Health는 Azure AD Connect 설치와 통합됩니다. [Azure AD Connect 릴리스 기록](./reference-connect-version-history.md)에 대해 자세히 알아보고 기능 피드백의 경우 [Connect Health 사용자 의견 채널](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)에서 투표하세요.

## <a name="may-2021"></a>2021년 5월
**에이전트 업데이트**
- AD FS용 Azure AD Connect Health 에이전트(버전 3.1.99.0)
  - AD FS 애플리케이션 활동 보고서에서 낮은 고유 사용자 수의 값에 대한 수정 사항입니다.
  - 비어 있거나 기본 GUID CorrelationId를 가진 로그인에 대한 수정 사항입니다.

## <a name="march-2021"></a>2021년 3월
**에이전트 업데이트**

- AD FS용 Azure AD Connect Health Agent(버전 3.1.95.0)

  - 로그인 이벤트 중에는 NT4 형식의 사용자 이름을 UPN으로 확인하기 위한 수정 사항입니다.
  - 전용 오류 코드가 있는 잘못된 애플리케이션 식별자 시나리오를 식별하기 위한 수정 사항입니다.
  - OAuth 클라이언트 식별자에 대한 새 속성을 추가하기 위한 변경 사항입니다.
  - 특정 로그인 시나리오에 대한 Azure AD 로그인 보고서의 **프로토콜** 및 **인증 유형** 필드에 올바른 값을 표시하기 위한 수정 사항입니다.
  - 요청 순서에 따라 Azure AD 로그인 보고서의 IP 체인 필드에 IP 주소를 표시하기 위한 수정 사항입니다.
  - 로그인 중에 보조 인증이 요청되었는지 여부를 구분하기 위해 새 필드를 도입하기 위한 변경 사항입니다.
  - Azure AD 로그인 보고서에 표시할 AD FS 애플리케이션 식별자 속성에 대한 수정 사항입니다.

## <a name="april-2020"></a>2020년 4월
**에이전트 업데이트**

- AD FS용 Azure AD Connect Health Agent(버전 3.1.77.0)

   - 잘못 보고된 "AD FS 서비스에 대한 SPN(서비스 사용자 이름)이 잘못됨" 경고에 대한 버그 수정 내용입니다.


## <a name="july-2019"></a>2019년 7월
**에이전트 업데이트**
* AD FS용 Azure AD Connect Health Agent(버전 3.1.59.0) 
   1. TestWindowsTransport의 텍스트 변경
   2. AD FS RP 업로드에 대한 변경 내용
   
* AD FS용 Azure AD Connect Health Agent(버전 3.1.56.0) 
   1. CheckOffice365Endpoints 테스트에서 TestWindowsTransport 테스트 추가 및 WsTrust 엔드포인트 검사 제거
   2. OS 및 .NET 정보 기록
   3. RP 구성 메시지 업로드 크기를 1MB로 늘립니다.
   4. 버그 수정
   
* AD DS용 Azure AD Connect Health 에이전트(버전 3.1.56.0) 
   1. OS 및 .NET 정보 기록 
   2. 버그 수정

## <a name="may-2019"></a>2019년 5월
**에이전트 업데이트:** 
* AD FS용 Azure AD Connect Health Agent(버전 3.1.51.0) 
   1. 동일한 client-request-id를 공유하는 여러 로그인을 구분하기 위한 버그 수정 사항입니다.
   2. 언어 지역화 서버의 잘못된 사용자 이름/암호 오류를 구문 분석하기 위한 버그 수정 사항   

## <a name="april-2019"></a>2019년 4월
**에이전트 업데이트:** 
* AD FS용 Azure AD Connect Health Agent(버전 3.1.46.0) 
   1. ADFS에 대한 중복된 SPN 경고 프로세스 확인 문제 수정

## <a name="march-2019"></a>2019년 3월
**에이전트 업데이트:** 
* AD DS용 Azure AD Connect Health 에이전트(버전 3.1.41.0)  
   1. .NET 버전 컬렉션
   2. 특정 범주가 누락된 경우 성능 카운터 수집 기능 개선
   3. 여러 모니터링 에이전트 인스턴스의 생성을 방지하기 위한 버그 수정 사항

* AD FS용 Azure AD Connect Health Agent(버전 3.1.41.0) 
   1. ADFSToolBox를 사용하여 AD FS 테스트 스크립트 통합 및 업그레이드
   2. .NET 버전 컬렉션 구현
   3. 특정 범주가 누락된 경우 성능 카운터 수집 기능 개선
   4. 여러 모니터링 에이전트 인스턴스의 생성을 방지하기 위한 버그 수정 사항


## <a name="november-2018"></a>2018년 11월
**새 GA 기능:** 
* 동기화를 위한 Azure AD Connect Health - 포털에서 중복된 특성 동기화 오류 진단 및 수정

**에이전트 업데이트:** 
* AD DS용 Azure AD Connect Health 에이전트(버전 3.1.24.0) 
   1. TLS(전송 계층 보안) 프로토콜 버전 1.2 규정 준수 및 적용
   2. 글로벌 카탈로그 경고 노이즈 감소
   3. 상태 에이전트 등록 버그 수정

* AD FS용 Azure AD Connect Health Agent(버전 3.1.24.0)  
   1. TLS(전송 계층 보안) 프로토콜 버전 1.2 규정 준수 및 적용
   2. 지역화된 운영 체제에 대한 테스트 ADFSRequestToken 지원
   3. 진단 에이전트 EventHandler 잠금 문제 해결
   4. 상태 에이전트 등록 버그 수정

## <a name="august-2018"></a>2018년 8월 
*  Azure AD Connect 버전 1.1.880.0과 함께 출시된 동기화용 Azure AD Connect Health Agent(버전 3.1.7.0)    
   1. [.NET Framework 기술 자료 릴리스를 사용한 모니터링 에이전트에서 나타나는 높은 CPU 문제](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)에 대한 핫픽스

## <a name="june-2018"></a>2018년 6월 
**새로운 미리 보기 기능:** 
* 동기화를 위한 Azure AD Connect Health - 포털에서 중복된 특성 동기화 오류 진단 및 수정 

**에이전트 업데이트:** 
* AD DS용 Azure AD Connect Health 에이전트(버전 3.1.7.0)    
  1. [.NET Framework 기술 자료 릴리스를 사용한 모니터링 에이전트에서 나타나는 높은 CPU 문제](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)에 대한 핫픽스
   
* AD FS용 Azure AD Connect Health Agent(버전 3.1.7.0)  
  1. [.NET Framework 기술 자료 릴리스를 사용한 모니터링 에이전트에서 나타나는 높은 CPU 문제](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)에 대한 핫픽스
  2. ADFS Server 2016 보조 서버에서 수정 결과 테스트
   
* AD FS용 Azure AD Connect Health Agent(버전 3.1.2.0)  
  1. 버전 3.0.244.0용으로 특별히 작성된 에이전트 메모리 관리 및 관련 경고에 대한 핫픽스


## <a name="may-2018"></a>2018년 5월
**에이전트 업데이트:**
* AD DS용 Azure AD Connect Health 에이전트(버전 3.0.244.0)
  1. 에이전트 개인 정보 보호 향상  
  2. 버그 수정 및 일반 개선 사항

* AD FS용 Azure AD Connect Health Agent(버전 3.0.244.0)
  1. 에이전트 진단 서비스 및 관련 PowerShell 모듈 향상
  2. 에이전트 개인 정보 보호 향상  
  3. 버그 수정 및 일반 개선 사항

* Azure AD Connect 버전 1.1.819.0과 함께 출시된 동기화용 Azure AD Connect Health Agent(버전 3.0.164.0) 
  1. 에이전트 개인 정보 보호 향상  
  2. 버그 수정 및 일반 개선 사항


## <a name="march-2018"></a>2018년 3월
**새로운 미리 보기 기능:**
* AD FS용 Azure AD Connect Health - 위험한 IP 보고서 및 경고

**에이전트 업데이트:**

* AD DS에 대한 Azure AD Connect Health Agent(버전 3.0.176.0)
  1. 에이전트 가용성 개선 사항 
  2. 버그 수정 및 일반 개선 사항
* AD FS에 대한 Azure AD Connect Health Agent(버전 3.0.176.0)
  1. 에이전트 가용성 개선 사항 
  2. 버그 수정 및 일반 개선 사항
* Azure AD Connect 버전 1.1.750.0과 함께 출시된 동기화용 Azure AD Connect Health Agent(버전 3.0.129.0)  
  1. 에이전트 가용성 개선 사항 
  2. 버그 수정 및 일반 개선 사항

## <a name="december-2017"></a>2017년 12월
**에이전트 업데이트:**

* AD DS에 대한 Azure AD Connect Health Agent(버전 3.0.145.0)
  1. 에이전트 가용성 개선 사항 
  2. 새 에이전트 문제 해결 명령 추가
  3. 버그 수정 및 일반 개선 사항
* AD FS에 대한 Azure AD Connect Health Agent(버전 3.0.145.0)
  1. 새 에이전트 문제 해결 명령 추가
  2. 에이전트 가용성 개선 사항 
  3. 버그 수정 및 일반 개선 사항
  
## <a name="october-2017"></a>2017년 10월
**에이전트 업데이트:**

 * Azure AD Connect 버전 1.1.649.0과 함께 출시된 동기화용 Azure AD Connect Health Agent(버전 3.0.129.0)
<br></br> Azure AD Connect 및 동기화용 Azure AD Connect Health Agent 간의 버전 호환성 문제가 해결되었습니다. 이 문제는 Azure AD Connect를 버전 1.1.647.0으로 현재 위치 업그레이드를 수행하려고 하며 현재 상태 에이전트 버전 3.0.127.0이 있는 고객에게 영향을 미칩니다. 업그레이드 후에 상태 에이전트는 더 이상 Azure AD Connect 동기화 서비스에 대한 상태 데이터를 Azure AD 상태 서비스로 전송할 수 없습니다. 이 수정 프로그램을 적용하면 상태 에이전트 버전 3.0.129.0이 Azure AD Connect의 현재 위치 업그레이드 동안 설치됩니다. 상태 에이전트 3.0.129.0은 Azure AD Connect 버전 1.1.649.0과 호환성 문제가 없습니다.

## <a name="july-2017"></a>2017년 7월
**에이전트 업데이트:**

* AD DS에 대한 Azure AD Connect Health Agent(버전 3.0.68.0)
  1. 버그 수정 및 일반 개선 사항
  2. 전체 클라우드 지원
* AD FS에 대한 Azure AD Connect Health Agent(버전 3.0.68.0)
  1. 버그 수정 및 일반 개선 사항
  2. 전체 클라우드 지원
* Azure AD Connect 버전 1.1.614.0과 함께 출시된 동기화용 Azure AD Connect Health Agent(버전 3.0.68.0)
  1. Microsoft Azure Government 클라우드 및 Microsoft 클라우드 독일에 대한 지원

## <a name="april-2017"></a>2017년 4월      
**에이전트 업데이트:**

* AD FS에 대한 Azure AD Connect Health Agent(버전 3.0.12.0)
  1. 버그 수정 및 일반 개선 사항
* AD DS에 대한 Azure AD Connect Health Agent(버전 3.0.12.0)
  1. 성능 카운터 업로드 개선 사항
  2. 버그 수정 및 일반 개선 사항

## <a name="october-2016"></a>2016년 10월
**에이전트 업데이트:**

* AD FS용 Azure AD Connect Health Agent(버전 2.6.408.0)
* 인증 요청에서 클라이언트 IP 주소를 검색하는 향상된 기능
* 경고와 관련된 버그 수정
* AD FS에 대한 Azure AD Connect Health Agent(버전 2.6.408.0)
* 경고와 관련된 버그 수정
* Azure AD Connect 버전 1.1.281.0와 함께 출시된 Azure AD Connect Health Agent(동기화 버전 2.6.353.0)
* 동기화 오류 보고서에 대한 필요한 데이터 제공
* 경고와 관련된 버그 수정

**새로운 미리 보기 기능:**

* Azure AD Connect에 대한 동기화 오류 보고서

**새로운 기능:**

* AD FS용 Azure AD Connect Health의 IP 주소 필드는 잘못된 사용자 이름/암호를 사용하는 상위 50 사용자에 대한 보고서에 표시됩니다.

## <a name="july-2016"></a>2016년 7월
**새로운 미리 보기 기능:**

* [AD DS용 Azure AD Connect Health](how-to-connect-health-adds.md)

## <a name="january-2016"></a>2016년 1월
**에이전트 업데이트:**

* AD FS에 대한 Azure AD Connect Health 에이전트(version 2.6.91.1512)

**새로운 기능:**

* [Azure AD Connect Health 에이전트에 대한 테스트 연결 도구](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015년 11월
**새로운 기능:**

* [Azure RBAC(Azure 역할 기반 액세스 제어)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) 지원

**새로운 미리 보기 기능:**

* [동기화를 위한 Azure AD Connect Health](how-to-connect-health-sync.md).

**수정된 문제:**

* 에이전트를 등록하는 동안 표시된 오류에 대한 버그 수정.

## <a name="september-2015"></a>2015년 9월
**새로운 기능:**

* AD FS에 대한 잘못된 사용자 이름 암호 보고서
* 인증되지 않은 HTTP 프록시를 구성하는 지원
* Server 코어에서 에이전트를 구성하는 지원
* AD FS에 대한 경고의 향상된 기능
* 연결 및 데이터 업로드를 위한 AD FS에 대한 Azure AD Connect Health 에이전트의 향상된 기능.

**수정된 문제:**

* AD FS 오류 형식에 대한 사용 현황 정보에서 버그 수정.

## <a name="june-2015"></a>2015년 6월
**AD FS 및 AD FS 프록시에 대한 Azure AD Connect Health의 최초 릴리스.**

**새로운 기능:**

* 전자 메일 알림으로 AD FS 및 AD FS 프록시 서버의 모니터링에 대한 경고.
* AD FS 토폴로지 및 AD FS 성능 카운터의 패턴에 쉽게 액세스합니다.
* 애플리케이션, 인증 방법, 요청 네트워크 위치 등으로 그룹화된 AD FS 서버에서 토큰 요청이 성공하는 추세입니다.
* 애플리케이션, 오류 형식 등으로 그룹화된 AD FS 서버에서 요청이 실패하는 추세입니다.
* Azure AD 전역 관리자 자격 증명을 사용하여 간단한 에이전트 배포.  

## <a name="next-steps"></a>다음 단계
[온-프레미스 ID 인프라 및 클라우드 동기화 서비스 모니터링](./whatis-azure-ad-connect.md)에 대해 자세히 알아봅니다.
