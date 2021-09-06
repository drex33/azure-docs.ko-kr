---
title: Azure AD Connect - Azure AD Connect를 사용하여 Azure AD로 AD FS 신뢰 관리 | Microsoft Docs
description: Azure AD Connect의 Azure AD 신뢰 처리 운영에 대한 세부 정보.
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8fea0133b7aa93b16a7e65e9e573723e7e21bae
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112460291"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Azure AD Connect를 사용하여 Azure AD로 AD FS 신뢰 관리

## <a name="overview"></a>개요

온-프레미스 환경을 Azure AD와 페더레이션하는 경우 온-프레미스 ID 공급자와 Azure AD 간에 트러스트 관계를 설정합니다.  Azure AD Connect는 온-프레미스 Active Directory Federation Service(AD FS)와 Azure AD 사이의 페더레이션을 관리할 수 있습니다. 이 문서는 다음에 대한 개요를 제공합니다.

* Azure AD Connect에서 신뢰에 구성하는 다양한 설정
* Azure AD Connect에서 설정하는 발급 변환 규칙(클레임 규칙)
* 업그레이드와 구성 업데이트 간에 클레임 규칙을 백업하고 복원하는 방법. 
* Azure AD를 통해 AD FS 트러스트를 보호하고 모니터링하는 모범 사례

## <a name="settings-controlled-by-azure-ad-connect"></a>Azure AD Connect에서 제어하는 설정

Azure AD Connect는 Azure AD 신뢰와 관련된 설정 **만** 관리합니다. Azure AD Connect는 AD FS의 다른 신뢰 당사자 신뢰에 대한 설정을 수정하지 않습니다. 다음 표에는 Azure AD Connect에서 제어하는 설정이 나와 있습니다.

| 설정 | Description |
| :--- | :--- |
| 토큰 서명 인증서 | Azure AD Connect를 사용하여 Azure AD와의 신뢰를 다시 설정하고 다시 만들 수 있습니다. Azure AD Connect는 AD FS의 토큰 서명 인증서에 대한 일회성 즉시 롤오버를 수행하고 Azure AD 도메인 페더레이션 설정을 업데이트합니다.|
| 토큰 서명 알고리즘 | 토큰 서명 알고리즘으로 SHA-256을 사용하는 것이 좋습니다. Azure AD Connect는 토큰 서명 알고리즘이 SHA-256보다 덜 안전한 값으로 설정되었는지 감지할 수 있습니다. 다음에 가능한 구성 작업에서 설정이 SHA-256으로 업데이트됩니다. 다른 신뢰 당사자 트러스트는 새로운 토큰 서명 인증서를 사용하도록 업데이트해야 합니다. |
| Azure AD 신뢰 식별자 | Azure AD Connect는 Azure AD 트러스트의 올바른 식별자 값을 설정합니다. AD FS는 식별자 값을 사용하여 Azure AD 신뢰를 고유하게 식별합니다. |
| Azure AD 엔드포인트 | Azure AD Connect는 Azure AD 신뢰에 구성된 엔드포인트가 항상 복원력 및 성능에 권장되는 최신 값을 따르도록 합니다. |
| 발급 변환 규칙 | 페더레이션된 설정에서 Azure AD의 기능이 최적의 성능을 발휘하는 데 필요한 여러 가지 클레임 규칙이 있습니다. Azure AD Connect는 Azure AD 트러스트가 항상 올바른 권장 클레임 규칙 집합으로 구성되도록 합니다. |
| 대체 ID | 동기화가 대체 ID를 사용하도록 구성된 경우, Azure AD Connect는 대체 ID를 사용하여 인증을 수행하도록 AD FS를 구성합니다. |
| 자동 메타데이터 업데이트 | 자동 메타 데이터 업데이트를 위해 Azure AD와 신뢰가 구성됩니다. AD FS는 Azure AD 신뢰의 메타데이터를 정기적으로 확인하고 Azure AD 쪽에서 변경된 경우 최신 상태로 유지합니다. |
| IWA(Windows 통합 인증) | Hybrid Azure AD 조인 작업 중에 하위 디바이스에 대한 Hybrid Azure AD 조인을 용이하게 하기 위해 IWA에 디바이스 등록이 활성화됩니다. |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Azure AD Connect에서 구성하는 실행 흐름 및 페더레이션 설정

Azure AD Connect는 구성 흐름 중에 Azure AD 신뢰에 대한 모든 설정을 업데이트하지 않습니다. 어떤 작업이나 실행 흐름이 진행 중인지에 따라 다른 설정이 수정됩니다. 다음 표에는 다양한 실행 흐름에서 영향을 받는 설정이 나와 있습니다.

