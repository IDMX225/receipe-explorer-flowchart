# Recipe Explorer CLI Flowchart Documentation

## Overview

This document provides a detailed explanation of the Recipe Explorer CLI application's workflow as visualized in the accompanying flowchart. The flowchart illustrates the application's architecture, control flow, and key asynchronous JavaScript patterns implemented throughout the codebase.

To render the Mermaid diagram in this document, you need:
1. A Markdown viewer that supports Mermaid syntax (like GitHub, GitLab, VS Code with extensions)
2. Or a Mermaid live editor (https://mermaid.live/)

The complete Mermaid diagram is included in the "Mermaid Diagram" section below.

## Flowchart Components

The flowchart is divided into several main sections:

1. **Main Application Flow** - Initialization and menu presentation
2. **Feature Branches** - Six primary features of the application
3. **System Operations** - Cache, API, and File System operations

## Main Application Flow

The application begins with initialization:

```mermaid
A[Start Application] --> B[Initialize]
B --> B1[Initialize Cache]
B --> B2[Initialize Favorites]
B1 --> B3[Clear Expired Cache]
B2 --> B3
B3 --> C[Show Main Menu]
```mermaid

This sequence:
1. Initializes both cache and favorites systems in parallel
2. Clears any expired cache entries
3. Presents the main menu to the user

## Feature Branches

### 1. Search Recipes

```mermaid
D -->|1. Search recipes| E[Search Recipes]
E --> E1[Get search term from user]
E1 --> E2[Create cache key: search_term]
E2 --> E3{Is in cache?}
E3 -->|Yes| E4[Return cached recipes]
E3 -->|No| E5[Call API: searchMealsByName]
E5 --> E6[Save results to cache]
E6 --> E7[Display formatted recipe list]
E4 --> E7
```mermaid

This branch demonstrates:
- User input handling
- Cache key generation
- Conditional API calls
- Result caching and formatting

### 2. View Recipe Details

```mermaid
D -->|2. View recipe details| J[View Recipe Details]
J --> J1[Get recipe ID]
J1 --> J2[Create cache key: recipe_id]
J2 --> J3{Is in cache?}
J3 -->|Yes| J4[Return cached recipe]
J3 -->|No| J5[Call API: getMealById]
J5 --> J6[Save result to cache]
J6 --> J7[Display formatted recipe]
J4 --> J7
J7 --> J8[Check if in favorites]
```mermaid

This branch shows:
- Recipe information retrieval
- Favorites management
- Promise chaining (fetching related recipes)

### 3. Explore By First Letter

```mermaid
D -->|3. Explore by first letter| F[Explore By First Letter]
F --> F1[Get letters from user]
F1 --> F2[Get unique letters, limit to 3]
F2 --> F3[Create cache key: letters_abc]
...
F6 --> F7[Combine results, remove duplicates]
```mermaid

Notable features:
- Input validation and normalization
- `Promise.all` for parallel API requests
- Result deduplication

### 4. Search By Ingredient

```mermaid
D -->|4. Search by ingredient| G[Search By Ingredient]
...
G5 --> G6[Create fetch promise]
G6 --> G7[Create timeout promise 5s]
G7 --> G8[Use Promise.race]
```mermaid

This demonstrates:
- Timeout handling with `Promise.race`
- Graceful failure handling
- User-friendly timeout messages

### 5. View Favorites

```mermaid
D -->|5. View favorites| H[View Favorites]
H --> H1[Read favorites.json file]
H1 --> H2{Have favorites?}
```mermaid

Shows:
- File system operations
- Handling empty states

### 6. Discover Random

```mermaid
D -->|6. Discover random| I[Discover Random]
I --> I1[Create 3 parallel API calls]
I1 --> I2[Promise.race for first result]
```mermaid

This illustrates:
- `Promise.race` for performance optimization
- Parallel API requests

## System Operations

### Cache System Detail

```mermaid
subgraph Cache System Detail
CA[getCachedOrFetch] --> CA1[Create cache key]
CA1 --> CA2[Call getFromCache]
CA2 --> CA3{Cache hit?}
...
end
```mermaid

The cache system:
- Stores API responses locally
- Implements expiration (24-hour TTL)
- Provides fallback to API when needed

### API Request Detail

```mermaid
subgraph API Request Detail
AA[Make API Request] --> AA1[Construct URL with parameters]
AA1 --> AA2[Call fetch with URL]
AA2 --> AA3{Response OK?}
AA3 -->|Yes| AA4[Parse JSON response]
AA3 -->|No| AA5{Retry attempts left?}
...
end
```mermaid

API operations include:
- Retry logic for failed requests
- Response parsing
- Error handling

### File System Operations

```mermaid
subgraph File System Operations
FS1[Read File] --> FS2{File exists?}
FS2 -->|Yes| FS3[Parse JSON content]
FS2 -->|No| FS4[Create empty structure]
...
end
```mermaid

File operations show:
- File existence checking
- Directory creation
- Error handling

## Asynchronous JavaScript Patterns

The Recipe Explorer application demonstrates several key asynchronous JavaScript patterns:

1. **Promise Chaining** - Used in the "View Recipe Details" feature to fetch recipe details and then related recipes
2. **Promise.all** - Used in "Explore by First Letter" to fetch recipes for multiple letters in parallel
3. **Promise.race** - Used in "Discover Random" to get the first random recipe that resolves, and in "Search by Ingredient" to implement a timeout
4. **Caching Strategy** - All features check the cache before making API calls
5. **Error Handling** - API requests include retry logic and graceful degradation when failures occur
6. **Async/Await** - Used throughout the application to handle asynchronous operations in a more readable way

## Using the Flowchart

This flowchart serves multiple purposes:

1. **Documentation** - Understanding how the application works
2. **Development Guide** - Following the program flow during coding
3. **Debugging Aid** - Tracing execution paths when troubleshooting
4. **Learning Resource** - Demonstrating asynchronous JavaScript patterns

To view this flowchart, you can use any Mermaid-compatible renderer or include it in an HTML page with the Mermaid JavaScript library.

## Mermaid Diagram

To include the flowchart in a Markdown file, use the following code fence syntax:

```mermaid
flowchart TD
    %% Main Application Flow
    A[Start Application] --> B[Initialize]
    B --> B1[Initialize Cache]
    B --> B2[Initialize Favorites]
    B1 --> B3[Clear Expired Cache]
    B2 --> B3
    B3 --> C[Show Main Menu]

    C --> D{User Choice}

    %% 1. Search Recipes - Expanded
    D -->|1. Search recipes| E[Search Recipes]
    E --> E1[Get search term from user]
    E1 --> E2[Create cache key: search_term]
    E2 --> E3{Is in cache?}
    E3 -->|Yes| E4[Return cached recipes]
    E3 -->|No| E5[Call API: searchMealsByName]
    E5 --> E6[Save results to cache]
    E6 --> E7[Display formatted recipe list]
    E4 --> E7
    E7 --> E8{Found recipes?}
    E8 -->|No| C
    E8 -->|Yes| E9{View details?}
    E9 -->|No| C
    E9 -->|Yes| E10[Get recipe index from user]
    E10 --> J1

    %% 2. View Recipe Details - Expanded
    D -->|2. View recipe details| J[View Recipe Details]
    J --> J1[Get recipe ID]
    J1 --> J2[Create cache key: recipe_id]
    J2 --> J3{Is in cache?}
    J3 -->|Yes| J4[Return cached recipe]
    J3 -->|No| J5[Call API: getMealById]
    J5 --> J6[Save result to cache]
    J6 --> J7[Display formatted recipe]
    J4 --> J7
    J7 --> J8[Check if in favorites]
    J8 --> J9{In favorites?}
    J9 -->|Yes| J10{Remove from favorites?}
    J9 -->|No| J11{Add to favorites?}
    J10 -->|Yes| J12[Remove recipe from favorites.json]
    J10 -->|No| J13
    J11 -->|Yes| J14[Add recipe to favorites.json]
    J11 -->|No| J13
    J12 --> J13[Fetch related recipes]
    J14 --> J13
    J13 --> J15[Display related recipes list]
    J15 --> C

    %% Additional branches omitted for brevity...
    %% Include the rest of your flowchart here

    D -->|7. Exit| Z[Exit Application]
```mermaid


## Conclusion

The Recipe Explorer CLI application demonstrates a well-structured approach to building a command-line application with asynchronous JavaScript. The flowchart provides a visual representation of the program flow, making it easier to understand the application architecture and the interactions between different components.
