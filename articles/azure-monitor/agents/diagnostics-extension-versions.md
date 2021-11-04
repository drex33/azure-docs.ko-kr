---
title: WAD(Windows Azure Diagnostics) 확장 구성 스키마 버전 기록
description: Azure Virtual Machines, VM Scale Sets, Service Fabric 및 Cloud Services에서 성능 카운터 수집과 관련됩니다.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: c730f1d5d440d809932499f04338e68a2375b6d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044251"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>WAD(Windows Azure Diagnostics) 확장 구성 스키마 버전 및 기록
이 문서에서는 Microsoft Azure SDK의 일부로 제공되는 [WAD(Azure Diagnostics Windows) 확장](diagnostics-extension-overview.md) 스키마 버전에 대한 버전 기록을 제공합니다.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Azure SDK 및 진단 버전 전달 차트  

|Azure SDK 버전 | 진단 확장 버전 | 모델|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |플러그 인|  
|2.0 - 2.4         |1.0                            |플러그 인|  
|2.5               |1.2                            |확장|  
|2.6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2.96              |1.7                            |"|
|2.96              |1.8                            |"|
|2.96              |1.8.1                          |"|
|2.96              |1.9                            |"|
|2.96              |1.11                           |"|


 Azure Diagnostics 버전 1.0은 처음에는 플러그 인 모델로 제공되었습니다. 따라서 Azure SDK를 설치할 때 함께 제공되는 Azure 진단 버전을 사용할 수 있게 되었습니다.  

 SDK 2.5(진단 버전 1.2)부터는 Azure 진단이 확장 모델로 이동되었습니다. 새 기능을 활용하기 위한 도구는 최신 Azure SDK에서만 사용할 수 있었지만 Azure 진단을 사용하는 모든 서비스는 Azure에서 직접 최신 전달 버전을 선택할 수 있습니다. 예를 들어, 여전히 SDK 2.5를 사용하는 모든 사용자는 최신 기능을 사용 중인지 여부와 관계 없이 이전 테이블에 표시된 최신 버전이 로딩됩니다.  

## <a name="schemas-index"></a>스키마 색인  
서로 다른 버전의 Azure 진단은 다른 구성 스키마를 사용합니다. 스키마 1.0 및 1.2는 더 이상 사용되지 않습니다. 버전 1.3 이상에 대한 자세한 내용은 [Diagnostics 1.3 이상 구성 스키마](diagnostics-extension-schema-windows.md)를 참조하세요.  

## <a name="version-history"></a>버전 기록

### <a name="diagnostics-extension-111"></a>진단 확장 1.11
Azure Monitor 싱크에 대한 지원이 추가되었습니다. 이 싱크는 성능 카운터에만 적용됩니다. VM, VMSS 또는 클라우드 서비스에 수집된 성능 카운터를 Azure Monitor에 사용자 지정 메트릭으로 보낼 수 있습니다. Azure Monitor 싱크는 다음을 지원합니다.
* [Azure Monitor 메트릭 API](/rest/api/monitor/metrics/list)를 통해 Azure Monitor로 보낸 모든 성능 카운터 검색
* Azure Monitor의 새로운 [통합 경고 환경](../alerts/alerts-overview.md)을 통해 Azure Monitor로 보낸 모든 성능 카운터에 대해 경고
* 성능 카운터의 와일드카드 연산자를 메트릭에 대한 “인스턴스” 차원으로 처리. 예를 들어 “LogicalDisk(\*)/DiskWrites/sec” 카운터를 수집한 경우 “인스턴스” 차원을 기준으로 필터링 및 분할하여 각 논리 디스크(C:, D: 등)에 대해 Disk Writes/sec를 그리거나 경고할 수 있습니다.

진단 확장 구성에서 Azure Monitor를 새 싱크로 정의
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```xml
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> 클래식 VM 및 클래식 클라우드 서비스에 대해 Azure Monitor 싱크를 구성하려면 진단 확장의 프라이빗 구성에서 추가 매개 변수를 정의해야 합니다.
>
> 자세한 내용은 [자세한 진단 확장 스키마 설명서](diagnostics-extension-schema-windows.md)를 참조하세요.

그런 다음, Azure Monitor 싱크로 라우팅되도록 성능 카운터를 구성할 수 있습니다.
```json
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "AzureMonitorSink",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT1M",
            "unit": "percent"
        }
    ]
},
```
```xml
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>진단 확장 1.9
Docker 지원이 추가되었습니다.


