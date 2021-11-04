---
title: Traffic Manager를 사용하여 여러 엔드포인트 간 장애 조치(failover)
titleSuffix: Azure Content Delivery Network
description: Azure Traffic Manager를 사용하여 여러 Azure Content Delivery Network 엔드포인트 간에 장애 조치(failover)를 구성하는 방법을 알아봅니다.
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 97acf57c4b00b795339a68ce87c7d2020839e384
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446755"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Azure Traffic Manager를 사용하여 여러 엔드포인트 간 장애 조치(failover)

Azure CDN(Content Delivery Network)을 구성하는 경우 요구에 대해 최적의 공급자 및 가격 책정 계층을 선택할 수 있습니다. 

Azure CDN은 전역으로 분산된 인프라를 사용하여 기본적으로 로컬 및 지역적 중복 및 전역 부하 분산을 만들어 서비스 가용성 및 성능을 개선합니다. 

콘텐츠를 제공할 수 있는 위치를 사용할 수 없는 경우 요청은 자동으로 다른 위치로 라우팅됩니다. 최적의 POP(현재 위치)는 각 클라이언트 요청을 처리하는 데 사용됩니다. 자동 라우팅은 요청 위치 및 서버 로드와 같은 요소를 기반으로 합니다.
 
여러 CDN 프로필이 있는 경우 Azure Traffic Manager를 사용하여 가용성 및 성능을 더욱 개선할 수 있습니다. 

다음에 대해 Azure CDN을 통해 Azure Traffic Manager를 사용하여 여러 CDN 엔드포인트 간에 부하를 분산합니다.
 
* 장애 조치 
* 지역 부하 분산 

일반적인 장애 조치(failover) 시나리오에서 모든 클라이언트 요청은 기본 CDN 프로필로 전달됩니다. 

프로필을 사용할 수 없는 경우 요청은 보조 프로필로 전달됩니다.  기본 프로필이 다시 온라인 상태가 되면 요청이 다시 시작됩니다.

이러한 방식으로 Azure Traffic Manager를 사용하여 웹 애플리케이션을 항상 사용할 수 있는지 확인합니다. 

이 문서에서는 다음에서 프로필을 사용하여 장애 조치(failover)를 구성하는 방법에 대한 지침과 예를 제공합니다. 

* **Verizon의 Azure CDN 표준**
* **Akamai의 Azure CDN 표준**

**Microsoft의 Azure CDN** 도 지원됩니다.

## <a name="create-azure-cdn-profiles"></a>Azure CDN 프로필 만들기
다른 공급자를 사용하여 두 개 이상의 Azure CDN 프로필 및 엔드포인트를 만듭니다.

