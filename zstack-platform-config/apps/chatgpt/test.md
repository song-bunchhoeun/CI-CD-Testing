---
title: test
description: 
published: true
date: 2026-04-06T03:25:23.549Z
tags: 
editor: markdown
dateCreated: 2026-04-06T03:24:33.312Z
---

apiVersion: v1

kind: Namespace

metadata:

  name: dgc-chatgpt-miniapp

\---

apiVersion: apps/v1

kind: Deployment

metadata:

  name: chatgpt-miniapp-dev

  namespace: dgc-chatgpt-miniapp

  labels:

    app: dgc-chatgpt

spec:

  replicas: 1

  selector:

    matchLabels:

      app: dgc-chatgpt

  template:

    metadata:

      labels:

        app: dgc-chatgpt

    spec:

      imagePullSecrets:

        - name: ghcr-auth

      containers:

      - name: chatgpt-app-1

        image: "ghcr.io/skailabcambodia/skychat@sha256:3a011d6afc67aa8ad801cfb571ea5cf877422275ff52a2aefe389ee0211093b2"

        ports:

        - containerPort: 8080

        env:

          - name: SERVER\_PORT

            value: "8081"

          - name: SKYCHAT\_JWT\_SECRET

            value: "anything\_would\_be\_fine\_for\_dev"

          - name: REDIS\_HOST

            value: "10.10.120.38"

          - name: REDIS\_PORT

            value: "6379"

          - name: AI\_SYSTEM\_PROMPT

            value: "You are a helpful assistant bot. You do not have any opinions towards the government. Always be polite! Always response in Khmer or English(only if necessary) language and for Cambodia context. Use Headings(medium) when suitable for long content. Keep it professional all the time. Keep it concise. Never response in Thai language. You are hosted by Digital Government Committee (dgc.gov.kh) inside an app called 'DG SuperApp"

          - name: S3\_USE\_SSL

            value: "true"

          - name: S3\_ENDPOINT

            value: "oss-ap-southeast-1.aliyuncs.com"

          - name: S3\_REGION

            value: oss-ap-southeast-1

          - name: S3\_BUCKET\_SSL

            value: "dgchatgpt-dev"

          - name: AI\_AZURE\_RESOURCE\_NAME

            value: "mptc-dgc-aichat"

          - name: AI\_AZURE\_MODEL

            value: "gpt-4o"

          - name: AI\_GOOGLE\_MODEL

            value: "gemini-2.0-flash-001"

          - name: APP\_NAME

            value: "DG ChatGPT"

          - name: APP\_ICON

            value: "chatgpt"

          - name: AI\_MODEL\_PROVIDER

            value: "azure"

          - name: AI\_AZURE\_API\_KEY

            valueFrom:

              secretKeyRef:

                name: chatgpt-env-uat

                key: AI\_AZURE\_API\_KEY

          - name: AI\_GOOGLE\_API\_KEY

            valueFrom:

              secretKeyRef:

                name: chatgpt-env-uat

                key: AI\_GOOGLE\_API\_KEY

          - name: DB\_URI

            valueFrom:

              secretKeyRef:

                name: chatgpt-env-uat

                key: DB\_URI

          - name: S3\_ACCESS\_KEY

            valueFrom:

              secretKeyRef:

                name: chatgpt-env-uat

                key: S3\_ACCESS\_KEY

          - name: S3\_SECRET\_KEY

            valueFrom:

              secretKeyRef:

                name: chatgpt-env-uat

                key: S3\_SECRET\_KEY

\---

apiVersion: v1

kind: Secret

metadata:

  name: chatgpt-db-secret

  namespace: dgc-chatgpt-miniapp

type: Opaque

stringData:

  username: <username>

  password: <password>

\---

apiVersion: v1

kind: Service

metadata:

  name: chatgpt-dev-service-nodeport

  namespace: dgc-chatgpt-miniapp

spec:

  type: NodePort

  ports:

    - port: 8080

      targetPort: 8080

      nodePort: 30086

  selector:

    app: dgc-chatgpt

  
 

  sessionAffinity: ClientIP

  sessionAffinityConfig:

    clientIP:

      timeoutSeconds: 86400

  externalTrafficPolicy: Local

  internalTrafficPolicy: Cluster

  ipFamilyPolicy: SingleStack

  ipFamilies:

    - IPv4