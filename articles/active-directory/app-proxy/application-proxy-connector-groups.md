---
title: 커넥터 그룹을 통해 별도의 네트워크에 앱 게시 - Azure Active Directory
description: Azure Active Directory 애플리케이션 프록시에서 커넥터 그룹을 만들고 관리하는 방법에 대해 설명합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 175b908d9ca9baf40fbb5ff17dc3750d715433df
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989706"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>커넥터 그룹을 사용하여 별도의 네트워크 및 위치에서 애플리케이션 게시

고객은 더 많은 시나리오와 애플리케이션을 위해 Azure AD의 애플리케이션 프록시를 활용합니다. 따라서 더 많은 토폴로지를 사용하여 응용 프로그램 프록시를 더욱 유연하게 만들었습니다. 특정 애플리케이션을 제공하는 특정 커넥터를 할당할 수 있도록 애플리케이션 프록시 커넥터 그룹을 만들 수 있습니다. 이 기능은 애플리케이션 프록시 배포를 최적화하는 보다 많은 제어와 방법을 제공합니다.

각 애플리케이션 프록시 커넥터는 커넥터 그룹에 할당됩니다. 동일한 커넥터 그룹에 속한 모든 커넥터는 고가용성 및 부하 분산을 위해 별도의 단위로 작동합니다. 모든 커넥터는 커넥터 그룹에 속합니다. 그룹을 만들지 않는 경우 모든 커넥터는 기본 그룹에 있습니다. 관리자는 Azure Portal에서 새 그룹을 만들고 커넥터를 할당할 수 있습니다.

모든 애플리케이션은 커넥터 그룹에 할당됩니다. 그룹을 만들지 않는 경우 모든 애플리케이션은 기본 그룹에 할당됩니다. 그러나 커넥터를 그룹으로 구성하면 특정 커넥터 그룹과 함께 작동하도록 각 애플리케이션을 설정할 수 있습니다. 이 경우 해당 그룹의 커넥터만 요청에 따라 애플리케이션을 제공합니다. 이 기능은 애플리케이션이 서로 다른 위치에서 호스트되는 경우에 유용합니다. 애플리케이션이 물리적으로 인접한 커넥터를 통해 항상 제공될 수 있도록 위치에 따라 커넥터 그룹을 만들 수 있습니다.

> [!TIP]
> 대규모 애플리케이션 프록시 배포가 있는 경우 기본 커넥터 그룹에 애플리케이션을 할당하지 마십시오. 이런 방식으로 새 커넥터는 활성 커넥터 그룹에 할당될 때까지 라이브 트래픽을 수신하지 않습니다. 또한 이 구성을 통해 사용자에 영향을 주지 않고 유지 관리를 수행할 수 있도록 기본 그룹으로 다시 이동하여 커넥터를 유휴 모드로 전환할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

커넥터를 그룹화하려면 [여러 커넥터를 설치](application-proxy-add-on-premises-application.md)해야 합니다. 새 커넥터를 설치하면 자동으로 **기본** 커넥터 그룹을 조인합니다.

## <a name="create-connector-groups"></a>커넥터 그룹 만들기

이 단계를 사용하여 원하는 수 만큼 커넥터 그룹을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **Enterprise 애플리케이션** > **애플리케이션 프록시** 를 선택합니다.
1. **새 커넥터 그룹** 을 선택합니다. 새 커넥터 그룹 블레이드가 표시됩니다.

   ![새 커넥터 그룹을 선택하는 화면을 표시합니다.](./media/application-proxy-connector-groups/new-group.png)

1. 새 커넥터 그룹에 이름을 지정한 다음 드롭다운 메뉴를 사용하여 이 그룹에 속하는 커넥터를 선택합니다.
1. **저장** 을 선택합니다.

## <a name="assign-applications-to-your-connector-groups"></a>커넥터 그룹에 애플리케이션 할당

애플리케이션 프록시를 사용하여 게시한 각 애플리케이션에 대해 이 단계를 사용합니다. 처음으로 게시할 때 애플리케이션을 커넥터 그룹에 할당하거나 이 단계를 사용하여 언제든지 할당을 변경할 수 있습니다.