| 실행 흐름 | 영향을 받는 설정 |
| :--- | :--- |
| 우선 설치(고속) | 없음 |
| 우선 설치(새로운 AD FS 팜) | 새 AD FS 팜이 생성되고 Azure AD와의 신뢰가 처음부터 새로 구축됩니다. |
| 우선 설치(기존 AD FS 팜, 기존 Azure AD 신뢰) | Azure AD 신뢰 식별자, 발급 변환 규칙, Azure AD 엔드포인트, 대체 ID(필요한 경우), 자동 메타데이터 업데이트 |
| Azure AD 신뢰 재설정 | 토큰 서명 인증서, 토큰 서명 알고리즘, Azure AD 신뢰 식별자, 발급 변환 규칙, Azure AD 엔드포인트, 대체 ID(필요한 경우), 자동 메타데이터 업데이트 |
| 페더레이션 서버 추가 | 없음 |
| WAP 서버 추가 | 없음 |
| 디바이스 옵션 | 발급 변환 규칙, 디바이스 등록을 위한 IWA |
| 페더레이션된 도메인 추가 | 도메인이 처음으로 추가되는 경우, 즉 단일 도메인 페더레이션에서 다중 도메인 페더레이션으로 설치가 변경되는 경우, Azure AD Connect는 신뢰를 처음부터 다시 만듭니다. Azure AD와의 신뢰가 이미 여러 도메인에 구성된 경우, 발급 변환 규칙만 수정됩니다. |
| TLS 업데이트 | 없음 |

Azure AD Connect는 설정이 수정되는 모든 작업 도중에 **%ProgramData%\AADConnect\ADFS** 에 현재 신뢰 설정의 백업을 만듭니다.

