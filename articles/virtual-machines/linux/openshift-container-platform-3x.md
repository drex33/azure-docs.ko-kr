---
title: Azure에서 OpenShift Container Platform 3.11 배포
description: Azure에서 OpenShift Container Platform 3.11을 배포합니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 177cbeb76433fb43094822989cfc5c6a3abfadfd
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390670"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Azure에서 OpenShift Container Platform 3.11 배포

Azure에서 OpenShift Container Platform 3.11을 배포하는 몇 가지 방법 중 하나를 사용할 수 있습니다.

- 필요한 Azure 인프라 구성 요소를 수동으로 배포한 다음, [OpenShift Container Platform 설명서](https://docs.openshift.com/container-platform)를 따를 수 있습니다.
- OpenShift Container Platform 클러스터의 배포를 간소화하는 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-container-platform/)을 사용할 수도 있습니다.
- 다른 방법은 Azure Marketplace 제품을 사용하는 것입니다.

모든 옵션에 Red Hat 구독이 필요합니다. 배포하는 동안 Red Hat Enterprise Linux 인스턴스는 Red Hat 구독에 등록되고 OpenShift Container Platform에 대한 자격이 포함된 풀 ID에 연결됩니다.
유효한 RHSM(Red Hat Subscription Manager) 사용자 이름, 암호 및 풀 ID가 있어야 합니다. 활성화 키, 조직 ID 및 풀 ID를 사용할 수 있습니다. 이 정보는 https://access.redhat.com에 로그인하여 확인할 수 있습니다.


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>OpenShift Container Platform Resource Manager 3.11 템플릿을 사용하여 배포

### <a name="private-clusters"></a>프라이빗 클러스터

프라이빗 OpenShift 클러스터를 배포하려면 마스터 부하 분산 장치(웹 콘솔) 또는 인프라 부하 분산 장치(라우터)에 연결된 공용 IP가 하나 이상이 필요합니다.  프라이빗 클러스터는 일반적으로 사용자 지정 DNS 서버(기본 Azure DNS 아님), 사용자 지정 도메인 이름(예: contoso.com) 및 미리 정의된 가상 네트워크를 사용합니다.  프라이빗 클러스터의 경우 모든 적절한 서브넷과 DNS 서버 설정으로 가상 네트워크를 미리 구성해야 합니다.  그런 다음 **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference** 및 **existingNodeSubnetReference** 를 사용하여 클러스터에서 사용할 기존 서브넷을 지정합니다.

프라이빗 마스터를 선택한 경우(**masterClusterType**=private) **masterPrivateClusterIp** 에 대해 고정 개인 IP를 지정해야 합니다.  이 IP는 마스터 부하 분산 장치의 프런트 엔드에 할당됩니다.  IP는 마스터 서브넷의 CIDR 내에 있어야 하며 사용 중이 아니어야 합니다.  **masterClusterDnsType** 은 "사용자 지정"으로 설정되어야 하고 **masterClusterDns** 에 대해 마스터 DNS 이름을 제공해야 합니다.  DNS 이름은 고정 개인 IP에 매핑되어야 하며 마스터 노드의 콘솔에 액세스하는 데 사용됩니다.

프라이빗 라우터를 선택한 경우(**routerClusterType**=private) **routerPrivateClusterIp** 에 대해 고정 개인 IP를 지정해야 합니다.  이 IP는 인프라 부하 분산 장치의 프런트 엔드에 할당됩니다.  IP는 인프라 서브넷의 CIDR 내에 있어야 하며 사용 중이 아니어야 합니다.  **routingSubDomainType** 은 "사용자 지정"으로 설정되어야 하고 라우팅을 위한 와일드카드 DNS 이름은 **routingSubDomain** 에 제공되어야 합니다.  

프라이빗 마스터 및 프라이빗 라우터를 선택한 경우 **domainName** 에 사용자 지정 도메인 이름도 입력해야 합니다.

성공적인 배포 후 Bastion Node는 ssh를 사용할 수 있는 공용 IP가 있는 유일한 노드입니다.  마스터 노드는 공용 액세스용으로 구성되어 있어도 ssh 액세스용으로 노출되지 않습니다.

