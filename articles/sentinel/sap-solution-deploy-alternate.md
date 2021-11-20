---
title: Microsoft 센티널 SAP data connector 전문가 구성 옵션, 온-프레미스 배포 및 SAPControl 로그 원본 | Microsoft Docs
description: 전문 구성 옵션 및 온-프레미스 컴퓨터를 사용 하 여 SAP 환경용 Microsoft 센티널 data connector를 배포 하는 방법에 대해 알아봅니다. SAPControl 로그 원본에 관해서도 자세히 알아봅니다.
author: batamig
ms.author: bagol
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 4e65ba4142aa87fe37fb90296a71edad974b4ad5
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871721"
---
# <a name="expert-configuration-options-on-premises-deployment-and-sapcontrol-log-sources"></a>전문가 구성 옵션, 온-프레미스 배포, SAPControl 로그 원본

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 온-프레미스 컴퓨터와 Azure Key Vault를 사용 하 여 자격 증명을 저장 하는 등의 전문가 또는 사용자 지정 프로세스에서 Microsoft 센티널 SAP data connector를 배포 하는 방법을 설명 합니다.

> [!NOTE]
> Microsoft 센티널 SAP 데이터 커넥터를 배포 하기 위한 기본 및 가장 권장 되는 프로세스는 [AZURE VM을 사용 하](sap-deploy-solution.md)는 것입니다. 이 문서는 고급 사용자를 위한 것입니다.

> [!IMPORTANT]
> Microsoft 센티널 SAP 솔루션은 현재 미리 보기로 제공 됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="prerequisites"></a>사전 요구 사항

Microsoft 센티널 SAP 데이터 커넥터를 배포 하기 위한 기본 필수 조건은 배포 방법에 관계 없이 동일 합니다.

