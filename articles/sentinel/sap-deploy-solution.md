---
title: SAP 지속적인 위협 모니터링 배포 | Microsoft Docs
description: SAP 환경에 대한 Azure Sentinel 솔루션을 배포하는 방법을 알아봅니다.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: how-to
ms.custom: mvc
ms.date: 07/06/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 7b6f68eea2c177ad4e6776723ae0387c0e0da6a1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361835"
---
#  <a name="deploy-sap-continuous-threat-monitoring-public-preview"></a>SAP 지속적인 위협 모니터링 배포(퍼블릭 미리 보기)

이 문서에서는 SAP에 대한 Azure Sentinel 지속적인 위협 모니터링 프로세스를 단계별로 안내합니다.

> [!IMPORTANT]
> Azure Sentinel SAP 솔루션은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="overview"></a>개요

[Azure Sentinel 솔루션](sentinel-solutions.md)에는 위협 탐지, 통합 문서, 관심 목록 등 번들로 제공되는 보안 콘텐츠가 포함됩니다. 솔루션을 사용하면 단일 프로세스로 특정 데이터 커넥터에 대한 Azure Sentinel 보안 콘텐츠를 온보딩할 수 있습니다.

Azure Sentinel SAP 데이터 커넥터를 사용하면 SAP 시스템에서 비즈니스 및 애플리케이션 계층 내의 정교한 위협을 모니터링할 수 있습니다.

SAP 데이터 커넥터는 전체 SAP 시스템 환경에서 다양한 14개의 애플리케이션 로그를 스트리밍하고 NetWeaver RFC 호출을 통해 ABAP(Advanced Business Application Programming)에서, OSSAP 제어 인터페이스를 통해 파일 스토리지 데이터에서 로그를 수집합니다. SAP 데이터 커넥터는 SAP 기본 인프라를 모니터링하는 Azure Sentinel 기능을 추가합니다.

Azure Sentinel로 SAP 로그를 수집하려면 SAP 환경에 Azure Sentinel SAP 데이터 커넥터가 설치되어 있어야 합니다. 이 자습서에 설명된 대로 배포를 위해서는 Azure VM에서 Docker 컨테이너를 사용하는 것이 좋습니다.

SAP 데이터 커넥터를 배포한 후에는 SAP 솔루션 보안 콘텐츠를 배포하여 조직의 SAP 환경을 원활하게 파악하고 관련 보안 작업 기능을 향상합니다.

이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * SAP 데이터 커넥터 배포를 위해 SAP 시스템을 준비하는 방법
> * Docker 컨테이너 및 Azure VM을 사용하여 SAP 데이터 커넥터를 배포하는 방법
> * Azure Sentinel에서 SAP 솔루션 보안 콘텐츠를 배포하는 방법

> [!NOTE]
> 보안 SNC 연결을 통해 SAP 데이터 커넥터를 배포하려면 추가 단계가 필요합니다. 자세한 내용은 [SNC로 Azure Sentinel SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)를 참조하세요.
>
## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 대로 Azure Sentinel SAP 데이터 커넥터 및 보안 콘텐츠를 배포하려면 다음 필수 조건이 있어야 합니다.