### <a name="diagnostics-extension-181"></a>진단 확장 1.8.1
프라이빗 구성에서 스토리지 계정 키 대신 SAS 토큰을 지정할 수 있습니다. SAS 토큰을 제공하는 경우 스토리지 계정 키는 무시됩니다.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>진단 확장 1.8
PublicConfig에 스토리지 형식이 추가되었습니다. StorageType은 *Table*, *Blob*, *TableAndBlob* 이 될 수 있습니다. *Table* 이 기본값입니다.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>진단 확장 1.7
EventHub로 라우팅하는 기능이 추가되었습니다.

### <a name="diagnostics-extension-15"></a>진단 확장 1.5
[Application Insights](../app/cloudservices.md)에 진단 데이터를 전송하는 싱크 요소 및 기능이 추가되어 시스템 및 인프라 수준뿐만 아니라 애플리케이션 전반에 나타나는 문제를 쉽게 진단할 수 있습니다.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2.6 및 진단 확장 1.3
Visual Studio에서 클라우드 서비스 프로젝트의 경우 다음 사항이 변경되었습니다. (이러한 변경 사항은 이후 버전의 Azure SDK에도 적용됩니다.)

* 이제 로컬 에뮬레이터에서 진단을 지원합니다. 따라서 Visual Studio에서 개발 및 테스트하는 동안 진단 데이터를 수집하고 애플리케이션에서 올바른 추적을 생성하고 있는지 확인할 수 있습니다. 연결 문자열 `UseDevelopmentStorage=true`는 Azure Storage 에뮬레이터를 사용하여 Visual Studio에서 클라우드 서비스 프로젝트를 실행하는 동안 진단 데이터 수집을 사용하도록 설정합니다. 모든 진단 데이터는 (개발 스토리지) Storage 계정에 수집됩니다.
* 진단 스토리지 계정 연결 문자열(Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString)은 서비스 구성(.cscfg) 파일에 다시 저장됩니다. Azure SDK 2.5에서는 진단 스토리지 계정이 diagnostics.wadcfgx 파일에 지정되었습니다.

Azure SDK 2.4 이하와 Azure SDK 2.6 이상 버전에서 연결 문자열이 작동하는 방식 간에 몇 가지 주목할 만한 차이점이 있습니다.

