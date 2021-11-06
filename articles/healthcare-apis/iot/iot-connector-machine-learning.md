---
title: IoT 커넥터 및 Azure Machine Learning Service - Azure Healthcare API
description: 이 문서에서는 IoT 커넥터 및 Azure Machine Learning 서비스를 사용하는 방법을 알아봅니다.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 8150734243fda78805b5ef4cbf3ab318222b21cb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894863"
---
# <a name="iot-connector-and-azure-machine-learning-service"></a>IoT 커넥터 및 Azure Machine Learning Service

이 문서에서는 IoT 커넥터 및 Azure Machine Learning Service를 사용하여 살펴보겠습니다.

## <a name="iot-connector-and-azure-machine-learning-service-reference-architecture"></a>IoT 커넥터 및 Azure Machine Learning Service 참조 아키텍처

IoT 커넥터를 사용하면 IoT 디바이스가 전자 의료 기록 교환(FHIR&#174;) 서비스와 원활하게 통합할 수 있습니다. 이 참조 아키텍처는 IoMT(의료 사물 인터넷) 프로젝트의 채택을 가속화하도록 설계되었습니다. 이 솔루션은 ML(Machine Learning) 컴퓨팅에 Azure Databricks 사용합니다. 그러나 Kubernetes 또는 파트너 ML 솔루션이 있는 Azure ML Services는 Machine Learning 채점 환경에 적합할 수 있습니다.

네 개의 선 색은 데이터 여정의 다른 부분을 표시합니다.

- **파랑** = IoT 데이터를 FHIR 서비스로.
- **녹색** = IoT 데이터 점수 매기기 데이터 경로
- **빨간색** = 환자 위험을 임상의에게 알리기 위한 데이터 핫 경로입니다. 핫 경로의 목표는 가능한 한 실시간에 가깝게 하는 것입니다.
- **주황색** = 데이터의 웜 경로입니다. 여전히 환자 치료에서 임상의를 지원합니다. 데이터 요청은 일반적으로 수동으로 또는 새로 고침 일정에 따라 트리거됩니다.

:::image type="content" source="media/iot-concepts/iot-connector-machine-learning.png" alt-text="IoT 커넥터 및 Machine Learning 서비스 참조 아키텍처의 스크린샷." lightbox="media/iot-concepts/iot-connector-machine-learning.png":::

**데이터 수집 – 1~5단계**

1. IoT 디바이스 또는 디바이스 게이트웨이를 통해 Azure IoT Hub/Azure IoT Edge로 전송된 데이터입니다.
2. Azure IoT Hub로 전송된 Azure IoT Edge의 데이터입니다.
3. 디바이스 관리를 위해 보안 스토리지 환경으로 전송된 원시 IoT 디바이스 데이터의 복사본입니다.
4. PHI IoMT 페이로드는 Azure IoT Hub에서 IoT 커넥터로 이동합니다. 여러 Azure 서비스는 1개의 IoT 커넥터 아이콘으로 표시됩니다.
5. 숫자 5의 세 부분: a. IoT 커넥터는 FHIR 서비스에서 환자 리소스를 요청합니다. b. FHIR 서비스는 환자 리소스를 IoT 커넥터로 다시 보냅니다. 다. IoT 환자 관찰은 FHIR 서비스에 기록됩니다.

**Machine Learning 및 AI 데이터 경로 – 6~11단계**

6. Azure Function(ML Input)으로 전송된 정규화된 그룹화되지 않은 데이터 스트림입니다.
7. Azure Function(ML Input)은 환자 리소스를 IoMT 페이로드와 병합하도록 요청합니다.
8. PHI가 있는 IoMT 페이로드는 Machine Learning 컴퓨팅 및 스토리지에 배포하기 위해 이벤트 허브로 전송됩니다.
9. PHI IoMT 페이로드는 더 긴 시간 동안 관찰을 채점하기 위해 Azure Data Lake Storage Gen 2로 전송됩니다.
10. PHI IoMT 페이로드는 창, 데이터 맞춤 및 데이터 채점을 위해 Azure Databricks 전송됩니다.
11. Azure Databricks 필요에 따라 데이터 레이크에서 더 많은 환자 데이터를 요청합니다. a. 또한 Azure Databricks 점수가 매기는 데이터의 복사본을 데이터 레이크로 보냅니다.

**알림 및 의료 조정 – 12-18단계**

**핫 경로**

12. Azure Databricks Azure Function(ML 출력)에 페이로드를 보냅니다.
13. RiskAssessment 및/또는 FHIR 서비스에 제출된 플래그 리소스입니다. a. 각 관찰 창에 대해 RiskAssessment 리소스가 FHIR 서비스에 제출됩니다. b. 위험 평가가 허용되는 범위를 벗어나는 관찰 기간의 경우 플래그 리소스도 FHIR 서비스에 제출해야 합니다.
14. 적절한 관리 팀으로 라우팅하기 위해 데이터 리포지토리로 전송된 채점된 데이터입니다. Azure SQL Server Power BI 기본 상호 작용으로 인해 이 디자인에 사용되는 데이터 리포지토리입니다.
15. Power BI 대시보드는 15분 이내에 위험 평가 출력으로 업데이트됩니다.

**웜 경로**

16. Power BI 데이터 새로 고침 일정에 따라 대시보드를 새로 고칩니다. 일반적으로 새로 고침 간격은 15분 이상입니다.
17. 현재 데이터로 Care Team 앱을 채웁습니다.
18. 의료 조직을 위한 Microsoft Teams 환자 앱을 통한 의료 조정.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 커넥터 및 Machine Learning 서비스 통합에 대해 배웠습니다. IoT 커넥터에 대한 개요는 다음을 참조하세요.

>[!div class="nextstepaction"]
>[IoT 커넥터 개요](iot-connector-overview.md)

(FHIR&#174;)는 HL7의 등록 상표이며 [HL7의](https://hl7.org/fhir/) 사용 권한으로 사용됩니다.