|영역  |Description  |
|---------|---------|
|**Azure 필수 조건**     |  **Azure Sentinel에 액세스**. [SAP 데이터 커넥터를 배포](#deploy-your-sap-data-connector)할 때 이 자습서에서 사용할 Azure Sentinel 작업 영역 ID 및 키를 기록해 둡니다. <br>Azure Sentinel에서 이러한 세부 정보를 보려면 **설정** > **작업 영역 설정** > **에이전트 관리** 로 이동합니다. <br><br>**Azure 리소스를 만드는 기능**. 자세한 내용은 [Azure Resource Manager 설명서](../azure-resource-manager/management/manage-resources-portal.md)를 참조하세요. <br><br>**Azure Key Vault에 액세스**. 이 자습서에서는 Azure Key Vault를 사용하여 자격 증명을 저장하는 데 권장되는 단계를 설명합니다. 자세한 내용은 [Azure Key Vault 설명서](../key-vault/index.yml)를 참조하세요.       |
|**시스템 필수 구성 요소**     |   **소프트웨어**. SAP 데이터 커넥터 배포 스크립트는 소프트웨어 필수 조건을 자동으로 설치합니다. 자세한 내용은 [자동으로 설치된 소프트웨어](#automatically-installed-software)를 참조하세요. <br><br> **시스템 연결**. SAP 데이터 커넥터 호스트 역할을 하는 VM이 다음 항목에 액세스할 수 있는지 확인합니다. <br>- Azure Sentinel <br>- Azure Key Vault <br>- SAP 환경 호스트(*32xx*, *5xx13*, *33xx* 등의 TCP 포트 사용, 여기서 *xx* 는 SAP 인스턴스 번호입니다.) <br><br>SAP 소프트웨어 다운로드 페이지에 액세스하려면 SAP 사용자 계정도 있어야 합니다.<br><br>**시스템 아키텍처**. SAP 솔루션은 VM에 Docker 컨테이너로 배포되며 각 SAP 클라이언트에는 자체 컨테이너 인스턴스가 필요합니다. 크기 조정 권장 사항은 [권장 가상 머신 크기 조정](sap-solution-detailed-requirements.md#recommended-virtual-machine-sizing)을 참조하세요. <br>VM 및 Azure Sentinel 작업 영역은 다른 Azure 구독 및 다른 Azure AD 테넌트에 있을 수 있습니다.|
|**SAP 필수 조건**     |   **지원되는 SAP 버전**. [SAP_BASIS 버전 750 SP13](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows) 이상을 사용하는 것이 좋습니다. <br>이전 SAP 버전 [SAP_BASIS 740](https://support.sap.com/en/my-support/software-downloads/support-package-stacks/product-versions.html#:~:text=SAP%20NetWeaver%20%20%20%20SAP%20Product%20Version,%20%20SAPKB710%3Cxx%3E%20%207%20more%20rows)에서 작업하는 경우 이 자습서에서 대체 지침을 제공하는 단계를 선택합니다.<br><br> **SAP 시스템 세부 정보**. 이 자습서에서 사용할 수 있도록 다음과 같은 SAP 시스템 세부 정보를 기록해 둡니다.<br>    - SAP 시스템 IP 주소<br>- SAP 시스템 번호(예: `00`)<br>    - SAP 시스템 ID(SAP NetWeaver 시스템 제공) 예들 들어 `NPL`입니다. <br>- SAP 클라이언트 ID(예: `001`)<br><br>**SAP NetWeaver 인스턴스 액세스**. SAP 인스턴스에 액세스하려면 다음 옵션 중 하나를 사용해야 합니다. <br>- [SAP ABAP 사용자/암호](#configure-your-sap-system). <br>- SAP CRYPTOLIB PSE를 사용하는 X509 인증서가 있는 사용자. 이 옵션을 사용하려면 전문가 매뉴얼 단계가 필요할 수 있습니다.<br><br>**SAP 팀의 지원**.  솔루션 배포를 위해 SAP 시스템이 [올바르게 구성](#configure-your-sap-system)되었는지 확인하려면 SAP 팀의 지원이 필요합니다.   |
|     |         |


### <a name="automatically-installed-software"></a>자동으로 설치된 소프트웨어

[SAP 데이터 커넥터 배포 스크립트](#deploy-your-sap-data-connector)는 SUDO(루트) 권한을 사용하여 VM에 다음 소프트웨어를 설치합니다.

- [Unzip.](https://www.microsoft.com/en-us/p/unzip/9mt44rnlpxxt?activetab=pivot:overviewtab)
- [NetCat](https://sectools.org/tool/netcat/)
- [Python 3.6 이상](https://www.python.org/downloads/)
- [Python3-pip](https://pypi.org/project/pip/)
- [Docker](https://www.docker.com/)

## <a name="configure-your-sap-system"></a>SAP 시스템 구성

이 절차에서는 SAP 시스템에 올바른 필수 조건이 설치되고 Azure Sentinel SAP 데이터 커넥터 배포용으로 구성되었는지 확인하는 방법을 설명합니다.

> [!IMPORTANT]
> 올바른 구성을 보장하려면 SAP 팀과 함께 이 절차를 수행하세요.
>

**SAP 데이터 커넥터용으로 SAP 시스템을 구성하려면** 다음을 수행합니다.

1. 버전에 따라 다음 SAP 노트가 시스템에 배포되었는지 확인합니다.

    |SAP BASIS 버전  |필요한 노트 |
    |---------|---------|
    |- 750 SP01~SP12<br>- 751 SP01~SP06<br>- 752 SP01~SP03     |  2641084: 보안 감사 로그 데이터에 대한 표준화된 읽기 권한       |
    |- 700~702<br>- 710~711, 730, 731, 740, 750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
    |- 700~702<br>- 710~711, 730, 731, 740<br>- 750~752     | 2502336: CD(변경 문서): RSSCD100 - 데이터베이스가 아닌 보관 계층에서 읽기 전용        |
    |     |         |

    이후 버전에는 추가 참고 사항이 필요하지 않습니다. 자세한 내용은 [SAP 지원 실행 패드 사이트](https://support.sap.com/en/index.html), SAP 사용자 계정으로 로그인을 참조하세요.

1. Azure Sentinel GitHub 리포지토리(https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR: )에서 다음 SAP 변경 요청 중 하나를 다운로드하여 설치합니다.

    - **SAP 버전 750 이상**: SAP 변경 요청 *144(NPLK900144)* 설치
    - **SAP 버전 740**: SAP 변경 요청 *146(NPLK900146)* 설치

    이 단계를 수행하는 경우 이진 모드를 사용하여 파일을 SAP 시스템으로 전송하고 **STMS_IMPORT** SAP 트랜잭션 코드를 사용해야 합니다.

    > [!NOTE]
    > SAP **가져오기 옵션** 영역에 **Ignore Invalid Component Version**(잘못된 구성 요소 버전 무시) 옵션이 표시될 수 있습니다. 표시되면 이 옵션을 선택한 후 계속합니다.
    >

1. SAP 변경 요청 *14(NPLK900140)* 를 가져와 **/MSFTSEN/SENTINEL_CONNECTOR** 라는 새 SAP 역할을 만듭니다. **STMS_IMPORT** SAP 트랜잭션 코드를 사용합니다.

    다음과 같이 필요한 권한으로 역할이 만들어졌는지 확인합니다.

    :::image type="content" source="media/sap/required-sap-role-authorizations.png" alt-text="Azure Sentinel SAP 데이터 커넥터에 필요한 SAP 역할 권한":::

    자세한 내용은 [ABAP 사용자에 대한 권한 부여](sap-solution-detailed-requirements.md#required-abap-authorizations)를 참조하세요.

1. SAP 데이터 커넥터에 대해 대화 상자가 아닌 RFC/NetWeaver 사용자를 만들고 새로 만든 **/MSFTSEN/SENTINEL_CONNECTOR** 역할을 연결합니다.

    - 역할을 연결한 후 역할 권한이 사용자에게 배포되는지 확인합니다.
    - 이 프로세스에서는 ABAP 사용자에 대한 사용자 이름 및 암호를 사용해야 합니다. 새 사용자를 만들고 필요한 권한을 부여한 다음에는 ABAP 사용자 암호를 변경해야 합니다.

1. **SAP NetWeaver RFC SDK 7.50 for Linux on x86_64 64 BIT** 버전을 다운로드하여 VM에 배치합니다. 이 버전은 설치 프로세스 중에 필요합니다.

    예를 들어 [SAP 소프트웨어 다운로드 사이트](https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=01200314690100002214&V=MAINT) > **SAP NW RFC SDK** > **SAP NW RFC SDK 7.50** > **nwrfc750X_X-xxxxxxx.zip** 에서 SDK를 찾습니다. **LINUX ON X86_64 65BIT** 옵션을 다운로드해야 합니다. SCP 사용과 같은 방법으로 VM에 파일을 복사합니다.

    SAP 소프트웨어 다운로드 페이지에 액세스하려면 SAP 사용자 계정이 필요합니다.

1. (선택 사항) SAP **Auditlog** 파일은 시스템 차원에서 사용되며 여러 SAP 클라이언트를 지원합니다. 그러나 Azure Sentinel SAP 솔루션의 각 인스턴스는 단일 SAP 클라이언트만 지원합니다.

    따라서 다중 클라이언트 SAP 시스템을 사용하는 경우에는 SAP 솔루션을 배포하는 클라이언트에 대해서만 **Auditlog** 파일을 사용하도록 설정하여 데이터 복제를 방지하는 것이 좋습니다.


## <a name="deploy-a-linux-vm-for-your-sap-data-connector"></a>SAP 데이터 커넥터용 Linux VM 배포

이 절차에서는 Azure CLI를 사용하여 Ubuntu 서버 18.04 LTS VM을 배포하고 [시스템 관리 ID](../active-directory/managed-identities-azure-resources/index.yml)를 할당하는 방법에 대해 설명합니다.

> [!TIP]
> RHEL 버전 7.7 이상 또는 SUSE 버전 15 이상에서 데이터 커넥터를 배포할 수도 있습니다. OS 및 패치 수준은 완전히 최신 상태여야 합니다.
>

**Ubuntu VM을 배포하고 준비하려면** 다음을 수행합니다.

1. 다음 명령을 예로 사용하여 리소스 그룹 및 VM 이름에 대한 값을 삽입합니다.

    ```azurecli
    az vm create  --resource-group [resource group name]   --name [VM Name] --image UbuntuLTS  --admin-username azureuser --data-disk-sizes-gb 10 – --size Standard_DS2 --generate-ssh-keys  --assign-identity
    ```

1. 새 VM에서 다음을 설치합니다.

    - [Venv](https://docs.python.org/3.8/library/venv.html)(Python 버전 3.8 이상 포함).
    - [Azure CLI](/cli/azure/) 버전 2.8.0 이상.

> [!IMPORTANT]
> 다른 VM과 마찬가지로 조직에 대한 보안 모범 사례를 모두 적용해야 합니다.
>

자세한 내용은 [빠른 시작: Azure CLI를 사용하여 Linux 가상 머신 만들기](../virtual-machines/linux/quick-create-cli.md)를 참조하세요.

## <a name="create-key-vault-for-your-sap-credentials"></a>SAP 자격 증명에 대한 키 자격 증명 모음 만들기

이 자습서에서는 새로 만들거나 전용인 [Azure Key Vault](../key-vault/index.yml)를 사용하여 SAP 데이터 커넥터에 대한 자격 증명을 저장합니다.

**Azure Key Vault를 만들거나 전용으로 설정하려면** 다음을 수행합니다.

1. 새 Azure Key Vault를 만들거나, 기존 Azure Key Vault를 선택하여 SAP 데이터 커넥터 배포 전용으로 설정합니다.

    예를 들어 새 Key Vault를 만들려면 Key Vault 리소스 그룹의 이름을 사용하고 Key Vault 이름을 입력하여 다음 명령을 실행합니다.

    ```azurecli
    kvgp=<KVResourceGroup>

    kvname=<keyvaultname>

    #Create Key Vault
    az keyvault create \
      --name $kvname \
      --resource-group $kvgp
    ```

1. 다음 방법 중 하나를 사용하여 VM의 관리 ID에 GET, LIST 및 SET 권한을 포함한 액세스 정책을 할당합니다.

    - **Azure Portal 통해:**

        Azure Key Vault에서 **액세스 정책** > **액세스 정책 추가 - 비밀 권한: Get, List 및 Set** > **보안 주체 선택** 을 선택합니다. [VM의 이름](#deploy-a-linux-vm-for-your-sap-data-connector)을 입력한 다음, **추가** > **저장** 을 선택합니다.

        자세한 내용은 [Key Vault 설명서](../key-vault/general/assign-access-policy-portal.md)를 참조하세요.

    - **Azure CLI 통해:**

        1. 다음 명령을 실행하여 [VM의 보안 주체 ID](#deploy-a-linux-vm-for-your-sap-data-connector)를 가져오고 Azure 리소스 그룹의 이름을 입력합니다.

            ```azurecli
            VMPrincipalID=$(az vm show -g [resource group] -n [Virtual Machine] --query identity.principalId -o tsv)
            ```

            다음 단계에서 사용할 수 있도록 보안 주체 ID가 표시됩니다.

        1. 다음 명령을 실행하여 Key Vault VM의 액세스 권한을 할당하고, 리소스 그룹의 이름 및 이전 단계에서 반환된 보안 주체 ID 값을 입력합니다.

            ```azurecli
            az keyvault set-policy -n [key vault] -g [resource group] --object-id $VMPrincipalID --secret-permissions get list set
            ```
## <a name="deploy-your-sap-data-connector"></a>SAP 데이터 커넥터 배포

Azure Sentinel SAP 데이터 커텍터 배포 스크립트는 [필수 소프트웨어](#automatically-installed-software)를 설치한 다음, [새로 만든 VM](#deploy-a-linux-vm-for-your-sap-data-connector)에 커넥터를 설치하고 [전용 키 자격 증명 모음](#create-key-vault-for-your-sap-credentials)에 자격 증명을 저장합니다.

SAP 데이터 커넥터 배포 스크립트는 [Azure Sentinel GitHub 리포지토리 > DataConnectors > SAP](https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh)에 저장됩니다.

SAP 데이터 커넥터 배포 스크립트를 실행하려면 다음 세부 정보가 필요합니다.

- [필수 조건](#prerequisites) 섹션에 나열된 Azure Sentinel 작업 영역 세부 정보.
- [필수 조건](#prerequisites) 섹션에 나열된 SAP 시스템 세부 정보.
- SUDO 권한이 있는 VM 사용자에 대한 액세스.
- [SAP 시스템 구성](#configure-your-sap-system)에서 만든, **/MSFTSEN/SENTINEL_CONNECTOR** 역할이 적용된 SAP 사용자.
- SAP 팀의 도움말.

**SAP 솔루션 배포 스크립트를 실행하려면** 다음을 수행합니다.

1. 다음 명령을 실행하여 SAP 솔루션을 VM에 배포합니다.

    ```azurecli
    wget -O sapcon-sentinel-kickstart.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-sentinel-kickstart.sh && bash ./sapcon-sentinel-kickstart.sh
    ```

1. 화면의 지침에 따라 SAP 및 Key Vault 세부 정보를 입력하고 배포를 완료합니다. 배포가 완료되면 확인 메시지가 나타납니다.

    ```azurecli
    The process has been successfully completed, thank you!
    ```

    Azure Sentinel은 초기화 시간 24시간 전까지 구성된 시간 범위에 대한 SAP 로그를 검색하기 시작합니다.

1. 시스템 로그를 검토하여 데이터 커넥터가 데이터를 전송하고 있는지 확인하는 것이 좋습니다. 다음을 실행합니다.

    ```bash
    docker logs -f sapcon-[SID]
    ```

## <a name="deploy-sap-security-content"></a>SAP 보안 콘텐츠 배포

Azure Sentinel **솔루션** 및 **관심 목록** 영역에서 [SAP 보안 콘텐츠](sap-solution-security-content.md)를 배포합니다.

**Azure Sentinel - SAP에 대한 지속적인 위협 모니터링** 솔루션을 통해 SAP 데이터 커텍터는 Azure Sentinel **데이터 커넥터** 영역에 표시할 수 있으며, **SAP - System Applications and Products**(SAP - 시스템 애플리케이션 및 제품) 통합 문서와 SAP 관련 분석 규칙을 배포합니다.

Azure Sentinel 작업 영역에 SAP 관련 관심 목록을 수동으로 추가합니다.

**SAP 솔루션 보안 콘텐츠를 배포하려면** 다음을 수행합니다.

1. Azure Sentinel 탐색 메뉴에서 **솔루션(미리 보기)** 을 선택합니다.

    **솔루션** 페이지에는 필터링되고 검색 가능한 솔루션 목록이 표시됩니다.

1. **Azure Sentinel - Continuous Threat Monitoring for SAP (preview)** (Azure Sentinel - SAP에 대한 지속적인 위협 모니터링(미리 보기))를 선택하여 SAP 솔루션 페이지를 엽니다.

    :::image type="content" source="media/sap/sap-solution.png" alt-text="Azure Sentinel - SAP에 대한 지속적인 위협 모니터링(미리 보기) 솔루션":::

1. **만들기** 를 선택하여 솔루션 배포 마법사를 시작하고 솔루션을 배포하려는 Azure 구독, 리소스 그룹 및 Log Analytics 작업 영역에 대한 세부 정보를 입력합니다.

1. **다음** 을 선택하여 **데이터 커넥터** **분석** 및 **통합 문서** 탭을 순환합니다. 여기에서 이 솔루션과 함께 배포되는 구성 요소에 대해 알아볼 수 있습니다.

    통합 문서의 기본 이름은 **SAP - 시스템 애플리케이션 및 제품 - 미리 보기** 입니다. 필요에 따라 통합 문서 탭에서 변경합니다.

    자세한 내용은 [Azure Sentinel SAP 솔루션: 보안 콘텐츠 참조(퍼블릭 미리 보기)](sap-solution-security-content.md)를 참조하세요.

1. **검토 + 만들기 탭** 에서 **유효성 검사 통과** 메시지가 표시될 때까지 기다린 후 **만들기** 를 선택하여 솔루션을 배포합니다.

    > [!TIP]
    > 링크에 대해 **템플릿 다운로드** 를 선택하여 솔루션을 코드로 배포할 수도 있습니다.

1. 배포가 완료되면 페이지의 오른쪽 위에 확인 메시지가 표시됩니다.

    새로 배포된 콘텐츠를 표시하려면 다음으로 이동합니다.

    - **위협 관리** > **통합 문서** > **내 통합 문서** 에서 [기본 제공 SAP 통합 문서](sap-solution-security-content.md#built-in-workbooks)를 찾습니다.
    - **구성** > **분석** 으로 이동하여 일련의 [SAP 관련 분석 규칙](sap-solution-security-content.md#built-in-analytics-rules)을 찾습니다.

1. 검색, 검색 규칙, 위협 헌팅 및 응답 플레이북에서 사용할 SAP 관련 관심 목록을 추가합니다. 이러한 관심 목록은 Azure Sentinel SAP 지속적인 위협 모니터링 솔루션에 대한 구성을 제공합니다.

    1. Azure Sentinel GitHub 리포지토리(https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists )에서 SAP 관심 목록을 다운로드합니다.

    1. Azure Sentinel **관심 목록** 영역에서 Azure Sentinel 작업 영역에 관심 목록을 추가합니다. 다운로드한 CSV 파일을 원본으로 사용하고 사용자 환경에 필요한 대로 사용자 지정합니다. 

        [ ![Azure Sentinel에 추가된 SAP 관련 관심 목록](media/sap/sap-watchlists.png) ](media/sap/sap-watchlists.png#lightbox)

        자세한 내용은 [Azure Sentinel 관심 목록 사용](watchlists.md) 및 [사용 가능한 SAP 관심 목록](sap-solution-security-content.md#available-watchlists)을 참조하세요.

1. Azure Sentinel에서 **SAP에 대한 Azure Sentinel 지속적인 위협 모니터링** 데이터 커넥터로 이동하여 연결을 확인합니다.

    [ ![SAP에 대한 Azure Sentinel 지속적인 위협 모니터링 데이터 커넥터 페이지](media/sap/sap-data-connector.png) ](media/sap/sap-data-connector.png#lightbox)

    SAP ABAP 로그는 Azure Sentinel **로그** 페이지의 **사용자 지정 로그** 에 표시됩니다.

    [ ![Azure Sentinel, 사용자 지정 로그 아래의 SAP ABAP 로그](media/sap/sap-logs-in-sentinel.png) ](media/sap/sap-logs-in-sentinel.png#lightbox)

    자세한 내용은 [Azure Sentinel SAP 솔루션 로그 참조(퍼블릭 미리 보기)](sap-solution-log-reference.md)를 참조하세요.


## <a name="update-your-sap-data-connector"></a>SAP 데이터 커넥터 업데이트

Docker 컨테이너가 이전 버전의 SAP 데이터 커넥터에서 이미 실행되고 있으면 SAP 데이터 커넥터 업데이트 스크립트를 실행하여 사용 가능한 최신 기능을 가져옵니다.

Azure Sentinel github 리포지토리에서 최신 버전의 관련 배포 스크립트를 사용해야 합니다. 

다음을 실행합니다.

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

머신의 SAP 데이터 커넥터 Docker 컨테이너가 업데이트됩니다. 

사용 가능한 다른 업데이트를 확인해야 합니다.

- [Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR)의 관련 SAP 변경 요청.
- **Azure Sentinel Continuous Threat Monitoring for SAP** 솔루션의 Azure Sentinel SAP 보안 콘텐츠
- [Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists)의 관련 관심 목록


## <a name="collect-sap-hana-audit-logs"></a>SAP HANA 감사 로그 수집

Syslog를 사용하여 SAP HANA 데이터베이스 감사 로그를 구성한 경우 Syslog 파일을 수집하도록 Log Analytics 에이전트도 구성해야 합니다.

1. [SAP 실행 패드 지원 사이트](https://launchpad.support.sap.com/#/notes/0002624117)에서 액세스할 수 있는 *SAP Note 0002624117* 에 설명된 대로 SAP HANA 감사 로그 내역이 Syslog를 사용하도록 구성되어 있는지 확인합니다. 자세한 내용은 다음을 참조하세요.

    - [SAP HANA 감사 내역-모범 사례](https://archive.sap.com/documents/docs/DOC-51098)
    - [감사에 대 한 권장 사항](https://help.sap.com/viewer/742945a940f240f4a2a0e39f93d3e2d4/2.0.05/en-US/5c34ecd355e44aa9af3b3e6de4bbf5c1.html)

1. 운영 체제 Syslog 파일에서 관련 HANA 데이터베이스 이벤트를 확인합니다.

1. 머신에 Log Analytics 에이전트를 설치하고 구성합니다.

    1. sudo 권한이 있는 .사용자로 HANA 데이터베이스 운영 체제에 로그인합니다.
    1. Azure Portal에서 Log Analytics 작업 영역으로 이동합니다. 왼쪽의 **설정** 에서 **에이전트 관리 > Linux 서버** 를 선택합니다.
    1. **Linux용 에이전트 다운로드 및 온보딩** 아래 상자에 표시된 코드를 터미널에 복사한 후 스크립트를 실행합니다.

    Log Analytics 에이전트가 머신에 설치되고 작업 영역에 연결됩니다. 자세한 내용은 Microsoft GitHub 리포지토리에서 [Linux 컴퓨터에 Log Analytics 에이전트 설치](../azure-monitor/agents/agent-linux.md) 및 [Linux용 OMS 에이전트](https://github.com/microsoft/OMS-Agent-for-Linux)를 참조하세요.

1. **에이전트 관리 > Linux 서버** 탭을 새로 고쳐 **1개의 Linux 컴퓨터가 연결** 되었는지 확인합니다.

1. 왼쪽의 **설정** 에서 **에이전트 구성** 을 선택하고 **Syslog** 탭을 선택합니다.

1. **기능 추가** 를 선택하여 수집하려는 기능을 추가합니다. 

    > [!TIP]
    > HANA 데이터베이스 이벤트가 저장되는 기능은 여러 다른 배포 간에 변경될 수 있으므로 모든 기능을 추가하고 Syslog 로그에서 확인한 다음, 관련이 없는 항목은 모두 제거하는 것이 좋습니다.
    >

1. Azure Sentinel에서 이제 HANA 데이터베이스 이벤트가 수집된 로그에 표시되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Sentinel SAP 솔루션에 대해 자세히 알아보세요.

- [SNC로 Azure Sentinel SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)
- [전문가 구성 옵션, 온-프레미스 배포, SAPControl 로그 원본](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 솔루션 자세한 SAP 요구 사항](sap-solution-detailed-requirements.md)
- [Azure Sentinel SAP 솔루션 로그 참조](sap-solution-log-reference.md)
- [Azure Sentinel SAP 솔루션: 기본 제공 보안 콘텐츠](sap-solution-security-content.md)
- [Azure Sentinel SAP 솔루션 배포 문제 해결](sap-deploy-troubleshoot.md)

자세한 내용은 [Azure Sentinel 솔루션](sentinel-solutions.md)을 참조하세요.
