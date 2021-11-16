---
title: 클라우드용 Microsoft Defender에 AWS 계정 연결
description: Microsoft Defender for Cloud로 AWS 리소스 방어
author: memildin
ms.author: memildin
ms.date: 11/07/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: connect-aws-accounts
ms.openlocfilehash: 98636887b213a26baf638e6ebc2813a02f395b73
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990266"
---
#  <a name="connect-your-aws-accounts-to-microsoft-defender-for-cloud"></a>클라우드용 Microsoft Defender에 AWS 계정 연결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼에 걸쳐 있는 경우 클라우드 보안 서비스도 동일한 작업을 수행해야 합니다.

클라우드용 Microsoft Defender는 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 워크로드를 보호합니다.

AWS 기반 리소스를 보호하려면 다음 두 가지 메커니즘 중 하나를 사용하여 계정을 연결하면 됩니다.

- **클래식 클라우드 커넥터 환경** - 초기 다중 클라우드 제품의 일부로 이러한 클라우드 커넥터를 AWS 및 GCP 계정 연결 방법으로 도입했습니다. 클래식 클라우드 커넥터 환경을 통해 AWS 커넥터를 이미 구성한 경우 최신 메커니즘을 사용하여 계정을 다시 연결하는 것이 좋습니다. 환경 설정 페이지를 통해 계정을 추가한 경우 중복 권장 사항이 표시되지 않도록 이전 커넥터를 제거하세요.

- **환경 설정 페이지(미리 보기)** (권장) - 이 미리 보기 페이지는 대폭 개선되고 더욱 간단해진 온보딩 환경(자동 프로비저닝 포함)을 제공합니다. 또한 이 메커니즘은 Defender for Cloud의 향상된 보안 기능을 AWS 리소스로 확장합니다.

    - **Defender for Cloud의 CSPM 기능** 은 AWS 리소스로 확장됩니다. 이 에이전트 없는 플랜은 AWS 관련 보안 권장 사항에 따라 AWS 리소스를 평가하며, 평가 결과는 보안 점수에 포함됩니다. 또한 리소스가 AWS(AWS CIS, AWS PCI DSS 및 AWS Foundational 보안 모범 사례)와 관련된 기본 제공 표준을 준수하는지 여부도 평가됩니다. Defender for Cloud의 [자산 인벤토리 페이지](asset-inventory.md)는 Azure 리소스와 함께 AWS 리소스를 관리하는 데 도움이 되는 다중 클라우드 지원 기능입니다.
    - **Kubernetes용 Microsoft Defender** 는 컨테이너 위협 탐지 및 고급 방어를 **Amazon EKS Linux 클러스터** 로 확장합니다.
    - **서버용 Microsoft Defender** 는 위협 탐지 및 고급 방어를 Windows 및 Linux EC2 인스턴스로 확장합니다. 이 플랜에는 엔드포인트용 Microsoft Defender의 통합 라이선스, 보안 기준 및 OS 수준 평가, 취약성 평가 검사, AAC(적응형 애플리케이션 제어), FIM(파일 무결성 모니터링) 등이 포함됩니다.

다음은 Defender for Cloud의 [개요 대시보드](overview-page.md)에 표시된 AWS 계정을 보여주는 스크린샷입니다.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Defender for Cloud의 개요 대시보드에 나열된 4개의 AWS 프로젝트" lightbox="./media/quickstart-onboard-aws/aws-account-in-overview.png":::


