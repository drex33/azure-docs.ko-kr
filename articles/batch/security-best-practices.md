---
title: Batch 보안 및 규정 준수 모범 사례
description: Azure Batch 솔루션을 사용하여 보안을 강화하기 위한 모범 사례와 유용한 팁에 대해 알아봅니다.
ms.date: 09/01/2021
ms.topic: conceptual
ms.openlocfilehash: f9bfbeb7a89e3ca1bc71001b173926302548a988
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435549"
---
# <a name="batch-security-and-compliance-best-practices"></a>Batch 보안 및 규정 준수 모범 사례

이 문서에서는 Azure Batch을 사용할 때 보안을 강화하기 위한 지침과 모범 사례를 제공합니다.

기본적으로 Azure Batch 계정에는 공용 엔드포인트가 있으며 공개적으로 액세스할 수 있습니다. Azure Batch 풀이 만들어지면 풀은 Azure 가상 네트워크의 지정된 서브넷에 프로비전됩니다. Batch 풀의 가상 머신은 Batch에서 생성된 공용 IP 주소를 통해 액세스합니다. 풀의 컴퓨팅 노드는 다중 인스턴스 작업을 실행하는 등 필요한 경우 서로 통신할 수 있지만 풀의 노드는 풀 외부의 가상 머신과 통신할 수 없습니다.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="일반적인 Batch 환경을 보여주는 다이어그램.":::

다양한 기능을 사용하여 보다 안전한 Azure Batch 배포를 만들 수 있습니다. [공용 IP 주소 없이 풀을 프로비전](batch-pool-no-public-ip-address.md)하여 이러한 노드에 대한 액세스를 제한하고 인터넷에서 이러한 노드의 검색 가능성을 줄일 수 있습니다. 컴퓨팅 노드는 [Azure virtual network의 서브넷에 풀을 프로 비전](batch-virtual-network.md)하여 다른 가상 머신 또는 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. 그리고 Azure Private Link를 통해 제공되는 서비스의 [가상 네트워크에서 개인 액세스](private-connectivity.md)를 사용하도록 설정할 수 있습니다.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="보다 안전한 Batch 환경을 보여주는 다이어그램.":::

## <a name="general-security-related-best-practices"></a>일반적인 보안 관련 모범 사례

### <a name="pool-configuration"></a>풀 구성

