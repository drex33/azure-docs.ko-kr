---
title: Visual Studio Code를 사용하여 Azure Stream Analytics 클라우드 작업에 대한 사용자 지정 .NET 역직렬 변환기 만들기
description: 이 자습서에서는 Visual Studio Code를 사용하여 Azure Stream Analytics 클라우드 작업에 대한 사용자 지정 .NET 역직렬 변환기를 만드는 방법을 보여 줍니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 6d5787268cd5f7d25b54f8c7bc63f59c20a8495b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128606991"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Visual Studio Code에서 Azure Stream Analytics에 대한 사용자 지정 .NET 역직렬 변환기 만들기

Azure Stream Analytics에는 JSON, CSV 및 Avro라는 [세 가지 데이터 형식에 대한 기본 제공 지원](stream-analytics-parsing-json.md)이 있습니다. 사용자 지정 .NET 역직렬 변환기를 사용하면 클라우드 작업에 대한 다른 형식(예: [프로토콜 버퍼](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) 및 기타 사용자 정의 형식)의 데이터를 읽을 수 있습니다.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Visual Studio Code의 사용자 지정 .NET 역직렬 변환기

Visual Studio Code를 사용하여 Azure Stream Analytics 클라우드 작업에 대한 사용자 지정 .NET 역직렬 변환기를 만들고, 테스트하고, 디버그할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