1. 두 개의 CDN 프로필을 만듭니다.
    * **Verizon의 Azure CDN 표준**
    * **Akamai의 Azure CDN 표준** 

    [새 CDN 프로필 만들기](cdn-create-new-endpoint.md#create-a-new-cdn-profile)의 단계에 따라 프로필을 만듭니다.
 
   ![여러 CDN 프로필](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 각 새 프로필에서 [새 CDN 엔드포인트 만들기](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)의 단계를 수행하여 하나 이상의 엔드포인트를 만듭니다.

## <a name="create-traffic-manager-profile"></a>트래픽 관리자 프로필 만들기
Azure Traffic Manager 프로필을 만들고 CDN 엔드포인트 간 부하 분산을 구성합니다. 

1. [Traffic Manager 프로필 만들기](../traffic-manager/quickstart-create-traffic-manager-profile.md)의 단계를 수행하여 Azure Traffic Manager 프로필을 만듭니다. 

    * **라우팅 메서드** 에서 **우선 순위** 를 선택합니다.

2. [Traffic Manager 엔드포인트 추가](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)의 단계를 수행하여 Traffic Manager 프로필에서 CDN 엔드포인트 추가

    * **형식** 에서 **외부 엔드포인트** 를 선택합니다.
    * **우선 순위** 에서 숫자를 입력합니다.

    예를 들어 **1** 의 우선 순위로 **cdndemo101akamai.azureedge.net** 을 만들고 **2** 의 우선 순위로 **cdndemo101verizon.azureedge.net** 을 만듭니다.

   ![CDN Traffic Manager 엔트포인트](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Azure CDN 및 Azure Traffic Manager에서 사용자 지정 도메인 구성
CDN 및 Traffic Manager 프로필을 구성한 후 이러한 단계를 수행하여 CDN 엔드포인트에 DNS 매핑을 추가하고 사용자 지정 도메인을 등록합니다. 예를 들어 사용자 지정 도메인 이름은 **cdndemo101.dustydogpetcare.online** 입니다.

1. GoDaddy 같은 사용자 지정 도메인의 도메인 공급자에 대한 웹 사이트로 이동하고 두 DNS CNAME 항목을 만듭니다. 

    a. 첫 번째 CNAME 항목의 경우 cdnverify 하위 도메인이 있는 사용자 지정 도메인을 CDN 엔드포인트에 매핑합니다. 이 항목은 2단계에서 Traffic Manager에 추가한 CDN 엔드포인트에 사용자 지정 도메인을 등록하는 데 필요한 단계입니다.

      예를 들면 다음과 같습니다. 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 두 번째 CNAME 항목의 경우 cdnverify 하위 도메인이 없는 사용자 지정 도메인을 CDN 엔드포인트에 매핑합니다. 이 항목은 Traffic Manager에 사용자 지정 도메인을 매핑합니다. 

      예를 들면 다음과 같습니다. 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > 도메인이 현재 라이브라서 중단할 수 없는 경우 이 단계를 마지막에 수행합니다. 사용자 지정 도메인 DNS를 Traffic Manager로 업데이트하기 전에 Traffic Manager 도메인 및 CDN 엔드포인트가 라이브인지 확인합니다.
    >
   
    > [!NOTE]
    > 이 장애 조치(failover) 시나리오를 구현하려면 두 엔드포인트가 서로 다른 프로필에 있어야 하고, 도메인 이름 충돌을 방지하려면 다른 CDN 공급자가 서로 다른 프로필을 사용해야 합니다.
    > 

2.  Azure CDN 프로필에서 첫 번째 CDN 엔드포인트(Akamai에)를 선택합니다. **사용자 지정 도메인 추가** 를 선택하고 **cdndemo101.dustydogpetcare.online** 을 입력합니다. 사용자 지정 도메인의 유효성을 검사하는 확인 표시가 녹색인지 확인합니다. 

    이 등록 프로세스를 완료하려면 Azure CDN은 **cdnverify** 하위 도메인을 사용하여 DNS 매핑의 유효성을 검사합니다. 자세한 내용은 [CNAME DNS 레코드 만들기](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)를 참조하세요. 이 단계에서는 해당 요청에 응답할 수 있도록 Azure CDN이 사용자 지정 도메인을 인식할 수 있게 설정합니다.
    
    > [!NOTE]
    > **Akamai의 Azure CDN** 프로필에서 TLS를 사용하도록 설정하려면 사용자 지정 도메인을 엔드포인트에 직접 연결해야 합니다. TLS를 사용하도록 설정하기 위한 cdnverify는 아직 지원되지 않습니다. 
    >

3.  사용자 지정 도메인의 도메인 공급자에 대한 웹 사이트로 돌아갑니다. 사용자가 만든 첫 번째 DNS 매핑을 업데이트합니다. 사용자 지정 도메인을 두 번째 CDN 엔드포인트에 매핑합니다.
                             
    예를 들면 다음과 같습니다. 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Azure CDN 프로필에서 두 번째 CDN 엔드포인트(Verizon)를 선택하고 2단계를 반복합니다. **사용자 지정 도메인 추가** 를 선택하고 **cdndemo101.dustydogpetcare.online** 을 입력합니다.
 
이러한 단계를 완료한 후 장애 조치(failover) 기능이 포함된 다중 CDN 서비스는 Azure Traffic Manager를 사용하여 구성됩니다. 

사용자 지정 도메인에서 테스트 URL에 액세스할 수 있습니다. 

기능을 테스트하려면 기본 CDN 엔드포인트를 사용하지 않도록 설정하고 요청이 두 번째 CDN 엔드포인트에 올바르게 이동되는지 확인합니다. 

## <a name="next-steps"></a>다음 단계
다른 CDN 엔드포인트 간에 부하를 분산하려면 지리 같은 다른 라우팅 방법을 구성할 수 있습니다. 

자세한 내용은 [Traffic Manager를 사용한 지리적 트래픽 라우팅 방법 구성](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)을 참조하세요.
