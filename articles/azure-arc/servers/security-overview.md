---
title: 보안 개요
description: Azure Arc 지원 서버에 대한 보안 정보입니다.
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: 0f3e093dc07c1fddb4598138078003425456c8d1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832209"
---
# <a name="azure-arc-for-servers-security-overview"></a>서버용 Azure Arc 보안 개요

이 문서에서는 기업에 Azure Arc 지원 서버를 배포하기 전에 평가해야 하는 보안 구성과 고려 사항에 대해 설명합니다.

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

각 Azure Arc 지원 서버에는 Azure 구독 내 리소스 그룹의 일부로 관리 ID가 있습니다. 이 ID는 온-프레미스 또는 다른 클라우드 환경에서 실행되는 서버를 나타냅니다. 표준 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)에서 이 리소스에 대한 액세스를 제어합니다. Azure Portal의 [**IAM(Access Control)**](../../role-based-access-control/role-assignments-portal.md) 페이지에서 Azure Arc 지원 서버에 대한 액세스 권한이 있는 사용자를 확인할 수 있습니다.

:::image type="content" source="./media/security-overview/access-control-page.png" alt-text="Azure Arc 지원 서버 액세스 제어" border="false" lightbox="./media/security-overview/access-control-page.png":::

사용자 및 애플리케이션에서 리소스에 대한 액세스 권한이 부여된 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 및 관리자 역할은 컴퓨터에 [확장](manage-vm-extensions.md)을 배포하거나 삭제하는 등 리소스를 변경할 수 있습니다. 확장에는 권한 있는 컨텍스트에서 실행되는 임의의 스크립트가 포함될 수 있으므로 Azure 리소스의 모든 기여자가 서버의 간접 관리자가 될 수 있습니다.

**Azure Connected Machine 온보딩** 역할은 대규모 온보딩에 사용할 수 있으며 Azure에서 새 Azure Arc 지원 서버를 읽거나 만들 수 있습니다. 이미 등록된 서버를 삭제하거나 확장을 관리하는 데 사용할 수 없습니다. 모범 사례로, 대규모 컴퓨터를 온보딩하는 데 사용되는 Azure AD(Azure Active Directory) 서비스 주체에게만 이 역할을 할당하는 것이 좋습니다.

**Azure Connected Machine Resource 관리자** 역할의 멤버인 사용자는 컴퓨터를 읽고 수정하고 다시 온보딩하고 삭제할 수 있습니다. 이 역할은 리소스 그룹 또는 구독의 다른 리소스가 아닌 Azure Arc 사용 가능한 서버의 관리를 지원하도록 설계되었습니다.

## <a name="agent-security-and-permissions"></a>에이전트 보안 및 사용 권한

Windows에서 Azure Connected Machine Agent(azcmagent)를 관리하려면 사용자 계정이 로컬 관리자 그룹의 구성원이어야 합니다. Linux에는 루트 액세스 권한이 있어야 합니다.

Azure Connected Machine Agent는 사용자의 컴퓨터에서 실행되는 세 가지 서비스로 구성됩니다.

* HIMDS(Hybrid Instance Metadata Service)는 Arc의 모든 핵심 기능을 담당합니다. 여기에는 Azure에 하트비트를 보내고, 다른 앱에 대한 로컬 인스턴스 메타데이터 서비스를 노출하여 컴퓨터의 Azure 리소스 ID에 대해 알아보고, 다른 Azure 서비스에 인증하기 위해 Azure AD 토큰을 검색하는 것이 포함됩니다. 이 서비스는 Windows의 권한 없는 가상 서비스 계정 및 Linux의 **himds** 사용자로 실행됩니다.

* GCService(게스트 구성 서비스)는 컴퓨터에서 Azure Policy를 평가하는 일을 담당합니다.

* ExtensionService(게스트 구성 확장 서비스)는 컴퓨터에서 확장(에이전트, 스크립트 또는 기타 소프트웨어)을 설치, 업데이트 및 삭제하는 역할을 합니다.

