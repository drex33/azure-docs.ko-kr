---
title: 자체 호스팅 Integration Runtime 만들기
titleSuffix: Azure Data Factory & Azure Synapse
description: 파이프라인이 프라이빗 네트워크의 데이터 스토리지에 액세스할 수 있도록 Azure Data Factory와 Azure Synapse Analytics에서 자체 호스팅 통합 런타임을 만드는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.date: 09/09/2021
ms.custom: devx-track-azurepowershell, synapse
ms.openlocfilehash: eff44b5ecdbce8485f0967bcaed73ab50c07556e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133363742"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기 및 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IR(통합 런타임)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory와 Synapse 파이프라인에서 사용하는 컴퓨팅 인프라입니다. IR에 대한 세부 정보는 [통합 런타임 개요](concepts-integration-runtime.md)를 참조하세요.

자체 호스팅 통합 런타임은 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 작업을 실행할 수 있습니다. 또한 온-프레미스 네트워크 또는 Azure 가상 네트워크의 컴퓨팅 리소스에 대해 변환 작업을 디스패치할 수 있습니다. 자체 호스팅 통합 런타임을 설치하려면 온-프레미스 머신이나 개인 네트워크 내부의 가상 머신이 필요합니다.  

이 문서에서는 자체 호스팅 IR을 만들고 구성하는 방법을 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="considerations-for-using-a-self-hosted-ir"></a>자체 호스팅 IR 사용을 위한 고려 사항

- 단일 자체 호스팅 통합 런타임을 여러 온-프레미스 데이터 원본에 사용할 수 있습니다. 동일한 Azure Active Directory(Azure AD) 테넌트 내에서 다른 데이터 팩터리 또는 Synapse 작업영역에 공유할 수도 있습니다. 자세한 내용은 [자체 호스팅 통합 런타임 공유](./create-shared-self-hosted-integration-runtime-powershell.md)를 참조하세요.
- 각 머신에는 자체 호스팅 통합 런타임 인스턴스를 하나씩만 설치할 수 있습니다. 온-프레미스 데이터 원본에 액세스해야 하는 데이터 팩터리 도는 Synapse 작업영역이 두 개가 있는 경우, [자체 호스팅 IR 공유 기능](./create-shared-self-hosted-integration-runtime-powershell.md)을 사용하여 자체 호스팅 IR을 공유하거나 각 데이터 팩터리에 대해 하나씩, 온-프레미스 컴퓨터 두 개에 자체 호스팅 IR을 설치합니다.  
- 자체 호스팅 통합 런타임이 데이터 원본과 같은 머신에 있을 필요는 없습니다. 그러나 자체 호스팅 통합 런타임이 데이터 원본에 가까우면, 데이터 원본에 연결하는 시간이 줄어듭니다. 온-프레미스 데이터 원본을 호스트하는 머신과 다른 머신에 자체 호스팅 통합 런타임을 설치하는 것이 좋습니다. 자체 호스팅 통합 런타임과 데이터 원본이 서로 다른 머신에 있으면, 자체 호스팅 통합 런타임과 데이터 원본 간에 리소스 경합이 발생하지 않습니다.
- 서로 다른 컴퓨터의 여러 자체 호스팅 통합 런타임이 동일한 온-프레미스 데이터 원본에 연결할 수 있습니다. 예를 들어, 두 개의 데이터 팩터리를 제공하는 자체 호스팅 통합 런타임이 두 개 있는 경우, 두 데이터 팩터리에 동일한 온-프레미스 데이터 원본을 등록할 수 있습니다.
- 자체 호스팅 통합 런타임을 사용하여 Azure 가상 네트워크 내에서 데이터 통합을 지원합니다.
- Azure ExpressRoute를 사용하더라도 데이터 원본은 방화벽으로 보호되는 온-프레미스 데이터 원본으로 취급해야 합니다. 자체 호스팅 통합 런타임을 사용하여 서비스를 데이터 원본에 연결합니다.
- Azure Infrastructure as a Service(IaaS) 가상 머신의 클라우드에 데이터 저장소가 있더라도 자체 호스팅 통합 런타임을 사용합니다.
- FIPS 규격 암호화를 사용하는 Windows 서버에 설치한 자체 호스팅 통합 런타임에서는 작업이 실패할 수 있습니다. 이 문제를 해결하려면 Azure Key Vault에 자격 증명/비밀 값 저장하기 또는 서버에서 FIPS 규격 암호화 사용하지 않기 등 두 가지 옵션이 있습니다. FIPS 규격 암호화를 사용하지 않도록 설정하려면 `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` 레지스트리 하위 키의 값을 1(사용)에서 0(사용 안 함)으로 변경합니다. [자체 호스팅 통합 런타임을 SSIS 통합 런타임을 위한 프록시](./self-hosted-integration-runtime-proxy-ssis.md)로 사용하는 경우 FIPS 규격 암호화를 사용하도록 설정할 수 있습니다. FIPS 규격 암호화는 온-프레미스에서 Azure Blob Storage 준비 영역으로 데이터를 이동할 때 사용됩니다.

## <a name="command-flow-and-data-flow"></a>명령 흐름 및 데이터 흐름

온-프레미스와 클라우드 간에 데이터를 이동하는 경우 이 작업은 자체 호스팅 통합 런타임을 사용하여 온-프레미스 데이터 원본과 클라우드 간에 데이터를 전송합니다.

