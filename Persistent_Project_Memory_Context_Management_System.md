# Persistent Project Memory & Context Management System

## 1. Project Overview

The Persistent Project Memory & Context Management System is a database-backed knowledge and memory layer designed for AI software-engineering agents.

The central problem is that AI/LLM-based agents have limited context windows. They can only process a finite amount of information at once, and information from older interactions may no longer be available in the active conversation. This becomes a serious problem when an AI agent works on a software project over a long period of time.

A software project contains information that should persist across sessions, including:

- Project requirements
- Architectural decisions
- Technical constraints
- Development tasks
- Bugs and their causes
- Solutions and attempted solutions
- Code-related knowledge
- Developer discussions
- Important conversations
- Project-specific conventions
- AI agent actions
- Historical context

This project solves that problem by creating a relational database that acts as a persistent project memory.

Instead of treating the database as simple conversation storage, the system stores structured knowledge about the software project. An AI agent can retrieve relevant information from the database whenever it needs historical context and use that information when generating its next response or taking an action.

The project is intentionally designed around strong DBMS fundamentals. Oracle SQL is used as the primary database technology, while a simple frontend/dashboard provides a practical interface for demonstrating CRUD operations, searching, and displaying project knowledge.

---

# 2. Core Idea

The system can be summarized as:

    User
      |
      v
    AI Agent / Application
      |
      v
    Context Management Layer
      |
      v
    Oracle Database
      |
      +-------------------------------+
      |                               |
      v                               v
    Store New Knowledge          Retrieve Relevant Knowledge
      |                               |
      +---------------+---------------+
                      |
                      v
                Context Builder
                      |
                      v
                  AI / LLM

The database is the persistent memory of the project.

The AI agent does not need to remember every previous interaction inside its active context window. Instead, important information is persisted in the database and retrieved when required.

---

# 3. Problem Statement

Modern AI agents are powerful at generating and understanding information, but their useful context is limited by the model's context window.

For a short interaction this is usually acceptable. However, software engineering is a long-running process. A project may continue for weeks, months, or years, and developers repeatedly make decisions that the AI should remember.

For example, a developer may tell an AI agent:

> "This project uses PostgreSQL because the payment system requires transactional consistency. Do not introduce MongoDB unless the architecture is explicitly changed."

Several sessions later, the developer might ask:

> "Can we use MongoDB for the order service?"

If the previous decision is no longer in the active context window, the AI may give an inconsistent recommendation.

The proposed system solves this by persisting the decision:

    Type: ARCHITECTURE_DECISION
    Project: E-Commerce Platform
    Decision: Use PostgreSQL
    Reason: Payment processing requires transactional consistency
    Constraint: Do not introduce MongoDB without approval
    Tags: database, architecture, constraint

The AI can then retrieve this information before answering the new question.

---

# 4. Main Objective

The primary objective is to design and implement a relational database that provides persistent, searchable project memory for an AI software-engineering agent.

The system should:

1. Store project information permanently.
2. Store structured project knowledge and memories.
3. Maintain user/developer information.
4. Track AI agents and AI models.
5. Track sessions and interactions.
6. Store requirements and architectural decisions.
7. Store tasks, bugs, solutions, and project constraints.
8. Associate memories with tags.
9. Support relationships between related memories.
10. Provide search and retrieval functionality.
11. Support complete CRUD operations.
12. Demonstrate joins, nested queries, aggregate functions, and other SQL concepts.
13. Provide a frontend/admin dashboard for database interaction.
14. Provide an interface through which an AI agent can retrieve relevant project context.
15. Maintain enough structure to support high-concurrency context logging and retrieval.

---

# 5. Project Scope

The project has two closely related layers.

## 5.1 DBMS Layer

The DBMS layer is the academic/core implementation.

It focuses on:

- Relational database design
- ER modeling
- Entity relationships
- Primary keys
- Foreign keys
- Constraints
- Normalization
- SQL CRUD operations
- Joins
- Nested queries
- Aggregate functions
- String/date/numeric functions
- Views
- Indexes
- Transactions
- Concurrency considerations
- Oracle SQL

## 5.2 AI Context Layer

The AI context layer demonstrates how the database can be used by an AI agent.

It focuses on:

- Persistent project memory
- Context ingestion
- Context retrieval
- Relevant-memory search
- Context construction
- Session continuity
- Project-specific knowledge
- AI-agent interaction with the database

The AI layer should remain relatively simple for the initial implementation. The goal is to demonstrate how a relational database can provide persistent memory rather than attempting to build a complete autonomous coding agent.

---

# 6. Key Design Principle

The most important design decision is:

> The system should store knowledge about the software project, not merely store conversations.

A conversation is only one source of project knowledge.

The database should represent information such as:

    Requirement
    Architecture Decision
    Technical Constraint
    Task
    Bug
    Solution
    Code Knowledge
    Conversation
    Project Memory
    Agent Action

This makes the system useful even when the original conversation is no longer available.

---

# 7. Example Use Case

Consider a project called "E-Commerce Platform".

A developer tells the AI:

> "Authentication is implemented using JWT. Access tokens expire after 15 minutes and refresh tokens are used for longer sessions."

The system stores this as project knowledge.

Later, another session begins.

The developer asks:

> "How should I implement the login endpoint?"

The application searches the project memory and retrieves relevant information:

    Authentication:
    - JWT is used.
    - Access token expires after 15 minutes.
    - Refresh tokens are used.
    - Authentication-related logic already exists.

The retrieved information is supplied to the AI model as context.

The AI can then provide a response consistent with the existing project architecture.

---

# 8. Another Example: Architectural Decision Memory

Developer:

> "We decided to use Redis for caching because database queries became too expensive."

Stored information:

    Memory Type: ARCHITECTURE_DECISION
    Project: E-Commerce Platform
    Topic: Caching
    Decision: Use Redis
    Reason: Reduce expensive database queries
    Status: ACTIVE
    Tags: redis, caching, performance

