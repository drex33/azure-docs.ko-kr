---
title: Azure Data Lake Analytics를 사용하여 Avro 데이터 쿼리 | Microsoft Docs
description: 메시지 본문 속성을 사용하여 디바이스 원격 분석을 Blob Storage에 라우팅하고 Blob Storage에 작성된 Avro 형식 데이터를 쿼리합니다.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 46f2811fa3f5a57add9af0b67444d236e3b18519
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231048"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Azure Data Lake Analytics를 사용하여 Avro 데이터 쿼리

이 아티클에서는 Azure IoT Hub에서 Azure 서비스로 메시지를 효율적으로 라우팅하기 위해 Avro 데이터를 쿼리하는 방법을 설명합니다. [메시지 라우팅](iot-hub-devguide-messages-d2c.md)을 통해 메시지 속성, 메시지 본문, 디바이스 쌍 태그 및 디바이스 쌍 속성 기반의 풍부한 쿼리를 사용하여 데이터를 필터링할 수 있습니다. 메시지 라우팅의 쿼리 기능에 대한 자세한 내용은 [메시지 라우팅 쿼리 구문](iot-hub-devguide-routing-query-syntax.md)에 대한 문서를 참조하세요.

Azure IoT Hub가 Azure Blob Storage에 메시지를 라우팅하는 경우 기본적으로 IoT Hub에서 메시지 본문 속성 및 메시지 속성을 모두 포함하는 Avro 형식으로 콘텐츠를 작성하는 것이 어려운 점이었습니다. Avro 형식은 다른 엔드포인트에는 사용되지 않습니다. Avro 형식은 데이터와 메시지 보존에 적합하지만 이를 사용하여 데이터를 쿼리하기에 어렵습니다. 반면 JSON 또는 CSV 형식은 데이터를 쿼리하기에 훨씬 용이합니다. 이제 IoT Hub는 AVRO뿐만 아니라 JSON의 Blob Storage에 데이터를 쓸 수 있습니다.

자세한 내용은 [Azure Storage를 라우팅 엔드포인트로 사용](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)을 참조하세요.

비관계형 빅 데이터 요구 사항 및 형식을 해결하고 이 어려움을 이겨내려면 데이터 변환 및 크기 조정 모두에 대해 여러 빅 데이터 패턴을 사용할 수 있습니다. 이런 패턴 중 하나인 "쿼리당 지불"은 Azure Data Lake Analytics이며, 이 문서의 핵심 내용입니다. Hadoop 또는 기타 솔루션에서 쿼리를 쉽게 실행할 수 있지만 Data Lake Analytics는 이 "쿼리당 지불" 방법에 더 적합합니다.

U-SQL에는 Avro용 "추출기"가 있습니다. 자세한 내용은 [U-SQL Avro 예제](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)를 참조하세요.

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro 데이터 쿼리 및 CSV 파일로 내보내기

이 섹션에서는 데이터를 기타 리포지토리 또는 데이터 저장소에 쉽게 배치할 수 있지만 Avro 데이터를 쿼리하고 이를 Azure Blob Storage의 CSV 파일로 내보냅니다.