![기존 Azure AD 신뢰 백업에 대한 메시지를 보여주는 Azure AD Connect 페이지](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> 1.1.873.0 버전 이전에는 백업이 발급 변환 규칙으로만 구성되었고, 마법사 추적 로그 파일에 백업되었습니다.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Azure AD Connect에서 설정하는 발급 변환 규칙

Azure AD Connect는 Azure AD 트러스트가 항상 올바른 권장 클레임 규칙 집합으로 구성되도록 합니다. Microsoft에서는 Azure AD 신뢰를 관리하는 데 Azure AD Connect를 사용할 것을 권장합니다. 이 섹션에는 발급 변환 규칙 집합과 그에 대한 설명이 나와 있습니다.

| 규칙 이름 | 설명 |
| --- | --- |
| UPN 발급 | 이 규칙은 userprincipalname의 동기화 설정에 구성된 특성에서 userprincipalname 값을 쿼리합니다.|
| 사용자 지정 ImmutableId 클레임의 objectguid 및 msdsconsistencyguid 쿼리 | 이 규칙은 objectguid 및 msdsconsistencyguid 값의 파이프라인에 임시 값을 추가합니다(있는 경우). |
| msdsconsistencyguid의 존재 여부 확인 | msdsconsistencyguid 값의 유무에 따라 ImmutableId로 사용할 항목을 가리키는 임시 플래그를 설정합니다. |
| 있는 경우 변경이 불가능한 ID로 msdsconsistencyguid 발급 | 있는 경우 ImmutableId로 msdsconsistencyguid를 발급합니다. |
| msdsConsistencyGuid 규칙이 없으면 objectGuidRule 발급 | msdsconsistencyguid 값이 없는 경우 objectguid 값이 ImmutableId로 발급됩니다. |
| nameidentifier 발급 | 이 규칙은 nameidentifier 클레임의 값을 발급합니다.|
| 도메인에 가입된 컴퓨터의 accounttype 발급 | 인증되는 엔터티가 도메인에 가입된 디바이스인 경우 이 규칙은 계정 유형을 도메인에 가입된 디바이스를 의미하는 DJ로 발급합니다. |
| 컴퓨터 계정이 아닐 경우 USER 값을 사용하여 AccountType 발급 | 인증되는 엔터티가 사용자일 경우, 이 규칙은 계정 유형을 User로 발급합니다. |
| 컴퓨터 계정이 아닐 경우 issuerid 발급 | 이 규칙은 인증 엔터티가 디바이스가 아닐 경우 issuerId 값을 발급합니다. 값은 Azure AD Connect에서 구성한 정규식을 통해 생성됩니다. 이 정규식은 Azure AD Connect를 사용하여 페더레이션된 모든 도메인을 고려한 후 생성됩니다. |
| DJ 컴퓨터 인증에 대한 issuerid 발급 | 이 규칙은 인증 엔터티가 디바이스일 경우 issuerId 값을 발급합니다. |
| 도메인에 가입된 컴퓨터의 onpremobjectguid 발급 | 인증되는 엔터티가 도메인에 가입된 디바이스인 경우 이 규칙은 디바이스의 온-프레미스 objectguid를 발급합니다. |
| 주 SID를 통해 전달 | 이 규칙은 인증 엔터티의 주 SID를 발급합니다. |
| 클레임을 통해 전달 - insideCorporateNetwork | 이 규칙은 인증이 내부 회사 네트워크에서 이루어지는지, 외부에서 이루어지는지 Azure AD가 알 수 있게 도와주는 클레임을 발급합니다. |
| 클레임을 통해 전달 – Psso |   |
| 암호 만료 클레임 발급 | 이 규칙은 암호 만료 시간, 인증되는 엔터티의 암호가 만료되기까지 남은 일수, 암호 변경 경로로 사용되는 URL에 대한 세 가지 클레임을 발급합니다.|
| 클레임을 통해 전달 – authnmethodsreferences | 이 규칙에 따라 발급되는 클레임의 값은 엔터티에 대해 수행된 인증의 유형을 나타냅니다. |
| 클레임을 통해 전달 - multifactorauthenticationinstant | 이 클레임의 값은 사용자가 다중 인증을 마지막으로 수행한 시간을 UTC로 나타냅니다. |
| 클레임을 통해 전달 - AlternateLoginID | 이 규칙은 대체 로그인 ID를 사용하여 인증이 수행된 경우 AlternateLoginID 클레임을 발급합니다. |

> [!NOTE]
> Azure AD Connect 구성 중에 기본값이 아닌 옵션을 사용할 경우 UPN 발급 및 ImmutableId에 대한 클레임 규칙이 다릅니다.

## <a name="restore-issuance-transform-rules"></a>발급 변환 규칙 복원

Azure AD Connect 버전 1.1.873.0 이상은 Azure AD 신뢰 설정에 대한 업데이트가 이루어질 때마다 Azure AD 신뢰 설정의 백업을 만듭니다. Azure AD 신뢰 설정은 **%ProgramData%\AADConnect\ADFS** 에 백업됩니다. 파일 이름은 AadTrust-&lt;date&gt;-&lt;time&gt;.txt와 같은 형식입니다(예 - AadTrust-20180710-150216.txt).

![Azure AD 신뢰의 백업 예제 스크린샷](./media/how-to-connect-azure-ad-trust/backup.png)

아래에 제시된 단계를 따라 발급 변환 규칙을 복원할 수 있습니다.

1. 서버 관리자에서 AD FS 관리 UI 열기
2. **AD FS &gt; 신뢰 당사자 신뢰 &gt; Microsoft Office 365 ID 플랫폼 &gt; 클레임 편집 발급 정책** 으로 이동하여 Azure AD 신뢰 속성을 엽니다.
3. **규칙 추가** 를 클릭합니다.
4. 클레임 규칙 템플릿에서 사용자 지정 규칙을 사용하여 클레임 보내기를 선택하고 **다음** 을 클릭합니다.
5. 백업 파일에서 클레임 규칙의 이름을 복사하고 **클레임 규칙 이름** 필드에 붙여넣습니다.
6. 백업 파일의 클레임 규칙을 **사용자 지정 규칙** 텍스트 필드에 복사하고 **마침** 을 클릭합니다.

> [!NOTE]
> Azure AD Connect에서 구성한 규칙과 추가 규칙이 충돌하지 않도록 합니다.

## <a name="best-practice-for-securing-and-monitoring-the-ad-fs-trust-with-azure-ad"></a>Azure AD를 통해 AD FS 트러스트를 보호하고 모니터링하는 모범 사례
AD FS를 Azure AD와 페더레이션하는 경우 페더레이션 구성(AD FS와 Azure AD 간에 구성된 트러스트 관계)을 긴밀하게 모니터링하고 비정상적이거나 의심스러운 활동을 캡처하는 것이 중요합니다. 이렇게 하려면 페더레이션 구성이 변경될 때마다 경고를 설정하고 알림을 받는 것이 좋습니다. 경고를 설정하는 방법을 알아보려면 [페더레이션 구성에 대한 변경 내용 모니터링](how-to-connect-monitor-federation-changes.md)을 참조하세요. 



## <a name="next-steps"></a>다음 단계
* [Azure AD Connect를 사용하여 Active Directory Federation Services 관리 및 사용자 지정](how-to-connect-fed-management.md)
