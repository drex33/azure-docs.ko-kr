---
title: Application Gateway 사용하여 인터넷에 애플리케이션 노출
titleSuffix: Azure Spring Cloud
description: Application Gateway 사용하여 애플리케이션을 인터넷에 노출하는 방법
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: e23503c7e38009a8ac6b5da82dd17e26c7aeeaee
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404681"
---
# <a name="expose-applications-to-the-internet-using-application-gateway"></a>Application Gateway 사용하여 인터넷에 애플리케이션 노출

이 문서에서는 Application Gateway 사용하여 인터넷에 애플리케이션을 노출하는 방법을 설명합니다. Azure Spring Cloud 서비스 인스턴스를 가상 네트워크에 배포하는 경우 서비스 인스턴스의 애플리케이션은 비공개 네트워크에서만 액세스할 수 있습니다. 인터넷에서 애플리케이션에 액세스할 수 있도록 하려면 Azure Application Gateway 통합해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI 버전 2.0.4 이상](/cli/azure/install-azure-cli).
- 기본 도메인 접미사를 사용하여 프라이빗 네트워크를 통해 액세스할 수 있는 애플리케이션을 사용하여 가상 네트워크에 배포된 Azure Spring Cloud 서비스 `.private.azuremicroservices.io` 인스턴스입니다. 자세한 내용은 [가상 네트워크에 Azure Spring Cloud 배포를 참조하세요.](./how-to-deploy-in-azure-virtual-network.md)
- 애플리케이션에 액세스하는 데 사용할 사용자 지정 도메인입니다.
- Key Vault 저장된 인증서로, HTTPS 수신기를 설정하는 데 사용할 사용자 지정 도메인과 일치합니다. 자세한 내용은 [자습서: Azure Key Vault 인증서 가져오기를](../key-vault/certificates/tutorial-import-certificate.md)참조하세요.

## <a name="configure-application-gateway-for-azure-spring-cloud"></a>Azure Spring Cloud 대한 Application Gateway 구성

브라우저에서 볼 수 있듯이 도메인 이름은 Application Gateway 트래픽을 Azure Spring Cloud 백 엔드로 안내하는 데 사용하는 호스트 이름과 동일한 것이 좋습니다. 이 권장 사항은 Application Gateway 사용하여 Azure Spring Cloud 호스트되고 가상 네트워크에 상주하는 애플리케이션을 노출할 때 최상의 환경을 제공합니다. Application Gateway 의해 노출되는 도메인이 Azure Spring Cloud 허용하는 도메인과 다른 경우 쿠키 및 생성된 리디렉션 URL(예: )이 손상될 수 있습니다.

Azure Spring Cloud 앞에서 Application Gateway 구성하려면 다음 단계를 사용합니다.

1. [가상 네트워크에 Azure Spring Cloud 배포의](./how-to-deploy-in-azure-virtual-network.md)지침을 따릅니다.
1. 선택한 도메인에 대한 인증서를 획득하고 Key Vault 저장합니다. 자세한 내용은 [자습서: Azure Key Vault 인증서 가져오기를](../key-vault/certificates/tutorial-import-certificate.md)참조하세요.
1. Azure Spring Cloud 배포된 앱의 Key Vault 사용자 지정 도메인 및 해당 인증서를 구성합니다. 자세한 내용은 [자습서: 기존 사용자 지정 도메인을 Azure Spring Cloud 매핑을](./tutorial-custom-domain.md)참조하세요.
1. 다음 목록에 따라 구성된 가상 네트워크에 Application Gateway 배포합니다.
   - 접미사가 인 도메인에서 참조하는 백 엔드 풀의 Azure Spring Cloud `private.azuremicroservices.io` 사용합니다.
   - Key Vault 동일한 인증서를 사용하여 HTTPS 수신기를 포함합니다.
   - 접미사가 붙는 도메인 대신 Azure Spring Cloud 구성된 사용자 지정 도메인 이름을 사용하는 HTTP 설정으로 가상 네트워크를 `private.azuremicroservices.io` 구성합니다.
1. Application Gateway 가리키도록 공용 DNS를 구성합니다.

## <a name="define-variables"></a>변수 정의

