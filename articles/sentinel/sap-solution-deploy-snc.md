---
title: SNC(Secure Network Communications) | Microsoft Sentinel SAP 데이터 커넥터 배포 Microsoft Docs
description: NetWeaver/ABAP 인터페이스 기반 로그에 대해 SNC를 통한 보안 연결을 사용하여 SAP 환경용 Microsoft Sentinel 데이터 커넥터를 배포하는 방법을 알아봅니다.
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.subservice: microsoft-sentinel
ms.openlocfilehash: cc3c58743fc1cfca90ff8a2f20e5450895accc75
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519879"
---
# <a name="deploy-the-microsoft-sentinel-sap-data-connector-with-snc"></a>SNC를 사용하여 Microsoft Sentinel SAP 데이터 커넥터 배포

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 NetWeaver/ABAP 인터페이스 기반 로그에 대한 SNC(Secure Network Communications)를 통해 SAP에 대한 보안 연결이 있는 경우 Microsoft Sentinel SAP 데이터 커넥터를 배포하는 방법을 설명합니다.

> [!NOTE]
> Microsoft Sentinel SAP 데이터 커넥터를 배포하는 데 가장 권장되는 기본 프로세스는 [Azure VM을 사용하는](sap-deploy-solution.md)것입니다. 이 문서는 고급 사용자를 위한 것입니다.

> [!IMPORTANT]
> Microsoft Sentinel SAP 솔루션은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Microsoft Sentinel SAP 데이터 커넥터를 배포하기 위한 기본 필수 조건은 배포 방법에 관계없이 동일합니다.