게스트 구성 및 확장 서비스는 Windows에서는 로컬 시스템으로 실행되고 Linux에서는 루트로 실행됩니다.

## <a name="using-a-managed-identity-with-azure-arc-enabled-servers"></a>Azure Arc 지원 서버에서 관리 ID 사용

기본적으로 Arc에서 사용하는 Azure Active Directory 시스템 할당 ID는 Azure에서 Azure Arc 지원 서버의 상태를 업데이트하는 데만 사용할 수 있습니다. 예를 들어 *마지막으로 표시* 된 하트비트의 상태를 업데이트할 수 있습니다. 서버 애플리케이션에서 시스템 할당 ID를 사용하여 다른 Azure 서비스에 액세스하는 경우 필요에 따라 ID에 다른 역할을 할당할 수 있습니다. Azure 리소스에 액세스하도록 시스템 할당 관리 ID를 구성하는 방법에 대한 자세한 내용은 [Azure Arc 지원 서버를 사용하여 Azure 리소스에 대해 인증을](managed-identity-authentication.md)참조하세요. 

컴퓨터에서 실행되는 애플리케이션에서 Hybrid Instance Metadata Service에 액세스할 수 있지만, 권한이 부여된 애플리케이션만 시스템 할당 ID에 대한 Azure AD 토큰을 요청할 수 있습니다. 토큰 URI에 처음 액세스하려고 할 때 서비스는 신뢰할 수 있는 호출자만 읽을 수 있는 파일 시스템의 위치에 임의로 생성된 암호화 Blob을 생성합니다. 그런 다음 호출자는 파일을 읽고(이에 따라 적절한 권한이 있음이 증명됨) 인증 헤더의 파일 내용으로 요청을 다시 시도하여 Azure AD 토큰을 성공적으로 검색합니다.

* Windows에서 호출자는 로컬 **관리자** 그룹 또는 **하이브리드 에이전트 확장 애플리케이션** 그룹의 구성원이어야 Blob을 읽을 수 있습니다.

* Linux에서 호출자는 Blob을 읽으려면 **himds** 그룹의 멤버여야 합니다.

Arc 지원 서버에서 관리 ID를 사용하여 Azure 리소스를 인증하고 액세스하는 방법에 대한 자세한 내용은 다음 동영상을 참조하세요.

> [!VIDEO https://www.youtube.com/embed/4hfwxwhWcP4]

## <a name="using-disk-encryption"></a>디스크 암호화 사용

Azure Connected Machine Agent는 공개 키 인증을 사용하여 Azure 서비스와 통신합니다. Azure Arc에 서버를 등록한 후에는 프라이빗 키가 디스크에 저장되고 에이전트가 Azure와 통신할 때마다 사용됩니다. 프라이빗 키가 도용된 경우 다른 서버에서 사용되어 서비스와 통신하고 원래 서버처럼 작동하게 할 수 있습니다. 여기에는 시스템 할당 ID에 대한 액세스 권한 및 ID가 액세스할 수 있는 모든 리소스가 포함됩니다. 프라이빗 키 파일은 **himds** 계정 액세스를 통해서만 읽을 수 있도록 보호됩니다. 오프라인 공격을 방지하려면 서버의 운영 체제 볼륨에서 전체 디스크 암호화(예: BitLocker, dm-crypt 등)를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 여러 하이브리드 컴퓨터에서 Azure Arc 지원 서버를 평가하거나 사용하도록 설정하기 전에 [Connected Machine 에이전트 개요를](agent-overview.md) 검토하여 요구 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 이해합니다.

* [계획 및 배포 가이드](plan-at-scale-deployment.md)를 검토하여 모든 규모의 Azure Arc 지원 서버 배포를 계획하고 중앙 집중식 관리와 모니터링을 구현합니다.