1. 메시지를 선택하기 위해 메시지 본문에서 속성을 사용하여 데이터를 Azure Blob Storage 엔드포인트에 라우팅하도록 Azure IoT Hub를 설정합니다.

   ![“사용자 지정 엔드포인트” 섹션](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![라우팅 규칙](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   경로 및 사용자 지정 엔드포인트 설정에 대한 자세한 내용은 [IoT 허브에 대한 메시지 라우팅](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)을 참조하세요.

2. 제품 설명서에서 참조하도록 속성 또는 메시지 본문에서 인코딩, 콘텐츠 형식 및 필요한 데이터가 디바이스에 포함되는지 확인합니다. 여기에 표시된 대로 Device Explorer에서 이러한 특성을 볼 때 해당 특성이 올바로 설정되어 있는지 확인할 수 있습니다.

   ![이벤트 허브 데이터 창](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Azure Data Lake Store 인스턴스 및 Data Lake Analytics 인스턴스를 설정합니다. Azure IoT Hub는 Data Lake Store 인스턴스에 라우팅되지 않지만 Data Lake Analytics 인스턴스는 필요로 합니다.

   ![Data Lake Store 및 Data Lake Analytics 인스턴스](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. Data Lake Analytics에서 Azure Blob Storage를 Azure IoT Hub에서 데이터를 라우팅하는 동일한 Blob Storage인 추가 저장소로 구성합니다.

   ![“데이터 소스” 창](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. [U-SQL Avro 예제](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)에서 설명한 대로, 4개의 DLL 파일이 필요합니다. Data Lake Store 인스턴스의 위치로 이러한 파일을 업로드합니다.

   ![업로드된 4개의 DLL 파일](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Visual Studio에서 U-SQL 프로젝트를 만듭니다.

   ![U-SQL 프로젝트 만들기](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. 다음과 같은 스크립트의 콘텐츠를 새로 만든 파일로 붙여넣습니다. Data Lake Analytics 계정, 연결된 DLL 파일 경로 및 스토리지 계정에 대한 올바른 경로 등 3개의 강조 표시된 섹션을 수정합니다.

   ![수정될 세 개의 섹션](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   CSV 파일에 대한 간단한 출력의 실제 U-SQL 스크립트입니다.

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll&quot;;
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @&quot;/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll&quot;;
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @&quot;/Assemblies/Avro/Newtonsoft.Json.dll&quot;;
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @&quot;/Assemblies/Avro/log4net.dll&quot;;

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @&quot;wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}&quot;;
        DECLARE @output_file string = @&quot;/output/output.csv&quot;;

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@&quot;
        {
            &quot;&quot;type&quot;&quot;:&quot;&quot;record&quot;&quot;,
            &quot;&quot;name&quot;&quot;:&quot;&quot;Message&quot;&quot;,
            &quot;&quot;namespace&quot;&quot;:&quot;&quot;Microsoft.Azure.Devices&quot;&quot;,
            &quot;&quot;fields&quot;&quot;:
           [{
                &quot;&quot;name&quot;&quot;:&quot;&quot;EnqueuedTimeUtc&quot;&quot;,
                &quot;&quot;type&quot;&quot;:&quot;&quot;string&quot;&quot;
            },
            {
                &quot;&quot;name&quot;&quot;:&quot;&quot;Properties&quot;&quot;,
                &quot;&quot;type&quot;&quot;:
                {
                    &quot;&quot;type&quot;&quot;:&quot;&quot;map&quot;&quot;,
                    &quot;&quot;values&quot;&quot;:&quot;&quot;string&quot;&quot;
                }
            },
            {
                &quot;&quot;name&quot;&quot;:&quot;&quot;SystemProperties&quot;&quot;,
                &quot;&quot;type&quot;&quot;:
                {
                    &quot;&quot;type&quot;&quot;:&quot;&quot;map&quot;&quot;,
                    &quot;&quot;values&quot;&quot;:&quot;&quot;string&quot;&quot;
                }
            },
            {
                &quot;&quot;name&quot;&quot;:&quot;&quot;Body&quot;&quot;,
                &quot;&quot;type&quot;&quot;:[&quot;&quot;null&quot;&quot;,&quot;&quot;bytes&quot;&quot;]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Data Lake Analytics가 분석 단위 10개 및 처리된 파일 177개로 제한된 다음의 스크립트를 실행하는 데 5분이 걸렸습니다. 결과는 다음 그림에 표시되는 CSV 파일 출력에 표시됩니다.

    ![CSV 파일로 출력 결과](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![CSV 파일로 변환된 출력](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    JSON을 구문 분석하려면 8단계를 계속합니다.

8. 대부분의 IoT 메시지는 JSON 파일 형식입니다. 다음 줄을 추가하여 WHERE 절을 추가하고 필요한 데이터만 출력하게 하는 메시지를 JSON 파일로 구문 분석할 수 있습니다.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                message["event"] AS msgevent,
                message["object"] AS msgobject,
                message["status"] AS msgstatus,
                message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    출력은 `SELECT` 명령에서 각 항목에 대한 열을 표시합니다.

    ![각 항목에 대한 열을 표시하는 출력](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure IoT Hub에서 Azure 서비스로 메시지를 효율적으로 라우팅하기 위해 Avro 데이터를 쿼리하는 방법을 알아보았습니다.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.

IoT Hub의 메시지 라우팅에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기](iot-hub-devguide-messaging.md)를 참조하세요.
