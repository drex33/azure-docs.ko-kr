---
title: 산업용 IoT를 사용하여 Azure Data Explorer로 데이터 가져오기
description: 이 자습서에서는 IIoT 데이터를 ADX로 이동하고 대시보드를 만들어 데이터를 보는 방법을 알아봅니다.
author: eross-msft
ms.author: lizross
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 6/16/2021
ms.openlocfilehash: 4ddd2e14d3be90c211d0e278a11e45bd51154fa1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550024"
---
# <a name="tutorial-using-industrial-iot-to-pull-data-into-azure-data-explorer"></a>자습서: 산업용 IoT를 사용하여 데이터를 Azure Data Explorer로 끌어오기

Azure IIoT(산업용 IoT) 플랫폼은 에지 모듈과 클라우드 마이크로서비스를 Azure PaaS(Platform as a Service) 서비스와 결합하여 산업 자산 검색 및 자산 데이터 취득 기능을 제공합니다. 산업용 설정에서 이 작업의 사실상 표준은 OPC UA입니다. 이 자습서에서는 간단한 사용 사례(클라우드 기반 조건 모니터링)를 예로 들어 OPC UA에서 사용하기 위해 Azure를 구성하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 배웁니다.
- Azure 계정을 얻고 IoT Hub 배포
- IoT Edge 배포
- OPC Publisher 설치 및 구성
- 시계열 데이터베이스 설정
- 대시보드 만들기
- 들어오는 데이터의 그래프 만들기

## <a name="requirements"></a>요구 사항

