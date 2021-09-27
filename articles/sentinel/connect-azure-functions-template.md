---
title: Azure Functions를 사용하여 Azure Sentinel을 데이터 원본에 연결 | Microsoft Docs
description: Azure Functions를 사용하여 데이터 원본에서 Azure Sentinel 데이터를 얻는 데이터 커넥터를 구성하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/07/2021
ms.author: yelevin
ms.openlocfilehash: b6e38d6ce5944d09a089404ae1fe2ca81a951c18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124781307"
---
# <a name="use-azure-functions-to-connect-azure-sentinel-to-your-data-source"></a>Azure Functions를 사용하여 Azure Sentinel을 데이터 원본에 연결

[PowerShell](../azure-functions/functions-reference-powershell.md) 또는 Python과 같은 다양한 코딩 언어와 함께 [Azure Functions](../azure-functions/functions-overview.md)를 사용하여 호환되는 데이터 원본의 REST API 엔드포인트에 대한 서버리스 커넥터를 만들 수 있습니다. 그런 다음, Azure 함수 앱을 사용하면 Azure Sentinel 데이터 원본의 REST API에 연결하여 로그를 끌어올 수 있습니다.

이 문서에서는 Azure 함수 앱을 사용하기 위해 Azure Sentinel을 구성하는 방법을 설명합니다. 원본 시스템을 구성해야 할 수도 있으며, 포털의 각 데이터 커넥터 페이지에서 공급업체 및 제품별 정보 링크를 찾거나 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지에서 서비스에 대한 섹션을 찾을 수 있습니다.




> [!NOTE]
> - Azure Sentinel에 수집되면, 데이터는 Azure Sentinel을 실행 중인 작업 영역의 지리적 위치에 저장됩니다.
>
>     장기 보존의 경우, 데이터를 Azure Data Explorer에 저장할 수도 있습니다. 자세한 내용은 [Azure Data Explorer 통합](store-logs-in-azure-data-explorer.md)을 참조하세요.
>
> - Azure Functions를 사용하여 Azure Sentinel 데이터를 수집하면 추가 데이터 수집 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/) 페이지를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure Functions를 사용하여 데이터 원본에 Azure Sentinel을 연결하고 해당 로그를 Azure Sentinel에 끌어오기 전에, 다음 권한 및 자격 증명이 있는지 확인합니다.

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- 함수 앱을 작성할 수 있는 Azure Functions에 대한 읽기 및 쓰기 권한이 있어야 합니다. [Azure Functions에 대해 자세히 알아보세요](../azure-functions/index.yml).

- 또한 제품 API에 액세스하기 위한 자격 증명(사용자 이름 및 암호, 토큰, 키 또는 기타 조합)이 필요합니다. 엔드포인트 URI와 같은 다른 API 정보가 필요할 수도 있습니다.

    자세한 내용은 연결하려는 서비스에 대한 설명서 및 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션을 참조하세요.

## <a name="configure-and-connect-your-data-source"></a>데이터 원본 구성 및 연결

> [!NOTE]
> - 작업 영역 및 API 권한 부여 키 또는 토큰을 Azure Key Vault에 안전하게 저장할 수 있습니다. Azure Key Vault는 키 값을 저장하고 검색하는 안전한 메커니즘을 제공합니다. [지침에 따라](../app-service/app-service-key-vault-references.md) Azure 함수 앱에서 Azure Key Vault를 사용합니다.
>
> - 일부 데이터 커넥터는 정상적으로 작동하기 위해 [Kusto Function](/azure/data-explorer/kusto/query/functions/user-defined-functions)을 기반으로 하는 파서를 사용합니다. Kusto 함수 및 별칭을 생성하는 지침에 대한 링크는 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션을 참조하세요.


### <a name="step-1---get-your-source-systems-api-credentials"></a>1단계 - 원본 시스템의 API 자격 증명 가져오기

원본 시스템의 지침에 따라 **API 자격 증명/권한 부여 키/토큰** 을 가져옵니다. 나중에 사용할 수 있도록 텍스트 파일에 복사하여 붙여 넣습니다.

필요한 정확한 자격 증명에 대한 세부 정보 및 해당 자격 증명을 찾거나 만들기 위한 제품 지침에 대한 링크는 포털의 데이터 커넥터 페이지 및 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션에서 찾을 수 있습니다.

원본 시스템에서 로깅 또는 기타 설정을 구성해야 할 수도 있습니다. 관련 지침은 이전 단락의 지침과 함께 찾을 수 있습니다.
### <a name="step-2---deploy-the-connector-and-the-associated-azure-function-app"></a>2단계 - 커넥터 및 연결된 Azure 함수 앱 배포

#### <a name="choose-a-deployment-option"></a>배포 옵션 선택

