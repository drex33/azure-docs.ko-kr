---
title: Azure AD 기본 제공 역할 - Azure Active Directory
description: Azure Active Directory 기본 제공 역할 및 권한을 설명합니다.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/15/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: generated, it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 768814932b54dab3a8d761d5502310b6b7315f2d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245465"
---
# <a name="azure-ad-built-in-roles"></a>Azure AD 기본 제공 역할

Azure AD(Azure Active Directory)에서 다른 관리자 또는 관리자가 아닌 사람이 Azure AD 리소스를 관리해야 하는 경우 필요한 권한을 제공하는 Azure AD 역할을 그 사람에게 할당합니다. 예를 들어 사용자를 추가 또는 변경하고, 사용자 암호를 다시 설정하고, 사용자 라이선스를 관리하고, 도메인 이름을 관리할 수 있도록 허용하는 역할을 할당할 수 있습니다.

이 문서에는 Azure AD 리소스 관리를 허용할 사람에게 할당할 수 있는 Azure AD 기본 제공 역할이 나열되어 있습니다. 역할을 할당하는 방법에 대한 자세한 내용은 [사용자에게 Azure AD 역할 할당](manage-roles-portal.md)을 참조하세요.

## <a name="all-roles"></a>모든 역할

> [!div class="mx-tableFixed"]
> | 역할 | 설명 | 템플릿 ID |
> | --- | --- | --- |
> | [애플리케이션 관리자](#application-administrator) | 앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [애플리케이션 개발자](#application-developer) | '사용자가 애플리케이션을 등록할 수 있음' 설정에 관계없이 애플리케이션 등록을 만들 수 있습니다. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [공격 페이로드 작성자](#attack-payload-author) | 관리자가 나중에 시작할 수 있는 공격 페이로드를 만들 수 있습니다. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [공격 시뮬레이션 관리자](#attack-simulation-administrator) | 공격 시뮬레이션 캠페인의 모든 측면을 만들고 관리할 수 있습니다. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [인증 관리자](#authentication-administrator) | 관리 사용자가 아닌 사용자의 인증 방법 정보를 보고, 설정하고, 재설정하기 위해 액세스할 수 있습니다. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [인증 정책 관리자](#authentication-policy-administrator) | 인증 방법 정책, 테넌트 전체 MFA 설정, 암호 보호 정책 및 확인 가능한 자격 증명을 만들고 관리할 수 있습니다. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Azure AD 조인 디바이스 로컬 관리자](#azure-ad-joined-device-local-administrator) | 이 역할에 할당된 사용자는 Azure AD에 조인된 디바이스의 로컬 관리자 그룹에 추가됩니다. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Azure DevOps 관리자](#azure-devops-administrator) | Azure DevOps 조직 정책 및 설정을 관리할 수 있습니다. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Azure Information Protection 관리자](#azure-information-protection-administrator) | Azure Information Protection 제품의 모든 측면을 관리할 수 있습니다. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF 키 세트 관리자](#b2c-ief-keyset-administrator) | IEF(Identity Experience Framework)에서 페더레이션 및 암호화의 비밀을 관리할 수 있습니다. | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [B2C IEF 정책 관리자](#b2c-ief-policy-administrator) | IEF(Identity Experience Framework)에서 보안 프레임워크 정책을 만들고 관리할 수 있습니다. | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [대금 청구 관리자](#billing-administrator) | 결제 정보 업데이트와 같은 일반 결제 관련 작업을 수행할 수 있습니다. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Cloud App Security 관리자](#cloud-app-security-administrator) | Cloud App Security 제품의 모든 측면을 관리할 수 있습니다. | 892c5842-a9a6-463a-8041-72aa08ca3cf6 |
> | [클라우드 애플리케이션 관리자](#cloud-application-administrator) | 앱 프록시를 제외한 앱 등록 및 엔터프라이즈 앱의 모든 측면을 만들고 관리할 수 있습니다. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [클라우드 디바이스 관리자](#cloud-device-administrator) | Azure AD에서 액세스 권한이 디바이스 관리로 제한됩니다. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [규정 준수 관리자](#compliance-administrator) | Azure AD 및 Microsoft 365에서 준수 구성 및 보고서를 읽고 관리할 수 있습니다. | 17315797-102d-40b4-93e0-432062caca18 |
> | [규정 준수 데이터 관리자](#compliance-data-administrator) | 규정 준수 콘텐츠를 만들고 관리합니다. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [조건부 액세스 관리자](#conditional-access-administrator) | 조건부 액세스 기능을 관리할 수 있습니다. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [고객 Lockbox 액세스 승인자](#customer-lockbox-access-approver) | 고객 조직 데이터에 액세스하려는 Microsoft 지원 요청을 승인할 수 있습니다. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Desktop Analytics 관리자](#desktop-analytics-administrator) | 데스크톱 관리 도구 및 서비스에 액세스하고 관리할 수 있습니다. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [디렉터리 읽기 권한자](#directory-readers) | 기본 디렉터리 정보를 읽을 수 있습니다. 일반적으로 애플리케이션과 게스트에 대한 디렉터리 읽기 액세스 권한을 부여하는 데 사용됩니다. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [디렉터리 동기화 계정](#directory-synchronization-accounts) | Azure AD Connect에서만 사용됩니다. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [디렉터리 쓰기 권한자](#directory-writers) | 기본 디렉터리 정보를 읽고 쓸 수 있습니다. 애플리케이션에 대한 액세스 권한은 사용자를 위한 것이 아닙니다. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [도메인 이름 관리자](#domain-name-administrator) | 클라우드 및 온-프레미스에서 도메인 이름을 관리할 수 있습니다. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Dynamics 365 관리자](#dynamics-365-administrator) | Dynamics 365 제품의 모든 측면을 관리할 수 있습니다. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Edge 관리자](#edge-administrator) | Microsoft Edge의 모든 측면을 관리합니다. | 3f1acade-1e04-4fbc-9b69-f0302cd84aef |
> | [Exchange 관리자](#exchange-administrator) | Exchange 제품의 모든 측면을 관리할 수 있습니다. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Exchange 받는 사람 관리자](#exchange-recipient-administrator) | Exchange Online 조직 내에서 Exchange Online 수신자를 만들거나 업데이트할 수 있습니다. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [외부 ID 사용자 흐름 관리자](#external-id-user-flow-administrator) | 사용자 흐름의 모든 측면을 만들고 관리할 수 있습니다. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [외부 ID 사용자 흐름 특성 관리자](#external-id-user-flow-attribute-administrator) | 모든 사용자 흐름에 사용할 수 있는 특성 스키마를 만들고 관리할 수 있습니다. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [외부 ID 공급자 관리자](#external-identity-provider-administrator) | 직접 페더레이션에 사용할 ID 공급자를 구성할 수 있습니다. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | 전역 관리자 | Azure AD 및 Azure AD ID를 사용하는 Microsoft 서비스의 모든 측면을 관리할 수 있습니다. | 62e90394-69f5-4237-9190-012177145e10 |
> | [글로벌 읽기 권한자](#global-reader) | 전역 관리자가 할 수 있는 모든 것을 읽을 수는 있지만, 업데이트할 수는 없습니다. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [그룹 관리자](#groups-administrator) | 이 역할의 구성원은 그룹을 만들고/관리하고, 이름이나 만료 정책과 같은 그룹 설정을 만들고/관리하고, 그룹 작업 및 감사 보고서를 볼 수 있습니다. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [게스트 초대자](#guest-inviter) | '멤버가 게스트를 초대할 수 있음' 설정에 관계없이 게스트 사용자를 초대할 수 있습니다. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [기술 지원팀 관리자](#helpdesk-administrator) | 관리자가 아닌 사용자 및 기술 지원팀 관리자의 암호를 재설정할 수 있습니다. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [하이브리드 ID 관리자](#hybrid-identity-administrator) | AD에서 Azure AD로 클라우드 프로비저닝, Azure AD Connect 및 페더레이션 설정을 관리할 수 있습니다. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Identity Governance 관리자](#identity-governance-administrator) | ID 거버넌스 시나리오에 Azure AD를 사용하여 액세스를 관리합니다. | 45d8d3c5-c802-45c6-b32a-1d70b5e1e86e |
> | [Insights 관리자](#insights-administrator) | Microsoft 365 Insights 앱에서 관리 권한이 있습니다. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Insights 비즈니스 리더](#insights-business-leader) | Microsoft 365 Insights 앱을 통해 대시보드 및 인사이트를 보고 공유할 수 있습니다. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Intune 관리자](#intune-administrator) | Intune 제품의 모든 측면을 관리할 수 있습니다. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Kaizala 관리자](#kaizala-administrator) | Microsoft Kaizala의 설정을 관리할 수 있습니다. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [지식 관리자](#knowledge-administrator) | 지식, 학습 및 기타 인텔리전트 기능을 구성할 수 있습니다. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [지식 관리자](#knowledge-manager) | 항목과 지식을 구성, 생성, 관리 및 승격할 수 있습니다. | 744ec460-397e-42ad-a462-8b3f9747a02c |
> | [라이선스 관리자](#license-administrator) | 사용자 및 그룹의 제품 라이선스를 관리할 수 있습니다. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [메시지 센터 개인 정보 읽기 권한자](#message-center-privacy-reader) | Office 365 메시지 센터에서만 보안 메시지 및 업데이트를 읽을 수 있습니다. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [메시지 센터 읽기 권한자](#message-center-reader) | Office 365 메시지 센터에서만 조직의 메시지 및 업데이트를 읽을 수 있습니다. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [최신 Commerce 사용자](#modern-commerce-user) | 회사, 부서 또는 팀의 상업적 구매를 관리할 수 있습니다. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [네트워크 관리자](#network-administrator) | 네트워크 위치를 관리하고 Microsoft 365 Software as a Service 애플리케이션에 대한 엔터프라이즈 네트워크 디자인 인사이트를 검토할 수 있습니다. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Office 앱 관리자](#office-apps-administrator) | 정책 및 설정 관리를 포함하여 Office 앱의 클라우드 서비스를 관리할 수 있고, 최종 사용자의 디바이스에서 "새로운 기능" 기능 콘텐츠를 선택, 선택 취소 및 게시하는 기능을 관리할 수 있습니다. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [파트너 계층1 지원](#partner-tier1-support) | 사용하지 마세요. 일반적인 용도로는 적합하지 않습니다. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [파트너 계층2 지원](#partner-tier2-support) | 사용하지 마세요. 일반적인 용도로는 적합하지 않습니다. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [암호 관리자](#password-administrator) | 비관리자 및 암호 관리자의 암호를 다시 설정할 수 있습니다. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Power BI 관리자](#power-bi-administrator) | Power BI 제품의 모든 측면을 관리할 수 있습니다. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Power Platform 관리자](#power-platform-administrator) | Microsoft Dynamics 365, Power Apps, Power Automate의 모든 측면을 만들고 관리할 수 있습니다. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [프린터 관리자](#printer-administrator) | 프린터 및 프린터 커넥터의 모든 것을 관리할 수 있습니다. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [프린터 기술자](#printer-technician) | 프린터를 등록/등록 취소하고 프린터 상태를 업데이트할 수 있습니다. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [권한 있는 인증 관리자](#privileged-authentication-administrator) | 사용자(관리자 또는 비관리자)의 인증 방법 정보를 보고, 설정하고. 재설정하기 위해 액세스할 수 있습니다. | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [권한 있는 역할 관리자](#privileged-role-administrator) | Azure AD의 역할 할당 및 Privileged Identity Management의 모든 것을 관리할 수 있습니다. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [보고서 읽기 권한자](#reports-reader) | 로그인 및 감사 보고서를 읽을 수 있습니다. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Search 관리자](#search-administrator) | Microsoft Search 설정의 모든 것을 만들고 관리할 수 있습니다. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Search 편집자](#search-editor) | 책갈피, 질문과 대답, 위치, 평면도와 같은 편집 콘텐츠를 만들고 관리할 수 있습니다. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | 보안 관리자 | Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽고 구성을 관리할 수 있습니다. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [보안 운영자](#security-operator) | 보안 이벤트를 만들고 관리합니다. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | 보안 읽기 권한자 | Azure AD 및 Office 365에서 보안 정보 및 보고서를 읽을 수 있습니다. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [서비스 지원 관리자](#service-support-administrator) | 서비스 상태 정보를 읽고, 지원 티켓을 관리할 수 있습니다. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [SharePoint 관리자](#sharepoint-administrator) | SharePoint 서비스의 모든 측면을 관리할 수 있습니다. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [비즈니스용 Skype 관리자](#skype-for-business-administrator) | 비즈니스용 Skype 제품의 모든 측면을 관리할 수 있습니다. | 75941009-915a-4869-abe7-691bff18279e |
> | [Teams 관리자](#teams-administrator) | Microsoft Teams 서비스를 관리할 수 있습니다. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Teams 통신 관리자](#teams-communications-administrator) | Microsoft Teams 서비스 내에서 호출 및 회의 기능을 관리할 수 있습니다. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Teams 통신 지원 엔지니어](#teams-communications-support-engineer) | 고급 도구를 사용하여 Teams 내에서 통신 문제를 해결할 수 있습니다. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Teams 통신 지원 전문가](#teams-communications-support-specialist) | 기본 도구를 사용하여 Teams 내에서 통신 문제를 해결할 수 있습니다. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Teams 디바이스 관리자](#teams-devices-administrator) | Teams 인증 디바이스에서 관리 관련 작업을 수행할 수 있습니다. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [사용 요약 보고서 읽기 권한자](#usage-summary-reports-reader) | Microsoft 365 사용량 현황 분석 및 생산성 점수에서 테넌트 수준 집계만 볼 수 있습니다. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [사용자 관리자](#user-administrator) | 제한된 관리자의 암호 재설정을 비롯하여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다. | fe930be7-5e62-47db-91af-98c3a49a38b1 |
> | [Windows 365 관리자](#windows-365-administrator) | 클라우드 PC의 모든 측면을 프로비전하고 관리할 수 있습니다. | 11451d60-acb2-45eb-a7d6-43d0f0125c13 |
> | [Windows 업데이트 배포 관리자](#windows-update-deployment-administrator) | 비즈니스용 Windows 업데이트 배포 서비스를 통해 Windows 업데이트 배포의 모든 측면을 만들고 관리할 수 있습니다. | 32696413-001a-46ae-978c-ce0f6b3620d2 |

## <a name="application-administrator"></a>애플리케이션 관리자

이 역할의 사용자는 엔터프라이즈 애플리케이션, 애플리케이션 등록 및 애플리케이션 프록시 설정의 모든 측면을 만들고 관리할 수 있습니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

또한 이 역할은 Microsoft Graph 및 Azure AD Graph에 대한 애플리케이션 권한을 제외하고 위임된 권한 및 애플리케이션 권한에 동의하는 기능을 부여합니다.

> [!IMPORTANT]
> 이 예외는 _다른_ 앱(예: 타사 앱 또는 사용자가 등록한 앱)에 대한 애플리케이션 권한에는 동의할 수 있다는 뜻입니다. 여전히 앱을 등록할 때 이 권한을 _요청_ 할 수 있지만, 이 권한을 _부여_(즉, 동의)하려면 전역 관리자처럼 더 많은 권한이 있는 관리자여야 합니다.
>
>이 역할은 애플리케이션 자격 증명을 관리하는 기능을 부여합니다. 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 애플리케이션의 ID에 사용자 또는 다른 개체를 만들거나 업데이트하는 기능과 같이 리소스에 대한 액세스 권한이 부여된 경우 이 역할에 할당된 사용자는 애플리케이션을 가장하는 동안 이러한 작업을 수행할 수 있습니다. 애플리케이션의 ID를 가장하는 이 기능은 본인의 역할 할당을 통해 사용자가 수행할 수 있는 권한 상승이 될 수 있습니다. 애플리케이션 관리자 역할에 사용자를 할당하면 애플리케이션의 ID를 가장하는 기능이 해당 사용자에게 부여된다는 점을 이해해야 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/create | 모든 유형의 애플리케이션 만들기 |
> | microsoft.directory/applications/delete | 모든 유형의 애플리케이션 삭제 |
> | microsoft.directory/applications/applicationProxy/read | 모든 애플리케이션 프록시 속성 읽기 |
> | microsoft.directory/applications/applicationProxy/update | 모든 애플리케이션 프록시 속성 업데이트 |
> | microsoft.directory/applications/applicationProxyAuthentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/applicationProxySslCertificate/update | 애플리케이션 프록시에 대한 SSL 인증서 설정 업데이트 |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | 애플리케이션 프록시에 대한 URL 설정 업데이트 |
> | microsoft.directory/applications/appRoles/update | 모든 유형의 애플리케이션에서 appRoles 속성 업데이트 |
> | microsoft.directory/applications/audience/update | 애플리케이션의 대상 그룹 속성 업데이트 |
> | microsoft.directory/applications/authentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/basic/update | 애플리케이션의 기본 속성 업데이트 |
> | microsoft.directory/applications/credentials/update | 애플리케이션 자격 증명 업데이트 |
> | microsoft.directory/applications/extensionProperties/update | 애플리케이션의 확장 속성 업데이트 |
> | microsoft.directory/applications/notes/update | 애플리케이션의 메모 업데이트 |
> | microsoft.directory/applications/owners/update | 애플리케이션 소유자 업데이트 |
> | microsoft.directory/applications/permissions/update | 모든 유형의 애플리케이션에서 공개된 권한 및 필요한 권한 업데이트 |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/applications/tag/update | 애플리케이션의 태그 업데이트 |
> | microsoft.directory/applications/verification/update | applicationsverification 속성 업데이트 |
> | microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션 인스턴스화 |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/connectors/create | 애플리케이션 프록시 커넥터 만들기 |
> | microsoft.directory/connectors/allProperties/read | 애플리케이션 프록시 커넥터의 모든 속성 읽기 |
> | microsoft.directory/connectorGroups/create | 애플리케이션 프록시 커넥터 그룹 만들기 |
> | microsoft.directory/connectorGroups/delete | 애플리케이션 프록시 커넥터 그룹 삭제 |
> | microsoft.directory/connectorGroups/allProperties/read | 애플리케이션 프록시 커넥터 그룹의 모든 속성 읽기 |
> | microsoft.directory/connectorGroups/allProperties/update | 애플리케이션 프록시 커넥터 그룹의 모든 속성 업데이트 |
> | microsoft.directory/deletedItems.applications/delete | 더 이상 복원할 수 없는 애플리케이션 영구 삭제 |
> | microsoft.directory/deletedItems.applications/restore | 일시 삭제된 애플리케이션을 원래 상태로 복원 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/applicationPolicies/create | 애플리케이션 정책 만들기 |
> | microsoft.directory/applicationPolicies/delete | 애플리케이션 정책 삭제 |
> | microsoft.directory/applicationPolicies/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/applicationPolicies/owners/read | 애플리케이션 정책의 소유자 읽기 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 개체 목록에 적용된 애플리케이션 정책 읽기 |
> | microsoft.directory/applicationPolicies/basic/update | 애플리케이션 정책의 표준 속성 업데이트 |
> | microsoft.directory/applicationPolicies/owners/update | 애플리케이션 정책의 소유자 속성 업데이트 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/servicePrincipals/create | 서비스 주체 만들기 |
> | microsoft.directory/servicePrincipals/delete | 서비스 주체 삭제 |
> | microsoft.directory/servicePrincipals/disable | 서비스 주체를 사용하지 않도록 설정 |
> | microsoft.directory/servicePrincipals/enable | 서비스 주체를 사용하도록 설정 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 애플리케이션 프로비저닝 비밀 및 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 애플리케이션 프로비저닝 동기화 작업을 시작, 다시 시작 및 일시 중지 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 애플리케이션 프로비저닝 동기화 작업 및 스키마 만들기 및 관리 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 읽기 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Microsoft Graph 및 Azure AD Graph의 애플리케이션 권한을 제외하고 특정 사용자 또는 모든 사용자를 대신하여 애플리케이션 권한 및 위임된 권한에 대한 동의 부여 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/servicePrincipals/audience/update | 서비스 주체의 대상 그룹 속성 업데이트 |
> | microsoft.directory/servicePrincipals/authentication/update | 서비스 주체의 인증 속성 업데이트 |
> | microsoft.directory/servicePrincipals/basic/update | 서비스 주체의 기본 속성 업데이트 |
> | microsoft.directory/servicePrincipals/credentials/update | 서비스 주체의 자격 증명 업데이트 |
> | microsoft.directory/servicePrincipals/notes/update | 서비스 주체의 메모 업데이트 |
> | microsoft.directory/servicePrincipals/owners/update | 서비스 주체의 소유자 업데이트 |
> | microsoft.directory/servicePrincipals/permissions/update | 서비스 주체의 권한 업데이트 |
> | microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책 업데이트 |
> | microsoft.directory/servicePrincipals/tag/update | 서비스 주체의 태그 속성 업데이트 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="application-developer"></a>애플리케이션 개발자

이 역할의 사용자는 "사용자가 애플리케이션을 등록할 수 있음" 설정이 아니오로 설정된 경우 애플리케이션 등록을 만들 수 있습니다. 또한 이 역할은 “사용자가 앱이 사용자 대신 회사 데이터에 액세스하는 것에 동의할 수 있음” 설정이 [아니요]로 설정된 경우 대신 동의할 수 있는 권한을 부여합니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록을 만들 때 소유자로 추가됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | 모든 유형의 애플리케이션 만들기. 작성자는 첫 번째 소유자로 추가됨 |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | OAuth 2.0 권한 부여 만들기. 작성자는 첫 번째 소유자가 됨 |
> | microsoft.directory/servicePrincipals/createAsOwner | 서비스 주체 만들기. 작성자는 첫 번째 소유자가 됨 |

## <a name="attack-payload-author"></a>공격 페이로드 작성자

이 역할의 사용자는 공격 페이로드를 만들 수 있지만 실제로 시작하거나 예약할 수는 없습니다. 그러면 테넌트의 모든 관리자가 공격 페이로드를 사용하여 시뮬레이션을 만들 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 공격 시뮬레이터에서 공격 페이로드 만들기 및 관리 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 공격 시뮬레이션, 응답 및 관련 학습에 대한 보고서 읽기 |

## <a name="attack-simulation-administrator"></a>공격 시뮬레이션 관리자

이 역할의 사용자는 공격 시뮬레이션 만들기, 시뮬레이션 시작/예약, 시뮬레이션 결과 검토의 모든 측면을 만들고 관리할 수 있습니다. 이 역할의 구성원은 테넌트의 모든 시뮬레이션에 액세스할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 공격 시뮬레이터에서 공격 페이로드 만들기 및 관리 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 공격 시뮬레이션, 응답 및 관련 학습에 대한 보고서 읽기 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 공격 시뮬레이터에서 공격 시뮬레이션 템플릿 만들기 및 관리 |

## <a name="authentication-administrator"></a>인증 관리자

이 역할이 부여된 사용자는 관리자가 아닌 사용자 및 일부 역할의 인증 방법(암호 포함)을 설정하거나 다시 설정할 수 있습니다. 인증 관리자는 관리자가 아닌 사용자나 일부 역할이 할당된 사용자가 기존의 비 암호 자격 증명(예: MFA 또는 FIDO)을 재등록하도록 요구할 수 있으며 다음에 로그인할 때 MFA를 요청하도록 **디바이스에 MFA 저장** 을 철회할 수도 있습니다. 인증 관리자가 인증 방법을 읽거나 업데이트할 수 있는 역할 목록은 [암호 재설정 권한](#password-reset-permissions)을 참조하세요.

[권한 있는 인증 관리자](#privileged-authentication-administrator) 역할에는 모든 사용자에게 재등록 및 다단계 인증을 강제 적용할 수 있는 권한이 있습니다.

[인증 정책 관리자](#authentication-policy-administrator) 역할에는 각 사용자가 등록하고 사용할 수 있는 방법을 결정하는 테넌트 인증 방법 정책을 설정할 수 있는 권한이 있습니다.

| 역할 | 사용자의 인증 방법 관리 | 사용자별 MFA 관리 | MFA 설정 관리 | 인증 방법 정책 관리 | 암호 보호 정책 관리 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 인증 관리자 | 일부 사용자의 경우 예(위 참조) | 일부 사용자의 경우 예(위 참조) | 아니요 | 아니요 | 아니요 |
| 권한 있는 인증 관리자| 모든 사용자에 대해 예 | 모든 사용자에 대해 예 | 아니요 | 아니요 | 아니요 |
| 인증 정책 관리자 | 아니요 |아니요 | 예 | 예 | 예 |

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 자격 증명을 변경할 수 있습니다. 사용자의 자격 증명을 변경한다는 것은 사용자의 ID 및 사용 권한을 가정할 수 있음을 의미할 수 있습니다. 다음은 그 예입니다.
>
>* 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 사용 권한이 부여되었을 수 있으며, 다른 위치에서는 인증 관리자에 권한이 부여되지 않습니다. 이 경로를 통해 인증 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>* Azure 구독 소유자: Azure에서 중요한 프라이빗 정보 또는 중요한 구성에 액세스할 수 있습니다.
>* 보안 그룹 및 Microsoft 365 그룹 소유자: 그룹 멤버 자격을 관리할 수 있습니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>* Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>* 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.

> [!IMPORTANT]
> 이 역할은 레거시 MFA 관리 포털 또는 하드웨어 OATH 토큰의 MFA 설정을 관리할 수 없습니다. [Set-MsolUser](/powershell/module/msonline/set-msoluser) commandlet Azure AD Powershell 모듈을 사용하여 동일한 기능을 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | 사용자에 대한 인증 방법 만들기 |
> | microsoft.directory/users/authenticationMethods/delete | 사용자에 대한 인증 방법 삭제 |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | 사용자에 대한 개인 식별 정보를 포함하지 않는 인증 방법의 표준 속성 읽기 |
> | microsoft.directory/users/authenticationMethods/basic/update | 사용자에 대한 인증 방법의 기본 속성 업데이트 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="authentication-policy-administrator"></a>인증 정책 관리자

이 역할을 가진 사용자는 인증 방법 정책, 테넌트 전체 MFA 설정, 암호 보호 정책을 구성할 수 있습니다. 이 역할은 암호 보호 설정을 관리할 수 있는 권한( 스마트 잠금 구성 및 사용자 지정 금지 암호 목록 업데이트)을 부여합니다.

[인증 관리자](#authentication-administrator) 역할과 [권한 있는 인증 관리자](#privileged-authentication-administrator) 역할에는 사용자에 등록된 인증 방법을 관리할 수 있는 권한이 있으며 모든 사용자에게 재등록 및 다단계 인증을 강제 적용할 수 있습니다.

| 역할 | 사용자의 인증 방법 관리 | 사용자별 MFA 관리 | MFA 설정 관리 | 인증 방법 정책 관리 | 암호 보호 정책 관리 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 인증 관리자 | 일부 사용자의 경우 예(위 참조) | 일부 사용자의 경우 예(위 참조) | 아니요 | 아니요 | 아니요 |
| 권한 있는 인증 관리자| 모든 사용자에 대해 예 | 모든 사용자에 대해 예 | 아니요 | 아니요 | 아니요 |
| 인증 정책 관리자 | 아니요 | 아니요 | 예 | 예 | 예 |

> [!IMPORTANT]
> 이 역할은 레거시 MFA 관리 포털 또는 하드웨어 OATH 토큰의 MFA 설정을 관리할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/allTasks | 조직의 강력한 인증 속성의 모든 측면 관리 |
> | microsoft.directory/userCredentialPolicies/create | 사용자에 대한 자격 증명 정책 만들기 |
> | microsoft.directory/userCredentialPolicies/delete | 사용자에 대한 자격 증명 정책 삭제 |
> | microsoft.directory/userCredentialPolicies/standard/read | 사용자에 대한 자격 증명 정책의 표준 속성 읽기 |
> | microsoft.directory/userCredentialPolicies/owners/read | 사용자에 대한 자격 증명 정책의 소유자 읽기 |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | policy.appliesTo 탐색 링크 읽기 |
> | microsoft.directory/userCredentialPolicies/basic/update | 사용자에 대한 기본 정책 업데이트 |
> | microsoft.directory/userCredentialPolicies/owners/update | 사용자에 대한 자격 증명 정책의 소유자 업데이트 |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | policy.isOrganizationDefault 속성 업데이트 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | 확인 가능한 자격 증명 카드를 읽습니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | 확인 가능한 자격 증명 카드 해지 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | 확인 가능한 자격 증명 계약을 만듭니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | 확인 가능한 자격 증명 계약을 읽습니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | 확인 가능한 자격 증명 계약을 업데이트합니다. |
> | microsoft.directory/verifiableCredentials/configuration/create | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 만들기 |
> | microsoft.directory/verifiableCredentials/configuration/delete | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성을 삭제하고 확인 가능한 자격 증명을 모두 삭제 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 읽기 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 업데이트 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |

## <a name="azure-ad-joined-device-local-administrator"></a>Azure AD 조인 디바이스 로컬 관리자

이 역할은 [디바이스 설정](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)의 추가 로컬 관리자로서 할당을 위해서만 사용할 수 있습니다. 이 역할을 가진 사용자가 Azure Active Directory에 가입된 모든 Windows 10 디바이스에서 로컬 컴퓨터 관리자가 됩니다. Azure Active Directory의 디바이스 개체를 관리하는 기능이 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | 그룹 설정의 기본 속성 읽기 |
> | microsoft.directory/groupSettingTemplates/standard/read | 그룹 설정 템플릿의 기본 속성 읽기 |

## <a name="azure-devops-administrator"></a>Azure DevOps 관리자

이 역할이 할당된 사용자는 Azure DevOps 정책을 관리하여 새 Azure DevOps 조직 만들기를 구성 가능한 사용자 또는 그룹 세트로 제한할 수 있습니다. 이 역할의 사용자는 회사의 Azure AD 조직에서 지원하는 모든 Azure DevOps 조직을 통해 이 정책을 관리할 수 있습니다. 이 역할은 회사의 Azure AD 조직에서 지원하는 Azure DevOps 조직 내부에서 다른 Azure DevOps 관련 권한(예: 프로젝트 컬렉션 관리자)을 부여하지 않습니다.

모든 엔터프라이즈 Azure DevOps 정책은 이 역할의 사용자가 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Azure DevOps 읽기 및 구성 |

## <a name="azure-information-protection-administrator"></a>Azure Information Protection 관리자

이 역할을 가진 사용자는 Azure Information Protection 서비스에 대한 모든 사용 권한을 갖습니다. 이 역할은 Azure Information Protection 정책에 대한 레이블을 구성하고, 보호 템플릿을 관리하고, 보호를 활성화하는 권한을 갖습니다. 이 역할은 ID 보호 센터, Privileged Identity Management, Microsoft 365 Service Health 또는 Office 365 보안 및 준수 센터에서 어느 권한도 부여하지 않습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면 관리 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF 키 세트 관리자

사용자는 토큰 암호화, 토큰 서명 및 클레임 암호화/암호 해독을 위한 정책 키와 비밀을 만들고 관리할 수 있습니다. 기존 키 컨테이너에 새 키를 추가하면 이 제한된 관리자는 기존 애플리케이션에 영향을 주지 않고 필요에 따라 비밀을 롤오버할 수 있습니다.  이 사용자는 이러한 비밀을 만든 후에도 비밀의 전체 콘텐츠와 만료 날짜를 볼 수 있습니다.

> [!IMPORTANT]
> 이 역할은 중요한 역할입니다.  사전 프로덕션 및 프로덕션 단계에서 키 세트 관리자 역할을 신중하게 감사하고 할당해야 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | 권한 부여 정책의 모든 속성을 읽고 업데이트 |

## <a name="b2c-ief-policy-administrator"></a>B2C IEF 정책 관리자

이 역할의 사용자는 Azure AD B2C에서 모든 사용자 지정 정책을 만들고, 읽고, 업데이트하고, 삭제할 수 있으므로 관련 Azure AD B2C 조직의 ID 환경 프레임워크를 완전히 제어할 수 있습니다. 이 사용자는 정책을 편집하여 외부 ID 공급자와의 직접 페더레이션을 설정하고, 디렉터리 스키마를 변경하고, 모든 사용자 관련 콘텐츠(HTML, CSS, JavaScript)를 변경하고, 인증을 완료하기 위한 요구 사항을 변경하고, 새 사용자를 만들고, 전체 마이그레이션을 포함하여 외부 시스템으로 사용자 데이터를 보내고, 암호 및 전화 번호와 같은 중요한 필드를 비롯한 모든 사용자 정보를 편집할 수 있습니다. 반면 이 역할은 암호화 키를 변경하거나 조직의 페더레이션에 사용되는 비밀을 편집할 수 없습니다.

> [!IMPORTANT]
> B2 IEF 정책 관리자는 매우 중요한 역할이므로 프로덕션 환경의 조직에 대해 매우 제한적으로 할당해야 합니다.  이러한 사용자의 활동을 면밀하게 감사해야 하며, 특히 프로덕션 조직이 그렇습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Azure Active Directory B2C에서 키 세트 읽기 및 구성 |

## <a name="billing-administrator"></a>대금 청구 관리자

구매, 구독 관리, 지원 티켓 관리 및 서비스 상태 모니터링을 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/organization/basic/update | 조직의 기본 속성 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.commerce.billing/allEntities/allTasks | Office 365 청구의 모든 측면 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="cloud-app-security-administrator"></a>Cloud App Security 관리자

이 역할의 사용자는 Cloud App Security에서 모든 권한을 갖습니다. 관리자를 추가하고 MCAS(Microsoft Cloud App Security) 정책과 설정을 추가하고 로그를 업로드하고 거버넌스 작업을 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="cloud-application-administrator"></a>클라우드 애플리케이션 관리자

이 역할의 사용자는 애플리케이션 관리자 역할과 동일한 권한을 가집니다. 다만 애플리케이션 프록시를 관리하는 권한은 없습니다. 이 역할은 엔터프라이즈 애플리케이션 및 애플리케이션 등록의 모든 측면을 만들고 관리하는 기능을 부여합니다. 이 역할에 할당된 사용자는 새 애플리케이션 등록 또는 엔터프라이즈 애플리케이션을 만들 때 소유자로 추가되지 않습니다.

또한 이 역할은 Microsoft Graph 및 Azure AD Graph에 대한 애플리케이션 권한을 제외하고 위임된 권한 및 애플리케이션 권한에 동의하는 기능을 부여합니다.

> [!IMPORTANT]
> 이 예외는 _다른_ 앱(예: 타사 앱 또는 사용자가 등록한 앱)에 대한 애플리케이션 권한에는 동의할 수 있다는 뜻입니다. 여전히 앱을 등록할 때 이 권한을 _요청_ 할 수 있지만, 이 권한을 _부여_(즉, 동의)하려면 전역 관리자처럼 더 많은 권한이 있는 관리자여야 합니다.
>
>이 역할은 애플리케이션 자격 증명을 관리하는 기능을 부여합니다. 이 역할이 할당된 사용자는 애플리케이션에 자격 증명을 추가하고 해당 자격 증명을 사용하여 애플리케이션의 ID를 가장할 수 있습니다. 애플리케이션의 ID에 사용자 또는 다른 개체를 만들거나 업데이트하는 기능과 같이 리소스에 대한 액세스 권한이 부여된 경우 이 역할에 할당된 사용자는 애플리케이션을 가장하는 동안 이러한 작업을 수행할 수 있습니다. 애플리케이션의 ID를 가장하는 이 기능은 본인의 역할 할당을 통해 사용자가 수행할 수 있는 권한 상승이 될 수 있습니다. 애플리케이션 관리자 역할에 사용자를 할당하면 애플리케이션의 ID를 가장하는 기능이 해당 사용자에게 부여된다는 점을 이해해야 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/create | 모든 유형의 애플리케이션 만들기 |
> | microsoft.directory/applications/delete | 모든 유형의 애플리케이션 삭제 |
> | microsoft.directory/applications/appRoles/update | 모든 유형의 애플리케이션에서 appRoles 속성 업데이트 |
> | microsoft.directory/applications/audience/update | 애플리케이션의 대상 그룹 속성 업데이트 |
> | microsoft.directory/applications/authentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/basic/update | 애플리케이션의 기본 속성 업데이트 |
> | microsoft.directory/applications/credentials/update | 애플리케이션 자격 증명 업데이트 |
> | microsoft.directory/applications/extensionProperties/update | 애플리케이션의 확장 속성 업데이트 |
> | microsoft.directory/applications/notes/update | 애플리케이션의 메모 업데이트 |
> | microsoft.directory/applications/owners/update | 애플리케이션 소유자 업데이트 |
> | microsoft.directory/applications/permissions/update | 모든 유형의 애플리케이션에서 공개된 권한 및 필요한 권한 업데이트 |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/applications/tag/update | 애플리케이션의 태그 업데이트 |
> | microsoft.directory/applications/verification/update | applicationsverification 속성 업데이트 |
> | microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션 인스턴스화 |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/deletedItems.applications/delete | 더 이상 복원할 수 없는 애플리케이션 영구 삭제 |
> | microsoft.directory/deletedItems.applications/restore | 일시 삭제된 애플리케이션을 원래 상태로 복원 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/applicationPolicies/create | 애플리케이션 정책 만들기 |
> | microsoft.directory/applicationPolicies/delete | 애플리케이션 정책 삭제 |
> | microsoft.directory/applicationPolicies/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/applicationPolicies/owners/read | 애플리케이션 정책의 소유자 읽기 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 개체 목록에 적용된 애플리케이션 정책 읽기 |
> | microsoft.directory/applicationPolicies/basic/update | 애플리케이션 정책의 표준 속성 업데이트 |
> | microsoft.directory/applicationPolicies/owners/update | 애플리케이션 정책의 소유자 속성 업데이트 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/servicePrincipals/create | 서비스 주체 만들기 |
> | microsoft.directory/servicePrincipals/delete | 서비스 주체 삭제 |
> | microsoft.directory/servicePrincipals/disable | 서비스 주체를 사용하지 않도록 설정 |
> | microsoft.directory/servicePrincipals/enable | 서비스 주체를 사용하도록 설정 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 애플리케이션 프로비저닝 비밀 및 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 애플리케이션 프로비저닝 동기화 작업을 시작, 다시 시작 및 일시 중지 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 애플리케이션 프로비저닝 동기화 작업 및 스키마 만들기 및 관리 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 읽기 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Microsoft Graph 및 Azure AD Graph의 애플리케이션 권한을 제외하고 특정 사용자 또는 모든 사용자를 대신하여 애플리케이션 권한 및 위임된 권한에 대한 동의 부여 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/servicePrincipals/audience/update | 서비스 주체의 대상 그룹 속성 업데이트 |
> | microsoft.directory/servicePrincipals/authentication/update | 서비스 주체의 인증 속성 업데이트 |
> | microsoft.directory/servicePrincipals/basic/update | 서비스 주체의 기본 속성 업데이트 |
> | microsoft.directory/servicePrincipals/credentials/update | 서비스 주체의 자격 증명 업데이트 |
> | microsoft.directory/servicePrincipals/notes/update | 서비스 주체의 메모 업데이트 |
> | microsoft.directory/servicePrincipals/owners/update | 서비스 주체의 소유자 업데이트 |
> | microsoft.directory/servicePrincipals/permissions/update | 서비스 주체의 권한 업데이트 |
> | microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책 업데이트 |
> | microsoft.directory/servicePrincipals/tag/update | 서비스 주체의 태그 속성 업데이트 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="cloud-device-administrator"></a>클라우드 디바이스 관리자

이 역할의 사용자는 Azure AD에서 디바이스를 사용하고 사용하지 않도록 설정하며, 삭제하고, Azure Portal에서 Windows 10 BitLocker 키(있는 경우)를 읽을 수 있습니다. 역할은 디바이스에서 다른 속성을 관리하는 사용 권한을 부여하지 않습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/devices/delete | Azure AD에서 디바이스 삭제 |
> | microsoft.directory/devices/disable | Azure AD에서 디바이스를 사용하지 않도록 설정 |
> | microsoft.directory/devices/enable | Azure AD에서 디바이스를 사용하도록 설정 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 디바이스 관리 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceManagementPolicies/basic/update | 디바이스 관리 애플리케이션 정책의 기본 속성 업데이트 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 디바이스 등록 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | 디바이스 등록 정책의 기본 속성 업데이트 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |

## <a name="compliance-administrator"></a>규정 준수 관리자

이 역할이 있는 사용자에게는 Microsoft 365 규정 준수 센터, Microsoft 365 관리 센터, Azure, Office 365 보안 및 준수 센터에서 규정 준수 관련 기능을 관리할 권한이 있습니다. 담당자는 Exchange 관리 센터와 Teams 및 비즈니스용 Skype 관리 센터 내의 모든 기능을 관리하고 Azure 및 Microsoft 365 관련 지원 티켓을 만들 수도 있습니다. 자세한 내용은 [Microsoft 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

그런 다음 | 가능한 작업
----- | ----------
[Microsoft 365 규정 준수 센터](https://protection.office.com) | Microsoft 365 서비스 전반에 걸쳐 조직의 데이터 보호 및 관리<br>규정 준수 경고 관리
[준수 관리자](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 조직의 규정 준수 활동 추적, 할당, 확인
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 데이터 거버넌스 관리<br>법적 조사/데이터 조사 수행<br>데이터 주체 요청 관리<br><br>이 역할은 Office 365 보안 및 준수 센터 역할 기반 액세스 제어의 [규정 준수 관리자 RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)과 동일한 권한을 갖습니다.
[Intune](/intune/role-based-access-control) | 모든 Intune 감사 데이터 확인
[Cloud App Security](/cloud-app-security/manage-admins) | 읽기 전용 권한 소유/경고를 관리할 수 있음<br>파일 정책을 생성/수정하고 파일 거버넌스 작업을 허용할 수 있음<br>데이터 관리에서 모든 기본 제공 보고서를 확인할 수 있음

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD 권한 관리에서 모든 속성 읽기 |
> | microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="compliance-data-administrator"></a>규정 준수 데이터 관리자

이 역할이 있는 사용자는 Microsoft 365 규정 준수 센터, Microsoft 365 관리 센터 및 Azure에서 데이터를 추적할 수 있는 권한이 있습니다. 또한 사용자는 Exchange 관리 센터, Compliance Manager, Teams 및 비즈니스용 Skype 관리 센터 내에서 규정 준수 데이터를 추적하고 Azure 및 Microsoft 365의 지원 티켓을 만들 수 있습니다. [이 설명서](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)에서는 규정 준수 관리자와 규정 준수 데이터 관리자의 차이점에 대해 자세히 설명합니다.

그런 다음 | 가능한 작업
----- | ----------
[Microsoft 365 규정 준수 센터](https://protection.office.com) | Microsoft 365 서비스 전반의 규정 준수 관련 정책 모니터링<br>규정 준수 경고 관리
[준수 관리자](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | 조직의 규정 준수 활동 추적, 할당, 확인
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 데이터 거버넌스 관리<br>법적 조사/데이터 조사 수행<br>데이터 주체 요청 관리<br><br>이 역할은 Office 365 보안 및 준수 센터 역할 기반 액세스 제어의 [규정 준수 데이터 관리자 RoleGroup](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center)과 동일한 권한을 갖습니다.
[Intune](/intune/role-based-access-control) | 모든 Intune 감사 데이터 확인
[Cloud App Security](/cloud-app-security/manage-admins) | 읽기 전용 권한 소유/경고를 관리할 수 있음<br>파일 정책을 생성/수정하고 파일 거버넌스 작업을 허용할 수 있음<br>데이터 관리에서 모든 기본 제공 보고서를 확인할 수 있음

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면 관리 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="conditional-access-administrator"></a>조건부 액세스 관리자

이 역할을 가진 사용자는 Azure Active Directory 조건부 액세스 설정을 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | 조건부 액세스 정책 만들기 |
> | microsoft.directory/conditionalAccessPolicies/delete | 조건부 액세스 정책 삭제 |
> | microsoft.directory/conditionalAccessPolicies/standard/read | 정책에 대한 조건부 액세스 읽기 |
> | microsoft.directory/conditionalAccessPolicies/owners/read | 조건부 액세스 정책 소유자 읽기 |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | 조건부 액세스 정책에 대한 "적용 대상" 속성 읽기 |
> | microsoft.directory/conditionalAccessPolicies/basic/update | 조건부 액세스 정책의 기본 속성 업데이트 |
> | microsoft.directory/conditionalAccessPolicies/owners/update | 조건부 액세스 정책에 대한 소유자 업데이트 |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | 조건부 액세스 정책에 대한 기본 테넌트 업데이트 |
> | microsoft.directory/crossTenantAccessPolicies/create | 테넌트 간 액세스 정책 만들기 |
> | microsoft.directory/crossTenantAccessPolicies/delete | 테넌트 간 액세스 정책 삭제 |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | 테넌트 간 액세스 정책의 기본 속성 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | 테넌트 간 액세스 정책의 소유자 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | 테넌트 간 액세스 정책의 policyAppliedTo 속성 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | 테넌트 간 액세스 정책의 기본 속성 업데이트 |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | 테넌트 간 액세스 정책의 소유자 업데이트 |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | 테넌트 간 액세스 정책의 기본 테넌트 업데이트 |

## <a name="customer-lockbox-access-approver"></a>고객 LockBox 액세스 승인자

조직에서 [고객 Lockbox 요청](/office365/admin/manage/customer-lockbox-requests)을 관리합니다. 이러한 고객 Lockbox 요청에 대한 이메일 알림을 수신하고 Microsoft 365 관리 센터에서 요청을 승인 및 거부할 수 있습니다. 고객 Lockbox 기능을 켜거나 끌 수도 있습니다. 전역 관리자만이 이 역할에 할당된 사용자의 암호를 다시 설정할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | 고객 Lockbox의 모든 측면 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="desktop-analytics-administrator"></a>데스크톱 분석 관리자

이 역할의 사용자는 Desktop Analytics 서비스를 관리할 수 있습니다. 여기에는 자산 인벤토리를 확인하고, 배포 계획을 만들고, 배포 및 상태를 볼 수 있는 기능이 포함됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | 데스크톱 분석의 모든 측면 관리 |

## <a name="directory-readers"></a>디렉터리 읽기 권한자

이 역할의 사용자는 기본 디렉터리 정보를 읽을 수 있습니다. 이 역할은 다음 용도로 사용해야 합니다.

* 모든 게스트 사용자가 아닌 특정 게스트 사용자에게만 읽기 권한을 부여합니다.
* "Azure AD 포털에 대한 액세스 권한을 관리자로만 제한"이 "예"로 설정된 경우 관리자가 아닌 특정 사용자에게 Azure Portal에 대한 액세스 권한을 부여합니다.
* Directory.Read.All을 사용할 수 없는 디렉터리에 대한 액세스 권한을 서비스 주체에게 부여합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | 관리 단위의 기본 속성 읽기 |
> | microsoft.directory/administrativeUnits/members/read | 관리 단위의 구성원 읽기 |
> | microsoft.directory/applications/standard/read | 애플리케이션의 표준 속성 읽기 |
> | microsoft.directory/applications/owners/read | 애플리케이션 소유자 읽기 |
> | microsoft.directory/applications/policies/read | 애플리케이션 정책 읽기 |
> | microsoft.directory/contacts/standard/read | Azure AD에서 연락처의 표준 속성 읽기 |
> | microsoft.directory/contacts/memberOf/read | Azure AD의 모든 연락처에 대한 그룹 멤버 자격 읽기 |
> | microsoft.directory/contracts/standard/read | 파트너 계약의 기본 속성 읽기 |
> | microsoft.directory/devices/standard/read | 디바이스의 기본 속성 읽기 |
> | microsoft.directory/devices/memberOf/read | 디바이스 멤버 자격 읽기 |
> | microsoft.directory/devices/registeredOwners/read | 등록된 디바이스 소유자 읽기 |
> | microsoft.directory/devices/registeredUsers/read | 등록된 디바이스 사용자 읽기 |
> | microsoft.directory/directoryRoles/standard/read | Azure AD 역할의 기본 속성 업데이트 |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Azure AD 역할의 적격 구성원 읽기 |
> | microsoft.directory/directoryRoles/members/read | Azure AD 역할의 모든 구성원 읽기 |
> | microsoft.directory/domains/standard/read | 도메인의 기본 속성 읽기 |
> | microsoft.directory/groups/standard/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 표준 속성 읽기 |
> | microsoft.directory/groups/appRoleAssignments/read | 그룹의 애플리케이션 역할 할당 읽기 |
> | microsoft.directory/groups/memberOf/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 memberOf 속성 읽기 |
> | microsoft.directory/groups/members/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 구성원 읽기 |
> | microsoft.directory/groups/owners/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 소유자 읽기 |
> | microsoft.directory/groups/settings/read | 그룹 설정 읽기 |
> | microsoft.directory/groupSettings/standard/read | 그룹 설정의 기본 속성 읽기 |
> | microsoft.directory/groupSettingTemplates/standard/read | 그룹 설정 템플릿의 기본 속성 읽기 |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | OAuth 2.0 권한 부여의 기본 속성 읽기 |
> | microsoft.directory/organization/standard/read | 조직의 기본 속성 읽기 |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | 암호 없는 인증에 대한 신뢰할 수 있는 인증 기관 읽기 |
> | microsoft.directory/applicationPolicies/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/roleAssignments/standard/read | 역할 할당의 기본 속성 읽기 |
> | microsoft.directory/roleDefinitions/standard/read | 역할 정의의 기본 속성 읽기 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 서비스 주체 역할 할당 읽기 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 서비스 주체에 할당된 역할 할당 읽기 |
> | microsoft.directory/servicePrincipals/standard/read | 서비스 주체의 기본 속성 읽기 |
> | microsoft.directory/servicePrincipals/memberOf/read | 서비스 주체의 그룹 멤버 자격 읽기 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 서비스 주체의 위임된 권한 부여 읽기 |
> | microsoft.directory/servicePrincipals/owners/read | 서비스 주체의 소유자 읽기 |
> | microsoft.directory/servicePrincipals/ownedObjects/read | 서비스 주체 소유의 개체 읽기 |
> | microsoft.directory/servicePrincipals/policies/read | 서비스 주체의 정책 읽기 |
> | microsoft.directory/subscribedSkus/standard/read | 구독의 기본 속성 읽기 |
> | microsoft.directory/users/standard/read | 사용자의 기본 속성 읽기 |
> | microsoft.directory/users/appRoleAssignments/read | 사용자의 애플리케이션 역할 할당 읽기 |
> | microsoft.directory/users/deviceForResourceAccount/read | 사용자의 deviceForResourceAccount 읽기 |
> | microsoft.directory/users/directReports/read | 사용자에 대한 직접 보고서 읽기 |
> | microsoft.directory/users/licenseDetails/read | 사용자의 라이선스 세부 정보 읽기 |
> | microsoft.directory/users/manager/read | 사용자의 관리자 읽기 |
> | microsoft.directory/users/memberOf/read | 사용자의 그룹 멤버 자격 읽기 |
> | microsoft.directory/users/oAuth2PermissionGrants/read | 사용자에게 위임된 권한 부여 읽기 |
> | microsoft.directory/users/ownedDevices/read | 사용자 소유의 디바이스 읽기 |
> | microsoft.directory/users/ownedObjects/read | 사용자 소유의 개체 읽기 |
> | microsoft.directory/users/photo/read | 사용자 사진 읽기 |
> | microsoft.directory/users/registeredDevices/read | 등록된 사용자 디바이스 읽기 |
> | microsoft.directory/users/scopedRoleMemberOf/read | 관리 단위로 범위가 한정된 Azure AD 역할의 사용자 멤버 자격 읽기 |

## <a name="directory-synchronization-accounts"></a>디렉터리 동기화 계정

사용하지 마십시오. 이 역할은 Azure AD Connect 서비스에 자동으로 할당되고 다른 사용에 적합하거나 지원되지 않습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/create | 모든 유형의 애플리케이션 만들기 |
> | microsoft.directory/applications/delete | 모든 유형의 애플리케이션 삭제 |
> | microsoft.directory/applications/appRoles/update | 모든 유형의 애플리케이션에서 appRoles 속성 업데이트 |
> | microsoft.directory/applications/audience/update | 애플리케이션의 대상 그룹 속성 업데이트 |
> | microsoft.directory/applications/authentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/basic/update | 애플리케이션의 기본 속성 업데이트 |
> | microsoft.directory/applications/credentials/update | 애플리케이션 자격 증명 업데이트 |
> | microsoft.directory/applications/notes/update | 애플리케이션의 메모 업데이트 |
> | microsoft.directory/applications/owners/update | 애플리케이션 소유자 업데이트 |
> | microsoft.directory/applications/permissions/update | 모든 유형의 애플리케이션에서 공개된 권한 및 필요한 권한 업데이트 |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/applications/tag/update | 애플리케이션의 태그 업데이트 |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/organization/dirSync/update | 조직 디렉터리 동기화 속성 업데이트 |
> | microsoft.directory/policies/create | Azure AD에서 정책 만들기 |
> | microsoft.directory/policies/delete | Azure AD에서 정책 삭제 |
> | microsoft.directory/policies/standard/read | 정책의 기본 속성 읽기 |
> | microsoft.directory/policies/owners/read | 정책의 소유자 읽기 |
> | microsoft.directory/policies/policyAppliedTo/read | policies.policyAppliedTo 속성 읽기 |
> | microsoft.directory/policies/basic/update | 정책의 기본 속성 업데이트 |
> | microsoft.directory/policies/owners/update | 정책의 소유자 업데이트 |
> | microsoft.directory/policies/tenantDefault/update | 기본 조직 정책 업데이트 |
> | microsoft.directory/servicePrincipals/create | 서비스 주체 만들기 |
> | microsoft.directory/servicePrincipals/delete | 서비스 주체 삭제 |
> | microsoft.directory/servicePrincipals/enable | 서비스 주체를 사용하도록 설정 |
> | microsoft.directory/servicePrincipals/disable | 서비스 주체를 사용하지 않도록 설정 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 서비스 주체의 암호 Single Sign-On 자격 증명 읽기 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 서비스 주체 역할 할당 읽기 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 서비스 주체에 할당된 역할 할당 읽기 |
> | microsoft.directory/servicePrincipals/standard/read | 서비스 주체의 기본 속성 읽기 |
> | microsoft.directory/servicePrincipals/memberOf/read | 서비스 주체의 그룹 멤버 자격 읽기 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 서비스 주체의 위임된 권한 부여 읽기 |
> | microsoft.directory/servicePrincipals/owners/read | 서비스 주체의 소유자 읽기 |
> | microsoft.directory/servicePrincipals/ownedObjects/read | 서비스 주체 소유의 개체 읽기 |
> | microsoft.directory/servicePrincipals/policies/read | 서비스 주체의 정책 읽기 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/servicePrincipals/audience/update | 서비스 주체의 대상 그룹 속성 업데이트 |
> | microsoft.directory/servicePrincipals/authentication/update | 서비스 주체의 인증 속성 업데이트 |
> | microsoft.directory/servicePrincipals/basic/update | 서비스 주체의 기본 속성 업데이트 |
> | microsoft.directory/servicePrincipals/credentials/update | 서비스 주체의 자격 증명 업데이트 |
> | microsoft.directory/servicePrincipals/notes/update | 서비스 주체의 메모 업데이트 |
> | microsoft.directory/servicePrincipals/owners/update | 서비스 주체의 소유자 업데이트 |
> | microsoft.directory/servicePrincipals/permissions/update | 서비스 주체의 권한 업데이트 |
> | microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책 업데이트 |
> | microsoft.directory/servicePrincipals/tag/update | 서비스 주체의 태그 속성 업데이트 |

## <a name="directory-writers"></a>디렉터리 작성자

이 역할의 사용자는 사용자, 그룹 및 서비스 주체의 기본 정보를 읽고 업데이트할 수 있습니다. [동의 프레임워크](../develop/quickstart-register-app.md)를 지원하지 않는 애플리케이션에만 이 역할을 할당합니다. 이 역할은 어느 사용자에게나 할당되면 안 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/extensionProperties/update | 애플리케이션의 확장 속성 업데이트 |
> | microsoft.directory/groups/assignLicense | 그룹 기반 라이선스 그룹에 제품 라이선스 할당 |
> | microsoft.directory/groups/create | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 그룹 기반 라이선스의 라이선스 할당 다시 처리 |
> | microsoft.directory/groups/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 동적 멤버 관리 규칙 업데이트 |
> | microsoft.directory/groups/groupType/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 그룹 유형에 영향을 주는 속성 업데이트 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups/onPremWriteBack/update | Azure AD Connect를 사용하여 온-프레미스에 다시 쓸 Azure Active Directory 그룹 업데이트 |
> | microsoft.directory/groups/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.directory/groups/settings/update | 그룹 설정 읽기 |
> | microsoft.directory/groups/visibility/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 가시성 속성 업데이트 |
> | microsoft.directory/groupSettings/create | 그룹 설정 만들기 |
> | microsoft.directory/groupSettings/delete | 그룹 설정 삭제 |
> | microsoft.directory/groupSettings/basic/update | 그룹 설정의 기본 속성 업데이트 |
> | microsoft.directory/oAuth2PermissionGrants/create | OAuth 2.0 권한 부여 만들기 |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | OAuth 2.0 권한 부여 업데이트 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 애플리케이션 프로비저닝 비밀 및 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 애플리케이션 프로비저닝 동기화 작업을 시작, 다시 시작 및 일시 중지 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 애플리케이션 프로비저닝 동기화 작업 및 스키마 만들기 및 관리 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 서비스 주체에게 그룹 데이터에 대한 직접 액세스 권한 부여 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/users/assignLicense | 사용자 라이선스 관리 |
> | microsoft.directory/users/create | 사용자 추가 |
> | microsoft.directory/users/disable | 사용자를 사용하지 않도록 설정 |
> | microsoft.directory/users/enable | 사용자를 사용하도록 설정 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/inviteGuest | 게스트 사용자 초대 |
> | microsoft.directory/users/reprocessLicenseAssignment | 사용자에 대한 라이선스 할당 다시 처리 |
> | microsoft.directory/users/basic/update | 사용자의 기본 속성 업데이트 |
> | microsoft.directory/users/manager/update | 사용자의 관리자 업데이트 |
> | microsoft.directory/users/photo/update | 사용자 사진 업데이트 |
> | microsoft.directory/users/userPrincipalName/update | 사용자의 사용자 계정 이름 업데이트 |

## <a name="domain-name-administrator"></a>도메인 이름 관리자

이 역할을 가진 사용자는 도메인 이름을 관리(읽기, 추가, 확인, 업데이트, 삭제)할 수 있습니다. 뿐만 아니라 사용자, 그룹 및 애플리케이션에 대한 디렉터리 정보를 읽을 수도 있습니다. 이러한 개체는 도메인 종속성을 갖고 있기 때문입니다. 온-프레미스 환경의 경우, 해당 역할을 가진 사용자는 연결된 사용자가 항상 온-프레미스에서 인증되도록 페더레이션을 위한 도메인 이름을 구성할 수 있습니다. 해당 사용자는 Single Sign-On을 통해 온-프레미스 암호를 사용하여 Azure AD 기반 서비스에 로그인할 수 있습니다. 페더레이션 설정은 Azure AD Connect를 통해 동기화해야 하므로 사용자는 Azure AD Connect를 관리할 수 있는 권한도 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | 도메인 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="dynamics-365-administrator"></a>Dynamics 365 Administrator

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Dynamics 365 Online 내에서 글로벌 사용 권한을 가질 뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [서비스 관리자 역할을 사용하여 Azure AD 조직 관리](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Dynamics 365 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Dynamics 365 관리자"입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="edge-administrator"></a>Edge 관리자

이 역할의 사용자는 Microsoft Edge에서 Internet Explorer 모드에 필요한 엔터프라이즈 사이트 목록을 만들고 관리할 수 있습니다. 이 역할은 사이트 목록을 만들고, 편집하고, 게시할 수 있는 권한을 부여하고, 추가적으로 지원 티켓을 관리할 수 있는 액세스 권한을 허용합니다. [자세히 알아보기](https://go.microsoft.com/fwlink/?linkid=2165707)

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.edge/allEntities/allProperties/allTasks | Microsoft Edge의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="exchange-administrator"></a>Exchange 관리자

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Exchange Online 내에서 글로벌 사용 권한을 갖습니다. 또한 모든 Microsoft 365 그룹을 만들고 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Microsoft 365 관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Exchange 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Exchange 관리자"입니다. [Exchange 관리 센터](https://go.microsoft.com/fwlink/p/?LinkID=529144)의 "Exchange Online 관리자"입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/groups/hiddenMembers/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 숨겨진 구성원 읽기 |
> | microsoft.directory/groups.unified/create | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups.unified/delete | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups.unified/restore | Microsoft 365 그룹 복원 |
> | microsoft.directory/groups.unified/basic/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.unified/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified/owners/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Exchange Online의 모든 측면 관리 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="exchange-recipient-administrator"></a>Exchange 받는 사람 관리자

이 역할을 가진 사용자는 받는 사람에 대한 읽기 권한과 Exchange Online에서 해당 받는 사람의 특성에 대한 쓰기 권한을 갖습니다. 자세한 내용은 [Exchange 수신자](/exchange/recipients/recipients)를 참조하세요.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Exchange Online에서 모든 받는 사람을 생성 및 삭제하고, 받는 사람의 모든 속성을 읽고 업데이트합니다. |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Exchange Online에서 메시지 추적의 모든 작업 관리 |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Exchange Online에서 받는 사람 마이그레이션과 관련한 모든 작업을 관리합니다. |

## <a name="external-id-user-flow-administrator"></a>외부 ID 사용자 흐름 관리자

이 역할이 할당된 사용자는 Azure Portal에서 사용자 흐름("기본 제공" 정책이라고도 함)을 만들고 관리할 수 있습니다. 이러한 사용자는 HTML/CSS/JavaScript 콘텐츠를 사용자 지정하고, MFA 요구 사항을 변경하고, 토큰에서 클레임을 선택하고, API 커넥터를 관리하고, Azure AD 조직의 모든 사용자 흐름에 대한 세션 설정을 구성할 수 있습니다. 반면 이 역할은 사용자 데이터를 검토하거나 조직 스키마에 포함된 특성을 변경할 수 없습니다. Identity Experience Framework(사용자 지정 정책이라고도 함) 정책을 변경하는 것도 이 역할의 범위를 벗어납니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Azure Active Directory B2C에서 사용자 특성 읽기 및 구성 |

## <a name="external-id-user-flow-attribute-administrator"></a>외부 ID 사용자 흐름 특성 관리자

이 역할의 사용자는 Azure AD 조직의 모든 사용자 흐름에서 사용할 수 있는 사용자 지정 특성을 추가 또는 삭제할 수 있습니다.  따라서 이 역할의 사용자는 새 요소를 변경하거나 최종 사용자 스키마에 추가하고 모든 사용자 흐름의 동작에 영향을 줄 수 있으며, 이로 인해 최종 사용자에게 요청하여 결국 애플리케이션에 대한 클레임으로 전송되는 데이터를 간접적으로 변경할 수 있습니다.  이 역할은 사용자 흐름을 편집할 수 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Azure Active Directory B2C에서 사용자 지정 정책 읽기 및 구성 |

## <a name="external-identity-provider-administrator"></a>외부 ID 공급자 관리자

이 관리자는 Azure AD 조직과 외부 ID 공급자 간의 페더레이션을 관리합니다.  이 역할의 사용자는 새 ID 공급자를 추가하고 사용 가능한 모든 설정(예: 인증 경로, 서비스 ID, 할당된 키 컨테이너)을 구성할 수 있습니다.  이 사용자는 Azure AD 조직이 외부 ID 공급자의 인증을 신뢰할 수 있게 해줍니다.  최종 사용자 환경에 미치는 영향은 조직의 유형에 따라 달라집니다.

* 직원 및 파트너를 위한 Azure AD 조직: 페더레이션을 추가하면(예: Gmail을 사용하여) 아직 사용되지 않은 모든 게스트 초대에 즉시 영향을 줍니다. [Google을 B2B 게스트 사용자에 대한 ID 공급자로 추가](../external-identities/google-federation.md)를 참조하세요.
* Azure Active Directory B2C 조직: 페더레이션을 추가해도(예: Facebook 또는 다른 Azure AD 조직을 사용하여) 사용자 흐름(기본 제공 정책이라고도 함)에서 ID 공급자를 옵션으로 추가하기 전에는 최종 사용자 흐름에 즉시 영향을 주지 않습니다. 관련 예제는 [Microsoft 계정을 ID 공급자로 구성](../../active-directory-b2c/identity-provider-microsoft-account.md)을 참조하세요.  사용자 흐름을 변경하려면 "B2C 사용자 흐름 관리자"의 제한된 역할이 필요합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Azure Active Directory B2C에서 ID 공급자 읽기 및 구성 |

## <a name="global-administrator"></a>전역 관리자

이 역할의 사용자는 Azure Active Directory의 모든 관리 기능뿐 아니라 Microsoft 365 보안 센터, Microsoft 365 규정 준수 센터, Exchange Online, SharePoint Online 및 비즈니스용 Skype Online과 같이 Azure Active Directory ID를 사용하는 서비스에도 액세스할 수 있습니다. 뿐만 아니라 전역 관리자는 모든 Azure 구독 및 관리 그룹을 관리하기 위해 [액세스 권한을 승격](../../role-based-access-control/elevate-access-global-admin.md)할 수 있습니다. 이렇게 하면 전역 관리자가 각 Azure AD 테넌트를 사용하여 모든 Azure 리소스에 대한 전체 액세스 권한을 얻을 수 있습니다. Azure AD 조직에 가입하는 사람은 전역 관리자가 됩니다. 회사에 여러 전역 관리자가 있을 수 있습니다. 전역 관리자는 모든 사용자 및 모든 다른 관리자의 암호를 다시 설정할 수 있습니다.

> [!NOTE]
> 모범 사례에 따라 조직 내에서 5명 미만의 사람에게만 전역 관리자 역할을 할당하는 것이 좋습니다. 자세한 내용은 [Azure AD 역할에 대한 모범 사례](best-practices.md)를 참조하세요.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Azure AD에서 액세스 검토 작성 및 삭제, 액세스 검토의 모든 속성 업데이트 및 그룹의 액세스 검토 관리 |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 관리 단위(구성원 포함)를 만들고 관리합니다. |
> | microsoft.directory/applications/allProperties/allTasks | 애플리케이션 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션 인스턴스화 |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/users/authenticationMethods/create | 사용자에 대한 인증 방법 만들기 |
> | microsoft.directory/users/authenticationMethods/delete | 사용자에 대한 인증 방법 삭제 |
> | microsoft.directory/users/authenticationMethods/standard/read | 사용자에 대한 인증 방법의 표준 속성 읽기 |
> | microsoft.directory/users/authenticationMethods/basic/update | 사용자에 대한 인증 방법의 기본 속성 업데이트 |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | 권한 부여 정책의 모든 측면 관리 |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.directory/connectors/create | 애플리케이션 프록시 커넥터 만들기 |
> | microsoft.directory/connectors/allProperties/read | 애플리케이션 프록시 커넥터의 모든 속성 읽기 |
> | microsoft.directory/connectorGroups/create | 애플리케이션 프록시 커넥터 그룹 만들기 |
> | microsoft.directory/connectorGroups/delete | 애플리케이션 프록시 커넥터 그룹 삭제 |
> | microsoft.directory/connectorGroups/allProperties/read | 애플리케이션 프록시 커넥터 그룹의 모든 속성 읽기 |
> | microsoft.directory/connectorGroups/allProperties/update | 애플리케이션 프록시 커넥터 그룹의 모든 속성 업데이트 |
> | microsoft.directory/contacts/allProperties/allTasks | 연락처 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/contracts/allProperties/allTasks | 파트너 계약 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/deletedItems/delete | 더 이상 복원할 수 없는 개체 영구 삭제 |
> | microsoft.directory/deletedItems/restore | 일시 삭제된 개체를 원래 상태로 복원 |
> | microsoft.directory/devices/allProperties/allTasks | 디바이스 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 디바이스 관리 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceManagementPolicies/basic/update | 디바이스 관리 애플리케이션 정책의 기본 속성 업데이트 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 디바이스 등록 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | 디바이스 등록 정책의 기본 속성 업데이트 |
> | microsoft.directory/directoryRoles/allProperties/allTasks | 디렉터리 역할 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Azure AD 역할 템플릿 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/domains/allProperties/allTasks | 도메인 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD 권한 관리에서 리소스 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groups/allProperties/allTasks | 그룹 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groupsAssignableToRoles/create | 역할 할당 가능 그룹 만들기 |
> | microsoft.directory/groupsAssignableToRoles/delete | 역할 할당 가능 그룹 삭제 |
> | microsoft.directory/groupsAssignableToRoles/restore | 역할 할당 가능 그룹 복원 |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | 역할 할당 가능 그룹 업데이트 |
> | microsoft.directory/groupSettings/allProperties/allTasks | 그룹 설정 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | 그룹 설정 템플릿 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/identityProtection/allProperties/allTasks | Azure AD Identity Protection에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | loginTenantBranding 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/organization/allProperties/allTasks | 조직의 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/policies/allProperties/allTasks | 정책 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | 조건부 액세스 정책의 모든 속성 관리 |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/allTasks |  |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management에서 모든 리소스 읽기 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 역할 할당 만들기 및 삭제, 모든 역할 할당 속성 읽기 및 업데이트 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 역할 정의 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | scopedRoleMemberships 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/serviceAction/activateService | 서비스에 대해 "서비스 활성화" 작업 수행 가능 |
> | microsoft.directory/serviceAction/disableDirectoryFeature | "디렉터리 기능 사용 안 함" 서비스 작업 수행 가능 |
> | microsoft.directory/serviceAction/enableDirectoryFeature | "디렉터리 기능 사용" 서비스 작업 수행 가능 |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Getavailableextentionproperties 서비스 작업 수행 가능 |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | 서비스 주체 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | 애플리케이션에 대한 권한 부여 동의 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 서비스 주체에게 그룹 데이터에 대한 직접 액세스 권한 부여 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | 구독 구입, 관리 및 삭제 |
> | microsoft.directory/users/allProperties/allTasks | 사용자 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/permissionGrantPolicies/create | 권한 부여 정책 만들기 |
> | microsoft.directory/permissionGrantPolicies/delete | 권한 부여 정책 삭제 |
> | microsoft.directory/permissionGrantPolicies/standard/read | 권한 부여 정책의 표준 속성 읽기 |
> | microsoft.directory/permissionGrantPolicies/basic/update | 권한 부여 정책의 기본 속성 업데이트 |
> | microsoft.directory/servicePrincipalCreationPolicies/create | 서비스 주체 만들기 정책 만들기 |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | 서비스 주체 만들기 정책 삭제 |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | 서비스 주체 만들기 정책의 표준 속성 읽기 |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | 서비스 주체 만들기 정책의 기본 속성 업데이트 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | 확인 가능한 자격 증명 카드를 읽습니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | 확인 가능한 자격 증명 카드 해지 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | 확인 가능한 자격 증명 계약을 만듭니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | 확인 가능한 자격 증명 계약을 읽습니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | 확인 가능한 자격 증명 계약을 업데이트합니다. |
> | microsoft.directory/verifiableCredentials/configuration/create | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 만들기 |
> | microsoft.directory/verifiableCredentials/configuration/delete | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성을 삭제하고 확인 가능한 자격 증명을 모두 삭제 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 읽기 |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 업데이트 |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Azure Advanced Threat Protection의 모든 측면 관리 |
> | microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection의 모든 측면 관리 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Windows 365의 모든 측면 관리 |
> | microsoft.commerce.billing/allEntities/allTasks | Office 365 청구의 모든 측면 관리 |
> | microsoft.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면 관리 |
> | microsoft.edge/allEntities/allProperties/allTasks | Microsoft Edge의 모든 측면 관리 |
> | microsoft.flow/allEntities/allTasks | Microsoft Power Automate의 모든 측면 관리 |
> | microsoft.intune/allEntities/allTasks | Microsoft Intune의 모든 측면 관리 |
> | microsoft.office365.complianceManager/allEntities/allTasks | Office 365 준수 관리자의 모든 측면을 관리합니다. |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | 데스크톱 분석의 모든 측면 관리 |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Exchange Online의 모든 측면 관리 |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Microsoft 365 관리 센터에서 콘텐츠 이해의 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | Microsoft 365 관리 센터에서 콘텐츠 이해에 대한 분석 보고서 읽기 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Microsoft 365 관리 센터에서 지식 네트워크의 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | Microsoft 365 관리 센터에서 지식 네트워크의 항목 표시 유형 관리 |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | 학습 앱에서 학습 원본 및 모든 속성을 관리합니다. |
> | microsoft.office365.lockbox/allEntities/allTasks | 고객 Lockbox의 모든 측면 관리 |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.messageCenter/securityMessages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지 읽기 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | 보안 및 규정 준수 센터의 모든 측면 관리 |
> | microsoft.office365.search/content/manage | Microsoft Search에서 콘텐츠 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Microsoft 365 보안 및 규정 준수 센터에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.sharePoint/allEntities/allTasks | SharePoint에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.userCommunication/allEntities/allTasks | 새로운 기능 메시지를 읽고 표시 여부 업데이트 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.powerApps/allEntities/allTasks | Power Apps의 모든 측면 관리 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Power BI의 모든 측면 관리 |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | 엔드포인트용 Microsoft Defender의 모든 측면 관리 |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | Windows Update 서비스의 모든 측면 읽기 및 구성 |

## <a name="global-reader"></a>전역 Reader

이 역할의 사용자는 모든 Microsoft 365 서비스의 설정 및 관리 정보를 읽을 수 있지만 관리 작업을 수행할 수는 없습니다. 전역 읽기 권한자는 전역 관리자에 대응되는 읽기 전용입니다. 계획, 감사 또는 조사에는 전역 관리자 대신 전역 읽기 권한자를 할당합니다. 전역 관리자 역할을 할당하지 않고 Exchange 관리자처럼 제한된 다른 관리자 역할과 함께 전역 읽기 권한자를 사용하면 작업을 쉽게 수행할 수 있습니다. 전역 읽기 권한자는 Microsoft 365 관리 센터, Exchange 관리 센터, SharePoint 관리 센터, Teams 관리 센터, 보안 센터, 규정 준수 센터, Azure AD 관리 센터 및 디바이스 관리 센터에서 작동합니다.

> [!NOTE]
> 현재 전역 읽기 권한자 역할에는 다음과 같은 제한 사항이 있습니다.
>
>- [OneDrive 관리 센터](https://admin.onedrive.com/) - OneDrive 관리 센터는 전역 읽기 권한자 역할을 지원하지 않습니다.
>- [Microsoft 365 관리 센터](https://admin.microsoft.com/Adminportal/Home#/homepage) - 전역 읽기 권한자는 통합 앱을 읽을 수 없습니다. Microsoft 365 관리 센터의 왼쪽 창에 있는 **설정** 아래에 **통합 앱** 탭이 없습니다.
>- [Office 보안 및 준수 센터](https://sip.protection.office.com/homepage) - 전역 읽기 권한자는 SCC 감사 로그를 읽거나 콘텐츠를 검색하거나 보안 점수를 볼 수 없습니다.
>- [Teams 관리 센터](https://admin.teams.microsoft.com) - 전역 읽기 권한자는 **Teams 수명 주기**, **분석 및 보고서**, **IP 전화 디바이스 관리** 및 **앱 카탈로그** 를 읽을 수 없습니다.
>- [PAM(Privileged Access Management)](/office365/securitycompliance/privileged-access-management-overview)은 전역 읽기 권한자 역할을 지원하지 않습니다.
>- [Azure Information Protection](/azure/information-protection/what-is-information-protection) - 전역 읽기 권한자는 [중앙 보고](/azure/information-protection/reports-aip)에만 지원되며 Azure AD 조직이 [통합 레이블 플랫폼](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)에 없는 경우에만 지원됩니다.
> - [SharePoint](https://admin.microsoft.com/sharepoint) - 현재 전역 읽기 권한자는 PowerShell을 사용하여 SharePoint에 액세스할 수 없습니다.
>
> 이러한 기능은 현재 개발 중입니다.
>

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/read | 액세스 검토의 모든 속성 읽기 |
> | microsoft.directory/administrativeUnits/allProperties/read | 관리 단위의 모든 속성 읽기 |
> | microsoft.directory/applications/allProperties/read | 모든 유형의 애플리케이션에서 권한 있는 속성을 포함한 모든 속성 읽기 |
> | microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | 사용자에 대한 개인 식별 정보를 포함하지 않는 인증 방법의 표준 속성 읽기 |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/cloudAppSecurity/allProperties/read | 클라우드 앱 보안에 대한 모든 속성 읽기 |
> | microsoft.directory/connectors/allProperties/read | 애플리케이션 프록시 커넥터의 모든 속성 읽기 |
> | microsoft.directory/connectorGroups/allProperties/read | 애플리케이션 프록시 커넥터 그룹의 모든 속성 읽기 |
> | microsoft.directory/contacts/allProperties/read | 연락처에 대한 모든 속성 읽기 |
> | microsoft.directory/devices/allProperties/read | 디바이스에서 모든 속성 읽기 |
> | microsoft.directory/directoryRoles/allProperties/read | Azure AD 역할에 대한 모든 속성 읽기 |
> | microsoft.directory/directoryRoleTemplates/allProperties/read | 역할 템플릿에 대한 모든 속성 읽기 |
> | microsoft.directory/domains/allProperties/read | 도메인의 모든 속성 읽기 |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD 권한 관리에서 모든 속성 읽기 |
> | microsoft.directory/groups/allProperties/read | 역할 할당 가능한 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 모든 속성(권한 있는 속성 포함) 읽기 |
> | microsoft.directory/groupSettings/allProperties/read | 그룹 설정의 모든 속성 읽기 |
> | microsoft.directory/groupSettingTemplates/allProperties/read | 그룹 설정 템플릿의 모든 속성 읽기 |
> | microsoft.directory/identityProtection/allProperties/read | Azure AD Identity Protection에서 모든 리소스 읽기 |
> | microsoft.directory/loginOrganizationBranding/allProperties/read | 조직의 브랜드 로그인 페이지에 대한 모든 속성 읽기 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/read | OAuth 2.0 권한 부여의 모든 속성 읽기 |
> | microsoft.directory/organization/allProperties/read | 조직에 대한 모든 속성 읽기 |
> | microsoft.directory/permissionGrantPolicies/standard/read | 권한 부여 정책의 표준 속성 읽기 |
> | microsoft.directory/policies/allProperties/read | 모든 정책 속성 읽기 |
> | microsoft.directory/conditionalAccessPolicies/allProperties/read | 조건부 액세스 정책의 모든 속성 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/read | 테넌트 간 정책의 모든 속성 읽기 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 디바이스 관리 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 디바이스 등록 정책의 표준 속성 읽기 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management에서 모든 리소스 읽기 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/roleAssignments/allProperties/read | 역할 할당의 모든 속성 읽기 |
> | microsoft.directory/roleDefinitions/allProperties/read | 역할 정의의 모든 속성 읽기 |
> | microsoft.directory/scopedRoleMemberships/allProperties/read | 관리 단위의 멤버 보기 |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Getavailableextentionproperties 서비스 작업 수행 가능 |
> | microsoft.directory/servicePrincipals/allProperties/read | servicePrincipals에서 권한 있는 속성을 포함한 모든 속성 읽기 |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | 서비스 주체 만들기 정책의 표준 속성 읽기 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.directory/subscribedSkus/allProperties/read | 제품 구독의 모든 속성 읽기 |
> | microsoft.directory/users/allProperties/read | 모든 사용자 속성 읽기 |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | 확인 가능한 자격 증명 카드를 읽습니다. |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | 확인 가능한 자격 증명 계약을 읽습니다. |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | 확인 가능한 자격 증명을 만들고 관리하는 데 필요한 구성 읽기 |
> | microsoft.cloudPC/allEntities/allProperties/read | Windows 365의 모든 측면 읽기 |
> | microsoft.commerce.billing/allEntities/read | Office 365 청구의 모든 리소스 읽기 |
> | microsoft.edge/allEntities/allProperties/read | Microsoft Edge의 모든 측면 읽기 |
> | microsoft.office365.exchange/allEntities/standard/read | Exchange Online의 모든 리소스 읽기 |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.messageCenter/securityMessages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지 읽기 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | 보안 및 규정 준수 센터에서 모든 속성 읽기 |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Microsoft 365 보안 및 규정 준수 센터에서 표준 속성 읽기 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/read | Windows Update 서비스의 모든 측면 읽기 |

## <a name="groups-administrator"></a>그룹 관리자

이 역할의 사용자는 그룹과 해당 설정(이름, 만료 정책 등)을 만들고 관리할 수 있습니다. 사용자를 이 역할에 할당하면 Outlook뿐 아니라 Teams, SharePoint, Yammer 등의 다양한 워크로드에서 조직의 모든 그룹을 관리하는 기능이 부여됩니다. 또한 사용자는 Microsoft 관리 센터, Azure Portal과 같은 다양한 관리 포털에서 다양한 그룹 설정뿐만 아니라 Teams 및 SharePoint 관리 센터와 같은 워크로드 관련 그룹 설정을 관리할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/groups/assignLicense | 그룹 기반 라이선스 그룹에 제품 라이선스 할당 |
> | microsoft.directory/groups/create | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups/delete | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups/hiddenMembers/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 숨겨진 구성원 읽기 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 그룹 기반 라이선스의 라이선스 할당 다시 처리 |
> | microsoft.directory/groups/restore | 삭제된 그룹 복원 |
> | microsoft.directory/groups/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 동적 멤버 관리 규칙 업데이트 |
> | microsoft.directory/groups/groupType/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 그룹 유형에 영향을 주는 속성 업데이트 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups/onPremWriteBack/update | Azure AD Connect를 사용하여 온-프레미스에 다시 쓸 Azure Active Directory 그룹 업데이트 |
> | microsoft.directory/groups/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.directory/groups/settings/update | 그룹 설정 읽기 |
> | microsoft.directory/groups/visibility/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 가시성 속성 업데이트 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 서비스 주체에게 그룹 데이터에 대한 직접 액세스 권한 부여 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="guest-inviter"></a>게스트 초대자

이 역할의 사용자는 **멤버가 초대할 수 있음** 사용자 설정을 아니요로 설정하는 경우 Azure Active Directory B2B 게스트 사용자 초대를 관리할 수 있습니다. B2B 협업에 대한 자세한 내용은 [Azure AD B2B 협업 정보](../external-identities/what-is-b2b.md)를 참조하세요. 다른 권한은 포함되지 않습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | 게스트 사용자 초대 |
> | microsoft.directory/users/standard/read | 사용자의 기본 속성 읽기 |
> | microsoft.directory/users/appRoleAssignments/read | 사용자의 애플리케이션 역할 할당 읽기 |
> | microsoft.directory/users/deviceForResourceAccount/read | 사용자의 deviceForResourceAccount 읽기 |
> | microsoft.directory/users/directReports/read | 사용자에 대한 직접 보고서 읽기 |
> | microsoft.directory/users/licenseDetails/read | 사용자의 라이선스 세부 정보 읽기 |
> | microsoft.directory/users/manager/read | 사용자의 관리자 읽기 |
> | microsoft.directory/users/memberOf/read | 사용자의 그룹 멤버 자격 읽기 |
> | microsoft.directory/users/oAuth2PermissionGrants/read | 사용자에게 위임된 권한 부여 읽기 |
> | microsoft.directory/users/ownedDevices/read | 사용자 소유의 디바이스 읽기 |
> | microsoft.directory/users/ownedObjects/read | 사용자 소유의 개체 읽기 |
> | microsoft.directory/users/photo/read | 사용자 사진 읽기 |
> | microsoft.directory/users/registeredDevices/read | 등록된 사용자 디바이스 읽기 |
> | microsoft.directory/users/scopedRoleMemberOf/read | 관리 단위로 범위가 한정된 Azure AD 역할의 사용자 멤버 자격 읽기 |

## <a name="helpdesk-administrator"></a>기술 지원팀 관리자

이 역할을 가진 사용자는 암호를 변경하고, 새로 고침 토큰을 무효화하고, 서비스 요청을 관리하며, 서비스 상태를 모니터링할 수 있습니다. 새로 고침 토큰을 무효화하면 사용자가 다시 로그인해야 합니다. 기술 지원팀 관리자가 사용자의 암호를 다시 설정하고 새로 고침 토큰을 무효화할 수 있는지 여부는 사용자에게 할당된 역할에 따라 달라집니다. 기술 지원팀 관리자가 암호를 다시 설정하고 새로 고침 토큰을 무효화할 수 있는 역할 목록은 [암호 재설정 권한](#password-reset-permissions)을 참조하세요.

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 암호를 변경할 수 있습니다. 사용자의 암호를 변경한다는 것은 사용자의 ID 및 권한을 가정할 수 있다는 것을 의미합니다. 다음은 그 예입니다.
>
>- 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 권한이 부여되었을 수 있으며, 다른 위치에서는 기술 지원 팀 관리자에 권한이 부여되지 않습니다. 이 경로를 통해 기술 지원 팀 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>- Azure 구독 소유자: Azure의 중요한 정보나 개인 정보 또는 중요한 구성에 액세스할 수 있습니다.
>- 보안 그룹 및 Microsoft 365 그룹 소유자: 그룹 멤버 자격을 관리할 수 있습니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>- Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>- 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.

[관리 단위](administrative-units.md)를 사용하여 일부 사용자에게 관리 권한을 위임하고 일부 사용자에게 정책을 적용할 수 있습니다.

이 역할을 이전에는 [Azure Portal](https://portal.azure.com/)에서 "암호 관리자"라고 했습니다. Azure AD의 "기술 지원팀 관리자" 이름이 이제는 Azure AD PowerShell 및 Microsoft Graph API의 이름과 일치합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="hybrid-identity-administrator"></a>하이브리드 ID 관리자

이 역할의 사용자는 Azure AD Connect 및 페더레이션 설정을 관리할 수 있을 뿐 아니라 클라우드 프로비저닝을 사용하여 AD에서 Azure AD로의 프로비전 구성 설정을 만들고 관리하고 배포할 수 있습니다. 또한 사용자는 이 역할을 사용하여 로그 문제를 해결하고 모니터링할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/create | 모든 유형의 애플리케이션 만들기 |
> | microsoft.directory/applications/delete | 모든 유형의 애플리케이션 삭제 |
> | microsoft.directory/applications/appRoles/update | 모든 유형의 애플리케이션에서 appRoles 속성 업데이트 |
> | microsoft.directory/applications/audience/update | 애플리케이션의 대상 그룹 속성 업데이트 |
> | microsoft.directory/applications/authentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/basic/update | 애플리케이션의 기본 속성 업데이트 |
> | microsoft.directory/applications/notes/update | 애플리케이션의 메모 업데이트 |
> | microsoft.directory/applications/owners/update | 애플리케이션 소유자 업데이트 |
> | microsoft.directory/applications/permissions/update | 모든 유형의 애플리케이션에서 공개된 권한 및 필요한 권한 업데이트 |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/applications/tag/update | 애플리케이션의 태그 업데이트 |
> | microsoft.directory/applications/synchronization/standard/read | 애플리케이션 개체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/applicationTemplates/instantiate | 애플리케이션 템플릿에서 갤러리 애플리케이션 인스턴스화 |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Azure AD 클라우드 프로비저닝 서비스의 모든 속성을 읽고 구성합니다. |
> | microsoft.directory/deletedItems.applications/delete | 더 이상 복원할 수 없는 애플리케이션 영구 삭제 |
> | microsoft.directory/deletedItems.applications/restore | 일시 삭제된 애플리케이션을 원래 상태로 복원 |
> | microsoft.directory/domains/allProperties/read | 도메인의 모든 속성 읽기 |
> | microsoft.directory/domains/federation/update | 도메인의 페더레이션 속성 업데이트 |
> | microsoft.directory/organization/dirSync/update | 조직 디렉터리 동기화 속성 업데이트 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/servicePrincipals/create | 서비스 주체 만들기 |
> | microsoft.directory/servicePrincipals/delete | 서비스 주체 삭제 |
> | microsoft.directory/servicePrincipals/disable | 서비스 주체를 사용하지 않도록 설정 |
> | microsoft.directory/servicePrincipals/enable | 서비스 주체를 사용하도록 설정 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | 애플리케이션 프로비저닝 비밀 및 자격 증명 관리 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 애플리케이션 프로비저닝 동기화 작업을 시작, 다시 시작 및 일시 중지 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 애플리케이션 프로비저닝 동기화 작업 및 스키마 만들기 및 관리 |
> | microsoft.directory/servicePrincipals/audience/update | 서비스 주체의 대상 그룹 속성 업데이트 |
> | microsoft.directory/servicePrincipals/authentication/update | 서비스 주체의 인증 속성 업데이트 |
> | microsoft.directory/servicePrincipals/basic/update | 서비스 주체의 기본 속성 업데이트 |
> | microsoft.directory/servicePrincipals/credentials/update | 서비스 주체의 자격 증명 업데이트 |
> | microsoft.directory/servicePrincipals/notes/update | 서비스 주체의 메모 업데이트 |
> | microsoft.directory/servicePrincipals/owners/update | 서비스 주체의 소유자 업데이트 |
> | microsoft.directory/servicePrincipals/permissions/update | 서비스 주체의 권한 업데이트 |
> | microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책 업데이트 |
> | microsoft.directory/servicePrincipals/tag/update | 서비스 주체의 태그 속성 업데이트 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 서비스 주체와 연결된 프로비저닝 설정 읽기 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="identity-governance-administrator"></a>Identity Governance 관리자

이 역할을 가진 사용자는 액세스 패키지, 액세스 검토, 카탈로그 및 정책을 비롯한 Azure AD Identity Governance 구성을 관리할 수 있으며, 액세스를 승인하고 검토하며 더 이상 액세스가 필요하지 않은 게스트 사용자를 제거할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Azure AD에서 액세스 검토 작성 및 삭제, 액세스 검토의 모든 속성 업데이트 및 그룹의 액세스 검토 관리 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD 권한 관리에서 리소스 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |

## <a name="insights-administrator"></a>Insights 관리자

이 역할의 사용자는 [Microsoft 365 Insights 애플리케이션](https://go.microsoft.com/fwlink/?linkid=2129521)의 모든 관리 기능에 액세스할 수 있습니다. 이 역할은 디렉터리 정보를 읽고 서비스 상태를 모니터링하고 지원 티켓을 제출하고 Insights 관리자 설정에 액세스할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.insights/allEntities/allTasks | Insights 앱의 모든 측면 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="insights-business-leader"></a>Insights 비즈니스 리더

이 역할의 사용자는 [Microsoft 365 insights 애플리케이션](https://go.microsoft.com/fwlink/?linkid=2129521)을 통해 대시보드 및 정보 세트에 액세스할 수 있습니다. 여기에는 모든 대시보드에 대한 전체 액세스 권한과 제공되는 인사이트 및 데이터 탐색 기능이 포함됩니다. 이 역할의 사용자는 제품 구성 설정에 액세스할 수 없습니다. 제품 구성 설정은 Insights 관리자 역할의 책임입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.insights/reports/read | Insights 앱에서 보고서 및 대시보드 보기 |
> | microsoft.insights/programs/update | Insights 앱에서 프로그램 배포 및 관리 |

## <a name="intune-administrator"></a>Intune 관리자

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Intune Online 내에서 글로벌 사용 권한을 갖습니다. 또한 이 역할은 정책을 연결하고 그룹을 만들고 관리하기 위해 사용자와 디바이스를 관리하는 기능을 포함합니다. 자세한 내용은 [Microsoft Intune에서 RBAC(역할 기반 관리 제어)](/intune/role-based-access-control)를 참조하세요

이 역할은 모든 보안 그룹을 만들고 관리할 수 있습니다. 그러나 Intune 관리자에게는 Office 그룹에 대한 관리자 권한이 없습니다. 즉, 관리자는 조직 내 모든 Office 그룹의 소유자 또는 멤버를 업데이트할 수 없습니다. 그러나 관리자는 자신이 만드는 Office 그룹을 관리할 수 있으며, 이 권한은 최종 사용자 권한의 일부로 제공됩니다. 따라서 관리자가 만드는 모든 Office 그룹(보안 그룹 아님)은 관리자의 250 할당량을 기준으로 계산되어야 합니다.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Intune 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Intune 관리자"입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/contacts/create | 연락처 만들기 |
> | microsoft.directory/contacts/delete | 연락처 삭제 |
> | microsoft.directory/contacts/basic/update | 연락처의 기본 속성 업데이트 |
> | microsoft.directory/devices/create | 디바이스 만들기(Azure AD에 등록) |
> | microsoft.directory/devices/delete | Azure AD에서 디바이스 삭제 |
> | microsoft.directory/devices/disable | Azure AD에서 디바이스를 사용하지 않도록 설정 |
> | microsoft.directory/devices/enable | Azure AD에서 디바이스를 사용하도록 설정 |
> | microsoft.directory/devices/basic/update | 디바이스의 기본 속성 업데이트 |
> | microsoft.directory/devices/extensionAttributeSet1/update | 디바이스에서 extensionAttribute1을 extensionAttribute5 속성으로 업데이트 |
> | microsoft.directory/devices/extensionAttributeSet2/update | 디바이스에서 extensionAttribute6을 extensionAttribute10 속성으로 업데이트 |
> | microsoft.directory/devices/extensionAttributeSet3/update | 디바이스에서 extensionAttribute11을 extensionAttribute15 속성으로 업데이트 |
> | microsoft.directory/devices/registeredOwners/update | 등록된 디바이스 소유자 업데이트 |
> | microsoft.directory/devices/registeredUsers/update | 등록된 디바이스 사용자 업데이트 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 디바이스 관리 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 디바이스 등록 정책의 표준 속성 읽기 |
> | microsoft.directory/groups/hiddenMembers/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 숨겨진 구성원 읽기 |
> | microsoft.directory/groups.security/create | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 |
> | microsoft.directory/groups.security/delete | 역할 할당 가능 그룹을 제외한 보안 그룹 삭제 |
> | microsoft.directory/groups.security/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.security/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | 역할 할당 가능 그룹을 제외하고 보안 그룹의 동적 멤버 자격 규칙 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 소유자 업데이트 |
> | microsoft.directory/groups.security/visibility/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 가시성 속성 업데이트 |
> | microsoft.directory/users/basic/update | 사용자의 기본 속성 업데이트 |
> | microsoft.directory/users/manager/update | 사용자의 관리자 업데이트 |
> | microsoft.directory/users/photo/update | 사용자 사진 업데이트 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Windows 365의 모든 측면 관리 |
> | microsoft.intune/allEntities/allTasks | Microsoft Intune의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="kaizala-administrator"></a>Kaizala 관리자

Microsoft Kaizala 서비스가 있는 경우 이 역할의 사용자에게는 서비스 내에서 설정을 관리할 수 있을 뿐 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있는 글로벌 권한이 부여됩니다. 또한 사용자는 조직 구성원의 Kaizala 도입 및 사용과 관련된 정보, 그리고 Kaizala 작업을 사용하여 생성된 비즈니스 보고서에 액세스할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="knowledge-administrator"></a>지식 관리자

이 역할의 사용자는 Microsoft 365 관리 센터에서 모든 지식, 학습 및 인텔리전트 기능 설정에 액세스할 수 있습니다. 제품군과 라이선스 정보에 대한 일반적인 지식을 갖고 있으며 액세스를 제어할 책임이 있습니다. 지식 관리자는 토픽, 머리글자어 및 학습 리소스와 같은 콘텐츠를 만들고 관리할 수 있습니다. 또한 콘텐츠 센터를 만들고, 서비스 상태를 모니터링하고, 서비스 요청을 만들 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/groups.security/create | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 |
> | microsoft.directory/groups.security/createAsOwner | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/groups.security/delete | 역할 할당 가능 그룹을 제외한 보안 그룹 삭제 |
> | microsoft.directory/groups.security/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 소유자 업데이트 |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Microsoft 365 관리 센터에서 콘텐츠 이해의 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Microsoft 365 관리 센터에서 지식 네트워크의 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | 학습 앱에서 학습 원본 및 모든 속성을 관리합니다. |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | 보안 및 규정 준수 센터에서 민감도 레이블의 모든 속성 읽기 |
> | microsoft.office365.sharePoint/allEntities/allTasks | SharePoint에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="knowledge-manager"></a>지식 관리자

이 역할의 사용자는 항목, 약자 및 학습 콘텐츠와 같은 콘텐츠를 만들고 관리할 수 있습니다. 이러한 사용자는 주로 정보의 품질 및 구조를 담당합니다. 이 사용자는 항목을 확인하거나, 편집 내용을 승인하거나, 항목을 삭제할 수 있는 항목 관리 작업에 대한 모든 권한을 가집니다. 이 역할은 용어 저장소 관리 도구의 일부로 분류를 관리하고 콘텐츠 센터를 만들 수도 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/groups.security/create | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 |
> | microsoft.directory/groups.security/createAsOwner | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/groups.security/delete | 역할 할당 가능 그룹을 제외한 보안 그룹 삭제 |
> | microsoft.directory/groups.security/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 소유자 업데이트 |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | Microsoft 365 관리 센터에서 콘텐츠 이해에 대한 분석 보고서 읽기 |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | Microsoft 365 관리 센터에서 지식 네트워크의 항목 표시 유형 관리 |
> | microsoft.office365.sharePoint/allEntities/allTasks | SharePoint에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="license-administrator"></a>라이선스 관리자

이 역할의 사용자는 사용자 및 그룹의(그룹 기반 라이선스 사용) 라이선스 할당을 추가, 제거 및 업데이트하고, 사용자의 사용 위치를 관리합니다. 이 역할은 사용 위치를 벗어나서 구독을 구매 또는 관리하고, 그룹을 만들거나 관리하고, 사용자를 만들거나 관리하는 기능을 부여하지 않습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/groups/assignLicense | 그룹 기반 라이선스 그룹에 제품 라이선스 할당 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 그룹 기반 라이선스의 라이선스 할당 다시 처리 |
> | microsoft.directory/users/assignLicense | 사용자 라이선스 관리 |
> | microsoft.directory/users/reprocessLicenseAssignment | 사용자에 대한 라이선스 할당 다시 처리 |
> | microsoft.directory/users/usageLocation/update | 사용자의 사용 위치 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="message-center-privacy-reader"></a>메시지 센터 개인 정보 읽기 권한자

이 역할의 사용자는 데이터 개인 정보 메시지를 포함하여 메시지 센터의 모든 알림을 모니터링할 수 있습니다. 메시지 센터 개인 정보 읽기 권한자는 데이터 개인 정보와 관련된 알림을 비롯한 이메일 알림을 수신하며, 메시지 센터 기본 설정을 사용하여 구독을 취소할 수 있습니다. 전역 관리자와 메시지 센터 개인 정보 읽기 권한자만 데이터 개인 정보 메시지를 읽을 수 있습니다. 또한 이 역할은 그룹, 도메인 및 구독을 볼 수 있습니다. 이 역할에는 서비스 요청을 보고, 만들고, 관리할 수 있는 권한이 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.messageCenter/securityMessages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="message-center-reader"></a>메시지 센터 읽기 권한자

이 역할의 사용자는 Exchange, Intune 및 Microsoft Teams와 같은 구성된 서비스에서 조직에 대한 [메시지 센터](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)의 알림 및 자문 상태 업데이트를 모니터링할 수 있습니다. 메시지 센터 읽기 권한자는 게시물 및 업데이트 이메일 다이제스트를 매주 수신하며, 메시지 센터 게시물을 Microsoft 365에 공유할 수 있습니다. Azure AD에서 이 역할에 할당된 사용자는 Azure AD 서비스에서 사용자 및 그룹처럼 읽기 전용 권한만 있습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="modern-commerce-user"></a>최신 Commerce 사용자

사용하지 마십시오. 이 역할은 상거래에서 자동으로 할당되며 다른 용도로 사용하기에 적합하거나 지원되지 않습니다. 아래에서 자세한 내용을 참조하세요.

최신 상거래 사용자 역할은 특정 사용자에게 Microsoft 365 관리 센터에 액세스하여 **홈**, **청구** 및 **지원** 에 대한 왼쪽 탐색 항목을 볼 수 있는 권한을 부여합니다. 이러한 영역에 제공되는 콘텐츠는 사용자가 직접 구매했거나 조직을 대신해서 구매한 제품을 관리하기 위해 사용자에게 할당하는 [상거래 관련 역할](../../cost-management-billing/manage/understand-mca-roles.md)을 통해 제어됩니다. 여기에는 요금 결제 또는 청구 계정 및 청구 프로필 액세스와 같은 작업이 포함될 수 있습니다.

최신 상거래 사용자 역할이 있는 사용자에게는 일반적으로 다른 Microsoft 구매 시스템에 관리 권한이 있지만 관리 센터에 액세스하는 데 사용되는 전역 관리자나 대금 청구 관리자 역할은 없습니다.

**최신 상거래 사용자 역할은 언제 할당되나요?**

* **Microsoft 365 관리 센터에서 셀프 서비스 구매** – 사용자는 셀프 서비스 구매를 통해 제품을 직접 구매하거나 가입하여 신제품을 사용해 볼 수 있습니다. 이러한 제품은 관리 센터에서 관리됩니다. 셀프 서비스로 구매하는 사용자에게는 상거래 시스템의 역할과 최신 상거래 사용자 역할이 할당되므로 관리 센터에서 구매를 관리할 수 있습니다. 관리자는 [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell)을 통해(Power BI, Power Apps 및 Power Automate에 대한) 셀프 서비스 구매를 차단할 수 있습니다. 자세한 내용은 [셀프 서비스 구매 질문과 대답](/microsoft-365/commerce/subscriptions/self-service-purchase-faq)을 참조하세요.
* **Microsoft 상업용 Marketplace에서 구매** – 셀프 서비스 구매와 마찬가지로 사용자가 Microsoft AppSource 또는 Azure Marketplace에서 제품이나 서비스를 구매할 때 사용자에게 전역 관리자나 대금 청구 관리자 역할이 없으면 최신 상거래 사용자 역할이 할당됩니다. 경우에 따라 사용자가 제품을 구매하지 못하게 차단될 수도 있습니다. 자세한 내용은 [Microsoft 상업용 마켓플레이스](../../marketplace/marketplace-faq-publisher-guide.yml#what-could-block-a-customer-from-completing-a-purchase-)를 참조하세요.
* **Microsoft의 제안** – 제안은 조직에 Microsoft 제품 및 서비스를 구매할 것을 권유하는 Microsoft의 공식 제안입니다. 제안을 수락하는 사람에게 Azure AD의 전역 관리자나 대금 청구 관리자 역할이 없으면 제안을 완료하기 위한 상거래 관련 역할과 관리 센터에 액세스하기 위한 최신 상거래 사용자 역할이 모두 할당됩니다. 이 사람은 관리 센터에 액세스할 때 자신의 상거래 관련 역할에 의해 권한이 부여된 기능만 사용할 수 있습니다.
* **상거래 관련 역할** - 일부 사용자에게 상거래 관련 역할이 할당됩니다. 전역 관리자나 대금 청구 관리자가 아닌 사용자에게는 관리 센터에 액세스할 수 있도록 최신 상거래 관리자 역할이 할당됩니다.

사용자가 최신 상거래 사용자 역할의 할당을 해제하면 Microsoft 365 관리 센터에 액세스할 수 없게 됩니다. 직접 또는 조직을 대신해서 제품을 관리하는 사용자는 제품을 관리할 수 없게 됩니다. 여기에는 라이선스 할당, 결제 방법 변경, 청구서 결제 또는 구독 관리에 대한 기타 작업이 포함될 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Microsoft 365 청구의 파트너 속성 읽기 |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | 볼륨 라이선스 서비스 센터의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/basic/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="network-administrator"></a>네트워크 관리자

이 역할의 사용자는 사용자 위치의 네트워크 원격 분석 데이터를 기반으로 작성되는 Microsoft의 네트워크 경계 아키텍처 권장 사항을 검토할 수 있습니다. Microsoft 365의 네트워크 성능은 일반적으로 사용자 위치와 관련이 있는 신중한 엔터프라이즈 고객 네트워크 경계 아키텍처에 좌우됩니다. 이 역할이 있으면 검색된 사용자 위치와 해당 위치의 네트워크 매개 변수 구성을 편집하여 원격 분석 측정 및 디자인 권장 사항을 쉽게 개선할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | 네트워크 위치의 모든 측면 관리 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="office-apps-administrator"></a>Office 앱 관리자

이 역할의 사용자는 Microsoft 365 앱의 클라우드 설정을 관리할 수 있습니다. 여기에는 클라우드 정책 관리, 셀프 서비스 다운로드 관리, Office 앱 관련 보고서를 살펴보는 기능이 포함됩니다. 이 역할은 주 관리 센터 내에서 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능도 부여합니다. 이 역할이 할당된 사용자는 Office 앱 최신 기능의 통신도 관리할 수도 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.userCommunication/allEntities/allTasks | 새로운 기능 메시지를 읽고 표시 여부 업데이트 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="partner-tier1-support"></a>파트너 계층1 지원

사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

> [!IMPORTANT]
> 이 역할은 관리자가 아닌 사용자의 암호를 다시 설정하고 새로 고침 토큰을 무효화할 수 있습니다. 이 역할은 더 이상 사용되지 않으며 API에서 더 이상 반환되지 않으므로 사용하면 안 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | 모든 유형의 애플리케이션에서 appRoles 속성 업데이트 |
> | microsoft.directory/applications/audience/update | 애플리케이션의 대상 그룹 속성 업데이트 |
> | microsoft.directory/applications/authentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/basic/update | 애플리케이션의 기본 속성 업데이트 |
> | microsoft.directory/applications/credentials/update | 애플리케이션 자격 증명 업데이트 |
> | microsoft.directory/applications/notes/update | 애플리케이션의 메모 업데이트 |
> | microsoft.directory/applications/owners/update | 애플리케이션 소유자 업데이트 |
> | microsoft.directory/applications/permissions/update | 모든 유형의 애플리케이션에서 공개된 권한 및 필요한 권한 업데이트 |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/applications/tag/update | 애플리케이션의 태그 업데이트 |
> | microsoft.directory/contacts/create | 연락처 만들기 |
> | microsoft.directory/contacts/delete | 연락처 삭제 |
> | microsoft.directory/contacts/basic/update | 연락처의 기본 속성 업데이트 |
> | microsoft.directory/groups/create | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups/delete | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups/restore | 삭제된 그룹 복원 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/users/assignLicense | 사용자 라이선스 관리 |
> | microsoft.directory/users/create | 사용자 추가 |
> | microsoft.directory/users/delete | 사용자 삭제 |
> | microsoft.directory/users/disable | 사용자를 사용하지 않도록 설정 |
> | microsoft.directory/users/enable | 사용자를 사용하도록 설정 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/restore | 삭제된 사용자 복원 |
> | microsoft.directory/users/basic/update | 사용자의 기본 속성 업데이트 |
> | microsoft.directory/users/manager/update | 사용자의 관리자 업데이트 |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.directory/users/photo/update | 사용자 사진 업데이트 |
> | microsoft.directory/users/userPrincipalName/update | 사용자의 사용자 계정 이름 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="partner-tier2-support"></a>파트너 계층2 지원

사용하지 마십시오. 이 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다. 이 역할은 적은 수의 Microsoft 전매 파트너에서 사용하기 위한 것으로 일반적인 용도로는 적합하지 않습니다.

> [!IMPORTANT]
> 이 역할은 암호를 다시 설정할 수 있으며 관리자가 아닌 모든 사용자와 관리자(전역 관리자 포함)의 새로 고침 토큰을 무효화할 수 있습니다. 이 역할은 더 이상 사용되지 않으며 API에서 더 이상 반환되지 않으므로 사용하면 안 됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | 모든 유형의 애플리케이션에서 appRoles 속성 업데이트 |
> | microsoft.directory/applications/audience/update | 애플리케이션의 대상 그룹 속성 업데이트 |
> | microsoft.directory/applications/authentication/update | 모든 유형의 애플리케이션에서 인증 업데이트 |
> | microsoft.directory/applications/basic/update | 애플리케이션의 기본 속성 업데이트 |
> | microsoft.directory/applications/credentials/update | 애플리케이션 자격 증명 업데이트 |
> | microsoft.directory/applications/notes/update | 애플리케이션의 메모 업데이트 |
> | microsoft.directory/applications/owners/update | 애플리케이션 소유자 업데이트 |
> | microsoft.directory/applications/permissions/update | 모든 유형의 애플리케이션에서 공개된 권한 및 필요한 권한 업데이트 |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/applications/tag/update | 애플리케이션의 태그 업데이트 |
> | microsoft.directory/contacts/create | 연락처 만들기 |
> | microsoft.directory/contacts/delete | 연락처 삭제 |
> | microsoft.directory/contacts/basic/update | 연락처의 기본 속성 업데이트 |
> | microsoft.directory/domains/allProperties/allTasks | 도메인 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groups/create | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups/delete | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups/restore | 삭제된 그룹 복원 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/organization/basic/update | 조직의 기본 속성 업데이트 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 역할 할당 만들기 및 삭제, 모든 역할 할당 속성 읽기 및 업데이트 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 역할 정의 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | scopedRoleMemberships 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/subscribedSkus/standard/read | 구독의 기본 속성 읽기 |
> | microsoft.directory/users/assignLicense | 사용자 라이선스 관리 |
> | microsoft.directory/users/create | 사용자 추가 |
> | microsoft.directory/users/delete | 사용자 삭제 |
> | microsoft.directory/users/disable | 사용자를 사용하지 않도록 설정 |
> | microsoft.directory/users/enable | 사용자를 사용하도록 설정 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/restore | 삭제된 사용자 복원 |
> | microsoft.directory/users/basic/update | 사용자의 기본 속성 업데이트 |
> | microsoft.directory/users/manager/update | 사용자의 관리자 업데이트 |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.directory/users/photo/update | 사용자 사진 업데이트 |
> | microsoft.directory/users/userPrincipalName/update | 사용자의 사용자 계정 이름 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="password-administrator"></a>암호 관리자

이 역할의 사용자는 제한적으로 암호를 관리할 수 있습니다. 이 역할은 서비스 요청을 관리하거나 서비스 상태를 모니터링하는 기능을 부여하지 않습니다. 암호 관리자가 사용자의 암호를 다시 설정할 수 있는지 여부는 사용자에게 할당된 역할에 따라 달라집니다. 암호 관리자가 암호를 다시 설정할 수 있는 역할 목록은 [암호 재설정 권한](#password-reset-permissions)을 참조하세요.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="power-bi-administrator"></a>Power BI 관리자

이 역할을 가진 사용자는 해당 서비스가 있는 경우 Microsoft Power BI 내에서 글로벌 사용 권한을 가질 뿐만 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [Power BI 관리자 역할 이해](/power-bi/service-admin-role)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Power BI 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "Power BI 관리자"입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Power BI의 모든 측면 관리 |

## <a name="power-platform-administrator"></a>Power Platform 관리자

이 역할의 사용자는 환경, Power Apps, 흐름, 데이터 손실 방지 정책의 모든 측면을 만들고 관리할 수 있습니다. 또한 이 역할이 할당된 사용자는 지원 티켓을 관리하고 서비스 상태를 모니터링할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.dynamics365/allEntities/allTasks | Dynamics 365의 모든 측면 관리 |
> | microsoft.flow/allEntities/allTasks | Microsoft Power Automate의 모든 측면 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.powerApps/allEntities/allTasks | Power Apps의 모든 측면 관리 |

## <a name="printer-administrator"></a>프린터 관리자

이 역할의 사용자는 Microsoft 유니버설 인쇄 솔루션에서 프린터를 등록하고 유니버설 인쇄 커넥터 설정을 비롯한 모든 프린터 구성을 관리할 수 있습니다. 모든 위임된 인쇄 권한 요청에 동의할 수 있습니다. 또한 프린터 관리자는 보고서를 인쇄할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Microsoft 인쇄에서 프린터 및 커넥터 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |

## <a name="printer-technician"></a>프린터 기술자

이 역할의 사용자는 Microsoft 유니버설 인쇄 솔루션에서 프린터를 등록하고 프린터 상태를 관리할 수 있습니다. 또한 모든 커넥터 정보를 읽을 수 있습니다. 프린터 기술자가 할 수 없는 주요 작업은 프린터 및 공유 프린터에 대한 사용자 권한을 설정하는 것입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Microsoft 인쇄에서 모든 커넥터 속성 읽기 |
> | microsoft.azure.print/printers/allProperties/read | Microsoft 인쇄에서 프린터의 모든 속성 읽기 |
> | microsoft.azure.print/printers/register | Microsoft 인쇄에서 프린터 등록 |
> | microsoft.azure.print/printers/unregister | Microsoft 인쇄에서 프린터 등록 취소 |
> | microsoft.azure.print/printers/basic/update | Microsoft 인쇄에서 프린터의 기본 속성 업데이트 |

## <a name="privileged-authentication-administrator"></a>권한 있는 인증 관리자

이 역할이 할당된 사용자는 전역 관리자를 포함하여 모든 사용자의 인증 방법(암호 포함)을 설정하거나 다시 설정할 수 있습니다. 권한 있는 인증 관리자는 사용자가 기존의 비암호 자격 증명(예: MFA 또는 FIDO)을 다시 등록하고 ‘디바이스에 MFA 저장’을 철회하도록 강제 적용한 다음, 모든 사용자가 다음에 로그인할 때 MFA를 요청할 수 있습니다.

[인증 관리자](#authentication-administrator) 역할에는 표준 사용자와 일부 관리자 역할이 있는 사용자에게 재등록 및 다단계 인증을 강제 적용할 수 있는 권한이 있습니다.

[인증 정책 관리자](#authentication-policy-administrator) 역할에는 각 사용자가 등록하고 사용할 수 있는 방법을 결정하는 테넌트 인증 방법 정책을 설정할 수 있는 권한이 있습니다.

| 역할 | 사용자의 인증 방법 관리 | 사용자별 MFA 관리 | MFA 설정 관리 | 인증 방법 정책 관리 | 암호 보호 정책 관리 |
| ---- | ---- | ---- | ---- | ---- | ---- |
| 인증 관리자 | 일부 사용자의 경우 예(위 참조) | 일부 사용자의 경우 예(위 참조) | 아니요 | 아니요 | 아니요 |
| 권한 있는 인증 관리자| 모든 사용자에 대해 예 | 모든 사용자에 대해 예 | 아니요 | 아니요 | 아니요 |
| 인증 정책 관리자 | 아니요 | 아니요 | 예 | 예 | 예 |

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 자격 증명을 변경할 수 있습니다. 사용자의 자격 증명을 변경한다는 것은 사용자의 ID 및 사용 권한을 가정할 수 있음을 의미할 수 있습니다. 다음은 그 예입니다.
>
>* 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 사용 권한이 부여되었을 수 있으며, 다른 위치에서는 인증 관리자에 권한이 부여되지 않습니다. 이 경로를 통해 인증 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>* Azure 구독 소유자: Azure에서 중요한 프라이빗 정보 또는 중요한 구성에 액세스할 수 있습니다.
>* 보안 그룹 및 Microsoft 365 그룹 소유자: 그룹 멤버 자격을 관리할 수 있습니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>* Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>* 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.


> [!IMPORTANT]
> 이 역할은 현재 레거시 MFA 관리 포털에서 사용자별 MFA를 관리할 수 없습니다. [Set-MsolUser](/powershell/module/msonline/set-msoluser) commandlet Azure AD Powershell 모듈을 사용하여 동일한 기능을 수행할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | 사용자에 대한 인증 방법 만들기 |
> | microsoft.directory/users/authenticationMethods/delete | 사용자에 대한 인증 방법 삭제 |
> | microsoft.directory/users/authenticationMethods/standard/read | 사용자에 대한 인증 방법의 표준 속성 읽기 |
> | microsoft.directory/users/authenticationMethods/basic/update | 사용자에 대한 인증 방법의 기본 속성 업데이트 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="privileged-role-administrator"></a>권한 있는 역할 관리자

이 역할을 가진 사용자는 Azure Active Directory 및 Azure AD Privileged Identity Management에서 역할 할당을 관리할 수 있습니다. Azure AD 역할에 할당 가능한 그룹을 만들고 관리할 수 있습니다. 또한 이 역할을 통해 Privileged Identity Management 및 관리 단위의 모든 것을 관리할 수 있습니다.

> [!IMPORTANT]
> 이 역할은 전역 관리자 역할을 포함하여 모든 Azure AD 역할의 할당을 관리할 수 있습니다. 이 역할은 사용자 생성 또는 업데이트와 같은 Azure AD의 다른 모든 권한 있는 기능을 포함하지는 않습니다. 그러나 이 역할에 할당된 사용자는 추가 역할을 할당하여 본인 또는 다른 사용자에게 추가 권한을 부여할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | 관리 단위(구성원 포함)를 만들고 관리합니다. |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | 권한 부여 정책의 모든 측면 관리 |
> | microsoft.directory/directoryRoles/allProperties/allTasks | 디렉터리 역할 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groupsAssignableToRoles/create | 역할 할당 가능 그룹 만들기 |
> | microsoft.directory/groupsAssignableToRoles/delete | 역할 할당 가능 그룹 삭제 |
> | microsoft.directory/groupsAssignableToRoles/restore | 역할 할당 가능 그룹 복원 |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | 역할 할당 가능 그룹 업데이트 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Privileged Identity Management에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.directory/roleAssignments/allProperties/allTasks | 역할 할당 만들기 및 삭제, 모든 역할 할당 속성 읽기 및 업데이트 |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | 역할 정의 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | scopedRoleMemberships 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/servicePrincipals/permissions/update | 서비스 주체의 권한 업데이트 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | 애플리케이션에 대한 권한 부여 동의 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="reports-reader"></a>보고서 구독자

이 역할의 사용자는 Microsoft 365 관리 센터의 사용 현황 보고 데이터 및 보고서 대시보드와 Power BI의 채택 컨텍스트 팩을 볼 수 있습니다. 또한 역할은 Azure AD의 로그온 보고서 및 활동과 함께 Microsoft Graph Reporting API에서 반환되는 데이터에 대한 액세스를 제공합니다. 보고서 구독자 역할에 할당된 사용자는 관련된 사용량 및 채택 메트릭에만 액세스할 수 있습니다. 설정을 구성하거나 Exchange와 같은 제품 특정 관리 센터에 액세스할 수 있는 관리자 권한은 없습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="search-administrator"></a>Search 관리자

이 역할의 사용자는 Microsoft 365 관리 센터의 모든 Microsoft Search 관리 기능에 액세스할 수 있습니다. 또한 이 역할의 사용자는 메시지 센터를 살펴보고, 서비스 상태를 모니터링하고, 서비스 요청을 만들 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.search/content/manage | Microsoft Search에서 콘텐츠 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="search-editor"></a>Search 편집자

이 역할의 사용자는 Microsoft 365 관리 센터에서 책갈피, 질문과 대답, 위치 등 Microsoft Search에 대한 콘텐츠를 만들고, 관리하고, 삭제할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Microsoft 365 관리 센터의 메시지 센터에서 보안 메시지를 제외한 메시지 읽기 |
> | microsoft.office365.search/content/manage | Microsoft Search에서 콘텐츠 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="security-administrator"></a>보안 관리자

이 역할의 사용자에게는 Microsoft 365 보안 센터, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection, Office 365 보안 및 준수 센터의 보안 관련 기능을 관리할 권한이 있습니다. Office 365 권한에 대한 자세한 정보는 [보안 및 규정 준수 센터의 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에 제공됩니다.

그런 다음 | 가능한 작업
--- | ---
[Microsoft 365 보안 센터](https://protection.office.com) | Microsoft 365 서비스 전반의 보안 관련 정책 모니터링<br>보안 위협과 경고 관리<br>보고서 보기
ID 보호 센터 | 보안 읽기 권한자 역할의 모든 권한<br>암호 재설정을 제외한 모든 ID 보호 센터 작업을 수행하는 기능도 있음
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 보안 읽기 권한자 역할의 모든 권한<br>Azure AD 역할 할당 또는 설정을 관리할 수 **없음**
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 보안 정책 관리<br>보안 위협 확인/조사/대응<br>보고서 보기
Azure Advanced Threat Protection | 의심스러운 보안 활동 모니터링/대응
Windows Defender ATP 및 EDR | 역할 할당<br>머신 그룹 관리<br>엔드포인트 위협 검색 및 자동 수정 구성<br>경고 확인/조사/대응
[Intune](/intune/role-based-access-control) | 사용자, 디바이스, 등록, 구성 및 애플리케이션 정보 확인<br>Intune을 변경할 수는 없음
[Cloud App Security](/cloud-app-security/manage-admins) | 관리자/정책/설정 추가, 로그 업로드, 거버넌스 작업 수행
[Microsoft 365 서비스 상태](/office365/enterprise/view-service-health) | Office 365 서비스의 상태 보기
[스마트 잠금](../authentication/howto-password-smart-lockout.md) | 실패한 로그인 이벤트가 발생할 때 잠금의 임계값 및 기간을 정의합니다.
[암호 보호](../authentication/concept-password-ban-bad.md) | 사용자 지정 금지 암호 목록 또는 온-프레미스 암호 보호를 구성합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/applications/policies/update | 애플리케이션 정책 업데이트 |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD 권한 관리에서 모든 속성 읽기 |
> | microsoft.directory/identityProtection/allProperties/read | Azure AD Identity Protection에서 모든 리소스 읽기 |
> | microsoft.directory/identityProtection/allProperties/update | Azure AD Identity Protection에서 모든 리소스 업데이트 |
> | microsoft.directory/policies/create | Azure AD에서 정책 만들기 |
> | microsoft.directory/policies/delete | Azure AD에서 정책 삭제 |
> | microsoft.directory/policies/basic/update | 정책의 기본 속성 업데이트 |
> | microsoft.directory/policies/owners/update | 정책의 소유자 업데이트 |
> | microsoft.directory/policies/tenantDefault/update | 기본 조직 정책 업데이트 |
> | microsoft.directory/conditionalAccessPolicies/create | 조건부 액세스 정책 만들기 |
> | microsoft.directory/conditionalAccessPolicies/delete | 조건부 액세스 정책 삭제 |
> | microsoft.directory/conditionalAccessPolicies/standard/read | 정책에 대한 조건부 액세스 읽기 |
> | microsoft.directory/conditionalAccessPolicies/owners/read | 조건부 액세스 정책 소유자 읽기 |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | 조건부 액세스 정책에 대한 "적용 대상" 속성 읽기 |
> | microsoft.directory/conditionalAccessPolicies/basic/update | 조건부 액세스 정책의 기본 속성 업데이트 |
> | microsoft.directory/conditionalAccessPolicies/owners/update | 조건부 액세스 정책에 대한 소유자 업데이트 |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | 조건부 액세스 정책에 대한 기본 테넌트 업데이트 |
> | microsoft.directory/crossTenantAccessPolicies/create | 테넌트 간 액세스 정책 만들기 |
> | microsoft.directory/crossTenantAccessPolicies/delete | 테넌트 간 액세스 정책 삭제 |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | 테넌트 간 액세스 정책의 기본 속성 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | 테넌트 간 액세스 정책의 소유자 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | 테넌트 간 액세스 정책의 policyAppliedTo 속성 읽기 |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | 테넌트 간 액세스 정책의 기본 속성 업데이트 |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | 테넌트 간 액세스 정책의 소유자 업데이트 |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | 테넌트 간 액세스 정책의 기본 테넌트 업데이트 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management에서 모든 리소스 읽기 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/servicePrincipals/policies/update | 서비스 주체의 정책 업데이트 |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.protectionCenter/allEntities/standard/read | 보안 및 규정 준수 센터에서 모든 리소스의 표준 속성 읽기 |
> | microsoft.office365.protectionCenter/allEntities/basic/update | 보안 및 규정 준수 센터에서 모든 리소스의 기본 속성 업데이트 |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | 공격 시뮬레이터에서 공격 페이로드 만들기 및 관리 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 공격 시뮬레이션, 응답 및 관련 학습에 대한 보고서 읽기 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | 공격 시뮬레이터에서 공격 시뮬레이션 템플릿 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="security-operator"></a>보안 운영자

이 역할의 사용자는 경고를 관리할 수 있으며 Microsoft 365 보안 센터, Azure Active Directory, Identity Protection, Privileged Identity Management, Office 365 보안 및 준수 센터의 모든 정보를 포함하여 보안 관련 기능에 대한 글로벌 읽기 전용 액세스 권한이 있습니다. Office 365 권한에 대한 자세한 정보는 [보안 및 규정 준수 센터의 권한](/office365/securitycompliance/permissions-in-the-security-and-compliance-center)에 제공됩니다.

| 그런 다음 | 가능한 작업 |
| --- | --- |
| [Microsoft 365 보안 센터](https://protection.office.com) | 보안 읽기 권한자 역할의 모든 권한<br/>보안 위협 경고 확인/조사/대응<br/>보안 센터의 보안 설정 관리 |
| [Azure AD ID 보호](../identity-protection/overview-identity-protection.md) | 보안 읽기 권한자 역할의 모든 권한<br>암호 재설정 및 경고 이메일 구성을 제외한 모든 ID 보호 센터 작업도 수행 가능 |
| [Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 보안 읽기 권한자 역할의 모든 권한 |
| [Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 보안 읽기 권한자 역할의 모든 권한<br>보안 경고 확인/조사/대응 |
| Windows Defender ATP 및 EDR | 보안 읽기 권한자 역할의 모든 권한<br>보안 경고 확인/조사/대응 |
| [Intune](/intune/role-based-access-control) | 보안 읽기 권한자 역할의 모든 권한 |
| [Cloud App Security](/cloud-app-security/manage-admins) | 보안 읽기 권한자 역할의 모든 권한 |
| [Microsoft 365 서비스 상태](/microsoft-365/enterprise/view-service-health) | Office 365 서비스의 상태 보기 |

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Microsoft Cloud App Security에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.directory/identityProtection/allProperties/allTasks | Azure AD Identity Protection에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management에서 모든 리소스 읽기 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Azure Advanced Threat Protection의 모든 측면 관리 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.intune/allEntities/read | Microsoft Intune에서 모든 리소스 읽기 |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Microsoft 365 보안 및 규정 준수 센터에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | 엔드포인트용 Microsoft Defender의 모든 측면 관리 |

## <a name="security-reader"></a>보안 Reader

이 역할의 사용자에게는 Microsoft 365 보안 센터, Azure Active Directory, ID 보호, Privileged Identity Management의 모든 정보를 비롯한 보안 관련 기능에 대한 전역 읽기 전용 액세스 권한이 있습니다. 또한 Azure Active Directory 로그인 보고서와 감사 로그 읽기 권한 및 Office 365 보안 및 준수 센터의 읽기 권한도 있습니다. Office 365 권한에 대한 자세한 정보는 [보안 및 규정 준수 센터의 권한](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)에 제공됩니다.

그런 다음 | 가능한 작업
--- | ---
[Microsoft 365 보안 센터](https://protection.office.com) | Microsoft 365 서비스 전반에서 보안 관련 정책 확인<br>보안 위협 및 경고 확인<br>보고서 보기
ID 보호 센터 | 모든 보안 보고서 및 보안 기능에 대한 설정 정보를 읽습니다.<br><ul><li>스팸 방지<li>암호화<li>데이터 손실 방지<li>맬웨어 방지<li>Advanced Threat Protection<li>피싱 방지<li>메일 흐름 규칙
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Azure AD Privileged Identity Management에 표시되는 다음과 같은 모든 정보에 대한 읽기 전용 액세스 권한이 있습니다. Azure AD 역할 할당 및 보안 검토에 대한 정책 및 보고서.<br>Azure AD Privileged Identity Management에 가입하거나 변경할 수 **없습니다**. 이 역할에 속한 사람은 Privileged Identity Management 포털 또는 PowerShell에서 적격 사용자를 대상으로 전역 관리자, 권한 있는 역할 관리자 등의 추가 역할을 활성화할 수 있습니다.
[Office 365 보안 및 준수 센터](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | 보안 정책 보기<br>보안 위협 확인/조사<br>보고서 보기
Windows Defender ATP 및 EDR | 경고 확인/조사 Windows Defender ATP에서 역할 기반 액세스 제어를 켜면 Azure AD 보안 읽기 권한자 역할과 같은 읽기 전용 권한이 있는 사용자는 Windows Defender ATP 역할에 할당될 때까지 액세스 권한을 잃게 됩니다.
[Intune](/intune/role-based-access-control) | 사용자, 디바이스, 등록, 구성 및 애플리케이션 정보 확인. Intune을 변경할 수는 없음
[Cloud App Security](/cloud-app-security/manage-admins) | 읽기 권한 소유 및 경고를 관리할 수 있음
[Microsoft 365 서비스 상태](/office365/enterprise/view-service-health) | Office 365 서비스의 상태 보기

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | 권한 있는 속성을 포함하여 감사 로그의 모든 속성 읽기 |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/bitlockerKeys/key/read | 디바이스의 bitlocker 메타데이터 및 키 읽기 |
> | microsoft.directory/entitlementManagement/allProperties/read | Azure AD 권한 관리에서 모든 속성 읽기 |
> | microsoft.directory/identityProtection/allProperties/read | Azure AD Identity Protection에서 모든 리소스 읽기 |
> | microsoft.directory/policies/standard/read | 정책의 기본 속성 읽기 |
> | microsoft.directory/policies/owners/read | 정책의 소유자 읽기 |
> | microsoft.directory/policies/policyAppliedTo/read | policies.policyAppliedTo 속성 읽기 |
> | microsoft.directory/conditionalAccessPolicies/standard/read | 정책에 대한 조건부 액세스 읽기 |
> | microsoft.directory/conditionalAccessPolicies/owners/read | 조건부 액세스 정책 소유자 읽기 |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | 조건부 액세스 정책에 대한 "적용 대상" 속성 읽기 |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Privileged Identity Management에서 모든 리소스 읽기 |
> | microsoft.directory/provisioningLogs/allProperties/read | 프로비저닝 로그의 모든 속성을 읽습니다. |
> | microsoft.directory/signInReports/allProperties/read | 권한 있는 속성을 포함하여 로그인 보고서의 모든 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.office365.protectionCenter/allEntities/standard/read | 보안 및 규정 준수 센터에서 모든 리소스의 표준 속성 읽기 |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | 공격 시뮬레이터에서 공격 페이로드의 모든 속성 읽기 |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | 공격 시뮬레이션, 응답 및 관련 학습에 대한 보고서 읽기 |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | 공격 시뮬레이터에서 공격 시뮬레이션 템플릿의 모든 속성 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="service-support-administrator"></a>서비스 지원 관리자

이 역할의 사용자는 Microsoft for Azure 및 Microsoft 365 서비스에서 지원 요청을 열 수 있으며, [Azure Portal](https://portal.azure.com) 및 [Microsoft 365 관리 센터](https://admin.microsoft.com)에서 서비스 대시보드 및 메시지 센터를 볼 수 있습니다. 자세한 내용은 [관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

> [!NOTE]
> 이전에는 [Azure Portal](https://portal.azure.com) 및 [Microsoft 365 관리 센터](https://admin.microsoft.com)에서 이 역할을 "서비스 관리자"라고 했습니다. Microsoft Graph API, Azure AD Graph API 및 Azure AD PowerShell의 기존 이름과 맞추기 위해 "서비스 지원 관리자"로 변경했습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="sharepoint-administrator"></a>SharePoint 관리자

이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft SharePoint Online 내에서 글로벌 사용 권한을 가질 뿐만 아니라 모든 Microsoft 365 그룹을 생성 및 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링할 수 있습니다. 자세한 내용은 [관리자 역할 정보](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)를 참조하세요.

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "SharePoint 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com)에서 이 역할은 "SharePoint 관리자"입니다.

> [!NOTE]
> 또한 이 역할은 Microsoft Graph API for Microsoft Intune에 범위가 지정된 권한을 부여하여 SharePoint 및 OneDrive 리소스와 관련된 정책을 관리하고 구성할 수 있게 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/groups.unified/create | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups.unified/delete | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups.unified/restore | Microsoft 365 그룹 복원 |
> | microsoft.directory/groups.unified/basic/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.unified/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified/owners/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.sharePoint/allEntities/allTasks | SharePoint에서 모든 리소스 만들기 및 삭제, 표준 속성 읽기 및 업데이트 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="skype-for-business-administrator"></a>비즈니스용 Skype 관리자

이 역할의 사용자는 해당 서비스가 있는 경우 Microsoft 비즈니스용 Skype 내에서 글로벌 사용 권한을 가질 뿐만 아니라 Azure Active Directory에서 Skype 관련 사용자 특성을 관리할 수 있습니다. 또한 이 역할은 지원 티켓을 관리하고 서비스 상태를 모니터링하고 Teams 및 Business용 Skype 관리 센터에 액세스하는 기능을 부여합니다. 계정에는 Teams에 대한 라이선스가 있어야 합니다. 그렇지 않으면 Teams PowerShell cmdlet을 실행할 수 없습니다. [비즈니스용 Skype 관리자 역할 정보](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)의 자세한 내용 및 [비즈니스용 Skype 및 Microsoft Teams 추가 기능 라이선스](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)의 Teams 라이선스 정보

> [!NOTE]
> Microsoft Graph API 및 Azure AD PowerShell에서 이 역할은 "Lync 서비스 관리자"로 식별됩니다. [Azure Portal](https://portal.azure.com/)에서는 “비즈니스용 Skype 관리자”입니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="teams-administrator"></a>Teams 관리자

이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터와 해당하는 PowerShell 모듈을 통해 Microsoft Teams 워크로드의 모든 측면을 관리할 수 있습니다. 여기에는 다른 영역 중 전화 통신, 메시징, 회의 및 팀 자체와 관련된 모든 관리 도구가 포함됩니다. 이 역할은 추가적으로 모든 Microsoft 365 그룹 만들기 및 관리 기능뿐 아니라 지원 티켓을 관리하고 서비스 상태를 모니터링하는 기능도 부여합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/groups/hiddenMembers/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 숨겨진 구성원 읽기 |
> | microsoft.directory/groups.unified/create | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups.unified/delete | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups.unified/restore | Microsoft 365 그룹 복원 |
> | microsoft.directory/groups.unified/basic/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.unified/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified/owners/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | 서비스 주체에게 그룹 데이터에 대한 직접 액세스 권한 부여 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.teams/allEntities/allProperties/allTasks | Teams에서 모든 리소스 관리 |

## <a name="teams-communications-administrator"></a>Teams 통신 관리자

이 역할의 사용자는 음성 및 전화 통신과 관련된 Microsoft Teams 워크로드의 측면을 관리할 수 있습니다. 여기에는 전화 번호 할당, 음성 및 회의 정책 및 호출 분석 도구 집합에 대한 전체 액세스를 위한 관리 도구가 포함됩니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.teams/callQuality/allProperties/read | CQD(통화 품질 대시보드)에서 모든 데이터 읽기 |
> | microsoft.teams/meetings/allProperties/allTasks | 모임 정책, 구성 및 회의 브리지를 포함한 모임 관리 |
> | microsoft.teams/voice/allProperties/allTasks | 통화 정책을 비롯한 음성, 전화번호 인벤토리 및 할당 관리 |

## <a name="teams-communications-support-engineer"></a>Teams 통신 지원 엔지니어

이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터에서 사용자 호출 문제 해결 도구를 사용하여 Microsoft Teams 및 비즈니스용 Skype 내에서 통신 문제를 해결할 수 있습니다. 이 역할의 사용자는 관련된 모든 참가자에 대한 전체 호출 레코드 정보를 볼 수 있습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.teams/callQuality/allProperties/read | CQD(통화 품질 대시보드)에서 모든 데이터 읽기 |

## <a name="teams-communications-support-specialist"></a>Teams 통신 지원 전문가

이 역할의 사용자는 Microsoft Teams 및 비즈니스용 Skype 관리 센터에서 사용자 호출 문제 해결 도구를 사용하여 Microsoft Teams 및 비즈니스용 Skype 내에서 통신 문제를 해결할 수 있습니다. 이 역할의 사용자는 조회하는 특정 사용자에 대한 호출에서 사용자 세부 정보를 보기만 할 수 있습니다. 이 역할에는 지원 티켓 보기, 생성 또는 관리 권한은 없습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | 비즈니스용 Skype Online의 모든 측면 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.teams/callQuality/standard/read | CQD(통화 품질 대시보드)에서 기본 데이터 읽기 |

## <a name="teams-devices-administrator"></a>Teams 디바이스 관리자

이 역할이 할당된 사용자는 Teams 관리 센터에서 [Teams 인증 디바이스](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices)를 관리할 수 있습니다. 이 역할을 통해 디바이스를 한 눈에 보고 디바이스를 검색 및 필터링할 수 있습니다. 사용자는 로그인된 계정, 디바이스 제조업체 및 모델을 포함하여 각 디바이스의 세부 정보를 확인할 수 있습니다. 사용자는 디바이스에서 설정을 변경하고 소프트웨어 버전을 업데이트할 수 있습니다. 이 역할은 Teams 활동 및 디바이스의 통화 품질을 확인할 수 있는 권한을 부여하지 않습니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |
> | microsoft.teams/devices/standard/read | 구성 정책을 비롯한 Teams 인증 디바이스의 모든 측면 관리 |

## <a name="usage-summary-reports-reader"></a>사용 요약 보고서 읽기 권한자

이 역할이 할당된 사용자는 사용 및 생산성 점수에 대한 Microsoft 365 관리 센터에서 테넌트 수준 집계 데이터와 관련 인사이트에 액세스할 수 있지만 사용자 수준 세부 정보나 인사이트에는 액세스할 수 없습니다. 두 보고서에 대한 Microsoft 365 관리 센터에서는 테넌트 수준 집계 데이터와 사용자 수준 세부 정보가 구분되어 있습니다. 이 역할은 고객과 법률 팀의 요청에 따라 개별 사용자 식별 가능 데이터에 대한 추가 보호 계층을 제공합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Microsoft 365 관리 센터에서 모든 네트워크 성능 속성 읽기 |
> | microsoft.office365.usageReports/allEntities/standard/read | 테넌트 수준에서 집계된 Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="user-administrator"></a>사용자 관리자

이 역할의 사용자는 사용자를 만들고, 사용자의 모든 것을 제한적으로(아래 표 참조) 관리하고, 암호 만료 정책을 업데이트할 수 있습니다. 또한 이 역할의 사용자는 모든 그룹을 만들고 관리할 수 있습니다. 이 역할은 사용자 보기를 만들고 관리하며, 지원 티켓을 관리하고, 서비스 상태를 모니터링하는 기능도 포함합니다. 사용자 관리자에게는 대부분의 관리자 역할에 속한 사용자의 일부 사용자 속성을 관리할 수 있는 권한이 없습니다. 이 역할의 관리자에게는 MFA를 관리하거나 공유 사서함을 관리할 수 있는 권한이 없습니다. 다음 표에는 이 제한이 적용되지 않는 예외적인 역할이 나열되어 있습니다.

| 사용자 관리자 권한 | 메모 |
| --- | --- |
| 사용자 및 그룹 만들기<br/>사용자 보기 만들기 및 관리<br/>Office 지원 티켓 관리<br/>암호 만료 정책 업데이트 |  |
| 라이선스 관리<br/>사용자 계정 이름을 제외한 모든 사용자 속성 관리 | 모든 관리자를 포함한 모든 사용자에게 적용 |
| 삭제 및 복원<br/>사용 안 함 및 사용<br/>사용자 계정 이름을 포함한 모든 사용자 속성 관리<br/>(FIDO) 디바이스 키 업데이트 | 관리자가 아닌 사용자 또는 다음 역할이 할당된 사용자에게 적용:<ul><li>기술 지원팀 관리자</li><li>역할이 없는 사용자</li><li>사용자 관리자</li></ul> |
| 새로 고침 토큰 무효화<br/>암호 재설정 | 사용자 관리자가 암호를 다시 설정하고 새로 고침 토큰을 무효화할 수 있는 역할 목록은 [암호 재설정 권한](#password-reset-permissions)을 참조하세요. |

> [!IMPORTANT]
> 이 역할의 사용자는 Azure Active Directory 내부 및 외부에 있는 중요한 프라이빗 정보 또는 중요한 구성에 대한 액세스 권한이 있을 수 있는 사용자의 암호를 변경할 수 있습니다. 사용자의 암호를 변경한다는 것은 사용자의 ID 및 권한을 가정할 수 있다는 것을 의미합니다. 다음은 그 예입니다.
>
>- 애플리케이션 등록 및 엔터프라이즈 애플리케이션 소유자: 소유한 앱의 자격 증명을 관리할 수 있습니다. 이러한 앱은 Azure AD에서 권한이 부여되었을 수 있으며, 다른 위치에서는 사용자 관리자에게 권한이 부여되지 않습니다. 이 경로를 통해 사용자 관리자는 애플리케이션 소유자의 ID를 가정하고, 애플리케이션의 자격 증명을 업데이트하여 권한 있는 애플리케이션의 ID를 추가로 가정할 수 있습니다.
>- Azure 구독 소유자: Azure에서 중요한 프라이빗 정보 또는 중요한 구성에 액세스할 수 있습니다.
>- 보안 그룹 및 Microsoft 365 그룹 소유자: 그룹 멤버 자격을 관리할 수 있습니다. 해당 그룹은 중요한 프라이빗 정보 또는 Azure AD 및 다른 위치의 중요한 구성에 대한 액세스 권한을 부여할 수 있습니다.
>- Exchange Online, Office 보안 및 준수 센터, 인사 관리 시스템과 같은 Azure AD 외부의 다른 서비스에 있는 관리자
>- 중요한 프라이빗 정보에 액세스할 수 있는 임원, 법률 고문 및 인사 관리 직원과 같은 비관리자.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/contacts/create | 연락처 만들기 |
> | microsoft.directory/contacts/delete | 연락처 삭제 |
> | microsoft.directory/contacts/basic/update | 연락처의 기본 속성 업데이트 |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Azure AD 권한 관리에서 리소스 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/groups/assignLicense | 그룹 기반 라이선스 그룹에 제품 라이선스 할당 |
> | microsoft.directory/groups/create | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups/delete | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups/hiddenMembers/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 숨겨진 구성원 읽기 |
> | microsoft.directory/groups/reprocessLicenseAssignment | 그룹 기반 라이선스의 라이선스 할당 다시 처리 |
> | microsoft.directory/groups/restore | 삭제된 그룹 복원 |
> | microsoft.directory/groups/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 동적 멤버 관리 규칙 업데이트 |
> | microsoft.directory/groups/groupType/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 그룹 유형에 영향을 주는 속성 업데이트 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups/onPremWriteBack/update | Azure AD Connect를 사용하여 온-프레미스에 다시 쓸 Azure Active Directory 그룹 업데이트 |
> | microsoft.directory/groups/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 소유자 업데이트 |
> | microsoft.directory/groups/settings/update | 그룹 설정 읽기 |
> | microsoft.directory/groups/visibility/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 가시성 속성 업데이트 |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | OAuth 2.0 권한 부여 만들기 및 삭제, 모든 속성 읽기 및 업데이트 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 서비스 주체 역할 할당 업데이트 |
> | microsoft.directory/users/assignLicense | 사용자 라이선스 관리 |
> | microsoft.directory/users/create | 사용자 추가 |
> | microsoft.directory/users/delete | 사용자 삭제 |
> | microsoft.directory/users/disable | 사용자를 사용하지 않도록 설정 |
> | microsoft.directory/users/enable | 사용자를 사용하도록 설정 |
> | microsoft.directory/users/inviteGuest | 게스트 사용자 초대 |
> | microsoft.directory/users/invalidateAllRefreshTokens | 사용자 새로 고침 토큰을 무효화하여 강제로 로그아웃 |
> | microsoft.directory/users/reprocessLicenseAssignment | 사용자에 대한 라이선스 할당 다시 처리 |
> | microsoft.directory/users/restore | 삭제된 사용자 복원 |
> | microsoft.directory/users/basic/update | 사용자의 기본 속성 업데이트 |
> | microsoft.directory/users/manager/update | 사용자의 관리자 업데이트 |
> | microsoft.directory/users/password/update | 모든 사용자 암호 재설정 |
> | microsoft.directory/users/photo/update | 사용자 사진 업데이트 |
> | microsoft.directory/users/userPrincipalName/update | 사용자의 사용자 계정 이름 업데이트 |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health 읽기 및 구성 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Microsoft 365 관리 센터에서 Service Health 읽기 및 구성 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="windows-365-administrator"></a>Windows 365 관리자

서비스가 있는 경우 이 역할의 사용자에게는 Windows 365 리소스에 대한 전역 권한이 있습니다. 또한 이 역할은 정책을 연결하고 그룹을 만들고 관리하기 위해 사용자와 디바이스를 관리하는 기능을 포함합니다.

이 역할은 보안 그룹을 만들고 관리할 수 있지만, Microsoft 365 그룹에 대한 관리자 권한이 없습니다. 즉, 관리자는 조직에서 Microsoft 365 그룹의 소유자 또는 멤버 자격을 업데이트할 수 없습니다. 그러나 관리자는 자신이 만드는 Microsoft 365 그룹을 관리할 수 있습니다. 이는 최종 사용자 권한의 일부입니다. 따라서 관리자가 만드는 Microsoft 365 그룹(보안 그룹이 아님)은 250개의 할당량에 대해 계산됩니다.

다음 작업을 수행해야 하는 사용자에게 Windows 365 관리자 역할을 할당합니다.

- Microsoft Endpoint Manager에서 Windows 365 클라우드 PC 관리
- 사용자 및 정책 할당을 포함하여 Azure AD에서 디바이스 등록 및 관리
- 역할을 할당할 수 있는 그룹이 아닌 보안 그룹 만들기 및 관리
- Microsoft 365 관리 센터에서 기본 속성 보기
- Microsoft 365 관리 센터에서 사용 현황 보고서 읽기
- Azure AD 및 Microsoft 365 관리 센터에서 지원 티켓 만들기 및 관리

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.directory/devices/create | 디바이스 만들기(Azure AD에 등록) |
> | microsoft.directory/devices/delete | Azure AD에서 디바이스 삭제 |
> | microsoft.directory/devices/disable | Azure AD에서 디바이스를 사용하지 않도록 설정 |
> | microsoft.directory/devices/enable | Azure AD에서 디바이스를 사용하도록 설정 |
> | microsoft.directory/devices/basic/update | 디바이스의 기본 속성 업데이트 |
> | microsoft.directory/devices/extensionAttributeSet1/update | 디바이스에서 extensionAttribute1을 extensionAttribute5 속성으로 업데이트 |
> | microsoft.directory/devices/extensionAttributeSet2/update | 디바이스에서 extensionAttribute6을 extensionAttribute10 속성으로 업데이트 |
> | microsoft.directory/devices/extensionAttributeSet3/update | 디바이스에서 extensionAttribute11을 extensionAttribute15 속성으로 업데이트 |
> | microsoft.directory/devices/registeredOwners/update | 등록된 디바이스 소유자 업데이트 |
> | microsoft.directory/devices/registeredUsers/update | 등록된 디바이스 사용자 업데이트 |
> | microsoft.directory/groups.security/create | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 |
> | microsoft.directory/groups.security/delete | 역할 할당 가능 그룹을 제외한 보안 그룹 삭제 |
> | microsoft.directory/groups.security/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.security/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | 역할 할당 가능 그룹을 제외하고 보안 그룹의 동적 멤버 자격 규칙 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security/owners/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 소유자 업데이트 |
> | microsoft.directory/groups.security/visibility/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 가시성 속성 업데이트 |
> | microsoft.directory/deviceManagementPolicies/standard/read | 디바이스 관리 애플리케이션 정책의 표준 속성 읽기 |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | 디바이스 등록 정책의 표준 속성 읽기 |
> | microsoft.azure.supportTickets/allEntities/allTasks | Azure 지원 티켓 만들기 및 관리 |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Windows 365의 모든 측면 관리 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Microsoft 365 서비스 요청 만들기 및 관리 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Office 365 사용량 보고서 읽기 |
> | microsoft.office365.webPortal/allEntities/standard/read | Microsoft 365 관리 센터에서 모든 리소스의 기본 속성 읽기 |

## <a name="windows-update-deployment-administrator"></a>Windows 업데이트 배포 관리자

이 역할의 사용자는 비즈니스용 Windows 업데이트 배포 서비스를 통해 Windows 업데이트 배포의 모든 측면을 만들고 관리할 수 있습니다. 배포 서비스를 사용하면 사용자가 업데이트 배포 시기와 방법에 대한 설정을 정의하고 테넌트에서 디바이스 그룹에 제공되는 업데이트를 지정할 수 있습니다. 사용자가 업데이트 진행 상황도 모니터링할 수 있도록 합니다.

> [!div class="mx-tableFixed"]
> | 동작 | 설명 |
> | --- | --- |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | Windows Update 서비스의 모든 측면 읽기 및 구성 |

## <a name="how-to-understand-role-permissions"></a>역할 권한을 이해하는 방법

권한에 대한 스키마는 Microsoft Graph의 REST 형식을 느슨하게 따릅니다.

`<namespace>/<entity>/<propertySet>/<action>`

예를 들면 다음과 같습니다.

`microsoft.directory/applications/credentials/update`

| 권한 요소 | Description |
| --- | --- |
| namespace | 작업을 노출하고 앞에 `microsoft`가 붙는 제품 또는 서비스입니다. 예를 들어 Azure AD의 모든 작업은 `microsoft.directory` 네임스페이스를 사용합니다. |
| 엔터티 | Microsoft Graph의 서비스에서 노출하는 논리적 기능 또는 구성 요소입니다. 예를 들어 Azure AD는 사용자 및 그룹을 노출하고, OneNote는 노트를 노출하고, Exchange는 사서함과 달력을 노출합니다. 네임스페이스의 모든 엔터티를 지정하는 특수 키워드 `allEntities`가 있습니다. 이 키워드는 전체 제품에 대한 액세스 권한을 부여하는 역할에 자주 사용됩니다. |
| propertySet | 액세스 권한이 부여되는 엔터티의 속성 또는 요소입니다. 예를 들어 `microsoft.directory/applications/authentication/read`는 Azure AD의 애플리케이션 개체에서 회신 URL, 로그아웃 URL 및 암시적 흐름 속성을 읽을 수 있는 기능을 부여합니다.<ul><li>`allProperties`는 권한 있는 속성을 포함하여 엔터티의 모든 속성을 지정합니다.</li><li>`standard`는 공통 속성을 지정하지만 `read` 작업과 관련된 권한 있는 속성을 제외합니다. 예를 들어 `microsoft.directory/user/standard/read`는 퍼블릭 전화 번호 및 이메일 주소와 같은 표준 속성을 읽을 수 있지만, 다단계 인증에 사용되는 프라이빗 보조 전화 번호 또는 이메일 주소는 읽을 수 없습니다.</li><li>`basic`은 공통 속성을 지정하지만 `update` 작업과 관련된 권한 있는 속성을 제외합니다. 읽을 수 있는 속성 세트는 업데이트할 수 있는 속성과 다를 수 있습니다. 이를 반영하기 위해 `standard` 및 `basic` 키워드가 있습니다.</li></ul> |
| action | 부여되는 작업. 대부분 만들기, 읽기, 업데이트 또는 삭제(CRUD) 작업입니다. 위의 모든 기능(만들기, 읽기, 업데이트 및 삭제)을 지정하는 특수 키워드 `allTasks`가 있습니다. |

## <a name="deprecated-roles"></a>사용되지 않는 역할

다음 역할은 사용할 수 없습니다. 해당 역할은 사용되지 않으며 향후 Azure AD에서 제거됩니다.

* 임시 라이선스 관리자
* 디바이스 연결
* 디바이스 관리
* 디바이스 사용자
* 전자 메일 확인 사용자 생성자
* 사서함 관리자
* 작업 공간 디바이스 연결

## <a name="roles-not-shown-in-the-portal"></a>포털에 표시되지 않는 역할

PowerShell 또는 MS Graph API에서 반환된 모든 역할이 Azure Portal에 표시되는 것은 아닙니다. 다음 표에는 이러한 차이점이 정리되어 있습니다.

API 이름 | Azure Portal 이름 | 메모
-------- | ------------------- | -------------
디바이스 연결 | 사용되지 않음 | [사용되지 않는 역할 설명서](#deprecated-roles)
디바이스 관리 | 사용되지 않음 | [사용되지 않는 역할 설명서](#deprecated-roles)
디바이스 사용자 | 사용되지 않음 | [사용되지 않는 역할 설명서](#deprecated-roles)
디렉터리 동기화 계정 | 사용하면 안되므로 표시되지 않습니다. | [디렉터리 동기화 계정 설명서](#directory-synchronization-accounts)
게스트 사용자 | 사용하면 안되므로 표시되지 않습니다. | 해당 없음
파트너 계층 1 지원 | 사용하면 안되므로 표시되지 않습니다. | [파트너 계층1 지원 설명서](#partner-tier1-support)
파트너 계층 2 지원 | 사용하면 안되므로 표시되지 않습니다. | [파트너 계층2 지원 설명서](#partner-tier2-support)
제한된 게스트 사용자 | 사용하면 안되므로 표시되지 않습니다. | 해당 없음
사용자 | 사용하면 안되므로 표시되지 않습니다. | 해당 없음
작업 공간 디바이스 연결 | 사용되지 않음 | [사용되지 않는 역할 설명서](#deprecated-roles)

## <a name="password-reset-permissions"></a>암호 재설정 권한

열 머리글은 암호를 다시 설정할 수 있는 역할을 나타냅니다. 테이블 행에는 암호를 다시 설정할 수 있는 역할이 포함됩니다.

암호 재설정 가능 | 암호 관리자 | 기술 지원팀 관리자 | 인증 관리자 | 사용자 관리 | 권한 있는 인증 관리자 | 전역 관리자
------ | ------ | ------ | ------ | ------ | ------ | ------
인증 관리자 | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
디렉터리 읽기 권한자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
전역 관리자 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
그룹 관리자 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
게스트 초대자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
기술 지원팀 관리자 | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
메시지 센터 읽기 권한자 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
암호 관리자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
권한 있는 인증 관리자 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
권한 있는 역할 관리자 | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
보고서 구독자 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
사용자<br/>(관리자 역할 없음) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
사용자<br/>(관리자 역할은 없지만 역할 할당 가능 그룹의 구성원) | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
사용자 관리 | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
사용 요약 보고서 읽기 권한자 | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* 전역 관리자는 자신의 전역 관리자 할당을 제거할 수 없습니다. 조직의 전역 관리자가 한 명도 없는 상황을 방지하기 위한 조치입니다.

## <a name="next-steps"></a>다음 단계

- [그룹에 Azure AD 역할 할당](groups-assign-role.md)
- [여러 역할의 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure 구독의 관리자로 사용자 할당](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