Later:

> "Should I introduce another caching system?"

The retrieval layer can return the previous decision.

This allows the AI to explain the existing architectural direction instead of making an isolated recommendation.

---

# 9. Another Example: Task Continuity

A developer asks:

> "Fix the JWT authentication bug."

The system creates:

    Task ID: 104
    Title: Fix JWT authentication bug
    Status: IN_PROGRESS
    Priority: HIGH

During the session, the agent discovers:

    Bug:
    Token validation fails when the token is close to expiration.

It stores the finding.

Later, the developer returns and asks:

> "What were we doing yesterday?"

The system can retrieve:

    Task #104
    Previous session
    Identified bug
    Attempted solution
    Remaining work

The AI can continue from the previous state.

---

# 10. Functional Requirements

## 10.1 User Management

The system should allow:

- Creating users/developers
- Updating user information
- Viewing user information
- Deleting users when appropriate
- Associating developers with projects

Possible attributes:

    user_id
    name
    email
    role
    created_at

---

# 11. Project Management

The system should allow:

- Creating projects
- Updating projects
- Viewing projects
- Tracking project status
- Associating users with projects
- Associating AI agents with projects

Possible attributes:

    project_id
    project_name
    description
    status
    repository_url
    created_at
    updated_at

Possible project statuses:

    ACTIVE
    ARCHIVED
    COMPLETED
    ON_HOLD

---

# 12. AI Agent Management

The system should track the AI agents/models that interact with the database.

Possible attributes:

    agent_id
    agent_name
    model_name
    model_version
    provider
    created_at

The database may also track basic agent metrics such as:

    total_sessions
    total_context_retrievals
    total_memories_created
    average_response_time

These metrics are optional depending on implementation scope.

---

# 13. Session Management

A session represents a period of interaction between a user and an AI agent.

A session can be treated as a dependent/weak-style entity in the conceptual model if the project chooses to demonstrate weak entities.

Possible attributes:

    session_id
    project_id
    user_id
    agent_id
    start_time
    end_time
    status

Possible statuses:

    ACTIVE
    COMPLETED
    TERMINATED

A session can contain many memories/interactions.

---

# 14. Memory Management

Memory is the central entity of the system.

A memory represents a persistent piece of project knowledge.

Possible attributes:

    memory_id
    project_id
    session_id
    task_id
    memory_type
    title
    content
    importance
    source
    created_at
    updated_at

Possible memory types:

    REQUIREMENT
    ARCHITECTURE_DECISION
    CONSTRAINT
    BUG
    SOLUTION
    CODE_KNOWLEDGE
    CONVERSATION
    PROJECT_RULE
    GENERAL_KNOWLEDGE

Importance could be represented using:

    LOW
    MEDIUM
    HIGH
    CRITICAL

---

# 15. Requirement Management

Requirements should be represented separately when useful.

Possible attributes:

    requirement_id
    project_id
    title
    description
    priority
    status
    created_at
    updated_at

Possible priorities:

    LOW
    MEDIUM
    HIGH
    CRITICAL

Possible statuses:

    PROPOSED
    APPROVED
    IN_PROGRESS
    COMPLETED
    REJECTED

Requirements can also be connected to memories.

---

# 16. Architecture Decision Management

Architecture decisions are important persistent project knowledge.

Possible attributes:

    decision_id
    project_id
    title
    decision
    reason
    status
    created_by
    created_at
    updated_at

Example:

    Title:
    Database Selection

    Decision:
    Use PostgreSQL

    Reason:
    Payment processing requires strong transactional consistency.

    Status:
    ACTIVE

Architecture decisions can be linked to memories and tags.

---

# 17. Task Management

Tasks represent work that needs to be performed.

Possible attributes:

    task_id
    project_id
    assigned_user
    assigned_agent
    title
    description
    priority
    status
    created_at
    due_date
    completed_at

Possible statuses:

    TODO
    IN_PROGRESS
    BLOCKED
    COMPLETED
    CANCELLED

---

# 18. Bug Management

Bugs represent known software defects.

Possible attributes:

    bug_id
    project_id
    task_id
    title
    description
    severity
    status
    discovered_at
    resolved_at

Possible severities:

    LOW
    MEDIUM
    HIGH
    CRITICAL

Possible statuses:

    OPEN
    INVESTIGATING
    RESOLVED
    CLOSED

A bug may be associated with one or more memories and solutions.

---

# 19. Solution Management

Solutions represent proposed or successful approaches to problems.

Possible attributes:

    solution_id
    bug_id
    project_id
    description
    status
    created_by
    created_at

Possible statuses:

    PROPOSED
    TESTING
    VERIFIED
    REJECTED

This allows the database to preserve not only the bug but also the history of attempted solutions.

---

# 20. Tag Management

Tags provide a flexible way to categorize memories.

Example tags:

    database
    authentication
    jwt
    architecture
    performance
    redis
    api
    security
    frontend
    backend

Possible table:

    TAG
    ---
    tag_id
    tag_name

A memory can have multiple tags, and a tag can belong to multiple memories.

This creates the required many-to-many relationship.

---

# 21. Memory-Tag Relationship

The relationship should be represented using an associative entity:

    MEMORY_TAG
    ----------
    memory_id
    tag_id

Relationship:

    MEMORY M:N TAG

This is one of the important relationships in the ER diagram.

Example:

    Memory:
    "JWT authentication is used for API authentication."

    Tags:
    authentication
    jwt
    security
    api

---

# 22. Memory-to-Memory Relationships

Memories may also reference other memories.

This creates a self-referencing relationship.

Example:

    Requirement
        |
        v
    Architecture Decision
        |
        v
    Implementation Knowledge
        |
        v
    Bug
        |
        v
    Solution

Possible relationship table:

    MEMORY_RELATION
    ---------------
    source_memory_id
    target_memory_id
    relation_type

