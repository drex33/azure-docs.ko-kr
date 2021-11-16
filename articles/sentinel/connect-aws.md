---
title: Microsoft 센티널로 AWS CloudTrail 커넥트 | Microsoft Docs
description: AWS 커넥터를 사용 하 여 AWS 리소스 로그에 Microsoft 센티널 액세스를 위임 하 고 AWS CloudTrail와 Microsoft 센티널 간의 트러스트 관계를 만듭니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3475ac35692f793a40380993640069e42070883c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517067"
---
# <a name="connect-aws-cloudtrail-to-microsoft-sentinel"></a>Microsoft 센티널로 AWS CloudTrail 커넥트

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

AWS 커넥터를 사용 하 여 AWS CloudTrail 관리 이벤트를 Microsoft 센티널로 스트리밍합니다. 이 연결 프로세스는 AWS 리소스 로그에 Microsoft 센티널에 대 한 액세스를 위임 하 여 AWS CloudTrail와 Microsoft 센티널 간에 트러스트 관계를 만듭니다. 이는 AWS 로그에 액세스할 수 있도록 Microsoft 센티널에 권한을 부여 하는 역할을 만들어 AWS에서 수행 됩니다.

> [!NOTE]
> AWS CloudTrail은 LookupEvents API에 [기본적으로 제한](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)이 있습니다. 계정별로 초당 2개 이하의 트랜잭션을 허용하며 각 쿼리는 최대 50개의 레코드를 반환할 수 있습니다. 따라서 단일 테넌트가 한 지역에서 초당 100개 이상의 레코드를 지속적으로 생성하는 경우 데이터 수집의 백로그 및 지연이 발생합니다.
> 현재 AWS 상용 CloudTrail은 Microsoft 센티널에만 연결할 수 있으며 AWS GovCloud CloudTrail에는 연결할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

Microsoft 센티널 작업 영역에 대 한 쓰기 권한이 있어야 합니다.

> [!NOTE]
> Microsoft 센티널은 모든 지역에서 CloudTrail 관리 이벤트를 수집 합니다. 한 지역에서 다른 지역으로 이벤트를 스트리밍하는 것이 좋습니다.

## <a name="connect-aws"></a>AWS 연결 


1. Microsoft 센티널에서 **데이터 커넥터** 를 선택한 다음 테이블에서 **Amazon Web Services** 줄을 선택 하 고 오른쪽에 있는 AWS 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. 다음 단계를 사용하여 **구성** 의 지침을 따릅니다.
 
1.  Amazon Web Services 콘솔의 **보안, ID 및 규정 준수** 에서 **IAM** 을 선택합니다.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **역할** 을 선택하고 **역할 만들기** 를 선택합니다.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **다른 AWS 계정** 을 선택합니다. **계정 id** 필드에 microsoft 센티널 포털의 AWS 커넥터 페이지에서 찾을 수 있는 **microsoft 계정 id** (**123412341234**)를 입력 합니다.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  **외부 Id 필요** 가 선택 되어 있는지 확인 하 고, Microsoft 센티널 포털의 AWS 커넥터 페이지에서 찾을 수 있는 외부 Id (작업 영역 id)를 입력 합니다.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  **권한 정책 연결** 에서 **AWSCloudTrailReadOnlyAccess** 를 선택합니다.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  태그를 입력합니다(선택 사항).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  그런 다음, **역할 이름** 을 입력하고 **역할 만들기** 단추를 선택합니다.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  역할 목록에서 이전에 만든 역할을 선택합니다.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  **역할 ARN** 을 복사합니다. Microsoft 센티널 포털의 Amazon Web Services 커넥터 화면에서 **추가할 역할** 에 해당 필드를 붙여넣고 **추가** 를 선택 합니다.

    ![AWS9](./media/connect-aws/aws-9.png)

1. AWS 이벤트의 Log Analytics에서 관련 스키마를 사용하려면 **AWSCloudTrail** 을 검색합니다.

    > [!IMPORTANT]
    > 2020년 12월 1일부터 **Awsrequestid** 필드는 **AwsRequestId_** 필드(밑줄이 추가됨)로 대체되었습니다. 이전 **Awsrequestid** 필드의 데이터는 고객의 지정된 데이터 보존 기간이 끝날 때까지 유지됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 AWS CloudTrail를 Microsoft 센티널에 연결 하는 방법을 알아보았습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
