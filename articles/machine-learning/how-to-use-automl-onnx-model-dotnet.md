---
title: .NET에서 AutoML ONNX 모델을 사용하여 예측 수행
description: ML.NET를 사용하여 .NET에서 AutoML ONNX 모델을 사용하여 예측하는 방법 알아보기
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: how-to
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.custom: automl
ms.openlocfilehash: e37f1a26b348772acbf3af0154bb5eb04fb8436a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129428847"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>.NET에서 AutoML ONNX 모델을 사용하여 예측 수행

이 문서에서는 ML.NET를 사용하여 C# .NET Core 콘솔 애플리케이션에서 AutoML(자동화된 ML) ONNX(Open Neural Network Exchange) 모델을 사용하여 예측하는 방법에 대해 알아봅니다.

[ML.NET](/dotnet/machine-learning/)은 .NET 에코시스템에 대한 오픈 소스, 플랫폼 간, 기계 학습 프레임워크로, [Model Builder](/dotnet/machine-learning/automate-training-with-model-builder) 및 [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli)와 같은 하위 코드 도구뿐 아니라 C# 또는 F#에서 코드 우선 접근 방식을 사용하여 사용자 정의 기계 학습 모델을 학습 및 사용할 수 있게 해줍니다. 프레임 워크는 확장할 수도 있으며 TensorFlow 및 ONNX와 같은 기타 인기 있는 기계 학습 프레임워크를 활용할 수 있습니다.