Possible relationship types:

    SUPPORTS
    CONTRADICTS
    DERIVED_FROM
    REPLACES
    RELATED_TO
    RESOLVES

This relationship is optional but strongly recommended because it makes the knowledge model more powerful.

---

# 23. Conversation/Interaction Storage

The system may maintain conversation records.

Possible attributes:

    interaction_id
    session_id
    user_message
    agent_response
    timestamp

However, not every conversation message needs to become permanent memory.

The system should distinguish between:

    Raw Interaction
    Persistent Memory

For example:

    User:
    "What is the weather today?"

does not necessarily need to become project memory.

But:

    User:
    "We will use JWT authentication for this project."

should potentially become persistent project knowledge.

This distinction keeps the database useful and prevents unnecessary storage.

---

# 24. Agent Action Logging

The system may record important actions taken by the AI agent.

Possible attributes:

    action_id
    agent_id
    project_id
    session_id
    action_type
    description
    timestamp
    status

Possible action types:

    CONTEXT_RETRIEVAL
    MEMORY_CREATION
    MEMORY_UPDATE
    TASK_CREATION
    BUG_CREATION
    SEARCH
    OTHER

This creates an audit trail for agent behavior.

---

# 25. Suggested Core Entity Set

The initial database should aim for the following entities:

    USER
    PROJECT
    PROJECT_USER
    AGENT
    PROJECT_AGENT
    SESSION
    MEMORY
    MEMORY_TAG
    TAG
    MEMORY_RELATION
    REQUIREMENT
    ARCHITECTURE_DECISION
    TASK
    BUG
    SOLUTION
    INTERACTION
    AGENT_ACTION

Not every entity has to be implemented if the project scope becomes too large. The minimum viable schema should prioritize:

    USER
    PROJECT
    AGENT
    SESSION
    MEMORY
    TAG
    MEMORY_TAG
    TASK
    REQUIREMENT
    ARCHITECTURE_DECISION

The remaining entities can be added as the implementation develops.

---

# 26. Proposed ER Relationships

The conceptual model should contain relationships such as:

    USER 1:M SESSION

    PROJECT 1:M SESSION

    AGENT 1:M SESSION

    PROJECT 1:M MEMORY

    SESSION 1:M MEMORY

    PROJECT 1:M TASK

    PROJECT 1:M REQUIREMENT

    PROJECT 1:M ARCHITECTURE_DECISION

    MEMORY M:N TAG

    MEMORY M:N MEMORY

    TASK 1:M MEMORY

    TASK 1:M BUG

    BUG 1:M SOLUTION

    SESSION 1:M INTERACTION

    AGENT 1:M AGENT_ACTION

Some relationships may be adjusted during normalization and implementation.

---

# 27. ER Diagram Requirements

The ER diagram should clearly demonstrate:

- Strong entities
- Primary keys
- Foreign keys
- One-to-one or one-to-many relationships where appropriate
- Many-to-many relationships
- Associative entities
- Weak/dependent entity concepts where appropriate
- Cardinality
- Participation constraints
- Attributes
- Relationships between project, users, agents, sessions, memories, tasks, and tags

A strong A3 diagram should visually emphasize:

    PROJECT
       |
       +---- USER
       |
       +---- AGENT
       |
       +---- SESSION
       |
       +---- MEMORY
                |
                +---- TAG
                |
                +---- MEMORY_RELATION
                |
                +---- TASK

---

# 28. Three Main Implementation Modules

The project should be divided into three major modules.

## Module 1: User, Project & Agent Management

Responsibilities:

- User CRUD
- Project CRUD
- User-project relationships
- AI agent CRUD
- Agent-project relationships
- Session creation
- Session lifecycle
- Basic agent metrics

Example queries:

    Create a project
    Update project status
    Delete a user
    Find all projects of a developer
    Find all active sessions
    Count sessions per agent
    Find the most active project

---

# 29. Module 2: Memory & Knowledge Ingestion

Responsibilities:

- Creating memories
- Updating memories
- Deleting memories
- Creating tags
- Assigning tags
- Creating requirements
- Creating architecture decisions
- Creating tasks
- Recording bugs
- Recording solutions
- Creating relationships between memories
- Applying constraints

Example workflow:

    User/Agent
        |
        v
    New project knowledge
        |
        v
    Classify memory
        |
        v
    Store Memory
        |
        +----> Add Tags
        |
        +----> Link Task
        |
        +----> Link Session
        |
        +----> Link Related Memory

---

# 30. Module 3: Context Retrieval

Responsibilities:

- Search project memory
- Search by keywords
- Search by tags
- Search by memory type
- Search by project
- Search by task
- Search by date
- Search by importance
- Retrieve related memories
- Retrieve previous architectural decisions
- Retrieve relevant requirements
- Retrieve unresolved bugs
- Construct context for the AI agent

Example query:

    "authentication architecture"

The system should retrieve relevant records such as:

    JWT authentication decision
    Authentication requirement
    Authentication bug
    Previous authentication solution
    Related security constraints

---

# 31. Context Retrieval Pipeline

The basic retrieval pipeline should be:

    User Query
        |
        v
    Context Retrieval API
        |
        v
    Search Oracle Database
        |
        v
    Filter by Project
        |
        v
    Filter/Rank by Relevance
        |
        v
    Retrieve Related Knowledge
        |
        v
    Build Context
        |
        v
    Send Context to AI Model

For the first implementation, relevance can be implemented using SQL techniques such as:

- Keyword matching
- Tags
- Memory type
- Importance
- Recency
- Project association

A vector database or advanced semantic search is not required for the initial DBMS implementation.

---

# 32. Simple Relevance Strategy

A basic relevance score can be calculated using factors such as:

    + keyword match
    + matching tags
    + same project
    + memory importance
    + recent update
    + related task
    + related memory

For example:

    relevance_score =
        keyword_match_score
        + tag_match_score
        + importance_score
        + recency_score

The exact formula can be simplified for the academic implementation.

