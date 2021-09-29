---
title: 연속 통합 자동화
description: Azure Pipelines 파이프라인 릴리스를 사용 하 여 Azure Data Factory에서 연속 통합을 자동화 하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c32b2e986e50fd2679b65b449eccdf5c7629a9a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220423"
---
# <a name="automate-continuous-integration-using-azure-pipelines-releases"></a>Azure Pipelines 릴리스를 사용 하 여 연속 통합 자동화

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

다음은 여러 환경에 Data Factory를 자동 배포하는 Azure Pipelines 릴리스를 설정하기 위한 가이드입니다.

## <a name="requirements"></a>요구 사항

-   Visual Studio Team Foundation Server에 연결된 Azure 구독 또는 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)를 사용하는 Azure Repos

-   Azure Repos Git 통합으로 구성된 Data Factory

-   각 환경에 대한 비밀이 포함된 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

## <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines 릴리스 설정

1.  [Azure DevOps](https://dev.azure.com/)에서 Data Factory로 구성된 프로젝트를 엽니다.

1.  페이지의 왼쪽에서 **파이프라인** 을 선택한 다음 **릴리스** 를 선택합니다.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image6.png" alt-text="파이프라인, 릴리스 선택":::

1.  **새 파이프라인** 을 선택하거나, 기존 파이프라인이 있는 경우에는 **새로 만들기** 를 선택한 다음 **새 릴리스 파이프라인** 을 선택합니다.

1.  **빈 작업** 템플릿을 선택합니다.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image13.png" alt-text="빈 작업 선택":::

1.  **단계 이름** 상자에 사용자 환경의 이름을 입력합니다.

1.  **아티팩트 추가** 를 선택한 다음 개발 Data Factory로 구성된 Git 리포지토리를 선택합니다. **기본 분기** 에 대한 리포지토리의 [게시 분기](source-control.md#configure-publishing-settings)를 선택합니다. 기본적으로 이 게시 분기는 `adf_publish`입니다. **기본 버전** 의 경우 **기본 분기에서 최신 버전** 을 선택합니다.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image7.png" alt-text="아티팩트 추가":::

1.  Azure Resource Manager 배포 작업을 추가합니다.

    a.  단계 보기에서 **단계 작업 보기** 를 선택합니다.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image14.png" alt-text="단계 보기":::

    b.  새 작업을 만듭니다. **ARM 템플릿 배포** 를 검색한 후 **추가** 를 선택합니다.

    다.  배포 작업에서 대상 Data Factory에 대한 구독, 리소스 그룹 및 위치를 선택합니다. 필요한 경우 자격 증명을 제공합니다.

    d.  **작업** 목록에서 **리소스 그룹 만들기 또는 업데이트** 를 선택합니다.

    e.  **템플릿** 상자 옆에 있는 줄임표 단추( **...** )를 선택합니다. 구성된 Git 리포지토리의 게시 분기에 생성된 Azure Resource Manager 템플릿을 찾습니다. adf_publish 분기의 &lt;FactoryName&gt; 폴더에서 `ARMTemplateForFactory.json` 파일을 찾습니다.

    f.  **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수** 상자 옆에서 매개 변수 파일을 선택합니다. adf_publish 분기의 &gt;FactoryName&lt; 폴더에서 `ARMTemplateParametersForFactory.json` 파일을 찾습니다.

    g.  **템플릿 매개 변수 재정의** 필드 **템플릿 매개 변수 재정의** 상자 옆에서 대상 Data Factory에 대해 원하는 매개 변수 값을 입력합니다. Azure Key Vault에서 제공하는 자격 증명의 경우 큰따옴표 사이에 비밀 이름을 입력합니다. 예를 들어 비밀 이름이 cred1인 경우 이 값에 대한 **"$(cred1)"** 을 입력합니다.

    h. **배포 모드** 에 대해 **증분** 을 선택합니다.

    > [!WARNING]
    > 전체 배포 모드에서는 리소스 그룹에 있지만 새 Resource Manager 템플릿에 지정되지 않은 리소스가 **삭제** 됩니다. 자세한 내용은 [Azure Resource Manager 배포 모드](../azure-resource-manager/templates/deployment-modes.md)를 참조하세요.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image9.png" alt-text="Data Factory Prod 배포":::

1.  릴리스 파이프라인을 저장합니다.

1. 릴리스를 트리거하려면 **릴리스 만들기** 를 선택합니다. 릴리스 만들기를 자동화하려면 [Azure DevOps 릴리스 트리거](/azure/devops/pipelines/release/triggers)를 참조하세요.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image10.png" alt-text="릴리스 만들기 선택":::

> [!IMPORTANT]
> CI/CD 시나리오에서는 서로 다른 환경에서의 IR(통합 런타임) 형식이 동일해야 합니다. 예를 들어 개발 환경에 자체 호스팅 IR이 있는 경우 테스트 및 프로덕션과 같은 다른 환경에서 동일한 IR이 자체 호스팅 유형이어야 합니다. 마찬가지로 여러 단계에서 통합 런타임을 공유하는 경우 개발, 테스트 및 프로덕션과 같은 모든 환경에서 통합 런타임을 연결된 자체 호스팅으로 구성해야 합니다.

## <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault에서 비밀을 가져옵니다.

Azure Resource Manager 템플릿에 전달할 비밀이 있는 경우 Azure Pipelines 릴리스에서 Azure Key Vault를 사용하는 것이 좋습니다.

비밀을 처리하는 두 가지 방법이 있습니다.

- 매개 변수 파일에 비밀을 추가합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)을 참조하세요.

    게시 분기에 업로드되는 매개 변수 파일의 복사본을 만듭니다. 다음 형식을 사용하여 Key Vault에서 가져오려는 매개 변수의 값을 설정합니다.

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    이 메서드를 사용하는 경우 키 자격 증명 모음에서 자동으로 암호를 끌어옵니다.

    매개 변수 파일은 게시 분기에 포함되어야 합니다.

- 이전 섹션에 설명된 Azure Resource Manager 배포 작업 전에 [Azure Key Vault 작업](/azure/devops/pipelines/tasks/deploy/azure-key-vault)을 추가합니다.

    1.  **작업** 탭에서 새 작업을 만듭니다. **Azure Key Vault** 를 검색하여 추가합니다.

    1.  Key Vault 작업에서 키 자격 증명 모음을 생성된 구독을 선택합니다. 필요한 경우 자격 증명을 제공하고 키 자격 증명 모음을 선택합니다.

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image8.png" alt-text="Key Vault 작업 추가":::

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines 에이전트에 권한 부여

올바른 권한이 설정되어 있지 않으면 액세스 거부 오류가 발생하여 Azure Key Vault 작업이 실패할 수 있습니다. 릴리스에 대한 로그를 다운로드하고, Azure Pipelines 에이전트에 권한을 부여하는 명령을 포함하는 .ps1 파일을 찾습니다. 명령을 직접 실행할 수 있습니다. 또는 파일에서 보안 주체 ID를 복사하고 Azure Portal에 액세스 정책을 수동으로 추가할 수 있습니다. `Get` 및 `List`는 필요한 최소 권한입니다.

## <a name="updating-active-triggers"></a>활성 트리거 업데이트

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-Az-ps)의 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