1. 디렉터리에 대한 관리 대시보드의 경우 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** &gt; 커넥터 그룹에 할당하려는 애플리케이션 &gt; **애플리케이션 프록시** 를 선택합니다.
1. **커넥터 그룹** 드롭다운 메뉴를 사용하여 애플리케이션에서 사용할 그룹을 선택합니다.
1. **저장** 을 선택하여 변경 내용을 적용합니다.

## <a name="use-cases-for-connector-groups"></a>커넥터 그룹의 사용 사례

커넥터 그룹은 다음을 비롯한 다양한 시나리오에 유용합니다.

### <a name="sites-with-multiple-interconnected-datacenters"></a>여러 데이터 센터가 연결되어 있는 사이트

많은 조직에는 서로 연결된 데이터 센터가 여러 개 있습니다. 이 경우 데이터 센터 간 링크가 비용이 높고 느리기 때문에 가능한 한 데이터 센터 내에서 많은 트래픽을 유지하려고 합니다. 각 데이터 센터에서 커넥터를 배포하여 데이터 센터 내에 있는 애플리케이션만 제공할 수 있습니다. 이 방법은 데이터 센터 간 링크를 최소화하고 사용자에게 완전히 투명한 경험을 제공합니다.

### <a name="applications-installed-on-isolated-networks"></a>격리된 네트워크에 설치된 애플리케이션

기본 회사 네트워크에 속하지 않은 네트워크에서 애플리케이션을 호스팅할 수 있습니다. 커넥터 그룹을 사용하여 격리된 네트워크의 전용 커넥터를 설치하고 해당 네트워크에 애플리케이션도 격리할 수 있습니다. 이는 일반적으로 타사 공급 업체가 조직의 특정 애플리케이션을 유지 관리할 때 발생합니다.

커넥터 그룹을 사용하면 특정 애플리케이션만 게시하는 네트워크에 전용 커넥터를 설치할 수 있으므로 애플리케이션 관리를 타사 공급 업체에 보다 쉽고 안전하게 아웃소싱할 수 있습니다.

### <a name="applications-installed-on-iaas"></a>IaaS에 설치된 애플리케이션

클라우드 액세스를 위해 IaaS에 설치된 애플리케이션의 경우 커넥터 그룹은 모든 앱에 대한 액세스를 보호하는 일반적인 서비스를 제공합니다. 커넥터 그룹은 회사 네트워크에 추가 종속성을 만들거나 앱 경험을 조각화하지 않습니다. 커넥터는 각 클라우드 데이터 센터에 설치될 수 있으며 해당 네트워크에 있는 애플리케이션만 처리할 수 있습니다. 고가용성을 얻기 위해 커넥터를 여러 개 설치할 수 있습니다.

예를 들어 IaaS 호스팅된 고유한 가상 네트워크에 연결된 가상 머신이 여러 개 있는 조직을 사용합니다. 직원들이 해당 애플리케이션을 사용할 수 있도록 하기 위해 이러한 사설망은 사이트 간 VPN을 사용하여 회사 네트워크에 연결됩니다. 이는 온-프레미스에 있는 직원들에게 좋은 경험을 제공합니다. 그러나 원격 직원에게는 아래 다이어그램과 같이 추가 온-프레미스 인프라가 액세스를 라우팅해야 하기 때문에 이상적이지 않을 수 있습니다.

