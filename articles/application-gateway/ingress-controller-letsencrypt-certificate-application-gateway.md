---
title: Application Gateway에서 LetsEncrypt.org 인증서 사용
description: 이 문서에서는 LetsEncrypt.org에서 인증서를 가져와 AKS 클러스터용 Application Gateway에서 인증서를 사용하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 7b4e6c00f6c50781b1916f2c7b0ae6d1008f1db2
ms.sourcegitcommit: 4298f71f502c70d601a7c58b28fc7bca62be3595
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133133394"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>AKS 클러스터용 Application Gateway에서 LetsEncrypt.org를 이용하여 인증서 사용

이 섹션에서는 AKS가 [LetsEncrypt.org](https://letsencrypt.org/)를 활용하고 도메인에 대한 TLS/SSL 인증서를 자동으로 가져오도록 구성합니다. 인증서가 AKS 클러스터에 대해 SSL/TLS 종료를 수행하는 Application Gateway에 설치됩니다. 여기서 설명하는 설정은 인증서 생성 및 관리를 자동화하는 [cert-manager](https://github.com/jetstack/cert-manager) Kubernetes 추가 항목을 사용합니다.

아래 단계를 따라 기존 AKS 클러스터에 [cert-manager](https://docs.cert-manager.io)를 설치합니다.

1. Helm 차트

    다음 스크립트를 실행하여 `cert-manager` Helm 차트를 설치합니다. 그러면

    - AKS에 새 `cert-manager` 네임스페이스를 만듭니다.
    - 인증서, 챌린지, 클러스터 발급자, 발급자, 주문과 같은 CRD 생성
    - [docs.cert-manager.io](https://cert-manager.io/docs/installation/compatibility/)에서 cert-manager 차트 설치

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    # Helm v3+
    helm install \
      cert-manager jetstack/cert-manager \
      --namespace cert-manager \
      --version v1.0.4 \
      # --set installCRDs=true

    # Helm v2
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v1.0.4 \
      jetstack/cert-manager \
      # --set installCRDs=true
      
    #To automatically install and manage the CRDs as part of your Helm release, 
    #   you must add the --set installCRDs=true flag to your Helm installation command.
    ```

2. ClusterIssuer 리소스

    `ClusterIssuer` 리소스를 만듭니다. `cert-manager`에서 서명된 인증서를 가져올 `Lets Encrypt` 인증 기관을 나타내는 데 필요합니다.

    네임스페이스가 아닌 `ClusterIssuer` 리소스를 사용하면 cert-manager는 여러 네임스페이스에서 사용할 수 있는 인증서를 발급합니다. `Let’s Encrypt`는 ACME 프로토콜을 사용하여 사용자가 특정 도메인 이름을 제어하는지 확인하고 인증서를 발급합니다. `ClusterIssuer` 속성 구성에 대한 자세한 내용은 [여기](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)를 참조하세요. `ClusterIssuer`는 테스트에 사용된 `Lets Encrypt` 스테이징 환경(브라우저/클라이언트 신뢰 저장소에 루트 인증서가 없음)을 사용하여 인증서를 발급하도록 `cert-manager`에게 지시합니다.

    아래 YAML의 기본 챌린지 형식은 `http01`입니다. 다른 챌린지는 [letsencrypt.org - 챌린지 형식](https://letsencrypt.org/docs/challenge-types/)에 설명되어 있습니다.

    > [!IMPORTANT] 
    > 아래 YAML의 `<YOUR.EMAIL@ADDRESS>` 업데이트

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. 앱 배포

    수신 리소스를 만들어 Lets Encrypt 인증서로 Application Gateway를 사용하여 `guestbook` 애플리케이션을 표시합니다.

    Application Gateway에 DNS 이름을 가진 공용 프런트 엔드 IP 구성이 있는지 확인합니다(기본 `azure.com` 도메인 사용 또는 `Azure DNS Zone` 서비스 프로비저닝, 사용자 고유의 사용자 지정 도메인 할당).
    주석 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`은 cert-manager에게 태그가 지정된 수신 리소스를 처리하도록 지시합니다.

    > [!IMPORTANT] 
    > 아래 YAML의 `<PLACEHOLDERS.COM>`을 사용자 고유의 도메인(또는 'kh-aks-ingress.westeurope.cloudapp.azure.com'과 같은 Application Gateway)으로 업데이트

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    몇 초 후 자동으로 발급된 **스테이징** `Lets Encrypt` 인증서를 사용하여 Application Gateway HTTPS URL을 통해 `guestbook` 서비스에 액세스할 수 있습니다.
    브라우저에서 잘못된 인증 기관에 대해 경고할 수 있습니다. `CN=Fake LE Intermediate X1`에서 스테이징 인증서를 발급합니다. 시스템이 예상대로 작동했으며 프로덕션 인증서를 사용할 준비가 되었음을 나타냅니다.

4. 프로덕션 인증서

    스테이징 인증서가 성공적으로 설정되면 프로덕션 ACME 서버로 전환할 수 있습니다.
    1. 수신 리소스에서 스테이징 주석을 `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`로 바꿉니다.
    1. 이전 단계에서 만든 기존 스테이징 `ClusterIssuer`를 삭제하고 위 ClusterIssuer YAML의 ACME 서버를 `https://acme-v02.api.letsencrypt.org/directory`로 교체하여 새 스테이징을 만듭니다.

5. 인증서 만료 및 갱신

    `Lets Encrypt` 인증서가 만료되기 전에 `cert-manager`가 Kubernetes 비밀 저장소에서 인증서를 자동으로 업데이트합니다. 이때 Application Gateway 수신 컨트롤러는 Application Gateway를 구성하는 데 사용 중인 수신 리소스에서 참조된 업데이트된 비밀을 적용합니다.
