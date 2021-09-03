---
title: 여러 Azure 지역에 Azure API Management 서비스 배포
titleSuffix: Azure API Management
description: 여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법에 대해 알아봅니다.
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: c98a3d930d1fcacdc1281c139b020652e4138ac1
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540117"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>여러 Azure 지역에 Azure API Management 서비스 인스턴스를 배포하는 방법

Azure API Management는 다중 지역 배포를 지원하므로 API 게시자는 원하는 수의 지원되는 Azure 지역에 단일 Azure API Management 서비스를 배포할 수 있습니다. 다중 지역 기능을 사용하면 지리적으로 분산된 API 소비자가 느끼는 요청 대기 시간을 줄일 수 있으며 한 지역이 오프라인으로 전환되는 경우 서비스 가용성이 개선됩니다.

새 Azure API Management 서비스는 처음에 단일 Azure 지역인 주 지역에 [단위][unit]를 하나만 포함합니다. 주 지역 또는 보조 지역에 추가 단위를 추가할 수 있습니다. API Management 게이트웨이 구성 요소는 선택한 모든 주 지역 및 보조 지역에 배포됩니다. 들어오는 API 요청은 자동으로 가장 가까운 지역으로 전달됩니다. 지역이 오프라인으로 전환되면 API 요청은 자동으로 실패한 지역 주위에 있는 가장 가까운 다음 게이트웨이로 라우팅됩니다.

> [!NOTE]
> API Management의 게이트웨이 구성 요소만 모든 지역에 배포됩니다. 서비스 관리 구성 요소 및 개발자 포털은 주 지역에서만 호스트됩니다. 따라서 주 지역이 중단되는 경우에는 주 지역이 다시 온라인으로 전환될 때까지 개발자 포털에 대한 액세스 및 구성을 변경하는 기능(예: API 추가, 정책 적용)이 손상됩니다. 주 지역이 오프라인 상태인 동안 사용 가능한 보조 지역은 사용 가능한 최신 구성을 사용하여 API 트래픽을 계속 제공합니다. 필요에 따라 [영역 중복](zone-redundancy.md)을 사용하여 주 지역 또는 보조 지역의 가용성 및 복원력을 개선할 수 있습니다.

>[!IMPORTANT]
> 단일 지역에 고객 데이터를 저장할 수 있는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역(싱가포르)에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장됩니다.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]


## <a name="prerequisites"></a>필수 요건

* 아직 API Management 서비스 인스턴스를 만들지 않은 경우 [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요. 프리미엄 서비스 계층을 선택합니다.
* API Management 인스턴스가 [가상 네트워크](api-management-using-with-vnet.md)에 배포되는 경우 추가하려는 위치에서 가상 네트워크, 서브넷 및 공용 IP 주소를 설정해야 합니다.

## <a name="deploy-api-management-service-to-an-additional-location"></a><a name="add-region"> </a>추가 위치에 API Management 서비스 배포

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **API** 를 선택합니다.
1. 위쪽 막대에서 **+ 추가** 를 선택합니다.
1. 드롭다운 목록에서 위치를 선택합니다.
1. 해당 위치에서 배율 **[단위](upgrade-and-scale.md)** 수를 선택합니다.
1. 필요에 따라 [**가용성 영역**](zone-redundancy.md)을 사용하도록 설정합니다.
1. API Management 인스턴스가 [가상 네트워크](api-management-using-with-vnet.md)에 배포된 경우 해당 위치에서 가상 네트워크 설정을 구성합니다. 해당 위치에서 사용할 수 있는 기존 가상 네트워크, 서브넷 및 공용 IP 주소를 선택합니다.
1. **추가** 를 선택하여 확인합니다.
1. 모든 위치를 구성할 때까지 이 프로세스를 반복합니다.
1. 위쪽 막대에서 **저장** 을 선택하여 배포 프로세스를 시작합니다.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>API Management 서비스 위치 삭제

1. Azure Portal에서 API Management 서비스로 이동하고 메뉴에서 **위치** 항목을 클릭합니다.
2. 제거할 위치에 대해 테이블의 오른쪽 끝에 있는 **...** 단추를 사용하여 상황에 맞는 메뉴를 엽니다. **삭제** 옵션을 선택합니다.
3. 삭제를 확인하고 **저장** 을 클릭하여 변경 내용을 적용합니다.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>지역 백 엔드 서비스로 API 호출 라우팅

Azure API Management는 백 엔드 서비스 URL을 하나만 제공합니다. 다양한 지역에 Azure API Management 인스턴스가 있더라도 API 게이트웨이는 하나의 지역에만 배포된 동일한 백 엔드 서비스로 요청을 전달합니다. 이런 경우 성능 향상은 Azure API Management 내에서 요청에 특정한 지역에 캐시된 응답에서만 발생하고 전 세계의 백 엔드에 계속 연결하는 경우 대기 시간이 길어질 수 있습니다.

시스템의 지리적 분포를 최대한 활용하려면 Azure API Management 인스턴스와 동일한 지역에 백 엔드 서비스를 배포해야 합니다. 그런 다음, 정책과 `@(context.Deployment.Region)` 속성을 사용하여 백 엔드의 로컬 인스턴스로 트래픽을 라우팅하면 됩니다.

1. Azure API Management 인스턴스로 이동하여 왼쪽 메뉴에서 **API** 를 클릭합니다.
2. 원하는 API를 선택합니다.
3. **인바운드 처리** 의 화살표 드롭다운에서 **코드 편집기** 를 클릭합니다.

    ![API 코드 편집기](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. 조건부 `choose` 정책과 결합된 `set-backend`를 사용하여 파일의 `<inbound> </inbound>` 섹션에 적절한 라우팅 정책을 생성합니다.

    예를 들어 아래 XML 파일은 미국 서부와 동아시아 지역에 해당합니다.

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)를 사용하여 백 엔드 서비스를 진행하고, API 호출을 Traffic Manager에 전달하고, Traffic Manager가 라우팅을 자동으로 확인하도록 할 수도 있습니다.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>API Management 지역 게이트웨이에 대한 사용자 지정 라우팅 사용

API Management는 [가장 짧은 대기 시간](../traffic-manager/traffic-manager-routing-methods.md#performance)을 기준으로 요청을 지역 ‘게이트웨이’로 라우팅합니다. API Management에서 이 설정을 재정의할 수는 없지만 사용자 지정 라우팅 규칙을 통해 고유한 Traffic Manager를 사용할 수 있습니다.

1. 고유한 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)를 만듭니다.
1. 사용자 지정 도메인을 사용하는 경우 API Management 서비스 대신 [Traffic Manager에서 사용](../traffic-manager/traffic-manager-point-internet-domain.md)합니다.
1. [Traffic Manager에서 API Management 지역별 엔드포인트를 구성](../traffic-manager/traffic-manager-manage-endpoints.md)합니다. 지역별 엔드포인트는 `https://<service-name>-<region>-01.regional.azure-api.net`의 URL 패턴을 따릅니다(예: `https://contoso-westus2-01.regional.azure-api.net`).
1. [Traffic Manager에서 API Management 지역별 상태 엔드포인트를 구성](../traffic-manager/traffic-manager-monitoring.md)합니다. 지역별 상태 엔드포인트는 `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`의 URL 패턴을 따릅니다(예: `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`).
1. Traffic Manager의 [라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)을 지정합니다.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
