# Diagramming with Mermaid

Let's have some fun building diagrams.

Let's see if the system is working. A simple test diagram:

```mermaid
flowchart LR
    a --> b & c--> d
```

## How to create and document a domain  model

Markup legend for a `classDiagram`:
- `--` represents a bidirectional association between two entities. Those entities can exist individually without knowing one about the other. Peer entities that might work together and hold references from one to the other.
- `-->` represents a directional association between two entities. Those entities can exist individually without knowing one about the other. But in this case just one entity can hold a reference to the other entity. The other one can never hold a reference to the other entity.
- `0--` represents an aggregation association. Entities linked by aggregation can still exist independently. But one is a parent which is linked to a child. If the parent entity is deleted, the child can still remain and live on independently.
- `*--` represents a composition association. Entities linked by composition have the closest relationship. Like aggregations, there is a parent. If the parent is deleted the child is also deleted. A child entity here makes no sense without a parent.
- `--|>` represents an inheritance association. It goes from the subclass to the template. 
- `:` can be used at the end of any of the above associations to add descriptions. Descriptions can be used on any flows through the diagram.
- `"1"`, `"1..*"`, `"0..*"` are ways of adding multiplicity to the relationships. Known as cardinality. Use them on each side to annotate the diagram, or just on one side of the relationship. An entity’s cardinality is defined on the opposite side of the relationship, which can be confusing to begin with.
- `%%`  is used to comment lines of the diagram. Nice!
- `link NODE_NAME LINK_URL _blank` to generate a clickable link on the name of the entity.

The above markup can be used to generate something like...


### Streamy Domain Model

```mermaid
classDiagram
    Title "1..*" -- "1..*" Genre: is associated with
    Title "1" *-- "0..*" Season: has
    Title "1" *-- "0..*" Review: has
    Title "0..*" o-- "1..*" Actor: features
        
    TV Show --|> Title: implements
    Short --|> Title: implements
    Film --|> Title: implements
    
    Viewer "0..*" --> "0..*" Title: watches
    
    Season "1" *-- "0..*" Review: has
    Season "1" *-- "1..*" Episode: contains
    
    Episode "1" *-- "0..*" Review: has
    
    link Title "http://www.example.com" _blank
```

Let's try another one...

### School Domain Model

```mermaid
classDiagram
    Teacher "1..*" -- "1..*" Student: is associated with
    Teacher "1" o-- "0..*" Class: teaches
    
    Student "1" o-- "1..*" Class: enrolled
    
    Class "1" *-- "0..*" Grade: has
    
    link Teacher "http://www.example.com/teacher" _blank
    link Student "http://www.example.com/student" _blank
    link Class "http://www.example.com/class" _blank
    link Grade "http://www.example.com/grade" _blank
```
## How to visualise application and user flows

Sequence diagrams are high-level views of a process, so try to keep the labels high-level too.

Markup legend for `sequenceDiagram`
- `autonumber` can be used to add numbers for the messages on the flow.
- `actor` defines an actor. An actor represents a human user.
- `participant` defines a participant. A participant represents a process. You can define aliases for participants like so `participant SUS as Sign Up Service`. When defining messages between participants, you can then use the shorter alias rather than its full name, which will still be rendered on the diagram.
- `->>` defines a synchronous interaction between two sequence diagram nodes which is a message. It is a request.
- `-->>` defines a reply message interaction. It is a response.
- `alt SOME_TAG_NAME` then `else TAG_NAME` and `end` to define branching logic in a diagram. You can use as many `else` as you need to define more branches.
- `--)` defines an asynchronous interaction between participants/actors.
- `activate PARTTICIPANT_NAME` with `deactivate PARTICIPANT_NAME` to define an activation that offers more insight at a glance on the diagram. Activations can also be defined by adding a `+` (activation) or `-` (deactiva- tion) sign to the ends of the arrows.
- `Note [Left|Right] of NODE_NAME` to add a note on the diagram for a specific node.
- `links` can be used to add an active dropdown for each actor or participant.