>[!WARNING]
>최신 버전의 PowerShell 및 Data Factory 모듈을 사용하지 않는 경우 명령을 실행하는 동안 역직렬화 오류가 발생할 수 있습니다. 
>

활성 트리거를 업데이트하려고 하면 배포에 실패할 수 있습니다. 활성 트리거를 업데이트하려면 수동으로 중단하고 배포 후에 시작해야 합니다. Azure PowerShell 작업을 사용하여 이 작업을 수행할 수 있습니다.

1.  릴리스의 **작업** 탭에서 **Azure PowerShell** 작업을 추가합니다. 최신 Azure PowerShell 버전의 작업 버전을 선택합니다. 

1.  팩터리가 있는 구독을 선택합니다.

1.  스크립트 형식으로 **스크립트 파일 경로** 를 선택합니다. 이를 위해서는 PowerShell 스크립트를 리포지토리에 저장해야 합니다. 다음 PowerShell 스크립트를 사용하여 트리거를 중지할 수 있습니다.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

`Start-AzDataFactoryV2Trigger` 함수로 유사한 단계를 완료하여 배포한 후에 트리거를 다시 시작할 수 있습니다.

데이터 팩터리 팀은 [샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)를 제공 했습니다. 

## <a name="next-steps"></a>다음 단계

- [연속 통합 및 지속적인 업데이트 개요](continuous-integration-delivery.md)
- [리소스 관리자 템플릿을 각 환경으로 수동으로 승격](continuous-integration-delivery-manual-promotion.md)
- [리소스 관리자 템플릿에서 사용자 지정 매개 변수 사용](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [연결된 Resource Manager 템플릿](continuous-integration-delivery-linked-templates.md)
- [핫픽스 프로덕션 환경 사용](continuous-integration-delivery-hotfix-environment.md)
- [샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)
