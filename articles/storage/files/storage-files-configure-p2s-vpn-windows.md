---
title: Azure Files에서 사용하기 위한 Windows의 P2S(지점 및 사이트 간) VPN 구성 | Microsoft Docs
description: Azure Files에서 사용하기 위한 Windows의 P2S(지점 및 사이트 간) VPN 구성 방법
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e73d987ad8029d1ee65af0f92173561230af599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589248"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Azure Files에서 사용하기 위한 Windows의 P2S(지점 및 사이트 간) VPN 구성
P2S(지점 및 사이트 간) VPN 연결을 사용하여 포트 445을 열지 않고 Azure 외부에서 SMB를 통해 Azure 파일 공유를 탑재할 수 있습니다. 지점 및 사이트 간 VPN 연결은 Azure와 개별 클라이언트 간의 VPN 연결입니다. Azure Files에서 P2S VPN 연결을 사용하려면 연결하려는 각 클라이언트에 대해 P2S VPN 연결을 구성해야 합니다. 온-프레미스 네트워크에서 Azure 파일 공유에 연결해야 하는 클라이언트가 많은 경우에는 각 클라이언트에 대해 지점 및 사이트 간 연결 대신 S2S(사이트 간) VPN 연결을 사용할 수 있습니다. 자세한 내용은 [Azure Files에서 사용하기 위한 사이트 간 VPN 구성](storage-files-configure-s2s-vpn.md)을 참조하세요.

Azure Files에 사용할 수 있는 네트워킹 옵션에 대해 자세히 알아보려면 이 방법 문서를 계속하기 전에 먼저 [직접 Azure 파일 공유 액세스에 대한 네트워킹 고려 사항](storage-files-networking-overview.md)을 읽어볼 것을 강력하게 권장합니다.

이 문서에서는 Azure 파일 공유를 온-프레미스에 직접 탑재하도록 Windows(Windows 클라이언트 및 Windows Server)에서 지점 및 사이트 간 VPN을 구성하는 단계를 자세히 설명합니다. VPN을 통해 Azure 파일 동기화 트래픽을 라우팅하는 경우 [Azure 파일 동기화 프록시 및 방화벽 설정 구성](../file-sync/file-sync-firewall-and-proxy.md)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>사전 요구 사항
- 최신 버전의 Azure PowerShell 모듈. Azure PowerShell을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하고 해당 운영 체제를 선택합니다. Windows에서 Azure CLI를 사용하고 싶은 분들은 그렇게 해도 되지만, 아래 지침은 Azure PowerShell에 대한 지침입니다.

- 온-프레미스에 탑재하려는 Azure 파일 공유. Azure 파일 공유는 스토리지 계정 내에 배포됩니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다. [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)에서 Azure 파일 공유 및 스토리지 계정을 배포하는 방법에 대해 자세히 알아봅니다.

- 온-프레미스에 탑재하려는 Azure 파일 공유가 포함된 스토리지 계정의 프라이빗 엔드포인트가 있는 가상 네트워크입니다. 프라이빗 엔드포인트를 만드는 방법에 대한 자세한 내용은 [Azure Files 네트워크 엔드포인트 구성](storage-files-networking-endpoints.md?tabs=azure-powershell)을 참조하세요. 

## <a name="collect-environment-information"></a>환경 정보 수집
지점 및 사이트 간 VPN을 설정하기 위해 먼저 가이드 전체에서 사용할 사용자 환경에 대한 일부 정보를 수집해야 합니다. 스토리지 계정, 가상 네트워크 및/또는 프라이빗 엔드포인트를 아직 만들지 않은 경우 [필수 조건](#prerequisites) 섹션을 참조하세요.

`<resource-group>`, `<vnet-name>`, `<subnet-name>`, `<storage-account-name>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```PowerShell
$resourceGroupName = "<resource-group-name>" 
$virtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"
$storageAccountName = "<storage-account-name>"

$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$subnetId = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq "StorageAccountSubnet" } | `
    Select-Object -ExpandProperty Id

$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

$privateEndpoint = Get-AzPrivateEndpoint | `
    Where-Object {
        $subnets = $_ | `
            Select-Object -ExpandProperty Subnet | `
            Where-Object { $_.Id -eq $subnetId }

        $connections = $_ | `
            Select-Object -ExpandProperty PrivateLinkServiceConnections | `
            Where-Object { $_.PrivateLinkServiceId -eq $storageAccount.Id }
        
        $null -ne $subnets -and $null -ne $connections
    } | `
    Select-Object -First 1
```

## <a name="create-root-certificate-for-vpn-authentication"></a>VPN 인증에 사용할 루트 인증서 만들기
온-프레미스 Windows 머신에서 VPN 연결을 인증하여 가상 네트워크에 액세스할 수 있도록 하려면 두 개의 인증서를 만들어야 합니다. 하나는 가상 머신 게이트웨이에 제공되는 루트 인증서이고, 다른 하나는 루트 인증서로 서명되는 클라이언트 인증서입니다. 다음 PowerShell은 루트 인증서를 만듭니다. 클라이언트 인증서는 게이트웨이의 정보를 사용하여 Azure 가상 네트워크 게이트웨이를 만든 후에 만들어집니다. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>가상 네트워크 게이트웨이 배포
Azure 가상 네트워크 게이트웨이는 온-프레미스 Windows 머신에서 연결할 서비스입니다. 이 서비스를 배포하려면 두 가지 기본 구성 요소가 필요합니다. 즉, 전 세계에 있는 클라이언트에 대한 게이트웨이를 식별하는 공용 IP와 클라이언트를 인증하는 데 사용되는 이전에 만든 루트 인증서가 필요합니다.

