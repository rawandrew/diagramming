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
- `actor` defines an actor. An actor represents a human user.
- `participant` defines a participant. A participant represents a process. You can define aliases for participants like so `participant SUS as Sign Up Service`. When defining messages between participants, you can then use the shorter alias rather than its full name, which will still be rendered on the diagram.
- `->>` defines a synchronous interaction between two sequence diagram nodes which is a message. It is a request.
- `-->>` defines a reply message interaction. It is a response.
- `alt SOME_TAG_NAME` then `else TAG_NAME` and `end` to define branching logic in a diagram. You can use as many `else` as you need to define more branches.

### User Signup Flow Diagram

```mermaid
sequenceDiagram
    actor Browser
    participant Sign Up Service
    participant User Service
    participant Kafka
    
    Browser ->> Sign Up Service: GET /sign_up
    Sign Up Service -->> Browser: 200 OK (HTML page)
    
    Browser ->> Sign Up Service: POST /sign_up
    Sign Up Service ->> Sign Up Service: Validate input
    
    alt invalid input
        Sign Up Service -->> Browser: Error
    else valid input
        Sign Up Service ->> User Service: POST /users
        User Service -->> Sign Up Service: 201 Created (User)
        Sign Up Service -->> Browser: 301 Redirect (Login Page)
    end
```