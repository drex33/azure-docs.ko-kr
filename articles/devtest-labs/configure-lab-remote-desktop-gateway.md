---
title: 원격 데스크톱 게이트웨이 사용 하도록 랩 구성
description: RDP 포트를 노출하지 않고도 랩 VM에 안전하게 액세스할 수 있도록 원격 데스크톱 게이트웨이를 사용하여 Azure DevTest Labs에서 랩을 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: fcafc9f035d57e7685ffc3646cecaf27b60684f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652418"
---
# <a name="configure-your-lab-in-azure-devtest-labs-to-use-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이를 사용하도록 Azure DevTest Labs에서 랩 구성
Azure DevTest Labs에서는 RDP 포트를 노출하지 않고도 랩 VM(가상 머신)에 안전하게 액세스할 수 있도록 랩용 원격 데스크톱 게이트웨이를 구성할 수 있습니다. 랩은 랩 사용자가 액세스할 수 있는 모든 가상 머신을 보고 연결할 수 있는 중앙 위치를 제공합니다. **가상 머신** 페이지의 **연결** 단추를 사용하면 컴퓨터별 RDP 파일이 만들어지며 이 파일을 열어 컴퓨터에 연결할 수 있습니다. 랩을 원격 데스크톱 게이트웨이에 연결하여 RDP 연결을 추가로 사용자 지정하고 보호할 수 있습니다. 

이 접근 방식은 랩 사용자가 게이트웨이 컴퓨터에 직접 인증하거나 도메인 조인 게이트웨이 컴퓨터에서 회사 자격 증명을 사용하여 컴퓨터에 연결할 수 있기 때문에 더 안전합니다. 랩은 또한 사용자가 인터넷에 RDP 포트를 노출하지 않고도 랩 가상 머신에 연결할 수 있도록 게이트웨이 컴퓨터에 대한 토큰 인증 사용을 지원합니다. 이 문서에서는 토큰 인증을 사용하여 랩 컴퓨터에 연결하는 랩을 설정하는 방법에 대한 예를 보여 줍니다.

## <a name="architecture-of-the-solution"></a>솔루션 아키텍처

![솔루션 아키텍처](./media/configure-lab-remote-desktop-gateway/architecture.png)

1. **연결** 단추를 선택하면 [RDP 파일 콘텐츠 가져오기](/rest/api/dtl/virtualmachines/getrdpfilecontents) 작업이 호출됩니다.1. 
1. RDP 파일 컨텐츠 가져오기 작업은 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`를 호출하여 인증 토큰을 요청합니다.
    1. `{gateway-hostname}`은 Azure Portal의 랩에 대한 **랩 설정** 페이지에 지정된 게이트웨이 호스트 이름입니다. 
    1. `{lab-machine-name}`은 연결하려는 컴퓨터의 이름입니다.
    1. `{port-number}`은 연결해야 하는 포트입니다. 일반적으로 이 포트는 3389입니다. 랩 VM이 DevTest Labs의 [공유 IP](devtest-lab-shared-ip.md) 기능을 사용하는 경우에는 이 포트가 다릅니다.
1. 원격 데스크톱 게이트웨이는 인증 토큰을 생성하기 위해 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`에서 Azure 함수로의 호출을 연기합니다. DevTest Labs 서비스는 요청 헤더에 기능 키를 자동으로 포함합니다. 이 기능 키는 랩의 키 자격 증명 모음에 저장됩니다. 실습의 **랩 설정** 페이지에서 **게이트웨이 토큰 비밀** 로 표시되는 비밀의 이름입니다.
1. Azure 함수는 게이트웨이 컴퓨터에 대한 인증서 기반 토큰 인증용 토큰을 반환할 것으로 예상됩니다.  
1. 그런 다음 Get RDP 파일 내용 작업은 인증 정보를 포함하여 전체 RDP 파일을 반환합니다.
1. 선호하는 RDP 연결 프로그램을 사용하여 RDP 파일을 엽니다. 모든 RDP 연결 프로그램이 토큰 인증을 지원하는 것은 아닙니다. 인증 토큰에는 함수 앱에서 설정한 만료 날짜가 있습니다. 토큰이 만료되기 전에 랩 VM에 연결합니다.
1. 원격 데스크톱 게이트웨이 컴퓨터가 RDP 파일의 토큰을 인증하면 연결이 랩 컴퓨터로 전달됩니다.

