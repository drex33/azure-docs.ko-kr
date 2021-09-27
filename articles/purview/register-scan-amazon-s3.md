---
title: Azure Purview용 Amazon S3 다중 클라우드 검사 커넥터
description: 이 방법 가이드에서는 Amazon S3 버킷을 스캔하는 방법에 대해 자세히 설명합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/19/2021
ms.custom: references_regions
ms.openlocfilehash: a7369854717fe9790badbe6cd9f4410a594ce1cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619417"
---
# <a name="amazon-s3-multi-cloud-scanning-connector-for-azure-purview"></a>Azure Purview용 Amazon S3 다중 클라우드 검사 커넥터

Azure Purview용 다중 클라우드 검사 커넥터를 사용하면 Azure 스토리지 서비스 외에도 Amazon Web Services를 비롯한 클라우드 공급자 간에 조직 데이터를 탐색할 수 있습니다.

이 문서에서는 Azure Purview를 사용하여 현재 Amazon S3 표준 버킷에 저장된 비정형 데이터를 스캔하고 데이터에 어떤 유형의 중요한 정보가 존재하는지 검색하는 방법을 설명합니다. 이 방법 가이드에서는 손쉬운 정보 보호 및 데이터 규정 준수를 위해 현재 데이터가 저장되어 있는 Amazon S3 버킷을 식별하는 방법에 대해서도 설명합니다.

이 서비스의 경우 Purview를 사용하여 Azure Purview용 다중 클라우드 검사 커넥터가 실행되는 AWS에 대한 보안 액세스 권한이 있는 Microsoft 계정을 제공합니다. Azure Purview용 다중 클라우드 검사 커넥터는 Amazon S3 버킷에 대한 액세스 권한을 사용하여 데이터를 읽은 다음, 메타데이터 및 분류만 포함한 검사 결과를 Azure에 다시 보고합니다. Purview 분류 및 레이블 지정 보고서를 사용하여 데이터 스캔 결과를 분석하고 검토할 수 있습니다.

> [!IMPORTANT]
> Azure Purview용 다중 클라우드 검사 커넥터는 Azure Purview에 대한 별도의 추가 기능입니다. Azure Purview용 다중 클라우드 검사 커넥터에 대한 사용 약관은 Microsoft Azure 서비스를 취득한 계약에 포함되어 있습니다. 자세한 내용은 https://azure.microsoft.com/support/legal/ 에서 Microsoft Azure 법적 정보를 참조하세요.
>

## <a name="purview-scope-for-amazon-s3"></a>Amazon S3의 Purview 범위

Purview 제한에 대한 자세한 내용은 다음을 참조하세요.

- [Azure Purview를 사용하여 리소스 할당량 관리 및 늘리기](how-to-manage-quotas.md)
- [Azure Purview에서 지원되는 데이터 원본 및 파일 형식](sources-and-scans.md)
- [Purview 계정에 대한 프라이빗 엔드포인트 사용](catalog-private-link.md)

### <a name="storage-and-scanning-regions"></a>스토리지 및 스캔 지역

Amazon S3 서비스의 Purview 커넥터는 현재 특정 지역에만 배포됩니다. 다음 표에서는 데이터가 저장된 지역을 Azure Purview에서 스캔할 지역에 매핑합니다.

> [!IMPORTANT]
> 고객 버킷의 지역에 따라 모든 관련 데이터 전송 요금이 고객에게 청구됩니다.
>

| 스토리지 지역 | 스캔 지역 |
| ------------------------------- | ------------------------------------- |
| 미국 동부(오하이오)                  | 미국 동부(오하이오)                        |
| 미국 동부(북부 버지니아)           | 미국 동부(북부 버지니아)                       |
| 미국 서부(북부 캘리포니아)         | 미국 서부(북부 캘리포니아)                        |
| 미국 서부(오리건)                | 미국 서부(오리건)                      |
| 아프리카(케이프타운)              | 유럽(프랑크푸르트)                    |
| 아시아 태평양(홍콩)        | 아시아 태평양(도쿄)                |
| 아시아 태평양(뭄바이)           | 아시아 태평양(싱가포르)                |
| 아시아 태평양(오사카-로컬)      | 아시아 태평양(도쿄)                 |
| 아시아 태평양(서울)            | 아시아 태평양(도쿄)                 |
| 아시아 태평양(싱가포르)        | 아시아 태평양(싱가포르)                 |
| 아시아 태평양(시드니)           | 아시아 태평양(시드니)                  |
| 아시아 태평양(도쿄)            | 아시아 태평양(도쿄)                |
| 캐나다(중부)                | 미국 동부(오하이오)                        |
| 중국(베이징)                 | 지원되지 않음                    |
| 중국(닝샤후이족자치구)                 | 지원되지 않음                   |
| 유럽(프랑크푸르트)              | 유럽(프랑크푸르트)                    |
| 유럽(아일랜드)                | 유럽(아일랜드)                   |
| 유럽(런던)                 | 유럽(런던)                 |
| 유럽(밀라노)                  | 유럽(파리)                    |
| 유럽(파리)                  | 유럽(파리)                   |
| 유럽(스톡홀름)              | 유럽(프랑크푸르트)                    |
| 중동(바레인)           | 유럽(프랑크푸르트)                    |
| 남아메리카(남아메리카)       | 미국 동부(오하이오)                        |
| | |