`<desired-vpn-name-here>`를 이러한 리소스에 사용할 이름으로 바꿔야 합니다.

> [!Note]  
> Azure 가상 네트워크 게이트웨이를 배포하는 데 최대 45분이 걸릴 수 있습니다. 이 리소스가 배포되는 동안 이 PowerShell 스크립트는 배포가 완료될 때까지 차단됩니다. 예상된 동작입니다.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>클라이언트 인증서 만들기
클라이언트 인증서는 가상 네트워크 게이트웨이의 URI를 사용하여 생성됩니다. 이 인증서는 이전에 만든 루트 인증서를 사용하여 서명됩니다.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>VPN 클라이언트 구성
Azure 가상 네트워크 게이트웨이는 온-프레미스 Windows 머신에서 VPN 연결을 초기화하는 데 필요한 구성 파일이 포함된 다운로드 가능한 패키지를 만듭니다. Windows 10/Windows Server 2016 이상의 [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) 기능을 사용하여 VPN 연결을 구성하겠습니다. 이 패키지에는 원하는 경우 레거시 Windows VPN 클라이언트를 구성할 수 있는 실행 가능 패키지도 포함되어 있습니다. 이 가이드에서는 레거시 Windows VPN 클라이언트가 아닌 Always On VPN을 사용합니다. Always On VPN 클라이언트는 최종 사용자가 머신에 대한 관리자 권한이 없어도 Azure VPN에서 연결/연결 해제할 수 있기 때문입니다. 

다음 스크립트는 가상 네트워크 게이트웨이에 대한 인증에 필요한 클라이언트 인증서를 설치하고, VPN 패키지를 다운로드하여 설치합니다. 잊지 말고 `<computer1>` 및 `<computer2>`를 적절한 정보로 바꿔야 합니다. `$sessions` 배열에 PowerShell 세션을 더 추가하여 원하는 수의 머신에서 이 스크립트를 실행할 수 있습니다. 사용 계정은 이러한 각 머신의 관리자여야 합니다. 이러한 머신 중 하나가 스크립트를 실행하는 로컬 머신이면 관리자 권한 PowerShell 세션에서 스크립트를 실행해야 합니다. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Azure 파일 공유 탑재
지점 및 사이트 간 VPN을 설정했으므로, PowerShell을 통해 설정한 컴퓨터에서 Azure 파일 공유를 탑재하는 데 사용할 수 있습니다. 다음 예제는 공유를 탑재하고, 공유가 실제로 탑재되었는지 증명하기 위해 공유의 루트 디렉터리를 나열하고, 공유를 탑재 해제합니다. 아쉽게도 PowerShell 원격을 통해 영구적으로 공유를 탑재할 수는 없습니다. 영구적으로 탑재하려면 [Windows에서 Azure 파일 공유 사용](storage-how-to-use-files-windows.md)을 참조하세요. 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="rotate-vpn-root-certificate"></a>VPN 루트 인증서 회전
만료 또는 새 요구 사항으로 인해 루트 인증서를 회전 해야 하는 경우 가상 네트워크 게이트웨이를 다시 배포 하지 않고도 기존 가상 네트워크 게이트웨이에 새 루트 인증서를 추가할 수 있습니다.  다음 샘플 스크립트를 사용 하 여 루트 인증서를 추가한 후에는 [VPN 클라이언트 인증서](#create-client-certificate)를 다시 만들어야 합니다.  

`<resource-group-name>`, `<desired-vpn-name-here>` 및를 `<new-root-cert-name>` 고유한 값으로 바꾼 다음 스크립트를 실행 합니다.

```PowerShell
#Creating the new Root Certificate
$ResourceGroupName = "<resource-group-name>"
$vpnName = "<desired-vpn-name-here>"
$NewRootCertName = "<new-root-cert-name>"

$rootcertname = "CN=$NewRootCertName"
$certLocation = "Cert:\CurrentUser\My"
$date = get-date -Format "MM_yyyy"
$vpnTemp = "C:\vpn-temp_$date\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}

#Fetching gateway details and adding the newly created Root Certificate.
$gateway = Get-AzVirtualNetworkGateway -Name $vpnName -ResourceGroupName $ResourceGroupName

Add-AzVpnClientRootCertificate `
    -PublicCertData $rootCertificate `
    -ResourceGroupName $ResourceGroupName `
    -VirtualNetworkGatewayName $gateway `
    -VpnClientRootCertificateName $NewRootCertName

```
## <a name="see-also"></a>참고 항목
- [직접 Azure 파일 공유 액세스를 위한 네트워킹 고려 사항](storage-files-networking-overview.md)
- [Azure Files에서 사용할 P2S(지점 및 사이트 간) VPN을 Linux에 구성](storage-files-configure-p2s-vpn-linux.md)
- [Azure Files에서 사용할 S2S(사이트 간) VPN 구성](storage-files-configure-s2s-vpn.md)
