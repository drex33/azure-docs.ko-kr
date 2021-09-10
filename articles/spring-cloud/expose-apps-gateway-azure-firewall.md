---
title: Application Gateway 및 Azure Firewall을 사용하여 애플리케이션을 인터넷에 노출
description: Application Gateway 및 Azure Firewall을 사용하여 애플리케이션을 인터넷에 노출하는 방법
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: e87ccabeb2d0e0cd837a835c5ac637bebc68b8b4
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529511"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Application Gateway 및 Azure Firewall을 사용하여 애플리케이션을 인터넷에 노출

이 문서는 Application Gateway 및 Azure Firewall을 사용하여 애플리케이션을 인터넷에 노출하는 방법에 대해 설명합니다. Azure Spring Cloud 서비스 인스턴스를 가상 네트워크에 배포하는 경우 서비스 인스턴스의 애플리케이션은 비공개 네트워크에서만 액세스할 수 있습니다. 인터넷에서 애플리케이션에 액세스할 수 있도록 하려면 **Azure Application Gateway** 와 통합하고 선택에 따라 **Azure Firewall** 과 통합해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI 버전 2.0.4 이상](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>변수 정의

[Azure virtual network에서 Azure Spring Cloud 배포(VNet 삽입)](./how-to-deploy-in-azure-virtual-network.md)에서 지시한 대로 만든 리소스 그룹과 가상 네트워크의 변수를 정의합니다. 실제 환경에 따라 값을 사용자 지정합니다.  SPRING_APP_PRIVATE_FQDN을 정의할 때 URI에서 'https'를 제거합니다.

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure CLI에 로그인하고 활성 구독을 선택합니다.

```azurecli
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

생성되게 될 **Azure Application Gateway** 는 Azure Spring Cloud 서비스 인스턴스와 동일한 가상 네트워크에 조인하거나 Azure Spring Cloud 서비스 인스턴스에 대한 피어링된 가상 네트워크에 조인합니다. 먼저 `az network vnet subnet create`를 사용하여 가상 네트워크에 Application Gateway에 대한 새 서브넷을 만들고 `az network public-ip create`를 사용하여 Application Gateway의 프런트 엔드로 공용 IP 주소도 만듭니다.

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>애플리케이션 게이트웨이 만들기

`az network application-gateway create`를 사용하여 애플리케이션 게이트웨이를 만들고 애플리케이션의 비공개 FQD(정규화된 도메인 이름)을 백 엔드 풀에서 서버로 지정합니다. 그런 다음 백 엔드 풀의 호스트 이름을 사용하도록 `az network application-gateway http-settings update`를 사용하여 HTTP 설정을 업데이트합니다.

```azurecli
APPLICATION_GATEWAY_NAME='my-app-gw'
APPLICATION_GATEWAY_PROBE_NAME='my-probe'
APPLICATION_GATEWAY_REWRITE_SET_NAME='my-rewrite-set'
APPLICATION_GATEWAY_REWRITE_RULE_NAME='remove-request-header'
APPLICATION_GATEWAY_RULE_NAME='rule1'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway probe create \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PROBE_NAME} \
    --protocol https \
    --host-name-from-http-settings true \
    --path /
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true \
    --probe ${APPLICATION_GATEWAY_PROBE_NAME}
az network application-gateway rewrite-rule set create \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_REWRITE_SET_NAME}
az network application-gateway rewrite-rule create \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --rule-set-name ${APPLICATION_GATEWAY_REWRITE_SET_NAME} \
    --name ${APPLICATION_GATEWAY_REWRITE_RULE_NAME} \
    --request-headers X-Forwarded-Proto="https"
az network application-gateway rule update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_RULE_NAME} \
    --rewrite-rule-set ${APPLICATION_GATEWAY_REWRITE_SET_NAME}
```

Azure가 애플리케이션 게이트웨이를 만들 때까지 최대 30분이 걸릴 수 있습니다. 만든 후에는 `az network application-gateway show-backend-health`를 사용하여 백 엔드 상태를 확인합니다.  이를 통해 애플리케이션 게이트웨이가 해당 비공개 FQDN을 통해 애플리케이션에 도달하는지 여부를 검사할 수 있습니다.

```azurecli
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

출력은 백 엔드 풀의 정상 상태를 나타냅니다.

```output
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>애플리케이션 게이트웨이의 프런트 엔드 공용 IP를 사용하여 애플리케이션에 액세스

`az network public-ip show`를 사용하여 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다.

```azurecli
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

![공용 IP의 앱](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>참고 항목

- [VNET에서 Azure Spring Cloud 문제 해결](./troubleshooting-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](./vnet-customer-responsibilities.md)