다음으로, 다음 명령을 사용하여 가상 네트워크에 [Azure Spring Cloud 배포에서](./how-to-deploy-in-azure-virtual-network.md)지시한 대로 만든 리소스 그룹 및 가상 네트워크에 대한 변수를 정의합니다. 실제 환경에 따라 값을 사용자 지정합니다. 를 정의할 때 `SPRING_APP_PRIVATE_FQDN` `https://` URI에서 를 제거합니다.

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_CLOUD_NAME='name-of-spring-cloud-instance'
APPNAME='name-of-app-in-azure-spring-cloud'
SPRING_APP_PRIVATE_FQDN='$APPNAME.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

다음 명령을 사용하여 Azure CLI 로그인하고 활성 구독을 선택합니다.

```azurecli
az login
az account set --subscription $SUBSCRIPTION
```

## <a name="acquire-a-certificate"></a>인증서 획득

### <a name="use-a-publicly-signed-certificate"></a>[공개적으로 서명된 인증서 사용](#tab/public-cert)

프로덕션 배포의 경우 공개적으로 서명된 인증서를 사용할 가능성이 높습니다. 이 경우 Azure Key Vault 인증서를 가져옵니다. 자세한 내용은 [자습서: Azure Key Vault 인증서 가져오기를](../key-vault/certificates/tutorial-import-certificate.md)참조하세요. 인증서에 전체 인증서 체인이 포함되어 있는지 확인합니다.

### <a name="use-a-self-signed-certificate"></a>[자체 서명된 인증서 사용](#tab/self-signed-cert)

테스트 또는 개발을 위해 자체 서명된 인증서가 필요한 경우 인증서를 만들어야 합니다. 또한 인증서의 "주체 대체 이름" 목록에 애플리케이션을 노출할 도메인 이름이 포함되어 있는지 확인해야 합니다. Azure Key Vault 통해 자체 서명된 인증서를 만들 때 Azure Portal 통해 만들 수 있습니다. 또는 Azure CLI 사용하는 경우 정책 JSON 파일이 필요합니다.

기본 정책을 요청하려면 다음 명령을 사용합니다.

```azurecli
az keyvault certificate get-default-policy
```

다음으로, 및 을 나타내는 다음 예제와 같이 정책 JSON을 조정합니다. `subject` `SubjectAlternativeNames`

```json
{
  // ...
    "subject": "C=US, ST=WA, L=Redmond, O=Contoso, OU=Contoso HR, CN=myapp.mydomain.com",
    "subjectAlternativeNames": {
        "dnsNames": [
            "myapp.mydomain.com",
            "*.myapp.mydomain.com"
        ],
        "emails": [
            "hello@contoso.com"
        ],
          "upns": []
      }
  // ...
}
```

JSON 정책 업데이트를 완료한 후(인증서 [정책 업데이트](/rest/api/keyvault/update-certificate-policy/update-certificate-policy)참조) 다음 명령을 사용하여 Key Vault 자체 서명된 인증서를 만들 수 있습니다.

```azurecli
KV_NAME='name-of-key-vault'
CERT_NAME_IN_KV='name-of-certificate-in-key-vault'

az keyvault certificate create \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --policy "$KV_CERT_POLICY"
```

---

## <a name="configure-the-public-domain-name-on-azure-spring-cloud"></a>Azure Spring Cloud 공용 도메인 이름 구성

트래픽은 공용 도메인 이름을 사용하여 Azure Spring Cloud 배포된 애플리케이션으로 들어갑니다. 이 호스트 이름을 수신 대기하고 HTTPS를 통해 수신하도록 애플리케이션을 구성하려면 다음 명령을 사용하여 앱에 사용자 지정 도메인을 추가합니다.

```azurecli
KV_NAME='name-of-key-vault'
KV_RG='resource-group-name-of-key-vault'
CERT_NAME_IN_ASC='name-of-certificate-in-Azure-Spring-Cloud'
CERT_NAME_IN_KV='name-of-certificate-with-intermediaries-in-key-vault'
DOMAIN_NAME=myapp.mydomain.com

# provide permissions to ASC to read the certificate from Key Vault:
VAULTURI=$(az keyvault show -n $KV_NAME -g $KV_RG --query properties.vaultUri -o tsv)

# get the object id for the Azure Spring Cloud Domain-Management Service Principal:
ASCDM_OID=$(az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId --output tsv)

# allow this Service Principal to read and list certificates and secrets from Key Vault:
az keyvault set-policy -g $KV_RG -n $KV_NAME  --object-id $ASCDM_OID --certificate-permissions get list --secret-permissions get list

# add custom domain name and configure TLS using the certificate:
az spring-cloud certificate add \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --name $CERT_NAME_IN_ASC \
    --vault-certificate-name $CERT_NAME_IN_KV \
    --vault-uri $VAULTURI
az spring-cloud app custom-domain bind \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --domain-name $DOMAIN_NAME \
    --certificate $CERT_NAME_IN_ASC \
    --app $APPNAME
```

