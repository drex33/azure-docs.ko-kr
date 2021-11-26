---
title: Azure 부서의 범위 automation 모범 사례
description: 이 문서에서는 Azure 부서의 범위 automation 도구에 대해 간략하게 설명 하 고, 경우에 사용할 항목에 대 한 지침을 제공 합니다.
author: tayganr
ms.author: tarifat
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/23/2021
ms.openlocfilehash: ccf31e555ecb5e50e149f0ab1a237dd6bc0f7ab6
ms.sourcegitcommit: 4298f71f502c70d601a7c58b28fc7bca62be3595
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133133613"
---
# <a name="azure-purview-automation-best-practices"></a>Azure 부서의 범위 automation 모범 사례

Azure 부서의 범위는 부서의 범위 Studio를 사용 하는 기본 사용자 환경을 제공 하지만 모든 작업은 그래픽 사용자 환경의 특정 시점 및 클릭 특성에 적합 하지 않습니다. 

다음은 그 예입니다.
* 자동화 된 프로세스의 일부로 실행할 검색 트리거
* 메타 데이터 변경 내용을 실시간으로 모니터링 합니다.
* 사용자 지정 사용자 환경을 구축 합니다.

Azure 부서의 범위는를 사용 하 여 기본 플랫폼과 자동화 된 방식으로 상호 작용할 수 있는 여러 가지 도구를 제공 합니다. Azure 부서의 범위 서비스의 개방형 특성으로 인해, Azure Resource Manager을 통해 액세스할 수 있는 제어 평면에서 Azure 부서의 범위의 여러 데이터 평면 (카탈로그, 검색, 관리 등)으로 다양 한 측면을 자동화할 수 있습니다.

이 문서에서는 사용할 수 있는 옵션을 요약 하 고, 경우 사용할 항목에 대 한 지침을 제공 합니다.

## <a name="tools"></a>도구

| 도구 형식 | 도구 | 시나리오 | 관리 | 카탈로그 | 검사 |
| --- | --- | --- | --- | --- | --- |
**리소스 관리** | <ul><li><a href="/azure/templates/" target="_blank">ARM 템플릿</a></li><li><a href="https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/purview_account" target="_blank">Terraform</a></li></ul> | 코드 제공 인프라(Infrastructure as Code) | ✓ | | |
**명령줄** | <ul><li><a href="/cli/azure/purview" target="_blank">Azure CLI</a></li><li><a href="/powershell/module/az.purview" target="_blank">Azure PowerShell</a></li></ul> | 대화형 | ✓ | | |
**API** | <ul><li><a href="/rest/api/purview/" target="_blank">REST API</a></li></ul> | 주문형 | ✓ | ✓ | ✓ |
**스트리밍** (Atlas Kafka) | <ul><li><a href="/azure/purview/manage-kafka-dotnet" target="_blank">Apache Kafka</a></li></ul> | 실시간 | | ✓ | |
**스트리밍** (진단 로그) | <ul><li><a href="/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD#destinations" target="_blank">Event Hubs</a></li></ul> | 실시간 | | | ✓ |
**SDK** | <ul><li><a href="/dotnet/api/overview/azure" target="_blank">.NET</a></li><li><a href="/java/api/overview/azure" target="_blank">Java</a></li><li><a href="/javascript/api/overview/azure" target="_blank">JavaScript</a></li><li><a href="/python/api/overview/azure" target="_blank">Python</a></li></ul> | 사용자 지정 개발 | ✓ | ✓ | ✓ |

## <a name="resource-management"></a>리소스 관리
[Azure Resource Manager](/azure/azure-resource-manager/management/overview) 는 고객이 Azure에서 리소스를 만들고, 업데이트 하 고, 삭제할 수 있도록 하는 배포 및 관리 서비스입니다. Azure 리소스를 반복적으로 배포 하는 경우에는 ARM 템플릿을 사용 하 여 일관성을 유지할 수 있습니다 .이 방법을 코드 인프라 라고 합니다.