---

# 33. CRUD Requirements

The frontend and SQL implementation must demonstrate:

## INSERT

Examples:

- Insert user
- Insert project
- Insert memory
- Insert task
- Insert tag
- Insert requirement

## UPDATE

Examples:

- Update project status
- Update memory importance
- Update task status
- Update bug status
- Update architecture decision

## DELETE

Examples:

- Delete a tag association
- Delete a memory
- Delete a completed task
- Delete an obsolete requirement

Deletion must respect foreign-key constraints.

## SELECT

The project should demonstrate simple and advanced retrieval queries.

---

# 34. Required SQL Query Categories

Each team member/module should demonstrate their own queries.

Each module should contain at least:

- 1 INSERT
- 1 UPDATE
- 1 DELETE
- 5 SELECT queries

The SELECT queries should include a mixture of:

1. Basic SELECT
2. SELECT with WHERE
3. JOIN
4. Aggregate/function query
5. Nested/subquery
6. Complex multi-table query
7. Date/string/numeric functions where appropriate

The exact distribution should follow the course rubric.

---

# 35. Example SQL Queries

## Query 1: Basic SELECT

Find all active projects.

    SELECT *
    FROM PROJECT
    WHERE status = 'ACTIVE';

---

## Query 2: JOIN

Find memories and their associated tags.

    SELECT m.memory_id,
           m.title,
           t.tag_name
    FROM MEMORY m
    JOIN MEMORY_TAG mt
      ON m.memory_id = mt.memory_id
    JOIN TAG t
      ON mt.tag_id = t.tag_id;

---

## Query 3: Aggregate

Count memories per project.

    SELECT project_id,
           COUNT(*) AS memory_count
    FROM MEMORY
    GROUP BY project_id;

---

## Query 4: Nested Query

Find memories belonging to active projects.

    SELECT *
    FROM MEMORY
    WHERE project_id IN (
        SELECT project_id
        FROM PROJECT
        WHERE status = 'ACTIVE'
    );

---

## Query 5: HAVING

Find projects with more than 20 memories.

    SELECT project_id,
           COUNT(*) AS memory_count
    FROM MEMORY
    GROUP BY project_id
    HAVING COUNT(*) > 20;

---

## Query 6: Multiple JOIN

Find unresolved bugs and their associated project and task.

    SELECT p.project_name,
           t.title AS task_title,
           b.title AS bug_title,
           b.status
    FROM PROJECT p
    JOIN TASK t
      ON p.project_id = t.project_id
    JOIN BUG b
      ON t.task_id = b.task_id
    WHERE b.status <> 'CLOSED';

---

## Query 7: Function Query

Find the average number of memories per session.

    SELECT AVG(memory_count)
    FROM (
        SELECT session_id,
               COUNT(*) AS memory_count
        FROM MEMORY
        GROUP BY session_id
    );

---

# 36. Frontend Requirements

The frontend should be simple but functional.

It does not need to look like a commercial application.

Its purpose is to demonstrate:

- Database connectivity
- Insert
- Update
- Delete
- Search
- Display
- Relationships
- Context retrieval

Recommended pages:

    Dashboard
    Projects
    Users
    Agents
    Sessions
    Memories
    Tags
    Requirements
    Architecture Decisions
    Tasks
    Bugs
    Search / Context Retrieval

---

# 37. Dashboard

The dashboard should provide a high-level view.

Example:

    Persistent Project Memory

    Projects: 5
    Active Sessions: 3
    Memories: 284
    Open Tasks: 17
    Open Bugs: 8
    Architecture Decisions: 32

    Recent Memories
    -----------------------------
    JWT authentication decision
    Redis caching decision
    Payment transaction requirement
    Login bug investigation

The numbers should come from SQL queries rather than hard-coded values.

---

# 38. Memory Management Interface

The memory page should allow the user to:

- Create a memory
- Edit a memory
- Delete a memory
- View memory details
- Assign tags
- Associate a project
- Associate a session
- Associate a task
- Set memory type
- Set importance
- Search memories

Example form:

    Project
    Session
    Memory Type
    Title
    Content
    Importance
    Tags

---

# 39. Context Search Interface

The most important AI-facing UI should be a search/context page.

Example:

    Search Project Knowledge

    [ authentication JWT login                  ] [Search]

    Results:

    1. JWT Authentication Decision
       Type: ARCHITECTURE_DECISION
       Importance: HIGH

    2. Authentication Requirement
       Type: REQUIREMENT
       Importance: HIGH

    3. JWT Expiration Bug
       Type: BUG
       Importance: MEDIUM

    4. Authentication Solution
       Type: SOLUTION
       Importance: HIGH

The user should be able to inspect the retrieved context.

---

# 40. AI Integration

The initial AI integration should be lightweight.

A backend service can expose functionality such as:

    POST /memory
    GET /memory/search
    GET /context
    POST /session
    POST /task
    PUT /memory/{id}
    DELETE /memory/{id}

The AI agent can call:

    GET /context?project_id=1&query=authentication

The backend then:

1. Receives the query.
2. Searches the Oracle database.
3. Retrieves relevant memories.
4. Retrieves related project information.
5. Builds a context package.
6. Returns the context to the AI agent.

---

# 41. Suggested Context Response

Example:

    {
        "project": "E-Commerce Platform",
        "query": "authentication",
        "context": [
            {
                "type": "ARCHITECTURE_DECISION",
                "content": "JWT is used for authentication."
            },
            {
                "type": "REQUIREMENT",
                "content": "Access tokens must expire after 15 minutes."
            },
            {
                "type": "BUG",
                "content": "Token validation failed near expiration."
            }
        ]
    }

The exact API structure can be modified based on the chosen backend framework.

---

# 42. Recommended Technology Stack

## Database

Oracle Database

Primary language:

    Oracle SQL / PL/SQL

## Backend

Choose one:

    Python + FastAPI
    Python + Flask
    Java + Spring Boot
    Node.js + Express

