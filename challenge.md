---
type: Challenge
title: GoGoManager
tags: []
---

# 🌄 Background

Is an app for helping managers manage their employees

---

## 🫡 Functional Requirement

---

### Authentication & Authorization

**Background:** 



Manager can register to the app and start manage their employee

**Contract:**

- POST /v1/auth

    Request Body:

    ```javascript
    {
      "email": "name@name.com", // should in email format
      "password": "asdfasdf", // string | minLength: 8 | maxLength: 32
      "action": "create|login" // string | enum
    }
    ```

    Response:

    - `200` Ok for existing user

        ```javascript
        {
          "email": "name@name.com", 
          "token": "asdfasdf" // use any token you want
        }
        ```

    - `201` Created for new user

        ```javascript
        {
          "email": "name@name.com", 
          "token": "asdfasdf" // use any token you want
        }
        ```

    - `400` Bad Request case:

        - Validation error

    - `404` Not Found case:

        - Email is not found if `action == 'login'`

    - `409` Conflict case:

        - Email is exist if `action == 'create'`

    - `500` Server Error

---

### Profile management 

**Background:** 

Manager can manage their information

**Contract:**

- GET /v1/user

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Response:

    - `200` Ok

        ```javascript
        {
          "email": "name@name.com", 
          "name": "",
          "userImageUri": "",
          "companyName": "",
          "companyImageUri": "",
        }
        ```

    - `401` Unauthorized for 

        - expired / invalid / missing request token

    - `500` Server Error

- PATCH /v1/user

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Request Body:

    ```javascript
    {
      "email": "name@name.com", // should in email format
      "name": "", // string | minLength 4 | maxLength 52
      "userImageUri": "", // string | should be an URI
      "companyName": "", // string | minLength 4 | maxLength 52
      "companyImageUri": "", // string | should be an URI
    }
    ```

    Response:

    - `200` Ok

        ```javascript
        {
          "email": "name@name.com", 
          "name": "",
          "userImageUri": "",
          "companyName": "",
          "companyImageUri": "",
        }
        ```

    - `400` Bad Request for:

        - Validation error

    - `409` Conflict for:

        - Email is used by another person

    - `401` Unauthorized for

        - Expired / invalid / missing request token

    - `500` Server Error

---

### File Upload 

**Background:** 

Manager can manage uploaded files

**Contract:**

- POST /v1/file

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Request Multipart Form-Data:

    | **key** | **value type**                                  |
    | :------ | :---------------------------------------------- |
    | flle    | file \| should be jpeg / jpg / png | max 100KiB |

    Response:

    - `200` Ok

        ```javascript
        {
          "uri": "name@name.com/file.jpg", // should be the URI from AWS S3
        }
        ```

    - `400` Bad Request case:

        - Validation error

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error

---

---

### Employees 

**Background:**

Manager can manage their employees

**Contract:**

- **POST /v1/employee**

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Request Body:

    ```javascript
    {
      "identityNumber": "", // string | minlength 5 | maxlength 33
      "name": "", // string | minlength 4 | maxlength 33
      "employeeImageUri": "", // string | should be an uri
      "gender": "male|female", // string | enum
      "departmentId": "", // string | should be a valid departmentId
    }
    ```

    Response:

    - `201` Created

        ```javascript
        {
          "identityNumber": "",
          "name": "",
          "employeeImageUri": "",
          "gender": "",
          "departmentId": "",
        }
        ```

    - `400` Bad Request case:

        - Validation error

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `409` Conflict for

        - identity number

    - `500` Server Error

- **GET /v1/employee**

    Request params (all optional)

    - `limit` & `offset` limit the output of the data

        - default `limit=5&offset=0`

        - value should be a number

        - invalid `limit` / `offset` value will using  the default value

    - `identityNumber` filter the result based on identity number

        - search should be searched from prefix (`123` should return information like `12333`)

        - Case insensitive

        - value should be a string

    - `name` filter the result based on name

        - search should be a prefix suffix (`abc` should return information like `aaabcddd`)

        - Case insensitive

        - value should be a string

    - `gender` filter the result based on gender

        - value should be a enum of `male` | `female`

        - invalid `gender`  will cause the search come up empty (`[]`)

    - `departmentId` filter the result based on the department

        - value should be a valid `departmentId`

        - invalid `departmentId` will cause the search come up empty (`[]`)

    Response:

    - `200` Ok

        ```javascript
        [
          {
            "identityNumber": "",
            "name": "",
            "employeeImageUri": "",
            "gender": "",
            "departmentId": "",
          }
        ]
        ```

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error



- **PATCH /v1/employee/:identityNumber**

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Request Body:

    ```javascript
    {
      "identityNumber": "", // string | minlength 5 | maxlength 33
      "name": "", // string | minlength 4 | maxlength 33
      "employeeImageUri": "", // string | should be an uri
      "gender": "male|female", // string | enum
      "departmentId": "", // string | should be a valid departmentId
    }
    ```

    Response:

    - `200` Ok

        ```javascript
        {
          "identityNumber": "",
          "name": "",
          "employeeImageUri": "",
          "gender": "",
          "departmentId": "",
        }
        ```

    - `400` Bad Request case:

        - Validation error

    - `404` Not Found case:

        - `identityNumber` is not found

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `409` Conflict for

        - identity number

    - `500` Server Error

