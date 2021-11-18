---
title: Application Gateway 통합 - Azure App Service | Microsoft Docs
description: Application Gateway가 Azure App Service와 통합되는 방법을 설명합니다.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: dda9b5a55255ca98ea6890caa5581a4096246645
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714963"
---
# <a name="application-gateway-integration"></a>Application Gateway 통합
Azure Application Gateway와 통합하는 데 약간 다른 구성을 필요로 하는 세 가지 App Service 변형이 있습니다. 이러한 변형에는 다중 테넌트라고도 하는 일반 App Service, ILB(내부 Load Balancer) ASE(App Service Environment) 및 외부 ASE가 포함됩니다. 이 문서에서는 트래픽을 보호하기 위해 서비스 엔드포인트를 사용하여 App Service(다중 테넌트)로 구성하는 방법을 안내합니다. 또한 이 문서에서는 프라이빗 엔드포인트 사용과 ILB 및 외부 ASE와의 통합에 대한 고려 사항에 대해 설명합니다. 마지막으로 이 문서에는 scm/kudu 사이트에 대한 고려 사항이 있습니다.

## <a name="integration-with-app-service-multi-tenant"></a>App Service(다중 테넌트)와 통합
App Service(다중 테넌트)에는 공용 인터넷 연결 엔드포인트가 있습니다. [서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하여 Azure Virtual Network 내의 특정 서브넷에서만 트래픽을 허용하고 다른 모든 것은 차단할 수 있습니다. 다음 시나리오에서는 이 기능을 사용하여 App Service 인스턴스가 특정 Application Gateway 인스턴스의 트래픽만 받을 수 있게 합니다.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="다이어그램은 Azure Virtual Network에서 Application Gateway로 이동하여 방화벽 아이콘을 통해 App Service의 앱 인스턴스로 이동하는 인터넷을 보여줍니다.":::

App Service 및 Application Gateway를 만드는 것 외에도 이 구성에는 두 가지 부분이 더 있습니다. 첫 번째 부분은 Application Gateway가 배포된 Virtual Network의 서브넷에서 서비스 엔드포인트를 사용하도록 설정하는 것입니다. 서비스 엔드포인트는 모든 네트워크 트래픽이 서브넷에서 나가 특정 서브넷 ID로 태그가 지정되는 App Service로 가게 합니다. 두 번째 부분은 이 특정 서브넷 ID로 태그가 지정된 트래픽만 허용되도록 특정 웹 앱의 액세스 제한을 설정하는 것입니다. 기본 설정에 따라 다른 도구를 사용하여 구성할 수 있습니다.

## <a name="using-azure-portal"></a>Azure Portal 사용
Azure Portal을 사용하여 설치를 프로비전하고 구성하는 네 가지 단계를 수행합니다. 기존 리소스가 있는 경우에는 첫 번째 단계를 건너뛰어도 됩니다.
1. App Service 설명서의 빠른 시작 중 하나를 사용하여 App Service 만들기(예: [.Net Core 빠른 시작](../quickstart-dotnetcore.md))
2. [포털 빠른 시작](../../application-gateway/quick-create-portal.md)을 사용하여 Application Gateway를 만들고, 백엔드 대상 추가 섹션은 건너뜁니다.
3. [Application Gateway에서 App Service를 백엔드로](../../application-gateway/configure-web-app-portal.md) 구성하고 액세스 제한 섹션은 건너뜁니다.
4. 마지막으로 [서비스 엔드포인트를 사용하는 액세스 제한](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule)을 만듭니다.

이제 Application Gateway를 통해 App Service에 액세스할 수 있지만, App Service에 직접 액세스를 시도하면 웹 사이트가 중지되었음을 나타내는 403 HTTP 오류가 표시될 것입니다.

:::image type="content" source="./media/app-gateway-with-service-endpoints/website-403-forbidden.png" alt-text="‘오류 403 - 사용할 수 없음’ 텍스트를 보여주는 스크린샷":::

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
[Resource Manager 배포 템플릿][template-app-gateway-app-service-complete]은 전체 시나리오를 프로비전합니다. 이 시나리오는 Application Gateway에서만 트래픽을 수신하는 액세스 제한 사항과 서비스 엔드포인트로 잠긴 App Service 인스턴스로 구성됩니다. 템플릿에는 편의를 위해 리소스 이름에 추가되는 고유한 접미사와 스마트 기본값이 다수 포함되어 있습니다. 이를 재정의하려면 리포지토리를 복제하거나 템플릿을 다운로드하여 편집해야 합니다.

템플릿을 적용하려면 템플릿 설명에 있는 ‘Azure에 배포’ 단추를 사용하거나 적절한 PowerShell/CLI를 사용하세요.

## <a name="using-azure-command-line-interface"></a>Azure 명령줄 인터페이스 사용
[Azure CLI 샘플](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md)은 Application Gateway에서만 트래픽을 수신하는 액세스 제한 사항과 서비스 엔드포인트로 잠긴 App Service를 프로비전합니다. 기존 App Service로 가는 트래픽만 기존 Application Gateway와 격리해야 하는 경우 다음 명령을 사용하면 됩니다.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

기본 구성에서는 이 명령으로 서브넷에 있는 서비스 엔드포인트 구성과 App Service의 액세스 제한 설정을 모두 확인할 수 있습니다.

## <a name="considerations-when-using-private-endpoint"></a>프라이빗 엔드포인트 사용 시 고려 사항

서비스 엔드포인트의 대안으로 프라이빗 엔드포인트를 사용하여 Application Gateway와 App Service(다중 테넌트) 간의 트래픽을 보호할 수 있습니다. Application Gateway가 App Service 앱의 개인 IP를 DNS로 확인할 수 있는지 또는 백 엔드 풀에서 개인 IP를 사용하고 http 설정에서 호스트 이름을 재정의하는지 확인해야 합니다.

:::image type="content" source="./media/app-gateway-with-service-endpoints/private-endpoint-appgw.png" alt-text="다이어그램은 Azure Virtual Network에서 Application Gateway로 이동하여 프라이빗 엔드포인트를 통해 App Service의 앱 인스턴스로 이동하는 트래픽을 보여줍니다.":::

## <a name="considerations-for-ilb-ase"></a>ILB ASE 고려 사항
ILB ASE는 인터넷에 노출되지 않으므로, 인스턴스와 Application Gateway 간의 트래픽이 이미 Virtual Network와 격리되어 있습니다. 다음 [방법 가이드](../environment/integrate-with-application-gateway.md)에서는 ILB ASE를 구성하고 Azure Portal을 사용하여 Application Gateway와 통합합니다.

Application Gateway 서브넷의 트래픽만 ASE에 도달하도록 하려면 ASE의 모든 웹 앱에 영향을 주는 NSG(네트워크 보안 그룹)를 구성하면 됩니다. NSG의 경우 서브넷 IP 범위와 포트(80/443)를 지정할 수 있습니다. ASE가 제대로 작동하는 데 [필요한 NSG 규칙](../environment/network-info.md#network-security-groups)은 재정의해서는 안 됩니다.

개별 웹 앱에 대한 트래픽을 격리하려면 IP 기반 액세스 제한을 사용해야 합니다. 서비스 엔드포인트가 ASE에 작동하지 않기 때문입니다. IP 주소는 Application Gateway 인스턴스의 개인 IP여야 합니다.

## <a name="considerations-for-external-ase"></a>외부 ASE 고려 사항
외부 ASE에는 다중 테넌트 App Service 같은 공용 연결 부하 분산 장치가 있습니다. 서비스 엔드포인트가 ASE에 대해 작동하지 않으므로 Application Gateway 인스턴스의 공용 IP를 사용하여 IP 기반 액세스 제한을 사용해야 합니다. Azure Portal을 사용하여 외부 ASE를 만들려면 이 [빠른 시작](../environment/create-external-ase.md) 가이드를 따르면 됩니다.

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.web/web-app-with-app-gateway-v2/ "전체 시나리오에 대한 Azure Resource Manager 템플릿"

## <a name="considerations-for-kuduscm-site"></a>Kudu/scm 사이트 고려 사항
Kudu라고도 하는 scm 사이트는 모든 웹 앱에 존재하는 관리 사이트입니다. scm 사이트를 역방향으로 프록시하는 것은 불가능하며, 개별 IP 주소 또는 특정 서브넷으로 차단해야 할 가능성이 높습니다.

기본 사이트와 동일한 액세스 제한을 사용하려는 경우 다음 명령을 사용하여 설정을 상속할 수 있습니다.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

scm 사이트에 대한 개별 액세스 제한을 설정하려면 아래와 같이--scm-사이트 플래그를 사용하여 액세스 제한을 추가하면 됩니다.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>다음 단계
App Service Environment에 대한 자세한 내용은 [App Service Environment 설명서](../environment/index.yml)를 참조하세요.

[Azure Web Application Firewall 설명서](../../web-application-firewall/ag/ag-overview.md)에 나오는 Application Gateway의 Web Application Firewall에 관한 내용을 참조하여 웹 앱을 더 안전하게 보호할 수 있습니다.