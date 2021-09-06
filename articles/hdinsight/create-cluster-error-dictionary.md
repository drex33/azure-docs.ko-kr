---
title: Azure HDInsight 클러스터 만들기 - 오류 사전
description: Azure HDInsight 클러스터를 만들 때 발생하는 오류 해결 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: d80ae76ed47366e56a3a1bf318219c6944fa3dbe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528835"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: 클러스터 만들기 오류

이 문서에서는 클러스터를 만들 때 발생할 수 있는 오류 해결 방법을 설명합니다.

> [!NOTE]
> 이 문서에서 설명하는 처음 세 가지 오류는 유효성 검사 오류입니다. 이러한 오류는 Azure HDInsight 제품에서 **CsmDocument_2_0** 클래스를 사용할 때 발생할 수 있습니다.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>오류 코드: DeploymentDocument 'CsmDocument_2_0' 유효성 검사 실패

**오류**: "스크립트 작업 위치에서 URI \<SCRIPT ACTION URL\>에 액세스할 수 없음"

### <a name="error-message-1"></a>오류 메시지 1

"원격 서버에서 (404) 찾을 수 없음 오류를 반환했습니다."

#### <a name="cause"></a>원인

사용자가 클러스터 만들기 요청에서 제공한 스크립트 작업 URL에 HDInsight 서비스가 액세스할 수 없습니다. 이 서비스는 스크립트 작업에 액세스하려고 하면 위의 오류 메시지를 수신합니다.

#### <a name="resolution"></a>해결 방법

- HTTP 또는 HTTPS URL의 경우 incognito 브라우저 창에서 이 URL로 이동하려고 시도하여 URL을 확인합니다.
- WASB URL의 경우 사용자가 요청에서 입력하는 스토리지 계정에 스크립트가 있어야 합니다. 또한 이 스토리지 계정의 스토리지 키가 올바른지 확인합니다.
- ADLS URL의 경우 스크립트가 스토리지 계정에 있어야 합니다.

---

### <a name="error-message-2"></a>오류 메시지 2

"지정된 스크립트 URI \<SCRIPT_URI\>가 ADLS에 있지만, 이 클러스터에는 데이터 레이크 스토리지 보안 주체가 없습니다."

#### <a name="cause"></a>원인

사용자가 클러스터 만들기 요청에서 제공한 스크립트 작업 URL에 HDInsight 서비스가 액세스할 수 없습니다. 이 서비스는 스크립트 작업에 액세스하려고 하면 위의 오류 메시지를 수신합니다.

#### <a name="resolution"></a>해결 방법

해당하는 Azure Data Lake Storage Gen 1 계정을 클러스터에 추가합니다. 또한 Data Lake Storage Gen 1 계정에 액세스하는 서비스 주체를 클러스터에 추가합니다.

---

### <a name="error-message-3"></a>오류 메시지 3

"요청에서 입력한 VM 크기 '\<CUSTOMER_SPECIFIED_VM_SIZE\>'가 잘못되었거나 '\<ROLE\>' 역할을 지원하지 않습니다. 유효한 값은 \<VALID_VM_SIZE_FOR_ROLE\>입니다."

#### <a name="cause"></a>원인

지정한 가상 머신 크기가 역할에 허용되지 않습니다. 이 오류는 VM 크기 값이 예상대로 작동하지 않거나 컴퓨터 역할에 적합하지 않을 때 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

오류 메시지에는 유효한 VM 크기 값이 나열됩니다. 유효한 값 중 하나를 선택하고 클러스터 만들기 요청을 다시 시도합니다.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>오류 코드: InvalidVirtualNetworkId  

### <a name="error"></a>오류

"VirtualNetworkId가 잘못되었습니다. VirtualNetworkId '\<USER_VIRTUALNETWORKID\>'*"

### <a name="cause"></a>원인

클러스터를 만드는 동안 지정한 **VirtualNetworkId** 값이 올바른 형식이 아닙니다.

### <a name="resolution"></a>해결 방법

**VirtualNetworkId** 및 서브넷 값이 올바른 형식인지 확인합니다. **VirtualNetworkId** 값을 얻으려면 다음을 수행합니다.

1. Azure Portal로 이동합니다.
1. 가상 네트워크를 선택합니다.
1. **속성** 메뉴 항목을 호출합니다. **ResourceID** 속성 값은 **VirtualNetworkId** 값입니다.

다음은 가상 네트워크 ID의 예입니다.

"/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet"

---

## <a name="error-code-customizationfailederrorcode"></a>오류 코드: CustomizationFailedErrorCode

### <a name="error"></a>오류

