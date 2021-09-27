---
title: Azure Files의 SMB 파일 공유
description: SMB(서버 메시지 블록) 프로토콜을 사용하여 Azure Files에서 호스트되는 파일 공유에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a1cc9d12e6a24820b5b84f8a1a275d8451d6247
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776940"
---
# <a name="smb-file-shares-in-azure-files"></a>Azure Files의 SMB 파일 공유
Azure Files는 Azure 파일 공유를 탑재하기 위한 두 가지 업계 표준 프로토콜인 [SMB(서버 메시지 블록)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 프로토콜(미리 보기)과 [NFS(네트워크 파일 시스템)](https://en.wikipedia.org/wiki/Network_File_System) 프로토콜을 제공합니다. Azure Files를 사용하여 워크로드에 가장 적합한 파일 시스템 프로토콜을 선택할 수 있습니다. 동일한 스토리지 계정 내에서 SMB 및 NFS 파일 공유를 만들 수 있지만 Azure 파일 공유는 SMB 및 NFS 프로토콜 둘 다에서 개별 Azure 파일 공유에 액세스하는 것을 지원하지는 않습니다. 모든 파일 공유에 대해 Azure Files는 스토리지 요구 사항에 맞게 스케일 업할 수 있고 수천 개의 클라이언트에서 동시에 액세스할 수 있는 엔터프라이즈급 파일 공유를 제공합니다.

이 문서에서는 SMB Azure 파일 공유에 대해 설명합니다. NFS Azure 파일 공유에 대한 내용은 [Azure Files의 MFS 파일 공유](files-nfs-protocol.md)를 참조하세요.

## <a name="common-scenarios"></a>일반적인 시나리오
SMB 파일 공유는 최종 사용자 파일 공유와 데이터베이스 및 애플리케이션을 백업하는 파일 공유를 비롯한 다양한 애플리케이션에 사용됩니다. SMB 파일 공유는 다음과 같은 시나리오에서 자주 사용됩니다.

- 팀 공유, 홈 디렉터리 등의 최종 사용자 파일 공유
- SQL Server 데이터베이스나 Win32 또는 .NET 로컬 파일 시스템 API용으로 작성된 LOB(기간 업무) 애플리케이션과 같은 Windows 기반 애플리케이션용 스토리지 지원 
- 새 애플리케이션 및 서비스 개발(특히 애플리케이션 또는 서비스에 임의 IO 및 계층 구조 스토리지에 대한 요구 사항이 있는 경우)

## <a name="features"></a>기능
Azure Files는 SMB 파일 공유의 프로덕션 배포에 필요한 SMB 및 Azure의 주요 기능을 지원합니다.

- AD 도메인 가입 및 DACL(임의 액세스 제어 목록)
- Azure Backup과의 통합 서버리스 백업
- Azure 프라이빗 엔드포인트를 사용한 네트워크 격리
- SMB 다중 채널을 사용하여 높은 네트워크 처리량 구현(프리미엄 파일 공유에만 해당)
- AES-256-GCM, AES-128-GCM 및 AES-128-CCM을 포함한 SMB 채널 암호화
- VSS 통합 공유 스냅샷을 통한 이전 버전 지원
- 실수로 인한 삭제를 방지하기 위한 Azure 파일 공유에 대한 자동 일시 삭제
- 인터넷 안전 SMB 3.0 이상을 사용한 선택적인 인터넷 액세스 가능 파일 공유

SMB 파일 공유를 온-프레미스에 직접 탑재할 수도 있고 [Azure 파일 동기화를 사용하여 온-프레미스에 캐시](../file-sync/file-sync-introduction.md)할 수도 있습니다.  

## <a name="security"></a>보안
Azure Files에 저장된 모든 데이터는 Azure SSE(스토리지 서비스 암호화)를 사용하여 저장 데이터로 암호화됩니다. 스토리지 서비스 암호화는 Windows의 BitLocker와 유사하게 작동하며, 데이터는 파일 시스템 수준 아래에서 암호화됩니다. Azure 파일 공유의 파일 시스템 아래에서 데이터가 암호화되어 디스크로 인코딩되므로 Azure 파일 공유를 읽거나 쓰기 위해 클라이언트의 기본 키에 액세스할 필요가 없습니다. 저장 데이터 암호화는 SMB 및 NFS 프로토콜 모두에 적용됩니다.

기본적으로 모든 Azure 스토리지 계정은 전송 중 암호화를 사용하도록 설정되어 있습니다. 즉, SMB를 통해 파일 공유를 탑재(또는 FileREST 프로토콜을 통해 액세스)할 때 Azure Files는 암호화 또는 HTTPS를 사용하는 SMB 3.x를 통해 만든 연결만 허용합니다. SMB 채널 암호화를 사용한 SMB 3.x를 지원하지 않는 클라이언트는 전송 중 암호화를 사용하도록 설정한 경우 Azure 파일 공유를 탑재할 수 없습니다. 

Azure Files Windows Server 2022 또는 Windows 11에서 사용하는 경우 SMB 3.1.1에서 AES-256-GCM을 지원합니다. SMB 3.1.1은 AES-128-GCM을 지원하고 SMB 3.0은 AES-128-CCM을 지원합니다. AES-128-GCM은 성능상의 이유로 Windows 10, 버전 21H1에서 기본적으로 협상됩니다.

Azure 스토리지 계정에 대해 전송 중 암호화를 사용하지 않도록 설정할 수 있습니다. 또한 암호화를 사용하지 않도록 설정하면 Azure Files에서 암호화 없는 SMB 2.1 및 SMB 3.x도 허용합니다. 전송 중 암호화를 사용하지 않도록 설정하는 주된 이유는 Windows Server 2008 R2 또는 이전 버전의 Linux 배포와 같은 이전 운영 체제에서 실행되어야 하는 레거시 애플리케이션을 지원하기 위해서입니다. Azure Files는 Azure 파일 공유와 동일한 Azure 지역 내에서만 SMB 2.1 연결을 허용합니다. Azure 파일 공유의 Azure 지역 외부(예: 온-프레미스 또는 다른 Azure 지역)에 있는 SMB 2.1 클라이언트는 파일 공유에 액세스할 수 없습니다.

## <a name="smb-protocol-settings"></a>SMB 프로토콜 설정
Azure Files는 SMB 프로토콜의 동작, 성능 및 보안에 영향을 주는 여러 설정을 제공합니다. 이는 스토리지 계정 내의 모든 Azure 파일 공유에 대해 구성됩니다.

### <a name="smb-multichannel"></a>SMB 다중 채널
SMB 다중 채널을 사용하면 SMB 3.x 클라이언트가 SMB 파일 공유에 대한 다중 네트워크 연결을 설정할 수 있습니다. Azure Files는 프리미엄 파일 공유(FileStorage 스토리지 계정 종류의 파일 공유)에서 SMB 다중 채널을 지원합니다. Azure Files에서 SMB 다중 채널을 사용하도록 설정하기 위한 추가 비용은 없습니다. SMB 다중 채널은 기본적으로 사용하지 않도록 설정되어 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
SMB 다중 채널의 상태를 보려면 프리미엄 파일 공유가 포함된 스토리지 계정으로 이동하고 스토리지 계정 목차의 **데이터 스토리지** 제목 아래에서 **파일 공유** 를 선택합니다. SMB 다중 채널의 상태는 **파일 공유 설정** 섹션에서 확인할 수 있습니다.

![SMB 다중 채널 설정을 강조 표시하는 스토리지 계정의 파일 공유 섹션 스크린샷](./media/files-smb-protocol/1-smb-multichannel-enable.png)

SMB 다중 채널을 사용하거나 사용하지 않도록 설정하려면 현재 상태(상태에 따라 **사용** 또는 **사용 안 함**)를 선택합니다. 결과 대화 상자는 SMB 다중 채널을 사용하거나 사용하지 않도록 설정하는 토글을 제공합니다. 원하는 상태를 선택하고 **저장** 을 선택합니다.

:::image type="content" source="media/files-smb-protocol/2-smb-multichannel-enable.png" alt-text="SMB 다중 채널 기능을 사용/사용 안 함을 설정하는 대화 상자의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
SMB 다중 채널의 상태를 가져오려면 `Get-AzStorageFileServiceProperty` cmdlet을 사용합니다. 이러한 PowerShell 명령을 실행하기 전에 `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName

# If you've never enabled or disabled SMB Multichannel, the value for the SMB Multichannel 
# property returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 
$defaultSmbMultichannelEnabled = $false

# Get the current value for SMB Multichannel
Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbMultichannelEnabled"; 
            Expression = { 
                if ($null -eq $_.ProtocolSettings.Smb.Multichannel.Enabled) { 
                    $defaultSmbMultichannelEnabled 
                } else { 
                    $_.ProtocolSettings.Smb.Multichannel.Enabled 
                } 
            } 
        }
```

SMB 다중 채널의 사용/사용 안 함을 설정하려면 `Update-AzStorageFileServiceProperty` cmdlet을 사용합니다.

```PowerShell
Update-AzStorageFileServiceProperty `
    -StorageAccount $storageAccount `
    -EnableSmbMultichannel $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
SMB 다중 채널의 상태를 가져오려면 `az storage account file-service-properties show` 명령을 사용합니다. 이러한 Bash 명령을 실행하기 전에 `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# If you've never enabled or disabled SMB Multichannel, the value for the SMB Multichannel 
# property returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 

## Search strings
replaceSmbMultichannel="\"smbMultichannelEnabled\": null"

# Replacement values for null parameters. 
defaultSmbMultichannelEnabled="\"smbMultichannelEnabled\": false"

# Build JMESPath query string
query="{" 
query="${query}smbMultichannelEnabled: protocolSettings.smb.multichannel.enabled"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbMultichannel/$defaultSmbMultichannelEnabled}"

# Print returned settings
echo $protocolSettings
```

SMB 다중 채널의 사용/사용 안 함을 설정하려면 `az storage account file-service-properties update` 명령을 사용합니다.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-smb-multichannel "true"
```
---

### <a name="smb-security-settings"></a>SMB 보안 설정
Azure Files는 조직의 요구 사항에 따라 SMB 프로토콜을 더 호환 가능하거나 더 안전하게 전환할 수 있는 설정을 표시합니다. 기본적으로 Azure Files는 최대한 호환되도록 구성되므로 이러한 설정을 제한하면 일부 클라이언트가 연결하지 못할 수 있습니다.

Azure Files는 다음 설정을 노출합니다.

- **SMB 버전**: 허용되는 SMB 버전입니다. 지원되는 프로토콜 버전은 SMB 3.1.1, SMB 3.0 및 SMB 2.1입니다. 기본적으로 SMB 2.1은 전송 중 암호화를 지원하지 않으므로 "보안 전송 필요"를 사용하도록 설정한 경우 SMB 2.1는 허용되지 않지만 모든 SMB 버전이 허용됩니다.
- **인증 방법**: 허용되는 SMB 인증 방법입니다. 지원되는 인증 방법은 NTLMv2 및 Kerberos입니다. 모든 인증 공급자는 기본적으로 허용됩니다. NTLMv2를 제거하면 스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재할 수 없습니다.
- **Kerberos 티켓 암호화**: 허용되는 암호화 알고리즘입니다. 지원되는 암호화 알고리즘은 RC4-HMAC 및 AES-256입니다.
- **SMB 채널 암호화**: 허용되는 SMB 채널 암호화 알고리즘입니다. 지원되는 암호화 알고리즘은 AES-256-GCM, AES-128-GCM 및 AES-128-CCM입니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
PowerShell 또는 CLI를 사용하여 SMB 보안 설정을 보고 변경할 수 있습니다. 원하는 탭을 선택하여 SMB 보안 설정을 가져오고 설정하는 방법에 대한 단계를 확인하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
SMB 프로토콜 설정을 가져오려면 `Get-AzStorageFileServiceProperty` cmdlet을 사용합니다 이러한 PowerShell 명령을 실행하기 전에 `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName

# If you've never changed any SMB security settings, the values for the SMB security 
# settings returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values. 
$smbProtocolVersions = "SMB2.1", "SMB3.0", "SMB3.1.1"
$smbAuthenticationMethods = "NTLMv2", "Kerberos"
$smbKerberosTicketEncryption = "RC4-HMAC", "AES-256"
$smbChannelEncryption = "AES-128-CCM", "AES-128-GCM", "AES-256-GCM"

# Gets the current values of the SMB security settings
Get-AzStorageFileServiceProperty -StorageAccount $storageAccount | `
    Select-Object -Property `
        ResourceGroupName, `
        StorageAccountName, `
        @{ 
            Name = "SmbProtocolVersions";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.Versions) {
                    [String]::Join(", ", $smbProtocolVersions)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.Versions)
                }
            }
        },
        @{
            Name = "SmbChannelEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.ChannelEncryption) {
                    [String]::Join(", ", $smbChannelEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.ChannelEncryption)
                }
            }
        },
        @{
            Name = "SmbAuthenticationMethods";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.AuthenticationMethods) {
                    [String]::Join(", ", $smbAuthenticationMethods)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.AuthenticationMethods)
                }
            }
        },
        @{
            Name = "SmbKerberosTicketEncryption";
            Expression = {
                if ($null -eq $_.ProtocolSettings.Smb.KerberosTicketEncryption) {
                    [String]::Join(", ", $smbKerberosTicketEncryption)
                } else {
                    [String]::Join(", ", $_.ProtocolSettings.Smb.KerberosTicketEncryption)
                }
            }
        }
```

조직의 보안, 성능 및 호환성 요구 사항에 따라 SMB 프로토콜 설정을 수정할 수 있습니다. 다음 PowerShell 명령은 SMB 파일 공유를 가장 안전한 옵션으로만 제한합니다.

> [!Important]  
> SMB Azure 파일 공유를 가장 안전한 옵션으로만 제한하면 일부 클라이언트에서 요구 사항을 충족하지 못할 경우 연결하지 못할 수 있습니다. 예를 들어 AES-256-GCM은 Windows Server 2022 및 Windows 11부터 SMB 채널 암호화에 대한 옵션으로 도입되었습니다. 즉, AES-256-GCM을 지원하지 않는 이전 클라이언트는 연결할 수 없습니다.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -SmbAuthenticationMethod "Kerberos" `
    -SmbChannelEncryption "AES-256-GCM" `
    -SmbKerberosTicketEncryption "AES-256" `
    -SmbProtocolVersion "SMB3.1.1"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
SMB 보안 설정의 상태를 가져오려면 `az storage account file-service-properties show` 명령을 사용합니다. 이러한 Bash 명령을 실행하기 전에 `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

# If you've never changed any SMB security settings, the values for the SMB security 
# settings returned by Azure Files will be null. Null returned values should be interpreted 
# as "default settings are in effect". To make this more user-friendly, the following 
# PowerShell commands replace null values with the human-readable default values.

# Values to be replaced
replaceSmbProtocolVersion="\"smbProtocolVersions\": null"
replaceSmbChannelEncryption="\"smbChannelEncryption\": null"
replaceSmbAuthenticationMethods="\"smbAuthenticationMethods\": null"
replaceSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": null"

# Replacement values for null parameters. If you copy this into your own 
# scripts, you will need to ensure that you keep these variables up-to-date with any new 
# options we may add to these parameters in the future.
defaultSmbProtocolVersions="\"smbProtocolVersions\": \"SMB2.1;SMB3.0;SMB3.1.1\""
defaultSmbChannelEncryption="\"smbChannelEncryption\": \"AES-128-CCM;AES-128-GCM;AES-256-GCM\""
defaultSmbAuthenticationMethods="\"smbAuthenticationMethods\": \"NTLMv2;Kerberos\""
defaultSmbKerberosTicketEncryption="\"smbKerberosTicketEncryption\": \"RC4-HMAC;AES-256\""

# Build JMESPath query string
query="{"
query="${query}smbProtocolVersions: protocolSettings.smb.versions,"
query="${query}smbChannelEncryption: protocolSettings.smb.channelEncryption,"
query="${query}smbAuthenticationMethods: protocolSettings.smb.authenticationMethods,"
query="${query}smbKerberosTicketEncryption: protocolSettings.smb.kerberosTicketEncryption"
query="${query}}"

# Get protocol settings from the Azure Files FileService object
protocolSettings=$(az storage account file-service-properties show \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "${query}")

# Replace returned values if null with default values 
protocolSettings="${protocolSettings/$replaceSmbProtocolVersion/$defaultSmbProtocolVersion}"
protocolSettings="${protocolSettings/$replaceSmbChannelEncryption/$defaultSmbChannelEncryption}"
protocolSettings="${protocolSettings/$replaceSmbAuthenticationMethods/$defaultSmbAuthenticationMethods}"
protocolSettings="${protocolSettings/$replaceSmbKerberosTicketEncryption/$defaultSmbKerberosTicketEncryption}"

# Print returned settings
echo $protocolSettings
```

조직의 보안, 성능 및 호환성 요구 사항에 따라 SMB 프로토콜 설정을 수정할 수 있습니다. 다음 Azure CLI 명령은 SMB 파일 공유를 가장 안전한 옵션으로만 제한합니다.

> [!Important]  
> SMB Azure 파일 공유를 가장 안전한 옵션으로만 제한하면 일부 클라이언트에서 요구 사항을 충족하지 못할 경우 연결하지 못할 수 있습니다. 예를 들어 AES-256-GCM은 Windows Server 2022 및 Windows 11부터 SMB 채널 암호화에 대한 옵션으로 도입되었습니다. 즉, AES-256-GCM을 지원하지 않는 이전 클라이언트는 연결할 수 없습니다.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --versions "SMB3.1.1" \
    --channel-encryption "AES-256-GCM" \
    --auth-methods "Kerberos" \
    --kerb-ticket-encryption "AES-256"
```
---

## <a name="limitations"></a>제한 사항
Azure Files의 SMB 파일 공유는 SMB 프로토콜 및 NTFS 파일 시스템에서 지원되는 기능의 일부를 지원합니다. 대부분의 사용 사례 및 애플리케이션에서는 이러한 기능이 필요하지 않지만, 지원되지 않는 기능을 사용하는 경우 일부 애플리케이션이 Azure Files에서 제대로 작동하지 않을 수 있습니다. 다음 기능은 지원되지 않습니다.

- [SMB 다이렉트](/windows-server/storage/file-server/smb-direct)  
- SMB 디렉터리 임대
- [SMB 파일 공유용 VSS](/archive/blogs/clausjor/vss-for-smb-file-shares)(VSS 공급자가 스냅샷이 만들어지기 전에 SMB 파일 공유에 데이터를 플러시하는 데 사용할 수 있음)
- 대체 데이터 스트림
- 확장된 특성
- 개체 식별자
- [하드 링크](/windows/win32/fileio/hard-links-and-junctions)
- [소프트 링크](/windows/win32/fileio/creating-symbolic-links)  
- [재분석 지점](/windows/win32/fileio/reparse-points)  
- [스파스 파일](/windows/win32/fileio/sparse-files)
- [짧은 파일 이름(8.3 별칭)](/windows/win32/fileio/naming-a-file#short-vs-long-names)  
- [압축](https://techcommunity.microsoft.com/t5/itops-talk-blog/smb-compression-deflate-your-io/ba-p/1183552)

## <a name="regional-availability"></a>국가별 가용성
SMB Azure 파일 공유는 모든 퍼블릭 및 소버린 지역을 비롯한 모든 Azure 지역에서 사용할 수 있습니다. 프리미엄 SMB 파일 공유는 [지역 하위 집합](https://azure.microsoft.com/global-infrastructure/services/?products=storage)에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)
- 기본 운영 체제에 SMB 파일 공유 탑재:
    - [Windows에 SMB 파일 공유 탑재](storage-how-to-use-files-windows.md)
    - [Linux에 SMB 파일 공유 탑재](storage-how-to-use-files-linux.md)
    - [macOS에 SMB 파일 공유 탑재](storage-how-to-use-files-mac.md)