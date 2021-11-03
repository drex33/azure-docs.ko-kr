---
title: AWS CloudTrail를 Azure Sentinel에 연결 | Microsoft Docs
description: AWS 커넥터를 사용하여 AWS 리소스 로그에 대한 액세스 권한을 Azure Sentinel에 위임하고 AWS CloudTrail와 Azure Sentinel 간에 트러스트 관계를 만듭니다.
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
ms.date: 05/27/2020
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 86be5f6a7a777b40d85531100a4ef2d5d0dc99c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009475"
---
# <a name="connect-aws-cloudtrail-to-azure-sentinel"></a>AWS CloudTrail에서 Azure Sentinel에 연결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

AWS 커넥터를 사용하여 AWS CloudTrail 관리 이벤트를 Azure Sentinel로 스트리밍합니다. 이 연결 프로세스는 AWS 리소스 로그에 대한 액세스 권한을 Azure Sentinel에 위임하여 AWS CloudTrail와 Azure Sentinel 간에 트러스트 관계를 만듭니다. 이 작업은 AWS에서 AWS 로그에 액세스할 수 있는 권한을 Azure Sentinel에 부여하는 역할을 만들어 수행합니다.

> [!NOTE]
> AWS CloudTrail은 LookupEvents API에 [기본적으로 제한](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)이 있습니다. 계정별로 초당 2개 이하의 트랜잭션을 허용하며 각 쿼리는 최대 50개의 레코드를 반환할 수 있습니다. 따라서 단일 테넌트가 한 지역에서 초당 100개 이상의 레코드를 지속적으로 생성하는 경우 데이터 수집의 백로그 및 지연이 발생합니다.
> 현재 AWS Commercial CloudTrail을 Azure Sentinel에만 연결할 수 있고 AWS GovCloud CloudTrail에는 연결할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Sentinel 작업 영역에 대한 쓰기 권한이 있어야 합니다.

> [!NOTE]
> Azure Sentinel은 모든 지역에서 CloudTrail 관리 이벤트를 수집합니다. 한 지역에서 다른 지역으로 이벤트를 스트리밍하는 것이 좋습니다.

## <a name="connect-aws"></a>AWS 연결 


1. Azure Sentinel에서 **데이터 커넥터** 를 선택한 다음, 테이블에서 **Amazon Web Services** 줄을 선택하고 오른쪽에 있는 AWS 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 다음 단계를 사용하여 **구성** 의 지침을 따릅니다.
 
1.  Amazon Web Services 콘솔의 **보안, ID 및 규정 준수** 에서 **IAM** 을 선택합니다.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **역할** 을 선택하고 **역할 만들기** 를 선택합니다.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **다른 AWS 계정** 을 선택합니다. **계정 ID** 필드에 Azure Sentinel 포털의 AWS 커넥터 페이지에서 찾을 수 있는 **Microsoft 계정 ID**(**123412341234**)를 입력합니다.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  **외부 ID 필요** 가 선택되어 있는지 확인하고 Azure Sentinel 포털의 AWS 커넥터 페이지에서 찾을 수 있는 외부 ID(작업 영역 ID)를 입력합니다.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  **권한 정책 연결** 에서 **AWSCloudTrailReadOnlyAccess** 를 선택합니다.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  태그를 입력합니다(선택 사항).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  그런 다음, **역할 이름** 을 입력하고 **역할 만들기** 단추를 선택합니다.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  역할 목록에서 이전에 만든 역할을 선택합니다.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  **역할 ARN** 을 복사합니다. Azure Sentinel 포털의 Amazon Web Services 커넥터 화면에서 **추가할 역할** 필드에 역할을 붙여넣고 **추가** 를 선택합니다.

    ![AWS9](./media/connect-aws/aws-9.png)

1. AWS 이벤트의 Log Analytics에서 관련 스키마를 사용하려면 **AWSCloudTrail** 을 검색합니다.

    > [!IMPORTANT]
    > 2020년 12월 1일부터 **Awsrequestid** 필드는 **AwsRequestId_** 필드(밑줄이 추가됨)로 대체되었습니다. 이전 **Awsrequestid** 필드의 데이터는 고객의 지정된 데이터 보존 기간이 끝날 때까지 유지됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 AWS CloudTrail을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