## <a name="prerequisites"></a>사전 요구 사항

Amazon S3 버킷을 Purview 데이터 원본으로 추가하고 S3 데이터를 스캔하기 전에 다음 사전 요구 사항을 수행해야 합니다.

> [!div class="checklist"]
> * Azure Purview 데이터 원본 관리자여야 합니다.
> * [Purview 계정 생성](#create-a-purview-account)(아직 없는 경우)
> * [Purview에 사용할 새 AWS 역할 생성](#create-a-new-aws-role-for-purview)
> * [AWS 버킷 스캔을 위한 Purview 자격 증명 만들기](#create-a-purview-credential-for-your-aws-s3-scan)
> * [암호화된 Amazon S3 버킷에 대한 스캔 구성](#configure-scanning-for-encrypted-amazon-s3-buckets)(관련된 경우)
> * 버킷을 Purview 리소스로 추가하는 경우 [AWS ARN](#retrieve-your-new-role-arn), [버킷 이름](#retrieve-your-amazon-s3-bucket-name) 및 경우에 따라 [AWS 계정 ID](#locate-your-aws-account-id) 값이 필요합니다.

### <a name="create-a-purview-account"></a>Purview 계정 만들기

- **Purview 계정이 이미 있는 경우,** AWS S3 지원에 필요한 구성을 계속할 수 있습니다. [AWS 버킷 스캔을 위한 Purview 자격 증명 만들기](#create-a-purview-credential-for-your-aws-s3-scan)부터 시작하세요.

- **Purview 계정을 만들어야 하는 경우,** [Azure Purview 계정 인스턴스 만들기](create-catalog-portal.md)의 지침을 따르세요. 계정을 만든 후 여기로 돌아와서 구성을 완료하고 Amazon S3 용 Purview 커넥터 사용을 시작합니다.

### <a name="create-a-new-aws-role-for-purview"></a>Purview에 대한 새 AWS 역할 만들기

이 절차에서는 Azure 계정 ID 및 외부 ID에 대한 값을 찾고, AWS 역할을 만든 다음, Purview에서 역할 ARN에 대한 값을 입력하는 방법을 설명합니다.

**Microsoft 계정 ID 및 외부 ID를 찾으려면 다음을 수행합니다**.

1. Purview에서 **관리 센터** > **보안 및 액세스** > **자격 증명** 으로 이동합니다.

1. **새로** 만들기를 선택하여 새 자격 증명을 만듭니다.

    오른쪽에 표시되는 **새 자격 증명** 창에서 Microsoft 계정 ID 및 외부 **ID**  값을 별도의 파일에 복사하거나 AWS의 관련 필드에 붙여넣는 데 편리하게 보관합니다.

    예를 들면 다음과 같습니다.

    [ ![Microsoft 계정 ID 및 외부 ID 값을 찾습니다.](./media/register-scan-amazon-s3/locate-account-id-external-id.png) ](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Purview에 대한 AWS 역할을 만들려면**:

1.  **Amazon Web Services** 콘솔을 열고 **Security, Identity, and Compliance**(보안, 자격 증명 및 규정 준수)에서 **IAM** 을 선택합니다.

1. **역할** 을 선택한 다음, **역할 만들기** 를 선택합니다.

1. **다른 AWS 계정** 을 선택한 후 다음 값을 입력합니다.

    |필드  |Description  |
    |---------|---------|
    |**계정 ID**     |    Microsoft 계정 ID를 입력합니다. `181328463391`     |
    |**외부 ID**     |   옵션에서 **외부 ID 필요...** 를 선택한 다음, 지정된 필드에 외부 ID를 입력합니다. <br>`e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    예를 들면 다음과 같습니다.

    ![AWS 계정에 Microsoft 계정 ID를 추가합니다.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. **Create role > Attach permissions policies**(역할 만들기 > 권한 정책 연결) 영역에서 **S3** 에 표시된 권한을 필터링합니다. **AmazonS3ReadOnlyAccess** 를 선택한 후 **다음: 태그** 를 선택합니다.

    ![새 Amazon S3 스캔 역할에 대해 ReadOnlyAccess 정책을 선택합니다.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > **AmazonS3ReadOnlyAccess** 정책은 S3 버킷을 스캔하는 데 필요한 최소 권한을 제공하며 다른 권한도 포함할 수 있습니다.
    >
    >버킷을 스캔하는 데 필요한 최소 권한만 적용하려면 계정의 단일 버킷을 스캔할지 또는 모든 버킷을 스캔할지에 따라 [AWS 정책에 대한 최소 권한](#minimum-permissions-for-your-aws-policy)에 나열된 권한으로 새 정책을 만듭니다. 
    >
    >**AmazonS3ReadOnlyAccess** 대신 역할에 새 정책을 적용합니다.

1. **태그 추가(선택 사항)** 영역에서 필요에 따라 새 역할에 대해 의미 있는 태그를 생성하도록 선택할 수 있습니다. 유용한 태그를 활용하면 사용자가 만든 각 역할에 대한 액세스를 구성, 추적 및 제어할 수 있습니다.

    필요에 따라 태그의 새 키와 값을 입력합니다. 완료했거나 이 단계를 건너뛰려면 **다음: 검토** 를 선택하여 역할 세부 정보를 검토하고 역할 만들기를 완료합니다.

    ![새 역할에 대한 액세스를 구성, 추적 또는 제어하기 위한 의미 있는 태그를 추가합니다.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. **검토** 영역에서 다음을 수행합니다.

    - **역할 이름** 필드에 의미 있는 역할 이름을 입력합니다.
    - **역할 설명** 상자에 역할의 목적을 식별하는 설명(선택 사항)을 입력합니다.
    - **정책** 섹션에서 올바른 정책(**AmazonS3ReadOnlyAccess**)이 역할에 연결되어 있는지 확인합니다.

    그런 다음, **역할 만들기** 를 선택하여 프로세스를 완료합니다.

    예를 들면 다음과 같습니다.

    ![역할을 만들기 전에 세부 정보를 검토합니다.](./media/register-scan-amazon-s3/review-role.png)


### <a name="create-a-purview-credential-for-your-aws-s3-scan"></a>AWS S3 검색에 대 한 부서의 범위 자격 증명 만들기

이 절차는 AWS 버킷을 스캔할 때 사용할 새 Purview 자격 증명을 생성하는 방법에 대해 설명합니다.

> [!TIP]
> [부서의 범위에 대 한 새 AWS 역할 만들기](#create-a-new-aws-role-for-purview)에서 계속 진행 하는 경우 부서의 범위에 **새 자격 증명** 창이 이미 열려 있을 수 있습니다.
>
> [스캔을 구성](#create-a-scan-for-one-or-more-amazon-s3-buckets)하는 동안 프로세스 중간에 새 자격 증명을 만들 수도 있습니다. 이 경우 **자격 증명** 필드에서 **새로 만들기** 를 선택합니다.
>

1. Purview에서 **관리 센터** 로 이동하고 **보안 및 액세스** 아래에서 **자격 증명** 을 선택합니다.

1. **새로 만들기** 를 선택하고 오른쪽에 나타나는 **새 자격 증명** 창에서 다음 필드를 사용하여 Purview 자격 증명을 만듭니다.

    |필드 |Description  |
    |---------|---------|
    |**이름**     |이 자격 증명에 대 한 의미 있는 이름을 입력 합니다.        |
    |**설명**     |이 자격 증명에 대한 선택적 설명(예: `Used to scan the tutorial S3 buckets`)을 입력합니다.         |
    |**인증 방법**     |역할 ARN을 사용하여 버킷에 액세스하고 있으니 **역할 ARN** 을 선택합니다.         |
    |**Microsoft 계정 ID**     |이 값을 클립 보드에 복사하려면 클릭합니다. 이 값은 [AWS에서 역할 ARN을 생성](#create-a-new-aws-role-for-purview)할 때 **Microsoft 계정 ID** 로 사용합니다.           |
    |**외부 ID**     |이 값을 클립 보드에 복사하려면 클릭합니다. 이 값은 [AWS에서 역할 ARN을 생성](#create-a-new-aws-role-for-purview)할 때 **외부 ID** 로 사용합니다.        |
    |**역할 ARN**     | [AMAZON iam 역할을 만들었으면](#create-a-new-aws-role-for-purview)AWS IAM 영역의 역할로 이동 하 여 **역할 arn** 값을 복사 하 고 여기에 입력 합니다. 예: `arn:aws:iam::181328463391:role/S3Role`. <br><br>자세한 내용은 [새 역할 ARN 검색](#retrieve-your-new-role-arn)을 참조하세요. |
    | | |

    자격 증명 만들기를 마치면 **만들기** 를 선택합니다.

1. 아직 없는 경우, 다음 단계로 사용할 **Microsoft 계정 ID** 및 **외부 ID** 값을 복사 후 붙여넣어 [Purview의 새 AWS 역할을 만듭니다](#create-a-new-aws-role-for-purview).

Purview 자격 증명에 대한 자세한 정보는 [Azure Purview에서 원본 인증을 위한 자격 증명](manage-credentials.md)을 참조하세요.


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>암호화된 Amazon S3 버킷에 대한 스캔 구성

AWS 버킷은 여러 암호화 유형을 지원합니다. **AWS-KMS** 암호화를 사용하는 버킷의 경우 스캔이 가능하도록 설정하려면 특별한 구성이 필요합니다.

> [!NOTE]
> 암호화 안 함, AES-256 또는 AWS-KMS S3 암호화를 사용하는 버킷의 경우 이 섹션을 건너뛰고 [Amazon S3 버킷 이름 검색](#retrieve-your-amazon-s3-bucket-name)을 계속합니다.
>

**Amazon S3 버킷에 사용되는 암호화 유형을 확인하려면 다음을 수행 합니다.**

1. AWS에서 **스토리지** > **S3** 으로 이동하고 왼쪽 메뉴에서 **버킷** 을 선택합니다.

    ![Amazon S3 버킷 탭을 선택합니다.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 확인할 버킷을 선택합니다. 버킷의 세부 정보 페이지에서 **속성** 탭을 선택하고 **기본 암호화** 영역까지 아래로 스크롤합니다.

    - 선택한 버킷이 **AWS-KMS** 암호화 이외의 것으로 구성된 경우(버킷의 기본 암호화를 **사용하지 않도록 설정** 된 경우 포함) 이 절차의 나머지 부분을 건너뛰고 [Amazon S3 버킷 이름 검색](#retrieve-your-amazon-s3-bucket-name)을 계속합니다.

    - 선택한 버킷이 **AWS-KMS** 암호화로 구성된 경우 아래 설명에 따라 계속해서 사용자 지정 **AWS-KMS** 암호화로 버킷 스캔을 허용하는 새 정책을 추가합니다.

    예를 들면 다음과 같습니다.

    ![AWS-KMS 암호화로 구성된 Amazon S3 버킷 뷰](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**사용자 지정 AWS-KMS 암호화로 버킷 스캔이 가능한 새 정책을 추가하려면 다음을 수행합니다.**

1. AWS에서 **서비스** >  **IAM** >  **정책** 으로 이동하고 **정책 만들기** 를 선택합니다.

1. **정책 만들기** > **Visual editor**(시각적 개체 편집기) 탭에서 다음 값으로 정책을 정의합니다.

    |필드  |Description  |
    |---------|---------|
    |**서비스**     |  **KMS** 를 입력하고 선택합니다.       |
    |**actions**     | **액세스 수준** 에서 **쓰기** 를 선택하여 **쓰기** 섹션을 확장합니다.<br>확장되면 **암호 해독** 옵션만 선택합니다.        |
    |**리소스**     |특정 리소스 또는 **모든 리소스** 를 선택합니다.         |
    | | |

    완료되면 **정책 검토** 를 선택하여 계속합니다.

    ![AWS-KMS 암호화를 사용하여 버킷을 스캔하는 정책을 만듭니다.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. **정책 검토** 페이지에서 의미 있는 정책 이름과 설명(선택 사항)을 입력한 다음, **정책 만들기** 를 선택합니다.

    새로 만든 정책이 정책 목록에 추가됩니다.

1. 스캔을 위해 추가한 역할에 새 정책을 연결합니다.

    1. **IAM** > **역할** 페이지로 다시 이동하여 [이전](#create-a-new-aws-role-for-purview)에 추가한 역할을 선택합니다.

    1. **권한** 탭에서 **Attach policies**(정책 연결)을 선택합니다.

        ![역할의 권한 탭에서 정책 연결을 선택합니다.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. **Attach Permissions**(권한 연결) 페이지에서 위에서 만든 새 정책을 검색하고 선택합니다. **Attach policy**(정책 연결)을 선택하여 정책을 역할에 연결합니다.

        **요약** 페이지가 업데이트되어 새 정책이 역할에 연결됩니다.

        ![역할에 새 정책이 연결되어 있는 업데이트된 요약 페이지를 봅니다.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>새 역할 ARN 검색

[Amazon S3 버킷에 대한 스캔을 만들](#create-a-scan-for-one-or-more-amazon-s3-buckets)때 AWS 역할 ARN을 기록하여 Purview에 복사해야 합니다.

**역할 ARN을 검색하려면 다음을 수행 합니다.**

1. AWS **IAM(ID 및 액세스 관리)**  > **역할** 영역에서 [Purview에 대해 만든](#create-a-purview-credential-for-your-aws-s3-scan) 새 역할을 검색하고 선택합니다.

1. 역할의 **요약** 페이지에서 **역할 ARN** 값의 오른쪽에 있는 **클립보드로 복사** 단추를 선택합니다.

    ![역할 ARN 값을 클립보드에 복사합니다.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

부서의 범위에서 AWS S3의 자격 증명을 편집 하 고 검색 된 역할을 **역할 ARN** 필드에 붙여 넣을 수 있습니다. 자세한 내용은 [하나 이상의 Amazon S3 버킷에 대 한 검색 만들기](#create-a-scan-for-one-or-more-amazon-s3-buckets)를 참조 하세요.

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Amazon S3 버킷 이름 검색

[Amazon S3 버킷에 대한 스캔을 만들](#create-a-scan-for-one-or-more-amazon-s3-buckets)때 Purview에 복사하려면 Amazon S3 버킷의 이름이 필요합니다.

**버킷 이름을 검색하려면:**

1. AWS에서 **스토리지** > **S3** 으로 이동하고 왼쪽 메뉴에서 **버킷** 을 선택합니다.

    ![Amazon S3 버킷 탭을 봅니다.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. 버킷을 검색하고 선택하여 버킷 세부 정보 페이지를 확인한 다음, 버킷 이름을 클립 보드에 복사합니다.

    예를 들면 다음과 같습니다.

    ![S3 버킷 URL을 검색하여 복사합니다.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    버킷 이름을 보안 파일에 붙여넣고 `s3://` 접두사를 추가하여 버킷을 Purview 리소스로 구성할 때 입력해야 하는 값을 만듭니다.

    `s3://purview-tutorial-bucket`

> [!TIP]
> 버킷의 루트 수준만 Purview 데이터 원본으로 지원됩니다. 예를 들어 하위 폴더가 포함된 다음 URL은 지원되지 않습니다. `s3://purview-tutorial-bucket/view-data`
>
> 그러나 특정 S3 버킷에 대한 검색을 구성하는 경우 검사에 대해 하나 이상의 특정 폴더를 선택할 수 있습니다. 자세한 내용은 검사 범위를 [정하는 단계를 참조하세요.](#create-a-scan-for-one-or-more-amazon-s3-buckets)
>

### <a name="locate-your-aws-account-id"></a>AWS 계정 ID 찾기

AWS 계정을 모든 버킷과 함께 Purview 데이터 원본으로 등록하려면 AWS 계정 ID가 필요합니다.

AWS 계정 ID는 AWS 콘솔에 로그인하는 데 사용하는 ID입니다. IAM 대시 보드에 로그인하면, 탐색 옵션 왼쪽 아래와 위쪽에 있는 로그인 URL의 숫자 부분에서도 찾을 수 있습니다.

예를 들면 다음과 같습니다.

![AWS 계정 ID를 검색합니다.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>단일 Amazon S3 버킷을 Purview 리소스로 추가

Purview에 데이터 원본으로 등록하려는 S3 버킷이 하나만 있는 경우 또는 AWS 계정에 여러 버킷이 있지만 모두를 Purview에 등록하지 않으려는 경우에는 이 절차를 사용합니다.

**버킷을 추가하려면**: 

1. Amazon S3 URL의 전용 Purview 커넥터를 사용하여 Purview 포털을 시작합니다. 이 URL은 Amazon S3 Purview 커넥터 제품 관리 팀에서 제공합니다.

    ![Purview 포털을 시작합니다.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Azure Purview **데이터 맵** 페이지로 이동하여 **등록** ![등록 아이콘](./media/register-scan-amazon-s3/register-button.png)을 선택합니다. > **Amazon S3** > **계속** 을 선택합니다.

    ![Amazon AWS 버킷을 Purview 데이터 원본으로 추가합니다.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > 여러 [컬렉션](manage-data-sources.md#manage-collections)이 있고 특정 컬렉션에 Amazon S3을 추가하려는 경우에는 오른쪽 상단의 **지도 보기** 를 선택한 다음, 컬렉션 안에서 **등록** ![등록 아이콘](./media/register-scan-amazon-s3/register-button.png) 단추를 클릭합니다.
    >

1. **원본 등록(Amazon S3)** 창이 열리면 다음 세부 정보를 입력합니다.

    |필드  |Description  |
    |---------|---------|
    |**이름**     |의미 있는 이름을 입력하거나 제공된 기본값을 사용합니다.         |
    |**버킷 URL**     | 다음 구문을 사용하여 AWS 버킷 URL을 입력합니다. `s3://<bucketName>`     <br><br>**참고:** 버킷의 루트 수준만 사용해야 합니다. 자세한 내용은 [Amazon S3 버킷 이름 검색](#retrieve-your-amazon-s3-bucket-name)을 참조하세요. |
    |**컬렉션 선택** |컬렉션 내에서 데이터 원본을 등록하도록 선택한 경우 해당 컬렉션은 이미 나열되어 있습니다. <br><br>필요에 따라 다른 컬렉션을 선택하거나, 컬렉션을 할당하지 않으려면 **없음** 을 선택하거나, 지금 새 컬렉션을 만들려면 **새로 만들기** 를 선택합니다. <br><br>Purview 컬렉션에 대한 자세한 내용은 [Azure Purview에서 데이터 원본 관리](manage-data-sources.md#manage-collections)를 참조하세요.|
    | | |

    완료되면 **마침** 을 선택하여 등록을 완료합니다.

[하나 이상의 Amazon S3 버킷에 대한 검색 만들기](#create-a-scan-for-one-or-more-amazon-s3-buckets)를 선택해 계속 진행합니다.

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Purview 리소스로 Amazon 계정 추가

Amazon 계정에 여러 S3 버킷이 있고 모든 버킷을 Purview 데이터 원본으로 등록하려는 경우 이 절차를 사용합니다.

[검사를 구성하는 경우](#create-a-scan-for-one-or-more-amazon-s3-buckets) 모든 버킷을 함께 검색하지 않으려면 검색하려는 특정 버킷을 선택할 수 있습니다.

**Amazon 계정을 추가하려면**:
1. Amazon S3 URL의 전용 Purview 커넥터를 사용하여 Purview 포털을 시작합니다. 이 URL은 Amazon S3 Purview 커넥터 제품 관리 팀에서 제공합니다.

    ![Amazon S3 전용 Purview 포털용 커넥터 시작](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Azure Purview **데이터 맵** 페이지로 이동하여 **등록** ![등록 아이콘](./media/register-scan-amazon-s3/register-button.png)을 선택합니다. > **Amazon 계정** > **계속** 을 선택합니다.

    ![Amazon 계정을 Purview 데이터 원본으로 추가합니다.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > 여러 [컬렉션](manage-data-sources.md#manage-collections)이 있고 특정 컬렉션에 Amazon S3을 추가하려는 경우에는 오른쪽 상단의 **지도 보기** 를 선택한 다음, 컬렉션 안에서 **등록** ![등록 아이콘](./media/register-scan-amazon-s3/register-button.png) 단추를 클릭합니다.
    >

1. **원본 등록(Amazon S3)** 창이 열리면 다음 세부 정보를 입력합니다.

    |필드  |Description  |
    |---------|---------|
    |**이름**     |의미 있는 이름을 입력하거나 제공된 기본값을 사용합니다.         |
    |**AWS 계정 ID**     | AWS 계정 ID를 입력합니다. 자세한 내용은 [AWS 계정 ID 찾기](#locate-your-aws-account-id)를 참조하세요.|
    |**컬렉션 선택** |컬렉션 내에서 데이터 원본을 등록하도록 선택한 경우 해당 컬렉션은 이미 나열되어 있습니다. <br><br>필요에 따라 다른 컬렉션을 선택하거나, 컬렉션을 할당하지 않으려면 **없음** 을 선택하거나, 지금 새 컬렉션을 만들려면 **새로 만들기** 를 선택합니다. <br><br>Purview 컬렉션에 대한 자세한 내용은 [Azure Purview에서 데이터 원본 관리](manage-data-sources.md#manage-collections)를 참조하세요.|
    | | |

    완료되면 **마침** 을 선택하여 등록을 완료합니다.

[하나 이상의 Amazon S3 버킷에 대한 검색 만들기](#create-a-scan-for-one-or-more-amazon-s3-buckets)를 계속합니다.

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>하나 이상의 Amazon S3 버킷에 대한 검색 만들기

버킷을 Purview 데이터 원본으로 추가한 후에는 예약된 간격으로 또는 즉시 실행되도록 스캔을 구성할 수 있습니다.

1. Purview Studio의 왼쪽 창에서 **데이터 맵** 탭을 선택한 후, 다음 중 하나를 수행합니다.

    - **지도 보기** 에서 데이터 원본 상자의 **새 스캔**![새 스캔 아이콘](./media/register-scan-amazon-s3/new-scan-button.png)을 선택합니다.
    - **목록 보기** 에서 데이터 원본의 행을 마우스로 가리키고 **새 스캔**![새 스캔 아이콘](./media/register-scan-amazon-s3/new-scan-button.png)을 선택합니다.

1. 오른쪽에 **스캔...** 창이 열리면 다음 필드를 정의한 후 **계속** 을 선택합니다.

    |필드  |Description  |
    |---------|---------|
    |**이름**     |  스캔에 대한 의미 있는 이름을 입력하거나 기본값을 사용합니다.       |
    |**형식** |모든 버킷이 포함된 AWS 계정을 추가한 경우에만 표시됩니다. <br><br>현재 옵션에는 **모두** > **Amazon S3** 만 포함됩니다. Purview의 지원 매트릭스가 확장되면서, 선택 가능한 옵션이 더 추가될 예정이니 기대해 주세요. |
    |**자격 증명**     |  역할 ARN이 있는 Purview 자격 증명을 선택합니다. <br><br>**팁**: 지금 새 자격 증명을 만들려면 **새로 만들기** 를 선택합니다. 자세한 내용은 [AWS 버킷 스캔을 위한 Purview 자격 증명 만들기](#create-a-purview-credential-for-your-aws-s3-scan)를 참조하세요.     |
    | **Amazon S3**    |   모든 버킷이 포함된 AWS 계정을 추가한 경우에만 표시됩니다. <br><br>검색할 버킷을 하나 이상 선택하거나 **모두 선택** 하여 계정의 모든 버킷을 검색합니다.      |
    | | |

    Purview는 역할 ARN이 유효한지와 버킷과 버킷 내부의 개체에 액세스할 수 있는지를 자동으로 확인한 다음 연결이 성공하면 계속 진행됩니다.

    > [!TIP]
    > 계속하기 전에 다른 값을 입력하고 연결을 직접 테스트하려면 오른쪽 아래에서 **연결 테스트** 를 선택한 후 **계속** 을 선택합니다.
    >

1. <a name="scope-your-scan"></a>검색 범위 창에서 **검사에** 포함할 특정 버킷 또는 폴더를 선택합니다.

    전체 AWS 계정에 대한 검색을 만들 때 검사할 특정 버킷을 선택할 수 있습니다. 특정 AWS S3 버킷에 대한 검색을 만들 때 검색할 특정 폴더를 선택할 수 있습니다.

1. **Select a scan rule set**(스캔 규칙 집합 선택) 창에서 **AmazonS3** 기본 규칙 집합을 선택하거나 **New scan rule set**(새 스캔 규칙 집합)을 선택하여 새 사용자 지정 규칙 집합을 만듭니다. 규칙 집합을 선택한 후 **계속** 을 선택합니다.

    새 사용자 지정 스캔 규칙 집합을 만들도록 선택한 경우 마법사를 사용하여 다음 설정을 정의합니다.

    |창  |Description  |
    |---------|---------|
    |**새 스캔 규칙 집합** /<br>**스캔 규칙 설명**    |   규칙 집합에 대한 의미 있는 이름과 설명(선택 사항)을 입력합니다.      |
    |**파일 형식 선택**     | 스캔에 포함할 모든 파일 형식을 선택하고 **계속** 을 선택합니다.<br><br>새 파일 형식을 추가하려면 **새 파일 형식** 을 선택하고 다음을 정의합니다. <br>- 추가하려는 파일 확장명 <br>- 설명(선택 사항)  <br>- 파일 콘텐츠에 사용자 지정 구분 기호가 있는지 또는 시스템 파일 형식인지를 정의하고 사용자 지정 구분 기호를 입력하거나 시스템 파일 형식을 선택합니다. <br><br>**만들기** 를 선택하여 사용자 지정 파일 형식을 만듭니다.     |
    |**분류 규칙 선택**     |   데이터 세트에서 실행할 분류 규칙으로 이동하여 선택합니다.      |
    |     |         |

    규칙 집합 만들기를 완료하면 **만들기** 를 선택합니다.

1. **Set a scan trigger**(스캔 트리거 설정) 창에서 다음 중 하나를 선택하고 **계속** 을 선택합니다.

    - **되풀이**: 되풀이 스캔 일정을 구성하려는 경우
    - **한 번**: 즉시 시작되는 스캔을 구성하려는 경우

1. **Review your scan**(스캔 검토) 창에서 스캔 세부 정보를 확인하여 올바른지 확인한 다음, **저장** 을 선택하거나 이전 창에서 **한 번** 을 선택한 경우 **저장 및 실행** 을 선택합니다.

    > [!NOTE]
    > 스캔이 시작되면 완료하는 데 최대 24시간이 걸릴 수 있습니다. 각 스캔을 시작하고 24시간 후에 **Insight Reports**(인사이트 보고서)를 검토하고 카탈로그를 스캔할 수 있습니다.
    >

자세한 내용은 [Purview 스캔 결과 살펴보기](#explore-purview-scanning-results)를 참조하세요.

## <a name="explore-purview-scanning-results"></a>Purview 스캔 결과 살펴보기

Amazon S3 버킷에서 Purview 스캔이 완료되면 Purview **데이터 맵** 영역에서 드릴다운하여 스캔 기록을 확인합니다.

데이터 원본을 선택하여 세부 정보를 확인한 다음, **스캔** 탭을 선택하여 현재 실행 중이거나 완료된 스캔을 확인합니다.
버킷이 여러 개인 AWS 계정을 추가한 경우에는 각 버킷에 대한 스캔 기록이 계정 아래에 표시됩니다.

예를 들면 다음과 같습니다.

![AWS 계정 원본 아래에 AWS S3 버킷 스캔이 표시됩니다.](./media/register-scan-amazon-s3/account-scan-history.png)

Purview의 다른 영역을 사용하여 Amazon S3 버킷을 비롯한 데이터 자산의 콘텐츠에 대한 세부 정보를 확인할 수 있습니다.

- **Purview 데이터 카탈로그를 검색** 하고 특정 버킷을 필터링합니다. 예를 들면 다음과 같습니다.

    ![카탈로그에서 AWS S3 자산을 검색합니다.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **인사이트 보고서를 살펴보면** 분류, 민감도 레이블, 파일 형식에 대한 통계 및 콘텐츠에 대한 자세한 정보를 볼 수 있습니다.

    모든 Purview 인사이트 보고서에는 Azure 데이터 원본의 나머지 결과와 함께 Amazon S3 스캔 결과가 포함됩니다. 관련된 경우 추가 **Amazon S3** 자산 유형이 보고서 필터링 옵션에 추가됩니다.

    자세한 내용은 [Azure Purview의 인사이트 이해](concept-insights.md)를 참조하세요.

## <a name="minimum-permissions-for-your-aws-policy"></a>AWS 정책에 대한 최소 권한

S3 버킷을 스캔할 때 사용할 [Purview용 AWS 역할을 만드는](#create-a-new-aws-role-for-purview) 기본 절차에는 **AmazonS3ReadOnlyAccess** 정책이 사용됩니다.

**AmazonS3ReadOnlyAccess** 정책은 S3 버킷을 스캔하는 데 필요한 최소 권한을 제공하며 다른 권한도 포함할 수 있습니다.

버킷을 스캔하는 데 필요한 최소 권한만 적용하려면 계정의 단일 버킷을 스캔할지 또는 모든 버킷을 스캔할지에 따라 다음 섹션에 나열된 권한으로 새 정책을 만듭니다.

**AmazonS3ReadOnlyAccess** 대신 역할에 새 정책을 적용합니다.

### <a name="individual-buckets"></a>개별 버킷

개별 S3 버킷을 스캔하는 경우 최소 AWS 권한에는 다음이 포함됩니다.

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

특정 버킷 이름으로 리소스를 정의해야 합니다. 예를 들면 다음과 같습니다.

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>계정의 모든 버킷

AWS 계정의 모든 버킷을 스캔하는 경우 최소 AWS 권한에는 다음이 포함됩니다.

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

와일드카드를 사용하여 리소스를 정의해야 합니다. 예를 들면 다음과 같습니다.

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

Azure Purview 인사이트 보고서에 대해 자세히 알아보기

> [!div class="nextstepaction"]
> [Azure Purview의 인사이트 이해](concept-insights.md)
