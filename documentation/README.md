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
    - MongoDB

- Messaging
    - Rabbitmq
    
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
    
- Deployment automation
    - Travis CI / Jenkins
    - Bamboo
    - Nexus

- Target platform 
    - Pivotal Cloud Foundry
    - Pivotal Big Data Suite

- Configuration:    
    - Git (boot configuration)
   
- Security 
    - CAS (Apereo)
    
- Clients
    - Node.js / Angular / SockJS
    - Vaadin / Vaadin TouchKit
    - iOS / Android / Windows Phone
    
- Protocol
    - HTTP(S) / REST
    - WebSockets

## Diagrams

### Context Diagram

@startuml

actor "Developer" <<Person>> as ADEV
actor "Expert" <<Person>> as AEXPR
actor "Subscriber" <<Person>> as ASUBSCR
actor "Workforce" <<Person>> as AWF
actor "Anonymous" <<Person>> as AU

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

rectangle "Bamboo" {
}
rectangle "Web Browser" as WB {
}
rectangle "Mobile" as MB {
}
rectangle "Portal" as Portal {
}

folder "Tokyo" as CQS {
    rectangle "Web Application \n[Container: Vaadin]\n\nSupply users with\n UI to \nsign up, \nlogin, \nwork with profile,\nquizzes and \nnotifications operations" as CWA {
    }
    rectangle "Commands Application\n [Container: Spring Boot Web,\nEmbedded Jetty,\n Spring Boot Security,\n CAS (Apereo)]\n\nREST commands API \nallows to manage business entities:\nusers, quiz contents and translations (templates), \nsubscriptions etc." as CCAPI {
    }
    rectangle "Reports Application\n [Container: Rabbitmq,\n HDFS,\n Apache Spark,\n Apache Hive,\n Spring XD]\n\n Allows to generate aggregated \n quizzes resulting reports and statistics\n and streaming notifications to other services." as CQAPI {
    }
    rectangle "Static content folder\n [Container: CDN]\n\n Stores images, videos etc." as CDN {
    }
    database "RDBMS [Container: MySQL]\n\n Stores users,\n quizzes content,\n assignments,\n results,\n subscriptions,\n feedback" as MySQL {
    }
    database "NoSQL [Container: MongoDB]\n\n Stores quizzes\n multilingual templates,\n notification templates  " as MongoDB {
    }
    database "NoSQL [Container: Hive]\n\n Stores aggregated quizzes\n passing results and\n feedback statistics" as Hive {
    }
    rectangle "Configuration [Container: Git]\n\n Stores bootstrap properties." as GIT {
    }
}

Bamboo -down-> CQS: Uses [HTTPS]
WB -down-> CWA: Uses [HTTPS]
MB -down-> CWA: Uses [WebSockets]
Portal -down-> CWA: Uses [HTTPS]

CWA -down-> CCAPI: Uses [HTTPS]
CCAPI -left-> CQAPI: Uses [TCP, port 5672] 

CCAPI -down-> MySQL : Uses [JDBC/SQL, port 3306]
CCAPI -down-> MongoDB : CRUD quiz templates\n [Mongo DB Wire Protocol, port 27017]
CWA -right-> CDN: Uses [HTTPS]
CWA -down-> CQAPI: CRUD data [HTTPS]
CQAPI -down-> Hive: CRUD data [JDBC/HQL, port 10000]
 

CWA -down-> GIT: Uses [HTTPS]
CCAPI -down-> GIT: Uses [HTTPS]

@enduml

### Component diagram

#### Web Application

@startuml

rectangle "Web Browser" as WB {
}
rectangle "Mobile" as MB {
}
rectangle "Portal" as Portal {
}
rectangle "Static content folder\n [Container: CDN]\n\n Stores images, videos etc." as CDN {
}
    
folder "Web Application" as CWA {
    rectangle "UI Component\n [Component: Vaadin Web UI, Vaadin TouchKit]\n\n Provides UI layout and events listeners, \nDefines entities for UI model" as WACUI {   
    }
    rectangle "Service Component\n [Component: Spring Beans, Spring Services]\n\n Communication layer\n with internal other services" as WACSC{
    }
    WB -down-> WACUI
    MB -down-> WACUI
    Portal -down-> WACUI
    WACUI -down-> CDN: References\n to resources: [HTTP]
    WACUI .down.> WACSC : maven: <dependency>
}

@enduml

#### Commands Application

@startuml 

rectangle "Configuration [Container: Git]\n\n Stores bootstrap properties." as GIT {
}

folder "Commands Application" as CCA {
    rectangle "Spring Web\n [Container: Spring Beans,\n Spring Web,\n Spring Security]\n\n Provides REST API to manage\n data repositories and business flows" as CCAAPI {
    }
    rectangle "Configuration\n [Component: Spring Beans,\n Spring Cloud Config]\n\n integrates GIT based\n configuration into application" as CCACFG {   
    }
    CCACFG -right-> GIT
    CCAAPI .down.> CCACFG : maven: <dependency>
}

@enduml