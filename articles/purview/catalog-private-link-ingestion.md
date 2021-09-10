---
title: 비공개로 안전하게 데이터 원본 검색
description: 이 문서에서는 프라이빗 엔드포인트를 설정하여 제한된 네트워크에서 데이터 원본을 검색하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: cbdf2220d1b4087376bc40db5b7da167144e5d9b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257192"
---
# <a name="scan-your-data-sources-privately-and-securely"></a>비공개로 안전하게 데이터 원본 검색

프라이빗 네트워크, 가상 네트워크 및 프라이빗 엔드포인트의 데이터 원본을 검색할 예정인 경우 Azure Purview 데이터 맵으로 검색되는 데이터 원본에서 이동하는 메타데이터에 대한 네트워크 격리를 보장하기 위해 Azure Purview 수집 프라이빗 엔드포인트를 배포해야 합니다.

Azure Purview는 _수집_ 프라이빗 엔드포인트를 사용하여 Azure 또는 온-프레미스 환경에서 데이터 원본을 검사할 수 있습니다. 수집 프라이빗 엔드포인트를 배포할 때 3가지 프라이빗 엔드포인트 리소스가 배포되고 Azure Purview 관리되는 리소스에 연결되어야 합니다.

- Blob 프라이빗 엔드포인트는 Azure Purview 관리형 스토리지 계정에 연결됩니다.
- 큐 프라이빗 엔드포인트는 Azure Purview 관리형 스토리지 계정에 연결됩니다.
- 네임스페이스 프라이빗 엔드포인트는 Azure Purview 관리형 이벤트 허브 네임스페이스에 연결됩니다.

:::image type="content" source="media/catalog-private-link/purview-private-link-architecture-ingestion.png" alt-text="Azure Purview 및 Private Link 아키텍처를 보여 주는 다이어그램":::

## <a name="deployment-checklist"></a>배포 검사 목록
이 가이드의 배포 옵션 중 하나를 사용하여 Azure Purview 계정에 대한 프라이빗 엔드포인트 수집을 사용하도록 설정합니다.

1. Azure Purview 수집 프라이빗 엔드포인트를 배포할 적절한 Azure 가상 네트워크 및 서브넷을 선택합니다. 다음 옵션 중 하나를 선택합니다.
   - Azure 구독에 [새 가상 네트워크](../virtual-network/quick-create-portal.md)를 배포합니다.
   - Azure 구독에서 기존 Azure 가상 네트워크 및 서브넷을 찾습니다.
  
