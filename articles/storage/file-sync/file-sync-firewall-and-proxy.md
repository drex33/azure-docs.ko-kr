---
title: Azure 파일 동기화 온-프레미스 방화벽 및 프록시 설정 | Microsoft Docs
description: Azure 파일 동기화 온-프레미스 프록시 및 방화벽 설정을 이해합니다. 포트, 네트워크 및 Azure에 대한 특수 연결에 대한 구성 세부 정보를 검토합니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 64c6f101d0b8acd9c3d0ca00593c4b63b2b21a83
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080502"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Azure 파일 동기화 프록시 및 방화벽 설정

Azure 파일 동기화는 온-프레미스 서버를 Azure Files에 연결하여, 다중 사이트 동기화 및 클라우드 계층화 기능을 사용하도록 설정합니다. 따라서 온-프레미스 서버가 인터넷에 연결되어야 합니다. IT 관리자는 서버가 Azure 클라우드 서비스에 연결하는 최상의 경로를 결정해야 합니다.

이 문서에서는 서버를 Azure 파일 동기화에 성공적이면서 안전하게 연결하기 위해 사용할 수 있는 특정 요구 사항 및 옵션에 대해 설명합니다.

이 방법 가이드를 읽기 전에 [Azure 파일 동기화 네트워킹 고려 사항](file-sync-networking-overview.md)을 읽어보는 것이 좋습니다.

## <a name="overview"></a>개요

Azure 파일 동기화는 Windows Server, Azure 파일 공유 및 일부 기타 Azure 서비스 간에 오케스트레이션 서비스로 작동하여, 동기화 그룹에 설명된 대로 데이터를 동기화합니다. Azure 파일 동기화가 제대로 작동하려면 다음 Azure 서비스와 통신하도록 서버를 구성해야 합니다.

- Azure Storage
- Azure 파일 동기화
- Azure Resource Manager
- 인증 서비스

> [!NOTE]
> Windows Server의 Azure 파일 동기화 에이전트는 클라우드 서비스에 대한 모든 요청을 시작하므로, 방화벽 관점에서 아웃바운드 트래픽만 고려하면 됩니다. <br /> Azure 파일 동기화 에이전트와 연결을 시작하는 Azure 서비스는 없습니다.

## <a name="ports"></a>포트

Azure 파일 동기화는 파일 데이터 및 메타데이터를 HTTPS를 통해서만 이동하며 아웃바운드로 포트 443이 열려 있도록 요구합니다.
따라서 모든 트래픽이 암호화됩니다.

## <a name="networks-and-special-connections-to-azure"></a>네트워크 및 Azure에 대한 특수 연결

Azure 파일 동기화 에이전트는 Azure에 대해 [ExpressRoute](../../expressroute/expressroute-introduction.md) 등과 같은 특수한 채널을 요구하지 않습니다.

Azure 파일 동기화는 Azure로 연결될 수 있는 모든 방식에 작동하며, 대역폭, 대기 시간과 같은 다양한 네트워크 특성에 맞게 자동으로 조정되고 미세 조정을 위한 관리 제어 기능을 제공합니다.

## <a name="proxy"></a>Proxy (프록시)

Azure 파일 동기화는 앱별 및 머신 차원의 프록시 설정을 지원합니다.

**앱별 프록시 설정** 을 통해 Azure 파일 동기화 트래픽에 대한 프록시를 적절하게 구성할 수 있습니다. 앱별 프록시 설정은 에이전트 버전 4.0.1.0 이상에서 지원되며 에이전트 설치 중 또는 Set-StorageSyncProxyConfiguration PowerShell cmdlet을 사용하여 구성될 수 있습니다.

앱별 프록시 설정을 구성하기 위한 PowerShell 명령입니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```

예를 들어 프록시 서버에 사용자 이름과 암호를 사용한 인증이 필요한 경우 다음 PowerShell 명령을 실행합니다.

```powershell
# IP address or name of the proxy server.
$Address="127.0.0.1"

# The port to use for the connection to the proxy.
$Port=8080