# <a name="azure-resource-manager-arm-template"></a>[ARM(Azure Resource Manager) 템플릿](#tab/ARM)

이 메서드는 ARM 템플릿을 사용하여 Azure Function 기반 커넥터의 자동화된 배포를 제공합니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 선택합니다. 목록에서 Azure Functions 기반 커넥터를 선택한 다음, **커넥터 페이지 열기** 를 선택합니다.

1. **구성** 에서 Azure Sentinel **작업 영역 ID** 와 **기본 키** 를 복사하여 옆에 붙여넣습니다.

1. **Azure에 배포** 를 선택합니다. (단추를 찾으려면 아래로 스크롤해야 할 수도 있습니다.)

1. **사용자 지정 배포** 화면이 나타납니다.
    - **구독**, **리소스 그룹**, 함수 앱을 배포할 **지역** 을 선택합니다.

    - 위의 [1단계](#step-1---get-your-source-systems-api-credentials)에서 저장한 API 자격 증명/권한 부여 키/토큰을 입력합니다.

    - 복사하여 따로 보관한 Azure Sentinel **작업 영역 ID** 및 **작업 영역 키**(기본 키)를 입력합니다.

        > [!NOTE]
        > 위의 값에 Azure Key Vault 암호를 사용하는 경우 문자열 값 대신 `@Microsoft.KeyVault(SecretUri={Security Identifier})`스키마를 사용합니다. 자세한 내용은 Key Vault 참조 설명서를 참조하세요.

    - **사용자 지정 배포** 화면에서 양식의 다른 필드를 작성합니다. 포털의 데이터 커넥터 페이지 또는 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션을 참조하세요.

    - **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

# <a name="manual-deployment-with-powershell"></a>[PowerShell을 통해 수동 배포](#tab/MPS)

다음 단계별 지침을 사용하여 PowerShell 함수를 사용하는 Azure Functions 기반 커넥터를 수동으로 배포합니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 선택합니다. 목록에서 Azure Functions 기반 커넥터를 선택한 다음, **커넥터 페이지 열기** 를 선택합니다.

1. **구성** 에서 Azure Sentinel **작업 영역 ID** 와 **기본 키** 를 복사하여 옆에 붙여넣습니다.

1. **함수 앱 생성**
    1. Azure Portal에 로그인한 다음, **함수 앱** 을 검색하고 선택합니다.

    1. **함수 앱** 페이지에서 **생성** 을 선택합니다. **함수 앱 생성** 마법사가 열립니다.

    1. **기본 사항** 탭에서 다음을 수행합니다.
        - **구독** 및 **리소스 그룹** 을 선택합니다.
        - 함수 앱에 이름을 지정합니다.
        - *PowerShell Core* 에 **런타임 스택** 을 설정합니다.
        - 적절한 버전 번호를 선택합니다.
        - 배포할 **지역** 을 선택하고 **다음: 호스팅** 을 선택합니다.

    1. **호스팅** 탭에서 다음을 수행합니다.
        - 사용할 **스토리지 계정** 을 선택하거나 새로 생성합니다.
        - **플랜 형식** 필드에서 *사용(서버리스)* 을 선택합니다.

    1. 필요한 다른 구성을 변경한 다음, **검토 + 생성** 을 선택합니다.

    1. "유효성 검사 통과" 메시지가 표시되면 **생성** 을 선택합니다.

    1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.

1. **함수 앱 코드 가져오기**
    1. 새로 만든 함수 앱의 탐색 메뉴에서 **Functions** 를 선택합니다.

    1. **Functions** 페이지에서 **+추가** 를 선택합니다.
    
    1. **함수 추가** 창에서 **타이머** 트리거를 선택합니다.

    1. 함수의 고유 이름을 입력하고 *cron* 식을 입력하여 일정을 지정합니다. 기본 간격은 5분입니다.

    1. 함수가 만들어지면 왼쪽 창에서 **코드 + 테스트** 를 선택합니다.

    1. 원본 시스템의 공급업체에서 제공하는 함수 앱 코드를 다운로드하고 복사하여 **함수 앱** *run.ps1* 편집기에 붙여 넣어 편집기에 기본값으로 있는 내용을 대체합니다. 커넥터 페이지 또는 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션에서 다운로드 링크를 찾을 수 있습니다.

    1. **저장** 을 선택합니다.

1. **함수 앱 구성**
    1. 함수 앱 페이지에서, 탐색 메뉴의 **설정** 아래의 **구성** 을 선택합니다.

    1. **애플리케이션 설정** 탭에서 **+ 새 애플리케이션 설정** 을 선택합니다.

    1. 제품에 대해 지정된 애플리케이션 설정을 대/소문자 구분 문자열 값과 함께 개별적으로 추가합니다. 데이터 커넥터 페이지 또는 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션을 참조하세요.

        > [!TIP]
        > 전용 클라우드를 사용하는 경우, *logAnalyticsUri* 애플리케이션 설정을 사용하여 로그 분석 API 엔드포인트를 재정의합니다. 예를 들어, 퍼블릭 클라우드를 사용하는 경우, 값을 비워 두고, Azure GovUS 클라우드 환경의 경우, `https://<CustomerId>.ods.opinsights.azure.us` 형식으로 값을 지정합니다.
        >

# <a name="manual-deployment-with-python"></a>[Python을 통해 수동 배포](#tab/MPY)

다음 단계별 지침을 사용하여 Python 함수를 사용하는 Azure Functions 기반 커넥터를 수동으로 배포합니다. 이러한 종류의 배포에는 Visual Studio Code가 필요합니다.

1. Azure Sentinel 포털에서 **데이터 커넥터** 를 선택합니다. 목록에서 Azure Functions 기반 커넥터를 선택한 다음, **커넥터 페이지 열기** 를 선택합니다.

1. **구성** 에서 Azure Sentinel **작업 영역 ID** 와 **기본 키** 를 복사하여 옆에 붙여넣습니다.

1. **함수 앱 배포**

    > [!NOTE]
    > Azure Function 개발을 위한 [Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md)(VS Code)를 준비해야 합니다.

    1. 데이터 커넥터 페이지 및 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션에 제공된 링크를 사용하여 Azure 함수 앱 파일을 다운로드합니다. 로컬 개발 컴퓨터로 보관 파일을 추출합니다.

    1. VS Code를 시작합니다. 메뉴 모음에서 **파일 > 폴더 열기** 를 선택합니다.

    1. 보관 파일에서 추출된 파일에서 최상위 폴더를 선택합니다.

    1. VS Code 작업 모음(왼쪽)에서 Azure 아이콘을 선택합니다. **Azure: Functions** 영역에서 **함수 앱에 배포** 단추를 선택합니다. 

        > [!NOTE]
        > 아직 로그인하지 않은 경우, 작업 막대에서 Azure 아이콘을 선택합니다. 그리고 **Azure: Functions** 영역에서 **Azure에 로그인** 을 선택합니다.  
        > 이미 로그인한 경우 다음 단계로 이동합니다.

    1. 프롬프트에서 다음 정보를 제공합니다.
        - **폴더 선택**: 작업 영역에서 폴더를 선택하거나 함수 앱을 포함하는 폴더를 찾습니다.
        - **구독 선택**: 사용할 구독을 선택합니다.
        - **Azure에서 새 함수 앱 생성** 을 선택합니다. (**고급** 옵션을 선택하지 마세요.)
        - **함수 앱의 전역적으로 고유한 이름 입력**:URL 경로에서 유효한 이름을 함수 앱에 지정합니다. 선택한 이름의 유효성을 검사하여 Azure Functions 전체에서 고유한지 확인합니다.
        - **런타임 선택**: *Python 3.8* 를 선택합니다.

    1. 배포가 시작됩니다. 함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다.

    1. 구성을 위해 함수 앱의 페이지로 돌아갑니다.

1. **함수 앱 구성**
    1. 함수 앱 페이지에서, 탐색 메뉴의 **설정** 아래의 **구성** 을 선택합니다.

    1. **애플리케이션 설정** 탭에서 **+ 새 애플리케이션 설정** 을 선택합니다.

    1. 제품에 대해 지정된 애플리케이션 설정을 대/소문자 구분 문자열 값과 함께 개별적으로 추가합니다. 데이터 커넥터 페이지 또는 추가할 애플리케이션 설정에 대한 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션을 참조하세요.

        - 전용 클라우드를 사용하는 경우, *logAnalyticsUri* 애플리케이션 설정을 사용하여 로그 분석 API 엔드포인트를 재정의합니다. 예를 들어, 퍼블릭 클라우드를 사용하는 경우, 값을 비워 두고, Azure GovUS 클라우드 환경의 경우, `https://<CustomerId>.ods.opinsights.azure.us` 형식으로 값을 지정합니다.

---

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면, 데이터는 [Azure Sentinel 데이터 커넥터 참조](data-connectors-reference.md) 페이지의 서비스에 대한 섹션에 나열된 테이블의 *CustomLogs* 아래의 **로그** 에 표시됩니다.

데이터를 쿼리하려면, 쿼리 창에서 해당 테이블 이름 또는 관련 Kusto 함수 별칭 중 하나를 입력합니다.

몇 가지 유용한 샘플 쿼리를 보려면 커넥터 페이지에서 **다음 단계** 탭을 참조하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Functions 기반 커넥터를 사용하여 데이터 원본에 Azure Sentinel을 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](./get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](./monitor-your-data.md)하여 데이터를 모니터링합니다.