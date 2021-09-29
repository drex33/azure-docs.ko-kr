---
title: '자습서: Azure Automation Runbooks를 사용하여 클러스터 만들기 - Azure HDInsight'
description: Azure Automation Runbook을 사용하여 클라우드에서 실행되는 스크립트로 Azure HDInsight 클러스터를 만들고 삭제하는 방법을 알아봅니다.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 12/27/2019
ms.openlocfilehash: d8091bd5e1bb95ff29d0be948a464f587a49f62f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819748"
---
# <a name="tutorial-create-azure-hdinsight-clusters-with-azure-automation"></a>자습서: Azure Automation으로 Azure HDInsight 클러스터 만들기

Azure Automation을 사용하여 클라우드에서 실행되고 요청 시 또는 일정에 따라 Azure 리소스를 관리하는 스크립트를 만들 수 있습니다. 이 문서에서는 PowerShell Runbook을 만들어 Azure HDInsight 클러스터를 만들고 삭제하는 방법을 설명합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * HDInsight와 상호 작용하는 데 필요한 모듈 설치
> * 클러스터를 만드는 동안 필요한 자격 증명을 만들고 저장
> * HDInsight 클러스터를 만드는 새 Azure Automation Runbook 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 기존 [Azure Automation 계정](../automation/quickstarts/create-account-portal.md)
* 기존 [Azure Storage 계정](../storage/common/storage-account-create.md). 클러스터 스토리지로 사용됩니다.

## <a name="install-hdinsight-modules"></a>HDInsight 모듈 설치

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Automation 계정을 선택합니다.
1. **공유 리소스** 에서 **모듈 갤러리** 를 선택합니다.
1. 상자에 **AzureRM.Profile** 을 입력하고 Enter 키를 눌러 검색합니다. 사용 가능한 검색 결과를 선택합니다.
1. **AzureRM.profile** 화면에서 **가져오기** 를 선택합니다. Azure 모듈 업데이트 확인란을 선택하고 **확인** 을 선택합니다.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermprofile-module.png" alt-text="AzureRM.profile 모듈 가져오기" border="false":::

1. **공유 리소스** 에서 **모듈 갤러리** 를 선택하여 모듈 갤러리로 돌아갑니다.
1. **HDInsight** 를 입력합니다. **AzureRM.HDInsight** 를 선택합니다.

    :::image type="content" source="./media/manage-clusters-runbooks/browse-modules-hdinsight.png" alt-text="HDInsight 모듈 찾아보기" border="true":::

1. **AzureRM.HDInsight** 패널에서 **가져오기** 를 선택하고 **확인** 을 선택합니다.

    :::image type="content" source="./media/manage-clusters-runbooks/import-azurermhdinsight-module.png" alt-text="AzureRM.HDInsight 모듈 가져오기" border="true":::

## <a name="create-credentials"></a>자격 증명 만들기

1. **공유 리소스** 에서 **자격 증명** 을 선택합니다.
1. **자격 증명 추가** 를 선택합니다.
1. **새 자격 증명** 패널에 필요한 정보를 입력합니다. 이 자격 증명은 클러스터 암호를 저장하는 데 사용 됩니다. 그러면 Ambari에 로그인할 수 있습니다.

    | 속성 | 값 |
    | --- | --- |
    | 이름 | `cluster-password` |
    | 사용자 이름 | `admin` |
    | 암호 | `SECURE_PASSWORD` |
    | 암호 확인 | `SECURE_PASSWORD` |

1. **만들기** 를 선택합니다.
1. 사용자 이름 `sshuser` 및 원하는 암호를 사용하여 새 자격 증명 `ssh-password`에도 동일한 프로세스를 반복합니다. **만들기** 를 선택합니다. 이 자격 증명은 클러스터의 SSH 암호를 저장하는 데 사용됩니다.

    :::image type="content" source="./media/manage-clusters-runbooks/create-credentials.png" alt-text="자격 증명 만들기" border="true":::

## <a name="create-a-runbook-to-create-a-cluster"></a>클러스터를 만드는 Runbook 만들기

1. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.
1. **Runbook 만들기** 를 선택합니다.
1. **Runbook 만들기** 패널에서 Runbook 이름을 입력합니다(예: `hdinsight-cluster-create`). **Runbook 유형** 드롭다운에서 **Powershell** 을 선택합니다.
1. **만들기** 를 선택합니다.

    :::image type="content" source="./media/manage-clusters-runbooks/create-runbook.png" alt-text="Runbook 만들기" border="true":::