이 자습서를 시작하기 전에 Azure 계정이 있어야 합니다. Azure 계정이 없는 경우 [여기를 클릭](https://azure.microsoft.com/free/)하여 무료로 얻을 수 있습니다.

## <a name="deploy-an-s1-iot-hub"></a>S1 IoT Hub 배포

Azure 구독에서 가장 먼저 해야 할 작업은 [포털](../azure-portal/azure-portal-quickstart-center.md)로 이동하여 [S1 IoT Hub를 배포](../iot-hub/iot-hub-create-through-portal.md)하는 것입니다. 무료 Azure 계정은 일일 400,000개의 4K 메시지를 제공합니다. 단일 4K 메시지에 약 15개의 OPC UA PubSub 데이터 태그를 넣을 수 있기 때문에 일일 6백만 개의 데이터 태그를 사용할 수 있으므로 이 사용 사례에 충분합니다. IoT Hub가 배포되면 공유 액세스 정책인 iothubowner에서 *iothubowner* 기본 연결 문자열을 복사합니다. 이 연결 문자열은 IoT Edge 설치에 필요합니다.

## <a name="deploy-iot-edge"></a>IoT Edge 배포

Windows 10 PC 또는 VM에서 [IoT Edge 설치 관리자](https://lnkd.in/ga6Ew4X)를 사용하여 IoT Edge를 배포합니다. 이 PC/VM은 인터넷에 연결되어 있어야 하며, 사용하려는 머신 또는 산업용 연결 어댑터 소프트웨어(dataFEED, Zenon, Kepware 등)에도 액세스할 수 있어야 합니다. 시설에서 머신이 있는 OT 네트워크와 인터넷 연결 사이에 여러 네트워크 계층을 사용해야 하는 경우 IoT Edge 디바이스를 중첩(즉, “데이지 체인”)할 수 있습니다. [GitHub](https://github.com/Azure/Industrial-IoT-Gateway-Installer/blob/master/Releases/Linux.zip)의 설치 관리자를 사용하여 Linux에서 기본적으로 IoT Edge를 실행할 수도 있습니다.

IoT Edge 설치 관리자를 실행할 때 **Docker Desktop 사용** 확인란을 선택하고, **Azure 산업용 클라우드 플랫폼(PaaS)에 대한 IoT Edge 구성** 라디오 단추를 선택된 상태로 유지하고, *iothubowner* 연결 문자열을 제공된 텍스트 상자에 붙여넣습니다. 설치 관리자는 실행 중인 PC의 이름을 Edge 인스턴스 이름으로 자동으로 미리 채우지만 필요한 경우 변경할 수 있습니다. **설치** 를 누릅니다. Docker Desktop이 검색되지 않으면 설치 관리자가 자동으로 다운로드합니다. Docker Desktop을 설치한 다음, 최상의 성능을 위해 WSL2(Linux용 Windows 하위 시스템 V2)로 구성합니다. Docker Desktop 설치가 완료되면 IoT Edge 설치 관리자를 다시 시작하고 다시 구성한 후 설치를 다시 누릅니다. Azure Portal의 **IoT Hub** 페이지에서 **IoT Edge** 탭으로 이동한 다음, 새로 설치한 IoT Edge 디바이스가 표시될 때까지 기다립니다(새로 고침을 몇 번 눌러야 할 수도 있음).

## <a name="install-and-configure-opc-publisher"></a>OPC Publisher 설치 및 구성

다음으로, Edge에서 OPC UA PubSub를 사용하여 모든 원격 분석 데이터를 표준화하고 정규화하는 데 사용되는 OPC Publisher를 설치하고 구성합니다. 여기서는 이진 형식을 클라우드 데이터베이스가 이해할 수 있는 형식으로 다시 변환하기 위한 별도의 클라우드 서비스가 없어도 클라우드 데이터베이스로 직접 수집할 수 있다는 점에서 OPC UA JSON 인코딩을 사용합니다. 이 업계 표준 형식을 사용하면 클라우드의 관리 오버헤드와 비용이 줄어듭니다. 이것은 중요한 사항입니다. 많은 사람들이 보다 효율적인 원격 분석 인코딩을 찾았다고 말하지만 클라우드에서 대규모로 원격 분석을 클라우드 데이터베이스 지원 형식으로 변환하는 작업의 비용 및 관리 측면을 모두 과소평가합니다. 

이제 OPC Publisher의 **시작** 페이지로 이동하여 지침을 따릅니다. **컨테이너 만들기 옵션** 에서 다음과 같이 지정합니다.

```
{
    "Hostname": "OPCPublisher",
    "Cmd": [
        "PkiRootPath=/appdata/pki", 
        "--lf=/appdata/publisher.log", 
        "--pf=/appdata/publishednodes.json",
        "--aa",
        "--di=60",
        "--me=Json",
        "--mm=PubSub"
    ],
    "HostConfig": {
        "Binds": [
            "/c/IoTEdgeMapping:/appdata"
        ]
    }
}
```

그런 다음, 클라우드 시계열 원격 분석 메시지를 생성하는 머신(또는 연결 어댑터 소프트웨어)에서 OPC UA 데이터 노드를 구성합니다. 이렇게 하려면 여기서 템플릿 ***publishednodes.json** _ 파일을 _*_C:\IoTEdgeMapping_*_ 에 복사하고 OPC UA 서버 _ *EndpointURL**과 클라우드에 보낼 데이터의 OPC UA 노드 ID로 채웁니다. 보낼 데이터를 모르는 경우 ID가 ns=0;i=2258인 데이터 노드가 서버의 현재 시간으로, 1초마다 변경됩니다. OPC UA는 데이터가 변경된 경우에만 데이터 변경 내용을 게시합니다. 그러나 데이터가 변경되지 않은 경우에도 OPC Publisher에서 특정 간격마다 데이터를 읽고 보낼 수 있습니다.

파일 편집이 완료되면 명령줄 명령 “**iotedge restart OPCPublisher**”를 사용하여 OPC Publisher를 다시 시작합니다. OPC Publisher는 지정된 모든 OPC UA 서버에 연결하고 나열된 모든 노드를 게시하려고 시도합니다. 경험상, 먼저 IoT Edge를 실행하는 PC에서 UA Expert와 같은 OPC UA 클라이언트를 사용하여 OPC UA 서버에 연결할 수 있는지 확인하고 DNS가 작동하지 않는 경우를 위해 호스트 이름 대신 OPC UA 서버의 IP 주소도 EndpointURL에 사용해 보아야 합니다. 이제 Azure Portal의 **IoT Hub** 페이지로 돌아가 **개요** 탭에서 수신되는 **디바이스-클라우드** 메시지를 볼 수 있는지 다시 확인합니다.

## <a name="set-up-the-time-series-database"></a>시계열 데이터베이스 설정

이제 OPC UA 원격 분석을 수신할 시계열 데이터베이스를 클라우드에서 설정합니다. 최적 성능을 위해 여러 데이터베이스 계층을 사용하고, 풍부한 분석과 대시보드 기능을 포함하며, 예측 및 변칙 검색도 지원한다는 점에서 Azure Data Explorer를 사용하여 작업을 수행하는 것이 가장 좋습니다. Azure Portal에서 인스턴스를 배포하고 개발/테스트 워크로드 SKU를 선택합니다. 또한 구성에서 스트리밍 수집을 사용하도록 설정해야 합니다.

OPC UA는 OPC UA 서버의 **DatasetWriterID** 및 **ExpandedNodeID**(네임스페이스와 노드 ID 모두 포함)로 원격 분석 데이터 태그를 고유하게 식별합니다. 또한 연결된 클라이언트에 제공되기 전에 OPC UA 서버가 데이터를 읽은 시간의 타임스탬프를 *SourceTimeStamp* 라고 합니다. 따라서 OPC UA 원격 분석의 시계열을 만들기 위해 데이터베이스 테이블에 **DatasetWriterID**, **ExpandedNodeID**, 읽은 데이터 값, **SourceTimeStamp** 를 제공하려고 합니다. Azure Data Explorer를 사용하여 지금 만들려는 항목이 이것입니다. OPC UA PubSub 메시지에 데이터 값 형식(float, int 등)도 포함할 수 있으며, OPC UA 사양에서는 이 기능을 ‘되돌릴 수 있는 인코딩’이라고 합니다.

먼저 이 사용 사례에 충분한 기본값인 10년 데이터 보존 기간으로 데이터베이스를 만듭니다. 그런 다음, **데이터 연결 만들기** 를 누르고 **IoT Hub** 를 선택하여 IoT Hub에서 직접 스트리밍 수집을 구성합니다. 연결에 이름을 지정하고 드롭다운에서 IoT Hub를 선택합니다. **공유 액세스 정책** 에서 *iothubowner* 를 선택하고 **소비자 그룹** 으로 *$Default* 를 선택합니다. 지금은 **테이블 이름** 을 비워 두되 **데이터 형식** 에서 *MULTILINE JSON* 을 선택하고 **매핑 이름** 으로 *opcua_mapping* 을 입력합니다. **만들기** 를 선택합니다.

## <a name="create-the-database-tables"></a>데이터베이스 테이블 만들기

새 데이터베이스에 필수 테이블을 만듭니다. 먼저 원시 OPC UA PubSub JSON 원격 분석 메시지를 수신할 단일 열이 있는 준비 테이블을 만듭니다. Azure Data Explorer는 여기에 설명된 기본 제공 KQL(Kusto 쿼리 언어)을 사용합니다. **쿼리** 탭을 선택하고 다음을 입력합니다.

```
.create table opcua_raw(payload: dynamic)

```

이전에 다음 명령을 사용하여 설정한 IoT Hub로 *opcua_mapping* 원격 분석 수집에 대한 매핑을 만듭니다.

```
.create table opcua_raw ingestion json mapping "opcua_mapping" @'[{"column":"payload","path":"$","datatype":"dynamic"}]'

```

**데이터 수집** 탭, 데이터 수집의 이름, **편집** 을 차례로 선택하고 **테이블 이름** 에 *opcua_raw* 를 입력한 다음, **업데이트** 를 눌러 방금 만든 테이블의 테이블 이름으로 원격 분석 수집을 업데이트합니다. **쿼리** 탭으로 돌아가서 첫 번째 원격 분석 메시지가 테이블에 입력될 때까지 몇 분 정도 기다립니다. 테이블에서 10개 행을 쿼리하여 이 작업을 확인할 수 있습니다.

```
opcua_raw
| take 10
```

OPC UA PubSub에서 **DataSetWriterID** 로 식별되는 데이터 세트를 사용하여 원격 분석을 단일 메시지로 일괄 처리할 수 있습니다. 해당 데이터 세트의 “일괄 처리를 해제”하여 데이터베이스의 개별 행에 배치해야 합니다. *mv-expand* 연산자를 사용하여 수집된 JSON을 새 중간 테이블로 확장하면 됩니다. 중간 테이블을 먼저 만들어야 합니다.

```
.create table opcua_intermediate(DataSetWriterID: string, payload: dynamic)
```

이제 확장을 수행할 다음 함수를 만듭니다.

```
.create-or-alter function OPCUARawExpand() {
    opcua_raw
    |mv-expand records = payload.Messages
    |project 
        DataSetWriterID = tostring(records["DataSetWriterId"]),
        Payload = todynamic(records["Payload"])
}
```

그런 다음, 새 함수를 적용하여 중간 테이블을 업데이트합니다.

```
.alter table opcua_intermediate policy update @'[{"Source": "opcua_raw", "Query": "OPCUARawExpand()", "IsEnabled": "True"}]'
```

새 테이블이 올바르게 채워지고 있는지 확인합니다.

```
opcua_intermediate
| take 10
```

 최종 OPC UA 원격 분석 테이블을 만들고 OPC UA 데이터 세트의 각 항목을 추가로 확장하여 이 테이블을 채웁니다.

```
.create table opcua_telemetry (DataSetWriterID: string, ExpandedNodeID: string, Value: dynamic, SourceTimestamp: datetime)
```

아래에 표시된 함수를 만듭니다.

```
.create-or-alter function OPCUADatasetExpand() {
    opcua_intermediate
    | mv-apply payload on (
        extend key = tostring(bag_keys(payload)[0])
        | extend p = payload[key]
        | project ExpandedNodeId = key, Value = todynamic(p.Value), SourceTimestamp = todatetime(p.SourceTimestamp)
    )
}
```

새 함수를 적용하여 세 번째이자 최종 테이블을 업데이트합니다.

```
.alter table opcua_telemetry policy update @'[{"Source": "opcua_intermediate", "Query": "OPCUADatasetExpand()", "IsEnabled": "True"}]'
```

OPC UA 원격 분석 테이블을 살펴보려면 다음 명령을 사용합니다.

```
opcua_telemetry
| take 10
```

## <a name="create-line-graph-of-the-data"></a>데이터의 꺾은선형 그래프 만들기

이제 데이터를 모두 부동 소수점 숫자로 캐스팅하여 데이터의 꺾은선형 그래프를 만들어 보겠습니다. 캐스트에 실패하면 데이터가 무시됩니다. 호스트된 Azure Data Explorer 대시보드를 이 작업에 사용합니다. **웹 UI에서 열기** 를 클릭하고 새 창에서 **대시보드** 를 클릭한 다음, **새 대시보드 만들기**, **타일 추가** 를 차례로 선택합니다. 그런 다음, **데이터 원본** 을 선택하고 Azure Data Explorer 인스턴스의 이름을 `https://<YourInstanceName>.<Your RegionName>.kusto.windows.net` 형식으로 입력합니다. 데이터베이스를 선택하고 **적용** 을 누릅니다. 다음 쿼리를 입력합니다.

```
opcua_telemetry
| project SourceTimestamp, yaxis = todouble(Value), DataSetWriterID,  ExpandedNodeID
```

**시각적 개체** 탭을 누르고 **Y 열** 로 *yaxis*, **X 열** 로 *SourceTimeStamp*, **계열 열** 로 *DataSetWriterID* 를 선택합니다.

이제 클라우드에서 조건 모니터링 사용 사례 데이터에 사용할 멋진 원격 분석 대시보드를 만들었습니다.