다음은 자체 호스팅 IR로 복사 시 데이터 흐름 단계를 대략적으로 요약한 것입니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/high-level-overview.png" alt-text="데이터 흐름에 대한 대략적인 개요":::

1. 데이터 개발자는 먼저 Azure Portal 또는 PowerShell cmdlet을 사용하여 Azure Data Factory 또는 Synapse 작업 영역 내에서 자체 호스팅 통합 런타임을 생성합니다.  그런 다음, 데이터 개발자는 온-프레미스 데이터 저장소에 대한 연결된 서비스를 만들어 서비스가 데이터 저장소에 연결하는 데 사용해야 하는 자체 호스팅 통합 런타임 인스턴스를 지정합니다.

2. 자체 호스팅 통합 런타임 노드가 Windows DPAPI(데이터 보호 응용 프로그래밍 인터페이스)를 사용하여 자격 증명을 암호화하고 로컬에 저장합니다. 고가용성을 위해 여러 노드가 설정된 경우 자격 증명이 다른 노드 간에 동기화됩니다. 각 노드는 DPAPI를 사용하여 자격 증명을 암호화하고 로컬에 저장합니다. 자격 증명 동기화는 데이터 개발자에게는 표시되지 않으며, 자체 호스팅 IR에서 처리됩니다.

