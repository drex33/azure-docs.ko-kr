---
title: Azure Virtual Desktop 아키텍처 권장 사항 - Azure
description: 앱 개발자를 위한 Azure Virtual Desktop의 아키텍처 권장 사항입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b398fe41ec1cda06fce1cf9141ec6bd5d6bcbfb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799211"
---
# <a name="architecture-recommendations"></a>아키텍처 권장 사항

Azure Virtual Desktop 배포는 최종 사용자 요구 사항, 서비스를 배포하는 조직의 기존 인프라 등과 같은 여러 요인에 따라 다양한 형태와 규모로 제공됩니다. 조직의 요구에 부합하는 올바른 아키텍처를 사용하려면 어떻게 해야 할까요?

이 문서에서는 Azure Virtual Desktop 배포 구조에 대한 지침을 제공합니다. 이 문서에 나열된 예제로만 Azure Virtual Desktop을 배포할 수 있는 것은 아닙니다. 그러나 조직 내부 및 외부 사용자에 대한 가장 기본적인 배포 형식 두 가지를 다루게 됩니다.

## <a name="deploying-azure-virtual-desktop-for-users-within-your-organization"></a>조직 내부 사용자를 위한 Azure Virtual Desktop 배포

조직 내부 사용자를 위해 Azure Virtual Desktop을 배포하는 경우 모든 사용자와 리소스를 동일한 Azure 테넌트에서 호스트할 수 있습니다. Azure Virtual Desktop의 현재 지원되는 ID 관리 방법을 사용하여 사용자를 보호할 수도 있습니다.

다음은 조직 내부 사용자에게 RemoteApps 및 데스크톱을 서비스할 수 있는 Azure Virtual Desktop 배포의 가장 기본적인 요구 사항입니다.

- 사용자 세션을 호스트하는 호스트 풀 1개
- 호스트 풀을 호스트하는 Azure 구독 1개
- 구독 및 ID 관리를 위한 소유 테넌트가 될 Azure 테넌트 1개

그러나 서로 다른 사용자 그룹에 서로 다른 앱을 제공하는 여러 호스트 풀이 있는 배포를 빌드할 수도 있습니다.

어떤 고객은 각 Azure Virtual Desktop 배포를 저장할 별도의 Azure 구독을 만들도록 선택합니다. 이 방법에서는 리소스를 제공하는 하위 조직에 따라 각 배포 비용을 서로 구분할 수 있습니다. 다른 고객은 Azure 청구 범위를 사용하여 비용을 더 세부적인 수준으로 구분하도록 선택합니다. 자세한 내용은 [범위 이해 및 작업](../../cost-management-billing/costs/understand-work-scopes.md)을 참조하세요.

## <a name="deploying-azure-virtual-desktop-for-users-outside-your-organization"></a>조직 외부 사용자를 위한 Azure Virtual Desktop 배포

Azure Virtual Desktop 배포가 조직 외부의 최종 사용자, 특히 일반적으로 Windows를 사용하지 않거나 조직의 내부 리소스에 대한 액세스 권한이 없는 사용자를 서비스할 경우 추가 보안 권장 사항을 고려해야 합니다.

Azure Virtual Desktop은 현재 B2B(기업-기업) 또는 B2C(기업-클라이언트) 사용자를 포함하여 외부 ID를 지원하지 않습니다. 이러한 ID는 수동으로 만들어 관리하며 사용자에게 직접 자격 증명을 제공해야 합니다. 그러면 사용자가 이러한 ID를 사용하여 Azure Virtual Desktop의 리소스에 액세스하게 됩니다.

고객에게 안전한 솔루션을 제공하기 위해 Microsoft에서는 고유한 전용 Active Directory를 사용하여 각 고객에 대한 AD(Azure Active Directory) 테넌트 및 구독을 만드는 것을 권장합니다. 이렇게 분리하려면 각 조직에 대해, 다른 배포 및 해당 리소스와는 완전히 격리된 별도의 Azure Virtual Desktop 배포를 만들어야 합니다. 각 조직에서 사용하는 가상 머신은 정보 보호를 위해 다른 회사의 리소스에 액세스할 수 없어야 합니다. AD DS(Active Directory Domain Services) 및 Azure AD Connect 조합이나 Azure AD Domain Services를 사용하여 이러한 개별 배포를 설정할 수 있습니다.