# The user name for a proxy.
$UserName="user_name"

# Please type or paste a string with a password for the proxy.
$SecurePassword = Read-Host -AsSecureString

$Creds = New-Object System.Management.Automation.PSCredential ($UserName, $SecurePassword)

# Please verify that you have entered the password correctly.
Write-Host $Creds.GetNetworkCredential().Password

Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"

Set-StorageSyncProxyConfiguration -Address $Address -Port $Port -ProxyCredential $Creds
```

서버의 전체 트래픽이 프록시를 통해 라우팅되므로 **머신 수준 프록시 설정** 은 Azure 파일 동기화 에이전트에 투명합니다.

머신 수준 프록시 설정을 구성하려면 다음 단계를 수행합니다.

1. .NET 애플리케이션에 대한 프록시 설정을 구성합니다.

   - 다음 두 파일을 편집합니다.  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - <system.net> 섹션을 machine.config 파일(<system.serviceModel> 섹션 아래)에 추가합니다.  127.0.01:8888을 프록시 서버의 IP 주소와 포트로 변경합니다. 

     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. WinHTTP 프록시 설정을 지정합니다.

   > [!NOTE]
   > 프록시 서버를 사용하도록 Windows Server를 구성하는 방법에는 여러 가지(WPAD, PAC 파일, netsh 등)가 있습니다. 아래 단계는 netsh를 사용하여 프록시 설정을 구성하는 방법을 설명하지만 [Windows의 프록시 서버 설정 구성](/troubleshoot/windows-server/networking/configure-proxy-server-settings) 설명서에 나열된 모든 방법이 지원됩니다.

   - 관리자 권한 명령 프롬프트 또는 PowerShell에서 다음 명령을 실행하여 기존 프록시 설정을 확인합니다.

     netsh winhttp show proxy

   - 관리자 권한 명령 프롬프트 또는 PowerShell에서 다음 명령을 실행하여 프록시 설정을 지정합니다(127.0.01:8888을 프록시 서버의 IP 주소와 포트로 변경).

     netsh winhttp set proxy 127.0.0.1:8888

3. 관리자 권한 명령 프롬프트 또는 PowerShell에서 다음 명령을 실행하여 Storage 동기화 에이전트 서비스를 다시 시작합니다.

      net stop filesyncsvc

      참고: Storage 동기화 에이전트(filesyncsvc) 서비스는 중지되면 자동으로 시작됩니다.

## <a name="firewall"></a>방화벽

이전 섹션에서 설명한 것처럼 포트 443을 아웃바운드로 열어 두어야 합니다. 데이터 센터, 분기 또는 지역의 정책에 따라, 이 포트를 통한 트래픽을 특정 도메인으로 추가로 제한하는 것이 바람직하거나 필요할 수 있습니다.

다음 표에서는 통신에 필요한 도메인에 대해 설명합니다.

| 서비스 | 퍼블릭 클라우드 엔드포인트 | Azure Government 엔드포인트 | 사용 |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | 초기 서버 등록 호출을 포함하는 모든 사용자 호출(예: PowerShell)은 이 URL로 이동되거나 이 URL을 통해 이동됩니다. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager 호출은 인증된 사용자가 수행해야 합니다. 성공하기 위해 이 URL이 사용자 인증에 사용됩니다. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Azure 파일 동기화 배포의 일부로, 구독의 Azure Active Directory에 서비스 주체가 만들어집니다. 이 URL이 해당 작업에 사용됩니다. 이 보안 주체는 Azure 파일 동기화 서비스에 대한 최소한의 권한 집합을 위임하는 데 사용됩니다. Azure 파일 동기화의 초기 설정을 수행하는 사용자는 구독 소유자 권한이 있는 인증된 사용자여야 합니다. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | 퍼블릭 엔드포인트 URL을 사용합니다. | 이 URL은 Azure 파일 동기화 서버 등록 UI가 관리자 로그인에 사용하는 Active Directory 인증 라이브러리에서 액세스합니다. |
| **Azure Storage** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | 서버는 파일을 다운로드할 때 스토리지 계정의 Azure 파일 공유와 직접 소통하면서 데이터 이동을 보다 효율적으로 수행합니다. 서버에는 대상으로 지정된 파일 공유 액세스만 허용하는 SAS 키가 있습니다. |
| **Azure 파일 동기화** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | 초기 서버 등록 후 서버는 해당 지역에서 Azure 파일 동기화 서비스 인스턴스에 대한 지역별 URL을 수신합니다. 서버는 이 URL을 사용하여 동기화를 처리하는 인스턴스와 직접 효율적으로 통신할 수 있습니다. |
| **Microsoft PKI** |  https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | https://www.microsoft.com/pki/mscorp/cps<br>http://crl.microsoft.com/pki/mscorp/crl/<br>http://mscrl.microsoft.com/pki/mscorp/crl/<br>http://ocsp.msocsp.com<br>http://ocsp.digicert.com/<br>http://crl3.digicert.com/ | Azure 파일 동기화 에이전트가 설치되면 PKI URL을 사용하여 Azure 파일 동기화 서비스 및 Azure 파일 공유와 통신하는 데 필요한 중간 인증서를 다운로드합니다. OCSP URL은 인증서의 상태를 확인하는 데 사용됩니다. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.ctldl.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Azure 파일 동기화 에이전트가 설치되면 Microsoft 업데이트 URL을 사용하여 Azure 파일 동기화 에이전트 업데이트를 다운로드합니다. |

> [!IMPORTANT]
> &ast;.afs.azure.net에 대한 트래픽을 허용하면 동기화 서비스에 대한 트래픽만 가능합니다. 이 도메인을 사용하는 다른 Microsoft 서비스는 없습니다.
> &ast;.one.microsoft.com에 대한 트래픽을 허용할 때 서버에서 동기화 서비스 이외의 위치로 트래픽이 전달될 수 있습니다. 하위 도메인에서 더 많은 Microsoft 서비스를 사용할 수 있습니다.

&ast;.afs.azure.net 또는 &ast;.one.microsoft.com이 너무 광범위한 경우 Azure 파일 동기화 서비스의 명시적 지역 인스턴스로만 통신을 허용하여 서버의 통신을 제한할 수 있습니다. 선택할 인스턴스는 서버를 배포 및 등록한 스토리지 동기화 서비스의 지역에 따라 다릅니다. 이 지역을 아래 표에서는 "기본 엔드포인트 URL"이라고 부릅니다.

BCDR(비즈니스 연속성 및 재해 복구)을 위해 GRS(지역 중복 스토리지)용으로 구성된 스토리지 계정에 Azure 파일 공유를 만들었을 수 있습니다. 이 경우 Azure 파일 공유는 지속적인 지역 정전 시 쌍을 이루는 지역에 장애 조치(failover)됩니다. Azure 파일 동기화는 동일한 지역 쌍을 스토리지로 사용합니다. 따라서 GRS 스토리지 계정을 사용하는 경우 서버가 Azure 파일 동기화의 쌍을 이루는 지역과 통신할 수 있도록 추가 URL을 설정해야 합니다. 아래 표에서는 이를 "쌍을 이루는 지역"이라고 합니다. 마찬가지로 Traffic Manager 프로필 URL도 사용하도록 설정해야 합니다. 이렇게 하면 장애 조치 시 네트워크 트래픽을 쌍을 이루는 지역으로 원활하게 다시 라우팅할 수 있으며, 이것을 아래 표에서는 "검색 URL"이라고 부릅니다.

| 클라우드  | 지역 | 기본 엔드포인트 URL | 쌍을 이루는 지역 | 검색 URL |
|--------|--------|----------------------|---------------|---------------|
| 공용 |오스트레일리아 동부 | https:\//australiaeast01.afs.azure.net<br>https:\//kailani-aue.one.microsoft.com | 오스트레일리아 남동부 | https:\//tm-australiaeast01.afs.azure.net<br>https:\//tm-kailani-aue.one.microsoft.com |
| 공용 |오스트레일리아 남동부 | https:\//australiasoutheast01.afs.azure.net<br>https:\//kailani-aus.one.microsoft.com | 오스트레일리아 동부 | https:\//tm-australiasoutheast01.afs.azure.net<br>https:\//tm-kailani-aus.one.microsoft.com |
| 공용 | 브라질 남부 | https:\//brazilsouth01.afs.azure.net | 미국 중남부 | https:\//tm-brazilsouth01.afs.azure.net |
| 공용 | 캐나다 중부 | https:\//canadacentral01.afs.azure.net<br>https:\//kailani-cac.one.microsoft.com | 캐나다 동부 | https:\//tm-canadacentral01.afs.azure.net<br>https:\//tm-kailani-cac.one.microsoft.com |
| 공용 | 캐나다 동부 | https:\//canadaeast01.afs.azure.net<br>https:\//kailani-cae.one.microsoft.com | 캐나다 중부 | https:\//tm-canadaeast01.afs.azure.net<br>https:\//tm-kailani.cae.one.microsoft.com |
| 공용 | 인도 중부 | https:\//centralindia01.afs.azure.net<br>https:\//kailani-cin.one.microsoft.com | 인도 남부 | https:\//tm-centralindia01.afs.azure.net<br>https:\//tm-kailani-cin.one.microsoft.com |
| 공용 | 미국 중부 | https:\//centralus01.afs.azure.net<br>https:\//kailani-cus.one.microsoft.com | 미국 동부 2 | https:\//tm-centralus01.afs.azure.net<br>https:\//tm-kailani-cus.one.microsoft.com |
| 공용 | 동아시아 | https:\//eastasia01.afs.azure.net<br>https:\//kailani11.one.microsoft.com | 동남 아시아 | https:\//tm-eastasia01.afs.azure.net<br>https:\//tm-kailani11.one.microsoft.com |
| 공용 | 미국 동부 | https:\//eastus01.afs.azure.net<br>https:\//kailani1.one.microsoft.com | 미국 서부 | https:\//tm-eastus01.afs.azure.net<br>https:\//tm-kailani1.one.microsoft.com |
| 공용 | 미국 동부 2 | https:\//eastus201.afs.azure.net<br>https:\//kailani-ess.one.microsoft.com | 미국 중부 | https:\//tm-eastus201.afs.azure.net<br>https:\//tm-kailani-ess.one.microsoft.com |
| 공용 | 독일 북부 | https:\//germanynorth01.afs.azure.net | 독일 중서부 | https:\//tm-germanywestcentral01.afs.azure.net |
| 공용 | 독일 중서부 | https:\//germanywestcentral01.afs.azure.net | 독일 북부 | https:\//tm-germanynorth01.afs.azure.net |
| 공용 | 일본 동부 | https:\//japaneast01.afs.azure.net | 일본 서부 | https:\//tm-japaneast01.afs.azure.net |
| 공용 | 일본 서부 | https:\//japanwest01.afs.azure.net | 일본 동부 | https:\//tm-japanwest01.afs.azure.net |
| 공용 | 한국 중부 | https:\//koreacentral01.afs.azure.net/ | 한국 남부 | https:\//tm-koreacentral01.afs.azure.net/ |
| 공용 | 한국 남부 | https:\//koreasouth01.afs.azure.net/ | 한국 중부 | https:\//tm-koreasouth01.afs.azure.net/ |
| 공용 | 미국 중북부 | https:\//northcentralus01.afs.azure.net | 미국 중남부 | https:\//tm-northcentralus01.afs.azure.net |
| 공용 | 북유럽 | https:\//northeurope01.afs.azure.net<br>https:\//kailani7.one.microsoft.com | 서유럽 | https:\//tm-northeurope01.afs.azure.net<br>https:\//tm-kailani7.one.microsoft.com |
| 공용 | 미국 중남부 | https:\//southcentralus01.afs.azure.net | 미국 중북부 | https:\//tm-southcentralus01.afs.azure.net |
| 공용 | 인도 남부 | https:\//southindia01.afs.azure.net<br>https:\//kailani-sin.one.microsoft.com | 인도 중부 | https:\//tm-southindia01.afs.azure.net<br>https:\//tm-kailani-sin.one.microsoft.com |
| 공용 | 동남 아시아 | https:\//southeastasia01.afs.azure.net<br>https:\//kailani10.one.microsoft.com | 동아시아 | https:\//tm-southeastasia01.afs.azure.net<br>https:\//tm-kailani10.one.microsoft.com |
| 공용 | 스위스 북부 | https:\//switzerlandnorth01.afs.azure.net<br>https:\//tm-switzerlandnorth01.afs.azure.net | 스위스 서부 | https:\//switzerlandwest01.afs.azure.net<br>https:\//tm-switzerlandwest01.afs.azure.net |
| 공용 | 스위스 서부 | https:\//switzerlandwest01.afs.azure.net<br>https:\//tm-switzerlandwest01.afs.azure.net | 스위스 북부 | https:\//switzerlandnorth01.afs.azure.net<br>https:\//tm-switzerlandnorth01.afs.azure.net |
| 공용 | 영국 남부 | https:\//uksouth01.afs.azure.net<br>https:\//kailani-uks.one.microsoft.com | 영국 서부 | https:\//tm-uksouth01.afs.azure.net<br>https:\//tm-kailani-uks.one.microsoft.com |
| 공용 | 영국 서부 | https:\//ukwest01.afs.azure.net<br>https:\//kailani-ukw.one.microsoft.com | 영국 남부 | https:\//tm-ukwest01.afs.azure.net<br>https:\//tm-kailani-ukw.one.microsoft.com |
| 공용 | 미국 중서부 | https:\//westcentralus01.afs.azure.net | 미국 서부 2 | https:\//tm-westcentralus01.afs.azure.net |
| 공용 | 서유럽 | https:\//westeurope01.afs.azure.net<br>https:\//kailani6.one.microsoft.com | 북유럽 | https:\//tm-westeurope01.afs.azure.net<br>https:\//tm-kailani6.one.microsoft.com |
| 공용 | 미국 서부 | https:\//westus01.afs.azure.net<br>https:\//kailani.one.microsoft.com | 미국 동부 | https:\//tm-westus01.afs.azure.net<br>https:\//tm-kailani.one.microsoft.com |
| 공용 | 미국 서부 2 | https:\//westus201.afs.azure.net | 미국 중서부 | https:\//tm-westus201.afs.azure.net |
| 정부 | US Gov 애리조나 | https:\//usgovarizona01.afs.azure.us | US Gov 텍사스 | https:\//tm-usgovarizona01.afs.azure.us |
| 정부 | US Gov 텍사스 | https:\//usgovtexas01.afs.azure.us | US Gov 애리조나 | https:\//tm-usgovtexas01.afs.azure.us |

- LRS(로컬 중복 스토리지) 또는 ZRS(영역 중복 스토리지)용으로 구성된 스토리지 계정을 사용하는 경우 "기본 엔드포인트 URL"에 나열된 URL만 사용하도록 설정해야 합니다.

- GRS용으로 구성된 스토리지 계정을 사용하는 경우 세 가지 URL을 사용하도록 설정합니다.

**예:**`"West US"`에 스토리지 동기화 서비스를 배포하고 서버를 등록합니다. 여기서 서버가 통신할 수 있는 URL은 다음과 같습니다.

> - https:\//westus01.afs.azure.net (primary endpoint: West US)
> - https:\//eastus01.afs.azure.net (paired fail-over region: East US)
> - https:\//tm-westus01.afs.azure.net (discovery URL of the primary region)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Azure 파일 동기화 IP 주소에 대한 허용 목록

Azure 파일 동기화는 지정된 Azure 서비스에 대한 IP 주소 접두사 그룹을 나타내는 [서비스 태그](../../virtual-network/service-tags-overview.md) 사용을 지원합니다. 서비스 태그를 사용하여 Azure 파일 동기화 서비스와 통신할 수 있는 방화벽 규칙을 만들 수 있습니다. Azure 파일 동기화의 서비스 태그는 `StorageSyncService`입니다.

Azure 내에서 Azure 파일 동기화를 사용하는 경우 네트워크 보안 그룹에서 직접 서비스 태그 이름을 사용하여 트래픽을 허용할 수 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md)을 참조하세요.

온-프레미스 Azure 파일 동기화를 사용하는 경우 서비스 태그 API를 사용하여 방화벽의 허용 목록에 대한 특정 IP 주소 범위를 가져올 수 있습니다. 이 정보를 가져오는 방법에는 다음 두 가지가 있습니다.

- 서비스 태그를 지원하는 모든 Azure 서비스에 대한 IP 주소 범위의 현재 목록은 Microsoft 다운로드 센터에서 JSON 문서 형식으로 매주 게시됩니다. 각 Azure 클라우드는 해당 클라우드와 관련된 IP 주소 범위를 포함하는 자체 JSON 문서를 포함합니다.
  - [Azure 공용](https://www.microsoft.com/download/details.aspx?id=56519)
  - [Azure 미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)
  - [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062)
  - [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)
- 서비스 태그 검색 API(미리 보기)를 사용 하면 현재 서비스 태그 목록을 프로그래밍 방식으로 검색할 수 있습니다. 미리 보기에서 서비스 태그 검색 API는 Microsoft 다운로드 센터에 게시된 JSON 문서에서 반환된 정보 보다 최신 정보가 아닌 정보를 반환할 수 있습니다. 자동화 기본 설정에 따라 API surface를 사용할 수 있습니다.
  - [REST API](/rest/api/virtualnetwork/servicetags/list)
  - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
  - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

서비스 태그 검색 API는 Microsoft 다운로드 센터에 게시된 JSON 문서만큼 자주 업데이트되지 않으므로 JSON 문서를 사용하여 온-프레미스 방화벽의 허용 목록을 업데이트하는 것이 좋습니다. 이 작업은 다음과 같이 수행할 수 있습니다.

```powershell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

