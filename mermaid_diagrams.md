# Diagramming with Mermaid

Let's have some fun building diagrams.

Let's see if the system is working. A simple test diagram:

```mermaid
flowchart LR
    a --> b & c--> d
```

## How to create and document a domain  model

```mermaid
classDiagram
    Title -- Genre
    Title *-- Season
    Title *-- Review
    Title o-- Actor    
    TV Show --|> Title
    Short --|> Title
    Film --|> Title
    
    Season *-- Episode 
```

#### Let's try something else

```mermaid
classDiagram
    Teacher -- Student
    Teacher o-- Class
    
    Student o-- Class
    
    Class  *-- Grade
```

