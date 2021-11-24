---
title: '빠른 시작: ARM 템플릿을 사용하여 공유 서비스로 라우팅'
titleSuffix: Azure Virtual WAN
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 모든 VNet 및 Branch에서 액세스할 워크로드를 통해 공유 서비스 VNet에 액세스하도록 경로를 설정하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: 7a0efac43b3508d134642252bf28bf46dbb6250a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049467"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 공유 서비스 VNet으로 라우팅

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 모든 VNet 및 Branch(VPN/ER/P2S)가 액세스할 수 있는 워크로드로 공유 서비스 VNet에 액세스하도록 경로를 설정하는 방법을 설명합니다. 이러한 공유 워크로드의 예로는 도메인 컨트롤러 또는 파일 공유와 같은 서비스가 있는 가상 머신 또는 [Azure Private Endpoint](../private-link/private-endpoint-overview.md)를 통해 내부적으로 노출되는 Azure 서비스가 포함될 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.network%2fvirtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* 이 구성에는 공개 키 인증서 데이터가 필요합니다. 샘플 데이터는 문서에서 제공됩니다. 그러나 샘플 데이터는 P2S 게이트웨이를 만들기 위한 템플릿 요구 사항을 충족하기 위해서만 제공됩니다. 템플릿이 완료되고 리소스가 배포된 후 구성이 작동하려면 이 필드를 사용자 고유의 인증서 데이터로 업데이트해야 합니다. [사용자 VPN 인증서](certificates-point-to-site.md#cer)를 참조하세요.

## <a name="review-the-template"></a><a name="review"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/virtual-wan-with-route-tables)에서 나온 것입니다. 이 문서의 템플릿이 너무 길어서 여기에 표시할 수 없습니다. 템플릿을 보려면 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.network/virtual-wan-with-route-tables/azuredeploy.json)을 참조하세요.

이 빠른 시작에서는 모든 게이트웨이 및 VNet 연결을 포함하여 Azure Virtual WAN 다중 허브 배포를 만듭니다. 입력 매개 변수 목록은 의도적으로 최소한으로 유지되었습니다. IP 주소 지정 체계는 템플릿 내의 변수를 수정하여 변경할 수 있습니다. 시나리오는 [시나리오: 공유 서비스 VNet](scenario-shared-services-vnet.md) 문서에 자세히 설명되어 있습니다.

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="배포 아키텍처" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

이 템플릿은 다음 리소스를 사용하여 완벽하게 작동하는 Azure Virtual WAN 환경을 만듭니다.

* 서로 다른 지역에 있는 2개의 개별 허브.
* 4개의 Azure VNet(Virtual Network).
* 각 VWan 허브에 대한 2개의 VNet 연결.
* 각 허브의 P2S(지점 및 사이트 간) VPN 게이트웨이 1개.
* 각 허브의 S2S(사이트 간) VPN 게이트웨이 1개.
* 각 허브의 ExpressRoute 게이트웨이 1개.
* 각 허브의 사용자 지정 경로 테이블 RT_SHARED.
* RT_SHARED 경로 테이블을 그룹화하기 위한 레이블 LBL_RT_SHARED.

템플릿에는 여러 개의 Azure 리소스가 정의되어 있습니다.

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> 이 ARM 템플릿은 하이브리드 연결에 필요한 고객 쪽 리소스를 만들지 않습니다. 템플릿이 배포된 후에도 P2S VPN 클라이언트, VPN 분기(로컬 사이트)를 만들어 구성하고 ExpressRoute 회로를 연결해야 합니다.
>

더 많은 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)을 참조하세요.

## <a name="deploy-the-template"></a><a name="deploy"></a>템플릿 배포

이 템플릿을 올바르게 배포하려면 다음과 같은 이유로 다른 방법 대신 Azure에 배포 단추 및 Azure Portal을 사용해야 합니다.

* P2S 구성을 만들려면 루트 인증서 데이터를 업로드해야 합니다. PowerShell 또는 CLI를 사용하는 경우 데이터 필드에서 인증서 데이터를 허용하지 않습니다.
* 이 템플릿은 인증서 데이터 업로드로 인해 Cloud Shell을 사용하여 제대로 작동하지 않습니다.
* 또한 IP 주소 범위 및 기타 값을 수용하도록 포털에서 템플릿 및 매개 변수를 쉽게 수정할 수 있습니다.

1. **Deploy to Azure** 를 클릭합니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.network%2fvirtual-wan-with-route-tables%2fazuredeploy.json)
1. 템플릿을 보려면 **템플릿 편집** 을 클릭합니다. 이 페이지에서 주소 공간 또는 특정 리소스 이름과 같은 일부 값을 조정할 수 있습니다. **저장** 을 선택하여 변경 내용을 저장하거나 **취소** 를 선택합니다.
1. 템플릿 페이지에서 값을 입력합니다. 이 템플릿의 경우 P2S 공용 인증서 데이터가 필요합니다. 이 문서를 연습으로 사용하는 경우 이 .cer 파일의 다음 데이터를 두 허브의 샘플 데이터로 사용할 수 있습니다. 템플릿이 실행되고 배포가 완료되면 P2S 구성을 사용하기 위해 이 정보를 사용자 고유 배포용 공개 키 [인증서 데이터](certificates-point-to-site.md#cer)로 바꾸어야 합니다.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. 값 입력이 완료되면 **검토 + 만들기** 를 선택합니다.
1. **검토 + 만들기** 페이지에서 유효성 검사가 통과되면 **만들기** 를 선택합니다.
1. 배포를 완료하는 데 약 75분이 걸립니다. 템플릿 **개요** 페이지에서 진행률을 볼 수 있습니다. 포털을 닫더라도 배포는 계속됩니다.

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="배포 완료의 예":::

## <a name="validate-the-deployment"></a><a name="validate"></a>배포 유효성 검사

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.
1. 이전 섹션에서 만든 리소스 그룹을 선택합니다. **개요** 페이지에서 다음 예와 비슷한 내용이 표시됩니다. :::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="리소스의 예" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. 가상 WAN을 클릭하여 허브를 확인합니다. 가상 WAN 페이지에서 각 허브를 클릭하여 연결 및 기타 허브 정보를 확인합니다.
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="허브의 예" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>하이브리드 구성 완료

템플릿에서 하이브리드 네트워크에 필요한 모든 설정을 구성하지는 않습니다. 요구 사항에 따라 다음 구성 및 설정을 완료해야 합니다.

* [VPN 분기 - 로컬 사이트 구성](virtual-wan-site-to-site-portal.md#site)
* [P2S VPN 구성 완료](virtual-wan-point-to-site-portal.md)
* [ExpressRoute 회로 연결](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>리소스 정리

만든 리소스가 더 이상 필요하지 않은 경우 해당 리소스를 삭제합니다. 일부 Virtual WAN 리소스는 종속성으로 인해 특정 순서로 삭제해야 합니다. 삭제를 완료하는 데 약 30분이 걸릴 수 있습니다.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [P2S VPN 구성 완료](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [VPN 분기 - 로컬 사이트 구성](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [ExpressRoute 회로 연결](virtual-wan-expressroute-portal.md)