3. Azure Data Factory와 Synapse 파이프라인은 자체 호스팅 통합 런타임과 통신하여 작업을 예약하고 관리합니다. 통신은 공유된 [Azure Relay](../azure-relay/relay-what-is-it.md#wcf-relay) 연결을 사용하는 컨트롤 채널을 통해 전달됩니다. 작업이 실행되어야 할 경우, 서비스는 자격 증명 정보와 함께 요청을 큐에 보관합니다. 이는 자격 증명이 자체 호스팅 통합 런타임에 아직 저장되지 않은 경우에 발생합니다. 자체 호스팅 통합 런타임은 큐를 폴링한 후 작업을 시작합니다.

4. 자체 호스팅 통합 런타임은 온-프레미스 저장소와 클라우드 스토리지 간에 데이터를 복사합니다. 복사 방향은 데이터 파이프라인에서 복사 작업을 구성하는 방법에 따라 달라집니다. 이 단계에서 자체 호스팅 통합 런타임은 안전한 HTTPS 채널을 통해 Azure Blob Storage 등의 클라우드 기반 스토리지 서비스와 직접 통신합니다.

## <a name="prerequisites"></a>필수 조건

- 지원되는 Windows 버전은 다음과 같습니다.
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

도메인 컨트롤러에는 자체 호스팅 통합 런타임을 설치할 수 없습니다.

- 자체 호스팅 통합 런타임을 사용하려면 .NET Framework 4.7.2 이상인 64비트 운영 체제가 필요합니다. 자세한 내용은 [.NET Framework 시스템 요구 사항](/dotnet/framework/get-started/system-requirements)을 참조하세요.
- 자체 호스팅 통합 런타임 머신에 권장되는 최소 구성은 코어 4개, 8GB RAM 및 80GB 여유가 있는 하드 드라이브 공간을 포함하는 2GHz 프로세서입니다. 시스템 요구 사항에 대한 세부 정보는 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)를 참조하세요.
- 호스트 머신이 최대 절전 모드로 전환된 경우, 자체 호스팅 통합 런타임이 데이터 요청에 응답하지 않습니다. 따라서 자체 호스팅 통합 런타임을 설치하기 전에 컴퓨터에서 전원 관리 옵션을 적절하게 구성하세요. 머신이 최대 절전 모드로 전환된 경우, 자체 호스팅 통합 런타임 설치 관리자가 메시지를 표시합니다.
- 자체 호스팅 통합 런타임을 성공적으로 설치 및 구성하기 위해서는 머신의 관리자여야 합니다.
- 복사 작업 실행은 특정 빈도로 발생합니다. 머신의 프로세서 및 RAM 사용량은 최대 및 유휴 시간과 동일한 패턴을 따릅니다. 리소스 사용률은 대부분 이동하는 데이터 양에 따라 달라집니다. 여러 복사 작업이 진행 중인 경우 사용량이 많은 시간 동안 리소스 사용량이 증가하는 것을 볼 수 있습니다.
- Parquet, ORC 또는 Avro 형식의 데이터를 추출하는 동안 작업이 실패할 수 있습니다. Parquet에 대한 자세한 내용은 [Azure Data Factory Parquet 형식](./format-parquet.md#using-self-hosted-integration-runtime)을 참조하세요. 파일 생성은 자체 호스팅 통합 머신에서 실행됩니다. 정상적으로 작동하기 위해 파일을 만들려면 다음 필수 구성 요소가 필요합니다.
  - [Visual C++ 2010 재배포 가능](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) 패키지(x64)
  - JRE 공급자(예: [Adopt OpenJDK](https://adoptopenjdk.net/))의 Java Runtime(JRE) 버전 8 JAVA_HOME 환경 변수가 JRE 폴더가 아니라 JDK 폴더로 설정 되어 있는지 확인 합니다.
  >[!NOTE]
  >[Parquet format] 설명서 (Parquet # using-runtime-runtime)에 설명 된 대로 메모리 오류가 발생 하는 경우 Java 설정을 조정 해야 할 수 있습니다.
  

>[!NOTE]
>정부 클라우드에서 실행하는 경우, [정부 클라우드에 연결](../azure-government/documentation-government-get-started-connect-with-ps.md)을 검토하세요.

## <a name="setting-up-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 설정

자체 호스팅 통합 런타임을 만들고 설정하려면 다음 프로시저를 따르세요.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Azure PowerShell을 사용하여 자체 호스팅 IR 만들기

1. 작업에 Azure PowerShell를 사용할 수 있습니다. 예를 들면 다음과 같습니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. 로컬 컴퓨터에서 자체 호스팅 통합 런타임을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)하여 설치합니다.

3. 인증 키를 검색한 다음 해당 키를 사용하여 자체 호스팅 통합 런타임을 등록합니다. 다음은 PowerShell 예제입니다.

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```
> [!NOTE]
> Azure Government에서 PowerShell 명령을 실행합니다. [PowerShell을 사용하여 Azure Government에 연결](../azure-government/documentation-government-get-started-connect-with-ps.md)을 참조하세요.

### <a name="create-a-self-hosted-ir-via-ui"></a>UI를 통해 자체 호스팅 IR 만들기

다음 단계에 따라 Azure Data Factory 또는 Azure Synapse UI를 사용하여 자체 호스팅 IR을 생성합니다.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1. Azure Data Factory UI의 홈페이지에서 맨 왼쪽 창에 있는 [관리 탭](./author-management-hub.md)을 선택합니다.

   :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="홈 페이지 관리 단추":::

1. 왼쪽 창에서 **통합 런타임** 을 선택한 다음, **+새로 만들기** 를 선택합니다.

   :::image type="content" source="media/doc-common-process/manage-new-integration-runtime.png" alt-text="Integration Runtime 만들기":::

1. **통합 런타입 설치** 페이지에서 **Azure, 자체 호스트** 를 차례로 선택하고 **계속** 을 선택합니다.

1. 다음 페이지에서 **자체 호스팅** 을 선택하여 자체 호스팅 IR을 만든 다음, **계속** 을 선택합니다.
   :::image type="content" source="media/create-self-hosted-integration-runtime/new-self-hosted-integration-runtime.png" alt-text="자체 호스팅 IR 생성":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

1. Azure Synapse UI의 홈페이지에서 맨 왼쪽 창에 있는 관리 탭을 선택합니다.

   :::image type="content" source="media/doc-common-process/get-started-page-manage-button-synapse.png" alt-text="홈 페이지 관리 단추":::

1. 왼쪽 창에서 **통합 런타임** 을 선택한 다음, **+새로 만들기** 를 선택합니다.

   :::image type="content" source="media/doc-common-process/manage-new-integration-runtime-synapse.png" alt-text="Integration Runtime 만들기":::

1. 다음 페이지에서 **자체 호스팅** 을 선택하여 자체 호스팅 IR을 만든 다음, **계속** 을 선택합니다.
   :::image type="content" source="media/create-self-hosted-integration-runtime/new-self-hosted-integration-runtime-synapse.png" alt-text="자체 호스팅 IR 생성":::

---

### <a name="configure-a-self-hosted-ir-via-ui"></a>UI를 통해 자체 호스팅 IR 구성

1. IR의 이름을 입력하고 **생성** 을 선택합니다.

1. **통합 런타임 설치** 페이지에서 **옵션 1** 아래의 링크를 선택하여 컴퓨터에서 기본 설치를 엽니다. 또는 **옵션 2** 의 단계에 따라 수동으로 설정합니다. 다음 지침은 수동 설치를 기준으로 합니다.

   :::image type="content" source="media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png" alt-text="통합 런타임 설정":::

    1. 인증 키를 복사하여 붙여넣습니다. **통합 런타임 다운로드 및 설치** 를 선택합니다.

    1. 로컬 Windows 컴퓨터에 자체 호스팅된 통합 런타임을 다운로드합니다. 설치 관리자를 실행합니다.

    1. **통합 런타임(자체 호스팅) 등록** 페이지에서 이전에 저장한 키를 붙여넣고 **등록** 을 선택합니다.

       :::image type="content" source="media/create-self-hosted-integration-runtime/register-integration-runtime.png" alt-text="통합 런타임 등록":::

    1. **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침** 을 선택합니다.

1. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 창이 표시됩니다.

    :::image type="content" source="media/create-self-hosted-integration-runtime/registered-successfully.png" alt-text="성공적인 등록":::

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure VM에 자체 호스팅 IR 설정

[자체 호스트 IR 템플릿 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vms-with-selfhost-integration-runtime)를 사용하여 Azure 가상 머신에서 자체 호스팅 IR 설정을 자동화할 수 있습니다. 이 템플릿은 Azure 가상 네트워크 내에서 완전히 작동하는 자체 호스팅 IR을 제공하는 쉬운 방법을 제공합니다. IR은 노드 수를 2 이상으로 설정하는 경우 고가용성 및 확장성 기능을 제공합니다.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>로컬 PowerShell을 통해 기존 자체 호스팅 IR 설정하기

명령줄을 사용하여 기존 자체 호스팅 IR을 설정하거나 관리할 수 있습니다. 이는 특히 자체 호스팅 IR 노드의 설치 및 등록을 자동화하는 데 도움이 될 수 있습니다.

Dmgcmd.exe은 자체 호스팅 설치 관리자에 포함되어 있습니다. 일반적으로 C:\Program Files\Microsoft Integration Runtime\4.0\Shared\ folder에 있습니다. 이 애플리케이션은 다양한 매개 변수를 지원하며, 자동화를 위해 일괄 처리 스크립트를 사용하여 명령줄을 통해 호출할 수 있습니다.

다음과 같이 애플리케이션을 실행합니다.

```powershell
dmgcmd ACTION args...
```

애플리케이션의 작업 및 인수에 대한 세부 정보: 

|ACTION|args|설명|
|------|----|-----------|
|`-rn`,<br/>`-RegisterNewNode`|"`<AuthenticationKey>`" ["`<NodeName>`"]|지정된 인증 키와 노드 이름을 사용하여 자체 호스팅 통합 런타임 노드를 등록합니다.|
|`-era`,<br/>`-EnableRemoteAccess`|"`<port>`" ["`<thumbprint>`"]|현재 노드에서 원격 액세스를 사용하도록 설정하여 고가용성 클러스터를 설정합니다. 또는 Azure Data Factory 또는 Azure Synapse 작업 영역을 거치지 않고 자체 호스팅 IR에 대해 직접 자격 증명 설정을 사용할 수 있습니다. 후자는 동일한 네트워크에 있는 원격 머신에서 **New-AzDataFactoryV2LinkedServiceEncryptedCredential** cmdlet을 사용하여 수행합니다.|
|`-erac`,<br/>`-EnableRemoteAccessInContainer`|"`<port>`" ["`<thumbprint>`"]|노드가 컨테이너에서 실행될 때 현재 노드에 대한 원격 액세스를 사용하도록 설정합니다.|
|`-dra`,<br/>`-DisableRemoteAccess`||현재 노드에 대한 원격 액세스를 사용하지 않도록 설정합니다. 다중 노드 설정에는 원격 액세스가 필요합니다. **New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell cmdlet은 원격 액세스를 사용하지 않도록 설정된 경우에도 계속 작동합니다. 이 동작은 cmdlet이 자체 호스팅 IR 노드와 동일한 머신에서 실행되는 경우에만 적용됩니다.|
|`-k`,<br/>`-Key`|"`<AuthenticationKey>`"|이전 인증 키를 덮어쓰거나 업데이트합니다. 이 작업에 주의하세요. 이전 자체 호스팅 IR 노드는 키가 새 통합 런타임인 경우 오프라인으로 전환할 수 있습니다.|
|`-gbf`,<br/>`-GenerateBackupFile`|"`<filePath>`" "`<password>`"|현재 노드에 대한 백업 파일을 생성합니다. 백업 파일에는 노드 키와 데이터 저장소 자격 증명이 포함됩니다.|
|`-ibf`,<br/>`-ImportBackupFile`|"`<filePath>`" "`<password>`"|백업 파일에서 노드 복원|
|`-r`,<br/>`-Restart`||자체 호스팅 통합 런타임 호스트 서비스를 다시 시작합니다.|
|`-s`,<br/>`-Start`||자체 호스팅 통합 런타임 호스트 서비스를 시작합니다.|
|`-t`,<br/>`-Stop`||자체 호스팅 통합 런타임 호스트 서비스를 중지합니다.|
|`-sus`,<br/>`-StartUpgradeService`||자체 호스팅 통합 런타임 업그레이드 서비스를 시작합니다.|
|`-tus`,<br/>`-StopUpgradeService`||자체 호스팅 통합 런타임 업그레이드 서비스를 중지합니다.|
|`-tonau`,<br/>`-TurnOnAutoUpdate`||자체 호스팅 통합 런타임 자동 업데이트를 설정합니다.|
|`-toffau`,<br/>`-TurnOffAutoUpdate`||자체 호스팅 통합 런타임 자동 업데이트를 해제합니다.|
|`-ssa`,<br/>`-SwitchServiceAccount`|"`<domain\user>`" ["`<password>`"]|새 계정으로 실행되도록 DIAHostService를 설정합니다. 시스템 계정 및 가상 계정에는 빈 암호 “”를 사용합니다.|

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Microsoft Download Center에서 자체 호스팅 IR 설치 및 등록

1. [Microsoft 통합 런타임 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=39717)로 이동합니다.
2. **다운로드** 를 선택하고, 64비트 버전을 선택한 후 **다음** 을 선택합니다. 32 비트 버전은 지원되지 않습니다.
3. MSI 파일을 직접 실행하거나 하드 드라이브에 저장한 후에 실행합니다.
4. **시작** 페이지에서 언어를 선택하고, **다음** 을 선택합니다.
5. Microsoft 소프트웨어 사용 조건에 동의하고 **다음** 을 선택합니다.
6. 자체 호스팅 통합 런타임을 설치할 **폴더** 를 선택하고 **다음** 을 선택합니다.
7. **설치 준비 완료** 페이지에서 **설치** 를 선택합니다.
8. **마침** 을 선택하여 설치를 완료합니다.
9. PowerShell을 사용하여 인증 키를 가져옵니다. 인증 키 검색을 위한 PowerShell 예제는 다음과 같습니다.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

10. 사용자의 머신에서 실행되는 Microsoft 통합 런타임 구성 관리자의 **통합 런타임(자체 호스팅) 등록** 창에서 다음 단계를 수행합니다.

    1. 텍스트 영역에 인증 키를 붙여넣습니다.

    2. 필요에 따라 **인증 키 표시** 를 선택하여 키 텍스트를 확인합니다.

    3. **등록** 을 선택합니다.

> [!NOTE]
> 릴리스 정보는 동일한 [Microsoft Integration Runtime 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=39717)에서 사용할 수 있습니다.

## <a name="service-account-for-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 서비스 계정

자체 호스팅 통합 런타임의 기본 로그온 서비스 계정은 **NT SERVICE\DIAHostService** 입니다. **서비스 -> 통합 런타임 서비스 -> 속성 -> 로그온** 에서 볼 수 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-service-account.png" alt-text="자체 호스팅 통합 런타임 서비스 계정":::

계정에 서비스로 로그온 권한이 있는지 확인합니다. 그렇지 않으면, 자체 호스팅 통합 런타임을 성공적으로 시작할 수 없습니다. **로컬 보안 정책 -> 보안 설정 -> 로컬 정책 -> 사용자 권한 할당 -> 서비스로 로그온** 에서 권한이 있는지 확인할 수 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-service-account-permission.png" alt-text="로컬 보안 정책 - 사용자 권한 할당의 스크린샷":::

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-service-account-permission-2.png" alt-text="서비스로 로그온 사용자 권한 할당의 스크린샷":::

## <a name="notification-area-icons-and-notifications"></a>알림 영역 아이콘 및 알림

알림 영역에서 아이콘이나 메시지 위로 커서를 이동하면 자체 호스팅 통합 런타임의 상태에 대한 세부 정보를 볼 수 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/system-tray-notifications.png" alt-text="알림 영역의 알림":::

## <a name="high-availability-and-scalability"></a>고가용성 및 확장성

자체 호스팅 통합 런타임을 다수의 온-프레미스 머신 또는 Azure의 가상 머신과 연결할 수 있습니다. 이러한 컴퓨터를 노드라고 합니다. 최대 4개의 노드를 자체 호스팅 통합 런타임에 연결할 수 있습니다. 논리 게이트웨이에 게이트웨이가 설치된 온-프레미스 머신에서 다수의 노드를 사용할 경우 다음 이점을 얻을 수 있습니다.

- 자체 호스팅 통합 런타임의 고가용성 덕분에 빅 데이터 솔루션 또는 클라우드 데이터 통합에서 더 이상 단일 실패 지점이 발생하지 않습니다. 이 가용성은 최대 4 개의 노드를 사용하는 경우 연속성을 보장하는 데 도움이 됩니다.
- 온-프레미스 및 클라우드 데이터 저장소 간의 데이터 이동 성능 및 처리량을 향상시킵니다. 자세한 내용은 [성능 비교](copy-activity-performance.md)를 참조하세요.

[다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 통합 런타임 소프트웨어를 설치하여 여러 노드를 연결할 수 있습니다. 그런 다음 [자습서](tutorial-hybrid-copy-powershell.md)의 설명에 따라 **New-AzDataFactoryV2IntegrationRuntimeKey** cmdlet에서 가져온 인증 키 중 하나를 사용하여 등록합니다.

> [!NOTE]
> 각 노드에 연결하기 위해 자체 호스팅 통합 런타임을 새로 만들 필요는 없습니다. 자체 호스팅 통합 런타임을 다른 컴퓨터에서 설치하고 동일한 인증 키를 사용하여 등록할 수 있습니다.

> [!NOTE]
> 고가용성과 확장성을 위해 다른 노드를 추가하기 전에 첫 번째 노드에서 **인트라넷에 원격 액세스** 옵션이 사용하도록 설정되어 있는지 확인합니다. 이렇게 하려면 **Microsoft Integration Runtime Configuration Manager**  >  **설정**  >  **인트라넷에 원격 액세스** 를 선택합니다.

### <a name="scale-considerations"></a>크기 조정 고려 사항

#### <a name="scale-out"></a>확장

프로세서 사용량이 많고 자체 호스팅 IR에서 사용 가능한 메모리가 부족한 경우 머신 간에 로드를 스케일 아웃하는 데 도움이 되는 새 노드를 추가합니다. 시간이 초과되거나 자체 호스팅 IR 노드가 오프라인 상태여서 작업이 실패하는 경우 게이트웨이에 노드를 추가하면 도움이 됩니다.

#### <a name="scale-up"></a>강화

프로세서 및 사용 가능한 RAM이 잘 활용되지 않는데도 동시 작업 실행이 노드의 제한에 도달하면 노드가 실행할 수 있는 동시 작업 수를 스케일 업합니다. 또한 자체 호스팅 IR 오버로드로 인해 활동 시간이 초과되는 경우에도 스케일 업할 수 있습니다. 다음 이미지와 같이 노드의 최대 용량을 늘릴 수 있습니다.  

:::image type="content" source="media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png" alt-text="노드에서 실행할 수 있는 동시 작업 수 늘리기":::

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항

통합 런타임 노드 간의 통신을 보호하는 데 사용하는 TLS/SSL 인증서에 대한 요구 사항은 다음과 같습니다.

- 인증서는 공개적으로 신뢰할 수 있는 X509 v3 인증서여야 합니다. 퍼블릭 파트너 CA(인증 기관)에서 발급한 인증서를 사용하는 것이 좋습니다.
- 각 통합 런타임 노드는 이 인증서를 신뢰해야 합니다.
- 마지막 SAN 항목만 사용되므로 SAN(주체 대체 이름) 인증서를 사용하지 않는 것이 좋습니다. 다른 모든 SAN 항목은 무시됩니다. 해당 SAN이 **node1.domain.contoso.com** 및 **node2.domain.contoso.com** 인 SAN 인증서는 FQDN(정규화된 도메인 이름)이 **node2.domain.contoso.com** 인 머신에서만 사용할 수 있습니다.
- Windows Server 2012 R2에서 지원하는 TLS/SSL 인증서의 키 크기는 모두 지원됩니다.
- CNG 키를 사용하는 인증서는 지원하지 않습니다.  

> [!NOTE]
> 이 인증서는 다음 경우에 사용됩니다.
>
> - 자체 호스팅 IR 노드의 포트를 암호화합니다.
> - 상태 동기화에 대한 노드 간 통신의 경우 노드 간 연결된 서비스의 자격 증명 동기화를 포함합니다.
> - PowerShell cmdlet이 로컬 네트워크 내에서 연결된 서비스 자격 증명 설정에 사용되는 경우
>
> 프라이빗 네트워크 환경이 안전하지 않거나 프라이빗 네트워크 내에서 노드 간의 통신을 보호하려는 경우에 이 인증서를 사용하는 것이 좋습니다.
>
> 자체 호스팅 IR에서 다른 데이터 저장소로 전송되는 데이터 이동은 이 인증서의 설정 여부와 관계없이 항상 암호화된 채널 내에서 이루어집니다.

### <a name="credential-sync"></a>자격 증명 동기화
자격 증명 또는 비밀 값을 Azure Key Vault 저장하지 않으면 자격 증명 또는 암호 값이 자체 호스팅 통합 런타임이 있는 컴퓨터에 저장됩니다. 각 노드에는 특정 버전의 자격 증명 복사본이 있습니다. 모든 노드가 함께 작동하도록 하려면, 모든 노드에 대해 버전 번호가 동일해야 합니다. 

## <a name="proxy-server-considerations"></a>프록시 서버 고려 사항

회사 네트워크 환경에서 프록시 서버를 사용하여 인터넷에 액세스하는 경우 자체 호스팅 통합 런타임이 적절한 프록시 설정을 사용하도록 구성합니다. 초기 등록 단계에서 프록시를 설정할 수 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/specify-proxy.png" alt-text="프록시 서버 지정":::

구성된 경우 자체 호스팅 통합 런타임은 프록시 서버를 사용하여 클라우드 서비스의 원본 및 대상(HTTP 또는 HTTPS 프로토콜 사용)에 연결합니다. 초기 설치 시 **변경 링크** 를 선택해야 하는 이유입니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/set-http-proxy.png" alt-text="프록시 설정":::

이 대화 상자에는 세 가지 구성 옵션이 있습니다.

- **프록시 사용 안 함**: 자체 호스팅 통합 런타임을 클라우드 서비스에 연결하는 데는 프록시를 명시적으로 사용하지 않습니다.
- **시스템 프록시 사용**: 자체 호스팅 통합 런타임은 diahost.exe.config 및 diawp.exe.config에 구성된 프록시 설정을 사용합니다. 해당 파일에 프록시 구성이 지정되어 있지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결합니다.
- **사용자 지정 프록시 사용**: diahost.exe.config 및 diawp.exe.config의 구성을 사용하는 대신 자체 호스팅 통합 런타임에 사용할 HTTP 프록시 설정을 구성합니다. **주소** 와 **포트** 값은 필수입니다. **사용자 이름** 과 **암호** 는 프록시 인증 설정에 따른 선택 사항입니다. 모든 설정은 자체 호스팅 통합 런타임d에서 Windows DPAPI를 사용하여 암호화되며 컴퓨터에 로컬로 저장됩니다.

업데이트된 프록시 설정을 저장하면, 통합 런타임 호스트 서비스가 자동으로 다시 시작됩니다.

자체 호스팅 통합 런타임을 등록한 후 프록시 설정을 확인하거나 업데이트하려면 Microsoft 통합 런타임 구성 관리자를 사용합니다.

1. **Microsoft 통합 런타임 구성 관리자** 를 엽니다.
1. **설정** 탭을 선택합니다.
1. **HTTP 프록시** 에서 **변경** 링크를 선택하여 **HTTP 프록시 설정** 대화 상자를 엽니다.
1. **다음** 을 선택합니다. 그러면 프록시 설정이 저장되고 통합 런타임 호스트 서비스를 다시 시작하기 위한 권한이 필요하다는 경고가 표시됩니다.

구성 관리자 도구를 사용하여 HTTP 프록시를 확인하고 업데이트할 수 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/view-proxy.png" alt-text="프록시 보기 및 업데이트":::

> [!NOTE]
> NTLM 인증을 사용하여 프록시 서버를 설정하면 통합 런타임 호스트 서비스가 도메인 계정에서 실행됩니다. 나중에 도메인 계정 암호를 변경하는 경우에는 서비스의 구성 설정을 업데이트하여 서비스를 다시 시작해야 합니다. 이 요구 사항으로 인해 암호를 자주 업데이트하지 않아도 되는 전용 도메인 계정을 사용하여 프록시 서버에 액세스하는 것이 좋습니다.

### <a name="configure-proxy-server-settings"></a>프록시 서버 설정 구성

HTTP 프록시에 대해 **시스템 프록시 사용** 옵션을 선택하는 경우 자체 호스팅 통합 런타임에서 diahost.exe.config와 diawp.exe.config의 프록시 설정을 사용합니다. 해당 파일에서 프록시를 지정하지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결합니다. 다음 절차에서는 diahost.exe.config 파일을 업데이트하는 지침을 제공합니다.

1. 파일 탐색기에서 원본 파일을 백업할 C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config의 안전한 복사본을 만듭니다.
1. 관리자 권한으로 메모장 열기.
1. 관리자 권한으로 Notepad를 열고 텍스트 파일 C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config를 엽니다.
1. 다음 코드에 표시된 것처럼 기본 **system.net** 태그를 찾습니다.

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    그러면 다음 예제와 같이 프록시 서버 세부 정보를 추가할 수 있습니다.

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    프록시 태그를 통해 추가 속성은 `scriptLocation`와 같은 필수 설정을 지정할 수 있습니다. 구문은 [\<proxy\>요소(네트워크 설정)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings)를 참조하세요.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```

1. 구성 파일을 원래 위치에 저장 자체 호스팅 통합 런타임 호스트 서비스를 다시 시작할 경우, 변경 내용이 적용됩니다.

   서비스를 다시 시작하려면, 제어판에서 서비스 애플릿을 사용합니다. 또는 통합 런타임 구성 관리자에서 **서비스 중지** 단추를 선택한 후 **서비스 시작** 을 선택합니다.

   서비스가 시작되지 않으면 편집한 애플리케이션 구성 파일에 잘못된 XML 태그 구문이 추가되었을 가능성이 높습니다.

> [!IMPORTANT]
> diahost.exe.config 및 diawp.exe.config를 둘 다 업데이트해야 합니다.

또한 회사의 허용 목록에 Microsoft Azure가 있는지 확인해야 합니다. 유효한 Azure IP 주소 목록을 다운로드할 수 있습니다. 지역별, 해당 클라우드의 태그가 지정된 서비스별로 구분되는 각 클라우드의 IP 범위는 이제 MS 다운로드에서 사용할 수 있습니다. 
   - 퍼블릭: https://www.microsoft.com/download/details.aspx?id=56519
   - US Gov: https://www.microsoft.com/download/details.aspx?id=57063 
   - 독일: https://www.microsoft.com/download/details.aspx?id=57064 
   - 중국: https://www.microsoft.com/download/details.aspx?id=57062 

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>방화벽 및 프록시 서버와 관련된 문제에서 가능한 증상

다음과 같은 오류 메시지가 표시되는 경우 방화벽 또는 프록시 서버가 잘못 구성된 것일 수 있습니다. 잘못 구성되면 자체 호스팅 통합 런타임이 Data Factory 또는 Synapse 파이프라인에 연결하여 자신을 인증할 수 없습니다. 이전 섹션을 참조하여 방화벽 및 프록시 서버가 올바르게 구성되었는지 확인합니다.

- 자체 호스팅 통합 런타임을 등록할 때 다음과 같은 오류 메시지가 표시됩니다. "이 통합 런타임 노드를 등록하지 못했습니다. 인증 키가 유효하며 통합 서비스 호스트 서비스가 이 머신에서 실행 중인지 확인하세요."
- 통합 런타임 구성 관리자를 열 때 상태가 **연결 끊김** 또는 **연결 중** 으로 표시됩니다. Windows 이벤트 로그를 볼 때 **이벤트 뷰어**  >  **애플리케이션 및 서비스 로그**  >  **Microsoft 통합 런타임** 에서 다음과 같은 오류 메시지가 표시됩니다.

  ```output
  Unable to connect to the remote server
  A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
  ```

### <a name="enable-remote-access-from-an-intranet"></a>인트라넷에서 원격 액세스 사용하기

PowerShell을 사용하여 자체 호스팅 통합 런타임이 설치된 위치가 아닌 네트워크의 다른 머신에서 자격 증명을 암호화하는 경우 **인트라넷에서 원격 액세스** 옵션을 사용하도록 설정할 수 있습니다. PowerShell을 사용하여 자체 호스팅 통합 런타임이 설치된 머신에서 자격 증명을 암호화하는 경우 **인트라넷에서 원격 액세스** 옵션을 사용하도록 설정할 수 없습니다.

고가용성과 확장성을 위해 다른 노드를 추가하기 전에 **인트라넷에서 원격 액세스** 를 사용하도록 설정해야 합니다.  

자체 호스팅 통합 런타임 설치(버전 3.3 이상) 실행 중에는 기본적으로 자체 호스팅 통합 런타임 설치 관리자가 **인트라넷에서 원격 액세스** 를 자체 호스팅 통합 런타임 머신에서 사용하지 않도록 설정합니다.

파트너 또는 다른 사용자의 방화벽을 사용하는 경우, 수동으로 8060 또는 사용자 구성 포트를 열 수 있습니다. 자체 호스팅 통합 런타임을 설정하는 동안 방화벽 문제가 발생하는 경우 다음 명령을 사용하여 방화벽을 구성하지 않고 자체 호스팅 통합 런타임을 설치합니다.

```cmd
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

자체 호스팅 통합 런타임 머신에서 8060 포트를 열지 않는 경우, 자격 증명 설정 애플리케이션 이외의 메커니즘을 사용하여 데이터 저장소 자격 증명을 구성합니다. 예를 들어 **New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell cmdlet을 사용할 수 있습니다.

## <a name="ports-and-firewalls"></a>포트 및 방화벽

다음 두 가지 방화벽을 고려해야 합니다.

- 기업에서는 *기업 방화벽* 이 조직의 중앙 라우터에서 실행됩니다.
- 또한 *Windows 방화벽* 은 자체 호스팅 통합 런타임이 설치된 로컬 머신에서 디먼으로 구성됩니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/firewall.png" alt-text="방화벽":::

회사 방화벽 수준에서는 다음 도메인 및 아웃바운드 포트를 구성해야 합니다.

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-internal.md)]

Windows 방화벽 수준 또는 머신 수준에서 대개 아웃바운드 포트를 사용하도록 설정됩니다. 해당 포트가 사용하도록 설정되어 있지 않으면 자체 호스팅 통합 런타임 머신에서 도메인 및 포트를 구성할 수 있습니다.

> [!NOTE]
> 현재 Azure Relay 서비스 태그를 지원하지 않으므로 Azure Relay와 통신하려면 NSG 규칙에서 **AzureCloud** 또는 **Internet** 서비스 태그를 사용해야 합니다.
> Azure Data Factory 및 Synapse 작업 영역 통신의 경우, NSG 규칙 설정에서 **DataFactoryManagement** 서비스 태그를 사용할 수 있습니다.

원본 및 싱크에 따라 회사 방화벽 또는 Windows 방화벽에서 추가 도메인 및 아웃바운드 포트를 허용해야 할 수 있습니다.

[!INCLUDE [domain-and-outbound-port-requirements](./includes/domain-and-outbound-port-requirements-external.md)]

Azure SQL Database 및 Azure Data Lake와 같은 일부 클라우드 데이터베이스의 경우에는 해당 방화벽 구성에서 자체 호스팅 통합 런타임의 IP 주소를 허용해야 할 수 있습니다.

### <a name="get-url-of-azure-relay"></a>Azure Relay URL 가져오기

방화벽의 허용 목록에 배치해야 하는 필수 도메인 및 포트 중 하나는 Azure Relay와의 통신입니다. 자체 호스팅 통합 런타임은 이를 연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 스키마 가져오기 및 데이터 미리 보기와 같은 대화형 제작에 사용합니다. **Servicebus.windows.net** 을 허용하지 않고 더 구체적인 URL을 포함하려는 경우 서비스 포털에서 자체 호스팅 통합 런타임에 필요한 모든 FQDN을 볼 수 있습니다. 다음 단계를 수행합니다.

1. 서비스 포털로 이동하여 자체 호스팅 통합 런타임을 선택합니다.
2. 편집 페이지에서 **노드** 를 선택합니다.
3. 모든 FQDN을 가져오려면 **서비스 URL 보기** 를 선택합니다.

   :::image type="content" source="media/create-self-hosted-integration-runtime/Azure-relay-url.png" alt-text="Azure Relay URL":::

4. 이 FQDN은 방화벽 규칙 허용 목록에 추가할 수 있습니다.

> [!NOTE]
> Azure Relay 연결 프로토콜과 관련된 자세한 내용은 [Azure Relay 하이브리드 연결 프로토콜](../azure-relay/relay-hybrid-connections-protocol.md)을 참조하세요.

### <a name="copy-data-from-a-source-to-a-sink"></a>원본에서 싱크로 데이터 복사

방화벽 규칙이 회사 방화벽, 자체 호스팅 통합 런타임 머신의 Windows 방화벽과 데이터 스토리지 자체에 올바르게 설정되어 있는지 확인합니다. 해당 규칙을 사용하면 자체 호스팅 통합 런타임을 원본과 싱크에 모두 정상적으로 연결할 수 있습니다. 복사 작업과 관련된 각 데이터 저장소에 대해 규칙을 사용하도록 설정합니다.

예를 들어, 온-프레미스 데이터 저장소에서 SQL Database 싱크 또는 Azure Synapse Analytics 싱크로 복사하려면 다음 단계를 수행합니다.

1. Windows 방화벽 및 회사 방화벽 둘 다에 대해 1433 포트에서 아웃바운드 TCP 통신을 허용합니다.
2. SQL 데이터베이스의 방화벽 설정을 구성하여 허용된 IP 주소 목록에 자체 호스팅 통합 런타임 머신의 IP 주소를 추가합니다.

> [!NOTE]
> 방화벽이 아웃바운드 1433 포트를 허용하지 않으면, 자체 호스팅 통합 런타임은 SQL 데이터베이스에 직접 액세스할 수 없습니다. 이 경우 [스테이징된 복사본](copy-activity-performance.md)을 사용하여 SQL Database 및 Azure Synapse Analytics를 사용할 수 있습니다. 이 시나리오에서는 데이터 이동에 HTTPS(443 포트)만 필요합니다.

## <a name="installation-best-practices"></a>설치 모범 사례

자체 호스팅 통합 런타임은 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 관리 ID 설치 패키지를 다운로드하여 설치할 수 있습니다. 단계별 지침은 [온-프레미스 및 클라우드 간 데이터 이동](tutorial-hybrid-copy-powershell.md) 문서를 참조하세요.

- 호스트 머신이 최대 절전 모드로 전환되지 않도록 해당 머신에서 자체 호스팅 통합 런타임에 대한 전원 관리 옵션을 구성합니다. 호스트 컴퓨터가 최대 절전 모드로 설정되면 자체 호스팅 통합 런타임도 오프라인 상태가 됩니다.
- 자체 호스팅 통합 런타임과 연결된 자격 증명을 정기적으로 백업합니다.
- 자체 호스팅 IR 설치 작업을 자동화하려면 [PowerShell을 통해 기존 자체 호스트 IR 설정](#setting-up-a-self-hosted-integration-runtime)을 참조하세요.

## <a name="next-steps"></a>다음 단계

단계별 지침은 [자습서: 온-프레미스 데이터를 클라우드로 복사](tutorial-hybrid-copy-powershell.md)를 참조하세요.