그런 다음, `$ipAddressRanges`의 IP 주소 범위를 사용하여 방화벽을 업데이트할 수 있습니다. 방화벽 업데이트 방법에 대한 자세한 내용은 방화벽/네트워크 어플라이언스의 웹 사이트를 확인하세요.

## <a name="test-network-connectivity-to-service-endpoints"></a>서비스 엔드포인트에 대한 네트워크 연결 테스트

서버가 Azure 파일 동기화 서비스에 등록되면 Test-StorageSyncNetworkConnectivity cmdlet 및 ServerRegistration.exe를 사용하여 이 서버와 관련된 모든 엔드포인트(URL)와의 통신을 테스트할 수 있습니다. 이 cmdlet은 불완전한 통신으로 인해 서버에서 Azure 파일 동기화가 완전히 작동하지 못할 때 문제를 해결하는 데 유용하며, 프록시 및 방화벽 구성을 미세 조정하는 데도 사용할 수 있습니다.

네트워크 연결 테스트를 실행하려면 Azure 파일 동기화 에이전트 버전 9.1 이상을 설치하고 다음 PowerShell 명령을 실행합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>요약 및 위험 제한

이 문서의 앞부분에 나오는 목록에는 Azure 파일 동기화가 현재 통신하는 URL이 포함되어 있습니다. 방화벽은 이러한 도메인의 아웃바운드 트래픽을 허용할 수 있어야 합니다. Microsoft는 이 목록을 업데이트 상태로 유지하려고 합니다.

방화벽 규칙을 제한하는 도메인을 설정하는 것도 보안을 강화하는 방법이 될 수 있습니다. 이러한 방화벽 구성을 사용하는 경우 시간이 지남에 따라 URL이 추가되고 변경될 수 있다는 점에 유의해야 합니다. 이 문서를 정기적으로 확인하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 파일 동기화 배포에 대한 계획](file-sync-planning.md)
- [Azure 파일 동기화 배포](file-sync-deployment-guide.md)
- [Azure 파일 동기화 모니터링](file-sync-monitoring.md)