- **DELETE /v1/employee/:identityNumber**

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Response:

    - `200` Ok deleted

    - `404` Not Found case:

        - `identityNumber` is not found

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error

---

### Departments 

**Background:**

Manager can manage department for their employee

**Contract:**

- **POST /v1/department**

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Request Body:

    ```javascript
    {
      "name": "", // string | minlength 4 | maxlength 33
    }
    ```

    Response:

    - `201` Created

        ```javascript
        {
          "departmentId": "", // use any id you want
          "name": ""
        }
        ```

    - `400` Bad Request case:

        - Validation error

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error

- **GET /v1/department**

    Request params (all optional)

    - `limit` & `offset` limit the output of the data

        - default `limit=5&offset=0`

        - value should be a number

        - invalid `limit` / `offset` value will using  the default value

    - `name` filter the result based on name

        - value should be a string

        - search should be a prefix suffix (`abc` should return information like `aaabcddd`)

        - Case insensitive

    Response:

    - `200` Ok

        ```javascript
        [
          {
            "departmentId": "",
            "name": ""
          }
        ]
        ```

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error



- **PATCH /v1/department/:departmentId**

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Request Body:

    ```javascript
    {
      "name": "", // string | minlength 4 | maxlength 33
    }
    ```

    Response:

    - `200` Ok

        ```javascript
        {
          "departmentId": "", // use any id you want
          "name": ""
        }
        ```

    - `400` Bad Request case:

        - Validation error

    - `404` Not Found case:

        - `identityNumber` is not found

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error

- **DELETE /v1/department/:departmentId**

    Header:

    | **key**       | **value**  |
    | :------------ | :--------- |
    | Authorization | bearer ... |

    Response:

    - `200` Ok deleted

    - `404` Not Found case:

        - `identityNumber` is not found

    - `409` Conflict for

        - Still contain employee

    - `401` Unauthorized for

        - expired / invalid / missing request token

    - `500` Server Error



---

---

Load test

## 👾 Non Functional Requirement

Features for the app maintainability

### Backend

---

- Server 

    - Available servers:

        | Name         | CPU Arch | vCPU | Memory (GiB) | CPU Credit |
        | :----------- | :------- | :--- | :----------- | :--------- |
        | `t2.nano`    | x86      | 1    | 0.5          | 0.2        |
        | `t2.micro`   | x86      | 1    | 1            | 0.5        |
        | `t4g.small`  | arm      | 2    | 2            | 1          |
        | `t4g.medium` | arm      | 2    | 4            | 2          |
        | `t4g.large`  | arm      | 2    | 8            | 5          |
        | `t4g.xlarge` | arm      | 4    | 16           | 10         |

    - For monolith

        - Development: Shared VM

        - Production: 1 CPU Credit

    - For microservice

        - Development: 3 CPU Credit

        - Production: 10 CPU Credit


    ⚠️ Development usage will be watched, any missuse (eg: abusing CPU / Network usage) will result a temporally ban on development environment ⚠️

---

---

- Database 

    - Available Database:

        - Postgres 17.2-R1

        - MySQL 8.4.39

        - MariaDB 11.4.4

    - Available Specs

        | Name         | vCPU | Memory (GiB) | DB Credit |
        | :----------- | :--- | :----------- | :-------- |
        | `t4g.micro`  | 2    | 1            | 0.5       |
        | `t4g.small`  | 2    | 2            | 1         |
        | `t4g.medium` | 2    | 4            | 2         |
        | `t4g.large`  | 2    | 8            | 5         |
        | `t4g.xlarge` | 4    | 16           | 10        |

    - For monolith

        - Development: Shared database

        - Production: 1 DB Credit

    - For microservice

        - Deveplopment: 1 DB Credit

        - Production: 10 DB Credit


    ⚠️ Development usage will be watched, any missuse (eg: abusing CPU / Network usage) will result a temporally ban on development environment ⚠️

---

---

---

- Load Balancers (for microservices) 

    - Available options: 

        - AWS Application Load Balancer (available early)

        - EC2 with `t4g.xlarge` (available near production test)

    - Can only pick one

---

---

- Service Discovery (for microservices) 

    - Available options: 

        - AWS Cloud Map

        - EC2 with `t4g.micro`

    - Can only pick one

---

---

- Upload 

    For uploading files, we use AWS S3, to use it, please use this environment variable 

    🫡 The variable will be given shortly and privately


    ⚠️ Do not upload the credential to the repository! Use `.gitignore` !  ⚠️

    ```bash
    # s3 to upload, all uploaded files will available just for only a day
    export AWS_ACCESS_KEY_ID=
    export AWS_SECRET_ACCESS_KEY=
    export AWS_S3_BUCKET_NAME=projectsprint-bucket-public-read
    export AWS_REGION=ap-southeast-1
    ```

📝 You can choose what service that you want to use by contacting admin

🫡 The development environment will be given at Thrusday, while production at Saturday

---

**Each team will be evaluated based on:**

- Compliance with the test scenario

    🫡 Test scenario will be given at Thrusday

- Load test result

- Making their codebase public and the collaborators are the same with the information inputted when registering



**Tips:**

- Work time is (very) limited, so work as fast as you can

- Cut the bureaucracies, avoid long meeting, abuse short meeting

- Drink coffee / tea or other form of caffeine

- There's time to be fancy at the technology, that time comes when we already master at the existing



