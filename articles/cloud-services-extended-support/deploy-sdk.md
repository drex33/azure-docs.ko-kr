---
title: Cloud Services(추가 지원) 배포 - SDK
description: Azure SDK를 사용하여 Cloud Services(추가 지원) 배포
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: b862f10ff5a2c45a04efb51bb8bd84e1e0a0a421
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132864227"
---
# <a name="deploy-cloud-services-extended-support-by-using-the-azure-sdk"></a>Azure SDK를 사용하여 Cloud Services(추가 지원) 배포

이 문서에서는 [Azure SDK](https://azure.microsoft.com/downloads/)를 사용하여 여러 역할(웹 역할 및 작업자 역할) 인스턴스 및 원격 데스크톱 확장을 포함하는 Cloud Services(추가 지원)를 배포하는 방법을 보여줍니다. Cloud Services(추가 지원)는 Azure Resource Manager를 기반으로 하는 Azure Cloud Services의 배포 모델입니다.

## <a name="before-you-begin"></a>시작하기 전에

Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다.

## <a name="deploy-cloud-services-extended-support"></a>Cloud Services(추가 지원) 배포
1. [Azure Compute SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Compute/43.0.0-preview)를 설치하고 표준 인증 메커니즘을 사용하여 클라이언트를 초기화합니다.

    ```csharp
        public class CustomLoginCredentials : ServiceClientCredentials
        {
            private string AuthenticationToken { get; set; }
            public override void InitializeServiceClient<T>(ServiceClient<T> client)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantID}");
                var credential = new ClientCredential(clientId: "{clientID}", clientSecret: "{clientSecret}");
                var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);
                if (result == null) throw new InvalidOperationException("Failed to obtain the JWT token");
                AuthenticationToken = result.Result.AccessToken;
            }
            public override async Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                if (request == null) throw new ArgumentNullException("request");
                if (AuthenticationToken == null) throw new InvalidOperationException("Token Provider Cannot Be Null");
                request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", AuthenticationToken);
                request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                //request.Version = new Version(apiVersion);
                await base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }
    
        var creds = new CustomLoginCredentials();
        m_subId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
        ResourceManagementClient m_ResourcesClient = new ResourceManagementClient(creds);
        NetworkManagementClient m_NrpClient = new NetworkManagementClient(creds);
        ComputeManagementClient m_CrpClient = new ComputeManagementClient(creds);
        StorageManagementClient m_SrpClient = new StorageManagementClient(creds);
        m_ResourcesClient.SubscriptionId = m_subId;
        m_NrpClient.SubscriptionId = m_subId;
        m_CrpClient.SubscriptionId = m_subId;
        m_SrpClient.SubscriptionId = m_subId;
    ```

2. Azure Resource Manager NuGet 패키지를 설치하여 새 리소스 그룹을 만듭니다.

    ```csharp 
    var resourceGroups = m_ResourcesClient.ResourceGroups;
    var m_location = “East US”;
    var resourceGroupName = "ContosoRG";//provide existing resource group name, if created already
    var resourceGroup = new ResourceGroup(m_location); 
    resourceGroup = await resourceGroups.CreateOrUpdateAsync(resourceGroupName, resourceGroup);
    ```

3. 서비스 패키지(.cspkg) 및 서비스 구성(.cscfg) 파일을 저장할 스토리지 계정 및 컨테이너를 만듭니다. [Azure Storage NuGet 패키지](https://www.nuget.org/packages/Azure.Storage.Common/)를 설치합니다. 기존 스토리지 계정을 사용하는 경우 이 단계는 선택 사항입니다. 스토리지 계정 이름은 고유해야 합니다.

    ```csharp
    string storageAccountName = “ContosoSAS”
    var stoInput = new StorageAccountCreateParameters
       {
            Location = m_location,
            Kind = Microsoft.Azure.Management.Storage.Models.Kind.StorageV2,
            Sku = new Microsoft.Azure.Management.Storage.Models.Sku(SkuName.StandardRAGRS),
        };
            StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.Create(rgName,
            storageAccountName, stoInput);
            bool created = false;
            while (!created)
               {
                    Thread.Sleep(600);
                    var stos = m_SrpClient.StorageAccounts.ListByResourceGroup(rgName);
                    created =
                    stos.Any(
                        t =>
                        StringComparer.OrdinalIgnoreCase.Equals(t.Name, storageAccountName));
                }
        
    StorageAccount storageAccountOutput = m_SrpClient.StorageAccounts.GetProperties(rgName, storageAccountName);.
    var accountKeyResult = m_SrpClient.StorageAccounts.ListKeysWithHttpMessagesAsync(rgName, storageAccountName).Result;
    CloudStorageAccount storageAccount = new CloudStorageAccount(new StorageCredentials(storageAccountName, accountKeyResult.Body.Keys.FirstOrDefault(). Value), useHttps: true);
        
    var blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExistsAsync().Wait();
    sharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddDays(-1);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddDays(2);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
    ```

4. 서비스 패키지(.cspkg) 파일을 스토리지 계정에 업로드합니다. 패키지 URL은 모든 스토리지 계정의 SAS(공유 액세스 서명) URI가 될 수 있습니다.

    ```csharp
    CloudBlockBlob cspkgblockBlob = container.GetBlockBlobReference(“ContosoApp.cspkg”);
    cspkgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cspkg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string cspkgsasContainerToken = cspkgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cspkgSASUrl = cspkgblockBlob.Uri + cspkgsasContainerToken;
    ```

5. 서비스 구성(cscfg) 파일을 스토리지 계정에 업로드합니다. 서비스 구성을 문자열 XML 또는 URL 형식 중 하나로 지정합니다.

    ```csharp
    CloudBlockBlob cscfgblockBlob = container.GetBlockBlobReference(“ContosoApp.cscfg”);
    cscfgblockBlob.UploadFromFileAsync(“./ContosoApp/ContosoApp.cscfg”). Wait();
    
    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasCscfgContainerToken = cscfgblockBlob.GetSharedAccessSignature(sasConstraints);
    
    //Return the URI string for the container, including the SAS token.
    string cscfgSASUrl = cscfgblockBlob.Uri + sasCscfgContainerToken;
    ```

6. 가상 네트워크와 서브넷을 만듭니다. [Azure Network NuGet 패키지](https://www.nuget.org/packages/Azure.ResourceManager.Network/)를 설치합니다. 기존 네트워크 및 서브넷을 사용하는 경우 이 단계는 선택 사항입니다.

    ```csharp
    VirtualNetwork vnet = new VirtualNetwork(name: vnetName) 
       { 
            AddressSpace = new AddressSpace 
               { 
                    AddressPrefixes = new List<string> { "10.0.0.0/16" } 
               }, 
                    Subnets = new List<Subnet> 
                    { 
                        new Subnet(name: subnetName) 
                        { 
                            AddressPrefix = "10.0.0.0/24" 
                        } 
                    }, 
                    Location = m_location 
               }; 
    m_NrpClient.VirtualNetworks.CreateOrUpdate(resourceGroupName, “ContosoVNet”, vnet);
    ```

7. 공용 IP 주소를 만들고 공용 IP 주소의 DNS 레이블 속성을 설정합니다. Cloud Services(확장 지원)는 [기본](../virtual-network/ip-services/public-ip-addresses.md#sku) SKU 공용 IP 주소만 지원합니다. 표준 SKU 공용 IP는 Cloud Services에서 작동하지 않습니다.
고정 IP를 사용하는 경우 서비스 구성(.cscfg) 파일에서 예약된 IP로 참조해야 합니다.

    ```csharp
    PublicIPAddress publicIPAddressParams = new PublicIPAddress(name: “ContosIp”) 
       { 
            Location = m_location, 
            PublicIPAllocationMethod = IPAllocationMethod.Dynamic, 
            DnsSettings = new PublicIPAddressDnsSettings() 
               { 
                    DomainNameLabel = “contosoappdns” 
               } 
       }; 
    PublicIPAddress publicIpAddress = m_NrpClient.PublicIPAddresses.CreateOrUpdate(resourceGroupName, publicIPAddressName, publicIPAddressParams);
    ```

8. 네트워크 프로필 개체를 만들고, 공용 IP 주소를 부하 분산 장치의 프런트 엔드에 연결합니다. Azure 플랫폼은 클라우드 서비스 리소스와 동일한 구독에서 '클래식' SKU 부하 분산 장치 리소스를 자동으로 만듭니다. 부하 분산 장치 리소스는 ARM의 읽기 전용 리소스입니다. 리소스에 대한 모든 업데이트는 클라우드 서비스 배포 파일(.cscfg & .csdef)을 통해서만 지원됩니다.

    ```csharp
    LoadBalancerFrontendIPConfiguration feipConfiguration = new LoadBalancerFrontendIPConfiguration() 
        { 
            Name = “ContosoFe”, 
            Properties = new LoadBalancerFrontendIPConfigurationProperties() 
                { 
                PublicIPAddress = new CM.SubResource() 
                    { 
                        Id = $"/subscriptions/{m_subId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{publicIPAddressName}", 
                    } 
                } 
        }; 
    
    CloudServiceNetworkProfile cloudServiceNetworkProfile = new CloudServiceNetworkProfile() 
        { 
            LoadBalancerConfigurations = new List<LoadBalancerConfiguration>() 
                { 
                    new LoadBalancerConfiguration() 
                       { 
                        Name  = 'ContosoLB', 
                        Properties = new LoadBalancerConfigurationProperties() 
                            { 
                            FrontendIPConfigurations = new List<LoadBalancerFrontendIPConfiguration>() 
                                { 
                                feipConfig 
                                } 
                            } 
                        } 
                } 
        }; 
    
    ```

9. 키 자격 증명 모음을 만듭니다. 이 키 자격 증명 모음은 Cloud Service(추가 지원) 역할에 연결된 인증서를 저장하는 데 사용됩니다. 키 자격 증명 모음은 Cloud Services(추가 지원) 인스턴스와 동일한 지역 및 구독에 있어야 하며 고유한 이름을 사용해야 합니다. 자세한 내용은 [Azure Cloud Services(추가 지원)에서 인증서 사용](certificates-and-key-vault.md)을 참조하세요.

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosoOrg” -Location “East US”
    ```

10. 키 자격 증명 모음의 액세스 정책을 업데이트하고, 인증서 권한을 사용자 계정에 부여합니다.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosoOrg'      -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete
    ```

    또는 `Get-AzADUser`를 실행하여 가져올 수 있는 개체 ID를 통해 액세스 정책을 설정합니다.

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -     ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates          create,get,list,delete
    ```

11. 이 예제에서는 키 자격 증명 모음에 자체 서명된 인증서를 추가합니다. Cloud Service(추가 지원) 역할에 배포하려면 인증서 지문을 서비스 구성(.cscfg) 파일에 추가해야 합니다.

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -       SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -      CertificatePolicy $Policy
    ```

12. OS 프로필 개체를 만듭니다. OS 프로필은 Cloud Services(추가 지원) 역할과 연결된 인증서를 지정합니다. 여기서는 이전 단계에서 만든 것과 동일한 인증서입니다.

    ```csharp
    CloudServiceOsProfile cloudServiceOsProfile = 
        new CloudServiceOsProfile 
           { 
                Secrets = new List<CloudServiceVaultSecretGroup> 
                   { 
                        New CloudServiceVaultSecretGroup { 
                        SourceVault = <sourceVault>, 
                        VaultCertificates = <vaultCertificates> 
                        } 
                   } 
           };
    ```

13. 역할 프로필 개체를 만듭니다. 역할 프로필은 이름, 용량 및 계층과 같은 SKU의 역할별 속성을 정의합니다. 

    이 예제에서는 두 개의 역할, ContosoFrontend와 ContosoBackend를 정의합니다. 역할 프로필 정보는 서비스 구성(.cscfg) 파일 및 서비스 정의(.csdef) 파일에 정의된 역할 구성과 일치해야 합니다.

    ```csharp
    CloudServiceRoleProfile cloudServiceRoleProfile = new CloudServiceRoleProfile()
       {
            Roles = new List<CloudServiceRoleProfileProperties>();
            
                // foreach role in cloudService
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoFrontend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                roles.Add(new CloudServiceRoleProfileProperties()
                    {
                        Name = 'ContosoBackend',
                        Sku = new CloudServiceRoleSku
                        {
                            Name = 'Standard_D1_v2',
                            Capacity = 2,
                            Tier = 'Standard'
                        }
                    );
    
                    }
                    }
    ```

14. (선택 사항) Cloud Services(추가 지원) 인스턴스에 추가하려는 확장 프로필 개체를 만듭니다. 이 예제에서는 RDP 확장을 추가합니다.

    ```csharp
    string rdpExtensionPublicConfig = "<PublicConfig>" +
        "<UserName>adminRdpTest</UserName>" +
        "<Expiration>2021-10-27T23:59:59</Expiration>" +
        "</PublicConfig>";
        string rdpExtensionPrivateConfig = "<PrivateConfig>" +
        "<Password>VsmrdpTest!</Password>" +
        "</PrivateConfig>";
    
        Extension rdpExtension = new Extension
                {
                    Name = name,
                    Properties = new CloudServiceExtensionProperties
                    {
                        Publisher = "Microsoft.Windows.Azure.Extensions",
                        Type = "RDP",
                        TypeHandlerVersion = "1.2.1",,
                        AutoUpgradeMinorVersion = true,
                        Settings = rdpExtensionPublicConfig,
                        ProtectedSettings = rdpExtensionPrivateConfig,
                        RolesAppliedTo = [“*”],
                    }
                };
                        
    CloudServiceExtensionProfile cloudServiceExtensionProfile = new CloudServiceExtensionProfile
        {
            Extensions = rdpExtension
        };
    ```

15. Cloud Services(추가 지원) 인스턴스의 배포를 만듭니다.

    ```csharp
    CloudService cloudService = new CloudService
        {
            Properties = new CloudServiceProperties
                {
                    RoleProfile = cloudServiceRoleProfile
                    Configuration = < Add Cscfg xml content here>,
                    // ConfigurationUrl = <Add your configuration URL here>,
                    PackageUrl = <Add cspkg SAS url here>,
                    ExtensionProfile = cloudServiceExtensionProfile,
                    OsProfile= cloudServiceOsProfile,
                    NetworkProfile = cloudServiceNetworkProfile,
                    UpgradeMode = 'Auto'
                },
                    Location = m_location
                };
    
    CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
    ```

## <a name="next-steps"></a>다음 단계
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- [Cloud Services(추가 지원)용 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support)를 방문하세요.
