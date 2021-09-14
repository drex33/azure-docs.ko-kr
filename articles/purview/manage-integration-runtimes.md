---
title: Integration Runtime 만들기 및 관리
description: 이 문서에서는 Azure Purview에서 Integration Runtime을 만들고 관리하는 단계를 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 1b2748664046c97258ee3414b741075627064bbc
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867483"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기 및 관리

이 문서에서는 Azure Purview에서 데이터 원본을 검사할 수 있는 SHIR(자체 호스팅 통합 런타임)을 만들고 관리하는 방법을 설명합니다.

> [!NOTE]
> Purview Integration Runtime은 동일한 컴퓨터의 Azure Synapse Analytics 또는 Azure Data Factory Integration Runtime으로 공유할 수 없습니다. 분리된 컴퓨터에 설치해야 합니다.

> [!IMPORTANT]
> 2021년 8월 18일 이후에 Azure Purview 계정을 만든 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 버전의 자체 호스팅 통합 런타임을 다운로드하여 설치해야 합니다.

## <a name="create-a-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 만들기

1. Purview Studio 홈페이지의 왼쪽 탐색 창에서 **관리 센터** 를 선택합니다.

2. 왼쪽 창의 **원본 및 스캔** 에서 **통합 런타임** 을 선택한 다음 **+ 새로 만들기** 를 선택합니다.

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="IR을 클릭합니다.":::

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

자체 호스팅 통합 런타임을 **관리 센터** 내부에 위치하는 **통합 런타임** 메뉴로 이동해 IR을 선택하고 편집을 클릭해 편집할 수 있습니다. 이제 설명을 업데이트하거나, 키를 복사하거나, 새 키를 다시 생성할 수 있습니다.

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="IR을 편집합니다.":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="IR 세부 정보를 편집합니다.":::

관리 센터의 **통합 런타임** 메뉴로 이동해 IR을 선택하고 **삭제** 를 클릭해 자체 호스팅 통합 런타임을 삭제할 수 있습니다. IR이 삭제되면 IR을 사용하는 진행 중인 검사가 실패합니다.

## <a name="next-steps"></a>다음 단계

- [검사를 통해 삭제된 자산을 검색하는 방법](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [Purview와 함께 프라이빗 엔드포인트 사용](catalog-private-link.md)