![Azure AD IaaS 네트워크를 보여주는 다이어그램](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Azure AD 애플리케이션 프록시 커넥터 그룹을 사용하면 공통 서비스를 통해 회사 네트워크에 대한 추가 종속성을 만들지 않고도 모든 애플리케이션에 대한 액세스를 보호할 수 있습니다.

![Azure AD Iaas 멀티 클라우드 공급업체](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>다중 포리스트 - 포리스트마다 서로 다른 커넥터 그룹

애플리케이션 프록시를 배포한 대부분의 고객은 KCD(Kerberos 제한 위임)를 수행하여 SSO(Single-Sign-On) 기능을 사용하고 있습니다. 이를 위해 커넥터의 컴퓨터는 사용자를 애플리케이션에 위임할 수 있는 도메인에 가입해야 합니다. KCD는 포리스트 간 기능을 지원합니다. 그러나 서로 간에 신뢰할 수 없는 고유한 다중 포리스트 환경이 있는 회사의 경우 모든 포리스트에 대해 단일 커넥터를 사용할 수 없습니다. 

이 경우 포리스트마다 특정 커넥터를 배포하고 해당 포리스트의 사용자만 제공하도록 게시된 애플리케이션을 제공하도록 설정할 수 있습니다. 각 커넥터 그룹마다 별도의 포리스트를 나타냅니다. 테넌트와 대부분의 환경이 모든 포리스트에 대해 통합되지만 Azure AD 그룹을 사용하여 사용자를 포리스트 애플리케이션에 할당할 수 있습니다.

### <a name="disaster-recovery-sites"></a>재해 복구 사이트

다음과 같이 사이트 구현 방법에 따라 DR(재해 복구) 사이트에서 수행할 수 있는 두 가지 방법이 있습니다.

* DR 사이트가 주 사이트와 정확히 같고 네트워킹과 AD 설정이 동일한 활성-활성 모드로 만들어진 경우 주 사이트와 동일한 커넥터 그룹의 DR 사이트에 커넥터를 만들 수 있습니다. 이를 통해 Azure AD는 장애 복구를 검색할 수 있습니다.
* DR 사이트가 주 사이트와 별개인 경우 DR 사이트에 다른 커넥터 그룹을 만들고 필요에 따라 1) 백업 애플리케이션을 만들거나 2) 수동으로 기존 애플리케이션을 DR 커넥터 그룹으로 전환할 수 있습니다.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>단일 테넌트에서 여러 회사 제공

단일 서비스 공급자가 여러 회사에 Azure AD 관련 서비스를 배포하고 유지 관리하는 모델을 구현하는 방법에는 여러 가지가 있습니다. 관리자가 커넥터와 애플리케이션을 여러 그룹으로 분리하는 데 커넥터 그룹이 도움이 됩니다. 소규모 회사에 적합한 한 가지 방법은 단일 Azure AD 테넌트를 보유하지만 다른 회사는 자체의 도메인 이름과 네트워크를 보유하는 것입니다. 또한 단일 IT 부서에서 규제 또는 비즈니스 상의 이유로 여러 회사에 서비스를 제공하는 M&A 시나리오 및 상황에서도 마찬가지입니다.

## <a name="sample-configurations"></a>샘플 구성

구현할 수 있는 몇 가지 예에는 다음 커넥터 그룹이 포함됩니다.

### <a name="default-configuration--no-use-for-connector-groups"></a>기본 구성 - 커넥터 그룹 사용 안 함

커넥터 그룹을 사용하지 않는 경우 구성은 다음과 같습니다.

![예제 Azure AD 커넥터 그룹 없음](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

이 구성은 소규모 배포 및 테스트에 충분합니다. 또한 조직에 플랫 네트워크 토폴로지가 있는 경우에도 잘 작동합니다.

### <a name="default-configuration-and-an-isolated-network"></a>기본 구성 및 격리된 네트워크

이 구성은 기본 구성이 진화한 것으로, IaaS 가상 네트워크와 같이 격리된 네트워크에서 실행되는 특정 응용 프로그램이 있습니다.

![예제 Azure AD에 커넥터 그룹 및 격리된 네트워크 없음](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>권장 구성 - 여러 특정 그룹 및 유휴 상태의 기본 그룹

크고 복잡한 조직에 권장되는 구성은 기본 커넥터 그룹을 어떤 애플리케이션도 제공하지 않고 유휴 또는 새로 설치된 커넥터에 사용되는 그룹으로 사용하는 것입니다. 모든 애플리케이션은 사용자 지정된 커넥터 그룹을 통해 제공됩니다. 이렇게 하면 위에서 설명한 모든 시나리오의 복잡성을 구현할 수 있습니다.

아래 예에서는 회사에 두 개의 데이터 센터(A 및 B)가 있고 두 개의 커넥터에서 각 사이트를 지원합니다. 각 사이트에는 실행되는 다양한 애플리케이션이 있습니다.

![2개의 데이터 센터와 2개의 커넥터가 있는 회사의 예](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>다음 단계

[Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
[Single-Sign on 사용](../manage-apps/what-is-single-sign-on.md)