### <a name="solution-requirements"></a>솔루션 요구 사항
DevTest Labs 토큰 인증 기능을 사용하려면 게이트웨이 컴퓨터, DNS(도메인 이름 서비스) 및 기능에 대한 몇 가지 구성 요구 사항이 있습니다.

### <a name="requirements-for-remote-desktop-gateway-machines"></a>원격 데스크톱 게이트웨이 컴퓨터에 대한 요구 사항
- HTTPS 트래픽을 처리하려면 TLS/SSL 인증서를 게이트웨이 시스템에 설치해야 합니다. 인증서는 게이트웨이 팜에 대한 부하 분산 장치의 FQDN(정규화된 도메인 이름) 또는 컴퓨터가 하나뿐인 경우 컴퓨터 자체의 FQDN과 일치해야 합니다. 와일드카드 TLS/SSL 인증서가 작동하지 않습니다.  
- 게이트웨이 컴퓨터에 설치된 서명 인증서. [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1) 스크립트를 사용하여 서명 인증서를 만듭니다.
- 원격 데스크톱 게이트웨이에 대한 토큰 인증을 지원하는 [플러그형 인증](https://code.msdn.microsoft.com/windowsdesktop/Remote-Desktop-Gateway-517d6273) 모듈을 설치합니다. 이러한 모듈의 예로 [System Center VMM(Virtual Machine Manager) 이미지](/system-center/vmm/install-console?view=sc-vmm-1807&preserve-view=true)와 함께 제공되는 `RDGatewayFedAuth.msi`를 들 수 있습니다. 시스템 센터에 대한 자세한 내용은 [시스템 센터 문서](/system-center/) 및 [가격 세부 정보](https://www.microsoft.com/cloud-platform/system-center-pricing)를 참조하세요.  
- 게이트웨이 서버는 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}`에 대한 요청을 처리할 수 ​​있습니다.

    게이트웨이-호스트 이름은 게이트웨이 팜 부하 분산 장치의 FQDN이거나 컴퓨터가 하나뿐인 경우 컴퓨터 자체의 FQDN입니다. `{lab-machine-name}`은 연결하려는 랩 컴퓨터의 이름이고 `{port-number}`는 연결이 설정되는 포트입니다.  기본적으로 이 포트는 3389입니다.  그러나 가상 머신이 DevTest Labs의 [공유 IP](devtest-lab-shared-ip.md) 기능을 사용하는 경우 포트가 달라집니다.
- IIS(인터넷 정보 서버)용 [애플리케이션 라우팅 요청](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 모듈을 사용하여 `https://{gateway-hostname}/api/host/{lab-machine-name}/port/{port-number}` 요청을 azure 함수로 리디렉션할 수 있습니다. 이 함수는 인증용 토큰을 얻기 위한 요청을 처리합니다.


## <a name="requirements-for-azure-function"></a>Azure 함수에 대한 요구 사항
Azure 함수는 `https://{function-app-uri}/app/host/{lab-machine-name}/port/{port-number}` 형식으로 요청을 처리하고 게이트웨이 컴퓨터에 설치된 동일한 서명 인증서를 기반으로 인증 토큰을 반환합니다. `{function-app-uri}`는 함수에 액세스하는 데 사용되는 URI입니다. 함수 키는 요청 헤더에 자동으로 전달됩니다. 샘플 함수는 [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/src/RDGatewayAPI/Functions/CreateToken.cs)를 참조하세요. 


## <a name="requirements-for-network"></a>네트워크 요구 사항

- 게이트웨이 컴퓨터에 설치된 TLS/SSL 인증서와 연결된 FQDN의 DNS는 트래픽을 게이트웨이 컴퓨터 또는 게이트웨이 컴퓨터 팜의 부하 분산 장치로 전달해야 합니다.
- 랩 컴퓨터가 개인 IP를 사용하는 경우 동일한 가상 네트워크를 공유하거나 피어링된 가상 네트워크를 사용하여 게이트웨이 컴퓨터에서 랩 컴퓨터로의 네트워크 경로가 있어야 합니다.