인프라를 코드로 구현 하려면 JSON 또는 [Bicep](/azure/azure-resource-manager/bicep/overview)를 사용 하거나 [terraform](/azure/developer/terraform/overview)과 같은 오픈 소스 대체를 사용 하 여 [ARM 템플릿을](/azure/azure-resource-manager/templates/overview) 빌드할 수 있습니다. 

사용하는 경우
* 반복 되는 Azure 부서의 범위 배포를 필요로 하는 시나리오에서는 템플릿이 다른 종속 리소스와 함께 Azure 부서의 범위를 일관 된 방식으로 배포 하도록 합니다.
* [배포 스크립트](/azure/azure-resource-manager/templates/deployment-script-template)와 함께 사용 하는 경우 템플릿 기반 솔루션은 제어 및 데이터 평면을 트래버스 하 여 종단 간 솔루션을 배포할 수 있습니다. 예를 들어 Azure 부서의 범위 계정을 만들고, 소스를 등록 하 고, 검색을 트리거합니다.

## <a name="command-line"></a>명령줄
Azure CLI 및 Azure PowerShell는 azure 부서의 범위와 같은 azure 리소스를 관리할 수 있는 명령줄 도구입니다. 명령 목록은 시간이 지남에 따라 증가 하지만, 현재 Azure 부서의 범위 제어 평면 작업의 하위 집합만 사용할 수 있습니다. 현재 사용할 수 있는 명령에 대 한 최신 목록을 보려면 설명서 ([Azure CLI](/cli/azure/purview)  |  [Azure PowerShell](/powershell/module/az.purview))를 확인 하세요.

* **Azure CLI** -대화형 명령줄 프롬프트 또는 스크립트를 사용 하 여 터미널을 통해 명령을 실행할 수 있도록 하는 플랫폼 간 도구입니다. Azure CLI에는 Azure 부서의 범위 계정 관리를 허용 하는 **부서의 범위 확장이** 있습니다. 예들 들어 `az purview account`입니다.
* **Azure PowerShell** -Azure 리소스를 관리 하기 위한 cmdlet 집합으로 구성 된 플랫폼 간 작업 자동화 프로그램입니다. Azure PowerShell에는 Azure 부서의 범위 계정을 관리할 수 있는 **Az. 부서의 범위** 라는 모듈이 있습니다. 예들 들어 `Get-AzPurviewAccount`입니다.

사용하는 경우
* 임시 작업 및 빠른 예비 작업에 가장 적합 합니다.

## <a name="api"></a>API
REST api는 다양 한 메서드 ( `POST` ,, `GET` `PUT` , `DELETE` )를 노출 하 고 만들기, 읽기, 업데이트 또는 삭제 (CRUD)와 같은 동작을 트리거하는 HTTP 끝점입니다. Azure 부서의 범위는 여러 [서비스 끝점](/rest/api/purview/)을 통해 azure 부서의 범위 플랫폼의 많은 부분을 노출 합니다.

사용하는 경우
* Azure CLI, Azure PowerShell 또는 native client 라이브러리를 통해 필요한 작업을 수행할 수 없습니다.
* 사용자 지정 응용 프로그램 개발 또는 프로세스 자동화

