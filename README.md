# PowerBI-Period-Slicer
This project was created to fulfill a user request: "I want a slicer to select custom time periods (last month, last 3 months, 6, 12, or 24 months, etc.)." While this might not be the most efficient solution for models in Import or Mixed mode, it works very well in DirectQuery mode, especially when you're limited to DAX-only calculated columns.
________________________________________
🧩 Solution Overview
1. Create a Custom Period Table in DAX
A calculated table is created using DATATABLE to define the visible period options for the slicer:
PERIOD_SLICER = 
DATATABLE(
    "PERIODOS", STRING,      -- Label shown in the slicer
    "MATCH", INTEGER,        -- Key used for matching with the date table
    "ORDER", INTEGER,        -- Sort order in the slicer
    {
        {"Mes en curso", 1, 1},
        {"2 últ. meses", 1, 2},
        {"2 últ. meses", 2, 2},
        {"3 últ. meses", 1, 3},
        {"3 últ. meses", 2, 3},
        {"3 últ. meses", 3, 3},
        {"6 últ. meses", 1, 4},
        {"6 últ. meses", 2, 4},
        {"6 últ. meses", 3, 4},
        {"6 últ. meses", 6, 4},
        {"12 últ. meses", 1, 5},
        {"12 últ. meses", 2, 5},
        {"12 últ. meses", 3, 5},
        {"12 últ. meses", 6, 5},
        {"12 últ. meses", 12, 5},
        {"24 últ. meses", 1, 6},
        {"24 últ. meses", 2, 6},
        {"24 últ. meses", 3, 6},
        {"24 últ. meses", 6, 6},
        {"24 últ. meses", 12, 6},
        {"24 últ. meses", 24, 6},
        {"Todos", 1, 7},
        {"Todos", 2, 7},
        {"Todos", 3, 7},
        {"Todos", 6, 7},
        {"Todos", 12, 7},
        {"Todos", 24, 7},
        {"Todos", -1, 7}
    }
)
You can easily adapt this logic for other period combinations by modifying the values in the table.
________________________________________
2. Add a Matching Column in the Calendar Table
Create a calculated column in the CALENDARIO table to match against the MATCH column in PERIOD_SLICER.
CO_MATCH = 
SWITCH(
    TRUE,  
    DATEDIFF(CALENDARIO[Fecha], TODAY(), MONTH) < 1, 1,
    DATEDIFF(CALENDARIO[Fecha], TODAY(), MONTH) < 2, 2,
    DATEDIFF(CALENDARIO[Fecha], TODAY(), MONTH) < 3, 3, 
    DATEDIFF(CALENDARIO[Fecha], TODAY(), MONTH) < 6, 6, 
    DATEDIFF(CALENDARIO[Fecha], TODAY(), MONTH) < 12, 12, 
    DATEDIFF(CALENDARIO[Fecha], TODAY(), MONTH) < 24, 24, 
    -1
)
________________________________________
3. Create a Many-to-Many Relationship
Connect PERIOD_SLICER[MATCH] to CALENDARIO[CO_MATCH] using a many-to-many relationship and activate it.
 
________________________________________
4. Add the Slicer to the Report
•	Use PERIOD_SLICER[PERIODOS] in a slicer visual.
•	Enable single selection to ensure a single period is applied at a time.
•	Optionally, default the slicer to "Mes en curso" (or any other) for default filtering behavior.
 
________________________________________
✅ Benefits
•	Offers intuitive period filtering for end users.
•	Works in DirectQuery mode, where calculated tables and columns are required.
•	Easy to customize and extend for additional periods.
________________________________________