ONNX는 AI 모델을 위한 오픈 소스 형식입니다. ONNX에서는 프레임워크 간의 상호 운용성을 지원합니다. 즉, PyTorch와 같이 널리 사용되는 여러 기계 학습 프레임워크 중 하나에서 모델을 학습시키고, ONNX 형식으로 변환하며, ML.NET과 같은 다른 프레임워크에서 ONNX 모델을 사용할 수 있습니다. 자세히 알아보려면 [ONNX 웹 사이트](https://onnx.ai/)를 방문하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [.NET Core SDK 3.1 이상](https://dotnet.microsoft.com/download)
- 텍스트 편집기 또는 IDE(예: [Visual Studio](https://visualstudio.microsoft.com/vs/) 또는 [Visual Studio Code](https://code.visualstudio.com/Download))
- ONNX 모델. AutoML ONNX 모델을 학습하는 방법에 대한 자세한 내용은 다음 [은행 마케팅 분류 Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)을 참조하세요.
- [Netron](https://github.com/lutzroeder/netron)(선택 사항)

## <a name="create-a-c-console-application"></a>C# 콘솔 애플리케이션 만들기

이 샘플에서는 .NET Core CLI를 사용하여 애플리케이션을 구축하지만 Visual Studio를 사용하여 동일한 작업을 수행할 수 있습니다. [.NET Core CLI](/dotnet/core/tools/)에 대해 자세히 알아봅니다.

1. 터미널을 열고 새 C# .NET Core 콘솔 애플리케이션을 만듭니다. 이 예제에서 애플리케이션의 이름은 `AutoMLONNXConsoleApp`입니다. 애플리케이션의 내용과 동일한 이름을 사용하여 디렉터리를 만듭니다.

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. 터미널에서 *AutoMLONNXConsoleApp* 디렉터리로 이동합니다.

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>소프트웨어 패키지 추가

1. .NET Core CLI를 사용하여 **Microsoft.ML**, **Microsoft.ML.OnnxRuntime** 및 **Microsoft.ML.OnnxTransformer** NuGet 패키지를 설치합니다.

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    이러한 패키지에는 .NET 애플리케이션에서 ONNX 모델을 사용하는 데 필요한 종속성이 포함되어 있습니다. ML.NET는 예측을 위해 [ONNX 런타임](https://github.com/Microsoft/onnxruntime)을 사용하는 API를 제공합니다.

1. *Program.cs* 파일을 열고 적절한 패키지를 참조하도록 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>ONNX 모델에 대한 참조 추가

콘솔 애플리케이션에서 ONNX 모델에 액세스하는 방법은 빌드 출력 디렉터리에 추가하는 것입니다.  MSBuild 공통 항목에 대해 자세히 알아보려면 [MSBuild 가이드](/visualstudio/msbuild/common-msbuild-project-items)를 참조하세요.

애플리케이션에서 ONNX 모델 파일에 대한 참조 추가

1. 애플리케이션의 *AutoMLONNXConsoleApp* 루트 디렉터리에 ONNX 모델을 복사합니다.
1. *AutoMLONNXConsoleApp.csproj* 파일을 열고 `Project` 노드 내에 다음 콘텐츠를 추가합니다.

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    이 경우 ONNX 모델 파일의 이름은 *automl-model. onnx* 입니다.

1. *Program.cs* 파일을 열고 `Program` 클래스 내에 다음 줄을 추가합니다.

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>MLContext 초기화

`Program` 클래스의 `Main` 메서드 내에 [`MLContext`](xref:Microsoft.ML.MLContext)의 새 인스턴스를 만듭니다.

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext) 클래스는 모든 ML.NET 작업의 시작점이며 `mlContext` 초기화를 통해 모델 수명 주기 간에 공유할 수 있는 새 ML.NET 환경이 생성됩니다. 개념적으로 Entity Framework의 DbContext와 유사합니다.

## <a name="define-the-model-data-schema"></a>모델 데이터 스키마 정의

모델에는 특정 형식의 입력 및 출력 데이터가 필요합니다. ML.NET를 사용하면 클래스를 통해 데이터 형식을 정의할 수 있습니다. 경우에 따라 어떤 형식인지 이미 알고 있을 수 있습니다. 데이터 형식을 모르는 경우 Netron와 같은 도구를 사용하여 ONNX 모델을 검사할 수 있습니다.

이 샘플에 사용된 모델은 NYC TLC Taxi Trip 데이터 세트의 데이터를 사용합니다. 데이터 샘플은 아래와 같습니다.

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3.75|CRD|15.5|
|VTS|1|1|480|2.72|CRD|10.0|
|VTS|1|1|1,680|7.8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>ONNX 모델 검사(선택 사항)

Netron과 같은 도구를 사용하여 모델의 입력 및 출력을 검사합니다.

1. Netron를 엽니다.
1. 상단 메뉴 모음에서 **파일 > 열기** 를 선택하고 파일 브라우저를 사용하여 모델을 선택합니다.
1. 모델이 열립니다. 예를 들어, *automl-model.onnx* 모델의 구조는 다음과 같습니다.

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX 모델":::

1. 그래프의 맨 아래 있는 마지막 노드(이 경우에는 `variable_out1`)를 선택하여 모델의 메타데이터를 표시합니다. 사이드바의 입력 및 출력에는 모델의 예상 입력, 출력 및 데이터 형식이 표시됩니다. 이 정보를 사용하여 모델의 입력 및 출력 스키마를 정의합니다.

### <a name="define-model-input-schema"></a>모델 입력 스키마 정의

*Program.cs* 파일 내에 다음 속성을 사용하여 `OnnxInput`이라는 새 클래스를 만듭니다.

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

각각의 속성은 데이터 세트의 한 열에 매핑됩니다. 특성을 사용하여 속성에 주석을 추가합니다.

[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 특성을 사용하면 데이터 작업을 수행할 때 ML.NET에서 열을 참조하는 방법을 지정할 수 있습니다. 예를 들어, `TripDistance` 속성이 표준 .NET 명명 규칙을 따르는 경우에도 모델은 `trip_distance`라는 열 또는 기능만 인식합니다. 이 명명 불일치를 처리하기 위해 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 특성은 `trip_distance` 이름을 기준으로 `TripDistance` 속성을 열 또는 기능에 매핑합니다.
  
숫자 값의 경우 ML.NET는 [`Single`](xref:System.Single) 값 형식에 대해서만 작동합니다. 하지만 일부 열의 원래 데이터 형식은 정수입니다. [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) 특성은 ONNX 및 ML.NET 간의 형식을 매핑합니다.

데이터 특성에 대한 자세한 내용은 [ML.NET 로드 데이터 가이드](/dotnet/machine-learning/how-to-guides/load-data-ml-net)를 참조하세요.

### <a name="define-model-output-schema"></a>모델 출력 스키마 정의

데이터를 처리한 후에는 특정 형식의 출력을 생성합니다. 데이터 출력 스키마를 정의합니다. *Program.cs* 파일 내에서 다음 속성을 사용하여 `OnnxOutput`이라는 새 클래스를 만듭니다.

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

`OnnxInput`과 마찬가지로 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 특성을 사용하여 `variable_out1` 출력을 보다 설명적인 이름 `PredictedFare`로 매핑합니다.

## <a name="define-a-prediction-pipeline"></a>예측 파이프라인 정의

ML.NET의 파이프라인은 일반적으로 입력 데이터에서 작동하여 출력을 생성하는 일련의 연결된 변환입니다. 데이터 변환에 대해 자세히 알아보려면 [ML.NET 데이터 변환 가이드](/dotnet/machine-learning/resources/transforms)를 참조하세요.

1. `Program` 클래스 내에 `GetPredictionPipeline`이라는 새 메서드 만들기

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. 입력 및 출력 열의 이름을 정의합니다. `GetPredictionPipeline` 메서드 내에 다음 코드를 추가합니다.

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. 파이프라인을 정의합니다. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)는 파이프라인의 작업, 입력 및 출력 스키마 청사진을 제공합니다.

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    이 경우 [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A)이 파이프라인의 유일한 변환이고, 입력 및 출력 열의 이름뿐 아니라 ONNX 모델 파일의 경로도 가져옵니다.

1. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)는 데이터에 적용할 작업 집합만 정의합니다. 데이터에서 작동하는 작업을 [`ITransformer`](xref:Microsoft.ML.ITransformer)라고 합니다. `Fit` 메서드를 사용하여 `onnxPredictionPipeline`에서 하나를 만듭니다.

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A) 메서드에 작업을 수행할 입력으로 [`IDataView`](xref:Microsoft.ML.IDataView)가 필요합니다. [`IDataView`](xref:Microsoft.ML.IDataView)는 테이블 형식을 사용하여 ML.NET에 데이터를 표시하는 방법입니다. 이 경우 파이프라인은 예측에만 사용되기 때문에 빈 [`IDataView`](xref:Microsoft.ML.IDataView)를 제공하여 [`ITransformer`](xref:Microsoft.ML.ITransformer)를 필요한 입력 및 출력 스키마 정보에 보낼 수 있습니다. 그러면 나중에 애플리케이션에서 사용하기 위해 적합한 [`ITransformer`](xref:Microsoft.ML.ITransformer)가 반환됩니다.

    > [!TIP]
    > 이 샘플에서는 파이프라인이 동일한 애플리케이션 내에서 정의 및 사용됩니다. 하지만 별도의 애플리케이션을 사용하여 예측할 파이프라인을 정의 및 사용하는 것이 좋습니다. ML.NET에서는 파이프라인을 직렬화하고 다른 .NET 최종 사용자 애플리케이션에서 나중에 사용할 목적으로 저장할 수 있습니다. ML.NET는 데스크톱 애플리케이션, 웹 서비스, WebAssembly 애플리케이션* 등의 다양한 배포 대상을 지원합니다. 파이프라인 저장에 대해 자세히 알아보려면 [ML.NET 저장 및 로그 학습 모델 가이드](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)를 참조하세요.
    >
    > *WebAssembly는 .NET Core 5 이상에서만 지원됩니다.

1. `Main` 메서드 내에서 필수 매개 변수를 사용하여 `GetPredictionPipeline` 메서드를 호출합니다.

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>모델을 사용하여 예측하기

이제 파이프라인이 있으므로 예측하는 데 사용할 수 있습니다. ML.NET는 [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602)이라는 단일 데이터 인스턴스에서 예측하기 위한 편의 API를 제공합니다.

1. `Main` 메서드 내에서 [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) 메서드를 사용하여 [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602)을 만듭니다.

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. 테스트 데이터 입력을 만듭니다.

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) 메서드를 사용하여 새 `testInput` 데이터를 기반으로 예측하도록 `predictionEngine`을 사용합니다.

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. 예측 결과를 콘솔에 출력합니다.

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. .NET Core CLI를 사용하여 애플리케이션을 실행합니다.

    ```dotnetcli
    dotnet run
    ```

    결과는 다음 출력과 유사하게 나타납니다.

    ```text
    Predicted Fare: 15.621523
    ```

ML.NET에서 예측하기에 대한 자세한 내용은 [모델을 사용하여 예측하기 가이드](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [ASP.NET Core Web API로 모델 배포](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [서버리스 .NET Azure 함수로 모델 배포](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)