---
title: Red Hat Enterprise Linux의 BYOS(사용자 구독 필요) Azure 이미지 | Microsoft Docs
description: Azure에서 제공되는 Red Hat Enterprise Linux의 BYOS(사용자 구독 필요) 이미지에 대해 알아봅니다.
author: mamccrea
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 06/10/2020
ms.author: mamccrea
ms.openlocfilehash: 59a89c069c332d4d0346e0f3c795763818c601de
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457110"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Azure에서 제공되는 Red Hat Enterprise Linux의 BYOS(사용자 구독 필요) 골드 이미지

**적용 대상:** :heavy_check_mark: Linux VM 

RHEL(Red Hat Enterprise Linux) 이미지는 종량제 또는 BYOS(사용자 구독 필요)(Red Hat 골드 이미지) 모델을 통해 Azure에서 이용할 수 있습니다. 본 문서에서는 Azure의 Red Hat 골드 이미지에 대한 개요를 제공합니다.

>[!NOTE]
> RHEL BYOS 골드 이미지는 Azure 퍼블릭(상업용) 및 Azure Government 클라우드에서 사용할 수 있습니다. Azure 중국 또는 Azure Blackforest 클라우드에서는 사용할 수 없습니다.

## <a name="important-points-to-consider"></a>고려할 중요한 사항

- 본 프로그램에서 제공하는 Red Hat 골드 이미지는 Azure Marketplace의 RHEL 종량제 이미지와 유사한 프로덕션 준비 RHEL 이미지입니다.
- 해당 이미지는 [Azure 상의 Red Hat Enterprise Linux 이미지](./redhat-images.md)에서 설명한 현재 정책을 따릅니다.
- 표준 지원 정책은 해당 이미지에서 만들어 낸 VM에 적용됩니다.
- Red Hat 골드 이미지에서 프로비저닝한 VM에는 RHEL 종량제 이미지와 관련된 RHEL 요금이 적용되지 않습니다.
- 이미지에 대한 자격이 없습니다. VM에 등록하고 구독하여 Red Hat에서 직접 업데이트를 받으려면 Red Hat 구독 관리자를 사용해야 합니다.
- [Azure 하이브리드 혜택](../../linux/azure-hybrid-benefit-linux.md)을 통해 종량제 이미지를 BYOS로 변경할 수 있습니다. 그러나 처음 배포한 BYOS를 Linux 이미지에 대한 종량제 청구 모델로 변경하는 것은 불가능합니다. 청구 모델을 BYOS에서 종량제로 변경하려면 해당 VM을 해당 이미지에서 재배포해야 합니다.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Red Hat 골드 이미지에 액세스하기 위한 요구 사항 및 약관

