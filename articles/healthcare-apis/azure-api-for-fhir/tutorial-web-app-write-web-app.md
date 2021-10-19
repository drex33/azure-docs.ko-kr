---
title: 웹앱 자습서 - 웹 애플리케이션 작성 - Azure API for FHIR
description: 이 자습서에서는 간단한 웹 애플리케이션을 배포하는 예제를 안내합니다. 자습서의 이 섹션에서는 웹 애플리케이션을 작성하는 과정을 안내합니다.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 8cf58e208b61af65886c57b0b94cf3b488209821
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787033"
---
# <a name="write-azure-web-application-to-read-fhir-data-in-azure-api-for-fhir"></a>Azure API for FHIR FHIR 데이터를 읽는 Azure 웹 애플리케이션 작성
이제 FHIR 서버 및 POST 데이터에 연결할 수 있으므로 FHIR 데이터를 읽을 웹 애플리케이션을 작성할 준비가 되었습니다. 자습서의 마지막 단계에서는 웹 애플리케이션을 작성하고 액세스하는 과정을 안내합니다.

## <a name="create-web-application"></a>웹 애플리케이션 만들기
Azure에서 **리소스 만들기** 를 선택하고 **웹앱** 을 선택합니다. 클라이언트 애플리케이션에 대한 리디렉션 URI에 지정된 대로 웹 애플리케이션의 이름을 지정하거나 이전 단계로 이동하여 리디렉션 URI를 새 이름으로 업데이트해야 합니다. 

![웹 애플리케이션 만들기](media/tutorial-web-app/create-web-app.png)

웹 애플리케이션을 사용할 수 있게 되면 **리소스로 이동** 합니다. 오른쪽의 개발 도구 아래에서 **App Service 편집기(미리 보기)** 를 선택한 다음, **이동** 을 선택합니다. 이동을 선택하면 App Service 편집기가 열립니다. *탐색* 아래 회색 공간을 마우스 오른쪽 단추로 클릭하고 **index.html** 이라는 새 파일을 만듭니다.

다음은 **index.html** 에 입력할 수 있는 코드입니다. 다음 항목을 업데이트해야 합니다.
* **clientId** - 클라이언트 애플리케이션 ID로 업데이트합니다. 이 ID는 토큰을 검색할 때 끌어온 ID와 동일합니다.
* **authority** - Azure AD 테넌트 ID로 업데이트
* **FHIRendpoint** - FHIR 서비스 이름을 갖도록 FHIRendpoint 업데이트
* **scopes** - 대상의 전체 URL을 반영하도록 업데이트

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

여기에서 웹 애플리케이션 리소스로 돌아가서 개요 페이지에 있는 URL을 열 수 있습니다. 로그인하여 이전에 만든 환자 James Tiberious Kirk를 확인합니다.

## <a name="next-steps"></a>다음 단계
Azure API for FHIR을 성공적으로 배포하고, 퍼블릭 클라이언트 애플리케이션을 등록하고, 액세스를 테스트하고, 작은 웹 애플리케이션을 만들었습니다. 다음 단계로 Azure API for FHIR에서 지원되는 기능을 확인하세요.

>[!div class="nextstepaction"]
>[지원되는 기능](fhir-features-supported.md)