Resource Manager 템플릿을 사용하여 배포하려면 매개 변수 파일을 사용하여 입력 매개 변수를 제공합니다. 배포를 추가로 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 요새 VM 크기(azuredeploy.json의 변수)
- 명명 규칙(azuredeploy.json의 변수)
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨(deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>매개 변수 파일 구성

[OpenShift Container Platform 템플릿](https://github.com/Microsoft/openshift-container-platform)에는 각기 다른 OpenShift Container Platform 버전에 사용할 수 있는 여러 분기가 있습니다.  필요에 따라, 리포지토리에서 직접 배포하거나, 배포하기 전에 리포지토리를 포크하고 템플릿 또는 스크립트를 사용자 지정할 수 있습니다.

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다.

다음 예제에서는 모든 필수 입력이 포함된 azuredeploy.parameters.json이라는 매개 변수 파일을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

매개 변수를 자신의 특정 정보로 바꿉니다.

릴리스마다 다른 매개 변수를 사용할 수 있으므로, 사용하는 분기에 필요한 매개 변수를 확인합니다.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.json file explained

| 속성 | 설명 | 유효한 옵션 | 기본값 |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | 아티팩트의 URL(json, 스크립트 등) |  |  https:\//raw.githubusercontent.com/Microsoft/openshift-container-platform/master  |
| `location` | 리소스를 배포할 Azure 지역 |  |  |
| `masterVmSize` | 마스터 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용되는 VM 크기 중 하나를 선택합니다. |  | Standard_E2s_v3 |
| `infraVmSize` | 인프라 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용되는 VM 크기 중 하나를 선택합니다. |  | Standard_D4s_v3 |
| `nodeVmSize` | 앱 노드 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용되는 VM 크기 중 하나를 선택합니다. |  | Standard_D4s_v3 |
| `cnsVmSize` | CNS(컨테이너 기본 스토리지) 노드 VM의 크기입니다. azuredeploy.json 파일에 나열된 허용되는 VM 크기 중 하나를 선택합니다. |  | Standard_E4s_v3 |
| `osImageType` | 사용할 RHEL 이미지입니다. defaultgallery: 주문형; 마켓플레이스: 타사 이미지 | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | `osImageType`이 마켓플레이스인 경우 마켓플레이스 제품의 '게시자', '제품', 'sku', '버전'에 적절한 값을 입력합니다. 이 매개 변수는 개체 유형입니다. |  |  |
| `storageKind` | 사용할 스토리지 유형  | 관리<br> unmanaged | 관리 |
| `openshiftClusterPrefix` | 모든 노드의 호스트 이름을 구성할 때 사용할 클러스터 접두사입니다.  1~20자 사이입니다. |  | mycluster |
| `minoVersion` | 배포할 OpenShift Container Platform 3.11의 부 버전 |  | 69 |
| `masterInstanceCount` | 배포할 마스터 노드 수 | 1, 3, 5 | 3 |
| `infraInstanceCount` | 배포할 인프라 노드 수 | 1, 2, 3 | 3 |
| `nodeInstanceCount` | 배포할 노드 수 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | 배포할 CNS 노드 수 | 3, 4 | 3 |
| `osDiskSize` | VM의 OS 디스크 크기(GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Docker 볼륨의 노드에 연결할 데이터 디스크 크기(GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | glusterfs에서 사용하기 위해 CNS 노드에 연결할 데이터 디스크 크기(GB) | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | OS(VM) 로그인 및 초기 OpenShift 사용자 모두에 대한 관리자 사용자 이름 |  | ocpadmin |
| `enableMetrics` | 메트릭 사용하도록 설정. 메트릭에는 더 많은 리소스가 필요하므로 인프라 VM에 적절한 크기를 선택합니다. | true <br> false | false |
| `enableLogging` | 로깅 사용하도록 설정. elasticsearch Pod에는 8GB RAM이 필요하므로 인프라 VM에 적절한 크기를 선택합니다. | true <br> false | false |
| `enableCNS` | 컨테이너 기본 스토리지 사용하도록 설정 | true <br> false | false |
| `rhsmUsernameOrOrgId` | Red Hat 구독 관리자 사용자 이름 또는 조직 ID |  |  |
| `rhsmPoolId` | 컴퓨팅 노드에 대한 OpenShift 인타이틀먼트가 포함된 Red Hat 구독 관리자 풀 ID |  |  |
| `rhsmBrokerPoolId` | 마스터 및 인프라 노드에 대한 OpenShift 인타이틀먼트가 포함된 Red Hat 구독 관리자 풀 ID. 다른 풀 ID가 없는 경우 'rhsmPoolId'와 동일한 풀 ID를 입력합니다. |  |
| `sshPublicKey` | 여기에 SSH 공개 키를 복사합니다. |  |  |
| `keyVaultSubscriptionId` | Key Vault가 포함된 구독의 구독 ID |  |  |
| `keyVaultResourceGroup` | Key Vault가 들어 있는 리소스 그룹의 이름입니다. |  |  |
| `keyVaultName` | 만든 Key Vault의 이름 |  |  |
| `enableAzure` | Azure 클라우드 공급자 사용하도록 설정 | true <br> false | true |
| `aadClientId` | 서비스 주체의 애플리케이션 ID라고도 하는 Azure Active Directory 클라이언트 ID |  |  |
| `domainName` | 사용할 사용자 지정 도메인 이름의 이름(해당하는 경우). 완전 프라이빗 클러스터를 배포하지 않는 경우 "없음"으로 설정 |  | 없음 |
| `masterClusterDnsType` | OpenShift 웹 콘솔의 도메인 유형입니다. '기본값'은 마스터 인프라 공용 IP의 DNS 레이블을 사용합니다. '사용자 지정'을 사용하면 자신의 이름을 정의할 수 있습니다. | default <br> 사용자 지정 | default |
| `masterClusterDns` | `masterClusterDnsType`에 대해 '사용자 지정'을 선택한 경우 OpenShift 웹 콘솔에 액세스하는 데 사용할 사용자 지정 DNS 이름 |  | console.contoso.com |
| `routingSubDomainType` | 'nipio'로 설정하면 `routingSubDomain`은 nip.io를 사용합니다.  라우팅에 사용하려는 고유한 도메인이 있는 경우 '사용자 지정'을 사용합니다. | nipio <br> 사용자 지정 | nipio |
| `routingSubDomain` | `routingSubDomainType`에 대해 '사용자 지정'을 선택한 경우 라우팅에 사용할 와일드카드 DNS 이름 |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | 기존 가상 네트워크를 사용할지 또는 새 가상 네트워크를 만들지 선택합니다. | 기존 <br> new | new |
| `virtualNetworkResourceGroupName` | `virtualNetworkNewOrExisting`에 대해 '새로 만들기'를 선택한 경우 새 가상 네트워크의 리소스 그룹 이름 |  | resourceGroup().name |
| `virtualNetworkName` | `virtualNetworkNewOrExisting`에 대해 '새로 만들기'를 선택한 경우 만들 새 가상 네트워크의 이름 |  | openshiftvnet |
| `addressPrefixes` | 새 가상 네트워크의 주소 접두사 |  | 10.0.0.0/14 |
| `masterSubnetName` | 마스터 서브넷의 이름 |  | mastersubnet |
| `masterSubnetPrefix` | 마스터 서브넷에 사용되는 CIDR - addressPrefix의 하위 집합이어야 합니다. |  | 10.1.0.0/16 |
| `infraSubnetName` | 인프라 서브넷의 이름 |  | infrasubnet |
| `infraSubnetPrefix` | 인프라 서브넷에 사용되는 CIDR - addressPrefix의 하위 집합이어야 합니다. |  | 10.2.0.0/16 |
| `nodeSubnetName` | 노드 서브넷의 이름 |  | nodesubnet |
| `nodeSubnetPrefix` | 노드 서브넷에 사용되는 CIDR - addressPrefix의 하위 집합이어야 합니다. |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | 마스터 노드의 기존 서브넷에 대한 전체 참조입니다. 새 vNet/서브넷을 만드는 경우 필요하지 않음 |  |  |
| `existingInfraSubnetReference` | 인프라 노드의 기존 서브넷에 대한 전체 참조입니다. 새 vNet/서브넷을 만드는 경우 필요하지 않음 |  |  |
| `existingCnsSubnetReference` | CNS 노드의 기존 서브넷에 대한 전체 참조입니다. 새 vNet/서브넷을 만드는 경우 필요하지 않음 |  |  |
| `existingNodeSubnetReference` | 컴퓨팅 노드의 기존 서브넷에 대한 전체 참조입니다. 새 vNet/서브넷을 만드는 경우 필요하지 않음 |  |  |
| `masterClusterType` | 클러스터가 프라이빗 마스터 노드를 사용하는지 또는 공용 마스터 노드를 사용하는지 지정합니다. 프라이빗을 선택하면 마스터 노드가 공개 IP를 통해 인터넷에 노출되지 않습니다. 대신 `masterPrivateClusterIp`에 지정된 개인 IP를 사용합니다. | public <br> private | public |
| `masterPrivateClusterIp` | 프라이빗 마스터 노드를 선택한 경우 마스터 노드의 내부 부하 분산 장치에서 사용할 개인 IP 주소를 지정해야 합니다. 이 고정 IP는 마스터 서브넷의 CIDR 블록 내에 있어야 하며 아직 사용 중이 아니어야 합니다. 공용 마스터 노드가 선택된 경우 이 값은 사용되지 않지만 계속 지정해야 합니다. |  | 10.1.0.200 |
| `routerClusterType` | 클러스터가 프라이빗 인프라 노드를 사용하는지 또는 공용 인프라 노드를 사용하는지 지정합니다. 프라이빗을 선택하면 인프라 노드가 공개 IP를 통해 인터넷에 노출되지 않습니다. 대신 `routerPrivateClusterIp`에 지정된 개인 IP를 사용합니다. | public <br> private | public |
| `routerPrivateClusterIp` | 프라이빗 인프라 노드를 선택한 경우 인프라 노드의 내부 부하 분산 장치에서 사용할 개인 IP 주소를 지정해야 합니다. 이 고정 IP는 인프라 서브넷의 CIDR 블록 내에 있어야 하며 아직 사용 중이 아니어야 합니다. 공용 인프라 노드를 선택한 경우 이 값은 사용되지 않지만 계속 지정해야 합니다. |  | 10.2.0.200 |
| `routingCertType` | 도메인 라우팅에 사용자 지정 인증서 사용 또는 기본 자체 서명 인증서 사용 - **사용자 지정 인증서** 섹션의 지침을 따르세요. | selfsigned <br> 사용자 지정 | selfsigned |
| `masterCertType` | 마스터 도메인용 사용자 지정 인증서 또는 기본 자체 서명 인증서 사용 - **사용자 지정 인증서** 섹션의 지침을 따르세요. | selfsigned <br> 사용자 지정 | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포

> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. CLI 버전은 `az --version` 명령으로 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음 예제에서는 openshiftrg라는 리소스 그룹에 OpenShift 클러스터 및 모든 관련 리소스를 배포하고 배포 이름을 myOpenShiftCluster로 지정합니다. 템플릿은 GitHub 리포지토리에서 직접 참조되며, azuredeploy.parameters.json이라는 로컬 매개 변수 파일이 사용됩니다.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포를 완료하려면 배포되는 전체 노드 수 및 구성된 옵션에 따라 60분 이상이 걸립니다. 배포를 마치면 OpenShift 콘솔의 Bastion DNS FQDN 및 URL이 터미널에 출력됩니다.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

배포가 완료될 때까지 명령줄을 대기하지 않으려면 그룹 배포에 대한 옵션 중 하나로 `--no-wait`를 추가합니다. 배포의 출력은 Azure Portal의 리소스 그룹에 대한 배포 섹션에서 검색할 수 있습니다.

## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. **OpenShift Console URL** 을 사용해 브라우저를 OpenShift 콘솔에 연결합니다. 베스천 호스트에 SSH를 사용하도록 설정할 수도 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com)
