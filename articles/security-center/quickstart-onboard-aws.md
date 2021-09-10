---
title: Azure Security Center에 AWS 계정 연결
description: Azure Security Center에서 AWS 리소스 모니터링
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ac057d169e3bab1b154eeb7fc9148310873ed549
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731132"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Azure Security Center에 AWS 계정 연결

클라우드 워크로드가 일반적으로 여러 클라우드 플랫폼에 걸쳐 있는 경우 클라우드 보안 서비스도 동일한 작업을 수행해야 합니다.

Azure Security Center는 Azure, AWS(Amazon Web Services) 및 GCP(Google Cloud Platform)의 워크로드를 보호합니다.

AWS 계정을 Security Center에 온보딩하면 AWS Security Hub와 Azure Security Center가 통합됩니다. 따라서 Security Center는 이러한 클라우드 환경 모두에서 가시성과 보호를 지원하여 다음을 제공합니다.

- 자동 에이전트 프로비저닝(Security Center에서는 [Azure Arc](../azure-arc/servers/overview.md)를 사용하여 AWS 인스턴스에 Log Analytics 에이전트 배포)
- 정책 관리
- 취약점 관리
- 포함된 EDR(엔드포인트 검색 및 응답)
- 잘못된 보안 구성 검색
- Security Center 권장 사항 및 AWS Security Hub 검사 결과를 보여주는 단일 보기
- Security Center의 보안 점수 계산에 AWS 리소스 통합
- AWS 리소스의 규정 준수 평가

아래 스크린샷에서는 Security Center의 개요 대시보드에 표시되는 AWS 계정을 볼 수 있습니다.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Security Center의 개요 대시보드에 나열된 3개의 GCP 프로젝트" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|[서버용 Azure Defender](defender-for-servers-introduction.md) 필요|
|필요한 역할 및 권한:|관련 Azure 구독의 **소유자**<br>소유자가 서비스 주체 세부 정보를 제공하는 경우에는 **기여자** 를 AWS 계정에 연결할 수도 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/no-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||



## <a name="connect-your-aws-account"></a>AWS 계정 연결

아래 단계에 따라 AWS 클라우드 커넥터를 만듭니다. 

### <a name="step-1-set-up-aws-security-hub"></a>1단계: AWS Security Hub 설정:

1. 여러 영역에 대한 보안 권장 사항을 보려면 관련된 각 지역에 대해 다음 단계를 반복합니다.

    > [!IMPORTANT]
    > AWS 마스터 계정을 사용하는 경우 다음 세 단계를 반복하여 모든 관련 지역에서 마스터 계정 및 연결된 모든 구성원 계정을 구성합니다.

    1. [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)를 사용하도록 설정합니다.
    1. [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html)를 사용하도록 설정합니다.
    1. Security Hub로 흐르는 데이터가 있는지 확인합니다.

        처음으로 Security Hub를 사용하도록 설정하는 경우 데이터를 사용할 수 있기까지 몇 시간이 걸릴 수 있습니다.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>2단계. AWS에서 Security Center 인증 설정

Security Center에서 AWS에 인증하도록 허용하는 두 가지 방법이 있습니다.

- **Security Center에 대한 IAM 역할 만들기** - 가장 안전한 방법이며 이 방법을 권장합니다.
- **Security Center에 대한 AWS 사용자** - IAM을 사용하도록 설정하지 않을 경우 덜 안전한 옵션입니다.

#### <a name="create-an-iam-role-for-security-center"></a>Security Center에 대한 IAM 역할 만들기
1. Amazon Web Services 콘솔의 **보안, ID 및 규정 준수** 에서 **IAM** 을 선택합니다.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS 서비스.":::

1. **역할** 을 선택하고 **역할 만들기** 를 선택합니다.
1. 다음 페이지에서 **다른 AWS 계정** 을 선택합니다.
1. 다음 세부 정보를 입력합니다.

    - **계정 ID** - Security Center의 AWS 커넥터 페이지에 표시된 것처럼 Microsoft 계정 ID(**158177204117**)를 입력합니다.
    - **필요한 외부 ID** - 선택해야 합니다.
    - **외부 ID** - Security Center의 AWS 커넥터 페이지에 표시된 것처럼 구독 ID를 입력합니다. 

1. **다음** 을 선택합니다.
1. **권한 정책 연결** 섹션에서 다음 정책을 선택합니다.

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 필요에 따라 태그를 추가합니다. 사용자에게 태그를 추가해도 연결에 영향을 주지 않습니다.
1. **다음** 을 선택합니다.

1. 역할 목록에서, 이전에 생성된 역할을 선택합니다.

1. 나중에 사용할 수 있도록 ARN(Amazon Resource Name)을 저장합니다. 

#### <a name="create-an-aws-user-for-security-center"></a>Security Center에 대한 AWS 사용자 만들기 
1. **사용자** 탭을 열고 **사용자 추가** 를 선택합니다.
1. **세부 정보** 단계에서 Security Center의 사용자 이름을 입력하고 AWS 액세스 형식으로 **프로그래밍 방식 액세스** 를 선택합니다. 
1. **Next Permissions**(다음: 권한)를 클릭합니다.
1. **기존 정책 직접 연결** 을 선택하고 다음 정책을 적용합니다.
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. 완료되면 **다음: 태그** 를 선택합니다. 필요에 따라 태그를 추가합니다. 사용자에게 태그를 추가해도 연결에 영향을 주지 않습니다.
1. **검토** 를 선택합니다.
1. 나중에 사용할 수 있도록 자동으로 생성된 **액세스 키 ID** 및 **비밀 액세스 키** CSV 파일을 저장합니다.
1. 요약 정보를 검토하고 **사용자 만들기** 를 클릭합니다.


