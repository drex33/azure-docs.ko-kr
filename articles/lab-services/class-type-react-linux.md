---
title: Azure Lab Services를 사용하여 Linux에서 React를 사용한 랩 설정
description: React 개발 클래스에 랩을 설정하는 방법을 알아봅니다.
author: emaher
ms.topic: article
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 96154718ce8e0ecff0ccdce0ded70272cdda828e
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991769"
---
# <a name="set-up-lab-for-react-on-linux"></a>Linux에서 React에 랩 설정

[React](https://reactjs.org/)는 UI(사용자 인터페이스)를 빌드할 수 있는 JavaScript 라이브러리입니다. React는 웹 사이트에 재사용 가능한 구성 요소를 만드는 선언적 수단입니다.  JavaScript 기반 프런트 엔드 개발을 위한 다른 유명한 라이브러리가 많이 있습니다.  랩을 만드는 동안 이런 라이브러리 중 몇 가지를 사용하게 됩니다.  [Redux](https://redux.js.org/)는 JavaScript 앱에 대한 예측 가능한 상태 컨테이너를 제공하는 라이브러리이며 React를 보완하는 목적으로 자주 사용됩니다. [JSX](https://reactjs.org/docs/introducing-jsx.html)는 UI의 모양을 설명하기 위해 React와 함께 자주 사용되는 JavaScript 라이브러리 구문 확장입니다.  [NodeJS](https://nodejs.org/)는 React 애플리케이션 웹 서버를 실행의 편리한 수단 중 하나입니다.

이 문서에서는 React 웹 개발 클래스에 필요한 개발 환경, 도구 및 라이브러리에 사용할 [Visual Studio Code](https://code.visualstudio.com/)를 설치하는 방법을 설명합니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 방법](./tutorial-setup-lab-account.md)의 자습서를 참조하세요. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

다음 표에서 설명하는 것과 같이 랩 계정 설정을 사용합니다. Azure Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Azure Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| ----------- | ------------ |  
| Marketplace 이미지 | 랩 계정 내에서 사용할 Ubuntu Server 18.04 LTS 이미지를 사용합니다. |

### <a name="lab-settings"></a>랩 설정

권장하는 VM(가상 머신)의 크기는 학생이 수행해야 하는 워크로드 유형에 따라 다릅니다.  

| 랩 설정 | 값 및 설명 |
| ------------ | ------------------ |
| Virtual Machine 크기 | **소형**|

워크로드를 테스트하여 더 큰 크기가 필요한지 확인하는 것을 권장합니다.  각 크기에 대한 자세한 내용은 [VM 크기 조정](administrator-guide.md#vm-sizing)을 참조하세요.

## <a name="template-machine-configuration"></a>템플릿 컴퓨터 구성

이 섹션의 단계에서는 템플릿 VM을 설정하는 방법을 설명합니다.

1. 개발 도구를 설치합니다.
1. 웹 브라우저용 디버거 확장을 설치합니다.
1. 방화벽 설정을 업데이트합니다.

### <a name="install-development-tools"></a>개발 도구 설치

1. 선호하는 웹 브라우저를 설치합니다.  
1. [Node.js](https://nodejs.org)를 설치합니다.

    ```bash
    sudo apt install nodejs
    ```

1. React, Redux 및 JSX를 설치하는 데 사용되는 [Node 패키지 관리자](https://www.npmjs.com/)를 설치합니다.

    ```bash
    sudo apt install npm
    ```

1. [Visual Studio Code](https://code.visualstudio.com/docs/setup/linux)를 설치합니다.
1. [Visual Studio Code용 Reactive Native 도구 확장](https://marketplace.visualstudio.com/items?itemName=msjsdiag.vscode-react-native)을 설치합니다.
1. 필요에 따라 [Redux](https://marketplace.visualstudio.com/search?term=Redux&target=VSCode&category=All%20categories&sortBy=Relevance) 및 [JSX](https://marketplace.visualstudio.com/search?term=JSX&target=VSCode&category=All%20categories&sortBy=Relevance)를 사용해 개발하는 데 쓰이는 확장을 설치합니다.

[React 앱 만들기](https://create-react-app.dev/)는 공식적으로 지원되는 ReactApp 개발 수단이며, npm 5.2 이상을 사용하는 경우 추가 구성이 필요하지 않습니다.  React 앱 만들기 사용 지침은 [시작](https://create-react-app.dev/docs/getting-started) 문서를 참조하세요.

React 기반 웹 사이트에 필요한 기타 구성 요소는 NPM를 사용하여 특정 애플리케이션에 설치됩니다. 예를 들어, 다음 명령을 입력하여 Redux 및 JSX 라이브러리를 설치합니다.

```bash
npm install react-redux
npm install react-jsx
```

### <a name="install-debugger-extensions"></a>디버거 확장 설치

브라우저용 React 개발자 도구 확장을 설치하여 React 구성 요소를 검사하고 성능 정보를 기록합니다.  

- [React 개발자 도구 Edge 추가 기능](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [React 개발자 도구 Chrome 확장](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [React 개발자 도구 FireFox 추가 기능](https://addons.mozilla.org/firefox/addon/react-devtools/)

개발 모드에서 앱을 실행하려면 기본 제공 명령 `npm start`을 사용합니다.  로컬 및 네트워크 url이 명령 출력에 나열됩니다.  HTTP 대신 HTTPS를 사용하려면 [개발 단계에서 https를 사용하여 React 앱 만들기](https://create-react-app.dev/docs/using-https-in-development)를 참조하세요.

### <a name="update-firewall-settings"></a>방화벽 설정 업데이트

공식 Ubuntu 빌드에는 [iptables](https://help.ubuntu.com/community/IptablesHowTo)가 설치되어 있으며 기본적으로 모든 수신 트래픽을 허용합니다.  그러나 더 제한적인 방화벽이 있는 VM을 사용하는 경우 NodeJS 서버에 대한 트래픽을 허용하는 인바운드 규칙을 추가합니다.  아래 예에서는 [iptables](https://help.ubuntu.com/community/IptablesHowTo)를 사용하여 포트 3000에 대한 트래픽을 허용합니다.

```bash
sudo iptables -I INPUT -p tcp -m tcp --dport 3000 -j ACCEPT
```

>[!IMPORTANT]
>강사는 템플릿 VM 또는 다른 랩 VM을 사용하여 학생의 웹 사이트에 액세스해야 합니다.

## <a name="cost"></a>Cost

이 수업의 예상 비용 예시를 살펴보겠습니다.  25명의 학생으로 구성된 클래스가 있다고 가정합니다. 각 학생에게는 20시간 일정의 클래스 시간이 있습니다.  학생에게는 일정에 있는 수업 시간 외에도 과제를 위해 추가적으로 10시간이 할당됩니다.  선택한 가상 머신 크기는 **소형** 으로, 랩 유닛 20개입니다.

- 학생 25명 &times;(일정 시간 20시간 + 할당 시간 10시간) &times; 20개의 랩 유닛 &times; 시간당 USD 0.01 = USD 150.00

> [!IMPORTANT]
> 예상 비용은 예시용으로만 사용됩니다.  최신 가격 책정 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 템플릿 이미지를 랩에 게시할 수 있습니다. 추가 지침은 [템플릿 VM 게시](how-to-create-manage-template.md#publish-the-template-vm)를 참조하세요.

랩을 설정할 때 다음 문서를 참조하세요.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)
