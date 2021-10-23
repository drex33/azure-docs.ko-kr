---
title: Integration Runtime 만들기 및 관리
description: 이 문서에서는 Azure Purview에서 Integration Runtime을 만들고 관리하는 단계를 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 4c74181ef587469daf003bca5c2276e2bf26fbb3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246008"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기 및 관리

이 문서에서는 Azure Purview에서 데이터 원본을 검사할 수 있는 SHIR(자체 호스팅 통합 런타임)을 만들고 관리하는 방법을 설명합니다.

> [!NOTE]
> Purview Integration Runtime은 동일한 컴퓨터의 Azure Synapse Analytics 또는 Azure Data Factory Integration Runtime으로 공유할 수 없습니다. 분리된 컴퓨터에 설치해야 합니다.

> [!IMPORTANT]
> 2021년 8월 18일 이후에 Azure Purview 계정을 만든 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 버전의 자체 호스팅 통합 런타임을 다운로드하여 설치해야 합니다.

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
- 검사 실행은 설정한 일정에 따라 특정 빈도로 발생합니다. 머신의 프로세서 및 RAM 사용량은 최대 및 유휴 시간과 동일한 패턴을 따릅니다. 또한 리소스 사용량은 검사되는 데이터의 양에 따라 크게 달라집니다. 여러 검사 작업이 진행 중인 경우 사용량이 많은 시간 동안 리소스 사용량이 증가한 것을 볼 수 있습니다.
- Parquet, ORC 또는 Avro 형식의 데이터를 추출하는 동안 작업이 실패할 수 있습니다.

