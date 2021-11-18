---
title: Azure의 SAP 배포 자동화 프레임워크 시작
description: Azure에서 SAP 배포 자동화 프레임워크를 빠르게 시작합니다. 샘플 매개 변수 파일을 사용하여 예제 구성을 배포합니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: how-to
ms.service: virtual-machines-sap
ms.openlocfilehash: c2ce939577d82dda2cbe8375bfaecbf6559ab145
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755187"
---
# <a name="get-started-with-sap-automation-framework-on-azure"></a>Azure에서 SAP 자동화 프레임워크 시작

[Azure에서 SAP 배포 자동화 프레임워크를](automation-deployment-framework.md)빠르게 시작합니다.

## <a name="prerequisites"></a>사전 요구 사항


- Azure 구독 Azure 구독이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure 환경에서 [SAP 소프트웨어를 다운로드합니다.](automation-software.md)
- [Terraform](https://www.terraform.io/) 설치. 자세한 내용은 [Azure의 Terraform 설명서도 참조하세요.](/azure/developer/terraform/)
- 로컬 컴퓨터에 [Azure Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli) 설치
- 필요에 따라 PowerShell을 사용하려는 경우:
    - 로컬 컴퓨터에 [Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module) 설치
    - 최신 PowerShell 모듈. 필요한 경우 [PowerShell 모듈을 업데이트합니다.](/powershell/azure/install-az-ps#update-the-azure-powershell-module)

일부 필수 구성 요소도 배포 환경에 이미 설치되어 있을 수 있습니다. Cloud Shell 및 배포자 모두 Terraform 및 Azure CLI 설치되어 있습니다.
## <a name="clone-the-repository"></a>리포지토리 복제

다음 단계를 사용하여 리포지토리를 복제하고 실행 환경을 준비합니다.

1. 자동화 프레임워크 배포를 위해 라는 디렉터리를 `Azure_SAP_Automated_Deployment` 만듭니다. 

# <a name="linux"></a>[Linux](#tab/linux)

```bash
mkdir ~/Azure_SAP_Automated_Deployment; cd $_
git clone https://github.com/Azure/sap-automation.git 
```

다음 단계를 사용하여 환경을 준비합니다.

```bash
export DEPLOYMENT_REPO_PATH=~/Azure_SAP_Automated_Deployment/sap-automation
export ARM_SUBSCRIPTION_ID=<subscriptionID>
```
> [!NOTE]
> 샘플 값을 `<subscriptionID>` 사용자 정보로 바꾸어야 합니다.

샘플 구성 파일을 복사하여 배포 자동화 프레임워크 테스트를 시작할 수 있습니다.

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-automation/deploy/samples/WORKSPACES WORKSPACES

```


# <a name="windows"></a>[Windows](#tab/windows)

```powershell
mkdir C:\Azure_SAP_Automated_Deployment
    
cd Azure_SAP_Automated_Deployment
    
git clone https://github.com/Azure/sap-automation.git 
```

PowerShell 모듈 가져오기

```powershell
Import-Module             C:\Azure_SAP_Automated_Deployment\sap-automation\deploy\scripts\pwsh\SAPDeploymentUtilities\Output\SAPDeploymentUtilities\SAPDeploymentUtilitiespsd1
```

---

> [!TIP]
> 배포자는 [이미 SAP 배포 자동화 프레임워크 리포지토리 를 복제합니다.](https://github.com/Azure/sap-automation) 

## <a name="copy-the-samples"></a>샘플 복사

샘플 구성 파일을 복사하여 배포 자동화 프레임워크 테스트를 시작할 수 있습니다.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
cd ~/Azure_SAP_Automated_Deployment

cp -R sap-automation/deploy/samples/WORKSPACES WORKSPACES
```
# <a name="windows"></a>[Windows](#tab/windows)

```powershell
cd C:\Azure_SAP_Automated_Deployment
mkdir WORKSPACES

xcopy sap-automation\deploy\samples\WORKSPACES WORKSPACES
```

---


## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [배포 계획](automation-plan-deployment.md)
