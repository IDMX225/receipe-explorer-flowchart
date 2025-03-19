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

    %% 3. Explore By First Letter - Expanded
    D -->|3. Explore by first letter| F[Explore By First Letter]
    F --> F1[Get letters from user]
    F1 --> F2[Get unique letters, limit to 3]
    F2 --> F3[Create cache key: letters_abc]
    F3 --> F4{Is in cache?}
    F4 -->|Yes| F5[Return cached recipes]
    F4 -->|No| F6[Call API for each letter in parallel]
    F6 --> F7[Combine results, remove duplicates]
    F7 --> F8[Save to cache]
    F8 --> F9[Display formatted recipe list]
    F5 --> F9
    F9 --> F10{Found recipes?}
    F10 -->|No| C
    F10 -->|Yes| F11{View details?}
    F11 -->|No| C
    F11 -->|Yes| F12[Get recipe index from user]
    F12 --> J1

    %% 4. Search By Ingredient - Expanded
    D -->|4. Search by ingredient| G[Search By Ingredient]
    G --> G1[Get ingredient from user]
    G1 --> G2[Create cache key: ingredient_name]
    G2 --> G3{Is in cache?}
    G3 -->|Yes| G4[Return cached recipes]
    G3 -->|No| G5[Set up API request with 5s timeout]
    G5 --> G6[Create fetch promise]
    G6 --> G7[Create timeout promise 5s]
    G7 --> G8[Use Promise.race]
    G8 --> G9{Request timed out?}
    G9 -->|Yes| G10[Return timeout message]
    G9 -->|No| G11[Save results to cache]
    G11 --> G12[Display formatted recipe list]
    G4 --> G12
    G10 --> G12
    G12 --> G13{Found recipes?}
    G13 -->|No| C
    G13 -->|Yes| G14{View details?}
    G14 -->|No| C
    G14 -->|Yes| G15[Get recipe index from user]
    G15 --> J1

    %% 5. View Favorites - Expanded
    D -->|5. View favorites| H[View Favorites]
    H --> H1[Read favorites.json file]
    H1 --> H2{Have favorites?}
    H2 -->|No| H3[Display No favorites message]
    H2 -->|Yes| H4[Display formatted favorites list]
    H3 --> C
    H4 --> H5{View details?}
    H5 -->|No| C
    H5 -->|Yes| H6[Get recipe index from user]
    H6 --> J1

    %% 6. Discover Random - Expanded
    D -->|6. Discover random| I[Discover Random]
    I --> I1[Create 3 parallel API calls]
    I1 --> I2[Promise.race for first result]
    I2 --> I3[Display formatted recipe]
    I3 --> I4[Check if in favorites]
    I4 --> I5{In favorites?}
    I5 -->|Yes| I6{Remove from favorites?}
    I5 -->|No| I7{Add to favorites?}
    I6 -->|Yes| I8[Remove recipe from favorites.json]
    I6 -->|No| C
    I7 -->|Yes| I9[Add recipe to favorites.json]
    I7 -->|No| C
    I8 --> C
    I9 --> C

    D -->|7. Exit| Z[Exit Application]

    %% Cache Operations Subgraph - Expanded
    subgraph Cache System Detail
    CA[getCachedOrFetch] --> CA1[Create cache key]
    CA1 --> CA2[Call getFromCache]
    CA2 --> CA3{Cache hit?}
    CA3 -->|Yes| CA4[Return cached data]
    CA3 -->|No| CA5[Call fetch function]
    CA5 --> CA6[Call saveToCache]
    CA6 --> CA7[Return fresh data]
    CA4 --> CA8[End]
    CA7 --> CA8
    end

    %% API Operations Subgraph - Expanded
    subgraph API Request Detail
    AA[Make API Request] --> AA1[Construct URL with parameters]
    AA1 --> AA2[Call fetch with URL]
    AA2 --> AA3{Response OK?}
    AA3 -->|Yes| AA4[Parse JSON response]
    AA3 -->|No| AA5{Retry attempts left?}
    AA5 -->|Yes| AA6[Wait 1 second]
    AA5 -->|No| AA7[Return error or empty array]
    AA6 --> AA2
    AA4 --> AA8[Extract meals from response]
    AA8 --> AA9[Return meal data]
    AA7 --> AA10[End]
    AA9 --> AA10
    end

    %% File System Operations
    subgraph File System Operations
    FS1[Read File] --> FS2{File exists?}
    FS2 -->|Yes| FS3[Parse JSON content]
    FS2 -->|No| FS4[Create empty structure]
    FS3 --> FS5[Return data]
    FS4 --> FS6[Create directory if needed]
    FS6 --> FS7[Write empty structure to file]
    FS7 --> FS5

    FS10[Write File] --> FS11[Ensure directory exists]
    FS11 --> FS12[Serialize data to JSON]
    FS12 --> FS13[Write to file system]
    FS13 --> FS14[Return success]
    end
