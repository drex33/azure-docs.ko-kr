---
title: Azure Communication Services - 웹 통화 샘플
titleSuffix: An Azure Communication Services sample
description: Communication Services 웹 통화 샘플에 대해 알아봅니다.
author: mariusu-msft
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 394181c73b2282dfa5b4cada49a0ba31cedc5ae9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255739"
---
# <a name="get-started-with-the-web-calling-sample"></a>웹 통화 샘플 시작

웹 통화 샘플은 Communication Services 웹 Calling SDK에서 제공하는 다양한 기능에 대한 단계별 연습으로 사용되는 웹 애플리케이션입니다.

이 샘플은 개발자를 위해 작성되었으며 Communication Services를 매우 쉽게 시작할 수 있습니다. 사용자 인터페이스는 여러 섹션으로 구분되며, 각 섹션에는 코드를 브라우저에서 사용자 고유의 Communication Services 애플리케이션으로 직접 복사할 수 있는 "코드 표시" 단추가 있습니다.

## <a name="get-started-with-the-web-calling-sample"></a>웹 통화 샘플 시작

> [!IMPORTANT]
> [이 샘플은 **Github** 에서 사용할 수 있습니다.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

/Project/readme.md를 따라 프로젝트를 설정하고 머신에서 로컬로 실행합니다.
[웹 통화 샘플](https://github.com/Azure-Samples/communication-services-web-calling-tutorial)이 머신에서 실행되면 다음 방문 페이지가 표시됩니다.

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="웹 통화 자습서 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="웹 통화 자습서 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>사용자 프로비저닝 및 SDK 초기화

백 엔드 토큰 프로비저닝 서비스에서 프로비저닝한 토큰을 사용하여 SDK를 초기화하려면 "사용자 프로비저닝 및 SDK 초기화"를 클릭합니다. 이 백 엔드 서비스는 `/project/webpack.config.js`에 있습니다.

사용자 고유의 솔루션에서 사용할 수 있는 샘플 코드를 확인하려면 "코드 표시" 단추를 클릭합니다.

SDK가 초기화되면 다음과 같이 표시됩니다.

:::image type="content" source="./media/user-provisioning.png" alt-text="사용자 프로비전" lightbox="./media/user-provisioning.png":::

이제 Communication Services 리소스를 사용하여 전화를 걸 준비가 되었습니다.

## <a name="placing-and-receiving-calls"></a>전화 걸기 및 받기

Communication Services 웹 Calling SDK는 **1:1**, **1:N** 및 **그룹** 통화를 허용합니다.

1:1 또는 1:N 발신 전화의 경우 쉼표로 구분된 값을 사용하여 통화할 여러 Communication Services 사용자 ID를 지정할 수 있습니다. 쉼표로 구분된 값을 사용하여 통화할 기존(PSTN) 전화 번호를 지정할 수도 있습니다.

PSTN 전화 번호로 전화를 거는 경우 대체 호출자 ID를 지정합니다. 발신 전화를 걸려면 "전화 걸기" 단추를 클릭합니다.

:::image type="content" source="./media/place-a-call.png" alt-text="전화 걸기" lightbox="./media/place-a-call.png":::

그룹 통화에 참가하려면 해당 통화를 식별하는 GUID를 입력하고 "그룹 참가" 단추를 클릭합니다.

:::image type="content" source="./media/join-a-group-call.png" alt-text="그룹 통화 참가" lightbox="./media/join-a-group-call.png":::

전화 걸기, 전화 받기 및 그룹 통화 참가에 대한 샘플 코드를 확인하려면 "코드 표시" 단추를 클릭합니다.

활성 통화는 다음과 같습니다.

:::image type="content" source="./media/group-call.png" alt-text="그룹 호출" lightbox="./media/group-call.png":::

이 샘플에서는 다음 기능에 대한 코드 조각도 제공합니다.

  - 비디오 아이콘을 클릭하여 비디오 카메라 켜기/끄기
  - 마이크 아이콘을 클릭하여 마이크 켜기/끄기
  - 재생 아이콘을 클릭하여 통화 보류/보류 해제
  - 화면 아이콘을 클릭하여 화면 공유 시작/중지
  - 사람 아이콘을 클릭하여 통화에 참가자 추가
  - 참가자 명단에서 "참가자 제거"를 클릭하여 통화에서 특정 참가자 제거


## <a name="next-steps"></a>다음 단계

>[!div class="nextstepaction"]
>[GitHub에서 샘플 다운로드](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

자세한 내용은 다음 문서를 참조하세요.

- [통화 SDK 사용법](../quickstarts/voice-video-calling/getting-started-with-calling.md) 숙지
- [통화 작동 방식](../concepts/voice-video-calling/about-call-types.md)에 대한 자세한 정보
- [API 참조 문서](/javascript/api/azure-communication-services/@azure/communication-calling/) 검토
- [Contoso Med 앱](https://github.com/Azure-Samples/communication-services-contoso-med-app) 샘플 검토

## <a name="additional-reading"></a>추가 자료

- [샘플](./overview.md) - 샘플 개요 페이지에서 추가 샘플과 예제를 찾아보세요.
- [Redux](https://redux.js.org/) - 클라이언트 쪽 상태 관리
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft 기반 UI 라이브러리
- [React](https://reactjs.org/) - 사용자 인터페이스 빌드용 라이브러리
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - 웹 애플리케이션 빌드용 프레임워크