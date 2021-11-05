---
title: '자습서: Azure Spring Cloud의 서비스 커넥터를 사용하여 Confluent Cloud의 Apache Kafka에 연결된 Spring Boot 앱 배포'
description: Azure Spring Cloud의 서비스 커넥터를 사용하여 Confluent Cloud의 Apache Kafka에 연결된 Spring Boot 앱을 만듭니다.
ms.devlang: java
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a407d164ec0214ddce6e1d8bf6254876d3642230
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449225"
---
# <a name="tutorial-deploy-a-spring-boot-app-connected-to-apache-kafka-on-confluent-cloud-with-service-connector-in-azure-spring-cloud"></a>자습서: Azure Spring Cloud의 서비스 커넥터를 사용하여 Confluent Cloud의 Apache Kafka에 연결된 Spring Boot 앱 배포

Azure Spring Cloud에서 실행되는 Spring Boot 애플리케이션에 대해 Confluent Cloud에서 Apache Kafka에 액세스하는 방법에 대해 알아봅니다. 이 자습서에서는 다음 작업을 완료합니다.

> [!div class="checklist"]
> * Confluent Cloud에서 Apache Kafka 만들기
> * Spring Cloud 애플리케이션 만들기
> * Spring Boot 앱 빌드 및 배포
> * 서비스 커넥터를 사용하여 Confluent Cloud에서 Azure Spring Cloud로 Apache Kafka 연결

## <a name="1-set-up-your-initial-environment"></a>1. 초기 환경 설정

1. 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Java 8 또는 11을 설치합니다.
3. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 이상을 설치합니다. 이를 통해 셸에서 명령을 실행하여 Azure 리소스를 프로비저닝하고 구성할 수 있습니다.

## <a name="2-clone-or-download-the-sample-app"></a>2. 샘플 앱 복제 또는 다운로드

샘플 리포지토리를 복제합니다.

```Bash
git clone https://github.com/Azure-Samples/serviceconnector-springcloud-confluent-springboot/
```

그런 다음, 해당 폴더로 이동합니다.

```Bash
cd serviceconnector-springcloud-confluent-springboot
```

## <a name="3-prepare-cloud-services"></a>3. 클라우드 서비스 준비

### <a name="31-create-an-instance-of-apache-kafka-for-confluent-cloud"></a>3.1 Confluent Cloud에 대한 Apache Kafka의 인스턴스 만들기

[이 지침](../partner-solutions/apache-kafka-confluent-cloud/create.md)에 따라 Confluent Cloud에 대한 Apache Kafka의 인스턴스를 만듭니다.

### <a name="32-create-kafka-cluster-and-schema-registry-on-confluent-cloud"></a>3.2 Confluent Cloud에서 Kafka 클러스터 및 스키마 레지스트리 만들기

1. Azure에서 제공하는 SSO로 Confluent Cloud에 로그인

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png" alt-text="Azure Portal을 사용하여 Confluent Cloud SSO 링크에 로그인" lightbox="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png":::

1. 기본 환경을 사용하거나 새 환경 만들기

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png" alt-text="Confluent Cloud에서 Apache Kafka의 클라우드 환경" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png":::

1. 다음 정보를 사용하여 Kafka 클러스터 만들기

    * 클러스터 형식: 표준
    * 지역/영역: eastus(버지니아), 단일 영역
    * 클러스터 이름: `cluster_1` 또는 다른 이름입니다.

1. **클러스터 개요** -> **클러스터 설정** 에서 kafka **부트스트랩 서버 URL** 을 가져오고 기록해 둡니다.

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png" alt-text="Confluent Cloud에서 Apache Kafka의 클러스터 설정" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png":::

1. **글로벌 액세스** 를 사용하여 **데이터 통합** -> **API 키** ->  **+ 키 추가** 에서 클러스터에 대한 API 키를 만듭니다. 키와 비밀을 기록해 둡니다.
1. **항목** ->  **+ 항목 추가** 에서 파티션이 6인 토픽 `test`을(를) 만들기
1. 기본 환경에서 **스키마 레지스트리** 탭을 클릭합니다. 스키마 레지스트리를 사용하도록 설정하고 **API 엔드포인트** 를 기록해 둡니다.
1. 스키마 레지스트리에 대한 API 키를 만듭니다. 키와 비밀을 기록해 둡니다.

### <a name="33-create-a-spring-cloud-instance"></a>3.3 Spring Cloud 인스턴트 만들기

Java의 [이 지침](../spring-cloud/quickstart.md)에 따라 Azure Spring Cloud의 인스턴스를 만듭니다. [서비스 커넥터를 지원하는 지역](concept-region-support.md)에 Spring Cloud 인스턴스가 만들어졌는지 확인 합니다.

## <a name="4-build-and-deploy-the-app"></a>4. 앱 빌드 및 배포

### <a name="41-build-the-sample-app-and-create-a-new-spring-app"></a>4.1 샘플 앱 빌드 및 새 Spring 앱 만들기

1. Azure에 로그인하고 구독을 선택합니다.

```azurecli
az login

az account set --subscription <Name or ID of your subscription>
```

1. Gradle을 사용하여 프로젝트 빌드

```Bash
./gradlew build
```