## <a name="streaming-atlas-kafka"></a>스트리밍 (Atlas Kafka)
각 Azure 부서의 범위 계정은 완전히 관리 되는 이벤트 허브와 함께 제공 되며, Azure Portal > Azure 부서의 범위 Account > 속성을 통해 발견 된 Atlas Kafka 끝점을 통해 액세스할 수 있습니다. 이벤트 허브에서 메시지를 사용 하 여 Azure 부서의 범위 이벤트를 모니터링할 수 있습니다. 외부 시스템은 이벤트 허브를 사용 하 여 이벤트가 발생 하는 동안 Azure 부서의 범위에 이벤트를 게시할 수도 있습니다.
* **이벤트 사용** -Azure 부서의 범위는 **ATLAS_ENTITIES** 메타 데이터 변경에 대 한 알림을 kafka 토픽으로 보냅니다. 메타 데이터 변경에 관심이 있는 응용 프로그램은 이러한 알림을 모니터링할 수 있습니다. 지원 되는 작업 `ENTITY_CREATE` 에는,,,,, 등이 `ENTITY_UPDATE` `ENTITY_DELETE` `CLASSIFICATION_ADD` `CLASSIFICATION_UPDATE` `CLASSIFICATION_DELETE` 있습니다.
* **게시 이벤트** -Azure 부서의 범위는 **ATLAS_HOOK** 에 대 한 알림을 통해 메타 데이터 변경에 대 한 알림을 받을 수 있습니다. 지원 되는 작업은 `ENTITY_CREATE_V2` ,, `ENTITY_PARTIAL_UPDATE_V2` `ENTITY_FULL_UPDATE_V2` , `ENTITY_DELETE_V2` 입니다.

사용하는 경우
* Apache Atlas 이벤트를 실시간으로 게시 하거나 사용 해야 하는 응용 프로그램 또는 프로세스입니다.