> [!NOTE]
> Application Gateway TLS 종료를 수행하려는 경우 개발 및 테스트 목적으로 Azure Spring Cloud 인증서를 구성하거나 Azure Spring Cloud Key Vault 읽을 수 있는 권한을 제공할 필요가 없습니다. 다음 명령을 사용하여 수행할 수 있는 도메인 이름을 바인딩하기만 하면 됩니다.
>
> ```azurecli
> az spring-cloud app custom-domain bind \
>     --resource-group $RESOURCE_GROUP \
>     --service $SPRING_CLOUD_NAME \
>     --domain-name $DOMAIN_NAME \
>     --app $APPNAME
> ```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

생성되게 될 Azure Application Gateway는 Azure Spring Cloud 서비스 인스턴스와 동일한 가상 네트워크에 조인하거나 Azure Spring Cloud 서비스 인스턴스에 대한 피어링된 가상 네트워크에 조인합니다. 먼저 `az network vnet subnet create`를 사용하여 가상 네트워크에 Application Gateway에 대한 새 서브넷을 만들고 `az network public-ip create`를 사용하여 Application Gateway의 프런트 엔드로 공용 IP 주소도 만듭니다.

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name $APPLICATION_GATEWAY_SUBNET_NAME \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --address-prefix $APPLICATION_GATEWAY_SUBNET_CIDR
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-a-managed-identity-for-application-gateway"></a>Application Gateway 대한 관리 ID 만들기