* [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치하고, Visual Studio Code를 다시 시작합니다.

* 이 [빠른 시작](quick-create-visual-studio-code.md)에서 Visual Studio Code를 사용하여 Stream Analytics 작업을 만드는 방법을 알아봅니다.

### <a name="create-a-custom-deserializer"></a>사용자 지정 역직렬 변환기 만들기

1. 터미널을 열고, 다음 명령을 실행하여 Visual Studio Code에서 **ProtobufDeserializer** 라는 사용자 지정 역직렬 변환기에 대한 .NET 클래스 라이브러리를 만듭니다.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. **ProtobufDeserializer** 프로젝트 디렉터리로 이동하고, [Microsoft.Azure.StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) 및 [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet 패키지를 설치합니다.

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. [MessageBodyProto 클래스](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) 및 [MessageBodyDeserializer 클래스](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs)를 프로젝트에 추가합니다.

4. **ProtobufDeserializer** 프로젝트를 빌드합니다.

### <a name="add-an-azure-stream-analytics-project"></a>Azure Stream Analytics 프로젝트 추가

1. Visual Studio Code를 열고, **Ctrl+Shift+P** 를 선택하여 명령 팔레트를 엽니다. 그런 다음, ASA를 입력하고 **ASA: 새 프로젝트 만들기** 를 선택합니다. 이름을 **ProtobufCloudDeserializer** 로 지정합니다.

### <a name="configure-a-stream-analytics-job"></a>Stream Analytics 작업 구성

1. **JobConfig.json** 을 두 번 클릭합니다. 다음 설정 외에는 기본 구성을 사용합니다.

   |설정|제안 값|
   |-------|---------------|
   |글로벌 스토리지 설정 리소스|현재 계정에서 데이터 원본 선택|
   |글로벌 스토리지 설정 구독| < 사용자 구독 >|
   |글로벌 스토리지 설정 스토리지 계정| < 사용자 스토리지 계정 >|
   |CustomCodeStorage 설정 스토리지 계정|< 사용자 스토리지 계정 >|
   |CustomCodeStorage 설정 컨테이너|< 사용자 스토리지 컨테이너 >|

2. **Inputs** 폴더 아래에서 **input.js** 를 엽니다. **라이브 입력 추가** 를 선택하고, Azure Data Lake Storage Gen2/Blob 스토리지에서 입력을 추가하고, **Azure 구독에서 선택** 을 선택합니다. 다음 설정 외에는 기본 구성을 사용합니다.

   |설정|제안 값|
   |-------|---------------|
   |속성|입력|
   |구독|< 사용자 구독 >|
   |스토리지 계정|< 사용자 스토리지 계정 >|
   |컨테이너|< 사용자 스토리지 컨테이너 >|
   |serialization 형식|**Custom(사용자 지정)** 을 선택합니다.|
   |SerializationProjectPath|CodeLens에서 **Choose library project path(라이브러리 프로젝트 경로 선택)** 를 선택하고, 이전 섹션에서 만든 **ProtobufDeserializer** 라이브러리 프로젝트를 선택합니다. 프로젝트를 빌드할 **build project(빌드 프로젝트)** 를 선택합니다.|
   |SerializationClassName|CodeLens에서 **select deserialization class(deserialization 클래스 선택)** 를 선택하여 클래스 이름과 DLL 경로를 자동으로 채웁니다.|
   |클래스 이름|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="사용자 지정 역직렬 변환기 입력 추가":::

3. 다음 쿼리를 **ProtobufCloudDeserializer.asaql** 파일에 추가합니다.

   ```sql
   SELECT * FROM Input
   ```

4. [샘플 protobuf 입력 파일](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf)를 다운로드합니다. **Inputs** 폴더에서 마우스 오른쪽 단추로 **Input.json** 을 클릭하고, **로컬 입력 추가** 를 선택합니다. 그런 다음, **local_input1.json** 을 두 번 클릭하고, 다음 설정을 제외한 기본 구성을 사용합니다.

   |설정|제안 값|
   |-------|---------------|
   |로컬 파일 경로 선택|CodeLens를 클릭하여 < The file path for the downloaded sample protobuf input file(다운로드한 샘플 protobuf 입력 파일 샘플의 파일 경로) >을 선택합니다.|

### <a name="execute-the-stream-analytics-job"></a>Stream Analytics 작업 실행

1. **ProtobufCloudDeserializer.asaql** 을 열고, CodeLens에서 **Run Locally(로컬로 실행)** 를 선택한 다음, 드롭다운 목록에서 **Use Local Input(로컬 입력 사용)** 을 선택합니다.

2. 오른쪽 작업 다이어그램 보기의 **Results(결과)** 탭에서 결과를 확인합니다. 또한 작업 다이어그램의 단계를 클릭하여 중간 결과를 확인할 수도 있습니다. 자세한 내용은 [작업 다이어그램을 사용하여 로컬로 디버그](debug-locally-using-job-diagram-vs-code.md)를 참조하세요.

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="로컬 실행 결과 확인":::

Stream Analytics 작업에 대한 사용자 지정 역직렬 변환기를 성공적으로 구현했습니다. 이 자습서에서는 로컬 입력 데이터를 사용하여 사용자 지정 역직렬 변환기를 로컬로 테스트했습니다. 또한 [클라우드에서 라이브 데이터 입력을 사용](visual-studio-code-local-run-live-input.md)하여 테스트할 수 있습니다. 클라우드에서 작업을 실행하려면 입력 및 출력을 올바르게 구성해야 합니다. 그런 다음, Visual Studio Code에서 방금 구현한 사용자 지정 역직렬 변환기를 사용하여 클라우드에서 실행할 작업을 Azure에 제출합니다.

### <a name="debug-your-deserializer"></a>역직렬 변환기 디버그

표준 .NET 코드를 디버그하는 것과 같은 방식으로 .NET 역직렬 변환기를 로컬로 디버그할 수 있습니다. 

1. 중단점을 .NET 함수에 추가합니다.

2. Visual Studio Code 작업 막대에서 **실행** 클릭하고, **launch.json 파일 만들기** 를 선택합니다.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="launch 파일 만들기":::

   **ProtobufCloudDeserializer** 를 선택한 다음, 드롭다운 목록에서 **Azure Stream Analytics** 를 선택합니다.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="launch 파일 만들기 2":::

   **launch.json** 파일을 편집하여 \<ASAScript\>.asaql을 ProtobufCloudDeserializer.asaql로 바꿉니다.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="launch 파일 구성":::

3. **F5** 키를 눌러 디버깅을 시작합니다. 프로그램이 중단점에서 정상적으로 중지됩니다. 이는 로컬 입력 및 라이브 입력 데이터 모두에 대해 작동합니다.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="사용자 지정 역직렬 변환기 디버그":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용된 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 중지하고 나중에 필요할 때 다시 시작할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음 단계를 사용하여 이 자습서에서 만든 리소스를 모두 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택한 다음, 만든 리소스의 이름을 선택합니다.  

2. 리소스 그룹 페이지에서 **삭제** 를 선택하고 텍스트 상자에서 삭제할 리소스의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프로토콜 버퍼 입력 직렬화에 대한 사용자 지정 .NET 역직렬 변환기를 구현하는 방법을 배웠습니다. 사용자 지정 역직렬 변환기를 만드는 방법을 자세히 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> * [Azure Stream Analytics 작업을 위한 다양한 .NET 역직렬 변환기 만들기](custom-deserializer-examples.md)
> * [Visual Studio Code에서 라이브 입력을 사용하여 로컬로 Azure Stream Analytics 작업 테스트](visual-studio-code-local-run-live-input.md)