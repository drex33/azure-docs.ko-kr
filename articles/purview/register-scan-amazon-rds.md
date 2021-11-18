---
title: Azure Purview용 Amazon RDS 다중 클라우드 검사 커넥터
description: 이 방법 가이드에서는 Microsoft SQL 및 PostgreSQL 데이터를 포함하여 Amazon RDS 데이터베이스를 검사하는 방법에 대해 자세히 설명합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: fab51bd25489527c36e37d5f60233fa62256367c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132722585"
---
# <a name="amazon-rds-multi-cloud-scanning-connector-for-azure-purview-public-preview"></a>Azure Purview용 Amazon RDS 다중 클라우드 검사 커넥터(공개 미리 보기)

Azure Purview용 다중 클라우드 검색 커넥터를 사용하면 Azure Storage 서비스 외에도 Amazon Web Services 비롯한 클라우드 공급자에서 조직 데이터를 탐색할 수 있습니다.

이 문서에서는 Azure Purview를 사용하여 Microsoft SQL 및 PostgreSQL 데이터베이스를 포함하여 Amazon RDS에 현재 저장된 정형 데이터를 검색하고 데이터에 존재하는 중요한 정보 유형을 검색하는 방법을 설명합니다. 또한 손쉬운 정보 보호 및 데이터 규정 준수를 위해 데이터가 현재 저장되어 있는 Amazon RDS 데이터베이스를 식별하는 방법도 알아봅니다.

이 서비스의 경우 Purview를 사용하여 AZURE Purview용 다중 클라우드 스캔 커넥터가 실행되는 AWS에 대한 보안 액세스 권한이 있는 Microsoft 계정 제공합니다. Azure Purview용 다중 클라우드 검색 커넥터는 Amazon RDS 데이터베이스에 대한 이 액세스를 사용하여 데이터를 읽은 다음, 메타데이터 및 분류만 포함한 검사 결과를 Azure에 다시 보고합니다. Purview 분류 및 레이블 지정 보고서를 사용하여 데이터 스캔 결과를 분석하고 검토할 수 있습니다.

> [!IMPORTANT]
> Azure Purview용 다중 클라우드 검사 커넥터는 Azure Purview에 대한 별도의 추가 기능입니다. Azure Purview용 다중 클라우드 스캔 커넥터의 약관은 Microsoft Azure Services를 획득한 계약에 포함되어 있습니다. 자세한 내용은 https://azure.microsoft.com/support/legal/ 에서 Microsoft Azure 법적 정보를 참조하세요.
>

> [!IMPORTANT]
> Amazon RDS에 대한 Purview 지원은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="purview-scope-for-amazon-rds"></a>Amazon RDS에 대한 Purview 범위

- **지원되는 데이터베이스 엔진:** Amazon RDS 구조적 데이터 스토리지는 여러 데이터베이스 엔진을 지원합니다. Azure Purview는 Microsoft SQL 및 PostgreSQL 기반의 Amazon RDS를 지원합니다.

- **지원되는 최대 열:** 300개 이상의 열이 있는 RDS 테이블 검색은 지원되지 않습니다.

- **공용 액세스 지원:** Purview는 AWS의 VPC Private Link 검사만 지원하며 공용 액세스 검색은 포함하지 않습니다.

- **지원되는 지역:** Purview는 다음 AWS 지역에 있는 Amazon RDS 데이터베이스만 지원합니다.

    - 미국 동부(오하이오)
    - 미국 동부(북부 버지니아)
    - 미국 서부(북부 캘리포니아)
    - 미국 서부(오리건)
    - 유럽(프랑크푸르트)
    - 아시아 태평양(도쿄)
    - 아시아 태평양(싱가포르)
    - 아시아 태평양(시드니)
    - 유럽(아일랜드)
    - 유럽(런던)
    - 유럽(파리)

- **IP 주소 요구 사항:** RDS 데이터베이스에 고정 IP 주소가 있어야 합니다. 고정 IP 주소는 이 문서에 설명된 대로 AWS PrivateLink를 구성하는 데 사용됩니다.

- **알려진 문제:** 다음 기능은 현재 지원되지 않습니다.

    - **연결 테스트** 단추입니다. 검사 상태 메시지는 연결 설정과 관련된 오류를 나타냅니다.
    - 데이터베이스에서 검색할 특정 테이블 선택
    - [데이터 계보.](concept-data-lineage.md)

자세한 내용은 다음을 참조하세요.

- [Azure Purview를 사용하여 리소스 할당량 관리 및 늘리기](how-to-manage-quotas.md)
- [Azure Purview에서 지원되는 데이터 원본 및 파일 형식](sources-and-scans.md)
- [Purview 계정에 대한 프라이빗 엔드포인트 사용](catalog-private-link.md)

## <a name="prerequisites"></a>필수 구성 요소

Amazon RDS 데이터베이스를 Purview 데이터 원본으로 추가하고 RDS 데이터를 검사하기 전에 다음 필수 구성을 수행했는지 확인합니다.

> [!div class="checklist"]
> * Azure Purview 데이터 원본 관리자여야 합니다.
> * Purview 계정이 필요합니다. [Azure Purview 계정 인스턴스(아직](create-catalog-portal.md)없는 경우)를 만듭니다.
> * 데이터가 있는 Amazon RDS PostgreSQL 또는 Microsoft SQL 데이터베이스가 필요합니다.