Python + FastAPI is recommended if simplicity and rapid development are priorities.

## Frontend

Choose one:

    React
    HTML/CSS/JavaScript
    Simple server-rendered frontend

For a DBMS academic project, a simple React or HTML/CSS/JavaScript frontend is sufficient.

## AI Layer

The AI integration can use an LLM API through the backend.

The exact model/provider is not part of the core DBMS requirement.

---

# 43. Recommended Project Architecture

A clean architecture could be:

    project-root/
    |
    +-- frontend/
    |   +-- components/
    |   +-- pages/
    |   +-- services/
    |
    +-- backend/
    |   +-- api/
    |   +-- services/
    |   +-- models/
    |   +-- database/
    |   +-- context/
    |
    +-- database/
    |   +-- schema/
    |   +-- tables/
    |   +-- constraints/
    |   +-- seed/
    |   +-- queries/
    |   +-- views/
    |   +-- indexes/
    |
    +-- docs/
    |   +-- requirements.md
    |   +-- er-diagram/
    |   +-- architecture.md
    |
    +-- README.md

The exact directory structure can be adjusted based on implementation choices.

---

# 44. Database Design Principles

The database should follow relational database design principles.

Important considerations:

- Atomic attributes
- Appropriate normalization
- Primary keys
- Foreign keys
- Referential integrity
- NOT NULL constraints
- UNIQUE constraints
- CHECK constraints
- Appropriate data types
- Avoiding unnecessary duplication
- Proper handling of many-to-many relationships
- Transaction consistency

The target should generally be at least Third Normal Form where practical.

---

# 45. Constraints

Examples of useful constraints:

## User

    email UNIQUE
    name NOT NULL

## Project

    project_name NOT NULL
    status CHECK (...)

## Memory

    project_id NOT NULL
    memory_type NOT NULL
    content NOT NULL

## Tag

    tag_name UNIQUE

## Task

    title NOT NULL
    status CHECK (...)

Foreign keys should enforce valid relationships.

---

# 46. Indexing Strategy

Since context retrieval is one of the central operations, indexing should be considered carefully.

Potential indexes:

    MEMORY(project_id)
    MEMORY(session_id)
    MEMORY(task_id)
    MEMORY(memory_type)
    MEMORY(created_at)
    MEMORY(importance)
    TAG(tag_name)
    TASK(project_id)
    TASK(status)
    BUG(project_id)
    SESSION(project_id)

Indexes should be justified rather than added blindly.

The project can demonstrate that indexing improves frequent retrieval operations.

---

# 47. High-Concurrency Consideration

The project should be presented as a high-concurrency context logging and retrieval system for DBMS evaluation.

The conceptual workload may contain:

    Multiple users
        +
    Multiple AI agents
        +
    Multiple sessions
        +
    Frequent memory inserts
        +
    Frequent context searches

Oracle's transaction and concurrency mechanisms should therefore be considered.

Important concepts:

- Transactions
- COMMIT
- ROLLBACK
- Isolation
- Locking
- Consistency
- Concurrent reads/writes

The project does not need to implement an advanced distributed concurrency system. It should demonstrate awareness of relational transaction behavior and safe concurrent database operations.

---

# 48. Transaction Example

When creating a memory and its tags:

    BEGIN TRANSACTION

    Insert memory

    Insert memory-tag relationships

    COMMIT

If any operation fails:

    ROLLBACK

The exact implementation depends on the backend/database driver.

---

# 49. Data Lifecycle

A typical memory lifecycle:

    Information appears
            |
            v
    AI/User identifies it as useful
            |
            v
       Memory created
            |
            v
       Tags assigned
            |
            v
      Related entities linked
            |
            v
        Memory retrieved
            |
            v
       Memory updated
            |
            v
      Memory archived/deleted

Not all memories should be deleted automatically. The system should distinguish between active and obsolete information where useful.

---

# 50. Memory Importance

Importance should influence retrieval.

Example:

    CRITICAL
    HIGH
    MEDIUM
    LOW

Examples:

CRITICAL:

    Security constraints
    Payment architecture decisions

HIGH:

    Major requirements
    Important architecture decisions

MEDIUM:

    Known bugs
    Development notes

LOW:

    Minor conversation details

This gives the retrieval system another dimension for prioritizing information.

---

# 51. Project Isolation

A critical rule is that context should be project-specific.

If a developer has:

    Project A
    Project B

A query for Project A should not accidentally return memories belonging to Project B.

Therefore, most context retrieval operations should include:

    project_id

as a filtering condition.

This is both a functional and data-integrity requirement.

---

# 52. Context Retrieval Rules

When retrieving context, prioritize:

1. Same project
2. Direct keyword/topic match
3. Matching tags
4. High-importance memories
5. Recent relevant memories
6. Related tasks
7. Related requirements
8. Related architecture decisions
9. Related memories

The retrieval system should avoid returning unrelated project information simply because it contains a similar keyword.

---

# 53. What Should Become a Memory?

Not every interaction should become persistent memory.

Good candidates:

- Explicit project decisions
- Requirements
- Constraints
- Architecture choices
- Bugs
- Solutions
- Important technical explanations
- Project-specific rules
- Important developer preferences related to the project
- Significant implementation discoveries

Poor candidates:

- Greetings
- Casual conversation
- Temporary questions
- Information unrelated to the project
- Duplicate information

This distinction prevents the database from becoming an uncontrolled dump of conversation history.

---

# 54. Future Enhancement: Semantic Retrieval

The first implementation can use SQL-based keyword and metadata retrieval.

A future version could add semantic retrieval.

Possible architecture:

    User Query
        |
        v
    Embedding Model
        |
        v
    Vector Representation
        |
        v
    Vector/Hybrid Search
        |
        v
    Relevant Memories

This should be considered a future enhancement rather than a requirement for the initial DBMS implementation.

The relational database remains the authoritative store for structured project information.