### User Signup Flow Diagram

```mermaid
sequenceDiagram
    autonumber
    
    actor Browser
    participant Sign Up Service
    participant User Service
    participant Kafka
    
    Browser ->> Sign Up Service: GET /sign_up
    activate Sign Up Service
    Sign Up Service -->> Browser: 200 OK (HTML page)
    deactivate Sign Up Service
    
    Browser ->>+ Sign Up Service: POST /sign_up
    Sign Up Service ->> Sign Up Service: Validate input
    
    alt invalid input
        Sign Up Service -->> Browser: Error
    else valid input
        Sign Up Service ->>+ User Service: POST /users
        User Service --) Kafka: User Created Event Published
        Note left of Kafka: other services take action based on this event
        User Service -->>- Sign Up Service: 201 Created (User)
        Sign Up Service -->>- Browser: 301 Redirect (Login Page)
    end
    
    links User Service: {"Repository": "https://www.example.com/repository"}
```

### How to Model Your Architecture

Markup legend for `flowchart [TB|TD|BT|RL|LR]`
- `TB` is top to bottom
- `TD` is top down
- `BT` is bottom to top
- `RL` is right to left
- `LR` is left to right
- `NODE_NAME["Label/Description"]` defines a node. A node is defined once and used everywhere in the diagram.
- `ParentNode-- "arrow label" -->ChildNode` defines a flow between two nodes. The arrow label is optional.
- `ParentNode --> |"arrow label"| ChildNode` can also be used to define a flow between two nodes. This is useful when you want to add a description to the flow. The description will be rendered on the diagram.
- use `classDef className styleProperties` to define a class. You can then use `class` to apply the class to a node. You can define as many classes as you need.
- use `class nodeId(s) className` to apply a class to a node. You can define multiple classes on a single node by separating them with a comma.

```mermaid
flowchart TD
    User["Premium Member
    [Person]
    A user of the website who has \n purchased a subscription"]
    
    LS["Listing Service
    [Software System]
    Serves web pages displaying title \n listing to the end user"]
    
    TS["Title Service
    [Software System]
    Provides an API to retrieve \n title information"]
    
    RS["Review Service
    [Software System]
    Provides an API to retrieve \n and submit reviews"]
    
    SS["Search Service
    [Software System]
    Provides an API to search \n for titles"]
   
    User -- "Views titles, searches titles \n and reviews titles using" --> LS
    LS -- "Retrieves title information from" --> TS
    LS -- "Retrieves from and submits reviews to" --> RS
    LS -- "Searches for titles using" --> SS
    
    classDef focusSystem fill:#1168bd,stroke:#0b4884,color:#ffffff
    classDef supportingSystem fill:#666,stroke:#0b4884,color:#ffffff
    classDef person fill:#08427b,stroke:#052e56,color:#ffffff
    
    class User person
    class LS focusSystem
    class TS,RS,SS supportingSystem
```

### How to Map You System's Containers

A container is a single deployable unit. A container diagram shows interactions between containers rather than systems. They sho everything deployed for that system to function.

```mermaid
flowchart TD
    User["Premium Member
    [Person]
    A user of the website who has \n purchased a subscription"]
    
    WA["Web Application
    [.NET Core MVC Application]
    Allows members to view and review titles \n from a web browser. Also exposes \n an API for the mobile app"]
    
    MA["Mobile Application
    [Xamarin Application]
    Allows members to view and review \n titles from their mobile devices"]
    
    User -- "Views titles, searches titles and reviews titles \n using [HTTPS]" --> WA
    User -- "Views titles, searches titles and reviews titles \n using [HTTPS]" --> MA
    
    classDef container fill:#1168bd,stroke:#0b4884, color:#ffffff
    classDef person fill:#08427b,stroke:#052e56,color:#ffffff
    
    class User person
    class WA,MA container
```