1. [Red Hat Cloud Access 프로그램](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 약관에 대해 알아봅니다. [Red Hat 구독 관리자](https://access.redhat.com/management/cloud)에서 Cloud Access에 대한 Red Hat 구독을 사용하도록 설정합니다. Cloud Access를 위해 등록하려는 Azure 구독을 보유 중이어야 합니다.

1. Cloud Access를 위해 사용하도록 설정한 Red Hat 구독이 자격 요구 사항을 만족하는 경우, Azure 구독이 자동적으로 골드 이미지 액세스를 사용할 수 있도록 설정됩니다.

### <a name="expected-time-for-image-access"></a>이미지 액세스에 대한 예상 소요 시간

Cloud Access 사용 단계를 완료한 후, Red Hat이 Red Hat 골드 이미지에 대한 자격 유효성을 검사합니다. 유효성 검사가 성공하면 세 시간 이내에 골드 이미지에 액세스할 수 있게 됩니다.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Azure Portal에서 Red Hat 골드 이미지를 사용하기

1. Azure 구독이 Red Hat 골드 이미지에 대한 액세스를 확보하면, 해당 이미지를 [Azure Portal](https://portal.azure.com)에서 찾을 수 있습니다. **리소스 만들기** > **모두 표시** 로 이동합니다.

1. 페이지 맨 위에서 프라이빗 제안을 볼 수 있습니다.

    ![Marketplace 프라이빗 제안](./media/rhel-byos-privateoffers.png)

1. 자주색 링크를 선택하거나 페이지 맨 아래로 스크롤하여 프라이빗 제안을 확인합니다.

1. UI 프로비저닝의 나머지 부분은 기존의 Red Hat 이미지와 다르지 않습니다. RHEL 버전을 선택하고 VM 프로비저닝을 위해 표시되는 프롬프트를 따릅니다. 해당 프로세스를 통해서도 최종 단계에서 해당 이미지에 대한 약관에 동의할 수 있습니다.

>[!NOTE]
>지금까지의 단계들은 프로그래밍 방식의 배포를 위해 Red Hat 골드 이미지를 사용하도록 설정하지 않습니다. "추가 정보" 섹션에서 설명한 것처럼 추가적인 단계가 필요합니다.

본 문서의 나머지 부분은 이미지의 약관을 프로비저닝하고 허용하도록 하는 CLI 방법을 중점적으로 설명합니다. 최종 결과(프로비저닝된 RHEL 골드 이미지 VM)가 관련되어 있는 한 UI와 CLI는 완벽히 교환할 수 있습니다.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Azure CLI의 Red Hat 골드 이미지 사용

다음 지침은 Azure CLI을 사용하여 RHEL VM에 대한 초기 배포 프로세스를 안내합니다. 해당 지침들은 [Azure CLI가 이미 설치](/cli/azure/install-azure-cli)된 상황으로 가정합니다.

>[!IMPORTANT]
>다음의 모든 명령에 대해 게시자, 제안, 계획, 이미지 참조에서 소문자만을 사용해야 합니다.

1. 원하는 구독 중인지 확인합니다.

    ```azurecli
    az account show -o=json
    ```

1. Red Hat 골드 이미지 VM에 대한 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. 해당 이미지의 약관에 동의합니다.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >해당 약관은 *Azure 구독 때마다, 이미지 SKU* 마다 한 번씩 동의해야 합니다.

1. (옵션) 다음 명령을 통해 VM 배포의 유효성을 검사합니다.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. 이전 예제와 같이 `--validate` 인수를 사용하지 않고 동일한 명령을 실행하여 VM을 프로비저닝합니다.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. SSH를 VM에 적용하고 자격 없는 이미지가 있는지 확인합니다. 해당 단계를 수행하려면 `sudo yum repolist`를 실행합니다. RHEL 8의 경우, `sudo dnf repolist`를 사용합니다. 출력은 Red Hat를 통한 VM 등록에 구독 관리자를 사용할 것을 요구합니다.

>[!NOTE]
>RHEL 8에서는 `dnf` 및 `yum`이 교환 가능합니다. [RHEL 8 관리자 가이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index)에서 자세한 내용을 참조하세요.

## <a name="use-the-red-hat-gold-images-from-powershell"></a>PowerShell의 Red Hat 골드 이미지 사용

다음 스크립트는 예제입니다. 원하는 구성을 통해 리소스 그룹, 위치, VM 이름, 로그인 정보, 기타 변수를 교체합니다. 게시자와 계획 정보는 소문자로만 작성해야 합니다.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Red Hat Enterprise Linux BYOS(사용자 구독 필요) 골드 이미지 암호화

Red Hat Enterprise Linux BYOS 골드 이미지는 [Azure Disk Encryption](../../linux/disk-encryption-overview.md)을 사용하여 보호할 수 있습니다. 암호화를 사용하도록 설정하기 전에 *반드시* 해당 구독이 등록되어 있어야 합니다. RHEL BYOS 골드 이미지 등록 방법에 대해서는 [Red Hat 구독 관리자를 사용하여 시스템을 Red Hat Customer Portal에 등록하고 구독하는 방법](https://access.redhat.com/solutions/253273)에서 자세히 알아보세요. 활성 Red Hat 구독이 있으면, [Red Hat 고객 포털 활성화 키 생성](https://access.redhat.com/articles/1378093)을 읽을 수도 있습니다.

Azure Disk Encryption은 [Red Hat 사용자 지정 이미지](../../linux/redhat-create-upload-vhd.md)에서는 지원되지 않습니다. Azure Disk Encryption의 추가 요구 사항 및 필수 구성 요소는 [Linux VM용 Azure Disk Encryption](../../linux/disk-encryption-overview.md#additional-vm-requirements)에 설명되어 있습니다.

Azure Disk Encryption 적용 단계에 대해서는 [Linux VM에서의 Azure Disk Encryption 시나리오](../../linux/disk-encryption-linux.md) 및 관련 문서를 참조하세요.

## <a name="additional-information"></a>추가 정보

- 본 제품을 사용하도록 설정하지 않은 구독에서 VM을 프로비저닝하려고 하면 다음 메시지가 표시됩니다.

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    이러한 경우에는 Microsoft 또는 Red Hat에 문의하여 구독을 사용하도록 설정합니다.

- RHEL BYOS 이미지의 스냅샷을 수정하거나 해당 사용자 지정 이미지를 [Shared Image Gallery](../../shared-image-galleries.md)에 게시하려는 경우, 해당 스냅샷의 원본과 일치하는 계획 정보를 제공해야 합니다. 예를 들면, 해당 명령은 다음과 같을 수 있습니다.

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    마지막 줄의 계획 매개 변수를 확인합니다.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images)은 사용자 지정 이미지에서 지원되지 않습니다.

- VM을 자동으로 RHEL BYOS 이미지에서 프로비저닝하는 경우, 샘플 명령에 표시한 것과 비슷한 계획 매개 변수를 제공해야 합니다. 예를 들어 Terraform을 사용하는 경우, [계획 차단](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)에 계획 정보를 제공합니다.

## <a name="next-steps"></a>다음 단계

- Red Hat Cloud Access에 대해서는 [Red Hat 퍼블릭 클라우드 설명서](https://access.redhat.com/public-cloud)에서 더 자세히 확인할 수 있습니다.
- Cloud Access에 대한 단계별 가이드 및 프로그램 세부 정보는 [Red Hat Cloud Access 설명서](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)를 참고하세요.
- Red Hat 업데이트 인프라에 대해서는 [Azure Red Hat 업데이트 인프라](./redhat-rhui.md)에서 자세히 확인하세요.
- Azure의 전체 Red Hat 이미지에 대해서는 [설명서 페이지](./redhat-images.md)에서 자세히 확인하세요.
- 전체 RHEL 버전에 대한 Red Hat 지원 정책 관련 정보는 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata) 페이지를 참조하세요.
- RHEL 골드 이미지 관련 추가 설명서는 [Red Hat 설명서](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access#proc_using-gold-images-azure_cloud-access)에서 확인하세요.