---

# 55. Future Enhancement: Automatic Memory Extraction

A future AI layer could automatically determine whether a conversation contains persistent knowledge.

Example:

    Conversation
        |
        v
    Memory Extraction Model
        |
        +---- No useful memory ---> discard
        |
        +---- Useful memory ------> classify
                                      |
                                      v
                                  Store Memory

The AI could classify:

    REQUIREMENT
    DECISION
    BUG
    SOLUTION
    CONSTRAINT
    CODE_KNOWLEDGE

---

# 56. Future Enhancement: Contradiction Detection

The system could detect conflicting project decisions.

Example:

Existing:

    "Use PostgreSQL."

New memory:

    "Move the project database to MongoDB."

The system could identify:

    Potential contradiction

and ask the developer to confirm whether the old decision should be replaced.

This is especially useful for long-running AI agents.

---

# 57. Future Enhancement: Memory Versioning

Instead of deleting old decisions, the system could preserve their history.

Example:

    Decision v1:
    PostgreSQL

    Decision v2:
    PostgreSQL + Redis

    Decision v3:
    PostgreSQL + Redis + Elasticsearch

This would allow the AI to understand how the architecture evolved over time.

---

# 58. Future Enhancement: Project Knowledge Graph

The relational model could eventually represent a project knowledge graph:

    Requirement
       |
       v
    Decision
       |
       v
    Implementation
       |
       v
    Bug
       |
       v
    Solution

This can help the AI understand relationships between different pieces of project knowledge.

The relational tables remain the underlying source of truth.

---

# 59. Future Enhancement: AI Task Agent

The database can eventually become the persistent state layer for an autonomous software-engineering agent.

The agent could:

    Read task
       |
       v
    Retrieve project context
       |
       v
    Analyze repository
       |
       v
    Propose solution
       |
       v
    Execute action
       |
       v
    Record result
       |
       v
    Update task/memory
       |
       v
    Continue later

This is outside the minimum DBMS implementation but demonstrates the long-term potential of the architecture.

---

# 60. Security Considerations

The system should consider:

- User authentication
- Authorization
- Project-level access control
- SQL injection prevention
- Input validation
- Secure API endpoints
- Protection of sensitive project information
- Safe database credentials
- Avoiding hard-coded secrets

The backend should use parameterized queries rather than constructing SQL directly from user input.

---

# 61. API Design

A possible API design:

## Projects

    GET    /projects
    GET    /projects/{id}
    POST   /projects
    PUT    /projects/{id}
    DELETE /projects/{id}

## Memories

    GET    /memories
    GET    /memories/{id}
    POST   /memories
    PUT    /memories/{id}
    DELETE /memories/{id}

## Search

    GET /memories/search
    GET /context

## Tasks

    GET    /tasks
    POST   /tasks
    PUT    /tasks/{id}
    DELETE /tasks/{id}

## Sessions

    GET    /sessions
    POST   /sessions
    PUT    /sessions/{id}

The API should be kept simple for the academic implementation.

---

# 62. Example Context API

Request:

    GET /context?project_id=1&query=authentication

Internal process:

    1. Validate project_id.
    2. Search MEMORY.
    3. Search TAG.
    4. Search REQUIREMENT.
    5. Search ARCHITECTURE_DECISION.
    6. Search TASK/BUG/SOLUTION where relevant.
    7. Find related memories.
    8. Rank results.
    9. Build context.
    10. Return structured context.

Response:

    {
      "project_id": 1,
      "query": "authentication",
      "results": [
        {
          "type": "ARCHITECTURE_DECISION",
          "title": "JWT Authentication",
          "content": "The project uses JWT..."
        },
        {
          "type": "REQUIREMENT",
          "title": "Token Expiration",
          "content": "Access tokens expire after 15 minutes..."
        }
      ]
    }

---

# 63. Suggested Development Phases

## Phase 1 — Requirement Analysis

Deliverables:

- Problem statement
- Objectives
- Functional requirements
- Non-functional requirements
- User roles
- Use cases
- Initial entity identification

This phase should be completed before database implementation.

---

# 64. Phase 2 — ER Modeling

Deliverables:

- Conceptual ER diagram
- Entities
- Attributes
- Primary keys
- Relationships
- Cardinality
- Weak/associative entities
- Many-to-many relationships

The ER diagram should be suitable for an A3 submission.

---

# 65. Phase 3 — Relational Schema

Convert the ER model into relational tables.

For every table define:

- Table name
- Column name
- Data type
- Primary key
- Foreign key
- Constraints

Example:

    MEMORY
    --------------------------------
    memory_id       NUMBER      PK
    project_id      NUMBER      FK
    session_id      NUMBER      FK
    memory_type     VARCHAR2
    title           VARCHAR2
    content         CLOB
    importance      VARCHAR2
    created_at      TIMESTAMP
    updated_at      TIMESTAMP

---

# 66. Phase 4 — Oracle Implementation

Create:

- Tables
- Primary keys
- Foreign keys
- Constraints
- Sequences/identity columns where appropriate
- Indexes
- Views
- Sample data

Use Oracle SQL/PLSQL according to the course requirements.

---

# 67. Phase 5 — Query Implementation

Each module should prepare its own SQL operations.

Minimum target:

    INSERT
    UPDATE
    DELETE
    5+ SELECT

Include:

- Joins
- Nested queries
- Aggregate functions
- GROUP BY
- HAVING
- ORDER BY
- Date functions
- String functions
- Numeric functions where appropriate

---

# 68. Phase 6 — Backend

Implement database connectivity.

Responsibilities:

- Database connection
- CRUD APIs
- Search APIs
- Context retrieval
- Validation
- Error handling
- Transaction handling

---

# 69. Phase 7 — Frontend

Build the dashboard.

Priority order:

1. Dashboard
2. Project management
3. Memory management
4. Task management
5. Search
6. Context retrieval
7. Additional entities

