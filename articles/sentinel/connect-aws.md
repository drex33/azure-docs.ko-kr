---
title: Microsoft 센티널을 커넥트 하 여 AWS 서비스 로그 데이터 수집 Amazon Web Services
description: AWS 커넥터를 사용 하 여 AWS 리소스 로그에 Microsoft 센티널 액세스를 위임 하 고 Amazon Web Services와 Microsoft 센티널 간의 트러스트 관계를 만듭니다.
author: yelevin
ms.topic: how-to
ms.date: 11/18/2021
ms.author: yelevin
ms.openlocfilehash: d47fd475da45999902dadc81204d267565aac820
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132940812"
---
# <a name="connect-microsoft-sentinel-to-amazon-web-services-to-ingest-aws-service-log-data"></a>Microsoft 센티널을 커넥트 하 여 AWS 서비스 로그 데이터 수집 Amazon Web Services

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

AWS (Amazon Web Services) 커넥터를 사용 하 여 AWS 서비스 로그를 Microsoft 센티널로 끌어옵니다. 이러한 커넥터는 Microsoft 센티널에 AWS 리소스 로그에 대 한 액세스 권한을 부여 하 여 작동 합니다. 커넥터를 설정 하면 Amazon Web Services와 Microsoft 센티널 간에 트러스트 관계가 설정 됩니다. 이는 AWS 로그에 액세스할 수 있도록 Microsoft 센티널에 권한을 부여 하는 역할을 만들어 AWS에서 수행 됩니다.

이 커넥터는 다음 두 가지 버전으로 사용할 수 있습니다. CloudTrail 관리 및 데이터 로그용 레거시 커넥터, S3 버킷에 AWS 하 여 다음 서비스에서 로그를 수집할 수 있는 새 버전.