많은 보안 기능은 Cloud Services 구성이 포함된 풀이 아니라 [가상 머신 구성](nodes-and-pools.md#configurations)을 사용하여 구성된 풀에서만 사용할 수 있습니다. 가능하면 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)을 활용하는 가상 머신 구성 풀을 사용하는 것이 좋습니다.

### <a name="batch-account-authentication"></a>Batch 계정 인증

Batch 계정 액세스는 공유 키와 [Azure Active Directory (AZURE AD)](batch-aad-auth.md)의 두 가지 인증 방법을 지원합니다.

Batch 계정 인증에 Azure AD를 사용하는 것이 좋습니다. 일부 Batch 기능에는 여기에서 설명하는 많은 보안 관련 기능을 비롯한 이 인증 방법이 필요합니다. Batch 계정에 대한 서비스 API 인증 메커니즘은 [allowedAuthenticationModes](/rest/api/batchmanagement/batch-account/create) 속성을 사용하는 Azure AD로만 제한될 수 있습니다. 이 속성이 설정되면 공유 키 인증을 사용하는 API 호출이 거부됩니다.

### <a name="batch-account-pool-allocation-mode"></a>Batch 계정 풀 할당 모드

Batch 계정을 만들 때 두 가지 [풀 할당 모드](accounts.md#batch-accounts)에서 선택할 수 있습니다.

- **Batch 서비스**: 기본 클라우드 서비스 또는 풀 노드를 할당하고 관리하는 데 사용되는 가상 머신 확장 집합 리소스가 내부 구독에서 생성되고 Azure Portal에 직접 표시되지 않는 기본 옵션입니다. Batch 풀 및 노드만 표시됩니다.
- **사용자 구독**: 기본 클라우드 서비스 또는 가상 머신 확장 집합 리소스는 Batch 계정과 동일한 구독에 생성됩니다. 따라서 이러한 리소스는 해당 Batch 리소스 외에도 구독에서 볼 수 있습니다.

사용자 구독 모드를 사용하여, Batch VM 및 기타 리소스는 풀이 만들어질 때 구독에서 직접 만들어집니다. Azure Reserved VM Instances 사용하여 Batch 풀을 만들거나, 가상 머신 확장 집합 리소스에서 Azure Policy 사용하거나, 구독의 코어 할당량을 관리하려는 경우 사용자 구독 모드가 필요합니다(구독의 모든 Batch 계정에서 공유). 사용자 구독 모드에서 Batch 계정을 만들려면 또한 Azure Batch를 사용하여 구독을 등록하고 Azure Key Vault와 계정을 연결해야 합니다.

## <a name="restrict-network-endpoint-access"></a>네트워크 엔드포인트 액세스 제한

### <a name="batch-network-endpoints"></a>Batch 네트워크 엔드포인트

기본적으로 공용 IP가 있는 엔드포인트는 Batch 계정, Batch 풀, 풀 노드와 통신하는 데 사용됨을 유의하여 주십시오.

### <a name="batch-account-api"></a>Batch 계정 API

 Batch 계정이 만들어지면 [REST API](/rest/api/batchservice/)를 사용하여 계정에 대한 대부분의 작업을 호출하는 데 사용되는 퍼블릭 엔드포인트가 만들어집니다. 계정 엔드포인트에는 형식 `https://{account-name}.{region-id}.batch.azure.com`을 사용하는 기본 URL이 있습니다. Batch 계정에 대한 액세스는 HTTPS를 사용하여 암호화되는 계정 엔드포인트에 대한 통신과 공유 키 또는 Azure AD(Azure Active Directory) 인증을 사용하여 인증된 각 요청으로 보호됩니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자

Batch 계정과 관련한 작업 외에도 [관리 작업](/rest/api/batchmanagement/)은 단일 및 여러 Batch 계정에 적용됩니다. 이러한 관리 작업은 Azure Resource Manager를 통해 액세스됩니다.

Azure Resource Manager 통한 Batch 관리 작업은 HTTPS를 사용하여 암호화되고 각 요청은 Azure AD 인증을 사용하여 인증됩니다.

### <a name="batch-pool-nodes"></a>Batch 풀 노드

Batch 서비스는 풀의 각 노드에서 실행되는 Batch 노드 에이전트와 통신합니다. 예를 들어 서비스는 태스크를 실행하거나 작업을 중지하거나 태스크에 대한 파일을 가져오기 위해 노드 에이전트에 지시합니다. 풀 노드 에이전트와의 통신은 하나 이상의 부하 분산 장치에 의해 사용하도록 설정되며, 이 수는 풀의 노드 수에 따라 달라집니다. 부하 분산 장치는 원하는 풀 노드에 대한 통신을 전달하며, 각 노드에는 고유한 포트 번호로 주소가 지정됩니다. 기본적으로 부하 분산 장치에는 연결된 공용 IP가 있습니다. RDP 또는 SSH를 통해 풀 노드에 원격으로 액세스할 수도 있습니다(이 액세스는 기본적으로 부하 분산 장치를 통한 통신을 통해 사용하도록 설정됨).

### <a name="restricting-access-to-batch-endpoints"></a>Batch 엔드포인트에 대한 액세스 제한 

특히 솔루션에서 가상 네트워크를 사용하는 경우 다양한 Batch 엔드포인트에 대한 액세스를 제한하는 데 몇 가지 기능을 사용할 수 있습니다. 

#### <a name="use-private-endpoints"></a>프라이빗 엔드포인트 사용

[Azure Private Link](../private-link/private-link-overview.md)를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services와 Azure 호스팅 고객 소유/파트너 서비스에 액세스할 수 있습니다. Private Link를 사용하여 가상 네트워크 내에서 부터 또는 피어링된 가상 네트워크에서 Batch 계정에 대한 액세스를 제한할 수 있습니다. Private Link에 매핑된 리소스 역시 프라이빗 피어링을 사용하여 VPN 또는 Azure ExpressRoute를 통해 온-프레미스에서 액세스할 수 있습니다.

프라이빗 엔드포인트를 사용하려면 배치 계정을 만들 때 적절하게 구성해야 합니다. 공용 네트워크 액세스 구성을 사용하지 않도록 설정해야 합니다. 만든 후에는 프라이빗 엔드포인트를 만들고 Batch 계정에 연결할 수 있습니다. 자세한 내용은 [Azure Batch 계정으로 프라이빗 엔드포인트 사용](private-connectivity.md)을 참조하세요.

#### <a name="create-pools-in-virtual-networks"></a>가상 네트워크에서 풀 만들기

Batch 풀의 계산 노드는 VNET(가상 네트워크)을 요구하지 않고 여러 인스턴스 작업을 실행하기 위해와 같이 서로 통신할 수 있습니다. 그러나 기본적으로 풀의 노드는 가상 네트워크의 풀 외부에 있고 라이선스 서버 또는 파일 서버와 같은 개인 IP 주소를 가진 가상 컴퓨터와 통신할 수 없습니다.

컴퓨팅 노드가 다른 가상 머신 또는 온-프레미스 네트워크와 안전하게 통신하도록 Azure VNET의 서브넷에서 풀이 존재하도록 구성할 수 있습니다.

풀이 공용 IP 엔드포인트를 가지고 있는 경우, 서브넷은 작업을 예약할 수 있도록 Batch 서비스로부터의 인바운드 통신을 허용하고, 컴퓨팅 노드에 대한 기타 작업을 수행하고, 워크로드에 필요한 Azure Storage 또는 기타 리소스와 통신할 수 있도록 아웃바운드 통신을 허용해야 합니다. Batch는 Virtual Machine 구성의 풀에 대해 컴퓨팅 노드에 연결된 네트워크 인터페이스 수준에서 NSG(네트워크 보안 그룹)을 추가합니다. 이러한 NSG에는 다음을 사용하도록 설정하는 규칙이 있습니다.

- 배치 서비스 IP 주소에서의 인바운드 TCP 트래픽
- 원격 액세스를 위한 인바운드 TCP 트래픽
- 가상 네트워크에 대한 모든 포트의 아웃바운드 트래픽(서브넷 수준 NSG 규칙에 따라 수정될 수 있음)
- 인터넷에 대한 모든 포트의 아웃바운드 트래픽(서브넷 수준 NSG 규칙에 따라 수정될 수 있음)

Batch는 자체 NSG를 구성하므로 가상 네트워크 서브넷 수준에서 NSG를 지정할 필요가 없습니다. Batch 컴퓨팅 노드가 배포된 서브넷과 연결된 NSG가 있거나 적용된 기본값을 재정의하는 사용자 지정 NSG 규칙을 적용하려는 경우 풀 노트에 대한 배치 서비스 통신과 Azure Storage에 대한 풀 노트 통신을 허용하기 위해서는 적어도 인바운드 및 아웃바운드 보안 규칙을 사용하여 이 NSG를 구성해야 합니다.

자세한 내용은 [가상 네트워크에서 Azure Batch 풀 만들기](batch-virtual-network.md)를 참조하세요.

#### <a name="create-pools-with-static-public-ip-addresses"></a>고정 공용 IP 주소를 사용하여 풀 만들기

기본적으로 풀과 연결된 공용 IP 주소는 동적입니다. 풀을 만들 때 생성되며 풀의 크기를 조정할 때 IP 주소를 추가하거나 제거할 수 있습니다. 풀 노드에서 실행 중인 태스크 애플리케이션에서 외부 서비스에 액세스해야 하는 경우 해당 서비스에 대한 액세스를 특정 IP로 제한해야 할 수 있습니다.  이 경우 동적 IP 주소를 갖는 것은 관리할 수 없습니다.

풀을 만들기 전에 Batch 계정과 동일한 구독에 고정 공용 IP 주소 리소스를 만들 수 있습니다. 그런 다음 풀을 만들 때 이러한 주소를 지정할 수 있습니다.

자세한 내용은 [지정된 공용 IP 주소를 사용하여 Azure Batch 풀 만들기](create-pool-public-ip.md)를 참조하세요.

#### <a name="create-pools-without-public-ip-addresses"></a>공용 IP 주소가 없는 풀 만들기

기본적으로 Azure Batch 가상 머신 구성 풀의 모든 계산 노드에는 하나 이상의 공용 IP 주소가 할당됩니다. 이러한 엔드포인트는 Batch 서비스에서 태스크를 예약하고 인터넷에 대한 아웃바운드 액세스를 비롯하여 컴퓨팅 노드와 통신하는 데 사용됩니다.

이러한 노드에 대한 액세스를 제한하고 인터넷에서 이러한 노드의 검색 가능성을 줄이기 위해 공용 IP 없이 풀을 프로비전할 수 있습니다.

자세한 내용은 [공용 IP 주소 없는 풀 만들기](batch-pool-no-public-ip-address.md)를 참조하세요.

#### <a name="limit-remote-access-to-pool-nodes"></a>풀 노드에 대한 원격 액세스 제한

기본적으로 Batch는 노드 사용자가 네트워크 연결을 사용하여 외부에서 RDP 또는 SSH를 사용하여 Batch 풀의 컴퓨팅 노드에 연결할 수 있게 해줍니다.

노드에 대한 원격 액세스를 제한하려면 다음 방법 중 하나를 사용합니다.

- 액세스를 거부하기 위해서 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration)를 구성합니다. 적절한 NSG(네트워크 보안 그룹)이 풀과 연결됩니다.
- [공용 IP 주소가 없는](batch-pool-no-public-ip-address.md) 풀 만들기. 기본적으로 이러한 풀은 VNet 외부에서 액세스할 수 없습니다.
- VNet과 NSG를 연결하여 RDP 또는 SSH 포트에 대한 액세스를 거부합니다.
- 노드에 사용자를 만들지 마세요. 노드 사용자가 없는 경우 원격 액세스를 수행할 수 없습니다.

## <a name="encrypt-data"></a>데이터 암호화

### <a name="encrypt-data-in-transit"></a>전송 중인 데이터 암호화

Batch 계정 엔드포인트(또는 Azure Resource Manager를 통해)에 대한 모든 통신은 HTTPS를 사용해야 합니다. Batch 서비스에 연결할 때 API에 지정된 배치 계정 URL에서 `https://`을 사용해야 합니다.

Batch 서비스와 통신하는 클라이언트는 TLS(전송 계층 보안) 1.2를 사용하도록 구성해야 합니다.

### <a name="encrypt-batch-data-at-rest"></a>미사용 배치 암호화 데이터

Batch 서비스에 의해 저장되는 경우 Batch API에 지정된 일부 정보(예: 계정 인증서, 작업 및 태스크 메타 데이터 및 작업 명령줄)가 자동으로 암호화됩니다. 기본적으로 이 데이터는 각 Batch 계정에 고유한 Azure Batch 플랫폼 관리형 키를 사용하여 암호화됩니다.

[고객 관리형 키](batch-customer-managed-key.md)를 사용하여 이 데이터를 암호화할 수도 있습니다. [Azure Key Vault](../key-vault/general/overview.md)는 배치 계정에 등록된 키 식별자를 사용하여 키를 생성하고 저장하는 데 사용됩니다.

### <a name="encrypt-compute-node-disks"></a>컴퓨팅 노드 디스크 암호화

Batch 컴퓨팅 노드에는 기본적으로 OS 디스크와 로컬 임시 SSD라는 두 개의 디스크가 있습니다. Batch에서 관리하는 [파일 및 디렉터리](files-and-directories.md)는 태스크 출력 파일과 같은 파일의 기본 위치인 임시 SSD에 있습니다. Batch 작업 애플리케이션은 SSD 또는 OS 디스크의 기본 위치를 사용할 수 있습니다.

보안을 강화하려면 다음 Azure 디스크 암호화 기능 중 하나를 사용하여 이러한 디스크를 암호화합니다.

- [플랫폼 관리형 키를 사용한 미사용 관리 디스크 암호화](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [플랫폼 관리형 키를 사용하여 호스트에서 암호화](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 디스크 암호화](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>컴퓨팅 노드에서 서비스에 안전하게 액세스

일괄 처리 노드는 [Azure Key Vault](../key-vault/general/overview.md)에 저장 된 자격 증명에 안전 하 게 액세스할 수 있으며,이를 통해 작업 응용 프로그램에서 다른 서비스에 액세스할 수 있습니다. 인증서는 풀 노드에 Key Vault 대한 액세스 권한을 부여하는 데 사용됩니다. [Batch 풀에서 자동 인증서 회전을 사용 하도록 설정](automatic-certificate-rotation.md)하면 자격 증명이 자동으로 갱신 됩니다. 이 옵션을 사용 하면 Azure Key Vault에 저장 된 자격 증명에 액세스 하는 것이 좋습니다. 하지만 자동 인증서 회전이 없는 [인증서로 자격 증명 및 암호에 안전 하 게 액세스할 수 있도록 배치 노드를 설정할](credential-access-key-vault.md) 수도 있습니다.

## <a name="governance-and-compliance"></a>거버넌스 및 규정 준수

### <a name="compliance"></a>규정 준수

고객이 전 세계 규제 산업 및 시장에서 자체 규정 준수 의무를 충족할 수 있도록 Azure는 [대규모 규정 준수 제품 포트폴리오](https://azure.microsoft.com/overview/trusted-cloud/compliance)를 유지 관리합니다.

이러한 제안은 공식 인증, 증명, 유효성 검사, 권한 부여 및 독립적인 타사 감사 회사에서 생성한 평가, 계약 수정, 자체 평가, Microsoft에서 생성한 고객 지침 문서를 포함하는 다양한 확인 유형을 기준으로 합니다. 규정 [준수 제품에 대한 포괄적인 개요](https://aka.ms/AzureCompliance)를 검토하여 Batch 솔루션과 관련될 수 있는 제품을 결정합니다.

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md)를 사용하면 조직의 표준을 적용하고 규정 준수를 대규모로 평가할 수 있습니다. Azure Policy에 대한 일반적인 사용 사례에는 리소스 일관성, 규정 준수, 보안, 비용 및 관리에 대한 거버넌스 구현이 포함됩니다.

풀 할당 모드 및 정책을 적용해야 하는 리소스에 따라 다음 방법 중 하나로 Batch와 함께 Azure Policy 사용합니다.

- 직접 Microsoft.Batch/batchAccounts 리소스를 사용합니다. Batch 계정에 대한 속성의 하위 집합을 사용할 수 있습니다. 예를 들어 정책에 유효한 Batch 계정 지역, 허용된 풀 할당 모드 및 계정에 대해 공용 네트워크를 사용할 수 있는지 여부가 포함될 수 있습니다.
- 간접적으로 Microsoft.Compute/virtualMachineScaleSets 리소스를 사용합니다. 사용자 구독 풀 할당 모드가 있는 Batch 계정은 Batch 계정 구독에서 만든 가상 머신 확장 집합 리소스에 대해 정책을 설정할 수 있습니다. 예를 들어 허용되는 VM 크기를 확인하고 각 풀 노드에서 특정 확장이 실행되도록 합니다.

## <a name="next-steps"></a>다음 단계

- [Batch에 대한 Azure 보안 기준](security-baseline.md)을 검토합니다.
- [Azure Batch 대한 모범 사례를](best-practices.md)를 자세히 읽어 보기.
