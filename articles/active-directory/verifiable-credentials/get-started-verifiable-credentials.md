---
title: 자습서 - 샘플 앱을 사용하여 Azure Active Directory 확인 가능한 자격 증명 시작(미리 보기)
description: 이 자습서에서는 샘플 앱 및 테스트 테넌트를 사용하여 확인 가능한 자격 증명을 발급하는 방법에 대해 알아봅니다.
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ROBOTS: NOINDEX
ms.openlocfilehash: 9958d12299287c7d53641ba75d917f1f18359f54
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728440"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>자습서 - 샘플 앱을 사용하여 Azure Active Directory 확인 가능한 자격 증명 시작(미리 보기)

이 자습서에서는 첫 번째 확인 가능한 자격 증명인 확인된 자격 증명 전문가 카드를 발급하는 데 필요한 단계를 살펴봅니다. 그런 다음, 이 카드를 사용하여 디지털 자격 증명 기술을 마스터한 확인된 자격 증명 전문가임을 검증 도구에 증명할 수 있습니다. 확인 가능한 자격 증명 샘플 앱을 사용하여 Azure Active Directory 확인 가능한 자격 증명을 시작하고 첫 번째 확인 가능한 자격 증명을 발급합니다.

![예제 카드의 이미지입니다.](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [NodeJS](https://nodejs.org/en/download/) 버전 10.14 이상이 테스트 시스템에 설치되어 있습니다.
- 샘플 앱을 호스트하는 리포지토리를 복제하려면 [GIT](https://git-scm.com/downloads)가 설치되어 있어야 합니다.
- [Visual Studio Code](https://code.visualstudio.com/Download)
- 샘플 사이트를 호스트할 시스템
- Microsoft Authenticator 버전 6.2005.3599 이상이 설치된 모바일 디바이스
- [NGROK](https://ngrok.com/) 시험판

## <a name="download-the-sample-code"></a>샘플 코드 다운로드

확인된 자격 증명 전문가 카드를 직접 발급하려면 로컬 머신에서 웹 사이트를 실행해야 합니다. 웹 사이트는 확인 가능한 자격 증명 발급 프로세스를 시작하는 데 사용됩니다. 이 자습서 전체에 사용되는 간단한 웹 사이트(NodeJS로 작성)는 제공됩니다.

먼저 [여기](https://github.com/Azure-Samples/active-directory-verifiable-credentials) GitHub에서 샘플 코드를 다운로드하거나 리포지토리를 로컬 머신에 복제합니다.

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

샘플 웹 사이트의 코드를 숙지하는 것이 좋습니다. `issuer` 폴더에는 확인 가능한 자격 증명을 발급하는 데 사용되는 모든 코드가 포함되어 있습니다. 자세한 내용은 샘플의 [추가 정보](https://github.com/Azure-Samples/active-directory-verifiable-credentials)에서 확인할 수 있습니다.

## <a name="run-the-issuer-website"></a>발급자 웹 사이트 실행

Visual Studio Code 내에서 또는 운영 체제에서 사용할 수 있는 명령줄에서 단계를 실행할 수 있습니다. 

1. `issuer` 폴더로 이동합니다. 

    ```terminal
    cd issuer
    ```

2. 필요한 모든 패키지를 설치하고 사이트를 시작해야 합니다.

   ```terminal
    npm install
    node app.js
    ```

3. 터미널에서 발급자 앱이 포트 8081에서 수신 대기하는 것을 볼 수 있습니다. 이제 Ngrok를 사용하여 역방향 프록시를 설정해 보겠습니다. 그래야 Authenticator가 앱과 통신할 수 있습니다. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Ngrok를 사용하여 역방향 프록시 만들기

샘플 웹 사이트를 실행하는 경우 디바이스는 로컬 머신에서 실행되는 노드 서버와 통신해야 합니다. [ngrok](https://ngrok.com/)를 사용하여 간편하게 인터넷을 통해 로컬 개발 서버를 사용할 수 있도록 하는 것이 좋습니다.

1.  **ngrok** 를 다운로드하고 추출한 후 다음을 실행해야 합니다.

    ```terminal
     ngrok http 8081
    ```

기본적으로 샘플 웹 사이트는 포트 `8081`에서 실행됩니다. **ngrok** 는 서버에 대한 두 개의 전달 URL을 출력합니다. `https://` 접두사가 있는 URL을 복사합니다.

![ngrok는 인터넷을 통해 애플리케이션 엔드포인트를 사용하도록 만드는 데 유용합니다.](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> PowerShell을 사용하는 경우 명령을 인식하기 위해 `./ngrok`를 입력해야 할 수 있습니다.

로컬 포트가 ngrok를 사용하여 인터넷에 노출되면 샘플 사이트는 ngrok를 통해 생성된 호스트 이름을 자동으로 사용합니다. 브라우저를 열고 ngrok https 전달 URL로 이동합니다. 샘플 사이트의 홈페이지에 성공적으로 방문할 수 있어야 합니다. 페이지가 열리면 디바이스가 로컬 서버에서 실행 중인 샘플 앱과 통신할 수 있습니다. 이제 확인된 자격 증명 전문가 카드를 직접 발급할 준비가 되었습니다.

## <a name="issue-a-credential"></a>자격 증명 발급

1. 모바일 디바이스에 Authenticator를 설치합니다. Microsoft Authenticator는 확인 가능한 자격 증명을 수신, 저장하고 이해 관계자에게 제시하는 데 사용됩니다.

2. 다음으로, 확인 가능한 자격 증명을 발급합니다. **자격 증명 가져오기** 단추를 **클릭** 합니다. **자격 증명 가져오기** 단추를 클릭하면 샘플 웹 사이트에 Authenticator를 사용하여 스캔할 수 있는 QR 코드가 표시됩니다. 모바일 디바이스의 브라우저에서 사이트를 보는 경우 **자격 증명 가져오기** 단추를 클릭하면 인증자 앱을 여는 딥 링크가 트리거되며 QR 코드를 스캔할 필요가 없습니다.

   ![자격 증명 가져오기 단추](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Authenticator를 사용하여 웹 사이트의 QR 코드를 검색하거나, 모바일을 통해 웹 사이트에 액세스하는 경우 자격 증명 가져오기 단추를 클릭하여 딥 링크를 트리거합니다. 

   ![QR 코드 ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. 이 때는 **추가** 단추가 회색으로 표시됩니다. 카드 이미지 아래에서 **귀하 계정에 로그인** 을 선택합니다.

   ![로그인 ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. 자격 증명 전문가 카드를 받기 전에 사용 중인 테넌트가 인증 정보를 제공하도록 요구합니다. 자습서를 처음 진행하여 자격 증명 전문가 계정이 없으면, B2C 테넌트에서 새 사용자 계정을 만듭니다.

   ![인증 후 진행](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. 로그인한 후에는 **추가** 단추가 더 이상 회색으로 표시되지 않습니다. **추가** 를 선택하여 새 VC를 수락합니다.

   ![인증 후 추가 선택](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. 축하합니다! 이제 확인된 자격 증명 전문가 VC가 있습니다.

   ![자격 증명 전문가 VC가 추가됨](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
다음에는 자격 증명을 확인할 차례입니다.

## <a name="validate-credentials"></a>자격 증명 유효성 검사

자습서의 발급 부분을 완료했고 Authenticator에 확인 가능한 자격 증명이 있으면, 자체 검증 도구 앱에서 유효성을 검사할 차례입니다.

1.  발급자 ngrok 서비스 실행을 중지합니다.

    ```terminal
     control+c
    ```


2. 다른 터미널 창에서 검증 도구 앱 폴더를 열고 발급자 앱을 실행한 방법과 유사하게 실행합니다.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. 이제 ngrok를 검증 도구 포트 8082를 사용하여 실행합니다.

    ```terminal
    ngrok http 8082
    ```

4. 브라우저에서 ngrok https 전달 URL을 열고 **자격 증명 확인** 단추를 탭합니다.  

   ![자격 증명 전문가 확인 단추](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Authenticator를 열고 QR 코드를 스캔합니다.

   ![qr 코드를 스캔하여 자격 증명 확인](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > iOS에서는 오른쪽 위에 있고 Android에서는 오른쪽 아래에 있습니다. QR 코드를 스캔합니다.

6. Authenticator의 새 권한 요청 화면에서 **허용** 을 선택합니다. 허용을 누르면 DID(탈중앙화 ID)로 확인 가능한 프레젠테이션에 서명하여 실제로 이 확인 가능한 자격 증명을 제어하고 있음을 증명하게 됩니다.

   ![새 권한 요청](media/get-started-verifiable-credentials/new-permission-request.png)

    성공적인 프레젠테이션 후에는 다음 세 가지를 업데이트해야 합니다.

   1. 이제 웹 페이지에 "축하합니다. 귀하의 이름" + 확인된 자격 증명 전문가입니다!"가 표시됩니다.
   
    ![축하, 다시 확인](media/get-started-verifiable-credentials/congratulations.png)


   2. 검증 도구 앱 터미널에도 로그의 동일한 메시지가 표시되어야 합니다.
   
    ![명령 프롬프트의 애플리케이션 작업](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. Authenticator에는 이 프레젠테이션의 최근 작업에 대한 항목이 있어야 합니다.

    ![Authenticator의 작업](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> 검증 도구 앱을 실행하는 동안 ngrok가 작동을 중지하고 연결이 너무 많다는 오류가 표시될 수 있습니다. ngrok에 계정을 등록하면 이러한 문제를 방지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

빠른 시작 가이드를 성공적으로 완료했으면 Azure AD 확인 가능한 자격 증명 서비스에서 자신만의 탈중앙화 식별자를 만들고 자신의 확인 가능한 자격 증명을 발급할 차례입니다.

>[!div class="nextstepaction"] 
>[확인 가능한 자격 증명 샘플 앱을 사용하여 자체 발급자 구성](./enable-your-tenant-verifiable-credentials.md)
