---
title: 가상 네트워크에서 Azure Spring Cloud 액세스
description: 가상 네트워크에서 Azure Spring Cloud 앱에 액세스하십시오.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 7ede1ecbbe86f0ec5ef3a79d38b42ff58b653b0b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005356"
---
# <a name="access-your-application-in-a-private-network"></a>개인 네트워크에서 애플리케이션에 액세스

이 문서에서는 프라이빗 네트워크에서 애플리케이션의 엔드포인트에 액세스하는 방법을 설명합니다.  액세스 권한을 얻으려면 프라이빗 FQDN(정규화된 도메인 이름)을 IP 주소로 변환/확인하기 위해 구독에서 **Azure 프라이빗 DNS 영역** 을 만들어야 합니다.

Azure Spring Cloud 서비스 인스턴스의 애플리케이션에 대한 **엔드포인트 할당** 을 가상 네트워크에 배포하는 경우 엔드포인트는 프라이빗 FQDN입니다. 도메인은 프라이빗 네트워크에서만 액세스할 수 있습니다. 앱과 서비스는 애플리케이션 엔드포인트를 사용합니다. 여기에는 [앱 및 배포 보기](./how-to-staging-environment.md#view-apps-and-deployments)에 설명된 **테스트 엔드포인트가** 포함됩니다. [실시간으로 Azure Spring Cloud 앱 로그 스트림](./how-to-log-streaming.md)에 설명된 **로그 스트리밍** 도 프라이빗 네트워크 내에서만 작동합니다.

## <a name="create-a-private-dns-zone"></a>프라이빗 DNS 영역 만들기

다음 절차에서는 프라이빗 네트워크의 애플리케이션에 대한 프라이빗 DNS 영역을 만듭니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal을 엽니다. 위쪽 검색 상자에서 **프라이빗 DNS 영역** 을 검색하고 결과에서 **프라이빗 DNS 영역** 을 선택합니다.

2. **프라이빗 DNS 영역** 페이지에서 **추가** 를 선택합니다.

3. **프라이빗 DNS 영역 만들기** 페이지에서 양식을 작성합니다. 영역 **이름** 으로 **<span>private.azuremicroservices.io</span>** 를 입력합니다.

    >[!NOTE]
    > Azure China를 사용하는 경우, 전체 문서에서 `private.azuremicroservices.io`을 `private.microservices.azure.cn`로 바꿉니다. [자세히 알아보기](/azure/china/resources-developer-guide#check-endpoints-in-azure).

4. **검토 + 만들기** 를 선택합니다.

5. **만들기** 를 선택합니다.

#### <a name="cli"></a>[CLI](#tab/azure-CLI)

1. 구독, 리소스 그룹 및 Azure Spring Cloud 인스턴스에 대한 변수를 정의합니다. 실제 환경에 따라 값을 사용자 지정합니다.

   ```azurecli
   SUBSCRIPTION='subscription-id'
   RESOURCE_GROUP='my-resource-group'
   VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
   ```

1. Azure CLI에 로그인하고 활성 구독을 선택합니다.

   ```azurecli
   az login
   az account set --subscription ${SUBSCRIPTION}
   ```

1. 프라이빗 DNS 영역을 만듭니다. 

   ```azurecli
   az network private-dns zone create --resource-group $RESOURCE_GROUP \
      --name private.azuremicroservices.io
   ```

---

영역을 만드는 데 몇 분이 걸릴 수 있습니다.

## <a name="link-the-virtual-network"></a>가상 네트워크 연결

가상 네트워크에 프라이빗 DNS 영역을 연결하려면 가상 네트워크 링크를 만들어야 합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 위에서 만든 프라이빗 DNS 영역 리소스를 선택합니다. **<span>private.azuremicroservices.io</span>**

2. 왼쪽 창에서 **가상 네트워크 링크** 를 선택합니다.

3. **추가** 를 선택합니다.

4. **링크 이름** 으로 **azure-spring-cloud-dns-link** 를 입력합니다.

5. **가상 네트워크** 로는 [Azure 가상 네트워크에 Azure Spring Cloud 배포(VNet 삽입)](./how-to-deploy-in-azure-virtual-network.md)에 설명된 대로 만든 가상 네트워크를 선택합니다.

    ![가상 네트워크 링크 추가](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. **확인** 을 선택합니다.

#### <a name="cli"></a>[CLI](#tab/azure-CLI)

방금 만든 프라이빗 DNS 영역을 Azure Spring Cloud 서비스가 있는 가상 네트워크에 연결합니다.

   ```azurecli
   az network private-dns link vnet create --resource-group $RESOURCE_GROUP \
       --name azure-spring-cloud-dns-link \
       --zone-name private.azuremicroservices.io \
       --virtual-network $VIRTUAL_NETWORK_NAME \
       --registration-enabled false
   ```

---

## <a name="create-dns-record"></a>DNS 레코드 만들기

DNS를 변환/확인하는 데 프라이빗 DNS 영역을 사용하려면 영역에 "A" 형식 레코드를 만들어야 합니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure 가상 네트워크에 Azure Spring Cloud 배포(VNet 삽입)](./how-to-deploy-in-azure-virtual-network.md)에 설명된 대로 만든 가상 네트워크 리소스를 선택합니다.

2. **연결된 디바이스** 검색 상자에 *kubernetes-internal* 을 입력합니다.

3. 필터링된 결과에서 서비스 인스턴스의 서비스 런타임 **서브넷** 에 연결된 **디바이스** 를 찾아 해당 **IP 주소** 를 복사합니다. 이 샘플에서 IP 주소는 *10.1.0.7* 입니다.

    [ ![DNS 레코드 만들기](media/spring-cloud-access-app-vnet/create-dns-record.png) ](media/spring-cloud-access-app-vnet/create-dns-record.png)

또는 다음 az CLI 명령을 사용하여 IP를 가져올 수 있습니다.

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. 위에서 만든 프라이빗 DNS 영역 리소스를 선택합니다. **<span>private.azuremicroservices.io</span>** .

5. **레코드 집합** 을 선택합니다.

6. **레코드 집합 추가** 에서 다음 정보를 입력하거나 선택합니다.

    |설정     |값                                                                      |
    |------------|---------------------------------------------------------------------------|
    |속성        |*\** 를 입력합니다.                                                                 |
    |형식        |**A** 선택                                                               |
    |TTL         |*1* 입력                                                                  |
    |TTL 단위    |**시간** 선택                                                           |
    |IP 주소  |3단계에서 복사한 IP 주소를 입력합니다. 샘플에서 *10.1.0.7* 을 입력합니다.    |

    그런 다음, **확인** 을 선택합니다.

    ![프라이빗 DNS 영역 레코드 추가](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

#### <a name="cli"></a>[CLI](#tab/azure-CLI)

1. Spring Cloud 서비스의 IP 주소를 찾습니다. 실제 환경에 따라 Spring Cloud 이름의 값을 사용자 지정합니다.

   ```azurecli
   SPRING_CLOUD_NAME='spring-cloud-name'
   SERVICE_RUNTIME_RG=`az spring-cloud show --resource-group $RESOURCE_GROUP \
       --name $SPRING_CLOUD_NAME --query \
       "properties.networkProfile.serviceRuntimeNetworkResourceGroup" \
       --output tsv`
   IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal \
       --resource-group $SERVICE_RUNTIME_RG \
       --query "[0].privateIpAddress" \
       --output tsv`
   ```

1. 이 IP 주소를 사용하여 DNS 영역에 A 레코드를 만듭니다. 

   ```azurecli
   az network private-dns record-set a add-record \
     --resource-group $RESOURCE_GROUP \
     --zone-name private.azuremicroservices.io \
     --record-set-name '*' \
     --ipv4-address $IP_ADDRESS
   ```

---

## <a name="assign-private-fqdn-for-your-application"></a>애플리케이션에 대한 프라이빗 FQDN 할당

[마이크로 서비스 애플리케이션 빌드 및 배포](./how-to-deploy-in-azure-virtual-network.md)의 절차를 수행하면 애플리케이션에 대한 프라이빗 FQDN을 할당할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. 가상 네트워크에 배포된 Azure Spring Cloud 서비스 인스턴스를 선택하고 왼쪽 메뉴에서 **앱** 탭을 엽니다.

2. 애플리케이션을 선택하여 **개요** 페이지를 표시합니다.

3. **엔드포인트 할당** 을 선택하여 프라이빗 FQDN을 애플리케이션에 할당합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    ![프라이빗 엔드포인트 할당](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. 이제 할당된 프라이빗 FQDN(레이블이 지정된 **URL**)을 사용할 수 있습니다. 이 파일은 프라이빗 네트워크 내에서만 액세스할 수 있으며 인터넷에서는 액세스할 수 없습니다.

#### <a name="cli"></a>[CLI](#tab/azure-CLI)

엔드포인트를 할당하도록 앱을 업데이트합니다. 실제 환경에 따라 Spring 앱 이름의 값을 사용자 지정합니다.

```azurecli
SPRING_CLOUD_APP='your spring cloud app'
az spring-cloud app update --name $SPRING_CLOUD_APP \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --assign-endpoint true
```

---

## <a name="access-application-private-fqdn"></a>애플리케이션 프라이빗 FQDN 액세스

할당 후 프라이빗 네트워크에서 애플리케이션의 프라이빗 FQDN에 액세스할 수 있습니다. 예를 들어 동일한 가상 네트워크 또는 피어링된 가상 네트워크에서 Jumpbox 컴퓨터를 만들고 해당Jumpbox 컴퓨터에서 프라이빗 FQDN에 액세스할 수 있습니다.

![VNet에서 프라이빗 엔드포인트 액세스](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>다음 단계

- [애플리케이션을 인터넷에 노출 - Application Gateway 및 Azure Firewall 사용](./expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>참고 항목

- [VNET에서 Azure Spring Cloud 문제 해결](./troubleshooting-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](./vnet-customer-responsibilities.md)