### <a name="step-3-configure-the-ssm-agent"></a>3단계: SSM 에이전트 구성

AWS Systems Manager는 AWS 리소스의 작업을 자동화하는 데 필요합니다. EC2 인스턴스에 SSM 에이전트가 없는 경우 다음과 같은 Amazon의 관련 지침을 따르세요.

- [Windows 인스턴스에 SSM Agent 설치 및 구성](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Amazon EC2 Linux 인스턴스에 SSM Agent 설치 및 구성](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>4단계. Azure Arc 사전 요구 사항 완료
1. 적절한 [Azure 리소스 공급자](../azure-arc/servers/agent-overview.md#register-azure-resource-providers)가 등록되었는지 확인합니다.
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. 대규모 온보딩을 위한 서비스 주체를 만듭니다. 온보딩에 사용하려는 구독의 **소유자** 로서, [대규모 온보딩을 위한 서비스 주체 만들기](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)에 설명된 대로 Azure Arc 온보딩의 서비스 주체를 만듭니다.


### <a name="step-5-connect-aws-to-security-center"></a>5단계. Security Center에 AWS 연결

1. Security Center 메뉴에서 **다중 클라우드 커넥터** 를 선택합니다.
1. **AWS 계정 추가** 를 선택합니다.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center의 다중 클라우드 커넥터 페이지에 표시되는 [AWS 계정 추가] 단추":::
1. 다음과 같이 **AWS 인증** 탭에서 옵션을 구성합니다.
    1. 커넥터의 **표시 이름** 을 입력합니다.
    1. 구독이 올바른지 확인합니다. 커넥터 및 AWS Security Hub 권장 사항을 포함할 구독입니다.
    1. 인증 옵션에 따라 [2단계. AWS에서 Security Center 인증 설정](#step-2-set-up-authentication-for-security-center-in-aws)에서 선택했습니다.
        - **역할 맡기** 를 선택하고 [Security Center의 IAM 역할 만들기](#create-an-iam-role-for-security-center)에서 ARN을 붙여넣습니다.
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Azure Portal에서 AWS 연결 마법사의 관련 필드에 ARN 파일 붙여넣기.":::

            또는

        - **자격 증명** 을 선택하고, [Security Center에 대한 AWS 사용자 만들기](#create-an-aws-user-for-security-center)에서 저장한 .csv 파일의 **액세스 키** 및 **비밀 키** 를 붙여넣습니다.
1. **다음** 을 선택합니다.
1. **Azure Arc 구성** 탭에서 옵션을 구성합니다.

    Security Center는 연결된 AWS 계정에서 EC2 인스턴스를 검색하고 SSM을 사용하여 Azure Arc에 온보딩합니다. 

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

- Security Center는 환경에서 AWS EC2 인스턴스를 검색하여 Azure Arc에 온보딩함으로써 Log Analytics 에이전트를 설치할 수 있게 해주고 위협 방지 및 보안 권장 사항을 제공합니다. 
- ASC 서비스는 6시간마다 새 AWS EC2 인스턴스를 검색하고 구성에 따라 온보딩합니다.
- AWS CIS 표준이 Security Center의 규정 준수 대시보드에 표시됩니다.
- Security Hub 정책을 사용하도록 설정하면 온보딩이 완료되고 5~10분 후에 Security Center 포털과 규정 준수 대시보드에 권장 사항이 표시됩니다.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Security Center의 권장 사항 페이지에 표시되는 AWS 리소스 및 권장 사항":::



## <a name="monitoring-your-aws-resources"></a>AWS 리소스 모니터링

위와 같이 Azure Security Center의 보안 권장 사항 페이지에는 Azure 및 GCP 리소스와 AWS 리소스가 함께 표시되어 진정한 다중 클라우드 보기를 구현합니다.

리소스 종류별로 리소스에 대한 모든 활성 권장 사항을 보려면 Security Center의 자산 인벤토리 페이지를 사용하여 관심 있는 AWS 리소스 종류로 필터링합니다.

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="AWS 옵션을 보여주는 자산 인벤토리 페이지의 리소스 종류 필터"::: 


## <a name="faq---aws-in-security-center"></a>FAQ - Security Center의 AWS

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>EC2 인스턴스를 지원하는 운영 체제는 무엇인가요?

AWS 머신용 Azure Arc에 자동 온보딩을 지원하는 OS

- Ubuntu 16.04 - SSM 에이전트는 기본적으로 미리 설치됩니다.
- Ubuntu 18.04 - SSM 에이전트는 기본적으로 미리 설치됩니다.
- Windows 서버 - SSM 에이전트는 기본적으로 미리 설치됩니다.
- CentOS Linux 7 – SSM을 수동으로 설치하거나 별도로 온보딩해야 합니다.
- SLES(SUSE Linux Enterprise Server) 15(x64) - SSM을 수동으로 설치하거나 별도로 온보딩해야 합니다.
- RHEL(Red Hat Enterprise Linux) 7(x64) - SSM을 수동으로 설치하거나 별도로 온보딩해야 합니다.


## <a name="next-steps"></a>다음 단계

AWS 계정 연결은 Azure Security Center에서 사용할 수 있는 다중 클라우드 환경의 일부입니다. 관련 정보는 다음 페이지를 참조하세요.

- [Azure Security Center에 GCP 계정 연결](quickstart-onboard-gcp.md)
