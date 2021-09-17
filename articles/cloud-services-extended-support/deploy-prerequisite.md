---
title: Azure Cloud Services(추가 지원)에 대한 필수 구성 요소
description: Azure Cloud Services(추가 지원)에 대한 필수 구성 요소
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a97d855730a67111f66d201b6ca177975540d535
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427888"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원)에 대한 필수 구성 요소

성공적인 Cloud Services(추가 지원) 배포를 보장하려면 배포를 시도하기 전에 아래 단계를 검토하고 각 항목을 완료합니다. 

## <a name="required-service-configuration-cscfg-file-updates"></a>필수 서비스 구성(.cscfg) 파일 업데이트

### <a name="1-virtual-network"></a>1) Virtual Network
Cloud Service(추가 지원) 배포는 가상 네트워크에 있어야 합니다. [Azure Portal](../virtual-network/quick-create-portal.md), [PowerShell](../virtual-network/quick-create-powershell.md), [Azure CLI](../virtual-network/quick-create-cli.md) 또는 [ARM 템플릿](../virtual-network/quick-create-template.md)을 통해 가상 네트워크를 만들 수 있습니다. [NetworkConfiguration](schema-cscfg-networkconfiguration.md) 섹션 아래의 서비스 구성(.cscfg)에서도 가상 네트워크 및 서브넷을 참조해야 합니다. 

클라우드 서비스와 동일한 리소스 그룹에 속한 가상 네트워크의 경우 서비스 구성(.cscfg) 파일의 가상 네트워크 이름만 참조하면 됩니다. 가상 네트워크와 클라우드 서비스가 서로 다른 두 리소스 그룹에 있는 경우 가상 네트워크의 전체 Azure Resource Manager ID를 서비스 구성(.cscfg) 파일에 지정해야 합니다.
 
#### <a name="virtual-network-located-in-same-resource-group"></a>동일한 리소스 그룹에 있는 Virtual Network
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
  </AddressAssignments> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>다른 리소스 그룹에 있는 Virtual Network
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
   </AddressAssignments>
```
### <a name="2-remove-the-old-plugins"></a>2) 이전 플러그 인 제거

서비스 구성(.cscfg) 파일에서 이전 원격 데스크톱 설정을 제거합니다.  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
서비스 구성(.cscfg) 파일에서 각 역할에 대한 이전 진단 설정을 제거합니다.

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>필수 서비스 정의 파일(.csdef) 업데이트

> [!NOTE]
> 서비스 정의 파일(.csdef)을 변경하려면 패키지 파일(.cspkg)을 다시 생성해야 합니다. 클라우드 서비스에 대한 최신 설정을 가져오려면 .csdef 파일에서 다음과 같이 변경하는 .cspkg 게시물을 빌드하고 재패키징하세요.

### <a name="1-virtual-machine-sizes"></a>1) Virtual Machine 크기
다음 크기는 Azure Resource Manager에서 더 이상 사용되지 않습니다. 그러나 계속 사용하려는 경우에는 연결된 Azure Resource Manager 명명 규칙으로 `vmsize` 이름을 업데이트합니다.  

| 이전 크기 이름 | 업데이트된 크기 이름 | 
|---|---|
| 매우 작음 | Standard_A1_v2 | 
| 소 | Standard_A1_v2 |
| 중간 | Standard_A2_v2 | 
| 대 | Standard_A4_v2 | 
| 매우 큼 | Standard_A8_v2 | 
| A5 | Standard_A2m_v2 | 
| A6 | Standard_A4m_v2 | 
| A7 | Standard_A8m_v2 |  
| A8 | 사용되지 않음 | 
| A9 | 사용되지 않음 |
| A10 | 사용되지 않음 | 
| A11 | 사용되지 않음 | 
| MSODSG5 | 사용되지 않음 | 

 예를 들어 `<WorkerRole name="WorkerRole1" vmsize="Medium"`는 `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`가 됩니다.
 
> [!NOTE]
> 사용 가능한 크기 목록을 검색하려면 [Resource Skus - List](/rest/api/compute/resourceskus/list)를 참조하고 다음 필터를 적용합니다. <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) 이전 원격 데스크톱 플러그 인 제거
이전 원격 데스크톱 플러그 인을 활용한 배포에서는 서비스 정의(.csdef) 파일 및 모든 관련 인증서에서 모듈을 제거해야 합니다. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
이전 진단 플러그 인을 활용한 배포에서는 서비스 정의(.csdef) 파일에서 각 역할에 대한 설정을 제거해야 합니다.

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```
## <a name="access-control"></a>Access Control

네트워킹 리소스를 포함하는 구독에는 Cloud Services(추가 지원)에 대한 [네트워크 기여자](../role-based-access-control/built-in-roles.md#network-contributor) 이상의 액세스 권한이 있어야 합니다. 자세한 내용은 [RBAC 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="key-vault-creation"></a>Key Vault 생성 

Key Vault는 Cloud Services(추가 지원)에 연결된 인증서를 저장하는 데 사용됩니다. 인증서를 Key Vault에 추가한 다음, 서비스 구성 파일에서 인증서 지문을 참조합니다. 또한 Cloud Services(추가 지원) 리소스가 Key Vault에서 비밀로 저장된 인증서를 검색할 수 있도록 포털에서 '배포를 위한 Azure Virtual Machines'에 Key Vault '액세스 정책'을 사용하도록 설정해야 합니다. [Azure Portal](../key-vault/general/quick-create-portal.md)에서 또는 [PowerShell](../key-vault/general/quick-create-powershell.md)을 사용하여 키 자격 증명 모음을 배포할 수 있습니다. 키 자격 증명 모음은 클라우드 서비스와 동일한 지역 및 구독에 생성되어야 합니다. 자세한 내용은 [Azure Cloud Services(추가 지원)에서 인증서 사용](certificates-and-key-vault.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support)를 방문합니다.
