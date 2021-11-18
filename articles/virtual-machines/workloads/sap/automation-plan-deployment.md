---
title: Azure의 자동화 프레임 워크를 사용 하 여 SAP 배포 계획
description: Azure에서 SAP deployment automation 프레임 워크를 사용 하도록 준비 합니다. 단계에는 자격 증명 관리, DevOps 구조 및 배포 시나리오에 대 한 계획이 포함 됩니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: 883ec46df7228b72c37299548a4883ba794f6b74
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132760754"
---
# <a name="plan-your-deployment-of-sap-automation-framework"></a>SAP automation 프레임 워크 배포 계획

[Azure에서 SAP deployment automation 프레임 워크](automation-deployment-framework.md)를 실행 하는 데는 여러 가지 고려 사항이 있습니다. 여기에는 배포 자격 증명 관리, 가상 네트워크 디자인 등의 항목이 포함 됩니다.

Azure 디자인 고려 사항에 대 한 일반 SAP의 경우 [sap 도입 시나리오 소개를](/azure/cloud-adoption-framework/scenarios/sap) 참조 하세요.

> [!NOTE]
> Terraform 배포는 [SAP deployment automation framework 리포지토리에서](https://github.com/Azure/sap-automation/)Microsoft에서 제공 하는 terraform 템플릿을 사용 합니다. 템플릿은 시스템 관련 정보와 함께 매개 변수 파일을 사용 하 여 배포를 수행 합니다.

## <a name="credentials-management"></a>자격 증명 관리

자동화 프레임 워크는 배포에 Azure Active Directory (Azure AD) [서비스 주체](#service-principal-creation) 를 사용 합니다. 각 [작업 영역](#workload-zone-structure)에 대해 다른 배포 자격 증명을 사용할 수 있습니다. 프레임 워크는 Azure Key Vault의 [배포자](automation-deployment-framework.md#deployment-components) 키 자격 증명 모음에 이러한 자격 증명을 유지 합니다. 그런 다음 배포 프로세스 중에 프레임 워크가 동적으로 이러한 자격 증명을 검색 합니다.

또한 automation 프레임 워크는 VM을 만들 때 제공 된 기본 VM (가상 머신) 계정에 대 한 자격 증명을 사용 합니다. 이러한 자격 증명은 다음과 같습니다.

| 자격 증명 | 범위 | 스토리지 | ID | Description |
| ---------- | ----- | ------- | ---------- | ----------- |
| 로컬 사용자 | 배포자 | - | 현재 사용자 | 부트스트랩 배포자. |
| [서비스 주체](#service-principal-creation) | 환경 | 배포자의 주요 자격 증명 모음 | 환경 식별자 | 배포 작업을 수행 합니다. |
| VM 자격 증명 | 환경 | 워크 로드의 주요 자격 증명 모음 | 환경 식별자 | 기본 VM 사용자 정보를 설정 합니다. |

### <a name="service-principal-creation"></a>서비스 사용자 만들기

서비스 사용자 만들기:

1. 서비스 주체를 만들 수 있는 적절 한 권한이 있는 계정으로 [Azure 명령줄 인터페이스 (Azure CLI)](/cli/azure/) 에 로그인 합니다.
1. 명령을 실행 하 여 새 서비스 주체를 만듭니다 `az ad sp create-for-rbac` . 에 대 한 설명 이름을 사용 해야 `--name` 합니다. 다음은 그 예입니다.
    ```azurecli
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --name="DEV-Deployment-Account"
    ```
1. 출력을 확인 합니다. 다음 단계에는 응용 프로그램 식별자 ( `appId` ), 암호 () `password` 및 테 넌 트 식별자 ()가 필요 `tenant` 합니다. 다음은 그 예입니다.
    ```json
    {
        "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "displayName": "DEV-Deployment-Account",
        "name": "http://DEV-Deployment-Account",
        "password": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```
1. 사용자 액세스 관리자 역할을 서비스 사용자에 게 할당 합니다. 다음은 그 예입니다. 
    ```azurecli
    az role assignment create --assignee <your-application-ID> --role "User Access Administrator"
    ```

자세한 내용은 [서비스 주체 만들기에 대 한 Azure CLI 설명서](/cli/azure/create-an-azure-service-principal-azure-cli) 를 참조 하세요.

## <a name="devops-structure"></a>DevOps 구조체

Terraform 자동화 템플릿은 [SAP deployment automation framework 리포지토리에](https://github.com/Azure/sap-automation/)있습니다. 대부분의 사용 사례에서이 리포지토리를 읽기 전용으로 간주 하 고 Terraform 템플릿을 수정 하지 마세요.

사용자 고유의 매개 변수 파일의 경우 관리 하는 소스 제어 리포지토리에 이러한 파일을 보관 하는 것이 좋습니다. [SAP deployment automation framework 리포지토리](https://github.com/Azure/sap-automation/) 및 리포지토리를 동일한 루트 폴더에 복제 합니다. 그런 다음 [적절 한 폴더 구조를 만듭니다](#folder-structure).

> [!IMPORTANT]
> 매개 변수 파일의 이름은 Terraform 상태 파일의 이름이 됩니다. 이러한 이유로 고유한 매개 변수 파일 이름을 사용 해야 합니다.

### <a name="folder-structure"></a>폴더 구조

다음 샘플 폴더 계층 구조는 자동화 프레임 워크 파일과 함께 구성 파일을 구성 하는 방법을 보여 줍니다. **Sap automation** 이라는 첫 번째 최상위 폴더에는 대부분의 사용 사례에서 변경 하지 않아도 되는 자동화 프레임 워크 파일이 있습니다. **작업 영역** 이라는 두 번째 최상위 폴더에는 배포 설정에 대 한 구성 파일을 포함 하는 하위 폴더가 포함 되어 있습니다.

| 폴더 이름 | 콘텐츠 | Description |
| ----------- | -------- | ----------- |
| 없음 (루트 수준) | 구성 파일, 템플릿 파일 | 이 배포 환경에서 관리 하는 모든 시스템에 대 한 루트 폴더입니다. |
| CONFIGURATION | 공유 구성 파일 | 여러 위치의 사용자 지정 구성 파일을 참조 하는 공유 폴더입니다. 예를 들어 사용자 지정 디스크 크기 조정 구성 파일입니다. |
| 배포자 | 배포자 구성 파일 | 환경에서 관리 하는 모든 배포에 대 한 [배포자 구성 파일이](automation-configure-control-plane.md) 포함 된 폴더입니다. **환경-지역 Virtual Network** 의 명명 규칙에 따라 각 하위 폴더의 이름을 지정 합니다. 예: **WEEU-DEP00-INFRASTRUCTURE**. |
| LIBRARY | SAP 라이브러리의 구성 파일 | 환경에서 관리 하는 모든 배포에 대 한 [SAP 라이브러리 구성 파일이](automation-configure-control-plane.md) 포함 된 폴더입니다. **환경-지역 Virtual Network** 의 명명 규칙에 따라 각 하위 폴더의 이름을 지정 합니다. 예를 들어 **WEEU** 입니다. |
| 바꾸십시오 | 환경 배포에 대 한 구성 파일 | 환경에서 관리 하는 [모든 작업 영역에 대 한 구성 파일](automation-deploy-workload-zone.md) 을 포함 하는 폴더입니다. 명명 규칙 **환경-지역 Virtual Network** 에서 각 하위 폴더의 이름을 지정 합니다. 예: **WEEU-SAP00-INFRASTRUCTURE**. |
| SYSTEM | SAP 시스템에 대 한 구성 파일 | 환경에서 관리 하는 [모든 SAP 시스템 id (SID) 배포에 대 한 구성 파일](automation-configure-system.md) 을 포함 하는 폴더입니다. 명명 규칙 **환경-지역-Virtual Network-SID** 를 기준으로 각 하위 폴더의 이름을 지정 합니다. 예: **WEEU-SAPO00-ABC**. |

:::image type="content" source="./media/automation-plan-deployment/folder-structure.png" alt-text="SAP HANA 및 여러 작업 환경에 대 한 별도의 폴더를 보여 주는 예제 폴더 구조의 스크린샷":::

## <a name="supported-deployment-scenarios"></a>지원 되는 배포 시나리오

자동화 프레임 워크는 [새로운 시나리오와 기존 시나리오 모두에 대 한 배포](automation-new-vs-existing.md)를 지원 합니다.

## <a name="azure-regions"></a>Azure 지역

솔루션을 배포 하기 전에 사용할 Azure 지역을 고려 하는 것이 중요 합니다. 특정 시나리오에 따라 서로 다른 Azure 지역이 범위에 있을 수 있습니다.

자동화 프레임 워크는 여러 Azure 지역에 대 한 배포를 지원 합니다. 각 지역은 다음을 호스팅합니다.

- 배포 인프라
- 상태 파일 및 설치 미디어를 포함 하는 SAP 라이브러리
- 작업 영역 1 개
- 1-작업 영역에서 SAP 시스템 N 개

## <a name="deployment-environments"></a>배포 환경

한 지역에서 여러 워크로드 영역을 지원하는 경우 배포 환경 및 SAP 라이브러리에 대한 고유 식별자를 사용합니다. 작업 영역에 대한 식별자를 사용하지 마세요. 예를 들어 `MGMT` 관리 목적으로 를 사용합니다.

또한 자동화 프레임워크는 워크로드 영역과 별도의 구독에 배포 환경 및 SAP 라이브러리를 보유하는 것을 지원합니다.

배포 환경에서 제공하는 서비스는 다음과 같습니다.

- Terraform 배포 및 Ansible 구성을 수행하는 배포 VM.
- Terraform 배포에서 사용할 서비스 주체 ID 정보를 포함하는 키 자격 증명 모음입니다.
- 아웃바운드 인터넷 연결을 제공하는 Azure Firewall 구성 요소입니다.

배포 구성 파일은 지역, 환경 이름 및 가상 네트워크 정보를 정의합니다. 다음은 그 예입니다.

```json
{
    "infrastructure": {
        "environment": "MGMT",
        "region": "westeurope",
        "vnets": {
            "management": {
                "address_space": "0.0.0.0/25",
                "subnet_mgmt": {
                    "prefix": "0.0.0.0/28"
                },
                "subnet_fw": {
                    "prefix": "0.0.0.0/26"
                }
            }
        }
    },
    "options": {
        "enable_deployer_public_ip": true
    },
    "firewall_deployment": true
}
```

자세한 내용은 배포자 를 [구성하는 방법에 대한 자세한 설명을 참조하세요.](automation-configure-control-plane.md)

## <a name="sap-library-configuration"></a>SAP 라이브러리 구성

SAP 라이브러리는 SAP 설치 미디어, BOM(Bill of Material) 파일 및 Terraform 상태 파일에 대한 스토리지를 제공합니다. 구성 파일은 SAP 라이브러리의 지역 및 환경 이름을 정의합니다. 매개 변수 정보 및 예제는 [자동화를 위해 SAP 라이브러리를 구성하는 방법을 참조하세요.](automation-configure-control-plane.md)

## <a name="workload-zone-structure"></a>워크로드 영역 구조

대부분의 SAP 구성에는 서로 다른 애플리케이션 계층에 대한 여러 [워크로드 영역이](automation-deployment-framework.md#deployment-components) 있습니다. 예를 들어 개발, 품질 보증 및 프로덕션에 대한 워크로드 영역이 서로 다를 수 있습니다.

각 워크로드 영역에서 다음 서비스에 대한 액세스 권한을 만들거나 부여합니다.

* 가상 네트워크, 서브넷 및 네트워크 보안 그룹의 경우 Azure Virtual Network.
* 시스템 자격 증명 및 배포 서비스 주체에 대한 Azure Key Vault.
* 부팅 진단 및 클라우드 감시에 대한 Azure Storage 계정

워크로드 영역 레이아웃을 디자인하기 전에 다음 질문을 고려하세요.

* 시나리오에 필요한 워크로드 영역은 몇 개입니까?
* 워크로드를 배포해야 하는 지역은 무엇인가요?
* [배포 시나리오는](#supported-deployment-scenarios)무엇인가요?

자세한 내용은 [자동화를 위해 워크로드 영역 배포를 구성하는 방법을 참조하세요.](automation-deploy-workload-zone.md)

## <a name="sap-system-setup"></a>SAP 시스템 설정

SAP 시스템에는 SAP 애플리케이션을 호스트하는 데 필요한 모든 Azure 구성 요소가 포함되어 있습니다.

SAP 시스템을 구성하기 전에 다음 질문을 고려하세요.

* 어떤 데이터베이스 백 엔드를 사용하시겠습니까?
* 필요한 데이터베이스 서버는 몇 개입니까?
* 시나리오에 고가용성이 필요한가요?
* 필요한 애플리케이션 서버는 몇 개입니까?
* 필요한 웹 디스패처는 몇 개인가요? 
* 필요한 중앙 서비스 인스턴스는 몇 개입니까?
* 어떤 크기의 VM(가상 머신)이 필요한가요?
* 어떤 VM 이미지를 사용하시겠습니까? 이미지가 Azure Marketplace 또는 사용자 지정인가요?
* [새 배포 시나리오 또는 기존 배포 시나리오에 배포하고](#supported-deployment-scenarios)있나요?
* IP 할당 전략은 무엇인가요? Azure에서 IP를 설정하거나 사용자 지정 설정을 사용하시겠습니까?

자세한 내용은 [자동화를 위해 SAP 시스템을 구성하는 방법을 참조하세요.](automation-configure-system.md)

## <a name="deployment-flow"></a>배포 흐름

배포를 계획할 때 전체 흐름을 고려하는 것이 중요합니다. 자동화 프레임워크를 통해 Azure에 SAP를 배포하는 세 가지 주요 단계가 있습니다.

1. 지역 준비. 이 단계에서는 지정된 Azure 지역에서 SAP 자동화 프레임워크를 지원하는 구성 요소를 배포합니다. 이 단계의 일부는 다음과 같습니다.
    1. 배포 환경 만들기
    1. Terraform 상태 파일에 대한 공유 스토리지 만들기
    1. SAP 설치 미디어에 대한 공유 스토리지 만들기
1. 워크로드 영역 준비 이 단계에서는 가상 네트워크 및 키 자격 증명 모음과 같은 [워크로드 영역 구성 요소](#workload-zone-structure)를 배포합니다.
1. 시스템 배포. 이 단계에는 [SAP 시스템 에 대한 인프라가](#sap-system-setup)포함됩니다.

## <a name="orchestration-environment"></a>오케스트레이션 환경

자동화 프레임워크의 경우 지원되는 다음 환경 중 하나에서 템플릿 및 스크립트를 실행해야 합니다.

* Azure Cloud Shell
* Azure 호스팅 Linux VM
* 로컬 Windows 컴퓨터의 PowerShell

## <a name="naming-conventions"></a>명명 규칙

자동화 프레임워크는 기본 명명 규칙을 사용합니다. 사용자 지정 명명 규칙을 사용하려면 배포 전에 사용자 지정 이름을 계획하고 정의합니다.

## <a name="disk-sizing"></a>디스크 크기 조정

사용자 지정 [디스크 크기를 구성하려면](automation-configure-extra-disks.md)배포 전에 사용자 지정 설정을 계획해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자동화 프레임워크의 수동 배포 정보](automation-manual-deployment.md)