## <a name="configure-the-lab-to-use-token-authentication"></a>토큰 인증을 사용하도록 랩 구성 
이 섹션에서는 토큰 인증을 지원하는 원격 데스크톱 게이트웨이 컴퓨터를 사용하도록 랩을 구성하는 방법을 보여 줍니다. 이 섹션에서는 원격 데스크톱 게이트웨이 팜 자체를 설정하는 방법을 다루지 않습니다. 자세한 내용은 이 문서 끝 부분에 있는 [원격 데스크톱 게이트웨이를 만드는 샘플](#sample-to-create-a-remote-desktop-gateway) 섹션을 참조하세요. 

랩 설정을 업데이트하기 전에 실습의 키 자격 증명 모음에 인증 토큰을 반환하는 함수를 성공적으로 실행하는 데 필요한 키를 저장합니다. Azure Portal의 함수에 대한 **관리** 페이지에서 함수 키 값을 가져올 수 있습니다. 키 자격 증명 모음에 비밀을 저장하는 방법에 대한 자세한 내용은 [키 자격 증명 모음에 비밀 추가](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault)를 참조하세요. 나중에 사용할 수 있도록 비밀 이름을 저장합니다.

랩의 주요 자격 증명 모음 ID를 찾으려면 다음 Azure CLI 명령을 실행합니다. 

```azurecli
az resource show --name {lab-name} --resource-type 'Microsoft.DevTestLab/labs' --resource-group {lab-resource-group-name} --query properties.vaultName
```

다음 단계를 사용하여 토큰 인증을 사용하도록 랩을 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** 를 선택한 다음, 목록에서 **DevTest Labs** 를 선택합니다.
1. 랩 목록에서 **랩** 을 선택합니다.
1. 랩 페이지에서 **구성 및 정책** 을 선택합니다.
1. 왼쪽 메뉴의 **설정** 섹션에서 **랩 설정** 을 선택합니다.
1. **원격 데스크톱** 섹션에서 **게이트웨이 호스트 이름** 필드에 원격 데스크톱 서비스 게이트웨이 시스템 또는 팜의 FQDN(정규화된 도메인 이름) 또는 IP 주소를 입력합니다. 이 값은 게이트웨이 컴퓨터에서 사용되는 TLS/SSL 인증서의 FQDN과 일치해야 합니다.

    ![랩 설정의 원격 데스크톱 옵션](./media/configure-lab-remote-desktop-gateway/remote-desktop-options-in-lab-settings.png)
1. **원격 데스크톱** 섹션에서 **게이트웨이 토큰** 비밀에 앞서 생성한 비밀의 이름을 입력합니다. 이 값은 함수 키 자체가 아니라 함수 키를 보유하고 있는 랩 키 자격 증명 모음의 비밀 이름입니다.

    ![랩 설정의 게이트웨이 토큰 비밀](./media/configure-lab-remote-desktop-gateway/gateway-token-secret.png)
1. 변경 내용을 **저장** 합니다.

    > [!NOTE] 
    > **저장** 을 클릭하면 [원격 데스크톱 게이트웨이 사용 조건](https://www.microsoft.com/licensing/product-licensing/products)에 동의하는 것입니다. 원격 게이트웨이에 대한 자세한 내용은 [원격 데스크톱 서비스 시작](/windows-server/remote/remote-desktop-services/Welcome-to-rds) 및 [원격 데스크톱 환경 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)를 참조하세요.


자동화를 통한 랩 구성이 선호되는 경우 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 비밀** 을 설정하는 샘플 PowerShell 스크립트는 [Set-DevTestLabGateway.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Set-DevTestLabGateway.ps1)을 참조하세요. [Azure DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)는 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 비밀** 설정으로 랩을 만들거나 업데이트하는 Azure Resource Manager 템플릿도 제공합니다.

## <a name="configure-network-security-group"></a>네트워크 보안 그룹 구성
랩의 보안을 강화하기 위해 랩 가상 머신에서 사용하는 가상 네트워크에 NSG(네트워크 보안 그룹)를 추가할 수 있습니다. NSG를 설정하는 방법에 대한 지침은 [네트워크 보안 그룹 만들기, 변경 또는 삭제](../virtual-network/manage-network-security-group.md)를 참조하세요.

다음은 처음 게이트웨이를 통과하는 트래픽만 랩 컴퓨터에 도달하도록 허용하는 NSG의 예입니다. 이 규칙의 소스는 단일 게이트웨이 컴퓨터의 IP 주소 또는 게이트웨이 컴퓨터 앞에 있는 부하 분산 장치의 IP 주소입니다.

![네트워크 보안 그룹 - 규칙](./media/configure-lab-remote-desktop-gateway/network-security-group-rules.png)

## <a name="sample-to-create-a-remote-desktop-gateway"></a>원격 데스크톱 게이트웨이를 만드는 샘플

> [!NOTE] 
> 샘플 템플릿을 사용하여 [원격 데스크톱 게이트웨이의 사용 조건](https://www.microsoft.com/licensing/product-licensing/products)에 동의합니다. 원격 게이트웨이에 대한 자세한 내용은 [원격 데스크톱 서비스 시작](/windows-server/remote/remote-desktop-services/Welcome-to-rds) 및 [원격 데스크톱 환경 배포](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)를 참조하세요.

[Azure DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)는 DevTest Labs에서 토큰 인증 및 원격 데스크톱 게이트웨이를 사용하는 데 필요한 리소스를 설정할 수 있는 몇 가지 샘플을 제공합니다. 이러한 샘플에는 게이트웨이 컴퓨터, 랩 설정 및 함수 앱용 Azure Resource Manager 템플릿이 포함됩니다.

다음 단계에 따라 원격 데스크톱 게이트웨이 팜에 대한 샘플 솔루션을 설정합니다.

1. 서명 인증서를 만듭니다.  [Create-SigningCertificate.ps1](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/tools/Create-SigningCertificate.ps1)을 실행합니다. 생성된 인증서의 지문, 암호 및 Base64 인코딩을 저장합니다.
2. TLS/SSL 인증서를 받습니다. TLS/SSL 인증서와 연결된 FQDN은 사용자가 제어하는 ​​도메인에 대한 것이어야 합니다. 이 인증서에 대한 지문, 암호 및 Base64 인코딩을 저장합니다. PowerShell을 사용하여 지문을 얻으려면 다음 명령을 사용합니다.

    ```powershell
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate;
    $cer.Import('path-to-certificate');
    $hash = $cer.GetCertHashString()
    ```

    PowerShell을 사용하여 Base64 인코딩을 가져오려면 다음 명령을 사용합니다.

    ```powershell
    [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes('path-to-certificate'))
    ```
3. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)에서 파일 다운로드.

    템플릿은 동일한 기본 URI에 있는 몇 가지 다른 Resource Manager 템플릿 및 관련 리소스에 대한 액세스 권한이 필요합니다. [https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/GatewaySample/arm/gateway) 및 RDGatewayFedAuth.msi의 모든 파일을 스토리지 계정의 Blob 컨테이너로 복사합니다.  
4. [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/GatewaySample/arm/gateway)에서 **azuredeploy.json** 을 배포합니다. 템플릿은 다음 매개 변수를 사용합니다.
    - adminUsername – 필수입니다.  게이트웨이 컴퓨터의 관리자 사용자 이름입니다.
    - adminPassword – 필수입니다. 게이트웨이 컴퓨터에 대한 관리자 계정의 암호입니다.
    - instanceCount – 만들 게이트웨이 컴퓨터 수입니다.  
    - alwaysOn – 만든 Azure Functions 앱을 웜 상태로 유지할지 여부를 나타냅니다. Azure Functions 앱을 유지하면 사용자가 처음 랩 VM에 연결하려고 할 때 지연을 방지할 수 있지만 비용에 영향을 줍니다.  
    - tokenLifetime – 만든 토큰의 유효 기간입니다. 형식은 HH:MM:SS입니다.
    - sslCertificate – 게이트웨이 시스템에 대한 TLS/SSL 인증서의 Base64 인코딩입니다.
    - sslCertificatePassword – 게이트웨이 시스템에 대한 TLS/SSL 인증서의 암호입니다.
    - sslCertificateThumbprint - TLS/SSL 인증서의 로컬 인증서 저장소에서 식별하기 위한 인증서 지문입니다.
    - signCertificate – 게이트웨이 시스템의 인증서 서명을 위한 Base64 인코딩입니다.
    - signCertificatePassword – 게이트웨이 컴퓨터의 인증서 서명을 위한 암호입니다.
    - signCertificateThumbprint - 서명 인증서의 로컬 인증서 저장소에서 식별하기 위한 인증서 지문입니다.
    - _artifactsLocation – 모든 지원 리소스를 찾을 수 있는 URI 위치입니다. 이 값은 상대 경로가 아니라 정규화된 URI여야 합니다.
    - _artifactsLocationSasToken – 위치가 Azure 스토리지 계정인 경우 지원 리소스에 액세스하는 데 사용되는 SAS(공유 액세스 서명) 토큰입니다.

    Azure CLI에서 다음 명령을 사용하여 템플릿을 배포할 수 있습니다.

    ```azurecli
    az deployment group create --resource-group {resource-group} --template-file azuredeploy.json --parameters @azuredeploy.parameters.json -–parameters _artifactsLocation="{storage-account-endpoint}/{container-name}" -–parameters _artifactsLocationSasToken = "?{sas-token}"
    ```

    다음은 매개 변수에 대한 설명입니다.

    - {storage-account-endpoint}는 `az storage account show --name {storage-acct-name} --query primaryEndpoints.blob`을 실행하여 얻을 수 있습니다.  {storage-acct-name}는 업로드한 파일을 보유하는 스토리지 계정의 이름입니다.  
    - {container-name}은 업로드한 파일을 보유하는 {storage-acct-name}의 컨테이너 이름입니다.  
    - {sas-token}은 `az storage container generate-sas --name {container-name} --account-name {storage-acct-name} --https-only –permissions drlw –expiry {utc-expiration-date}`를 실행하여 얻을 수 있습니다. 
        - {storage-acct-name}는 업로드한 파일을 보유하는 스토리지 계정의 이름입니다.  
        - {container-name}은 업로드한 파일을 보유하는 {storage-acct-name}의 컨테이너 이름입니다.  
        - {utc-expiration-date}는 SAS 토큰이 만료되고 더 이상 스토리지 계정에 액세스하는 데 SAS 토큰을 사용할 수 없는 UTC 날짜입니다.

    템플릿 배포 출력에서 gatewayFQDN 및 gatewayIP의 값을 기록합니다. 또한 [함수 앱 설정](../azure-functions/functions-how-to-use-azure-function-app-settings.md) 탭에서 찾을 수 있는 새로 생성된 함수에 대한 함수 키의 값을 저장해야 합니다.
5. TLS/SSL 인증서의 FQDN이 이전 단계에서 gatewayIP의 IP 주소를 가리키도록 DNS를 구성합니다.

    원격 데스크톱 게이트웨이 팜이 만들어지고 적절한 DNS 업데이트가 이루어지면 DevTest Labs의 랩에서 사용할 준비가 된 것입니다. 배포한 게이트웨이 컴퓨터를 사용하려면 **게이트웨이 호스트 이름** 및 **게이트웨이 토큰 비밀** 설정을 구성해야 합니다. 

    > [!NOTE]
    > 랩 컴퓨터가 개인 IP를 사용하는 경우 동일한 가상 네트워크를 공유하거나 피어링된 가상 네트워크를 사용하여 게이트웨이 컴퓨터에서 랩 컴퓨터로의 네트워크 경로가 있어야 합니다.

    게이트웨이와 랩이 모두 구성되면 랩 사용자가 **연결** 을 클릭할 때 생성되는 연결 파일에는 토큰 인증을 사용하여 연결하는 데 필요한 정보가 자동으로 포함됩니다.     

## <a name="next-steps"></a>다음 단계
원격 데스크톱 서비스에 대한 자세한 내용은 다음 문서를 참조하세요. [원격 데스크톱 서비스 설명서](/windows-server/remote/remote-desktop-services/Welcome-to-rds)