> [!IMPORTANT]
> Self-Hosted Integration Runtime을 사용하여 Parquet 파일을 검사하려면 IR 머신에 **64비트 JRE 8(Java Runtime Environment) 또는 OpenJDK를** 설치해야 합니다. 설치 가이드는 [페이지 아래쪽의 Java Runtime Environment 섹션을](#java-runtime-environment-installation) 확인하세요.

## <a name="setting-up-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 설정

자체 호스팅 통합 런타임을 만들고 설정하려면 다음 프로시저를 따르세요.

## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

1. [Purview Studio](https://web.purview.azure.com/resource/)홈페이지의 왼쪽 탐색 창에서 **데이터 맵** 선택합니다.

2. 왼쪽 창의 **원본 및 스캔** 에서 **통합 런타임** 을 선택한 다음 **+ 새로 만들기** 를 선택합니다.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR에서 를 선택합니다.":::

3. **통합 런타임 설정** 페이지에서 **자체 호스팅** 을 선택하여 자체 호스팅 IR을 만든 다음 **계속** 을 선택합니다.

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="새 SHIR을 만듭니다.":::

4. IR의 이름을 입력하고 생성을 선택합니다.

5. **통합 런타임 설정** 페이지에서 **수동 설정** 섹션의 단계를 따릅니다. 다운로드 사이트에서 실행하려는 VM 또는 컴퓨터에 통합 런타임을 다운로드해야 합니다.

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="키 가져오기":::

   - 인증 키를 복사하여 붙여넣습니다.

   - 로컬 Windows 컴퓨터에 [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717)에서 자체 호스팅 통합 런타임을 다운로드합니다. 설치 관리자를 실행합니다. 5\.4.7803.1 및 5.6.7795.1 등의 자체 호스팅 통합 런타임 버전이 지원됩니다. 

   - 이전에 저장한 두 키 중 하나를 **Integration Runtime(자체 호스팅) 등록** 페이지에 붙여넣고 **등록** 을 선택합니다.

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="키를 입력합니다.":::

   - **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침** 을 선택합니다.

6. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 창이 표시됩니다.

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="성공적으로 등록 되었습니다.":::

## <a name="networking-requirements"></a>네트워킹 요구 사항

자체 호스팅 통합 런타임 머신이 제대로 작동하려면 여러 리소스에 연결해야 합니다.

* 자체 호스팅 통합 런타임을 사용하여 검사하려는 원본
* Purview 리소스에 대한 자격 증명을 저장하는 데 사용되는 모든 Azure Key Vault
* Purview에서 만든 관리형 Storage 계정 및 Event Hub 리소스

관리형 Storage 및 Event Hub 리소스는 Purview 리소스의 이름이 포함된 리소스 그룹 아래의 구독에서 찾을 수 있습니다. Azure Purview는 이러한 리소스를 사용하여 여러 가지 다른 사항 중에서 검사 결과를 검색하므로 자체 호스팅 통합 런타임이 이러한 리소스와 직접 연결할 수 있어야 합니다.

회사 및 머신 방화벽을 통해 허용해야 하는 도메인 및 포트는 다음과 같습니다.

> [!NOTE]
> '\<managed Purview storage account>'로 나열된 도메인의 경우 Purview 리소스와 연결된 관리형 스토리지 계정의 이름을 추가합니다. 포털에서 이 리소스를 찾을 수 있습니다. 리소스 그룹에서 managed-rg-\<your Purview Resource name> 그룹을 검색합니다. 예제: managed-rg-contosoPurview 이 리소스 그룹의 스토리지 계정 이름을 사용합니다.
> 
> '\<managed Event Hub resource>'로 나열된 도메인의 경우 Purview 리소스와 연결된 관리형 Event Hub의 이름을 추가합니다. 관리형 스토리지 계정과 동일한 리소스 그룹에서 찾을 수 있습니다.

| 도메인 이름                  | 아웃바운드 포트 | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | 글로벌 인프라 Purview는 이를 사용하여 해당 검사를 실행합니다. 전용 리소스가 없기 때문에 와일드카드가 필요합니다. |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview는 이를 사용하여 연결된 서비스 버스에 연결합니다. 위의 도메인을 허용하는 방법을 다루지만 프라이빗 엔드포인트를 사용하는 경우 이 단일 도메인에 대한 액세스를 테스트해야 합니다.|
| `*.frontend.clouddatahub.net` | 443            | 글로벌 인프라 Purview는 이를 사용하여 해당 검사를 실행합니다. 전용 리소스가 없기 때문에 와일드카드가 필요합니다. |
| `<managed Purview storage account>.core.windows.net`          | 443            | 자체 호스팅 통합 런타임에서 관리형 Azure 스토리지 계정에 연결하는 데 사용됩니다.|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | purview에서 검사 프로세스를 실행하는 데 사용하는 큐입니다. |
| `download.microsoft.com` | 443           | SHIR 업데이트의 경우 선택 사항입니다. |

원본에 따라 다른 Azure 또는 외부 원본의 도메인을 허용해야 할 수도 있습니다. 아래에는 몇 가지 예와 Key Vault에 저장된 자격 증명에 연결하는 경우 Azure Key Vault 도메인도 나와 있습니다.

| 도메인 이름                  | 아웃바운드 포트 | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | 선택 사항으로, Azure Storage 계정에 연결합니다. |
| `*.database.windows.net`      | 1433           | 선택 사항으로, Azure SQL Database 또는 Azure Synapse Analytics에 연결합니다. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 선택 사항으로, Azure Data Lake Store Gen 1에 연결합니다. |
| `<datastoragename>.dfs.core.windows.net`    | 443            | 선택 사항으로, Azure Data Lake Store Gen 2에 연결합니다. |
| `<your Key Vault Name>.vault.azure.net` | 443           | 자격 증명이 Azure Key Vault에 저장된 경우 필요합니다. |
| 다양한 도메인 | 종속          | SHIR이 연결할 다른 원본에 대한 도메인 |
  
> [!IMPORTANT]
> 대부분의 환경에서는 DNS가 올바르게 구성되었는지도 확인해야 합니다. 확인하기 위해 SHIR 머신에서 **nslookup** 을 사용하여 위의 도메인 각각에 대한 연결을 확인할 수 있습니다. 각 nslookup은 리소스의 IP를 반환해야 합니다. [프라이빗 엔드포인트](catalog-private-link.md)를 사용하는 경우 공용 IP가 아닌 개인 IP가 반환되어야 합니다. IP가 반환되지 않거나 프라이빗 엔드포인트를 사용할 때 공용 IP가 반환되는 경우 DNS/VNET 연결 또는 프라이빗 엔드포인트/VNET 피어링을 처리해야 합니다.

## <a name="manage-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 관리

**관리 센터에서** 통합 런타임으로 이동하여 IR을 선택한 다음, 편집을 선택하여 자체 호스팅 **통합 런타임을** 편집할 수 있습니다. 이제 설명을 업데이트하거나, 키를 복사하거나, 새 키를 다시 생성할 수 있습니다.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR을 편집합니다.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR 세부 정보를 편집합니다.":::

관리 센터에서 통합 런타임으로 이동하여 IR을 선택한 다음, 삭제를 선택하여 자체 호스팅 **통합 런타임을** **삭제할** 수 있습니다. IR이 삭제되면 IR을 사용하는 진행 중인 검사가 실패합니다.

## <a name="java-runtime-environment-installation"></a>Java Runtime Environment 설치

Purview와 함께 Self-Hosted Integration Runtime을 사용하여 Parquet 파일을 검사하는 경우 자체 호스팅 IR 머신에 Java Runtime Environment 또는 OpenJDK를 설치해야 합니다.

자체 호스팅 IR을 사용하여 Parquet 파일을 검색할 때 서비스는 먼저 JRE에 대한 레지스트리를 확인하여 Java 런타임을 찾습니다( *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 없는 경우). 두 번째로 *`JAVA_HOME`* OpenJDK에 대한 시스템 변수를 확인합니다.

- **JRE 사용**: 64비트 IR에는 64비트 JRE가 필요합니다. [여기](https://go.microsoft.com/fwlink/?LinkId=808605)서 찾을 수 있습니다.
- **OpenJDK 사용**: IR 버전 3.13부터 지원됩니다. 다른 모든 필수 OpenJDK 어셈블리와 함께 jvm.dll을 자체 호스팅 IR 머신으로 패키지하고, 이에 따라 JAVA_HOME 시스템 환경 변수를 설정합니다.


## <a name="next-steps"></a>다음 단계

- [검사를 통해 삭제된 자산을 검색하는 방법](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Purview와 함께 프라이빗 엔드포인트 사용](catalog-private-link.md)