1. 퍼블릭 엔드포인트가 할당된 앱을 만듭니다. Spring Cloud 프로젝트를 생성할 때 Java 버전 11을 선택한 경우 --runtime-version=Java_11 스위치를 포함합니다.

```azurecli
az spring-cloud app create -n hellospring -s <service-instance-name> -g <your-resource-group-name> --assign-endpoint true
```

## <a name="42-create-service-connection-using-service-connector"></a>4.2 서비스 커넥터를 사용하여 서비스 연결 만들기

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

다음 명령을 실행하여 Confluent Cloud의 Apache Kafka를 Spring Cloud 앱에 연결합니다.

```azurecli
az spring-cloud connection create confluent-cloud -g <your-spring-cloud-resource-group> --service <your-spring-cloud-service> --app <your-spring-cloud-app> --deployment <your-spring-cloud-deployment> --bootstrap-server <kafka-bootstrap-server-url> --kafka-key <cluster-api-key> --kafka-secret <cluster-api-secret> --schema-registry <kafka-schema-registry-endpoint> --schema-key <registry-api-key> --schema-secret <registry-api-secret>
```

* Spring Cloud 인스턴스를 만든 리소스 그룹 이름을 사용하여 *\<your-resource-group-name>* 을(를) **바꿉니다**.
* kafka 부트스트랩 서버 URL(값은 `pkc-xxxx.eastus.azure.confluent.cloud:9092`와(과) 같아야 함)을 사용하여 *\<kafka-bootstrap-server-url>* **바꾸기**
* 클러스터 API 키 및 암호를 사용하여 *\<cluster-api-key>* 및 *\<cluster-api-secret>* 을(를) **바꿉니다**.
* kafka Schema Registry 엔드포인트(값은 `https://psrc-xxxx.westus2.azure.confluent.cloud`와(과) 같아야 함)를 사용하여 *\<kafka-schema-registry-endpoint>* **바꾸기**
* kafka Schema Registry API 키 및 비밀을 사용하여 *\<registry-api-key>* 및 *\<registry-api-secret>* 을(를) **바꿉니다**.

> [!NOTE]
> "구독이 Microsoft.ServiceLinker를 사용하도록 등록되어 있지 않습니다."라는 오류 메시지가 표시되면 `az provider register -n Microsoft.ServiceLinker`를 실행하여 Service Connector 리소스 공급자를 등록하고 연결 명령을 다시 실행합니다. 

#### <a name="portal"></a>[포털](#tab/Azure-portal)

**서비스 커넥터(미리 보기)** 를 클릭하고 다음 설정을 선택하거나 입력합니다.

| 설정      | 제안 값  | 설명                               |
| ------------ |  ------- | -------------------------------------------------- |
| **서비스 유형** | Confluent Cloud의 Apache Kafka | 대상 서비스 형식입니다. Confluent cloud에 Apache Kafka 없는 경우, 이 자습서의 이전 단계를 완료하세요. |
| **이름** | 생성된 고유 이름 | Spring Cloud와 대상 서비스 간의 연결을 식별하는 연결 이름  |
| **Kafka 부트스트랩 서버 URL** | Kafka 부트스트랩 서버 URL | 3\.2단계에서 이 값을 가져옵니다. |
| **클러스터 API 키** | 클러스터 API 키 |  |
| **클러스터 API 비밀** |  클러스터 API 비밀 |  |
| **스키마 레지스트리에 대한 연결 만들기**  | checked | 스키마 레지스트리에 대한 연결도 만들기 |
| **스키마 레지스트리 엔드포인트** | Kafka 스키마 레지스트리 엔드포인트  |  |
| **스키마 레지스트리 API 키** | Kafka 스키마 레지스트리 API 키 | |
| **스키마 레지스트리 API 비밀** | Kafka 스키마 레지스트리 API 비밀 | |

**검토 + 만들기** 를 선택하여 연결 설정을 검토합니다. 그런 다음 **만들기** 를 선택하여 서비스 연결 만들기를 시작합니다.

---

## <a name="43-deploy-the-jar-file-for-the-app"></a>4.3 앱에 대한 Jar 파일 배포

다음 명령을 실행하여 Spring Cloud 앱에 jar 파일(`build/libs/java-springboot-0.0.1-SNAPSHOT.jar`)을 업로드합니다.

```azurecli
az spring-cloud app deploy -n hellospring -s <service-instance-name> -g <your-resource-group-name>  --artifact-path build/libs/java-springboot-0.0.1-SNAPSHOT.jar
```

## <a name="5-validate-the-kafka-data-ingestion"></a>5. Kafka 데이터 수집의 유효성 검사

Azure Portal에서 Spring Cloud 앱의 엔드포인트로 이동하고 애플리케이션 URL을 클릭합니다. "10 개의 메시지가 항목 테스트에 생성되었습니다."가 표시됩니다.

그런 다음 Confluent Portal로 이동하면 항목의 페이지에 프로덕션 처리량이 표시됩니다.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png" alt-text="샘플 메트릭" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png":::

## <a name="next-steps"></a>다음 단계

서비스 커넥터에 대해 자세히 알아보려면 아래 나열된 자습서를 따르세요.

> [!div class="nextstepaction"]
> [서비스 커넥터 개념에 대해 알아보기](./concept-service-connector-internals.md)
