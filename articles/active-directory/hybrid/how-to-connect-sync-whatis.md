---
title: 'Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정 | Microsoft Docs'
description: Azure AD Connect 동기화 작업 및 사용자 지정 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89662379"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정
Azure Active Directory Connect 동기화 서비스(Azure AD Connect 동기화)는 Azure AD Connect의 주요 구성 요소입니다. 온-프레미스 환경과 Azure AD 간의 ID 데이터를 동기화하는 데 관련된 모든 작업을 담당합니다. Azure AD Connect 동기화는 DirSync, Azure AD Sync 및 Azure Active Directory Connector가 구성된 Forefront Identity Manager의 후속 제품입니다.

이 항목은 **Azure AD Connect 동기화**(**동기화 엔진** 이라고도 함)를 다루며 관련된 모든 다른 항목에 대한 링크를 나열합니다. Azure AD Connect에 대한 링크는 [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)을 참조하세요.

동기화 서비스는 두 개의 구성 요소, 온-프레미스 **Azure AD Connect 동기화** 및 **Azure AD Connect 동기화 서비스** 라는 Azure AD의 서비스 쪽 구성 요소로 구성됩니다.

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect 동기화 항목
| 항목 | 포함된 내용 및 적용 시기 |
| --- | --- |
| **Azure AD Connect 동기화 기본 사항** | |
| [아키텍처 이해](concept-azure-ad-connect-sync-architecture.md) |동기화 엔진을 처음 접하고 아키텍처와 사용되는 용어에 대해 알고자 하는 사용자를 대상으로 합니다. |
| [기술 개념](how-to-connect-sync-technical-concepts.md) |간단한 버전의 아키텍처 항목으로 사용되는 용어를 간략히 설명합니다. |
| [Azure AD Connect에 대한 토폴로지](plan-connect-topologies.md) |다양한 토폴로지와 동기화 엔진에서 지원하는 시나리오에 대해 설명합니다. |
| **사용자 지정 구성** | |
| [설치 마법사 다시 실행](how-to-connect-installation-wizard.md) |Azure AD Connect 설치 마법사를 다시 실행하는 경우 사용할 수 있는 옵션을 설명합니다. |
| [선언적 프로비전 이해](concept-azure-ad-connect-sync-declarative-provisioning.md) |선언적 프로비전이라는 구성 모델을 설명합니다. |
| [선언적 프로비전 식 이해](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |선언적 프로비전에 사용되는 식 언어에 대한 구문을 설명합니다. |
| [기본 구성 이해](concept-azure-ad-connect-sync-default-configuration.md) |기본 규칙 및 기본 구성에 대해 설명합니다. 또한 기본 시나리오에 대해 규칙이 어떻게 작동하는지도 설명합니다. |
| [사용자 및 연락처 이해](concept-azure-ad-connect-sync-user-and-contacts.md) |이전 항목에 연결된 내용으로 사용자 및 연락처에 대한 구성이 특히 다중 포리스트 환경에서 어떻게 함께 작동하는지 설명합니다. |
| [기본 구성으로 변경하는 방법](how-to-connect-sync-change-the-configuration.md) |특성 흐름에 대한 일반적인 구성을 변경하는 방법을 안내합니다. |
| [기본 구성 변경에 대한 모범 사례](how-to-connect-sync-best-practices-changing-default-configuration.md) |기본 구성을 변경하기 위한 제한 사항을 지원합니다. |
| [필터링 구성](how-to-connect-sync-configure-filtering.md) |어떤 개체를 Azure AD에 동기화할지 제한하는 방법에 대한 다양한 옵션을 설명하고 이 옵션을 구성하는 방법을 단계별로 설명합니다. |
| **기능 및 시나리오** | |
| [실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md) |*실수로 인한 삭제 방지* 기능과 이를 구성하는 방법을 설명합니다. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |데이터를 가져오고 동기화하고 내보내는 기본 제공 스케줄러를 설명합니다. |
| [암호 해시 동기화 구현](how-to-connect-password-hash-synchronization.md) |암호 동기화의 작동 방식, 구현 방법, 작동 및 문제 해결 방법에 대해 설명합니다. |
| [디바이스 쓰기 저장](how-to-connect-device-writeback.md) |Azure AD Connect에서 디바이스 쓰기 저장이 작동하는 방식에 대해 설명합니다. |
| [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md) |자체 사용자 지정 특성을 사용하여 Azure AD 스키마를 확장하는 방법에 대해 설명합니다. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |사용자의 Microsoft 365 리소스를 사용자와 동일한 지역에 배치하는 방법을 설명합니다. |
| **동기화 서비스** | |
| [Azure AD Connect 동기화 서비스 기능](how-to-connect-syncservice-features.md) |동기화 서비스 쪽과 Azure AD에서 동기화 설정을 변경하는 방법을 설명합니다. |
| [중복 특성 복원력](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |**userPrincipalName** 및 **proxyAddresses** 중복 특성 값 복원력을 사용하도록 설정하고 사용하는 방법을 설명합니다. |
| **작업 및 UI** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |[작업](how-to-connect-sync-service-manager-ui-operations.md), [커넥터](how-to-connect-sync-service-manager-ui-connectors.md), [메타버스 디자이너](how-to-connect-sync-service-manager-ui-mvdesigner.md) 및 [메타버스 검색](how-to-connect-sync-service-manager-ui-mvsearch.md) 탭을 포함한 Synchronization Service Manager UI를 설명합니다. |
| [운영 작업 및 고려 사항](./how-to-connect-sync-staging-server.md) |재해 복구와 같은 운영 문제에 대해 설명합니다. |
| **방법...** | |
| [Azure AD 계정 재설정](how-to-connect-azureadaccount.md) |Azure AD Connect 동기화에서 Azure AD로 연결하는 데 사용되는 서비스 계정의 자격 증명을 재설정하는 방법. |
| **자세한 내용 및 참조** | |
| [Ports](reference-connect-ports.md) |동기화 엔진, 온-프레미스 디렉터리 및 Azure AD 간에 열어야 하는 포트를 나열합니다. |
| [Azure Active Directory에 동기화된 특성](reference-connect-sync-attributes-synchronized.md) |온-프레미스 AD와 Azure AD 간에 동기화되는 모든 특성을 나열합니다. |
| [함수 참조](reference-connect-sync-functions-reference.md) |선언적 프로비전에 사용할 수 있는 모든 함수를 나열합니다. |

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)