"사용자 지정 스크립트 작업의 오류로 인해 클러스터를 배포하지 못했습니다. 실패한 작업: \<SCRIPT_NAME\>. Ambari UI로 이동하여 오류를 자세히 디버그하세요."

### <a name="cause"></a>원인

클러스터 만들기 요청 중에 제공한 사용자 지정 스크립트는 클러스터가 성공적으로 배포된 후에 실행됩니다. 이 오류 코드는 \<SCRIPT_NAME\>이라는 사용자 지정 스크립트를 실행하는 동안 오류가 발생했음을 나타냅니다.

### <a name="resolution"></a>해결 방법

이 스크립트는 사용자 지정 스크립트이므로 문제를 해결하고 필요한 경우 스크립트를 다시 실행하는 것이 좋습니다. 스크립트 오류를 해결하려면 /var/lib/ambari-agent/* 폴더의 로그를 검사합니다. 또는 Ambari UI에서 **작업** 페이지를 연 다음, **run_customscriptaction** 작업을 선택하여 오류 세부 정보를 확인합니다.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>오류 코드: InvalidDocumentErrorCode

### <a name="error"></a>오류

"\<DATABASE_NAME\> 데이터베이스의 \<META_STORE_TYPE\> 메타스토어 스키마 버전 \<METASTORE_MAJOR_VERSION\>이 클러스터 버전 \<CLUSTER_VERSION\>과 호환되지 않습니다."

### <a name="cause"></a>원인

사용자 지정 메타스토어가 선택한 HDInsight 클러스터 버전과 호환되지 않습니다. 현재 HDInsight 4.0 클러스터는 메타스토어 버전 3.0 이상만 지원하는 반면, HDInsight 3.6 클러스터는 메타스토어 버전 3.0 이상을 지원하지 않습니다.

### <a name="resolution"></a>해결 방법

HDInsight 클러스터 버전에서 지원하는 메타스토어 버전만 사용합니다. 사용자 지정 메타스토어를 지정하지 않으면 HDInsight는 내부적으로 메타스토어를 만들고, 클러스터 삭제 시 메타스토어를 삭제합니다.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>오류 코드: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>오류

"클러스터 관리 엔드포인트에 연결하여 스케일링 작업을 수행할 수 없습니다. 네트워크 보안 규칙이 클러스터에 대한 외부 액세스를 차단하지 않으며 클러스터 관리자(Ambari) UI에 액세스할 수 있는지 확인하세요."

### <a name="cause"></a>원인

NSG(네트워크 보안 그룹)의 방화벽 규칙이 중요한 Azure 상태 및 관리 서비스와의 클러스터 통신을 차단합니다.

### <a name="resolution"></a>해결 방법

네트워크 보안 그룹을 사용하여 네트워크 트래픽을 제어할 계획인 경우 HDInsight를 설치하기 전에 다음 작업을 수행합니다.

- HDInsight에 대해 사용할 Azure 지역을 식별합니다.
- HDInsight에 필요한 IP 주소를 식별합니다. 자세한 내용은 [HDInsight 관리 IP 주소](./hdinsight-management-ip-addresses.md)를 참조하세요.
  - HDInsight를 설치하려는 서브넷에 대한 네트워크 보안 그룹을 만들거나 수정합니다.
  - 네트워크 보안 그룹의 경우 IP 주소의 포트 443에서 인바운드 트래픽을 허용합니다. 이렇게 구성하면 HDInsight 관리 서비스가 가상 네트워크 외부에서 클러스터에 도달할 수 있습니다.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>오류 코드: StoragePermissionsBlockedForMsi

### <a name="error"></a>오류

"관리 ID에 스토리지 계정에 대한 권한이 없습니다. 스토리지 계정의 관리 ID에 'Storage Blob 데이터 소유자' 역할이 할당되었는지 확인하세요. 스토리지: /subscriptions/ \<Subscription ID\> /resourceGroups/\<Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, 관리 ID: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\<Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>원인

ID를 관리하는 데 필요한 권한을 제공하지 않았습니다. 사용자가 할당한 관리 ID에 Azure Data Lake Storage Gen2 스토리지 계정에 대한 Blob Storage 기여자 역할이 없습니다.

### <a name="resolution"></a>해결 방법

1. Azure Portal을 엽니다.
1. 스토리지 계정으로 이동합니다.
1. **Access Control(IAM)** 아래를 확인합니다.
1. 사용자에게 Storage Blob 데이터 기여자 역할 또는 Storage Blob 데이터 소유자 역할이 할당되었는지 확인합니다.

자세한 내용은 [Data Lake Storage Gen2 계정에서 관리 ID에 대한 권한 설정](hdinsight-hadoop-use-data-lake-storage-gen2.md)을 참조하세요.

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>오류 코드: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>오류

"/subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> 서브넷을 사용하여 구성한 네트워크 보안 그룹 /subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\>의 보안 규칙에서 필요한 인바운드 및/또는 아웃바운드 연결을 허용하지 않습니다. 자세한 내용은 [Azure HDInsight에 대한 가상 네트워크 계획](./hdinsight-plan-virtual-network-deployment.md)을 방문하거나 고객 지원팀에 문의하세요."

### <a name="cause"></a>원인

네트워크 보안 그룹 또는 UDR(사용자 정의 경로)이 HDInsight 클러스터에 대한 인바운드 트래픽을 제어하는 경우 클러스터가 중요한 Azure 상태 및 관리 서비스와 통신할 수 있어야 합니다.

### <a name="resolution"></a>해결 방법

네트워크 보안 그룹을 사용하여 네트워크 트래픽을 제어할 계획인 경우 HDInsight를 설치하기 전에 다음 작업을 수행합니다.

- HDInsight에 사용할 Azure 지역을 확인하고, 해당 지역의 안전한 IP 주소 목록을 만듭니다. 자세한 내용은 [상태 및 관리 서비스: 특정 지역](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions)을 참조하세요.
- HDInsight에 필요한 IP 주소를 식별합니다. 자세한 내용은  [HDInsight 관리 IP 주소](./hdinsight-management-ip-addresses.md)를 참조하세요.
- HDInsight를 설치하려는 서브넷에 대한 네트워크 보안 그룹을 만들거나 수정합니다. 네트워크 보안 그룹의 경우 IP 주소의 포트 443에서 인바운드 트래픽을 허용합니다. 이렇게 구성하면 HDInsight 관리 서비스가 가상 네트워크 외부에서 클러스터에 도달할 수 있습니다.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>오류 코드: 클러스터 설치 작업에서 하나 이상의 호스트에 구성 요소를 설치하지 못했습니다.

### <a name="error"></a>오류

"클러스터 설치 작업에서 하나 이상의 호스트에 구성 요소를 설치하지 못했습니다. 요청을 다시 시도하세요."

### <a name="cause"></a>원인 

일반적으로 이 오류는 일시적인 문제 또는 Azure 중단이 있을 때 발생합니다.

### <a name="resolution"></a>해결 방법

[Azure 상태](https://status.azure.com) 페이지에서 클러스터 배포에 영향을 줄 수 있는 Azure 중단을 확인합니다. 중단이 없으면 클러스터 배포를 다시 시도합니다.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>오류 코드: FailedToConnectWithClusterErrorCode

### <a name="error"></a>오류

클러스터 관리 엔드포인트에 연결할 수 없습니다. 나중에 다시 시도하십시오.

### <a name="cause"></a>원인

클러스터를 만드는 동안 HDInsight 서비스가 클러스터에 연결할 수 없습니다.

### <a name="resolution"></a>해결 방법

사용자 지정 VNet NSG(네트워크 보안 그룹) 및 UDR(사용자 정의 경로)을 사용하는 경우 클러스터가 HDInsight 관리 서비스와 통신할 수 있는지 확인합니다. 자세한 내용은 [HDInsight 관리 IP 주소](./hdinsight-management-ip-addresses.md)를 참조하세요.

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>오류 코드: 정책 위반으로 인해 배포에 실패했습니다. '정책에서 '\<Resource URI\>' 리소스를 허용하지 않습니다. 정책 식별자: '[{"policyAssignment":{"name":"\<Policy Name\> ","id":"/providers/Microsoft.Management/managementGroups/\<Management Group Name\> providers/Microsoft.Authorization/policyAssignments/\<Policy Name\>"},"policyDefinition": \<Policy Definition\>

### <a name="cause"></a>원인

구독 기반 Azure 정책은 공용 IP 주소 만들기를 거부할 수 있습니다. HDInsight 클러스터를 생성하려면 두 개의 공용 IP가 필요합니다.

다음 정책은 일반적으로 클러스터 만들기에 영향을 줍니다.

* 구독 내에서 IP 주소 또는 부하 분산 장치를 만들지 못하도록 차단하는 정책
* 스토리지 계정을 만들지 못하도록 차단하는 정책
* IP 주소 또는 Load Balancer와 같은 네트워킹 리소스를 삭제하지 못하도록 차단하는 정책

### <a name="resolution"></a>해결 방법

HDInsight 클러스터를 만드는 동안 구독 기반 Azure Policy 할당을 삭제하거나 사용하지 않도록 설정합니다.

---

## <a name="next-steps"></a>다음 단계

클러스터 만들기 오류 문제 해결에 대한 자세한 내용은 [Azure HDInsight를 사용하여 클러스터 만들기 오류 문제 해결](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)을 참조하세요.