시작하기 전에 시스템에서 주 [SAP 데이터 커넥터 배포 프로시저](sap-deploy-solution.md#prerequisites)에 설명된 필수 조건을 준수하는지 확인합니다.

SNC를 사용한 작업을 위한 기타 필수 조건은 다음과 같습니다.

- **SNC를 통한 안전한 SAP 연결**. 연결하려는 AS ABAP 시스템의 리포지토리 상수에서 연결별로 SNC 매개 변수를 정의합니다. 자세한 내용은 관련 [SAP 커뮤니티 Wiki 페이지](https://wiki.scn.sap.com/wiki/display/Security/Securing+Connections+to+AS+ABAP+with+SNC)를 참조하세요.

- SAP Service Marketplace에서 다운로드한 **SAPCAR 유틸리티**. 자세한 내용은 [SAP 설치 가이드](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/467291d0dc104d19bba073a0380dc6b4.html)를 참조하세요.

자세한 내용은 [Microsoft Sentinel SAP 솔루션 상세 SAP 요구 사항(공개 미리 보기)을 참조하세요.](sap-solution-detailed-requirements.md)

## <a name="create-your-azure-key-vault"></a>Azure Key Vault 만들기

Microsoft Sentinel SAP 데이터 커넥터에만 사용할 수 있는 Azure Key Vault를 만듭니다.

다음 명령을 실행하여 Azure Key Vault를 만들고 Azure 서비스 주체에 대한 액세스 권한을 부여합니다.

``` azurecli
kvgp=<KVResourceGroup>

kvname=<keyvaultname>

spname=<sp-name>

kvname=<keyvaultname>
# Optional when Azure MI not enabled - Create sp user for AZ cli connection, save details for env.list file

az ad sp create-for-rbac –name $spname

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

## <a name="deploy-the-sap-data-connector"></a>SAP 데이터 커넥터 배포

이 프로시저에서는 SNC를 통해 연결할 때 VM에 SAP 데이터 커넥터를 배포하는 방법을 설명합니다.

[SAP 자격 증명](#add-azure-key-vault-secrets)으로 준비된 [키 자격 증명 모음](#create-your-azure-key-vault)이 있는 경우 이 프로시저를 수행하는 것이 좋습니다.

**SAP 데이터 커넥터를 배포하려면**:

1. 데이터 커넥터 VM에서 [SAP Launchpad 사이트](https://support.sap.com) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip** 에서 최신 SAP NW RFC SDK를 다운로드합니다.

    > [!NOTE]
    > SDK에 액세스하려면 SAP 사용자 로그인 정보가 필요하며, 운영 체제와 일치하는 SDK를 다운로드해야 합니다.
    >
    > **LINUX ON X86_64** 옵션을 선택해야 합니다.

1. 의미 있는 이름을 사용하여 새 폴더를 만들고 SDK Zip 파일을 새 폴더에 복사합니다.

1. Microsoft Sentinel 솔루션 GitHub 리포지션을 데이터 커넥터 VM에 복제하고 Microsoft Sentinel SAP 솔루션 **systemconfig.ini** 파일을 새 폴더에 복사합니다.

    예를 들면 다음과 같습니다.

    ```bash
    mkdir /home/$(pwd)/sapcon/<sap-sid>/
    cd /home/$(pwd)/sapcon/<sap-sid>/
    wget  https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/template/systemconfig.ini
    cp <**nwrfc750X_X-xxxxxxx.zip**> /home/$(pwd)/sapcon/<sap-sid>/
    ```

1. 포함된 주석을 지침으로 사용하여 필요에 따라 **systemconfig.ini** 파일을 편집합니다.

    키 자격 증명 모음 비밀을 제외한 모든 구성을 편집해야 합니다. 자세한 내용은 [수동으로 SAP 데이터 커넥터 구성](sap-solution-deploy-alternate.md#manually-configure-the-sap-data-connector)을 참조하세요.

1. **systemconfig.ini** 파일의 지침을 사용하여 Microsoft Sentinel에 로깅하려는 로그를 정의합니다.

    예를 들어 [Microsoft Sentinel로 전송되는 SAP 로그 정의를](sap-solution-deploy-alternate.md#define-the-sap-logs-that-are-sent-to-microsoft-sentinel)참조하세요.

    > [!NOTE]
    > SNC 통신 관련 로그는 NetWeaver/ABAP 인터페이스를 통해 검색되는 로그입니다. SAP 컨트롤 및 HANA 로그는 SNC의 범위를 벗어났습니다.
    >

1. **systemconfig.ini** 파일의 지침을 사용하여 다음 구성을 정의합니다.

    - 감사 로그에 사용자 메일 주소를 포함할지 여부
    - 실패한 API 호출을 다시 시도할지 여부
    - cexal 감사 로그를 포함할지 여부
    - 데이터 추출(특히 큰 추출) 간의 시간 간격을 대기할지 여부

    자세한 내용은 [SAL 로그 커넥터 구성](sap-solution-deploy-alternate.md#sal-logs-connector-settings)을 참조하세요.

1. VM의 **sapcon** 디렉터리에 업데이트된 **systemconfig.ini** 파일을 저장합니다.

1. SAP 데이터 커넥터가 설치된 상태에서 미리 정의된 Docker 이미지를 다운로드한 후 실행합니다. 다음을 실행합니다.

    ```bash
    docker pull docker pull mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    docker create -v $(pwd):/sapcon-app/sapcon/config/system -v /home/azureuser /sap/sec:/sapcon-app/sec --env SCUDIR=/sapcon-app/sec --name sapcon-snc mcr.microsoft.com/azure-sentinel/solutions/sapcon:latest-preview
    ```

## <a name="post-deployment-sap-system-procedures"></a>배포 후 SAP 시스템 프로시저

SAP 데이터 커넥터를 배포한 후 다음 SAP 시스템 프로시저를 수행합니다.

1. [SAP Service Marketplace](https://launchpad.support.sap.com/#/) > **Software Downloads**(소프트웨어 다운로드) > **Browse our Download Catalog**(다운로드 카탈로그 찾아보기) > **SAP Cryptographic Software**(SAP 암호화 소프트웨어)에서 SAP Cryptographic Library(SAP 암호화 라이브러리)를 다운로드합니다.

    자세한 내용은 [SAP 설치 가이드](https://help.sap.com/viewer/d1d04c0d65964a9b91589ae7afc1bd45/5.0.4/en-US/86921b29cac044d68d30e7b125846860.html)를 참조하세요.

1. SAPCAR 유틸리티를 사용하여 라이브러리 파일을 추출하고 `<sec>` 디렉터리에서 SAP 데이터 커넥터 VM에 배포합니다.

1. 라이브러리 파일을 실행할 수 있는 권한이 있는지 확인합니다.

1. `<sec>` 디렉터리의 전체 경로 값을 사용하여 **SECUDIR** 이라는 환경 변수를 정의합니다.

1. PSE(개인 보안 환경)를 만듭니다. **sapgenspe** 명령줄 도구는 SAP 데이터 커넥터 VM의 `<sec>` 디렉터리에서 사용할 수 있습니다.

    예를 들면 다음과 같습니다.

    ```bash
    ./sapgenpse get_pse -p my_pse.pse -noreq -x my_pin "CN=sapcon.com, O=my_company, C=IL"
    ```

    자세한 내용은 SAP 설명서의 [Creating a Personal Security Environment](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/285bb1fda3fa472c8d9205bae17a6f95.html)(개인 보안 환경 만들기)를 참조하세요.

1. PSE에 대한 자격 증명을 만듭니다. 예를 들면 다음과 같습니다.

    ```bash
    ./sapgenpse seclogin -p my_pse.pse -x my_pin -O MXDispatcher_Service_User
    ```

    자세한 내용은 SAP 설명서의 [Creating Credentials](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/d8b50371667740e797e6c9f0e9b7141f.html)(자격 증명 만들기)를 참조하세요.

1. ID 센터와 AS ABAP SNC PSE 간에 퍼블릭 키 인증서를 교환합니다.

    예를 들어 ID 센터의 퍼블릭 키 인증서를 내보내려면 다음을 실행합니다.

    ```bash
    ./sapgenpse export_own_cert -o my_cert.crt -p my_pse.pse -x abcpin
    ```

    AS ABAP SNC PSE로 인증서를 가져와서 PSE에서 내보낸 다음, ID 센터로 다시 가져옵니다.

    예를 들어 인증서를 ID 센터로 가져오려면 다음을 실행합니다.

    ```bash
    ./sapgenpse maintain_pk -a full_path/my_secure_dir/my_exported_cert.crt -p my_pse.pse -x my_pin
    ```

    자세한 내용은 SAP 설명서에서 [Exchanging the Public-Key Certificates](https://help.sap.com/viewer/4773a9ae1296411a9d5c24873a8d418c/8.0/en-US/7bbf90b29c694e6080e968559170fbcd.html)(퍼블릭 키 인증서 교환)를 참조하세요.

## <a name="edit-the-sap-data-connector-configuration"></a>SAP 데이터 커넥터 구성 편집

1. SAP 데이터 커넥터 VM에서 **systemconfig.ini** 파일로 이동하여 다음 매개 변수를 관련 값으로 정의합니다.

    ```ini
    [Secrets Source]
    secrets = AZURE_KEY_VAULT
    ```

1. [Azure 키 자격 증명 모음](#create-your-azure-key-vault)에서 다음 비밀을 생성합니다.

    - 값이 `<Relevant DN details>`인 `<Interprefix>-ABAPSNCPARTNERNAME`
    - 값이 `<lib_Path>`인 `<Interprefix>-ABAPSNCLIB`
    - 값이 `<Certificate_Code>)`인 `<Interprefix>-ABAPX509CERT`

    예를 들면 다음과 같습니다.

    ```ini
    S4H-ABAPSNCPARTNERNAME  =  'p:CN=help.sap.com, O=SAP_SE, C=IL' (Relevant DN)
    S4H-ABAPSNCLIB = 'home/user/sec-dir' (Relevant directory)
    S4H-ABAPX509CERT = 'MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO' (Relevant certificate code)
    ```

    > [!NOTE]
    > 기본적으로 `<Interprefix>` 값은 SID입니다(예: `A4H-<ABAPSNCPARTNERNAME>`).
    >

구성 파일에 직접 비밀을 입력하는 경우 다음과 같이 매개 변수를 정의합니다.

```ini
[Secrets Source]
secrets = DOCKER_FIXED
[ABAP Central Instance]
snc_partnername =  <Relevant_DN_Deatils>
snc_lib =  <lib_Path>
x509cert = <Certificate_Code>
For example:
snc_partnername =  p:CN=help.sap.com, O=SAP_SE, C=IL (Relevant DN)
snc_lib = /sapcon-app/sec/libsapcrypto.so (Relevant directory)
x509cert = MIIDJjCCAtCgAwIBAgIBNzA ... NgalgcTJf3iUjZ1e5Iv5PLKO (Relevant certificate code)
```

### <a name="attach-the-snc-parameters-to-your-user"></a>사용자에게 SNC 매개 변수 연결

1. SAP 데이터 커넥터 VM에서 `SM30` 트랜잭션을 호출하고 `USRACLEXT` 테이블을 유지하도록 선택합니다.

1. 새 항목을 추가합니다. **사용자** 필드에서 ABAP 시스템을 연결하는 데 사용되는 통신 사용자를 입력합니다.

1. 메시지가 표시되면 SNC 이름을 입력합니다. SNC 이름은 Identity Manager PSE를 만들 때 제공되는 고유한 고유 이름입니다. 예: `CN=IDM, OU=SAP, C=DE`

    SNC 이름 전에 `p`를 추가했는지 확인합니다. 예: `p:CN=IDM, OU=SAP, C=DE`.

1. **저장** 을 선택합니다.

SNC는 데이터 커넥터 VM에서 사용하도록 설정됩니다.

## <a name="activate-the-sap-data-connector"></a>SAP 데이터 커넥터 활성화

이 프로시저에서는 이 문서의 앞부분에 있는 절차를 통해 만든 보안 SNC 연결을 사용하여 SAP 데이터 커넥터를 활성화하는 방법을 설명합니다.

1. Docker 이미지를 활성화하려면 다음을 수행합니다.

    ```bash
    docker start sapcon-<SID>
    ```

1. 연결을 확인합니다. 다음을 실행합니다.

    ```bash
    docker logs sapcon-<SID>
    ```

1. 연결이 실패하면 로그를 사용하여 문제를 파악합니다.

    필요한 경우 Docker 이미지를 사용하지 않도록 설정합니다.

    ```bash
    docker stop sapcon-<SID>
    ```

예를 들어 **systemconfig.ini** 파일 또는 Azure 키 자격 증명 모음의 잘못된 구성으로 인해 문제가 발생할 수 있습니다. 또는 SNC를 통해 보안 연결을 만드는 단계 중 일부가 올바르게 실행되지 않았을 수 있습니다.

다시 위의 단계에 따라 SNC를 통해 보안 연결을 구성해 보세요. 자세한 내용은 Microsoft [Sentinel SAP 솔루션 배포 문제 해결을 참조하세요.](sap-deploy-troubleshoot.md)

## <a name="next-steps"></a>다음 단계

SAP 데이터 커넥터가 활성화된 후 **SAP용 Microsoft Sentinel - Continuous Threat Monitoring** 솔루션을 배포하여 계속 진행합니다. 자세한 내용은 [SAP 보안 콘텐츠 배포](sap-deploy-solution.md#deploy-sap-security-content)를 참조하세요.

솔루션을 배포하면 SAP 데이터 커넥터가 Microsoft Sentinel에 표시되고 SAP 통합 문서 및 분석 규칙이 배포됩니다. 완료되면 SAP 관심 목록을 수동으로 추가하고 사용자 지정합니다.

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel SAP 솔루션 세부 SAP 요구 사항](sap-solution-detailed-requirements.md)
- [Microsoft Sentinel SAP 솔루션 로그 참조](sap-solution-log-reference.md)
- [Microsoft Sentinel SAP 솔루션: 보안 콘텐츠 참조](sap-solution-security-content.md)