The UI should prioritize functionality over visual complexity.

---

# 70. Phase 8 — AI Integration

Add a simple AI integration.

Workflow:

    User Question
        |
        v
    Retrieve Context
        |
        v
    Oracle DB
        |
        v
    Context
        |
        v
    LLM
        |
        v
    Answer

The AI should receive project-specific context retrieved from the database.

---

# 71. Non-Functional Requirements

## Performance

Common memory retrieval operations should be efficient.

## Scalability

The schema should support many:

- Projects
- Users
- Sessions
- Memories
- Tags
- Tasks

## Reliability

Transactions should preserve data consistency.

## Maintainability

Backend, frontend, and database logic should be separated.

## Security

User input should be validated and SQL injection prevented.

## Data Integrity

Foreign keys and constraints should prevent invalid relationships.

---

# 72. Database Naming Conventions

Use consistent names.

Recommended:

    PROJECT
    USER_ACCOUNT
    AI_AGENT
    SESSION
    MEMORY
    TAG
    MEMORY_TAG
    TASK
    REQUIREMENT
    ARCHITECTURE_DECISION

Use:

    snake_case

for column names if that matches the team's Oracle convention.

Avoid inconsistent naming such as:

    projectID
    Project_Id
    PROJECTID

Choose one convention and use it everywhere.

---

# 73. Important Implementation Rule

Do not hard-code database results into the frontend.

For example, this is wrong:

    Memories: 284

if 284 is hard-coded.

The dashboard should execute a database query such as:

    SELECT COUNT(*)
    FROM MEMORY;

and display the returned result.

This demonstrates that the frontend is actually connected to the DBMS.

---

# 74. Important AI Integration Rule

The AI should not directly modify arbitrary database records.

Use a controlled backend layer:

    AI
     |
     v
    Backend API
     |
     v
    Validation
     |
     v
    Database

This allows the backend to:

- Validate requests
- Enforce permissions
- Apply business rules
- Manage transactions
- Prevent dangerous queries

---

# 75. Example End-to-End Scenario

A developer starts a project.

    Project:
    E-Commerce Platform

The developer adds a requirement:

    "The payment system must support transactional consistency."

The developer makes an architecture decision:

    "Use PostgreSQL."

The agent stores this information as project memory.

Later a bug occurs:

    "Payment transaction occasionally fails."

The agent creates:

    Bug
    ↓
    Investigation
    ↓
    Solution
    ↓
    Memory

Weeks later:

    Developer:
    "Why are we using PostgreSQL?"

The context retrieval system finds:

    Architecture Decision
    +
    Payment Requirement
    +
    Related Memories

The AI answers using the retrieved project context.

This demonstrates the full purpose of the system.

---

# 76. What the Database Is Actually Providing

The database provides four major capabilities:

## 1. Persistence

Knowledge survives after the AI conversation ends.

## 2. Structure

Knowledge is categorized into meaningful entities.

## 3. Retrieval

Relevant information can be queried efficiently.

## 4. Relationships

The system can understand how different pieces of project knowledge are connected.

This is more powerful than simply storing chat transcripts.

---

# 77. Minimum Viable Product

If implementation time becomes limited, build the following first:

    USER
    PROJECT
    AGENT
    SESSION
    MEMORY
    TAG
    MEMORY_TAG
    TASK
    REQUIREMENT
    ARCHITECTURE_DECISION

Then implement:

    User CRUD
    Project CRUD
    Memory CRUD
    Task CRUD
    Tag management
    Memory search
    Context retrieval
    Dashboard
    Oracle SQL queries

After this is stable, add:

    BUG
    SOLUTION
    INTERACTION
    AGENT_ACTION
    MEMORY_RELATION

---

# 78. Recommended Priority

The implementation priority should be:

    1. Database schema
    2. Constraints
    3. Sample data
    4. Required SQL queries
    5. Backend connectivity
    6. CRUD APIs
    7. Search
    8. Context retrieval
    9. Frontend dashboard
    10. AI integration
    11. Advanced features

Do not start by building the AI chatbot.

The database is the foundation.

---

# 79. Demo Flow

A strong final demonstration could follow this sequence:

## Step 1

Create a project.

## Step 2

Create a developer and associate them with the project.

## Step 3

Create an AI agent.

## Step 4

Start a session.

## Step 5

Add project requirements.

## Step 6

Add an architecture decision.

## Step 7

Add a memory describing the decision.

## Step 8

Add tags.

## Step 9

Create a task.

## Step 10

Create a bug related to the task.

## Step 11

Record a solution.

## Step 12

Search for:

    "authentication"

## Step 13

Show the SQL query retrieving relevant memories.

## Step 14

Show the frontend displaying the results.

## Step 15

Ask the AI a question requiring historical context.

## Step 16

Show that the backend retrieves the stored project knowledge before generating the answer.

This gives a clear demonstration of both DBMS functionality and the practical purpose of the system.

---

# 80. Final Project Pitch

The project can be described as:

> The Persistent Project Memory & Context Management System is a relational database-backed memory layer for AI software-engineering agents. It stores structured project knowledge such as requirements, architectural decisions, tasks, bugs, solutions, constraints, conversations, and technical memories. Instead of relying entirely on the limited context window of an AI model, the agent can retrieve relevant project knowledge from an Oracle database whenever it needs historical context. The system supports project and user management, session tracking, memory ingestion, tagging, context retrieval, CRUD operations, complex SQL queries, and a frontend dashboard. The database is designed around relational modeling, normalization, referential integrity, many-to-many relationships, dependent entities, indexing, transactions, and high-concurrency context logging and retrieval.

---

# 81. Instructions for AI Coding Assistants

If an AI coding assistant is given this README as project context, it should follow these principles.

## General

- Treat Oracle DB as the source of truth.
- Do not invent database data.
- Do not hard-code database results.
- Preserve relational integrity.
- Use parameterized queries.
- Keep frontend, backend, and database responsibilities separated.
- Prefer simple maintainable implementations.
- Do not add unnecessary technologies.
- Do not over-engineer the AI component.

