---
title: 빠른 시작-리소스 만들기 및 관리 `room`
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Services 리소스 내에서 방을 만드는 방법을 알아봅니다.
author: radubulboaca
manager: mariusu
services: azure-communication-services
ms.author: radubulboaca
ms.date: 11/19/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 90fa2ecb652eec00c3b6c205366eea05d4fdc878
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487820"
---
# <a name="quickstart-create-and-manage-a-room-resource"></a>빠른 시작: 대화방 리소스 만들기 및 관리

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

이 빠른 시작은 Azure Communication Services 대화방을 시작 하는 데 도움이 됩니다. 는 `room` 미리 결정 된 기간 동안 공동으로 작업할 수 있도록 알려진 고정 참가자 집합을 위한 서버 관리 통신 공간입니다. 

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `RoomsQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o RoomsQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd RoomsQuickstart
dotnet build
```

### <a name="initialize-a-room-client"></a>대화방 클라이언트 초기화

`RoomsClient`새로 만들고 `rooms` 속성 및 수명 주기를 관리 하는 데 사용 되는 새 개체를 만듭니다. `Communications Service`요청을 인증 하는 데 사용 되는의 연결 문자열입니다. 연결 문자열에 대 한 자세한 내용은 [이 페이지](../create-communication-resource.md#access-your-connection-strings-and-service-endpoints)를 참조 하세요.

```csharp
// Find your Communication Services resource in the Azure portal
var connectionString = "<connection_string>"; 
RoomsClient client = new RoomsClient(connectionString);
```

### <a name="create-a-room"></a>방 만들기

`room`아래 코드 조각을 사용 하 여 기본 속성을 사용 하 여 새를 만듭니다.

```csharp
CreateRoomRequest createRoomRequest = new CreateRoomRequest();
Response<RoomResult> createRoomResponse = await roomsClient.CreateRoomAsync(createRoomRequest);
RoomResult createRoomResult = createRoomResponse.Value;
string roomId = createRoomResult.Id;
```

`rooms`는 서버 쪽 엔터티입니다 .를 추적 하 고 `roomId` 선택한 저장소 미디어에를 유지 하려고 할 수 있습니다. 를 참조 하 여 `roomId` 개체의 속성을 확인 하거나 업데이트할 수 있습니다 `room` . 

### <a name="get-properties-of-an-existing-room"></a>기존 대화방의 속성을 가져옵니다.

를 참조 하 여 기존의 세부 정보를 검색 합니다 `room` `roomId` .

```csharp
Response<RoomResult> getRoomResponse = await roomsClient.GetRoomAsync(
    createdRoomId: "<roomId>")
RoomResult getRoomResult = getRoomResponse.Value;
```

### <a name="update-the-lifetime-of-a-room"></a>대화방의 수명 업데이트

의 수명은 `room` `ValidFrom` 및 매개 변수에 대 한 업데이트 요청을 실행 하 여 수정할 수 있습니다 `ValidUntil` .

```csharp
UpdateRoomRequest updateRoomRequest = new UpdateRoomRequest()
{
    ValidFrom = new DateTimeOffset(2021, 12, 1, 8, 6, 32,
                                 new TimeSpan(1, 0, 0)),
    ValidUntil = new DateTimeOffset(2021, 12, 2, 8, 6, 32,
                                 new TimeSpan(1, 0, 0))
    } 
 };
Response<RoomResult> updateRoomResponse = await roomsClient.UpdateRoomAsync(createdRoomId, updateRoomRequest);
RoomResult updateRoomResult = updateRoomResponse.Value;
``` 

### <a name="add-new-participants"></a>새 참가자 추가 

에 새 참가자를 추가 하려면 `room` 방에 대해 업데이트 요청을 실행 합니다 `Participants` .

```csharp
UpdateRoomRequest updateRoomRequest = new UpdateRoomRequest()
{
    Participants = {
        { "<ParticipantId1>", new {} }
        { "<ParticipantId2>", new {} }
        { "<ParticipantId3>", new {} }
        { "<ParticipantId4>", new {} }
    } 
 };
Response<RoomResult> updateRoomResponse = await roomsClient.UpdateRoomAsync(createdRoomId, updateRoomRequest);
RoomResult updateRoomResult = updateRoomResponse.Value;
```

에 추가 된 참가자는 `room` 호출을 조인할 수 있습니다.

### <a name="remove-participants"></a>참가자 제거

에서 참가자를 제거 하 `room` 고 해당 액세스를 취소 하려면 목록을 업데이트 합니다 `Participants` .

```csharp
UpdateRoomRequest updateRoomRequest = new UpdateRoomRequest()
{
    Participants = {
        { "<ParticipantId", null }
    } 
 };
Response<RoomResult> updateRoomResponse = await roomsClient.UpdateRoomAsync(createdRoomId, updateRoomRequest);
RoomResult updateRoomResult = updateRoomResponse.Value;
```

### <a name="join-a-room-call"></a>대화방 통화 조인

대화방 통화를 조인 하려면 [클라이언트 앱에 음성 통화 추가](../voice-video-calling/getting-started-with-calling.md) 가이드를 사용 하 여 웹 응용 프로그램을 설정 합니다. 초기화 되 고 인증 된 후에는 `callAgent` 속성을 식별자로 사용 하 여 컨텍스트 개체를 지정할 수 있습니다 `roomId` `room` . 호출에 조인 하려면 메서드를 사용 하 `join` 고 컨텍스트 인스턴스를 전달 합니다.

```js

const context = { roomId: '<RoomId>' }

const call = callAgent.join(context);

```

### <a name="delete-room"></a>대화방 삭제
기존 `room` 를 제거 하려는 경우 명시적인 삭제 요청을 실행할 수 있습니다. 모든 `rooms` 및 관련 리소스는 유효 기간이 끝날 때와 유예 기간이 지나면 자동으로 삭제 됩니다. 

```csharp
Response deleteRoomResponse = await roomsClient.DeleteRoomAsync(createdRoomId)
```

## <a name="object-model"></a>개체 모델

다음 표에서는 개체의 기본 속성을 보여 줍니다 `room` . 

| Name                  | 설명                               |
|-----------------------|-------------------------------------------|
| `roomId`              | 고유 `room` 식별자입니다.                  |
| `ValidFrom`           | 가장 이른 시간을 `room` 사용할 수 있습니다. | 
| `ValidUntil`          | 최신 시간을 `room` 사용할 수 있습니다. |
| `Participants`        | 기존 참가자 Id의 목록입니다.       | 

## <a name="next-steps"></a>다음 단계

이 섹션에서는 다음 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> - 새 대화방 만들기
> - 대화방의 속성을 가져옵니다.
> - 대화방의 속성 업데이트
> - 대화방 통화 조인
> - 대화방 삭제

다음을 수행할 수도 있습니다.
 - [음성 및 비디오 호출 개념](../../concepts/voice-video-calling/about-call-types.md) 에 대 한 자세한 정보
 - Azure Communication Services [샘플](../../samples/overview.md) 검토