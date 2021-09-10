---
title: TLS로 웹 서비스 보호
titleSuffix: Azure Machine Learning
description: TLS 버전 1.2로 HTTPS를 사용하도록 설정하여 Azure Machine Learning을 통해 배포된 웹 서비스를 보호하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jhirono
author: jhirono
ms.date: 07/07/2021
ms.topic: how-to
ms.openlocfilehash: 10eb9d57b19968737077a595030bb2a986ec6c7b
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564791"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호


이 문서에서는 Azure Machine Learning을 통해 배포된 웹 서비스를 보호하는 방법을 보여줍니다.

[HTTPS](https://en.wikipedia.org/wiki/HTTPS)를 사용하여 웹 서비스 액세스를 제한하고 클라이언트가 제출하는 데이터를 보호합니다. HTTPS는 둘 간의 통신을 암호화하여 클라이언트와 웹 서비스 간의 통신을 보안하는데 도움이 됩니다. 암호화는 [TLS(전송 계층 보안)](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용합니다. TLS는 TLS의 선행 작업인 *SSL(Secure Sockets Layer)* (SSL)이라고도 합니다.

> [!TIP]
> Azure Machine Learning SDK는 통신 보호와 관련된 속성에 "SSL"이라는 용어를 사용합니다. 그렇더라도 웹 서비스에 *TLS* 가 사용되지 않는 것은 아닙니다. 단지 SSL이 조금 더 일반적으로 사용되는 용어일 뿐입니다.
>
> 특히 Azure Machine Learning을 통해 배포된 웹 서비스는 AKS 및 ACI에 대해 TLS 버전 1.2를 지원합니다. ACI 배포의 경우 이전 TLS 버전을 사용하는 경우 최신 TLS 배포를 사용하도록 다시 배포하는 것이 좋습니다.
>
> Azure Machine Learning용 TLS 버전 1.3 - AKS 유추는 지원되지 않습니다.

TLS 및 SSL 모두 암호화 및 ID 확인에 도움이 되는 *디지털 인증서* 가 사용됩니다. 디지털 인증서의 작동 방식에 대해서는 Wikipedia 토픽 [공개 키 인프라](https://en.wikipedia.org/wiki/Public_key_infrastructure)를 참조하세요.

> [!WARNING]
> 웹 서비스에 HTTPS를 사용하지 않으면 서비스에 대해 전송되는 데이터가 인터넷의 다른 사람에게 보여질 수 있습니다.
>
> 또한 클라이언트는 HTTPS를 통해 자신이 연결하는 대상 서버의 진위성을 확인할 수 있습니다. 이 기능은 [중간자(man-in-the-middle)](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) 공격으로부터 클라이언트를 보호합니다.

웹 서비스 보안을 위한 일반적인 프로세스는 다음과 같습니다.

1. 도메인 이름을 가져옵니다.

2. 디지털 인증서를 가져옵니다.

3. TLS를 사용하도록 설정하여 웹 서비스를 배포하거나 업데이트합니다.

4. 웹 서비스를 가리키도록 DNS를 업데이트합니다.

> [!IMPORTANT]
> AKS(Azure Kubernetes Service)에 배포하는 경우 고유 인증서를 구입하거나 Microsoft에서 제공되는 인증서를 사용할 수 있습니다. Microsoft의 인증서를 사용할 경우 도메인 이름 또는 TLS/SSL 인증서를 얻을 필요가 없습니다. 자세한 내용은 이 문서의 [TLS 사용 및 배포](#enable) 섹션을 참조하세요.

[배포 대상](how-to-deploy-and-where.md) 간에 보안 시 약간의 차이가 있습니다.

## <a name="get-a-domain-name"></a>도메인 이름 가져오기

고유 도메인 이름이 없으면 *도메인 이름 등록자* 에게서 하나 구입합니다. 프로세스 및 가격은 등록자마다 다릅니다. 등록자는 도메인 이름 관리 도구를 제공합니다. 이러한 도구를 사용하여 FQDN(정규화된 도메인 이름)(예: www\.contoso.com)을 웹 서비스를 호스트하는 IP 주소에 매핑합니다.

## <a name="get-a-tlsssl-certificate"></a>TLS/SSL 인증서를 받습니다.

TLS/SSL 인증서(디지털 인증서)는 여러 방법으로 얻을 수 있습니다. 가장 일반적인 방법은 CA(*인증 기관*)에서 구입하는 것입니다. 인증서를 어디에서 얻든 다음과 같은 파일이 필요합니다.

* **인증서**. 인증서는 전체 인증서 체인을 포함해야 하며 “PEM으로 인코딩”되어야 합니다.
* **키**. 또한 키도 PEM으로 인코딩되어야 합니다.

인증서를 요청할 때는 웹 서비스에 사용하려는 주소의 FQDN(예: www\.contoso.com)을 제공해야 합니다. 인증서에 적힌 주소와 클라이언트에 사용되는 주소를 비교하여 웹 서비스의 ID를 확인합니다. 이러한 주소가 일치하지 않으면 클라이언트에 오류 메시지가 표시됩니다.

> [!TIP]
> 인증 기관이 인증서와 키를 PEM으로 인코딩된 파일로 제공할 수 없는 경우 [OpenSSL](https://www.openssl.org/)과 같은 유틸리티를 사용하여 형식을 변경할 수 있습니다.

> [!WARNING]
> *자체 서명된* 인증서는 개발에만 사용해야 합니다. 프로덕션 환경에서는 사용하지 마세요. 자체 서명된 인증서로 인해 클라이언트 애플리케이션에 문제가 발생할 수 있습니다. 자세한 내용은 클라이언트 애플리케이션에 사용되는 네트워크 라이브러리에 대한 설명서를 참조하세요.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> TLS를 사용하도록 설정하고 배포

**AKS 배포** 의 경우 AML 작업 영역에서 [AKS 클러스터를 만들거나 연결할 때](how-to-create-attach-kubernetes.md) TLS 종료를 사용하도록 설정할 수 있습니다. AKS 모델 배포 시 배포 구성 개체로 TLS 종료를 사용하지 않도록 설정할 수 있습니다. 그렇지 않으면 기본적으로 AKS 클러스터를 만들거나 연결할 때 모든 AKS 모델 배포 시에 TLS 종료가 사용하도록 설정됩니다.

ACI 배포의 경우 배포 구성 개체를 사용하여 모델 배포 시 TLS 종료를 사용하도록 설정할 수 있습니다.


### <a name="deploy-on-azure-kubernetes-service"></a>Azure Kubernetes Service에서 배포

  > [!NOTE]
  > 이 섹션의 정보는 디자이너에 대해 보안 웹 서비스를 배포할 때에도 적용됩니다. Python SDK 사용에 익숙하지 않으면 [Pytho용 Azure Machine Learning SDK란 무엇인가요?](/python/api/overview/azure/ml/intro)를 참조하세요.

AML 작업 영역에서 [AKS 클러스터를 만들거나 연결](how-to-create-attach-kubernetes.md)할 때 **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 및 **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 구성 개체를 사용하여 TLS 종료를 사용하도록 설정할 수 있습니다. 두 메서드 모두 **enable_ssl** 메서드가 있는 구성 개체를 반환하며 **enable_ssl** 메서드를 사용하여 TLS를 활성화할 수 있습니다.

Microsoft 인증서 또는 CA에서 구입한 사용자 지정 인증서로 TLS를 사용하도록 설정할 수 있습니다. 

* **Microsoft의 인증서를 사용하는 경우**, *leaf_domain_label* 매개 변수를 사용해야 합니다. 이 매개 변수는 서비스에 대해 DNS 이름을 생성합니다. 예를 들어 "contoso" 값은 "contoso\<six-random-characters>.\<azureregion>.cloudapp.azure.com"이라는 도메인 이름을 생성하고, 여기서 \<azureregion>은 서비스가 포함된 지역입니다. 선택적으로 *overwrite_existing_domain* 매개 변수를 사용하여 기존 *leaf_domain_label* 을 덮어쓸 수 있습니다. 다음 예제에서는 Microsoft 인증서로 TLS를 사용하도록 설정하는 구성을 만드는 방법을 보여줍니다.

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Microsoft의 인증서를 사용하는 경우에는 고유 인증서 또는 도메인 이름을 구입할 필요가 없습니다.

    > [!WARNING]
    > AKS 클러스터가 내부 부하 분산 장치로 구성된 경우에는 Microsoft 제공 인증서 사용이 __지원되지 않으며__ 사용자 지정 인증서를 사용하여 TLS를 사용하도록 설정해야 합니다.

* **구입한 사용자 지정 인증서를 사용할 때** 는 *ssl_cert_pem_file*, *ssl_key_pem_file* 및 *ssl_cname* 매개 변수를 사용합니다. 다음 예제에서는 .pem 파일을 사용해서 구입한 TLS/SSL 인증서를 사용하는 구성을 만드는 방법을 보여줍니다.
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

*enable_ssl* 에 대한 자세한 내용은 [AksProvisioningConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 및 [AksAttachConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)을 참조하세요.

### <a name="deploy-on-azure-container-instances"></a>Azure Container Instances에서 배포

Azure Container Instances를 배포할 때는 다음 코드 조각에 표시된 것처럼 TLS 관련 매개 변수에 대해 값을 제공합니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

자세한 내용은 [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)을 참조하세요.

## <a name="update-your-dns"></a>DNS 업데이트

사용자 지정 인증서가 사용되는 AKS 배포 또는 ACI 배포의 경우 엔드포인트 점수 매기기의 IP 주소를 가리키도록 DNS 레코드를 업데이트해야 합니다.

> [!IMPORTANT]
> AKS 배포를 위해 Microsoft의 인증서를 사용하는 경우 클러스터에 대해 DNS 값을 수동으로 업데이트할 필요가 없습니다. 값이 자동으로 설정됩니다.

다음 단계에 따라 사용자 지정 도메인 이름에 대해 DNS 레코드를 업데이트할 수 있습니다.
1. 엔드포인트 점수 매기기 URI에서 엔드포인트 점수 매기기 IP 주소를 가져옵니다. 일반적으로 *http://104.214.29.152:80/api/v1/service/<service-name>/score* 형식입니다. 이 예제에서 IP 주소는 104.214.29.152.입니다.
1. 도메인 이름 등록자의 도구를 사용하여 도메인 이름에 대해 DNS 레코드를 업데이트합니다. 레코드는 FQDN(예: www\.contoso.com)을 IP 주소에 매핑합니다. 레코드는 엔드포인트 점수 매기기의 IP 주소를 가리켜야 합니다.

    > [!TIP]
    > Microsoft는 사용자 지정 DNS 이름 또는 인증서에 대한 DNS를 업데이트할 책임이 없습니다. 도메인 이름 등록 기관으로 업데이트해야 합니다.

1. DNS 레코드 업데이트 후 *nslookup custom-domain-name* 명령을 사용하여 DNS 확인의 유효성을 검사할 수 있습니다. DNS 레코드가 올바르게 업데이트된 경우 사용자 지정 도메인 이름이 엔드포인트 점수 매기기의 IP 주소를 가리킵니다.

    등록자 및 도메인 이름에 대해 구성된 TTL(“Time To Live”)에 따라 클라이언트가 도메인 이름을 확인할 수 있으려면 몇 분 또는 몇 시간까지 지연될 수 있습니다.

Azure Machine Learning DNS 확인에 대한 자세한 내용은 [사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법](how-to-custom-dns.md)을 참조하세요.
## <a name="update-the-tlsssl-certificate"></a>TLS/SSL 인증서 업데이트

TLS/SSL 인증서가 만료되고 갱신되어야 합니다. 일반적으로 매년 발생합니다. 다음 섹션의 정보에 따라 Azure Kubernetes Service에 배포된 모델에 대해 인증서를 업데이트하고 갱신합니다.

### <a name="update-a-microsoft-generated-certificate"></a>Microsoft에서 생성된 인증서 업데이트

인증서가 원래 Microsoft에서 생성된 경우(서비스를 만들기 위해 *leaf_domain_label* 을 사용할 때), 필요할 때 **자동으로 갱신됩니다**. 이를 수동으로 갱신하려면 다음 예시 중 하나에 따라 인증서를 업데이트합니다.

> [!IMPORTANT]
> * 기존 인증서가 아직 유효하면 `renew=True`(SDK) 또는 `--ssl-renew`(CLI)를 사용하여 이를 갱신하도록 구성을 적용합니다. 예를 들어 기존 인증서가 아직 10일 동안 유효하고 `renew=True`를 사용하지 않으면 인증서가 갱신되지 않을 수 있습니다.
> * 서비스가 처음 배포되었을 때 `leaf_domain_label`은 `<leaf-domain-label>######.<azure-region>.cloudapp.azure.com` 패턴을 사용하여 DNS 이름을 만들기 위해 사용됩니다. 기존 이름(원래 생성된 6자리 숫자 포함)을 보존하려면 원래 `leaf_domain_label` 값을 사용합니다. 생성된 6자리 숫자는 포함하지 마세요.

**SDK 사용**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**CLI 사용**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

자세한 내용은 다음 참조 문서를 확인하세요.

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

### <a name="update-custom-certificate"></a>사용자 지정 인증서 업데이트

인증서가 원래 인증 기관에서 생성된 경우 다음 단계를 따릅니다.

1. 인증 기관에서 제공된 문서를 사용하여 인증서를 갱신합니다. 이 프로세스는 새 인증서 파일을 만듭니다.

1. SDK 또는 CLI를 사용하여 새 인증서로 서비스를 업데이트합니다.

    **SDK 사용**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **CLI 사용**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

자세한 내용은 다음 참조 문서를 확인하세요.

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

## <a name="disable-tls"></a>TLS 사용 안 함

Azure Kubernetes Service에 배포된 모델에 대해 TLS를 사용하지 않도록 설정하려면 `status="Disabled"`로 `SslConfiguration`을 만든 후 업데이트를 수행합니다.

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>다음 단계
방법 배우기:
+ [웹 서비스로 배포된 기계 학습 모델 사용](how-to-consume-web-service.md)
+ [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)
+ [사용자 지정 DNS 서버에서 작업 영역을 사용하는 방법](how-to-custom-dns.md)