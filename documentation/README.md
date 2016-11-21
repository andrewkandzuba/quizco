# Project "Tokyo"

# Existing solution

## Technological stack

- Windows OS applications
- Client side deployment 
- SMTP communication channel

## Diagrams

### Context Diagram 

- *Designer* - the system is responsible for managing quizzes content
- *Runner* - the system is responsible for running quizzes and collecting results 
- *Developer* - the producer of Quiz.co Software
- *Expert* - responsible for refreshing of quizzes contents inside Quiz.co Software
- *Maintainer* - the maintainer of quizzes updates. 
- *Subscriber* - consumer of paid licence of Quiz.co Software 
- *Workforce* - the person is obligated to pass quizzes in Quiz.co Software

@startuml

actor "Developer" <<User>> as ADEV
actor "Expert" <<User>> as AEXPR
actor "Maintainer" <<User>> as ADEVOP
actor "Subscriber" <<User>> as ASUBSCR
actor "Workforce" <<User>> as AWF

component "Designer" <<Application>> as CQD
component "Runner" <<Application>> as CQR
component "MailServer" <<Software System>> as CMB

usecase (Provides new versions) as UC1
usecase (Updates quizzes content) as UC2
usecase (Exports updated quizzes) as UC3
usecase (Sends updated quizzes to subscribers) as UC4
usecase (Receives updated quizzes) as UC5
usecase (Add updated quizzes) as UC6
usecase (Passes quizzes) as UC7
usecase (Checks quiz results) as UC8

ADEV -down-> UC1
UC1 -left-> CQD
UC1 -> CQR
AEXPR -down-> UC2
UC2 -down-> CQD
UC3 -up-> CQD
ADEVOP -up-> UC3
ADEVOP -> UC4
UC4 -right-> CMB
ASUBSCR -down-> UC5
UC5 -down-> CMB
ASUBSCR -up-> UC6
UC6 -up-> CQR
UC7 -down-> CQR
AWF -down-> UC7
UC8 -left-> CQR
ASUBSCR -up-> UC8

@enduml

## Proposed solution

## Technological stack

- Architectural pattern
    - Microservices

- Development 
    - Java 8 SE
    - Spring Boot
    - Maven

- Persistence 
    - MySQL
    - Redis

- Reporting
    - Apache Hive
    - Apache Spark

- Testing
    - Docker
    - Cucumber 
    - Apache Selenium 
    - Xamarin

- Performance 
    - JMeter
    - YourKit
    - JMH 

- Source tracking 
    - Stash 
    
- Release automation
    - Travis CI / Jenkins
    - Bamboo
    - Nexus

- Target platform 
    - Cloud Foundry
    - Docker 
    - Zookeeper (service registry)
    - Git (boot configuration)
   
- Security 
    - CAS (Apereo)
    
- Client
    - Windows 
    - Web / Node.js / Angular / SockJS
    - Vaadin (admin console) 
    - iOS / Android / Windows Phone
    
- Protocol
    - HTTP(S) / REST
    - WebSockets

## Diagrams

### Context Diagram

@startuml

actor "Developer" <<User>> as ADEV
actor "Expert" <<User>> as AEXPR
actor "Subscriber" <<User>> as ASUBSCR
actor "Workforce" <<User>> as AWF
actor "Anonymous" <<User>> as AU

component "Tokyo" <<Software System>> as CQS

usecase (develops and deploys new versions) as UC1
usecase UC2 as " 
logins, 
manages profile, 
reviews and updates quizzes content"
usecase UC3 as " 
logins, 
manages profile, 
receives notifications, 
reviews available quizzes, 
assigns quiz, 
review results, 
reports feedback"
usecase UC4 as " 
logins, 
manages profile, 
receives quiz assignments,
passes quizzes"
usecase UC5 as (signs up)

ADEV -down-> UC1
UC1 -down-> CQS
AEXPR -down-> UC2
UC2 -down-> CQS
ASUBSCR -up-> UC3
UC3 -up-> CQS
AWF -up-> UC4
UC4 -up-> CQS
AU -down-> UC5
UC5 -down-> CQS


@enduml


### Container diagram

@startuml

actor "Anonymous" <<Person>> as AU
actor "Expert" <<Person>> as AEXPR
actor "Subscriber" <<Person>> as ASUBSCR
actor "Workforce" <<Person>> as AWF

folder "Tokyo" as CQS {
    package "Web" {
        interface HTTP as HTTPF
        interface HTTPS as HTTPSF
        HTTPF -down- [Spring Boot Security]
        [Spring Boot Security] -right-> [Spring Boot MVC]
        [Spring Boot MVC] -up-> HTTPSF
    }
    package "Commands API" {
        interface HTTPS as HTTPSA
        HTTPSA -down-> [Spring Boot Web]
        [Spring Boot Web] -left-> [Spring Data JPA]
    }
    database "MySQL" {
        folder "Users" {
        }
        folder "Roles" {
        }
        folder "Quizzes"{
        }
        folder "Subscriptions"{
        }
    }
}

AU -down-> HTTPF
AEXPR -down-> HTTPSF
ASUBSCR -down-> HTTPSF
AWF -down-> HTTPSF
[Spring Boot MVC] ..> HTTPSA : CRUD commands 

note left of HTTPF
    Allows anonymous users to sign up,
    browse unsecured content
end note
note right of HTTPSF
   Allows users to login,
   conduct secured operations allowed by the certain role
end note
note right of HTTPSA
   REST commands allows to manage business entities:
   users, quiz contents, subscriptions etc.
end note
 
 
@enduml