시작하기 전에 시스템이 주 [SAP 데이터 커넥터 배포 프로시저](sap-deploy-solution.md#prerequisites)에 설명된 사전 요구 사항을 충족하는지 확인합니다.

자세한 내용은 [Microsoft 센티널 sap solution 자세한 sap 요구 사항 (공개 미리 보기)](sap-solution-detailed-requirements.md)을 참조 하세요.

## <a name="create-your-azure-key-vault"></a>Azure Key Vault 만들기

Microsoft 센티널 SAP 데이터 커넥터용 전용으로 사용할 수 있는 Azure key vault를 만듭니다.

다음 명령을 실행하여 Azure Key Vault를 만들고 Azure 서비스 주체에 대한 액세스 권한을 부여합니다.

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file
az ad sp create-for-rbac –name $spname --role Contributor

SpID=$(az ad sp list –display-name $spname –query “[].appId” --output tsv

#Create key vault
az keyvault create \
  --name $kvname \
  --resource-group $kvgp
  
# Add access to SP
az keyvault set-policy --name $kvname --resource-group $kvgp --object-id $spID --secret-permissions get list set
```

자세한 내용은 [빠른 시작: Azure CLI을 사용하여 Key Vault 만들기](../key-vault/general/quick-create-cli.md)를 참조하세요.

## <a name="add-azure-key-vault-secrets"></a>Azure Key Vault 비밀 추가

Azure Key Vault 비밀을 추가하려면 사용자 고유의 시스템 ID와 추가하려는 자격 증명을 사용하여 다음 스크립트를 실행합니다.

```azurecli
#Add Abap username
az keyvault secret set \
  --name <SID>-ABAPUSER \
  --value "<abapuser>" \
  --description SECRET_ABAP_USER --vault-name $kvname

#Add Abap Username password
az keyvault secret set \
  --name <SID>-ABAPPASS \
  --value "<abapuserpass>" \
  --description SECRET_ABAP_PASSWORD --vault-name $kvname

#Add java Username
az keyvault secret set \
  --name <SID>-JAVAOSUSER \
  --value "<javauser>" \
  --description SECRET_JAVAOS_USER --vault-name $kvname

#Add java Username password
az keyvault secret set \
  --name <SID>-JAVAOSPASS \
  --value "<javauserpass>" \
  --description SECRET_JAVAOS_PASSWORD --vault-name $kvname

#Add abapos username
az keyvault secret set \
  --name <SID>-ABAPOSUSER \
  --value "<abaposuser>" \
  --description SECRET_ABAPOS_USER --vault-name $kvname

#Add abapos username password
az keyvault secret set \
  --name <SID>-ABAPOSPASS \
  --value "<abaposuserpass>" \
  --description SECRET_ABAPOS_PASSWORD --vault-name $kvname

#Add Azure Log ws ID
az keyvault secret set \
  --name <SID>-LOG_WS_ID \
  --value "<logwsod>" \
  --description SECRET_AZURE_LOG_WS_ID --vault-name $kvname

#Add Azure Log ws public key
az keyvault secret set \
  --name <SID>-LOG_WS_PUBLICKEY \
  --value "<loswspubkey>" \
  --description SECRET_AZURE_LOG_WS_PUBLIC_KEY --vault-name $kvname
```

자세한 내용은 [az keyvault secret](/cli/azure/keyvault/secret) CLI 설명서를 참조하세요.

## <a name="perform-an-expert--custom-installation"></a>전문가/사용자 지정 설치 수행

이 절차에서는 온-프레미스를 설치할 때와 같이 전문가 또는 사용자 지정 설치를 사용하여 SAP 데이터 커넥터를 배포하는 방법을 설명합니다.

SAP 자격 증명으로 준비된 Key Vault가 있는 경우 이 절차를 수행하는 것이 좋습니다.

**SAP 데이터 커넥터를 배포하려면**:

1. 온-프레미스 머신에서 [SAP Launchpad 사이트](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip** 에서 최신 SAP NW RFC SDK를 다운로드합니다.

    > [!NOTE]
    > SDK에 액세스하려면 SAP 사용자 로그인 정보가 필요하며, 운영 체제와 일치하는 SDK를 다운로드해야 합니다.
    >
    > **LINUX ON X86_64** 옵션을 선택해야 합니다.

1. 온-프레미스 머신에서 의미 있는 이름을 사용하여 새 폴더를 만들고 SDK zip 파일을 새 폴더에 복사합니다.

1. microsoft 센티널 solution GitHub 리포지토리를 온-프레미스 컴퓨터에 복제 하 고 microsoft 센티널 SAP solution **systemconfig.ini** 파일을 새 폴더에 복사 합니다.

    예를 들면 다음과 같습니다.

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini 
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. 포함된 주석을 지침으로 사용하여 필요에 따라 **systemconfig.ini** 파일을 편집합니다. 자세한 내용은 [수동으로 SAP 데이터 커넥터 구성](#manually-configure-the-sap-data-connector)을 참조하세요.

    구성을 테스트하려면 사용자와 암호를 **systemconfig.ini** 구성 파일에 직접 추가하는 것이 좋습니다. [Azure Key vault](#add-azure-key-vault-secrets)를 사용하여 자격 증명을 저장하는 것이 좋지만, **env.List** 파일, [Docker 암호](#manually-configure-the-sap-data-connector)를 사용하거나, 자격 증명을 **systemconfig.ini** 파일에 직접 추가할 수도 있습니다.

1. **systemconfig.ini** 파일의 지침을 사용 하 여 Microsoft 센티널에 수집 하려는 로그를 정의 합니다. 예를 들어 [Microsoft 센티널로 전송 되는 SAP 로그 정의](#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)를 참조 하세요.

1. **systemconfig.ini** 파일의 지침을 사용하여 다음 구성을 정의합니다.

    - 감사 로그에 사용자 메일 주소를 포함할지 여부
    - 실패한 API 호출을 다시 시도할지 여부
    - cexal 감사 로그를 포함할지 여부
    - 데이터 추출(특히 큰 추출) 간의 시간 간격을 대기할지 여부

    자세한 내용은 [SAL 로그 커넥터 구성](#sal-logs-connector-settings)을 참조하세요.

1. 머신의 **sapcon** 디렉터리에 업데이트된 **systemconfig.ini** 파일을 저장합니다.

1. 자격 증명에 대해 **env.list** 파일을 사용하도록 선택한 경우 필요한 자격 증명을 사용하여 임시 **env.list** 파일을 만듭니다. Docker 컨테이너가 올바르게 실행되면 이 파일을 삭제해야 합니다.

    > [!NOTE]
    > 다음 스크립트에는 특정 ABAP 시스템에 연결되는 각 Docker 컨테이너가 있습니다. 사용자 환경에 필요한 대로 스크립트를 수정합니다.
    >

    다음을 실행합니다.

    ```bash
    ##############################################################
    # Include the following section if you're using user authentication
    ##############################################################
    # env.list template for Credentials
    SAPADMUSER=<SET_SAPCONTROL_USER>
    SAPADMPASSWORD=<SET_SAPCONTROL_PASS>
    ABAPUSER=SET_ABAP_USER>
    ABAPPASS=<SET_ABAP_PASS>
    JAVAUSER=<SET_JAVA_OS_USER>
    JAVAPASS=<SET_JAVA_OS_USER>
    ##############################################################
    # Include the following section if you are using Azure Keyvault
    ##############################################################
    # env.list template for AZ Cli when MI is not enabled
    AZURE_TENANT_ID=<your tenant id>
    AZURE_CLIENT_ID=<your client/app id>
    AZURE_CLIENT_SECRET=<your password/secret for the service principal>
    ##############################################################
    ```

1. SAP 데이터 커넥터가 설치된 상태에서 미리 정의된 Docker 이미지를 다운로드한 후 실행합니다.  다음을 실행합니다.

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker run --env-file=<env.list_location> -d --restart unless-stopped -v /home/$(pwd)/sapcon/<sap-sid>/:/sapcon-app/sapcon/config/system --name sapcon-<sid> sapcon
    rm -f <env.list_location>
    ```

1. Docker 어댑터가 제대로 실행되고 있는지 확인합니다. 다음을 실행합니다.

    ```bash
    docker logs –f sapcon-[SID]
    ```

1. **SAP 솔루션에 대 한 Microsoft 센티널 연속 위협 모니터링** 배포를 계속 합니다.

    솔루션을 배포 하면 SAP 데이터 커넥터가 Microsoft 센티널에 표시 되 고 SAP 통합 문서 및 분석 규칙을 배포할 수 있습니다. 완료되면 SAP 관심 목록을 수동으로 추가하고 사용자 지정합니다.

    자세한 내용은 [SAP 보안 콘텐츠 배포](sap-deploy-solution.md#deploy-sap-security-content)를 참조하세요.

## <a name="manually-configure-the-sap-data-connector"></a>SAP 데이터 커넥터 수동 구성

Microsoft 센티널 SAP solution data connector는 [배포 절차](#perform-an-expert--custom-installation)의 일부로 sap 데이터 커넥터 컴퓨터에 복제 한 **systemconfig.ini** 파일에서 구성 됩니다.

다음 코드는 샘플 **systemconfig.ini** 파일을 보여 줍니다.

```python
[Secrets Source]
secrets = '<DOCKER_RUNTIME/AZURE_KEY_VAULT/DOCKER_SECRETS/DOCKER_FIXED>'
keyvault = '<SET_YOUR_AZURE_KEYVAULT>'
intprefix = '<SET_YOUR_PREFIX>'

[ABAP Central Instance]
##############################################################
# Define the following values according to your server configuration.
ashost = <SET_YOUR_APPLICATION_SERVER_HOST>
mshost = <SET_YOUR_MESSAGE_SERVER_HOST> - #In case different then App
##############################################################
group = <SET_YOUR_LOGON_GROUP>
msserv = <SET_YOUR_MS_SERVICE> - #Required only if the message server service is not defined as sapms<SYSID> in /etc/services
sysnr = <SET_YOUR_SYS_NUMBER>
user = <SET_YOUR_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
passwd = <SET_YOUR_PASSWORD>
snc_partnername = <SET_YOUR_SNC_PARTNER_NAME>
snc_lib = <SET_YOUR_SNC_LIBRARY_PATH>
x509cert = <SET_YOUR_X509_CERTIFICATE>
##############################################################
sysid = <SET_YOUR_SYSTEM_ID>
client = <SET_YOUR_CLIENT>

[Azure Credentials]
loganalyticswsid = <SET_YOUR_LOG_ANALYTICS_WORKSPACE_ID>
publickey = <SET_YOUR_PUBLIC_KEY>

[File Extraction ABAP]
osuser = <SET_YOUR_SAPADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
ospasswd = <SET_YOUR_SAPADM_PASS>
x509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
appserver = <SET_YOUR_SAPCTRL_SERVER IP OR FQDN>
instance = <SET_YOUR_SAP_INSTANCE NUMBER, example 10>
abapseverity = <SET_ABAP_SEVERITY 0 = All logs ; 1 = Warning ; 2 = Error>
abaptz = <SET_ABAP_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use abaptz = GMT+12>

[File Extraction JAVA]
javaosuser = <SET_YOUR_JAVAADM_LIKE_USER>
##############################################################
# Enter your password OR your X509 SNC parameters
javaospasswd = <SET_YOUR_JAVAADM_PASS>
javax509pkicert = <SET_YOUR_X509_PKI_CERTIFICATE>
##############################################################
javaappserver = <SET_YOUR_JAVA_SAPCTRL_SERVER IP ADDRESS OR FQDN>
javainstance = <SET_YOUR_JAVA_SAP_INSTANCE for example 10>
javaseverity = <SET_JAVA_SEVERITY  0 = All logs ; 1 = Warning ; 2 = Error>
javatz = <SET_JAVA_TZ --Use ONLY GMT FORMAT-- example - For OS Timezone = NZST use javatz = GMT+12>
```

### <a name="define-the-sap-logs-that-are-sent-to-microsoft-sentinel"></a>Microsoft 센티널로 전송 되는 SAP 로그를 정의 합니다.

Microsoft 센티널 SAP solution **systemconfig.ini** 파일에 다음 코드를 추가 하 여 microsoft 센티널로 전송 되는 로그를 정의 합니다.

자세한 내용은 [Microsoft 센티널 SAP solution logs 참조 (공개 미리 보기)](sap-solution-log-reference.md)를 참조 하세요.

```python
##############################################################
# Enter True OR False for each log to send those logs to Microsoft Sentinel
[Logs Activation Status]
ABAPAuditLog = True
ABAPJobLog = True
ABAPSpoolLog = True
ABAPSpoolOutputLog = True
ABAPChangeDocsLog = True
ABAPAppLog = True
ABAPWorkflowLog = True
ABAPCRLog = True
ABAPTableDataLog = False
# ABAP SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
ABAPFilesLogs = False
SysLog = False
ICM = False
WP = False
GW = False
# Java SAP Control Logs - Retrieved by using SAP Conntrol interface and OS Login
JAVAFilesLogs = False
##############################################################
```

### <a name="sal-logs-connector-settings"></a>SAL 로그 커넥터 설정

Microsoft 센티널 SAP data connector **systemconfig.ini** 파일에 다음 코드를 추가 하 여 microsoft 센티널에 sap 로그 수집에 대 한 다른 설정을 정의 합니다.

자세한 내용은 [전문가/사용자 지정 SAP 데이터 커넥터 설치](#perform-an-expert--custom-installation)를 참조하세요.

```python
##############################################################
[Connector Configuration]
extractuseremail = True
apiretry = True
auditlogforcexal = False
auditlogforcelegacyfiles = False
timechunk = 60
##############################################################
```

이 섹션에서 다음 매개 변수를 구성할 수 있습니다.

|매개 변수 이름  |Description  |
|---------|---------|
|**extractuseremail**     |  사용자 메일 주소가 감사 로그에 포함되는지 여부를 결정합니다.       |
|**apiretry**     |   API 호출이 장애 조치 메커니즘으로 다시 시도되는지 여부를 결정합니다.      |
|**auditlogforcexal**     |  시스템에서 SAP BASIS 버전 7.4와 같은 비 SAL 시스템에 대해 감사 로그를 강제로 사용하도록 하는지 여부를 결정합니다.       |
|**auditlogforcelegacyfiles**     |  시스템에서 레거시 시스템 기능(예: 더 낮은 패치 수준의 SAP BASIS 버전 7.4)의 감사 로그를 강제로 사용하도록 하는지 여부를 결정합니다.|
|**timechunk**     |   시스템에서 데이터 추출 간격으로 특정 시간(분) 동안 대기하는지 결정합니다. 많은 양의 데이터가 예상되는 경우 이 매개 변수를 사용합니다. <br><br>예를 들어, 처음 24시간 동안 초기 데이터를 로드할 때 각 데이터를 추출하는 데 충분한 시간을 주기 위해 데이터 추출이 30분마다 실행되도록 할 수 있습니다. 이러한 경우에는 이 값을 **30** 으로 설정합니다.  |
|     |         |

### <a name="configuring-an-abap-sap-control-instance"></a>ABAP SAP Control 인스턴스 구성

모든 ABAP 로그를 Microsoft 센티널에 수집 하려면 NW RFC와 sap control 웹 서비스 기반 로그를 모두 포함 하 여 다음 ABAP sap 컨트롤 세부 정보를 구성 합니다.

|설정  |Description  |
|---------|---------|
|**javaappserver**     |SAP Control ABAP 서버 호스트를 입력합니다. <br>예: `contoso-erp.appserver.com`         |
|**javainstance**     |SAP Control ABAP 인스턴스 번호를 입력합니다. <br>예: `00`         |
|**abaptz**     |SAP Control ABAP 서버에서 구성된 표준 시간대를 GMT 형식으로 입력합니다. <br>예: `GMT+3`         |
|**abapseverity**     |Microsoft 센티널에 ABAP 로그를 수집 하려는 가장 낮은 심각도의 심각도 수준을 입력 합니다.  값은 다음과 같습니다. <br><br>- **0** = 모든 로그 <br>- **1** = 경고 <br>- **2** = 오류     |

### <a name="configuring-a-java-sap-control-instance"></a>Java SAP Control 인스턴스 구성

Microsoft Sentinel에 SAP Control 웹 서비스 로그를 구성하려면 다음 JAVA SAP Control 인스턴스 세부 정보를 구성합니다.

|매개 변수  |Description  |
|---------|---------|
|**javaappserver**     |SAP Control Java 서버 호스트를 입력합니다. <br>예: `contoso-java.server.com`         |
|**javainstance**     |SAP Control ABAP 인스턴스 번호를 입력합니다. <br>예: `10`         |
|**javatz**     |SAP Control Java 서버에서 구성된 표준 시간대를 GMT 형식으로 입력합니다. <br>예: `GMT+3`         |
|**javaseverity**     |Microsoft Sentinel에 웹 서비스 로그를 검색하려는 가장 낮고 포괄적인 심각도 수준을 입력합니다.  값은 다음과 같습니다. <br><br>- **0** = 모든 로그 <br>- **1** = 경고 <br>- **2** = 오류     |

## <a name="next-steps"></a>다음 단계

SAP 데이터 커넥터를 설치한 후 SAP 관련 보안 콘텐츠를 추가할 수 있습니다.

자세한 내용은 [SAP 솔루션 배포](sap-deploy-solution.md#deploy-sap-security-content)를 참조하세요.

자세한 내용은 다음을 참조하세요.

- [SNC를 사용하여 Microsoft Sentinel SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)
- [Microsoft Sentinel SAP 솔루션 세부 SAP 요구 사항](sap-solution-detailed-requirements.md)
- [Microsoft Sentinel SAP 솔루션 로그 참조](sap-solution-log-reference.md)
- [Microsoft Sentinel SAP 솔루션: 보안 콘텐츠 참조](sap-solution-security-content.md)
- [Microsoft Sentinel SAP 솔루션 배포 문제 해결](sap-deploy-troubleshoot.md)