- [AMAZON VPC (가상 사설 클라우드)](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)  -  [VPC Flow 로그](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
- [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html)  -  검색 [결과](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings.html)
- [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html)  -  [관리](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-events-with-cloudtrail.html) 및 [데이터](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) 이벤트

> [!IMPORTANT]
>
> - Amazon Web Services S3 커넥터는 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

# <a name="s3-connector-new"></a>[S3 커넥터 (신규)](#tab/s3)

이 문서에서는 새 AWS S3 커넥터를 구성 하는 방법을 설명 합니다. 설정 하는 프로세스는 두 부분으로 구성 됩니다. AWS side 및 Microsoft 센티널 side.

1. AWS 환경에서:

    - **S3 버킷에** 로그를 보내도록 AWS 서비스를 구성 합니다.

    - 알림을 제공 하려면 **SQS (Simple Queue Service) 큐** 를 만듭니다.

    - AWS 리소스에 액세스할 수 있도록 Microsoft 센티널 계정 (외부 ID)에 대 한 사용 권한을 부여 하는 **가정 된 역할** 을 만듭니다.

    - 적절 한 리소스 (S3 버킷, SQS)에 대 한 Microsoft 센티널 액세스 권한을 부여 하려면 적절 한 **IAM 권한 정책을** 연결 합니다.

1. Microsoft 센티널:

    - Microsoft 센티널 포털에서 **AWS S3 커넥터** 를 사용 하도록 설정 하 고 구성 합니다. 아래의 지침을 따르세요.

각 측의 프로세스는 다른 쪽에서 사용 되는 정보를 생성 합니다. 이 공유는 보안 통신을 만듭니다.

microsoft는 **이 프로세스의 AWS 측면을 자동화** 하는 스크립트를 GitHub 리포지토리에서 사용할 수 있게 되었습니다. 이 문서의 뒷부분에 나오는 [자동 설치](#automatic-setup) 에 대 한 지침을 참조 하세요.

## <a name="architecture-overview"></a>아키텍처 개요

이 그래픽 및 다음 텍스트는이 커넥터 솔루션의 파트가 상호 작용 하는 방법을 보여 줍니다.

:::image type="content" source="media/connect-aws/s3-connector-architecture.png" alt-text="W S 3 커넥터 아키텍처의 스크린샷":::

- AWS services는 S3 (Simple Storage Service) 저장소 버킷에 로그를 보내도록 구성 되어 있습니다.

- S3 버킷은 새 로그를 받을 때마다 SQS (Simple Queue Service) 메시지 큐에 알림 메시지를 보냅니다.

- Microsoft 센티널 AWS S3 커넥터는 정기적으로 잦은 간격으로 SQS 큐를 폴링합니다. 큐에 메시지가 있으면 로그 파일에 대 한 경로가 포함 됩니다.

- 커넥터는 경로를 사용 하 여 메시지를 읽은 다음 S3 버킷에 있는 파일을 페치합니다.

- SQS 큐 및 S3 버킷에 연결 하기 위해 Microsoft 센티널은 AWS S3 커넥터 구성에 포함 된 AWS 자격 증명 및 연결 정보를 사용 합니다. AWS 자격 증명은 해당 리소스에 대 한 액세스를 제공 하는 역할 및 사용 권한 정책으로 구성 됩니다. 마찬가지로, Microsoft 센티널 작업 영역 ID는 AWS 구성에 포함 되어 있으므로 양방향 인증을 적용 합니다.

## <a name="global-prerequisites"></a>전역 전제 조건

Microsoft 센티널 작업 영역에 대 한 쓰기 권한이 있어야 합니다.

## <a name="automatic-setup"></a>자동 설치

온 보 딩 프로세스를 간소화 하기 위해 Microsoft 센티널은 필요한 AWS 리소스, 자격 증명 및 권한으로 커넥터의 AWS 측 [설정을 자동화 하는 PowerShell 스크립트](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/AWS-S3) 를 제공 했습니다.

스크립트는 다음 작업을 수행 합니다.

- 지정 된 S3 버킷 및 SQS 큐의 로그에 Microsoft 센티널 액세스 권한을 부여 하는 데 필요한 최소한의 권한을 가진 *IAM 가정 역할* 을 만듭니다.

- 지정 된 AWS 서비스에서 해당 S3 버킷에 로그를 보내고 해당 SQS 큐에 알림 메시지를 보낼 수 있도록 합니다.

- 필요한 경우에는이 목적을 위해 S3 버킷을 만들고 해당 SQS 큐를 만듭니다.

- 필요한 IAM 권한 정책을 구성 하 고 위에서 만든 IAM 역할에 적용 합니다.

### <a name="prerequisites"></a>사전 요구 사항

컴퓨터에 PowerShell 및 AWS CLI가 있어야 합니다.

   - [PowerShell 설치 지침](/powershell/scripting/install/installing-powershell)
   - [AWS CLI에 대 한 설치 지침](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

### <a name="instructions"></a>Instructions

커넥터를 설정 하는 스크립트를 실행 하려면 다음 단계를 사용 합니다.

1. Microsoft 센티널 탐색 메뉴에서 **데이터 커넥터** 를 선택 합니다.

1. 데이터 커넥터 갤러리에서 **Amazon Web Services** s 3을 선택 하 고 세부 정보 창에서 **커넥터 페이지 열기** 를 선택 합니다.

1. **구성** 섹션에서 1로 설정 **합니다. AWS 환경을 설정** 하 고 **PowerShell 스크립트를 사용 하 여 설치 (권장)** 를 확장 합니다.

1. 화면의 지시에 따라 [AWS S3 설치 스크립트](https://github.com/Azure/Azure-Sentinel/blob/master/DataConnectors/AWS-S3/ConfigAwsS3DataConnectorScripts.zip?raw=true) 를 다운로드 하 고 압축을 풉니다 (링크는 기본 설치 스크립트 및 도우미 스크립트를 포함 하는 zip 파일을 다운로드 합니다). 커넥터 페이지에서 다운로드 합니다.

1. 스크립트를 실행 하기 전에 `aws configure` PowerShell 명령줄에서 명령을 실행 하 고 메시지가 표시 되 면 관련 정보를 입력 합니다. AWS 명령줄 인터페이스를 참조 하세요. [ ](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) 세부 정보에 대 한 구성 기본 사항입니다.

1. 이제 스크립트를 실행 합니다. 커넥터 페이지 ("환경 설정 스크립트 실행")에서 명령을 복사 하 여 명령줄에 붙여넣습니다.

1. 스크립트에서 작업 영역 ID를 입력 하 라는 메시지를 표시 합니다. 이 ID는 커넥터 페이지에 표시 됩니다. 스크립트를 복사 하 여 스크립트의 프롬프트에 붙여 넣습니다.

   :::image type="content" source="media/connect-aws/aws-run-script.png" alt-text="설치 스크립트 및 작업 영역 I D를 실행 하는 명령의 스크린샷" lightbox="media/connect-aws/aws-run-script.png":::

1. 스크립트 실행이 완료 되 면 스크립트의 출력에서 **역할 ARN** 및 **sqs URL** 을 복사 하 고 (아래 첫 번째 스크린샷 참조) 커넥터 페이지의 해당 필드에 2 아래에 붙여 넣습니다 **. 연결 추가** (아래의 두 번째 스크린샷 참조).

   :::image type="content" source="media/connect-aws/aws-script-output.png" alt-text="W S 커넥터 설치 스크립트의 출력 스크린샷" lightbox="media/connect-aws/aws-script-output.png":::

   :::image type="content" source="media/connect-aws/aws-add-connection-auto.png" alt-text="스크립트의 A W S 역할 정보를 S3 커넥터에 붙여 넣는 스크린샷" lightbox="media/connect-aws/aws-add-connection-auto.png":::

1. **대상 테이블** 드롭다운 목록에서 데이터 형식을 선택 합니다. 이는 커넥터에서이 연결을 수집 하도록 설정 하는 AWS 서비스의 로그와 수집 데이터를 저장할 Log Analytics 테이블을 알려 줍니다. 그런 다음 **연결 추가** 를 선택 합니다.

> [!NOTE]
> 스크립트가 실행을 완료 하는 데 최대 30 분이 걸릴 수 있습니다.

## <a name="manual-setup"></a>수동 설치

자동 설치 스크립트를 사용 하 여이 커넥터를 배포 하는 것이 좋습니다. 이 편의를 활용 하지 않으려는 어떤 이유로 든 커넥터를 수동으로 설정 하려면 아래 단계를 따르세요.

### <a name="prerequisites"></a>사전 요구 사항

- AWS services-VPC, GuardDuty 또는 CloudTrail에서 로그를 배송 하는 **S3 버킷이** 있어야 합니다.

    - AWS에서 [S3 저장소 버킷을](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) 만듭니다.

- S3 버킷에 알림이 게시 될 **Sqs 메시지 큐** 가 있어야 합니다.

    - AWS에서 [표준 SQS (Simple Queue Service) 큐](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html) 를 만듭니다.

### <a name="instructions"></a>Instructions

수동 설치는 다음 단계로 구성 됩니다.
- [AWS으로 간주 되는 역할을 만들고 AWS 센티널 계정에 대 한 액세스 권한을 부여 합니다.](#create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account)
- [로그를 S3 버킷으로 내보내도록 AWS 서비스 구성](#configure-an-aws-service-to-export-logs-to-an-s3-bucket)
- [AWS에서 SQS (Simple Queue Service) 만들기](#create-a-simple-queue-service-sqs-in-aws)
- [SQS 알림 사용](#enable-sqs-notification)
- [IAM 권한 정책 적용](#apply-iam-permissions-policies)

#### <a name="create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account"></a>AWS으로 간주 되는 역할을 만들고 AWS 센티널 계정에 대 한 액세스 권한을 부여 합니다.

1. Microsoft 센티널에서 **데이터 커넥터** 를 선택한 다음 테이블의 **Amazon Web Services S3** 줄을 선택 하 고 오른쪽에 있는 AWS 창에서 **커넥터 열기 페이지** 를 선택 합니다.

1. **구성** 에서 **외부 Id (작업 영역 id)** 를 복사 하 여 따로 붙여넣습니다.
 
1. AWS 관리 콘솔의 **보안, id & 준수** 에서 **IAM** 을 선택 합니다.

   :::image type="content" source="media/connect-aws/aws-select-iam.png" alt-text="Amazon Web Services 콘솔의 스크린샷":::

1. **역할** 을 선택하고 **역할 만들기** 를 선택합니다.

   :::image type="content" source="media/connect-aws/aws-select-roles.png" alt-text="W S 역할 만들기 화면의 스크린샷.":::

1. **다른 AWS 계정** 을 선택합니다. **계정 ID** 필드에 번호 **197857026523** (여기에서 복사 하 여 붙여 넣을 수 있음)을 입력 합니다. 이 번호는 **AWS에 대 한 Microsoft 센티널의 서비스 계정 ID** 입니다. 이 역할을 사용 하는 계정이 Microsoft 센티널 사용자 임을 AWS에 알립니다.

   :::image type="content" source="media/connect-aws/aws-enter-account.png" alt-text="W S 역할 구성 화면의 스크린샷.":::

1. 외부 **ID 필요** 확인란을 선택한 다음, Microsoft Sentinel 포털의 AWS 커넥터 페이지에서 복사하여 붙여넣은 **외부 ID(작업 영역 ID)를** 입력합니다. 그런 **다음, 다음: 사용 권한을 선택합니다.**

   :::image type="content" source="media/connect-aws/aws-enter-external-id.png" alt-text="WS 역할 구성 화면의 연속 스크린샷.":::

1. 지금은 다음 단계인 권한 정책 연결 을 **건너뜁니다.** 나중에 [에 지시하면](#apply-iam-permissions-policies)다시 돌아갑니다. 완료되면 **다음: 태그** 를 선택합니다.

   :::image type="content" source="media/connect-aws/aws-skip-permissions.png" alt-text="다음: 태그의 스크린샷.":::

1. **태그를** 입력합니다(선택 사항). 그런 다음, **Next: 검토** 를 클릭합니다.

   :::image type="content" source="media/connect-aws/aws-add-tags.png" alt-text="태그 화면의 스크린샷.":::

1. 역할 **이름을** 입력하고 **역할 만들기를** 선택합니다.

   :::image type="content" source="media/connect-aws/aws-create-role.png" alt-text="역할 명명 화면의 스크린샷.":::

1. **역할** 목록에서 만든 새 역할을 선택합니다.

   :::image type="content" source="media/connect-aws/aws-select-role.png" alt-text="역할 목록 화면의 스크린샷.":::

1. 역할 **ARN을** 복사하여 붙여넣습니다.

   :::image type="content" source="media/connect-aws/aws-copy-role-arn.png" alt-text="역할 A R N을 복사하는 스크린샷":::

1. AWS SQS 대시보드에서 만든 SQS 큐를 선택하고 큐의 URL을 복사합니다.

    :::image type="content" source="media/connect-aws/aws-copy-queue-url.png" alt-text="S QS 큐 UL을 복사하는 스크린샷.":::

1. Microsoft Sentinel 포털의 AWS S3 커넥터 페이지에서 2 아래에 **있습니다. 연결 추가:**
    1. 2단계 전에 복사한 IAM 역할 ARN을 **역할 ARN** 필드에 붙여넣습니다.
    1. 마지막 단계에서 복사한 SQS 큐의 URL을 **SQS URL** 필드에 붙여넣습니다.
    1. **대상 테이블** 드롭다운 목록에서 데이터 형식을 선택합니다. 이렇게 하면 이 연결을 수집하기 위해 설정된 AWS 서비스의 로그와 수집된 데이터를 저장할 Log Analytics 테이블을 커넥터에 알릴 수 있습니다.
    1. **연결 추가** 를 선택합니다.

   :::image type="content" source="media/connect-aws/aws-add-connection.png" alt-text="S3 커넥터에 A W S 역할 연결을 추가하는 스크린샷." lightbox="media/connect-aws/aws-add-connection.png":::

#### <a name="configure-an-aws-service-to-export-logs-to-an-s3-bucket"></a>로그를 S3 버킷으로 내보내도록 AWS 서비스 구성

- [S3 버킷에 VPC 흐름 로그를 게시합니다.](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-s3.html)

    > [!NOTE]
    > 로그 형식을 사용자 지정하도록 선택하는 경우 Log Analytics 작업 영역의 *TimeGenerated* 필드에 매핑되는 *시작* 특성을 포함해야 합니다. 그렇지 않으면 *TimeGenerated* 필드는 로그 이벤트를 정확하게 설명하지 않는 이벤트의 *인을 통해* 채워집니다.

- [GuardDuty 결과를 S3 버킷으로](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_exportfindings.html)내보냅니다.

    > [!NOTE]
    > *TimeGenerated* 필드는 결과 *업데이트 값으로* 채워집니다.

- AWS CloudTrail 내역은 기본적으로 S3 버킷에 저장됩니다.
    - [단일 계정 에 대한 내역을 만듭니다.](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html)
    - [조직 전체의 여러 계정에 걸쳐 내역을 만듭니다.](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-trail-organization.html)

#### <a name="create-a-simple-queue-service-sqs-in-aws"></a>AWS에서 SQS(Simple Queue Service) 만들기

[SQS 큐를](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-configure-create-queue.html)아직 만들지 않은 경우 지금 이 작업을 수행합니다.

#### <a name="enable-sqs-notification"></a>SQS 알림 사용

[SQS 큐에 알림을 보내도록 S3 버킷을 구성합니다.](https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-event-notifications.html)

#### <a name="apply-iam-permissions-policies"></a>IAM 권한 정책 적용

[만든 Microsoft Sentinel 역할에](#create-an-aws-assumed-role-and-grant-access-to-the-aws-sentinel-account) 적용해야 하는 권한 정책은 다음과 같습니다.

- AmazonSQSReadOnlyAccess
- AWSLambdaSQSQueueExecutionRole
- AmazonS3ReadOnlyAccess
- 액세스 KMS

   이러한 정책과 다양한 유형의 AWS 서비스 로그를 수신하기 위해 적용해야 하는 추가 정책에 대한 자세한 내용은 GitHub 리포지션의 [AWS S3 커넥터 사용 권한 정책 페이지를](https://github.com/Azure/Azure-Sentinel/blob/master/DataConnectors/AWS-S3/AwsRequiredPolicies.md) 참조하세요.

## <a name="known-issues-and-troubleshooting"></a>알려진 문제 및 문제 해결

### <a name="known-issues"></a>알려진 문제

- 다른 유형의 로그는 동일한 S3 버킷에 저장할 수 있지만 동일한 경로에 저장해서는 안 됩니다.

- 각 SQS 큐는 하나의 메시지 유형을 가리킵니다. 따라서 GuardDuty 결과 *및* VPC 흐름 로그를 검색하려는 경우 각 형식에 대해 별도의 큐를 설정해야 합니다.

- 마찬가지로 단일 SQS 큐는 S3 버킷에서 하나의 경로만 제공할 수 있으므로 어떤 이유로든 로그를 여러 경로에 저장하려면 각 경로에 고유한 전용 SQS 큐가 필요합니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

1. **로그 데이터가 S3 버킷에 있는지 확인합니다.**

   S3 버킷 대시보드를 보고 데이터가 해당 대시보드로 흐르는지 확인합니다. 그렇지 않은 경우 AWS 서비스를 올바르게 설정했는지 확인합니다.

1. **메시지가 SQS 큐에 도착했는지 확인합니다.**

   AWS SQS 큐 대시보드 보기 - 모니터링 탭 아래에 "보낸 메시지 수" 그래프 위젯에 트래픽이 표시됩니다. 트래픽이 표시되지 않으면 S3 PUT 개체 알림이 올바르게 구성되었는지 확인합니다. 

1. **SQS 큐에서 메시지를 읽고 있는지 확인합니다.**

   큐 대시보드에서 "받은 메시지 수" 및 "삭제된 메시지 수" 위젯을 확인합니다. 삭제된 메시지 아래에 알림이 없으면 상태 메시지를 확인합니다. 일부 권한이 누락된 것일 수 있습니다. IAM 구성을 확인합니다.

# <a name="cloudtrail-connector-legacy"></a>[CloudTrail 커넥터(레거시)](#tab/ct)

> [!NOTE]
> AWS CloudTrail은 LookupEvents API에 [기본적으로 제한](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)이 있습니다. 계정별로 초당 2개 이하의 트랜잭션을 허용하며 각 쿼리는 최대 50개의 레코드를 반환할 수 있습니다. 따라서 단일 테넌트가 한 지역에서 초당 100개 이상의 레코드를 지속적으로 생성하는 경우 데이터 수집의 백로그 및 지연이 발생합니다.
> 현재 AWS Commercial CloudTrail은 MICROSOFT Sentinel에만 연결할 수 있으며 AWS GovCloud CloudTrail에는 연결할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

Microsoft Sentinel 작업 영역에 대한 쓰기 권한이 있어야 합니다.

> [!NOTE]
> Microsoft Sentinel은 모든 지역에서 CloudTrail 관리 이벤트를 수집합니다. 한 지역에서 다른 지역으로 이벤트를 스트리밍하는 것이 좋습니다.

## <a name="connect-aws-cloudtrail"></a>AWS CloudTrail 커넥트

1. Microsoft Sentinel에서 **데이터 커넥터를** 선택한 다음, 테이블에서 **Amazon Web Services** 줄을 선택하고 오른쪽의 AWS 창에서 **커넥터 페이지 열기를** 선택합니다.

1. 다음 단계를 사용하여 **구성** 의 지침을 따릅니다.
 
1.  Amazon Web Services 콘솔의 **보안, ID 및 규정 준수** 에서 **IAM** 을 선택합니다.

    :::image type="content" source="media/connect-aws/aws-select-iam.png" alt-text="Amazon Web Services 콘솔의 스크린샷.":::

1.  **역할** 을 선택하고 **역할 만들기** 를 선택합니다.

    :::image type="content" source="media/connect-aws/aws-select-roles.png" alt-text="WS 역할 만들기 화면의 스크린샷.":::

1. **다른 AWS 계정** 을 선택합니다. 계정 **ID** 필드에 **197857026523** 번호를 입력합니다(여기에서 복사하여 붙여넣을 수 있습니다). 이 번호는 **AWS에 대한 Microsoft Sentinel의 서비스 계정 ID입니다.** 이 역할을 사용하는 계정이 Microsoft Sentinel 사용자임을 AWS에 알릴 수 있습니다.

   :::image type="content" source="media/connect-aws/aws-enter-account.png" alt-text="WS 역할 구성 화면의 스크린샷.":::

1. 외부 **ID 필요** 확인란을 선택한 다음, Microsoft Sentinel의 AWS 커넥터 페이지에서 찾을 수 있는 **외부 ID(작업 영역 ID)를** 입력합니다. AWS에 *대한 특정 Microsoft Sentinel 계정을* 식별합니다. 그런 **다음, 다음: 사용 권한을 선택합니다.**

   :::image type="content" source="media/connect-aws/aws-enter-external-id.png" alt-text="WS 역할 구성 화면의 연속 스크린샷.":::

1.  **권한 정책 연결에서** **AWSCloudTrailReadOnlyAccess** 옆에 있는 확인란을 표시합니다. 이후 **다음: 태그** 를 선택합니다.

    :::image type="content" source="media/connect-aws/aws-apply-permissions.png" alt-text="WS 적용 권한 화면의 스크린샷.":::

1. **태그를** 입력합니다(선택 사항). 그런 다음, **Next: 검토** 를 클릭합니다.

   :::image type="content" source="media/connect-aws/aws-add-tags.png" alt-text="태그 화면의 스크린샷.":::

1. 역할 **이름을** 입력하고 **역할 만들기를** 선택합니다.

   :::image type="content" source="media/connect-aws/aws-create-role-ct.png" alt-text="역할 명명 화면의 스크린샷.":::

1. **역할** 목록에서 만든 새 역할을 선택합니다.

   :::image type="content" source="media/connect-aws/aws-select-role.png" alt-text="역할 목록 화면의 스크린샷.":::

1. 역할 **ARN을** 복사하여 붙여넣습니다.

   :::image type="content" source="media/connect-aws/aws-copy-role-arn.png" alt-text="역할 A R N을 복사하는 스크린샷":::

1.  Microsoft Sentinel의 Amazon Web Services 커넥터 페이지에서 **역할 ARN을 역할에** 붙여넣어 **필드를 추가하고 추가를** 선택합니다. 

    :::image type="content" source="media/connect-aws/aws-add-connection-ct.png" alt-text="AWS 커넥터에 W S 역할 연결을 추가 하는 스크린샷" lightbox="media/connect-aws/aws-add-connection-ct.png":::

1. AWS 이벤트의 Log Analytics에서 관련 스키마를 사용하려면 **AWSCloudTrail** 을 검색합니다.

    > [!IMPORTANT]
    > 2020년 12월 1일부터 **Awsrequestid** 필드는 **AwsRequestId_** 필드(밑줄이 추가됨)로 대체되었습니다. 이전 **Awsrequestid** 필드의 데이터는 고객의 지정된 데이터 보존 기간이 끝날 때까지 유지됩니다.

---

## <a name="next-steps"></a>다음 단계

이 문서에서는 AWS 리소스에 연결 하 여 로그를 Microsoft 센티널에 수집 하는 방법을 배웠습니다. Microsoft 센티널에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft 센티널을 사용 하 여 위협 검색을](detect-threats-built-in.md)시작 하세요.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
