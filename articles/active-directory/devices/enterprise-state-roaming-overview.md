---
title: Azure Active Directory의 엔터프라이즈 상태 로밍이란?
description: Enterprise State Roaming은 사용자에게 Windows 디바이스에 통합된 환경을 제공합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 338ca079f2c351b1600d387c3e8e81a27382604e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596394"
---
# <a name="what-is-enterprise-state-roaming"></a>엔터프라이즈 상태 로밍이란?

Windows 10에서는 [Azure Active Directory(Azure AD)](../fundamentals/active-directory-whatis.md) 사용자가 사용자 설정 및 애플리케이션 설정 데이터를 클라우드와 안전하게 동기화할 수 있습니다. Enterprise State Roaming은 사용자의 모든 Windows 디바이스에 통합된 환경을 제공하고 새 디바이스 구성에 드는 시간을 줄여 줍니다. 엔터프라이즈 상태 로밍은 Windows 8에서 처음 도입된 표준 [소비자 설정 동기화](https://go.microsoft.com/fwlink/?linkid=2015135) 와 비슷한 방식으로 작동합니다. 뿐만 아니라 Enterprise State Roaming은 다음 기능을 제공합니다.

* **기업 데이터와 소비자 데이터 분리** - 조직에서 데이터를 제어할 수 있으며, 소비자 클라우드 계정에 회사 데이터가 섞이거나 엔터프라이즈 클라우드 계정에 기업 데이터가 섞이지 않습니다.
* **보안 강화** – 데이터가 사용자의 Windows 10 디바이스에서 나가기 전에 Azure RMS(Azure Rights Management)를 사용하여 자동으로 암호화되고, 암호화된 상태로 클라우드에 보존됩니다. 설정 이름 및 Windows 앱 이름 같은 네임 스페이스를 제외한 모든 콘텐츠가 암호화된 상태로 클라우드에 보존됩니다.  
* **더 나은 관리 및 모니터링** – Azure AD 포털 통합을 통해 조직에서 누가, 어떤 디바이스에서 설정을 동기화할 수 있는지를 제어하고 확인할 수 있습니다. 

엔터프라이즈 상태 로밍은 여러 Azure 지역에서 사용할 수 있습니다. 사용할 수 있는 지역 목록 업데이트는 Azure Active Directory의 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services) 페이지에서 확인할 수 있습니다.

| 아티클 | Description |
| --- | --- |
| [Azure Active Directory에서 엔터프라이즈 상태 로밍 활성화](enterprise-state-roaming-enable.md) |엔터프라이즈 상태 로밍은 프리미엄 Azure Active Directory(Azure AD)를 구독하는 모든 조직에 제공됩니다. Azure AD 구독을 가져오는 방법에 대한 자세한 내용은 [Azure AD 제품](https://azure.microsoft.com/services/active-directory) 페이지를 참조하세요. |
| [설정 및 데이터 로밍 FAQ](enterprise-state-roaming-faqs.yml) |이 문서에서는 설정 및 앱 데이터 동기화에 대한 IT 관리자의 질문에 답변합니다. |
| [설정 동기화에 대한 그룹 정책 및 MDM 설정](enterprise-state-roaming-group-policy-settings.md) |Windows 10은 설정 동기화를 제한하는 그룹 정책 및 MDM(모바일 디바이스 관리) 정책 설정을 제공합니다. |
| [Windows 10 로밍 설정 참조](enterprise-state-roaming-windows-settings-reference.md) |Windows 10에서 로밍되거나 백업되는 모든 설정의 전체 목록입니다. |
| [문제 해결](enterprise-state-roaming-troubleshooting.md) |문제 해결을 위한 몇 가지 기본 단계를 살펴보며 알려진 문제 목록이 포함되어 있습니다. |

## <a name="next-steps"></a>다음 단계

엔터프라이즈 상태 로밍 사용에 대한 자세한 내용은 [엔터프라이즈 상태 로밍 사용](enterprise-state-roaming-enable.md)을 참조하세요.