::: zone pivot="env-settings"

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|가격 책정:|CSPM 플랜은 무료입니다.<br>Kubernetes용 Defender는 미리 보기 기간 동안 무료입니다. 그 후에는 Azure 리소스의 Kubernetes용 Defender 플랜과 동일한 가격으로 요금이 청구됩니다.<br>[Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 통해 Azure에 연결된 모든 AWS 머신의 경우 서버용 Defender 플랜은 Azure 머신의 서버용 Defender 플랜과 동일한 가격으로 요금이 청구됩니다. AWS EC2에 Azure Arc 에이전트가 배포되지 않은 경우 해당 머신에 대한 요금이 청구되지 않습니다.|
|필요한 역할 및 권한:|관련 Azure 구독의 **소유자**<br>소유자가 서비스 주체 세부 정보를 제공하면(서버용 Defender 플랜에 필요) **기여자** 도 AWS 계정을 연결할 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/지역(Azure Government, Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>사전 요구 사항

- AWS 계정을 Azure 구독에 연결하려면 AWS 계정에 대한 액세스 권한이 필요합니다.

- **Kubernetes용 Defender 플랜을 사용하려면** 다음이 필요합니다.
    - EKS K8s API 서버에 액세스할 수 있는 권한이 있는 하나 이상의 Amazon EKS 클러스터. 새 EKS 클러스터를 만들어야 하는 경우 [Amazon EKS 시작하기 – eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)의 지침을 따릅니다.
    - 클러스터의 지역에 새 SQS 큐, Kinesis Fire Hose 배달 스트림 및 S3 버킷을 만들 수 있는 리소스 용량.

- **서버용 Defender 플랜을 사용하려면** 다음이 필요합니다.
    - 사용하도록 설정된 서버용 Microsoft Defender([빠른 시작: 향상된 보안 기능 사용](enable-enhanced-security.md) 참조)
    - AWS Systems Manager(SSM)에서 관리하고 SSM 에이전트를 사용하는 EC2 인스턴스가 있는 활성 AWS 계정. 일부 AMI(Amazon Machine Images)에는 SSM 에이전트가 미리 설치되어 있으며, 해당 AMI는 [SSM 에이전트가 미리 설치된 AMI](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-technical-details.html#ami-preinstalled-agent)에 나열됩니다. EC2 인스턴스에 SSM 에이전트가 없는 경우 다음과 같은 Amazon의 관련 지침을 따르세요.
        - [하이브리드 환경용 SSM 에이전트(Windows) 설치](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-win.html)
        - [하이브리드 환경용 SSM 에이전트(Linux) 설치](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-linux.html)


## <a name="connect-your-aws-account"></a>AWS 계정 연결

아래 단계에 따라 AWS 클라우드 커넥터를 만듭니다. 

1. Defender for Cloud 메뉴에서 **환경 설정** 을 엽니다.
1. **환경 추가** > **Amazon Web Services**.를 선택합니다.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-environment-settings.png" alt-text="AWS 계정을 Azure 구독에 연결":::

1. 커넥터 리소스를 저장할 위치를 포함하여 AWS 계정의 세부 정보를 입력하고 **다음: 플랜 선택** 을 선택합니다.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-details.png" alt-text="AWS 계정 추가 마법사의 1단계: 계정 세부 정보를 입력합니다.":::

1. [플랜 선택] 탭에서는 이 AWS 계정에 사용할 Defender for Cloud 기능을 선택합니다. 

    > [!NOTE]
    > 기능마다 권한에 대한 고유의 요구 사항이 있으며 요금이 발생할 수 있습니다.

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-plans-selection.png" alt-text="[플랜 선택] 탭에서는 이 AWS 계정에 사용할 Defender for Cloud 기능을 선택합니다.":::

    > [!IMPORTANT]
    > 권장 사항의 현재 상태를 표시하기 위해 CSPM 계획은 AWS 리소스 API를 하루에 여러 번 쿼리합니다. 이러한 읽기 전용 API 호출에는 요금이 발생하지 않지만 읽기 이벤트에 대한 흔적을 사용하도록 설정한 경우 CloudTrail에 *등록* 됩니다. [AWS 설명서](https://aws.amazon.com/cloudtrail/pricing/)에서 설명한 대로 하나의 흔적을 유지하는 데 드는 추가 비용이 없습니다. AWS에서 데이터를 내보내는 경우(예: 외부 SIEM으로) 증가된 이 호출 양으로 인해 수집 비용도 증가할 수 있습니다. 이러한 경우 Defender for Cloud 사용자 또는 역할 ARN(arn:aws:iam::[accountId]:role/CspmMonitorAws)의 읽기 전용 호출을 필터링하는 것이 좋습니다(이는 기본 역할 이름이며 계정에 구성된 역할 이름을 확인함).

    - 서버용 Defender 범위를 AWS EC2로 확장하려면 **서버** 플랜을 **켜기** 로 설정하고 구성을 필요한 대로 편집합니다. 

    - Kubernetes용 Defender 범위를 AWS EKS Linux 클러스터로 확장하려면 **컨테이너** 플랜을 **켜기** 로 설정하고 구성을 필요한 대로 편집합니다.

1. 다음 단계를 수행하여 설정을 완료합니다.
    1. **다음: 액세스 구성** 을 선택합니다.
    1. CloudFormation 템플릿을 다운로드합니다.
    1. 다운로드한 CloudFormation 템플릿을 사용하여 화면의 지시에 따라 AWS에서 스택을 만듭니다.
    1. **다음: 검토 및 생성** 을 선택합니다.
    1. **만들기** 를 선택합니다.

Defender for Cloud가 AWS 리소스 검사를 즉시 시작하며, 몇 시간 내에 보안 권장 사항이 표시됩니다.

::: zone-end


::: zone pivot="classic-connector"

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Microsoft Defender](defender-for-servers-introduction.md)가 필요합니다.|
|필요한 역할 및 권한:|관련 Azure 구독의 **소유자**<br>소유자가 서비스 주체 세부 정보를 제공하는 경우에는 **기여자** 를 AWS 계정에 연결할 수도 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||


## <a name="connect-your-aws-account"></a>AWS 계정 연결

아래 단계에 따라 AWS 클라우드 커넥터를 만듭니다. 

### <a name="step-1-set-up-aws-security-hub"></a>1단계: AWS Security Hub 설정:

1. 여러 영역에 대한 보안 권장 사항을 보려면 관련된 각 지역에 대해 다음 단계를 반복합니다.

    > [!IMPORTANT]
    > AWS 관리 계정을 사용하는 경우 다음 세 단계를 반복하여 모든 관련 지역에서 관리 계정 및 연결된 모든 구성원 계정을 구성합니다.

    1. [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)를 사용하도록 설정합니다.
    1. [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)를 사용하도록 설정합니다.
    1. 데이터가 Security Hub로 전송되는지 확인합니다. 처음으로 Security Hub를 사용하도록 설정하는 경우 데이터를 사용할 수 있기까지 몇 시간이 걸릴 수 있습니다.

### <a name="step-2-set-up-authentication-for-defender-for-cloud-in-aws"></a>2단계. AWS에서 Defender for Cloud에 대한 인증 설정

Defender for Cloud가 AWS에 인증할 수 있도록 허용하는 방법에는 두 가지가 있습니다.

- **Defender for Cloud에 대한 IAM 역할 만들기**(권장) - 가장 안전한 방법입니다.
- **Defender for Cloud에 대한 AWS 사용자** - IAM을 사용하도록 설정하지 않을 경우 덜 안전한 옵션입니다.

#### <a name="create-an-iam-role-for-defender-for-cloud"></a>Defender for Cloud에 대한 IAM 역할 만들기
1. Amazon Web Services 콘솔의 **보안, ID 및 규정 준수** 에서 **IAM** 을 선택합니다.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS 서비스.":::

1. **역할** 을 선택하고 **역할 만들기** 를 선택합니다.
1. 다음 페이지에서 **다른 AWS 계정** 을 선택합니다.
1. 다음 세부 정보를 입력합니다.

    - **계정 ID** - 서버용 Defender의 AWS 커넥터 페이지에 표시된 것처럼 Microsoft 계정 ID(**158177204117**)를 입력합니다.
    - **필요한 외부 ID** - 선택해야 합니다.
    - **외부 ID** - Defender for Cloud의 AWS 커넥터 페이지에 표시된 것처럼 구독 ID를 입력합니다. 

1. **다음** 을 선택합니다.
1. **권한 정책 연결** 섹션에서 다음 [AWS 관리형 정책](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html)을 선택합니다.

    - SecurityAudit(`arn:aws:iam::aws:policy/SecurityAudit`)
    - AmazonSSMAutomationRole(`arn:aws:iam::aws:policy/service-role/AmazonSSMAutomationRole`)
    - AWSSecurityHubReadOnlyAccess(`arn:aws:iam::aws:policy/AWSSecurityHubReadOnlyAccess`)

1. 필요에 따라 태그를 추가합니다. 사용자에게 태그를 추가해도 연결에 영향을 주지 않습니다.
1. **다음** 을 선택합니다.

1. 역할 목록에서, 이전에 생성된 역할을 선택합니다.

1. 나중에 사용할 수 있도록 ARN(Amazon Resource Name)을 저장합니다. 

#### <a name="create-an-aws-user-for-defender-for-cloud"></a>Defender for Cloud에 대해 AWS 사용자 만들기 
1. **사용자** 탭을 열고 **사용자 추가** 를 선택합니다.
1. **세부 정보** 단계에서 Defender for Cloud의 사용자 이름을 입력하고 AWS 액세스 형식으로 **프로그래밍 방식 액세스** 를 선택합니다. 
1. **Next Permissions**(다음: 권한)를 클릭합니다.
1. **기존 정책 직접 연결** 을 선택하고 다음 정책을 적용합니다.
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 완료되면 **다음: 태그** 를 선택합니다. 필요에 따라 태그를 추가합니다. 사용자에게 태그를 추가해도 연결에 영향을 주지 않습니다.
1. **검토** 를 선택합니다.
1. 나중에 사용할 수 있도록 자동으로 생성된 **액세스 키 ID** 및 **비밀 액세스 키** CSV 파일을 저장합니다.
1. 요약 정보를 검토하고 **사용자 만들기** 를 선택합니다.


### <a name="step-3-configure-the-ssm-agent"></a>3단계: SSM 에이전트 구성

AWS Systems Manager는 AWS 리소스의 작업을 자동화하는 데 필요합니다. EC2 인스턴스에 SSM 에이전트가 없는 경우 다음과 같은 Amazon의 관련 지침을 따르세요.

- [Windows 인스턴스에 SSM Agent 설치 및 구성](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Amazon EC2 Linux 인스턴스에 SSM Agent 설치 및 구성](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>4단계. Azure Arc 사전 요구 사항 완료
1. 적절한 [Azure 리소스 공급자](../azure-arc/servers/agent-overview.md#register-azure-resource-providers)가 등록되었는지 확인합니다.
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. 대규모 온보딩을 위한 서비스 주체를 만듭니다. 온보딩에 사용하려는 구독의 **소유자** 로서, [대규모 온보딩을 위한 서비스 주체 만들기](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)에 설명된 대로 Azure Arc 온보딩의 서비스 주체를 만듭니다.


### <a name="step-5-connect-aws-to-defender-for-cloud"></a>5단계. Defender for Cloud에 AWS 연결

1. Defender for Cloud의 메뉴에서 **환경 설정** 을 열고 클래식 커넥터 환경으로 다시 전환하는 옵션을 선택합니다.

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Defender for Cloud에서 다시 클래식 클라우드 커넥터 환경으로 전환합니다.":::

1. **AWS 계정 추가** 를 선택합니다.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Defender for Cloud의 다중 클라우드 커넥터 페이지에 표시되는 [AWS 계정 추가] 단추":::
1. 다음과 같이 **AWS 인증** 탭에서 옵션을 구성합니다.
    1. 커넥터의 **표시 이름** 을 입력합니다.
    1. 구독이 올바른지 확인합니다. 커넥터 및 AWS Security Hub 권장 사항을 포함할 구독입니다.
    1. 인증 옵션에 따라 [2단계. AWS에서 Defender for Cloud에 대한 인증 설정](#step-2-set-up-authentication-for-defender-for-cloud-in-aws)에서 선택했습니다.
        - **역할 맡기** 를 선택하고 [Defender for Cloud의 IAM 역할 만들기](#create-an-iam-role-for-defender-for-cloud)에서 ARN을 붙여넣습니다.

            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Azure Portal에서 AWS 연결 마법사의 관련 필드에 ARN 파일 붙여넣기.":::

            또는

        - **자격 증명** 을 선택하고, [Defender for Cloud에 대해 AWS 사용자 만들기](#create-an-aws-user-for-defender-for-cloud)에서 저장한 .csv 파일의 **액세스 키** 및 **비밀 키** 를 붙여넣습니다.
1. **다음** 을 선택합니다.
1. **Azure Arc 구성** 탭에서 옵션을 구성합니다.

    Defender for Cloud는 연결된 AWS 계정에서 EC2 인스턴스를 검색하고 SSM을 사용하여 Azure Arc에 온보딩합니다. 

    > [!TIP]
    > 지원되는 운영 체제 목록은 FAQ에서 [내 EC2 인스턴스에 어떤 운영 체제가 지원되나요?](#what-operating-systems-for-my-ec2-instances-are-supported)를 참조하세요.

    1. 선택한 구독에서 검색된 AWS EC2를 온보딩할 **리소스 그룹** 및 **Azure 지역** 을 선택합니다.
    1. [대규모 온보딩을 위한 서비스 주체 만들기](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)에 설명된 대로 **서비스 주체 ID** 및 Azure Arc에 대한 **서비스 주체 클라이언트 암호** 를 입력합니다.
    1. 머신이 프록시 서버를 통해 인터넷에 연결하는 경우 해당 머신이 프록시 서버와 통신하는 데 사용할 프록시 서버 IP 주소 또는 이름과 포트 번호를 지정합니다. 해당 값을 ```http://<proxyURL>:<proxyport>``` 형식으로 입력합니다.
    1. **검토 + 만들기** 를 선택합니다.

        요약 정보 검토

        Azure에서 쉽게 알아볼 수 있도록 태그 섹션에는 온보딩된 각 EC2에 대해 자동으로 생성되는 모든 Azure 태그와 관련 세부 정보가 나열됩니다. 

        Azure 태그에 대한 자세한 내용은 [태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성](../azure-resource-manager/management/tag-resources.md)을 참조하세요.

### <a name="step-6-confirmation"></a>6단계. 확인

커넥터가 성공적으로 생성되고 AWS Security Hub가 올바르게 구성된 경우:

- Defender for Cloud는 환경에서 AWS EC2 인스턴스를 검색하여 Azure Arc에 온보딩함으로써 Log Analytics 에이전트를 설치할 수 있게 해주고 위협 방지 및 보안 권장 사항을 제공합니다. 
- Defender for Cloud 서비스는 6시간마다 새 AWS EC2 인스턴스를 검색하고 구성에 따라 온보딩합니다.
- AWS CIS 표준이 Defender for Cloud의 규정 준수 대시보드에 표시됩니다.
- Security Hub 정책을 사용하도록 설정하면 온보딩이 완료되고 5~10분 후에 Defender for Cloud 포털과 규정 준수 대시보드에 권장 사항이 표시됩니다.


::: zone-end

:::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Defender for Cloud의 권장 사항 페이지에 표시되는 AWS 리소스 및 권장 사항" lightbox="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png":::


## <a name="monitoring-your-aws-resources"></a>AWS 리소스 모니터링

이전 스크린샷에서 볼 수 있듯이, Defender for Cloud의 보안 권장 사항 페이지에는 AWS 리소스가 표시됩니다. 환경 필터를 사용하여 Defender for Cloud의 다중 클라우드 기능을 이용할 수 있습니다. Azure, AWS 및 GCP 리소스에 대한 권장 사항을 함께 볼 수 있습니다.

리소스 종류별로 리소스에 대한 모든 활성 권장 사항을 보려면 Defender for Cloud의 자산 인벤토리 페이지를 사용하여 관심 있는 AWS 리소스 종류로 필터링합니다.

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="AWS 옵션을 보여주는 자산 인벤토리 페이지의 리소스 종류 필터"::: 


## <a name="faq---aws-in-defender-for-cloud"></a>FAQ - Defender for Cloud의 AWS

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>EC2 인스턴스를 지원하는 운영 체제는 무엇인가요?

AWS 머신용 Azure Arc에 자동 온보딩을 지원하는 OS

- Ubuntu 16.04 - SSM 에이전트는 기본적으로 미리 설치됩니다.
- Ubuntu 18.04 - SSM 에이전트는 기본적으로 미리 설치됩니다.
- Windows 서버 - SSM 에이전트는 기본적으로 미리 설치됩니다.
- CentOS Linux 7 – SSM을 수동으로 설치하거나 별도로 온보딩해야 합니다.
- SLES(SUSE Linux Enterprise Server) 15(x64) - SSM을 수동으로 설치하거나 별도로 온보딩해야 합니다.
- RHEL(Red Hat Enterprise Linux) 7(x64) - SSM을 수동으로 설치하거나 별도로 온보딩해야 합니다.


## <a name="next-steps"></a>다음 단계

AWS 계정 연결은 클라우드용 Microsoft Defender에서 사용할 수 있는 다중 클라우드 환경의 일부입니다. 관련 정보는 다음 페이지를 참조하세요.

- [클라우드용 Microsoft Defender에 GCP 계정 연결](quickstart-onboard-gcp.md)
