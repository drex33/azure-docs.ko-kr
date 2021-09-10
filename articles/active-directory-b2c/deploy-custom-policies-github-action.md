---
title: GitHub Actions를 사용하여 사용자 지정 정책 배포
titleSuffix: Azure AD B2C
description: GitHub Actions를 사용하여 CI/CD 파이프라인에 Azure AD B2C 사용자 지정 정책을 배포하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 956803f14d9df49e99411eae2cb6a76db7636b03
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187876"
---
# <a name="deploy-custom-policies-with-github-actions"></a>GitHub Actions를 사용하여 사용자 지정 정책 배포

[GitHub Actions](https://docs.github.com/actions/quickstart)는 연속 통합(CI) 및 지속적인 배포(CD) 워크플로를 GitHub 리포지토리에서 직접 만들 수 있도록 합니다. 이 문서에서는GitHub Actions를 사용하여 Azure Active Directory B2C(Azure AD B2C) [사용자 지정 정책](user-flow-overview.md)의 배포를 자동화하는 방법을 설명합니다.

사용자 지정 정책 배포 프로세스를 자동화하려면 [Azure AD B2C 사용자 지정 정책 배포를 위한 GitHub 작업](https://github.com/marketplace/actions/deploy-azure-ad-b2c-custom-policy)을 사용합니다. 이 GitHub 작업은 [Azure AD B2C 커뮤니티](https://github.com/azure-ad-b2c/deploy-trustframework-policy)에서 개발했습니다. 

이 작업을 수행하면 [Microsoft Graph API](/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta&preserve-view=true)를 사용하여 Azure AD B2C 사용자 지정 정책을 Azure AD B2C 테넌트에 배포합니다. 테넌트에 아직 정책이 없는 경우 만들어집니다. 그렇지 않으면 교체됩니다.

> [!IMPORTANT]
> Azure Pipelines를 사용하여 Azure AD B2C 사용자 지정 정책을 관리하기 위해 현재 Microsoft Graph API `/beta` 엔드포인트에서 사용할 수 있는 **미리 보기** 작업을 사용합니다. 프로덕션 애플리케이션에서는 이러한 API의 사용이 지원되지 않습니다. 자세한 내용은 [Microsoft Graph REST API 베타 엔드포인트 참조](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](tutorial-create-user-flows.md)에 있는 단계를 완료합니다.
* GitHub 리포지토리를 아직 만들지 않은 경우 [새롭게 하나를 만듭니다](https://docs.github.com/en/get-started/quickstart/create-a-repo).  

## <a name="select-a-custom-policies-folder"></a>사용자 지정 정책 폴더 선택

GitHub 리포지토리는 모든 Azure AD B2C 정책 파일 및 기타 자산을 포함할 수 있습니다. 리포지토리의 루트 디렉터리에서 사용자 지정 정책을 포함하는 기존 폴더를 만들거나 선택합니다. 

예를 들어 *정책* 이라는 폴더를 선택합니다. Azure AD B2C 사용자 지정 정책 파일을 *정책* 폴더에 추가합니다. 이제 변경 내용을 **커밋** 합니다.

변경 내용을 **푸시** 하지 마세요. 이 작업은 배포 워크플로를 설정한 후에 수행합니다.

## <a name="register-a-microsoft-graph-application"></a>Microsoft Graph 애플리케이션 등록

GitHub 작업이 [Microsoft Graph API](microsoft-graph-operations.md)와 상호 작용할 수 있도록 Azure AD B2C 테넌트에서 애플리케이션 등록을 만듭니다. 아직 등록하지 않은 경우 [Microsoft Graph 애플리케이션을 등록](microsoft-graph-get-started.md)합니다.

GitHub작업이 Microsoft Graph의 데이터에 액세스하도록 하려면, 등록된 애플리케이션에 관련 [애플리케이션 사용 권한](/graph/permissions-reference)을 부여합니다. 앱 등록의 **API 사용 권한** 내에서 **Microsoft Graph** > **정책** > **Policy.ReadWrite.TrustFramework** 권한을 부여합니다.

## <a name="create-a-github-encrypted-secret"></a>GitHub 암호화 비밀 만들기

GitHub 비밀은 조직, 리포지토리 또는 리포지토리 환경에서 만든 암호화된 환경 변수입니다. 이 단계에서는 이전에 [MS Graph 애플리케이션 등록](#register-a-microsoft-graph-application) 단계에서 등록한 애플리케이션에 대한 애플리케이션 비밀을 저장합니다.

Azure AD B2C 사용자 지정 정책을 배포하는 GitHub 작업은 비밀을 사용하여 Microsoft Graph API와 상호 작용에 사용되는 액세스 토큰을 가져옵니다. 자세한 정보는[리포지토리의 암호화된 비밀 만들기](https://docs.github.com/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)를 참조하세요.

GitHub 비밀을 만들려면 다음 단계를 따르세요.

1. GitHub에서 리포지토리의 기본 페이지로 이동합니다.
1. 리포지토리 이름 아래에서 **설정** 을 선택합니다.
1. 왼쪽 세로 막대에서 **비밀** 을 선택합니다.
1. **새 리포지토리 비밀** 을 선택합니다.
1. **이름** 에는 **ClientSecret** 을 입력합니다.
1. **값** 에는 이전에 만든 애플리케이션 비밀을 입력합니다.
1. **비밀 추가** 를 선택합니다.

## <a name="create-a-github-workflow"></a>GitHub 워크플로 만들기

GitHub 워크플로는 사용자의 리포지토리에 추가하는 자동화된 프로세스입니다. 워크플로는 하나 이상의 작업으로 구성되며 이벤트에서 예약하거나 트리거할 수 있습니다. 이 단계에서는 사용자 지정 정책을 배포하는 워크플로를 만듭니다.

워크플로를 만들려면 다음 단계를 수행합니다.

1. GitHub에서 리포지토리의 기본 페이지로 이동합니다.
1. 리포지토리 이름 아래에서 **작업** 을 선택합니다.

   ![GitHub Actions 탭을 보여주는 스크린샷](media/deploy-custom-policies-github-action/github-actions-tab.png)

1. 이전에 워크플로를 구성하지 않은 경우 **직접 워크플로 설정** 을 선택합니다. 그렇지 않은 경우, **새 연결** 을 선택합니다.

   ![새로운 워크플로를 만드는 방법을 보여주는 스크린샷](media/deploy-custom-policies-github-action/set-up-a-workflow.png)

1. GitHub는 `.github/workflows` 폴더에 `main.yml`이라는 워크플로 파일을 만듭니다. 이 파일에는 Azure AD B2C 환경과 배포할 사용자 지정 정책을 포함하여 워크플로에 대한 정보가 포함되어 있습니다. GitHub 웹 편집기에서 다음 YAML 코드를 추가합니다.

    ```yml
    on: push

    env:
      clientId: 00000000-0000-0000-0000-000000000000
      tenant: your-tenant.onmicrosoft.com
    
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v2
    
        - name: 'Upload TrustFrameworkBase Policy'
          uses: azure-ad-b2c/deploy-trustframework-policy@v3
          with:
            folder: "./Policies"
            files: "TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml"
            tenant: ${{ env.tenant }}
            clientId: ${{ env.clientId }}
            clientSecret: ${{ secrets.clientSecret }}
    ```

1.  YAML 파일의 다음 속성을 업데이트합니다.

    | 섹션| 속성 | 값 |
    | ---- | ----- |----- |
    | `env` | `clientId` | [MS Graph 애플리케이션 등록](#register-a-microsoft-graph-application) 단계에서 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** |
    |`env`| `tenant` | 사용자의 Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)(예: contoso.onmicrosoft.com). |
    | `with`| `folder`| 사용자 지정 정책 파일이 저장 되는 폴더(예: `./Policies`)|
    | `with`| `files` | 배포할 정책 파일의 쉼표로 구분 된 목록(예:`TrustFrameworkBase.xml,TrustFrameworkExtensions.xml,SignUpOrSignin.xml`)|
    
    > [!IMPORTANT]
    > 에이전트를 실행하고 정책 파일을 업로드하는 경우 올바른 순서로 업로드되었는지 확인합니다.
    >
    > 1. *TrustFrameworkBase.xml*
    > 1. *TrustFrameworkExtensions.xml*
    > 1. *SignUpOrSignin.xml*
    > 1. *ProfileEdit.xml*
    > 1. *PasswordReset.xml*

1. **커밋 시작** 을 선택합니다.
1. 커밋 메시지 필드 아래에서 현재 분기 또는 새 분기에 커밋을 추가할지 여부를 나타냅니다. **새로운 파일 커밋** 또는 **새 파일 제안** 을 선택하여 끌어오기 요청을 만듭니다.

## <a name="test-your-workflow"></a>워크플로 테스트

만든 워크플로를 테스트하려면 사용자 지정 정책의 변경 내용을 **푸시** 합니다. 작업이 실행되기 시작하면, GitHub에서 실행의 진행률에 대한 시각화 그래프를 보고 각 단계의 작업을 볼 수 있습니다.

1. GitHub에서 리포지토리의 기본 페이지로 이동합니다.
1. 리포지토리 이름 아래에서 **작업** 을 선택합니다.
1. 왼쪽 세로 막대에서 사용자가 만든 워크플로를 선택합니다.
1. **워크플로 실행** 에서 보고자 하는 실행의 이름을 선택합니다.

   ![워크플로 활동을 선택하는 방법을 보여주는 스크린샷](media/deploy-custom-policies-github-action/workflow-report.png)

1. **작업** 또는 시각화 그래프에서 보고자 하는 작업을 선택합니다.
1. 각 단계의 결과를 봅니다. 다음 스크린샷은 **사용자 지정 정책 업로드** 단계 로그를 보여줍니다.
 
   ![업로드 사용자 지정 정책 단계 로그](media/deploy-custom-policies-github-action/job-activity.png)


## <a name="optional-schedule-your-workflow"></a>선택 사항: 워크플로 예약

사용자가 만든 워크플로는 [푸시](https://docs.github.com/actions/reference/events-that-trigger-workflows#push) 이벤트에 의해 트리거됩니다. 원하는 경우 워크플로를 트리거하는 다른 이벤트(예: [끌어오기 요청](https://docs.github.com/actions/reference/events-that-trigger-workflows#pull_request))를 선택할 수 있습니다.

[POSIX cron 구문](https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07)을 사용하여 특정 UTC 시간에 워크플로를 실행하도록 예약할 수도 있습니다. 일정 이벤트를 사용하면 예약된 시간에 워크플로를 트리거할 수 있습니다. 자세한 정보는 [예약된 이벤트](https://docs.github.com/actions/reference/events-that-trigger-workflows#scheduled-events)를 참조하세요.

다음 예제에서는 매일 5:30 및 17:30 UTC에 워크플로를 트리거합니다.

```yml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

워크플로를 편집하려면 다음을 수행 합니다.

1. GitHub에서 리포지토리의 기본 페이지로 이동합니다.
1. 리포지토리 이름 아래에서 **작업** 을 선택합니다.
1. 왼쪽 세로 막대에서 사용자가 만든 워크플로를 선택합니다.
1. **워크플로 실행** 에서 보고자 하는 실행의 이름을 선택합니다.
1. 메뉴에서 세 개의 점 **...** 을 선택한 다음 **워크플로 파일 보기** 를 선택합니다.

   ![워크플로 파일을 보는 방법을 보여주는 스크린샷](media/deploy-custom-policies-github-action/edit-workflow.png)
   
1. GitHub 웹 편집기에서 **편집** 을 선택합니다.
1. 위의 예제에 따라 `on: push`을 변경합니다.
1. 변경 내용을 **커밋** 합니다

## <a name="next-steps"></a>다음 단계

- [워크플로를 트리거하는 이벤트](https://docs.github.com/actions/reference/events-that-trigger-workflows)를 구성하는 방법 알아보기