## <a name="streaming-diagnostic-logs"></a>스트리밍 (진단 로그)
azure 부서의 범위는 "진단 설정"을 통해 플랫폼 로그 및 메트릭을 하나 이상의 대상 (Log Analytics 작업 영역, Storage 계정 또는 Azure Event Hubs)으로 보낼 수 있습니다. [사용 가능한 메트릭에](/azure/purview/how-to-monitor-with-azure-monitor#available-metrics) 는,,,, 및가 있습니다 `Data Map Capacity Units` `Data Map Storage Size` `Scan Canceled` `Scan Completed` `Scan Failed` `Scan Time Taken` .

구성 된 후 Azure 부서의 범위는 이러한 이벤트를 대상에 JSON 페이로드로 자동으로 보냅니다. 여기에서 이러한 이벤트를 사용 하 고 작업 해야 하는 응용 프로그램 구독자는 오케스트레이션 다운스트림 논리의 옵션을 사용 하 여이 작업을 수행할 수 있습니다.

사용하는 경우
* 실시간으로 진단 이벤트를 사용 해야 하는 응용 프로그램 또는 프로세스입니다.

## <a name="sdk"></a>SDK)
Microsoft는 azure 서비스를 프로그래밍 방식으로 관리 하 고 상호 작용 하기 위해 Azure Sdk를 제공 합니다. Azure 부서의 범위 클라이언트 라이브러리는 일관성, 하기 쉬우며 및 자연 스러운로 설계 된 다양 한 언어 (.NET, Java, JavaScript 및 Python)에서 사용할 수 있습니다.

사용하는 경우
* 네이티브 클라이언트 라이브러리 (사용 가능한 경우)가 개발자에 게 자연스럽 게 느낄 수 있는 대상 언어로 표준 프로그래밍 언어 규칙을 따르는 REST API 것이 좋습니다.

**Azure SDK for .NET**
* [Docs](/dotnet/api/azure.analytics.purview.account?view=azure-dotnet-preview&preserve-view=true)  |  부서의 범위를 [NuGet](https://www.nuget.org/packages/Azure.Analytics.Purview.Account/1.0.0-beta.1) 합니다.
* [Docs](/dotnet/api/azure.analytics.purview.administration?view=azure-dotnet-preview&preserve-view=true)  |  [NuGet](https://www.nuget.org/packages/Azure.Analytics.Purview.Administration/1.0.0-beta.1) 부서의 범위. 관리
* [Docs](/dotnet/api/azure.analytics.purview.catalog?view=azure-dotnet-preview&preserve-view=true)  |  부서의 범위를 [NuGet](https://www.nuget.org/packages/Azure.Analytics.Purview.Catalog/1.0.0-beta.2) 합니다.
* [Docs](/dotnet/api/azure.analytics.purview.scanning?view=azure-dotnet-preview&preserve-view=true)  |  [NuGet](https://www.nuget.org/packages/Azure.Analytics.Purview.Scanning/1.0.0-beta.2) 부서의 범위를 검색 합니다.
* [Docs](/dotnet/api/microsoft.azure.management.purview?view=azure-dotnet-preview&preserve-view=true)  |  부서의 범위를 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/) 합니다.

**Java용 Azure SDK**
* [Docs](/java/api/com.azure.analytics.purview.account?view=azure-java-preview&preserve-view=true)  |  [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-purview-account/1.0.0-beta.1/jar) . 부서의 범위.
* 문서 | [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-purview-administration/1.0.0-beta.1/jar) . 부서의 범위.
* [Docs](/java/api/com.azure.analytics.purview.catalog?view=azure-java-preview&preserve-view=true)  |  [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-purview-catalog/1.0.0-beta.2/jar) . 부서의 범위.
* [Docs](/java/api/com.azure.analytics.purview.scanning?view=azure-java-preview&preserve-view=true)  |  [Maven](https://search.maven.org/artifact/com.azure/azure-analytics-purview-scanning/1.0.0-beta.2/jar) 부서의 범위를 검색 합니다.
* [Docs](/java/api/com.azure.resourcemanager.purview?view=azure-java-preview&preserve-view=true)  |  [Maven](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-purview/1.0.0-beta.1/jar) . 부서의 범위

**JavaScript용 Azure SDK**
* [Docs](/javascript/api/overview/azure/purview-account-rest-readme?view=azure-node-preview&preserve-view=true)  |  [npm](https://www.npmjs.com/package/@azure-rest/purview-account) @azure-rest/purview-account
* [Docs](/javascript/api/overview/azure/purview-administration-rest-readme?view=azure-node-preview&preserve-view=true)  |  [npm](https://www.npmjs.com/package/@azure-rest/purview-administration) @azure-rest/purview-administration
* [Docs](/javascript/api/overview/azure/purview-catalog-rest-readme?view=azure-node-preview&preserve-view=true)  |  [npm](https://www.npmjs.com/package/@azure-rest/purview-catalog) @azure-rest/purview-catalog
* [Docs](/javascript/api/overview/azure/purview-scanning-rest-readme?view=azure-node-preview&preserve-view=true)  |  [npm](https://www.npmjs.com/package/@azure-rest/purview-scanning) @azure-rest/purview-scanning
* [Docs](/javascript/api/@azure/arm-purview/?view=azure-node-preview&preserve-view=true)  |  [npm](https://www.npmjs.com/package/@azure/arm-purview) @azure/arm-purview

**Python용 Azure SDK**
* [Docs](/python/api/azure-purview-account/?view=azure-python-preview&preserve-view=true)  |  [Pypi](https://pypi.org/project/azure-purview-account/) azure-부서의 범위
* [Docs](/python/api/azure-purview-administration/?view=azure-python-preview&preserve-view=true)  |  [Pypi](https://pypi.org/project/azure-purview-administration/) azure-부서의 범위-관리
* [Docs](/python/api/azure-purview-catalog/?view=azure-python-preview&preserve-view=true)  |  [Pypi](https://pypi.org/project/azure-purview-catalog/) 부서의 범위-카탈로그
* [Docs](/python/api/azure-purview-scanning/?view=azure-python-preview&preserve-view=true)  |  [Pypi](https://pypi.org/project/azure-purview-scanning/) azure-부서의 범위-검색
* [Docs](/python/api/azure-mgmt-purview/?view=azure-python&preserve-view=true)  |  [Pypi](https://pypi.org/project/azure-mgmt-purview/) azure-관리-부서의 범위

## <a name="next-steps"></a>다음 단계
* [Azure 부서의 범위 REST API](/rest/api/purview)