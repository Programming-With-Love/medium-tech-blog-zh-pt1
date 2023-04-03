# Mocking Server API On Kubernetes

> 原文：<https://medium.easyread.co/mocking-server-api-on-kubernetes-963bdeb40297?source=collection_archive---------1----------------------->

## Mocking API guna memudahkan proses software development secara simultan

![](img/e71e90dc2f3dd2a99a59255103be264a.png)

Photo by [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Hai Semua, Saya ini berbagi sebuah tools untuk memudahkan proses *software development* antara tim backend dan tim frontend agar bisa mengerjakan secara simultan

Dalam dunia software engineer kita mengenal mock-api dan juga api-contract keduanya adalah metode dalam merancang sebuah api agar bisa dikerjakan secara simultan, ada beberapa tools yang bisa digunakan untuk kedua metode tersebut dan pada artikel ini menggunakan tools *prism* sebagai mock-api server serta *swagger-ui* untuk api list nya.

Pada artikel ini saya ingin berbagi Install Mock-API server pada artikel ini menggunakan (Prism)[ [https://github.com/stoplightio/prism](https://github.com/stoplightio/prism) ] serta Swagger-UI untuk melihat list API kedua tools ini dijalankan diatas kubernetes

# Prerequisite

*   Kubernetes Cluster Version 1.23.x Or Newer
*   Kubectl Version 1.18.x Or Newer

# Mock-API

*   Siapkan API Document dengan format OpenAPI Document dengan nama api.yaml

```
 openapi: 3.0.0
      info:
        description: 'This is a sample server Petstore server.  You can find out more about     Swagger
          at [http://swagger.io](http://swagger.io) or on [irc.freenode.net, #swagger](http://swagger.io/irc/).      For
          this sample, you can use the api key `special-key` to test the authorization     filters.'
        version: 1.0.0
        title: Swagger Petstore
        termsOfService: http://swagger.io/terms/
        contact:
          email: apiteam@swagger.io
        license:
          name: Apache 2.0
          url: http://www.apache.org/licenses/LICENSE-2.0.html
      paths:
        "/no_auth/pets":
          get:
            tags:
            - pet
            summary: Get all pets
            description: ''
            operationId: getPets
            parameters:
            - in: query
              name: name
              schema:
                type: string
              description: ''
              required: true
            security: []
            responses:
              '200':
                description: ''
                content:
                  application/json:
                    schema:
                      type: array
                      items:
                        "$ref": "#/components/schemas/Pet"
                      example:
                      - name: a_name
                        photoUrls: []
              '401':
                description: Authorization information is missing or invalid.
          post:
            tags:
            - pet
            summary: Add a new pet to the store
            description: ''
            operationId: addPet
            requestBody:
              "$ref": "#/components/requestBodies/Pet"
            responses:
              '405':
                description: Invalid input
            security: []

          get:
            tags:
            - user
            summary: Get user by user name
            description: ''
            operationId: getUserByName
            parameters:
            - name: username
              in: path
              description: 'The name that needs to be fetched. Use user1 for testing. '
              required: true
              schema:
                type: string
            responses:
              '200':
                description: successful operation
                content:
                  application/json; charset=utf-8:
                    schema:
                      "$ref": "#/components/schemas/User"
              '400':
                description: Invalid username supplied
              '404':
                description: User not found
          put:
            tags:
            - user
            summary: Updated user
            description: This can only be done by the logged in user.
            operationId: updateUser
            parameters:
            - name: username
              in: path
              description: name that need to be updated
              required: true
              schema:
                type: string
            requestBody:
              content:
                application/json:
                  schema:
                    "$ref": "#/components/schemas/User"
              description: Updated user object
              required: true
            responses:
              '400':
                description: Invalid user supplied
              '404':
                description: User not found
          delete:
            tags:
            - user
            summary: Delete user
            description: This can only be done by the logged in user.
            operationId: deleteUser
            parameters:
            - name: username
              in: path
              description: The name that needs to be deleted
              required: true
              schema:
                type: string
            responses:
              '400':
                description: Invalid username supplied
              '404':
                description: User not found
      components:
        requestBodies:
          UserArray:
            content:
              application/json:
                schema:
                  type: array
                  items:
                    "$ref": "#/components/schemas/User"
            description: List of user object
            required: true
          Pet:
            content:
              application/json:
                schema:
                  "$ref": "#/components/schemas/Pet"
            description: Pet object that needs to be added to the store
            required: true
        securitySchemes:
          petstore_auth:
            type: oauth2
            flows:
              implicit:
                authorizationUrl: http://petstore.swagger.io/oauth/dialog
                scopes:
                  write:pets: modify pets in your account
                  read:pets: read your pets
          api_key:
            type: apiKey
            name: api_key
            in: header
        schemas:
            type: object
            properties:
              id:
                type: integer
                format: int64
              name:
                type: string
            xml:
              name: Tag
          Pet:
            type: object
            required:
            - name
            - photoUrls
            properties:
              id:
                type: integer
                format: int64
              category:
                "$ref": "#/components/schemas/Category"
              name:
                type: string
                example: doggie
              photoUrls:
                type: array
                xml:
                  name: photoUrl
                  wrapped: true
                items:
                  type: string
              tags:
                type: array
                xml:
                  name: tag
                  wrapped: true
                items:
                  "$ref": "#/components/schemas/Tag"
              status:
                type: string
                description: pet status in the store
                enum:
                - available
                - pending
                - sold
            xml:
              name: Pet
          ApiResponse:
            type: object
            properties:
              code:
                type: integer
                format: int32
              type:
                type: string
              message:
                type: string
```

*   Create Namespace mock-api dan deploy configmaps-nya

```
kubectl create ns mock-api 
```

```
kubectl create configmap open-api --from-file=api.yaml
```

*   Deploy Mock-api dengan menggunakan configuration berikut dan apply

```
---
 apiVersion: apps/v1
 kind: Deployment
 metadata:
   name: prism
   namespace: mock-api
 spec:
   selector:
     matchLabels:
       app.kubernetes.io/name: prism
       app.kubernetes.io/part-of: openapi-mocks
   template:
     metadata:
       labels:
         app.kubernetes.io/name: prism
         app.kubernetes.io/part-of: openapi-mocks
     spec:
       volumes:
         - name: openapi
           configMap:
             name: openapi
             items:
             - key: api.yaml
               path: api.yaml
       containers:
       - name: prism
         image: stoplight/prism:4
         volumeMounts:
           - name: openapi
             mountPath: /api-doc
         args:
           - mock
           - -h 
           - "0.0.0.0"
           - -d
           - "/api-doc/api.yaml"
---
 apiVersion: v1
 kind: Service
 metadata:
   name: prism
   namespace: mock-api
 spec:
   type: ClusterIP
   ports:
     - name: http
       protocol: TCP
       port: 80
       targetPort: 4010
   selector:
     app.kubernetes.io/name: prism
     app.kubernetes.io/part-of: openapi-mocks
```

```
kubectl apply -f deployment.yaml -n mock-api
```

# Swagger-UI

Untuk melihat seluruh list yang ada pada dokument api kita butuh dashboard/UI untuk menampilkannya pada artikel ini kita menggunakan swagger sebagai UI untuk melihat list api yang ada pada dokumen.

*   Swagger-UI menggunaka configmaps untuk api document yang sama dengan mock-apinya, selain itu juga swagger-ui membutuhkan configmaps juga untuk config url pada swagger-ui berikut adalah configmap-nya

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: swagger
  namespace: mock-api
data:
  swagger.json: |-
    [
        {"name": "Contoh API Document", 
        "url": "documentation/api.yaml"}
    ]
```

*   Deploy swagger-ui dengan configuration berikut

```
---
 apiVersion: apps/v1
 kind: Deployment
 metadata:
   name: swegger-ui
   namespace: mock-api
 spec:
   selector:
     matchLabels:
       app.kubernetes.io/name: swegger-ui
       app.kubernetes.io/part-of: openapi-mocks
   template:
     metadata:
       labels:
         app.kubernetes.io/name: swegger-ui
         app.kubernetes.io/part-of: openapi-mocks
     spec:
       volumes:
         - name: openapi
           configMap:
             name: openapi
             items:
             - key: api.yaml
               path: api.yaml
       containers:
       - name: swegger-ui
         image: swaggerapi/swagger-ui
         volumeMounts:
           - name: openapi
             mountPath: /usr/share/nginx/html/docs
         env: 
            - name: URLS
              valueFrom:
                configMapKeyRef:
                  name: swagger
                  key: swagger.json
---
 apiVersion: v1
 kind: Service
 metadata:
   name: swegger-ui
   namespace: mock-api
 spec:
   type: ClusterIP
   ports:
     - name: http
       protocol: TCP
       port: 80
       targetPort: 8080
   selector:
     app.kubernetes.io/name: swegger-ui
```

Sekian Tutorial dari saya, jika banyak kurangnya saya mohon maaf

Terima kasih