## <a name="configure-aws-to-allow-purview-to-connect-to-your-rds-vpc"></a>Purview가 RDS VPC에 연결할 수 있도록 AWS 구성

Azure Purview는 데이터베이스가 동일한 VPC 내에서만 RDS 데이터베이스에 액세스할 수 있는 VPC(가상 프라이빗 클라우드)에서 호스트되는 경우에만 검사를 지원합니다.

Azure Purview 서비스에 대한 Azure 다중 클라우드 스캔 커넥터는 AWS에서 별도의 Microsoft 계정 실행됩니다. RDS 데이터베이스를 검사하려면 Microsoft AWS 계정이 VPC의 RDS 데이터베이스에 액세스할 수 있어야 합니다. 이 액세스를 허용하려면 RDS VPC(고객 계정)에서 Azure Purview용 다중 클라우드 스캔 커넥터가 실행되는 VPC(Microsoft 계정) 간에 [AWS PrivateLink를](https://aws.amazon.com/privatelink/) 구성해야 합니다.

다음 다이어그램에서는 고객 계정과 Microsoft 계정 구성 요소를 보여 줍니다. 계정에서 AZURE Purview용 다중 클라우드 스캔 커넥터가 Microsoft 계정 실행되는 VPC에 대한 연결을 사용하도록 설정하기 위해 만들어야 하는 구성 요소가 노란색으로 강조 표시되어 있습니다.

:::image type="content" source="media/register-scan-amazon-rds/vpc-architecture.png" alt-text="VPC 아키텍처의 Azure Purview 서비스에 대한 다중 클라우드 검사 커넥터 다이어그램." border="false":::


> [!IMPORTANT]
> 고객의 프라이빗 네트워크에 대해 생성된 모든 AWS 리소스는 고객의 AWS 청구서에 추가 비용이 발생합니다.
>

### <a name="configure-aws-privatelink-using-a-cloudformation-template"></a>CloudFormation 템플릿을 사용하여 AWS PrivateLink 구성

다음 절차에서는 AWS CloudFormation 템플릿을 사용하여 AWS PrivateLink를 구성하여 Purview가 RDS VPC에 연결할 수 있도록 하는 방법을 설명합니다. 이 절차는 AWS에서 수행되며 AWS 관리자를 위한 것입니다.

이 CloudFormation 템플릿은 Azure [GitHub 리포지토리](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS)에서 다운로드할 수 있으며 대상 그룹, 부하 분산기 및 엔드포인트 서비스를 만드는 데 도움이 됩니다.

- **동일한 VPC에 여러 RDS 서버가 있는 경우** [모든 RDS 서버 IP 주소 및 포트를 지정하여](#parameters)이 절차를 한 번 수행합니다. 이 경우 CloudFormation 출력에는 각 RDS 서버에 대해 서로 다른 포트가 포함됩니다.

    [Purview에서 이러한 RDS 서버를 데이터 원본으로 등록하는](#register-an-amazon-rds-data-source)경우 실제 RDS 서버 포트 대신 출력에 포함된 포트를 사용합니다.

- **여러 VPC에 RDS 서버가 있는 경우 각 VPC에** 대해 이 절차를 수행합니다.


> [!TIP]
> 이 절차를 수동으로 수행할 수도 있습니다. 자세한 내용은 [AWS PrivateLink 수동(고급) 구성을](#configure-aws-privatelink-manually-advanced)참조하세요.
>

**CloudFormation 템플릿을 사용하여 RDS 데이터베이스를 준비하려면:**

1. Azure GitHub 리포지토리에서 이 절차에 필요한 CloudFormation [RDSPrivateLink_CloudFormation.yaml](https://github.com/Azure/Azure-Purview-Starter-Kit/tree/main/Amazon/AWS/RDS) 템플릿을 다운로드합니다.

    1. [연결된 GitHub 페이지의](https://github.com/Azure/Azure-Purview-Starter-Kit/blob/main/Amazon/AWS/RDS/Amazon_AWS_RDS_PrivateLink_CloudFormation.zip)오른쪽에서 **다운로드를** 선택하여 zip 파일을 다운로드합니다.

    1. **RDSPrivateLink_CloudFormation.yaml** 파일에 액세스할 수 있도록 .zip 파일을 로컬 위치에 추출합니다.

1. AWS 포털에서 **CloudFormation** 서비스로 이동합니다. 페이지의 오른쪽 위에서 새 리소스를 사용하여 **스택**  >  **만들기(표준)** 를 선택합니다.

1. 필수 **조건 - 템플릿 준비** 페이지에서 **템플릿 준비 완료를** 선택합니다.

1. 템플릿 **지정** 섹션에서 템플릿 **파일 업로드** 선택합니다. **파일 선택을 선택하고** 이전에 다운로드한 **RDSPrivateLink_CloudFormation.yaml** 파일로 이동한 후 **다음을** 선택하여 계속합니다.

1. 스택 이름 섹션에서 **스택의** 이름을 입력합니다. 이 이름은 프로세스에서 나중에 만든 리소스 이름에 대해 자동으로 추가된 접미사와 함께 사용됩니다. 따라서

    - 스택에 의미 있는 이름을 사용해야 합니다.
    - 스택 이름이 19자 이하인지 확인합니다.

1. <a name="parameters"></a>매개 **변수** 영역에서 AWS의 RDS 데이터베이스 페이지에서 사용할 수 있는 데이터를 사용하여 다음 값을 입력합니다.

    |이름  |설명  |
    |---------|---------|
    |**엔드포인트 & 포트**    | RDS 엔드포인트 URL 및 포트의 확인된 IP 주소를 입력합니다. 예: `192.168.1.1:5432` <br><br>- **RDS 프록시가 구성된 경우** 관련 데이터베이스에 대한 프록시의 읽기/쓰기 엔드포인트 IP 주소를 사용합니다. IP 주소가 고정적이기 때문에 Purview로 작업할 때 RDS 프록시를 사용하는 것이 좋습니다.<br><br>- **동일한 VPC 뒤에 여러 엔드포인트가 있는 경우** 최대 10개의 쉼표로 구분된 엔드포인트를 입력합니다.  이 경우 단일 부하 분산 장치가 VPC에 생성되어 AWS의 Azure Purview용 Amazon RDS 다중 클라우드 스캔 커넥터에서 VPC의 모든 RDS 엔드포인트로 연결할 수 있습니다.       |
    |**네트워킹**     | VPC ID 입력        |
    |**VPC IPv4 CIDR**     | VPC의 CIDR 값을 입력합니다. RDS 데이터베이스 페이지에서 VPC 링크를 선택하여 이 값을 찾을 수 있습니다. 예: `192.168.0.0/16`        |
    |**서브넷**     |VPC와 연결된 서브넷을 Select all.         |
    |**보안**     | RDS 데이터베이스와 연결된 VPC 보안 그룹을 선택합니다.         |
    |     |         |

    완료되면 **다음을** 선택하여 계속합니다.

1. 스택 구성 **옵션의** 설정은 이 절차에서 선택 사항입니다.

    사용자 환경에 필요한 대로 설정을 정의합니다. 자세한 내용은 자세한 **정보** 링크를 선택하여 AWS 설명서에 액세스합니다. 완료되면 **다음을** 선택하여 계속합니다.

1. **검토** 페이지에서 선택한 값이 사용자 환경에 맞는지 확인합니다. 필요한 내용을 변경한 다음, 완료되면 **스택 만들기를** 선택합니다.

1. 리소스가 만들어지는지 감시합니다. 완료되면 이 절차에 대한 관련 데이터가 다음 탭에 표시됩니다.

    - **이벤트:** CloudFormation 템플릿에서 수행하는 이벤트/활동을 표시합니다.
    - **리소스:** 새로 만든 대상 그룹, 부하 분산기 및 엔드포인트 서비스를 표시합니다.
    - **출력:** **ServiceName** 값과 RDS 서버의 IP 주소 및 포트를 표시합니다.

        여러 RDS 서버를 구성한 경우 다른 포트가 표시됩니다. 이 경우 [RDS 데이터베이스를](#register-an-amazon-rds-data-source) Purview 데이터 원본으로 등록할 때 실제 RDS 서버 포트 대신 여기에 표시된 포트를 사용합니다.

1. **출력** 탭에서 **ServiceName** 키 값을 클립보드에 복사합니다.

    [RDS 데이터베이스를](#register-an-amazon-rds-data-source) Purview 데이터 원본으로 등록할 때 Azure Purview 포털에서 **ServiceName** 키 값을 사용합니다. 엔드포인트 **서비스를 통해 프라이빗 네트워크에 커넥트** **ServiceName** 키를 입력합니다.

## <a name="register-an-amazon-rds-data-source"></a>Amazon RDS 데이터 원본 등록

**Amazon RDS 서버를 Azure Purview 데이터 원본으로 추가하려면:**

1. Azure Purview에서 **데이터 맵** 페이지로 이동하고 **등록 등록** ![ 아이콘을 선택합니다. ](./media/register-scan-amazon-s3/register-button.png)

1.  **원본** 페이지에서 **등록을 선택합니다.** 오른쪽에 표시되는 **원본 등록** 페이지에서 **데이터베이스** 탭을 선택한 **다음, Amazon RDS(PostgreSQL) 또는 Amazon RDS(SQL)** 를 선택합니다. 

    :::image type="content" source="media/register-scan-amazon-rds/register-amazon-rds.png" alt-text="Amazon RDS(PostgreSQL)를 선택하는 원본 등록 페이지의 스크린샷.":::

1. 원본에 대한 세부 정보를 입력합니다.

    |필드  |설명  |
    |---------|---------|
    |**이름**     |  원본에 대한 의미 있는 이름(예: )을 입력합니다. `AmazonPostgreSql-Ups`       |
    |**서버 이름**     | 다음 구문에서 RDS 데이터베이스의 이름을 입력합니다. `<instance identifier>.<xxxxxxxxxxxx>.<region>.rds.amazonaws.com`  <br><br>Amazon RDS 포털에서 이 URL을 복사하고 URL에 AWS 지역이 포함되어 있는지 확인하는 것이 좋습니다.      |
    |**포트**     |  RDS 데이터베이스에 연결하는 데 사용되는 포트를 입력합니다.<br><br>        - PostgreSQL: `5432`<br> - Microsoft SQL:`1433`<br><br>        [CloudFormation 템플릿을 사용하여 AWS PrivateLink를 구성하고](#configure-aws-privatelink-using-a-cloudformation-template) 동일한 VPC에 여러 RDS 서버가 있는 경우 읽기 RDS 서버 포트 대신 CloudFormation **출력** 탭에 나열된 포트를 사용합니다.       |
    |**엔드포인트 서비스를 통해 프라이빗 네트워크에 커넥트**     |  [이전 절차의](#configure-aws-privatelink-using-a-cloudformation-template)끝에서 가져온 **ServiceName** 키 값을 입력합니다. <br><br>RDS 데이터베이스를 수동으로 준비한 경우 [5단계: 엔드포인트 서비스 만들기의](#step-5-create-an-endpoint-service)끝에서 얻은 서비스 **이름** 값을 사용합니다.       |
    |**컬렉션(선택** 사항)     |  데이터 원본을 추가할 컬렉션을 선택합니다. 자세한 내용은 [Azure Purview에서 데이터 원본 관리(미리 보기)](manage-data-sources.md)를 참조하세요.       |
    |     |         |

1. 계속 진행할 준비가 되면 **등록을** 선택합니다.

RDS 데이터 원본이 원본 맵 또는 목록에 표시됩니다. 예를 들어 다음과 같습니다.

:::image type="content" source="media/register-scan-amazon-rds/amazon-rds-in-sources.png" alt-text="원본 페이지의 Amazon RDS 데이터 원본 스크린샷.":::

## <a name="create-purview-credentials-for-your-rds-scan"></a>RDS 검사에 대한 Purview 자격 증명 만들기

Amazon RDS 데이터 원본에 지원되는 자격 증명에는 사용자 이름/암호 인증만 포함되며 암호는 Azure KeyVault 비밀에 저장됩니다.

### <a name="create-a-secret-for-your-rds-credentials-to-use-in-purview"></a>Purview에서 사용할 RDS 자격 증명에 대한 비밀 만들기

1.  암호를 비밀로 Azure KeyVault에 추가합니다. 자세한 내용은 [Azure Portal 사용하여 Key Vault 비밀 설정 및 검색을 참조하세요.](../key-vault/secrets/quick-create-portal.md)

1.  **Get** 및 **List** 권한을 사용하여 KeyVault에 액세스 정책을 추가합니다. 예를 들어 다음과 같습니다.

    :::image type="content" source="media/register-scan-amazon-rds/keyvault-for-rds.png" alt-text="Purview의 RDS에 대한 액세스 정책 스크린샷.":::

    정책의 보안 주체를 정의할 때 Purview 계정을 선택합니다. 예를 들어 다음과 같습니다.

    :::image type="content" source="media/register-scan-amazon-rds/select-purview-as-principal.png" alt-text="Purview 계정을 보안 주체로 선택하는 스크린샷.":::

    **저장을** 선택하여 액세스 정책 업데이트를 저장합니다. 자세한 내용은 [Azure Key Vault 액세스 정책 할당을 참조하세요.](/azure/key-vault/general/assign-access-policy-portal)

1. Azure Purview에서 KeyVault 연결을 추가하여 KeyVault를 RDS 비밀과 Purview에 연결합니다. 자세한 내용은 [Azure Purview의 원본 인증에 대한 자격 증명을 참조하세요.](manage-credentials.md)

### <a name="create-your-purview-credential-object-for-rds"></a>RDS에 대한 Purview 자격 증명 개체 만들기

Azure Purview에서 Amazon RDS 계정을 검색할 때 사용할 자격 증명 개체를 만듭니다.

1. Purview **관리** 영역에서 보안을 선택하고 자격 증명 새 에 **액세스합니다.**  >    >  

1. 인증 방법으로 **SQL 인증을** 선택합니다. 그런 다음, Key Vault 및 비밀의 이름을 포함하여 RDS 자격 증명이 저장되는 Key Vault 대한 세부 정보를 입력합니다.

    예를 들어 다음과 같습니다.

    :::image type="content" source="media/register-scan-amazon-rds/new-credential-for-rds.png" alt-text="RDS에 대한 새 자격 증명의 스크린샷.":::

자세한 내용은 [Azure Purview의 원본 인증에 대한 자격 증명을 참조하세요.](manage-credentials.md)

## <a name="scan-an-amazon-rds-database"></a>Amazon RDS 데이터베이스 검사

RDS 데이터베이스에 대한 Azure Purview 검사를 구성하려면 다음을 수행합니다.

1.  Purview **원본** 페이지에서 검색할 Amazon RDS 데이터 원본을 선택합니다.

1.  :::image type="icon" source="media/register-scan-amazon-s3/new-scan-button.png" border="false"::: **새 검색을** 선택하여 검사 정의를 시작합니다. 오른쪽에 열리는 창에서 다음 세부 정보를 입력한 다음, **계속을** 선택합니다.

    - **이름:** 검색에 사용할 의미 있는 이름을 입력합니다.
    - **데이터베이스 이름:** 검색할 데이터베이스의 이름을 입력합니다. Purview 외부에서 사용할 수 있는 이름을 찾고 등록된 RDS 서버의 각 데이터베이스에 대해 별도의 검사를 만들어야 합니다.
    - **자격 증명:** RDS 데이터베이스에 액세스하기 위해 Azure Purview용 다중 클라우드 스캔 커넥터에 대해 이전에 만든 자격 증명을 선택합니다.

1.  검색 **규칙 집합 선택** 창에서 사용할 검사 규칙 집합을 선택하거나 새 검사 규칙 집합을 만듭니다. 자세한 내용은 [검사 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조하세요.

1.  검색 트리거 설정 창에서 **검색을** 한 번 실행할지, 아니면 되풀이 시간에 실행할지를 선택한 다음, **계속을** 선택합니다.

1.  검사 **검토** 창에서 세부 정보를 검토한 다음 **저장 및 실행** 또는 **저장을** 선택하여 나중에 실행합니다.

검사를 실행하는 동안 **새로 고침을** 선택하여 검사 진행 상황을 모니터링합니다.

> [!NOTE]
> Amazon RDS PostgreSQL 데이터베이스로 작업하는 경우 전체 검사만 지원됩니다. PostgreSQL에 **마지막으로 수정된 시간** 값이 없기 때문에 증분 검색은 지원되지 않습니다. 
>

## <a name="explore-scanning-results"></a>검사 결과 살펴보기

Amazon RDS 데이터베이스에서 Purview 검사가 완료되면 Purview **데이터 맵**  영역에서 드릴다운하여 검사 기록을 확인합니다. 데이터 원본을 선택하여 세부 정보를 확인한 다음, **스캔** 탭을 선택하여 현재 실행 중이거나 완료된 스캔을 확인합니다.

Purview의 다른 영역을 사용하여 Amazon RDS 데이터베이스를 포함하여 데이터 자산의 콘텐츠에 대한 세부 정보를 확인할 수 있습니다.

- **카탈로그 에서 RDS 데이터를 탐색합니다.** Purview 카탈로그는 모든 원본 유형에서 통합 보기를 표시하며 RDS 검사 결과는 Azure SQL 유사한 방식으로 표시됩니다. 필터를 사용하여 카탈로그를 찾아보거나 자산을 찾아보고 계층 구조를 탐색할 수 있습니다. 자세한 내용은 다음을 참조하세요.

    - [자습서: Azure Purview(미리 보기)에서 자산 찾아보기 및 해당 계보 보기](tutorial-browse-and-view-lineage.md)
    - [Azure Purview Data Catalog 검색](how-to-search-catalog.md)
    - [Azure SQL Database 등록 및 검사](register-scan-azure-sql-database.md)

- **인사이트 보고서를 살펴보면** 분류, 민감도 레이블, 파일 형식에 대한 통계 및 콘텐츠에 대한 자세한 정보를 볼 수 있습니다.

    모든 부서의 범위 통찰력 보고서에는 Azure 데이터 원본의 나머지 결과와 함께 Amazon RDS 검색 결과가 포함 됩니다. 관련 된 경우 **AMAZON RDS** 자산 유형이 보고서 필터링 옵션에 추가 됩니다.

    자세한 내용은 [Azure Purview의 인사이트 이해](concept-insights.md)를 참조하세요.

- **검색** 및 **용어집** 영역과 같은 **다른 부서의 범위 기능에서 RDS 데이터를 봅니다**. 자세한 내용은 다음을 참조하세요.

    - [검사 규칙 집합 만들기](create-a-scan-rule-set.md)
    - [자습서: Azure Purview에서 용어집 용어 만들기 및 가져오기(미리 보기)](tutorial-import-create-glossary-terms.md)


## <a name="configure-aws-privatelink-manually-advanced"></a>AWS PrivateLink 수동 구성 (고급)

이 절차에서는 Azure 부서의 범위에 연결 하기 위해 VPC에서 RDS 데이터베이스를 준비 하는 데 필요한 수동 단계에 대해 설명 합니다.

기본적으로이 문서의 앞부분에서 설명한 것 처럼 CloudFormation 템플릿을 대신 사용 하는 것이 좋습니다. 자세한 내용은 [AWS PrivateLink를 사용 하 여 CloudFormation 템플릿 구성](#configure-aws-privatelink-using-a-cloudformation-template)을 참조 하세요.

### <a name="step-1-retrieve-your-amazon-rds-endpoint-ip-address"></a>1 단계: Amazon RDS 끝점 IP 주소 검색

Amazon VPC 내에서 호스트 되는 Amazon RDS 끝점의 IP 주소를 찾습니다. 대상 그룹을 만들 때이 IP 주소를 프로세스의 뒷부분에서 사용 합니다.

**RDS 끝점 IP 주소를 검색 하려면**:

1.  Amazon RDS에서 RDS 데이터베이스로 이동 하 여 끝점 URL을 식별 합니다. 이는 **연결 & 보안** 에서 **끝점** 값으로 배치 됩니다.

    > [!TIP]
    > 다음 명령을 사용 하 여 끝점의 데이터베이스 목록을 가져옵니다. `aws rds describe-db-instances`
    >

1.  끝점 URL을 사용 하 여 Amazon RDS 데이터베이스의 IP 주소를 찾습니다. 예를 들어 다음 방법 중 하나를 사용 합니다.

    - **Ping**: `ping <DB-Endpoint>`

    - **nslookup**: `nslookup <Db-Endpoint>`

    - **온라인 nslookup**. 검색 상자에 데이터베이스 **끝점** 값을 입력 하 고 **DNS 레코드 찾기** 를 선택 합니다. **NSLookup.io** 는 다음 화면에 IP 주소를 표시 합니다.

### <a name="step-2-enable-your-rds-connection-from-a-load-balancer"></a>2 단계: 부하 분산 장치에서 RDS 연결을 사용 하도록 설정

나중에 프로세스에서 만든 부하 분산 장치에서 RDS 연결을 허용 하도록 하려면 다음을 수행 하십시오.

1.  **VPC IP 범위를 찾습니다**.

    Amazon RDS에서 RDS 데이터베이스로 이동 합니다. **연결 & 보안** 영역에서 **VPC** 링크를 선택 하 여 IP 범위 (IPv4 CIDR)를 찾습니다.

    **VPCs** 영역에서 IP 범위는 **IPv4 CIDR** 열에 표시 됩니다.

    > [!TIP]
    > CLI를 통해이 단계를 수행 하려면 다음 명령을 사용 합니다. `aws ec2 describe-vpcs`
    >
    > 자세한 내용은 [ec2-AWS CLI 1.19.105 명령 참조 (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/)를 참조 하세요.
    >

1.  <a name="security-group"></a>**이 IP 범위에 대 한 보안 그룹을 만듭니다**.

    1. 에서 Amazon EC2 콘솔을 열고 https://console.aws.amazon.com/ec2/ **보안 그룹** 으로 이동 합니다.

    1. **보안 그룹 만들기** 를 선택 하 고 다음 세부 정보를 포함 하 여 보안 그룹을 만듭니다.

        - **보안 그룹 이름**: 의미 있는 이름을 입력 합니다.
        - **설명**: 보안 그룹에 대 한 설명을 입력 합니다.
        - **VPC**: RDS database VPC를 선택 합니다.

    1.  **인바운드 규칙** 에서 **규칙 추가** 를 선택 하 고 다음 세부 정보를 입력 합니다.

        - **유형**: **사용자 지정 TCP** 를 선택 합니다.
        - **포트 범위**: RDS 데이터베이스 포트를 입력 합니다.
        - **원본**: **사용자 지정** 을 선택 하 고 이전 단계에서 VPC IP 범위를 입력 합니다.

    1.  페이지 아래쪽으로 스크롤하고 **보안 그룹 만들기** 를 선택 합니다.

1.  **새 보안 그룹을 RDS에 연결** 합니다.

    1.  Amazon RDS에서 RDS 데이터베이스로 이동 하 고 **수정** 을 선택 합니다.

    1.  **연결** 섹션까지 아래로 스크롤하고 **보안 그룹** 필드에서 [이전 단계](#security-group)에서 만든 새 보안 그룹을 추가 합니다. 그런 다음 페이지 아래쪽으로 스크롤하여 계속을 선택 **합니다.**

    1.  **수정 예약** 섹션에서 **즉시 적용** 을 선택 하 여 보안 그룹을 즉시 업데이트 합니다.

    1.  **DB 인스턴스 수정** 을 선택 합니다.

> [!TIP]
> CLI를 통해이 단계를 수행 하려면 다음 명령을 사용 합니다.
>
> - `aws  ec2 create-security-group--description <value>--group-name <value>[--vpc-id <value>]`
>
>     자세한 내용은 [AWS-CLI 1.19.105 명령 참조 (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html)를 참조 하세요.
>
> - `aws rds --db-instance-identifier <value> --vpc-security-group-ids <value>`
>
>     자세한 내용은 [수정-db-인스턴스-AWS CLI 1.19.105 명령 참조 (amazon.com)](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html)를 참조 하세요.
>

### <a name="step-3-create-a-target-group"></a>3 단계: 대상 그룹 만들기

**AWS에서 대상 그룹을 만들려면 다음을 수행 합니다**.

1.  에서 Amazon EC2 콘솔을 열고 https://console.aws.amazon.com/ec2/ **부하 분산**  >  **대상 그룹** 으로 이동 합니다.

1.  **대상 그룹 만들기** 를 선택 하 고 대상 그룹을 만들어 다음 세부 정보를 포함 해야 합니다.

    - **대상 유형**: **IP 주소** 를 선택 합니다 (선택 사항).
    - **프로토콜**: **TCP** 선택
    - **포트**: RDS 데이터베이스 포트를 입력 합니다.
    - **VPC**: RDS 데이터베이스 VPC를 입력 합니다.

    > [!NOTE]
    > Rds 데이터베이스 페이지의 **연결 & 보안** 에서 rds 데이터베이스 포트 및 VPC 값을 찾을 수 있습니다.

    완료 되 면 **다음** 을 선택 하 여 계속 합니다.

1.  **대상 등록** 페이지에서 RDS 데이터베이스 IP 주소를 입력 하 고 다음을 **보류 중으로 포함** 을 선택 합니다.

1.  **대상 테이블에** 나열 된 새 대상이 표시 되 면 페이지 맨 아래에 있는 **대상 그룹 만들기** 를 선택 합니다.

> [!TIP]
> CLI를 통해이 단계를 수행 하려면 다음 명령을 사용 합니다.
>
> - `aws elbv2 create-target-group --name <tg-name> --protocol <db-protocol> --port <db-port> --target-type ip --vpc-id <db-vpc-id>`
>
>     자세한 내용은 [create-target-group-AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-target-group.html)를 참조 하세요.
>
> - `aws elbv2 register-targets --target-group-arn <tg-arn> --targets Id=<db-ip>,Port=<db-port>`
>
>     자세한 내용은 [레지스터-대상-AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/register-targets.html)를 참조 하세요.
>

### <a name="step-4-create-a-load-balancer"></a>4단계: 부하 분산 장치 만들기

[새 네트워크 부하 분산 장치를 만들어](#create-load-balancer) RDS IP 주소로 트래픽을 전달 하거나 기존 부하 분산 장치에 [새 수신기를 추가할](#listener) 수 있습니다.

<a name="create-load-balancer"></a>**RDS IP 주소로 트래픽을 전달 하는 네트워크 부하 분산 장치를 만들려면 다음을** 수행 합니다.

1.  에서 Amazon EC2 콘솔을 열고 부하 https://console.aws.amazon.com/ec2/ **분산**  >  **부하 분산 장치** 로 이동 합니다.

1.    >  **네트워크 Load Balancer** Load Balancer 만들기를 선택 하 고 다음 값을 선택 하거나 입력 합니다.

    - **구성표**: **내부** 선택

    - **VPC**: RDS database VPC를 선택 합니다.

    - **매핑**: 모든 AWS 지역에 대해 RDS가 정의 되었는지 확인 한 다음 이러한 영역을 모두 선택 해야 합니다. 이 정보는 [RDS 데이터베이스](#step-1-retrieve-your-amazon-rds-endpoint-ip-address) 페이지의 **연결 & 보안** 탭에서 **가용성 영역** 값에서 찾을 수 있습니다.

    - **수신기 및 라우팅**:

        - *프로토콜*: **TCP** 선택
        - *포트*: **RDS DB 포트** 선택
        - *기본 작업*: [이전 단계](#step-3-create-a-target-group) 에서 만든 대상 그룹을 선택 합니다.

1.  페이지 맨 아래에서 만들기를 선택 하 **Load Balancer**  >  **부하 분산 장치를 확인** 합니다.

1.  새 Load Balancer의 **상태** 열이 활성화 될 때까지 몇 분 정도 기다린 후 화면을 새로 고칩니다 **.**


> [!TIP]
> CLI를 통해이 단계를 수행 하려면 다음 명령을 사용 합니다.
> - `aws elbv2 create-load-balancer --name <lb-name> --type network --scheme internal --subnet-mappings SubnetId=<value>`
>
>    자세한 내용은 [AWS CLI 2.2.7 명령 참조 (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-load-balancer.html)를 참조 하세요.
>
> - `aws elbv2 create-listener --load-balancer-arn <lb-arn> --protocol TCP --port 80 --default-actions Type=forward,TargetGroupArn=<tg-arn>`
>
>    자세한 내용은 [create-listener-AWS CLI 2.2.7 Command Reference (amazonaws.com)](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/elbv2/create-listener.html)를 참조 하세요.
>

<a name="listener"></a>**기존 부하 분산 장치에 수신기를 추가 하려면 다음을** 수행 합니다.

1. 에서 Amazon EC2 콘솔을 열고 부하 https://console.aws.amazon.com/ec2/ **분산**  >  **부하 분산 장치** 로 이동 합니다.

1. 수신기 추가 **> 부하** 분산 장치를 선택  >  합니다.

1. **수신기** 탭의 **프로토콜: 포트** 영역에서 **TCP** 를 선택 하 고 수신기에 대 한 새 포트를 입력 합니다.


> [!TIP]
> CLI를 통해이 단계를 수행 하려면 다음 명령을 사용 합니다. `aws elbv2  create-listener --load-balancer-arn <value> --protocol <value>  --port <value> --default-actions Type=forward,TargetGroupArn=<target_group_arn>`
>
> 자세한 내용은 [AWS 설명서](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-listener.html)를 참조 하세요.
>

### <a name="step-5-create-an-endpoint-service"></a>5 단계: 끝점 서비스 만들기

[Load Balancer 만들어지고](#step-4-create-a-load-balancer) 상태가 **활성** 이면 끝점 서비스를 만들 수 있습니다.

**끝점 서비스를 만들려면 다음을 수행 합니다**.

1.  에서 Amazon VPC 콘솔을 열고 https://console.aws.amazon.com/vpc/ **가상 사설 클라우드 > 끝점 서비스로** 이동 합니다.

1.  **끝점 서비스 만들기** 를 선택 하 고 **사용 가능한 부하 분산 장치** 드롭다운 목록에서 [이전 단계](#step-4-create-a-load-balancer)에서 만든 새 부하 분산 장치 또는 새 수신기를 추가한 부하 분산 장치를 선택 합니다.

1.  **끝점 서비스 만들기** 페이지에서 **끝점에 대 한 동의 필요** 옵션의 선택을 취소 합니다.

1.  페이지 맨 아래에서 **서비스**  >  **닫기 닫기** 를 선택 합니다.

1.  **끝점 서비스** 페이지로 돌아갑니다.

    1. 만든 새 엔드포인트 서비스를 선택합니다.
    1. 보안 **주체 허용** 탭에서 **보안 주체 추가를 선택합니다.**
    1. > **ARN을 추가할 보안 주체** 필드에 를 입력합니다. `arn:aws:iam::181328463391:root`
    1. **보안 주체 추가를 선택합니다.**

    > [!NOTE]
    > ID를 추가할 때 별표(*)를 사용하여 모든 보안 주체에 대한 권한을 추가합니다. 이렇게 하면 모든 AWS 계정의 모든 보안 주체가 엔드포인트 서비스에 대한 엔드포인트를 만들 수 있습니다. 자세한 내용은 AWS [설명서를 참조하세요.](https://docs.aws.amazon.com/vpc/latest/privatelink/add-endpoint-service-permissions.html)

> [!TIP]
> CLI를 통해 이 단계를 수행하려면 다음 명령을 사용합니다.
>
> - `aws ec2 create-vpc-endpoint-service-configuration --network-load-balancer-arns  <lb-arn>  --no-acceptance-required`
>
>    자세한 내용은 [create-vpc-endpoint-service-configuration - AWS CLI 2.2.7 명령 참조(amazonaws.com)를 참조하세요.](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/create-vpc-endpoint-service-configuration.html)
>
> - `aws ec2 modify-vpc-endpoint-service-permissions --service-id <endpoint-service-id> --add-allowed-principals <purview-scanner-arn>`
>
>    자세한 내용은 [modify-vpc-endpoint-service-permissions - AWS CLI 2.2.7 명령 참조(amazonaws.com)를 참조하세요.](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/modify-vpc-endpoint-service-permissions.html)
>

<a name="service-name"></a>**Azure Purview에서 사용할 서비스 이름을 복사하려면:**

엔드포인트 서비스를 만든 후 [RDS 데이터베이스를](#register-an-amazon-rds-data-source) Purview 데이터 원본으로 등록할 때 Azure Purview 포털에서 **서비스 이름** 값을 복사할 수 있습니다.

선택한 엔드포인트 서비스의 **세부 정보** 탭에서 서비스 **이름을** 찾습니다.

> [!TIP]
> CLI를 통해 이 단계를 수행하려면 다음 명령을 사용합니다. `Aws ec2 describe-vpc-endpoint-services`
>
> 자세한 내용은 [describe-vpc-endpoint-services - AWS CLI 2.2.7 명령 참조(amazonaws.com)를 참조하세요.](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ec2/describe-vpc-endpoint-services.html)
>

## <a name="troubleshoot-your-vpc-connection"></a>VPC 연결 문제 해결

이 섹션에서는 Azure Purview를 사용하여 VPC 연결을 구성할 때 발생할 수 있는 일반적인 오류와 문제를 해결하고 해결하는 방법을 설명합니다.

### <a name="invalid-vpc-service-name"></a>잘못된 VPC 서비스 이름

또는 오류가 `Invalid VPC service name` `Invalid endpoint service` Azure Purview에 나타나는 경우 다음 단계를 사용하여 문제를 해결합니다.

1. VPC 서비스 이름이 올바른지 확인합니다. 예를 들어 다음과 같습니다.

    :::image type="content" source="media/register-scan-amazon-rds/locate-service-name.png" alt-text="AWS의 VPC 서비스 이름 스크린샷.":::

1. Microsoft ARN이 허용된 보안 주체에 나열되어 있는지 확인합니다. `arn:aws:iam::181328463391:root`

    자세한 내용은 [5단계: 엔드포인트 서비스 만들기를 참조하세요.](#step-5-create-an-endpoint-service)

1. RDS 데이터베이스가 지원되는 지역 중 하나에 나열되어 있는지 확인합니다. 자세한 내용은 [Amazon RDS에 대한 Purview 범위를 참조하세요.](#purview-scope-for-amazon-rds)

### <a name="invalid-availability-zone"></a>잘못된 가용성 영역

Azure Purview에 오류가 표시되는 경우 `Invalid Availability Zone` 다음 세 지역 중 하나 이상에 대해 RDS가 정의되어 있는지 확인합니다.

- **us-east-1a**
- **us-east-1b**
- **us-east-1c**

자세한 내용은 AWS [설명서를 참조하세요.](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-getting-started.html)

### <a name="rds-errors"></a>RDS 오류

Azure Purview에 다음과 같은 오류가 나타날 수 있습니다.

- `Unknown database`. 이 경우 정의된 데이터베이스가 없습니다. 구성된 데이터베이스 이름이 올바른지 확인합니다.

- `Failed to login to the Sql data source. The given auth credential does not have permission on the target database.` 이 경우 사용자 이름과 암호가 올바르지 않습니다. 자격 증명을 확인하고 필요에 따라 업데이트합니다.


## <a name="next-steps"></a>다음 단계

Azure Purview 인사이트 보고서에 대해 자세히 알아보기

> [!div class="nextstepaction"]
> [Azure Purview의 인사이트 이해](concept-insights.md)