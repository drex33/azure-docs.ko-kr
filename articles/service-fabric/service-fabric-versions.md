---
title: Azure Service Fabric 버전
description: Azure Service Fabric의 클러스터 버전과 적극적으로 지원되는 플랫폼 버전에 대해 알아봅니다.
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 2d662ec817ed1fffed56836feb7a8e73f279408b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460378"
---
# <a name="service-fabric-supported-versions"></a>Service Fabric 지원 버전
이 문서의 표에는 적극적으로 지원되는 Service Fabric 및 플랫폼 버전이 요약되어 있습니다.

## <a name="windows"></a>Windows

| Service Fabric 런타임 |다음에서 직접 업그레이드 가능|다음으로 다운그레이드 가능*|호환되는 SDK 또는 NuGet 패키지 버전|지원되는 .NET 런타임** |OS 버전 |지원 종료 |
| --- | --- | --- | --- | --- | --- | --- |
| 8.2 RTO<br>8.2.1235.9590 | 8.0 CU3<br>8.0.536.9590 | 8.0 | 버전 5.2보다 적거나 같음 | .NET 5.0(GA), >= .NET Core 3.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 현재 버전 |
| 8.1 CU3.1<br>8.1.337.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | 버전 5.1보다 낮거나 같음 | .NET 5.0(GA), >= .NET Core 3.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 CU3<br>8.1.335.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | 버전 5.1보다 낮거나 같음 | .NET 5.0(GA), >= .NET Core 3.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 CU2<br>8.1.329.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | 버전 5.1보다 낮거나 같음 | .NET 5.0(GA), >= .NET Core 3.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 CU1<br>8.1.321.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | 버전 5.1보다 낮거나 같음 | .NET 5.0 (GA), >= .NET Core 2.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 RTO<br>8.1.316.9590 | 7.2 CU7<br>7.2.477.9590 | 8.0 | 버전 5.1보다 낮거나 같음 | .NET 5.0 (GA), >= .NET Core 2.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.0 CU3<br>8.0.536.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | 버전 5.0보다 낮거나 같음 | .NET 5.0 (GA), >= .NET Core 2.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 2월 28일 |
| 8.0 CU2<br>8.0.521.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | 버전 5.0보다 낮거나 같음 | .NET 5.0 (GA), >= .NET Core 2.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 2월 28일 |
| 8.0 CU1<br>8.0.516.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | 버전 5.0보다 낮거나 같음 | .NET 5.0 (GA), >= .NET Core 2.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 2월 28일 |
| 8.0 RTO<br>8.0.514.9590 | 7.1 CU10<br>7.1.510.9590 | 7.2 | 버전 5.0보다 낮거나 같음 | .NET 5.0 (GA), >= .NET Core 2.1, <br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2022년 2월 28일 |
| 7.2 CU7<br>7.2.477.9590 | 7.0 CU9<br>7.0.478.9590 | 7.1 | 버전 4.2보다 낮거나 같음 | .NET 5.0(미리 보기 지원), .NET Core 2.1 이상,<br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2021년 11월 30일 |
| 7.2 CU6<br>7.2.457.9590 | 7.0 CU4<br>7.0.470.9590 |7.1 | 버전 4.2보다 낮거나 같음 | .NET 5.0(미리 보기 지원), .NET Core 2.1 이상,<br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date)| 2021년 11월 30일 |
| 7.2 RTO-CU5<br>7.2.413.9590-7.2.452.9590 | 7.0 CU4<br>7.0.470.9590 | 7.1 |버전 4.2보다 낮거나 같음 | >= .NET Core 2.1,<br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date)| 2021년 11월 30일 |
| 7.1<br>7.1.510.9590 |7.0 CU3<br>7.0.466.9590 |해당 없음 | 버전 4.1보다 낮거나 같음 | >= .NET Core 2.1,<br>모두 >= .NET Framework 4.5 | [지원되는 OS 버전 참조](#supported-windows-versions-and-support-end-date) | 2021년 7월 31일 |

\* 버전은 지원 되지 않아야 합니다.<br/>
** Service Fabric은 .NET Core 런타임을 제공하지 않습니다. 서비스 작성자가 이를 <a href="/dotnet/core/deploying/">사용할 수 있는지</a> 확인합니다.

## <a name="supported-windows-versions-and-support-end-date"></a>지원되는 Windows 버전 및 지원 종료 날짜
OS 버전 지원이 종료되면 특정 OS에서 Service Fabric 지원이 종료됩니다.


### <a name="windows-server"></a>Windows Server

| OS 버전 | Service Fabric 지원 종료 날짜 | OS 수명 주기 링크 |
|---|---|---|
|Windows Server 2019|2029/1/9|<a href="/lifecycle/products/windows-server-2019">Windows Server 2019 - Microsoft 수명 주기</a>|
|Windows Server 2016 |2027/1/12|<a href="/lifecycle/products/windows-server-2016">Windows Server 2016 - Microsoft 수명 주기</a>|
|Windows Server 2012 R2 |2023/10/10|<a href="/lifecycle/products/windows-server-2012-r2">Windows Server 2012 R2 - Microsoft 수명 주기</a>|
|버전 20H2 |2022/5/10|<a href="/lifecycle/products/windows-server">Windows Server - Microsoft 수명 주기</a>|
|버전 2004 |2021/12/14|<a href="/lifecycle/products/windows-server">Windows Server - Microsoft 수명 주기</a>|
|버전 1909 |2021/5/11|<a href="/lifecycle/products/windows-server">Windows Server - Microsoft 수명 주기</a>|

<br>

### <a name="windows-10"></a>Windows 10

| OS 버전 | Service Fabric 지원 종료 날짜 | OS 수명 주기 링크 |
| --- | --- | --- |
| Windows 10 2019 LTSC | 2029/1/9 | <a href="/lifecycle/products/windows-10-ltsc-2019">Windows 10 2019 LTSC - Microsoft 수명 주기</a> |
| 버전 20H2 | 2023/5/9 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise 및 Education - Microsoft 수명 주기</a> |
| 버전 2004 | 2021/12/14| <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise 및 Education - Microsoft 수명 주기</a> |
| 버전 1909 | 2022/5/10 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise 및 Education - Microsoft 수명 주기</a> |
| 버전 1809 | 2021/5/11 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise 및 Education - Microsoft 수명 주기</a> |
| 버전 1803 | 2021/5/11 | <a href="/lifecycle/products/windows-10-enterprise-and-education">Windows 10 Enterprise 및 Education - Microsoft 수명 주기</a> |

## <a name="linux"></a>Linux

| Service Fabric 런타임 | 다음에서 직접 업그레이드 가능 |다운 그레이드 가능 *|호환되는 SDK 또는 NuGet 패키지 버전 | 지원되는 .NET 런타임** | OS 버전 | 지원 종료 |
| --- | --- | --- | --- | --- | --- | --- |
| 8.2 RTO<br>8.2.1124.1 | 8.0 CU3<br>8.0.527.1 | 8.0 | 버전 5.2 보다 작거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 현재 버전 |
| 8.1 CU 3.1<br>8.1.340.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | 버전 5.1보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 CU3<br>8.1.334.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | 버전 5.1보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 CU2<br>8.1.328.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | 버전 5.1보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 CU1<br>8.1.323.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | 버전 5.1보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.1 RTO<br>8.1.320.1 | 7.2 CU7<br>7.2.476.1 | 8.0 | 버전 5.1보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 6월 30일 |
| 8.0 CU3<br>8.0.527.1 | 7.1 CU8<br>7.1.508.1 | 7.2 | 버전 5.0보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 2월 28일 |
| 8.0 CU1<br>8.0.515.1 | 7.1 CU8<br>7.1.508.1 | 7.2 | 버전 5.0보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 2월 28일 |
| 8.0 RTO<br>8.0.513.1 | 7.1 CU8<br>7.1.508.1 | 7.2 | 버전 5.0보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2022년 2월 28일 |
| 7.2 CU7<br>7.2.476.1 | 7.0 CU9<br>7.0.472.1 | 7.1 | 버전 4.2보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2021년 11월 30일 |
| 7.2 RTO-CU6<br>7.2.431.1-7.2.456.1 | 7.0 CU4<br>7.0.469.1 | 7.1 | 버전 4.2보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2021년 11월 30일 |
| 7.1<br>7.1.508.1| 7.0 CU3<br>7.0.465.1 | 해당 없음 | 버전 4.1보다 낮거나 같음 | >= .NET Core 2.1 | [지원되는 OS 버전 참조](#supported-linux-versions-and-support-end-date) | 2021년 7월 31일 |

\* 버전이 지원되지 않아야 합니다.<br/>
** Service Fabric은 .NET Core 런타임을 제공하지 않으며 서비스 작성자가 이를 <a href="/dotnet/core/deploying/">사용할 수 있는지</a> 확인합니다.

## <a name="supported-linux-versions-and-support-end-date"></a>지원되는 Linux 버전 및 지원 종료 날짜
OS 버전 지원이 종료되면 특정 OS에서 Service Fabric 지원이 종료됩니다.

#### <a name="ubuntu"></a>Ubuntu
| OS 버전 | Service Fabric 지원 종료 날짜| OS 수명 주기 링크 |
| --- | --- | --- |
| Ubuntu 18.04 | 2028년 4월 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu 수명 주기</a>|
| Ubuntu 16.04 | 2024년 4월 | <a href="https://wiki.ubuntu.com/Releases">Ubuntu 수명 주기</a>|

## <a name="service-fabric-version-name-and-number-reference"></a>Service Fabric 버전 이름 및 번호 참조
다음 표에는 Service Fabric의 버전 이름과 해당 버전 번호가 나와 있습니다.

| 버전 이름 | Windows 버전 번호 | Linux 버전 번호 |
| --- | --- | --- |
| 8.2 RTO | 8.2.1235.9590 | 8.2.1124.1 |
| 8.1 CU3.1 | 8.1.337.9590 | 8.1.340.1 |
| 8.1 CU3 | 8.1.335.9590 | 8.1.334.1 |
| 8.1 CU2 | 8.1.329.9590 | 8.1.328.1 |
| 8.1 CU1 | 8.1.321.9590 | 8.1.323.1 |
| 8.1 RTO | 8.1.316.9590 | 8.1.320.1 |
| 8.0 CU3 | 8.0.536.9590 | 8.0.527.1 |
| 8.0 CU2 | 8.0.521.9590 | 해당 없음 |
| 8.0 CU1 | 8.0.516.9590 | 8.0.515.1 | 
| 8.0 RTO | 8.0.514.9590 | 8.0.513.1 | 
| 7.2 CU7 | 7.2.477.9590 | 7.2.476.1 |
| 7.2 CU6 | 7.2.457.9590 | 7.2.456.1 |
| 7.2 CU5 | 7.2.452.9590 | 7.2.454.1 |
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7.2 CU3 | 7.2.433.9590 | 해당 없음 |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 RTO | 7.2.413.9590 | 해당 없음 |
| 7.1 CU10 | 7.1.510.9590 | 해당 없음 |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.4 CU8 | 6.4.670.9590 | 해당 사항 없음|
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU6 | 6.4.658.9590 | 해당 사항 없음|
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU2 | 6.4.622.9590 | 해당 사항 없음|
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.1 CU3 | 6.1.472.9494 | 해당 사항 없음|
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 5.7 CU4 | 5.7.221.9494 | 해당 사항 없음|
| 5.7 RTO | 5.7.198.9494 | 해당 사항 없음|
| 5.6 CU3 | 5.6.220.9494 | 해당 사항 없음|
| 5.6 CU2 | 5.6.210.9494 | 해당 사항 없음|
| 5.6 RTO | 5.6.204.9494 | 해당 사항 없음|
| 5.5 CU4 | 5.5.232.0 | 해당 사항 없음|
| 5.5 CU3 | 5.5.227.0 | 해당 사항 없음|
| 5.5 CU2 | 5.5.219.0 | 해당 사항 없음|
| 5.5 CU1 | 5.5.216.0    | 해당 사항 없음|
| 5.4 CU2 | 5.4.164.9494 | 해당 사항 없음|
| 5.3 CU3 | 5.3.311.9590 | 해당 사항 없음|
| 5.3 CU2 | 5.3.301.9590 | 해당 사항 없음|
| 5.3 CU1 | 5.3.204.9494 | 해당 사항 없음|
| 5.3 RTO | 5.3.121.9494 | 해당 사항 없음|