1. 다음과 같이 **PowerShell Runbook 편집** 화면에서 다음 코드를 입력하고 **게시** 를 선택합니다.

    :::image type="content" source="./media/manage-clusters-runbooks/publish-runbook.png" alt-text="Runbook 게시" border="true":::

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $subscriptionID,
    
      [Parameter (Mandatory= $true)]
      [String] $resourceGroup,
    
      [Parameter (Mandatory= $true)]
      [String] $storageAccount,
    
      [Parameter (Mandatory= $true)]
      [String] $containerName,
    
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    # Set cluster variables
    $storageAccountKey = (Get-AzureRmStorageAccountKey –Name $storageAccount –ResourceGroupName $resourceGroup)[0].value 
    
    # Setting cluster credentials
    
    #Automation credential for Cluster Admin
    $clusterCreds = Get-AutomationPSCredential –Name 'cluster-password'
    
    #Automation credential for user to SSH into cluster
    $sshCreds = Get-AutomationPSCredential –Name 'ssh-password' 
    
    $clusterType = "Hadoop" #Use any supported cluster type (Hadoop, HBase, Storm, etc.)
    $clusterOS = "Linux"
    $clusterWorkerNodes = 3
    $clusterNodeSize = "Standard_D3_v2"
    $location = Get-AzureRmStorageAccount –StorageAccountName $storageAccount –ResourceGroupName $resourceGroup | %{$_.Location}
    
    ### Provision HDInsight cluster
    New-AzureRmHDInsightCluster –ClusterName $clusterName –ResourceGroupName $resourceGroup –Location $location –DefaultStorageAccountName "$storageAccount.blob.core.windows.net" –DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName –ClusterType $clusterType –OSType $clusterOS –Version “3.6” –HttpCredential $clusterCreds –SshCredential $sshCreds –ClusterSizeInNodes $clusterWorkerNodes –HeadNodeSize $clusterNodeSize –WorkerNodeSize $clusterNodeSize
    ```

## <a name="create-a-runbook-to-delete-a-cluster"></a>클러스터를 삭제하는 Runbook 만들기

1. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.
1. **Runbook 만들기** 를 선택합니다.
1. **Runbook 만들기** 패널에서 Runbook 이름을 입력합니다(예: `hdinsight-cluster-delete`). **Runbook 유형** 드롭다운에서 **Powershell** 을 선택합니다.
1. **만들기** 를 선택합니다.
1. 다음과 같이 **PowerShell Runbook 편집** 화면에서 다음 코드를 입력하고 **게시** 를 선택합니다.

    ```powershell
    Param
    (
      [Parameter (Mandatory= $true)]
      [String] $clusterName
    )
    
    ### Authenticate to Azure 
    $Conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    
    Remove-AzureRmHDInsightCluster -ClusterName $clusterName
    ```

## <a name="execute-runbooks"></a>Runbook 실행

### <a name="create-a-cluster"></a>클러스터 만들기

1. **프로세스 자동화** 에서 **Runbook** 을 선택하여 Automation 계정의 Runbook 목록을 봅니다.
1. `hdinsight-cluster-create`를 선택하거나 클러스터 생성 Runbook을 만들 때 사용한 이름을 선택합니다.
1. **시작** 을 선택하여 Runbook을 즉시 실행합니다. 주기적으로 실행되도록 Runbook을 예약할 수도 있습니다. [Azure Automation에서 Runbook 예약](../automation/shared-resources/schedules.md)을 참조하세요.
1. 스크립트에 필요한 매개 변수를 입력하고 **확인** 을 선택합니다. 그러면 **CLUSTERNAME** 매개 변수에서 지정한 이름을 사용하는 새 HDInsight 클러스터가 만들어집니다.

    :::image type="content" source="./media/manage-clusters-runbooks/execute-create-runbook.png" alt-text="클러스터 만들기 Runbook 실행" border="true":::

### <a name="delete-a-cluster"></a>클러스터 삭제

앞에서 만든 `hdinsight-cluster-delete` Runbook을 선택하여 클러스터를 삭제합니다. **시작** 을 선택하고, **CLUSTERNAME** 매개 변수를 입력하고, **확인** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

앞에서 만든 Azure Automation 계정이 더 이상 필요 없으면 요금이 발생하지 않도록 계정을 삭제합니다. 삭제하려면 Azure Portal로 이동하고, Azure Automation 계정을 만든 리소스 그룹을 선택하고, Automation 계정을 선택한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure PowerShell을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-powershell.md)