Application Gateway 인증서를 읽기 위해 Key Vault 액세스할 수 있어야 합니다. 이렇게 하려면 사용자 할당 관리 [ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용합니다. 다음 명령을 사용하여 관리 ID를 만듭니다.

```azurecli
APPGW_IDENTITY_NAME='name-for-appgw-managed-identity'
az identity create \
    --resource-group $RESOURCE_GROUP \
    --name $APPGW_IDENTITY_NAME
```

그런 다음 나중에 Key Vault 인증서에 액세스할 수 있는 권한을 부여하는 데 사용되기 때문에 관리 ID에 대한 objectId를 가져옵니다.

```azurecli
APPGW_IDENTITY_CLIENTID=$(az identity show --resource-group $RESOURCE_GROUP --name $APPGW_IDENTITY_NAME --query clientId --output tsv)
APPGW_IDENTITY_OID=$(az ad sp show --id $APPGW_IDENTITY_CLIENTID --query objectId --output tsv)
```

## <a name="set-policy-on-key-vault"></a>Key Vault 정책 설정

Application Gateway 대한 관리 ID가 Key Vault 저장된 인증서에 액세스할 수 있도록 다음 명령을 사용하여 Key Vault 구성합니다.

```azurecli
az keyvault set-policy \
    --name $KV_NAME \
    --resource-group $KV_RG \
    --object-id $APPGW_IDENTITY_OID \
    --secret-permissions get list \
    --certificate-permissions get list
```

## <a name="create-application-gateway"></a>Application Gateway 만들기

`az network application-gateway create`를 사용하여 애플리케이션 게이트웨이를 만들고 애플리케이션의 비공개 FQD(정규화된 도메인 이름)을 백 엔드 풀에서 서버로 지정합니다. 사용자 할당 관리 ID를 사용하고 인증서의 비밀 ID를 사용하여 Key Vault 인증서를 가리킵니다. 그런 다음 를 사용하여 HTTP 설정을 `az network application-gateway http-settings update` 업데이트하여 공용 호스트 이름을 사용합니다.

```azurecli
APPGW_NAME='name-for-application-gateway'

KEYVAULT_SECRET_ID_FOR_CERT=$(az keyvault certificate show --name $CERT_NAME_IN_KV --vault-name $KV_NAME --query sid --output tsv)

az network application-gateway create \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --capacity 2 \
    --sku Standard_v2 \
    --frontend-port 443 \
    --http-settings-cookie-based-affinity Disabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --subnet $APPLICATION_GATEWAY_SUBNET_NAME \
    --servers $SPRING_APP_PRIVATE_FQDN \
    --key-vault-secret-id $KEYVAULT_SECRET_ID_FOR_CERT \
    --identity $APPGW_IDENTITY_NAME
```

Azure가 애플리케이션 게이트웨이를 만들 때까지 최대 30분이 걸릴 수 있습니다.

>[!NOTE]
> Application Gateway TLS 종료를 수행하려는 경우 개발 및 테스트 목적으로 를 `http-settings-port` `80` 및 로 `http-settings-protocol` `Https` 변경하고 "공개적으로 서명된 인증서 사용"에 대한 아래 지침을 계속 따릅니다.

### <a name="update-http-settings-to-use-the-domain-name-towards-the-backend"></a>백 엔드에 도메인 이름을 사용하도록 HTTP 설정 업데이트

#### <a name="use-a-publicly-signed-certificate"></a>[공개적으로 서명된 인증서 사용](#tab/public-cert-2)

".private.azuremicroservices.io" 접미사가 붙는 도메인 대신 공용 도메인 이름을 호스트 이름으로 사용하여 트래픽을 Azure Spring Cloud 보내도록 HTTP 설정을 업데이트합니다.

```azurecli
az network application-gateway http-settings update \
    --resource-group $RESOURCE_GROUP \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings
```

#### <a name="use-a-self-signed-certificate"></a>[자체 서명된 인증서 사용](#tab/self-signed-cert-2)

".private.azuremicroservices.io" 접미사가 붙는 도메인 대신 공용 도메인 이름을 호스트 이름으로 사용하여 트래픽을 Azure Spring Cloud 보내도록 HTTP 설정을 업데이트합니다. 자체 서명된 인증서가 사용되면 Application Gateway HTTP 설정 허용 목록에 추가되어야 합니다.

인증서를 허용 목록에 추가하려면 먼저 다음 명령을 사용하여 Key Vault 인증서의 공개 부분을 가져옵니다.

```azurecli
az keyvault certificate download \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --file ./selfsignedcert.crt \
    --encoding DER
```

그런 다음, 다음 명령을 사용하여 Application Gateway 업로드합니다.

```azurecli
az network application-gateway root-cert create \
    --resource-group $RG \
    --cert-file ./selfsignedcert.crt \
    --gateway-name $APPGW_NAME \
    --name MySelfSignedTrustedRootCert
```

이제 다음 명령을 사용하여 HTTP 설정 업데이트하여 새(자체 서명된) 루트 인증서를 신뢰할 수 있습니다.

```azurecli
az network application-gateway http-settings update \
    --resource-group $RG \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings \
    --root-certs MySelfSignedTrustedRootCert
```

---

### <a name="check-the-deployment-of-application-gateway"></a>Application Gateway 배포 확인

만든 후 다음 명령을 사용하여 백 엔드 상태를 확인합니다. 이 명령의 출력을 사용하면 애플리케이션 게이트웨이가 프라이빗 FQDN을 통해 애플리케이션에 도달하는지 여부를 확인할 수 있습니다.

```azurecli
az network application-gateway show-backend-health \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP
```

출력은 다음 예제와 같이 백 엔드 풀의 정상 상태를 나타냅니다.

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

## <a name="configure-dns-and-access-the-application"></a>DNS 구성 및 애플리케이션 액세스

이제 CNAME 또는 A 레코드를 사용하여 Application Gateway 가리키도록 공용 DNS를 구성합니다. 다음 명령을 사용하여 Application Gateway 공용 주소를 찾을 수 있습니다.

```azurecli
az network public-ip show \
    --resource-group $RESOURCE_GROUP \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --query [ipAddress] \
    --output tsv
```

이제 공용 도메인 이름을 사용하여 애플리케이션에 액세스할 수 있습니다.

## <a name="see-also"></a>참고 항목

- [VNET에서 Azure Spring Cloud 문제 해결](./troubleshooting-vnet.md)
- [VNET에서 Azure Spring Cloud를 실행하는 고객의 책임](./vnet-customer-responsibilities.md)