## Database

- Follow the defined relational model.
- Use primary keys and foreign keys.
- Use appropriate constraints.
- Preserve normalization.
- Use transactions where multiple related writes must succeed together.
- Add indexes only when justified by retrieval patterns.

## Backend

- Validate all incoming data.
- Never construct SQL using raw user input.
- Use clear service and repository/database layers.
- Return meaningful error responses.
- Keep context retrieval logic separate from ordinary CRUD logic.

## Frontend

- All displayed project data should come from backend APIs.
- Forms should validate input.
- CRUD operations should provide success/error feedback.
- Search should call the backend rather than filtering only a hard-coded dataset.

## AI

- Treat the database as persistent project memory.
- Retrieve context before answering project-specific questions.
- Keep project context isolated using project_id.
- Do not assume information that has not been retrieved.
- Do not store every conversation message automatically.
- Important project knowledge should be explicitly classified before becoming persistent memory.

---

# 82. AI Coding Assistant Workflow

When implementing a feature, the coding assistant should follow:

    1. Understand the existing architecture.
    2. Inspect the relevant database schema.
    3. Identify affected entities.
    4. Identify relationships and constraints.
    5. Design the SQL/query changes.
    6. Implement backend logic.
    7. Implement frontend changes.
    8. Test CRUD behavior.
    9. Test database constraints.
    10. Test error handling.
    11. Verify that existing functionality still works.

Do not modify unrelated parts of the project without a clear reason.

---

# 83. AI Context Retrieval Contract

When the AI asks for context, the retrieval layer should return information in a structured form.

Minimum context metadata:

    project_id
    memory_id
    memory_type
    title
    content
    importance
    created_at

Additional useful metadata:

    tags
    task
    requirement
    architecture_decision
    related_memories
    source_session

The retrieval layer should make it possible to trace every piece of returned context back to a database record.

---

# 84. Data Traceability

Every important piece of AI-generated project context should ideally be traceable to its database source.

Example:

    AI statement:
    "The project uses JWT authentication."

Source:

    MEMORY_ID = 52
    TYPE = ARCHITECTURE_DECISION
    PROJECT_ID = 1

This is useful for debugging and future explainability features.

---

# 85. Avoid These Design Mistakes

Do not:

- Store everything as one giant conversation table.
- Put all project information into one MEMORY table without structure.
- Ignore foreign keys.
- Store tags as comma-separated strings.
- Store multiple values in one column.
- Hard-code dashboard statistics.
- Let the frontend directly execute arbitrary SQL.
- Let the AI execute arbitrary SQL.
- Mix database logic throughout frontend components.
- Build advanced vector search before the relational system works.
- Build a complex autonomous coding agent before completing the DBMS requirements.

The relational model should remain the foundation.

---

# 86. Success Criteria

The project is successful when:

1. The complete relational schema is implemented in Oracle.
2. The ER diagram accurately represents the database.
3. Data integrity constraints work correctly.
4. Multiple projects and users can be managed.
5. Sessions can be tracked.
6. Project knowledge can be stored.
7. Memories can be tagged.
8. Tasks and requirements can be stored.
9. Architectural decisions can be preserved.
10. Memories can be searched.
11. Relevant context can be retrieved.
12. CRUD operations work through the frontend.
13. Required SQL queries are demonstrated.
14. Complex and nested queries are included.
15. The system demonstrates relational DBMS concepts.
16. The AI layer can consume retrieved project context.
17. The project can demonstrate a realistic long-running software project scenario.

---

# 87. Long-Term Vision

The long-term vision is to create a persistent knowledge layer for AI agents that work on software projects for extended periods.

Instead of:

    AI Agent
       |
       v
    Current Conversation
       |
       v
    Response

the system evolves toward:

    AI Agent
       |
       +--------------------------+
       |                          |
       v                          v
    Current Context        Persistent Project Memory
                                  |
                     +------------+-------------+
                     |            |             |
                     v            v             v
                Requirements  Decisions      Tasks
                     |            |             |
                     +------------+-------------+
                                  |
                                  v
                            Project History
                                  |
                                  v
                           Context Retrieval
                                  |
                                  v
                              AI Agent

The result is an AI agent that can work with continuity instead of starting from an almost empty context every time a new session begins.

The relational database is therefore not merely a storage mechanism. It becomes the persistent state and knowledge layer of the AI software-engineering system.

---

# 88. Final Architecture Summary

    ┌─────────────────────────────────────────────────┐
    │                    USER                         │
    └──────────────────────┬──────────────────────────┘
                           │
                           ▼
    ┌─────────────────────────────────────────────────┐
    │                FRONTEND / DASHBOARD             │
    │                                                 │
    │ Projects | Memories | Tasks | Search | Context │
    └──────────────────────┬──────────────────────────┘
                           │
                           ▼
    ┌─────────────────────────────────────────────────┐
    │                  BACKEND API                    │
    │                                                 │
    │ CRUD | Validation | Search | Context Builder   │
    └──────────────┬──────────────────┬───────────────┘
                   │                  │
                   ▼                  ▼
    ┌────────────────────────┐  ┌─────────────────────┐
    │       ORACLE DB        │  │       LLM/AI        │
    │                        │  │                     │
    │ Users                  │  │ Generates responses │
    │ Projects               │  │ using retrieved     │
    │ Agents                 │  │ project context     │
    │ Sessions               │  │                     │
    │ Memories               │  └──────────┬──────────┘
    │ Tags                   │             │
    │ Requirements           │             │
    │ Decisions              │             │
    │ Tasks                  │             │
    │ Bugs                   │             │
    │ Solutions              │             │
    └────────────┬───────────┘             │
                 │                         │
                 └──────── Context ────────┘

The core principle remains:

    "The AI's active context is temporary.
     The project's knowledge should be persistent."