2. Azure Purview에서 프라이빗 네트워크를 사용하여 데이터 원본을 검색할 수 있도록 수집을 위한 적절한 [DNS 이름 확인 방법](./catalog-private-link-name-resolution.md#deployment-options)을 정의합니다. 다음 옵션 중 하나를 사용할 수 있습니다.
   - 이 가이드에서 자세히 설명하는 단계를 사용하여 새 Azure DNS 영역을 배포합니다.
   - 이 가이드에서 자세히 설명하는 단계를 사용하여 기존 Azure DNS 영역에 필요한 DNS 레코드를 추가합니다.
   - 이 가이드의 단계를 완료한 후 기존 DNS 서버에 필요한 DNS A 레코드를 수동으로 추가합니다.
3. 수집 프라이빗 엔드포인트가 있는 [새 Purview 계정](#option-1---deploy-a-new-azure-purview-account-with-ingestion-private-endpoint)을 배포하거나 [기존 Purview 계정](#option-2---enable-ingestion-private-endpoint-on-existing-azure-purview-accounts)에 대한 수집 프라이빗 엔드포인트를 배포합니다.
4. Azure Purview 수집 프라이빗 엔드포인트가 배포된 동일한 VNet 내에 [자체 호스팅 통합 런타임](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)을 배포하고 등록합니다.
5. 이 가이드를 완료한 후 필요한 경우 DNS 구성을 조정합니다.
6. Azure Purview에 대한 관리 컴퓨터, 자체 호스팅 IR VM 및 데이터 원본 간의 네트워크 및 이름 확인의 유효성을 검사합니다. 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_ingestion_-private-endpoint"></a>옵션 1 - _수집_ 프라이빗 엔드포인트를 사용하는 새 Azure Purview 계정 배포

1. [Azure Portal](https://portal.azure.com)로 이동한 다음, **Purview 계정** 페이지로 이동합니다. **+ 만들기** 를 선택하여 새 Azure Purview 계정을 만듭니다.

2. 기본 정보를 입력하고 **네트워킹** 탭에서 연결 방법을 **프라이빗 엔드포인트** 로 설정합니다. 프라이빗 엔드포인트 사용을 **수집만** 으로 설정합니다.

      :::image type="content" source="media/catalog-private-link/purview-pe-scenario-2-1.png" alt-text="프라이빗 엔드포인트 만들기 페이지 선택 항목을 보여 주는 스크린샷":::

3. 프라이빗 엔드포인트와 페어링하려는 **구독**, **가상 네트워크** 및 **서브넷** 에 대한 세부 정보를 제공하여 수집 프라이빗 엔드포인트를 설정합니다.

4. 선택적으로 **프라이빗 DNS 통합** 을 선택하여 Azure 프라이빗 DNS 영역을 사용합니다.
   
   > [!IMPORTANT]
   > Azure Purview와 데이터 원본 간에 올바른 이름 확인을 허용하려면 올바른 Azure 프라이빗 DNS 영역을 선택하는 것이 중요합니다. 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.

5.  **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지에서 Azure가 구성의 유효성을 검사합니다.

6.  "유효성 검사 통과" 메시지가 표시되면 **만들기** 를 선택합니다.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="계정을 만들기 위한 유효성 검사를 통과했음을 보여 주는 스크린샷":::

## <a name="option-2---enable-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>옵션 2 - 기존 Azure Purview 계정에서 _수집_ 프라이빗 엔드포인트를 사용하도록 설정

1.  [Azure Portal](https://portal.azure.com)로 이동한 다음 Azure Purview 계정을 클릭하고 **설정** 에서 **네트워킹** 을 선택한 다음, **수집 프라이빗 엔드포인트 연결** 을 선택합니다.

2. 수집 프라이빗 엔드포인트 연결에서 **+ 새로 만들기** 를 선택하여 새 수집 프라이빗 엔드포인트를 만듭니다.

3. 기본 정보를 입력하고 기존 가상 네트워크와 서브넷 세부 정보를 선택합니다. 선택적으로 **프라이빗 DNS 통합** 을 선택하여 Azure 프라이빗 DNS 영역을 사용합니다. 
   
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="프라이빗 엔드포인트 정보 채우기를 보여 주는 스크린샷":::
   
   > [!IMPORTANT]
   > Azure Purview와 데이터 원본 간에 올바른 이름 확인을 허용하려면 올바른 Azure 프라이빗 DNS 영역을 선택하는 것이 중요합니다. 기존 Azure 프라이빗 DNS 영역을 사용하거나 나중에 수동으로 DNS 서버에서 DNS 레코드를 만들 수도 있습니다. 
   > 
   >자세한 내용은 [프라이빗 엔드포인트에 대한 DNS 이름 확인 구성](./catalog-private-link-name-resolution.md)을 참조하세요.


4. **만들기** 를 선택하여 설정을 완료합니다.

> [!NOTE]
> 이전 단계에서 설명한 Azure Purview 포털 환경을 통해서만 수집 프라이빗 엔드포인트를 만들 수 있습니다. Private Link 센터에서는 만들 수 없습니다.

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>자체 호스팅 IR(통합 런타임)을 배포하고 데이터 원본을 검사합니다.
Azure Purview에 대한 수집 프라이빗 엔드포인트를 배포한 후에는 하나 이상의 자체 호스팅 IR(통합 런타임)을 설정하고 등록해야 합니다.

- Microsoft SQL Server, Oracle, SAP 등의 모든 온-프레미스 원본 유형은 현재 자체 호스팅 IR 기반 검사를 통해서만 지원됩니다. 자체 호스팅 IR은 개인 네트워크 내에서 실행된 다음, Azure에서 가상 네트워크와 피어링되어야 합니다. 
   
- Azure Blob Storage 및 Azure SQL Database와 같은 모든 Azure 원본 유형의 경우 Azure Purview 수집 프라이빗 엔드포인트와 동일한 VNet에 배포되는 자체 호스팅 통합 런타임을 사용하여 검사를 실행하도록 명시적으로 선택해야 합니다. 

자체 호스팅 IR을 설정하려면 [자체 호스팅 통합 런타임 만들기 및 관리](manage-integration-runtimes.md)의 단계를 수행합니다. 그런 다음, **Integration Runtime을 통해 연결** 드롭다운 목록에서 자체 호스트 IR을 선택하여 네트워크 격리를 보장하도록 Azure 원본에 대해 검사를 설정합니다.
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="자체 호스팅 IR을 사용하여 Azure 검사를 실행하는 것을 보여 주는 스크린샷":::

> [!IMPORTANT]
> 2021년 8월 18일 이후에 Azure Purview 계정을 만든 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 최신 버전의 자체 호스팅 통합 런타임을 다운로드하여 설치해야 합니다.
> 
## <a name="next-steps"></a>다음 단계

-  [프라이빗 엔드포인트에 대한 해결 확인](./catalog-private-link-name-resolution.md)
-  [Azure Purview에서 데이터 원본 관리](./manage-data-sources.md)
-  [Azure Purview 계정의 프라이빗 엔드포인트 구성 문제 해결](./catalog-private-link-troubleshoot.md)