* Azure SDK 2.4 이하 버전에서는 진단 로그를 전송하기 위한 스토리지 계정 정보를 가져오기 위해 진단 플러그인에 의해 런타임으로 연결 문자열이 사용되었습니다.
* Azure SDK 2.6 이상 버전에서 Visual Studio는 게시하는 동안 진단 연결 문자열을 사용하여 적절한 스토리지 계정 정보로 진단 확장을 구성합니다. 연결 문자열을 통해 사용자는 Visual Studio에서 게시하는 동안 사용할 다양한 서비스 구성에 대해 서로 다른 스토리지 계정을 정의할 수 있습니다. 그러나 진단 플러그인을 더 이상 사용할 수 없으므로(Azure SDK 2.5 이후) .cscfg 파일 자체만으로 진단 확장을 활성화할 수 없습니다. Visual Studio 또는 PowerShell과 같은 도구를 통해 별도로 확장을 사용하도록 설정해야 합니다.
* PowerShell을 사용한 진단 확장 구성 프로세스를 단순화하기 위해 Visual Studio의 패키지 출력에도 각 역할에 대한 진단 확장의 공용 구성 XML이 포함됩니다. Visual Studio에서는 진단 연결 문자열을 사용하여 공용 구성에 있는 스토리지 계정 정보를 채웁니다. 퍼블릭 구성 파일이 Extensions 폴더에서 생성되고 `PaaSDiagnostics.<RoleName>.PubConfig.xml` 패턴을 따릅니다. 모든 PowerShell 기반 배포에서 이 패턴을 사용하여 각 구성을 역할에 매핑합니다.
* .cscfg 파일의 연결 문자열은 Azure Portal에서 진단 데이터에 액세스하는 데도 사용되므로 **모니터링** 탭에 나타날 수 있습니다. 연결 문자열은 포털에서 자세한 모니터링 데이터를 표시하도록 서비스를 구성하는 데 필요합니다.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Azure SDK 2.6 이상으로 프로젝트 마이그레이션
Azure SDK 2.5에서 Azure SDK 2.6 이상으로 마이그레이션하는 경우 .wadcfgx 파일에 진단 스토리지 계정이 지정된 경우 값이 계속 유지됩니다. 서로 다른 스토리지 구성에 대해 서로 다른 스토리지 계정을 유연성 있게 사용하려면 프로젝트에 연결 문자열을 수동으로 추가해야 합니다. Azure SDK 2.4 또는 이전 버전에서 Azure SDK 2.6으로 프로젝트를 마이그레이션하는 경우 진단 연결 문자열이 유지됩니다. 그러나 이전 섹션에서 지정된 것처럼 Azure SDK 2.6에서 연결 문자열을 처리하는 방식이 변경되었음에 유의하세요.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Visual Studio에서 진단 스토리지 계정을 결정하는 방법
* 진단 연결 문자열이.cscfg 파일에 지정된 경우 Visual Studio에서는 게시할 때와 패키징 중 공용 구성 xml 파일을 생성할 때 이 문자열을 사용하여 진단 확장을 구성합니다.
* 진단 연결 문자열이.cscfg 파일에 지정되지 않은 경우 Visual Studio에서는 게시할 때와 패키징 중 공용 구성 xml 파일을 생성할 때 .wadcfgx 파일에 지정된 스토리지 계정을 대신 사용하여 진단 확장을 구성합니다.
* .cscfg 파일의 진단 연결 문자열은 .wadcfgx 파일의 스토리지 계정보다 우선합니다. 진단 연결 문자열이.cscfg 파일에 지정된 경우, Visual Studio에서는 이 문자열을 사용하고 .wadcfgx의 스토리지 계정은 무시합니다.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>“개발 스토리지 연결 문자열 업데이트...” 확인란의 기능은 무엇인가요?
**Microsoft Azure에 게시할 때 Microsoft Azure 저장소 계정 자격 증명을 통해 진단 및 캐싱에 대한 개발 저장소 연결 문자열 업데이트** 확인란을 사용하면 게시 중에 지정된 Azure 저장소 계정으로 개발 저장소 계정 연결 문자열을 편리하게 업데이트할 수 있습니다.

예를 들어, 사용자가 이 확인란을 선택하고 진단 연결 문자열에서 `UseDevelopmentStorage=true`를 지정한다고 가정합니다. Azure에 프로젝트를 게시할 때 Visual Studio는 사용자가 게시 마법사에 지정한 스토리지 계정을 사용하여 진단 연결 문자열을 자동으로 업데이트합니다. 그러나 실제 스토리지 계정을 진단 연결 문자열로 지정한 경우 해당 계정이 대신 사용됩니다.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Azure SDK 2.4 이하 및 Azure SDK 2.5 이상 간의 진단 기능 차이점
Azure SDK 2.4에서 Azure SDK 2.5 이상으로 업그레이드하는 경우 다음 진단 기능 차이점을 명심해야 합니다.

* **구성 API가 사용되지 않음** – 진단의 프로그래밍 방식 구성은 Azure SDK 2.4 또는 이전 버전에서 사용할 수 있지만 Azure SDK 2.5 이상에서는 사용되지 않습니다. 코드에 진단 구성이 현재 정의된 경우 계속해서 진단하려면 마이그레이션된 프로젝트에서 해당 설정을 처음부터 다시 구성해야 합니다. Azure SDK 2.4에 대한 진단 구성 파일은 diagnostics.wadcfg이고 Azure SDK 2.5 이상에서는 diagnostics.wadcfgx입니다.
* **클라우드 서비스 애플리케이션에 대한 진단은 인스턴스 수준이 아닌 역할 수준에서만 구성할 수 있습니다.**
* **앱을 배포할 때마다 진단 구성이 업데이트됨** – 이로 인해 서버 탐색기에서 진단 구성을 변경한 후 앱을 다시 배포하는 경우 패리티 문제가 발생할 수 있습니다.
* **Azure SDK 2.5 이상에서 크래시 덤프는 코드가 아닌 진단 구성 파일에서 구성됨** – 코드에서 크래시 덤프를 구성했다면 Azure SDK 2.6으로 마이그레이션할 때 크래시 덤프가 전송되지 않으므로 구성을 코드에서 구성 파일로 수동으로 전송해야 합니다.