# Recipe Explorer CLI Flowchart Documentation

## Overview

This document provides a detailed explanation of the Recipe Explorer CLI application's workflow as visualized in the accompanying flowchart. The flowchart illustrates the application's architecture, control flow, and key asynchronous JavaScript patterns implemented throughout the codebase.


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
```
