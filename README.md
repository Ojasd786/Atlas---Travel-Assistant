project/
├── backend/
│   ├── main.py                      # server entry
│   ├── requirements.txt
│   ├── data/                        # Member 1
│   │   ├── places.csv
│   │   ├── hotels.csv
│   │   ├── restaurants.csv
│   │   ├── transport.csv
│   │   └── city_metadata.json
│   └── src/
│        ├── ai_engine.py           # Member 4
│        ├── itinerary.py           # Member 4
│        ├── chat_ai.py             # Member 4
│        ├── data_loader.py         # Member 4
│        ├── interest_extractor.py  # Member 2
│        ├── intent_classifier.py   # Member 2
│        ├── query_cleaner.py       # Member 2
│        └── prompt_templates.py    # Member 2
│
└── frontend_flutter/
    ├── lib/
    │   ├── main.dart
    │   ├── screens/
    │   │    ├── home_screen.dart
    │   │    ├── itinerary_screen.dart
    │   │    └── chat_screen.dart
    │   └── services/
    │         └── api.dart
    └── pubspec.yaml

    STRICT JSON RESPONSE FORMAT (MUST FOLLOW EXACTLY)

The LLM must ALWAYS return:

{
 "itinerary": {
      "day1": [],
      "day2": [],
      ...
 },
 "approx_trip_cost": "",
 "hotels": [
      {"name": "", "rating": "", "area": ""}
 ],
 "tourist_places": [
      {"name": "", "ticket_price": "", "distance_km": ""}
 ],
 "taxi_prices": "",
 "restaurants": [
      {"name": "", "cuisine": "", "rating": ""}
 ]
}


Even in chat, return structured JSON if it’s travel-related.

7️⃣ LLM SYSTEM PROMPT (TRAVEL-ONLY RESTRICTION)

System instruction to AI:

You are a travel-planning AI assistant.
You must ONLY answer questions related to travel, trips, itineraries, hotels, restaurants, tourist places, ticket prices, transport, trip budgets, weather, food, or travel safety.
If the user asks ANYTHING outside travel, respond only with:
“I can only assist with travel-related questions.”

You MUST always respond in the following JSON format, fully filled and valid:

{(insert strict JSON above)}


  
🔵 MEMBER 1 — DATA CREATOR

Provide high-quality real data to assist the LLM.

📌 OUTPUT FILES

Inside backend/data/:

places.csv

hotels.csv

restaurants.csv

transport.csv

city_metadata.json

📌 CITIES TO COVER (5 ONLY)

Goa 

London

Agra

Zurich

Jaipur

📌 HOW TO COLLECT DATA

Sources:

Google Maps

TripAdvisor

Holidify

MakeMyTrip

Official tourism sites

📌 FILE DETAILS (EXTREMELY PRECISE)
✔ places.csv

Columns:

place_name
city
category
description
best_time
ticket_price (INR)


Row count:

8 to 12 places per city.

Examples of categories:

beach, fort, temple, market, museum

Description rule:

1–2 lines ONLY.

✔ hotels.csv

Columns:

hotel_name
city
rating (0–5)
price_range (low/medium/high)
area


Row count:

5 per city.

✔ restaurants.csv

Columns:

restaurant_name
city
cuisine
rating
price_level


Row count:

5 per city.

✔ transport.csv

Columns:

city
transport_type
avg_cost
availability


Row count:

3–4 per city.

✔ city_metadata.json

Each city MUST have:

{
 "city": "goa",
 "best_season": "Nov–Feb",
 "summary": "Short 2–3 line summary",
 "avg_daily_cost": "Estimated INR amount"
}

MEMBER 1 DONE HERE.
🔵 MEMBER 2 — NLP & PROMPT SPECIALIST

Works in: backend/src/

Creates 4 files:

1️⃣ query_cleaner.py

Purpose:

Remove emojis

Remove punctuation

Fix repeated letters

Convert to clean lowercase sentence

2️⃣ interest_extractor.py

Purpose:

Detect interests from text

Interests to support:

beach

nightlife

adventure

food

trekking

temple

nature

history

shopping

Output: List of matched interests.

3️⃣ intent_classifier.py

Purpose:
Detect the type of user request.

Possible intents:

trip_plan
hotel_suggestion
restaurant_suggestion
modify_itinerary
tourist_spots
ticket_prices
taxi_prices
weather_info
budget_estimate
general_travel_info
non_travel (fallback)


Use keyword matching.

4️⃣ prompt_templates.py

Contains TEXT templates for:

Itinerary generation

Hotel listing

Restaurant listing

Ticket prices

Taxi prices

General travel chat

AND must always include:

Travel-only restriction

JSON output requirement

Structured format


🔵 MEMBER 3 — FLUTTER UI DEVELOPER

Works in: frontend_flutter/lib/

SCREEN 1 — HOME SCREEN
UI ELEMENTS:

TextField: Enter city

Slider: Days (1–10)

Dropdown: Budget (low, medium, high)

Chips: Interests (beach, nature, adventure, food, nightlife, temples)

Button: "Generate Trip"

Loading indicator

Error message handling

UX RULES:

Do not allow empty city

Auto lowercase city before sending

Show loading spinner during API call

Navigate to itinerary screen on success

SCREEN 2 — ITINERARY SCREEN
MUST DISPLAY:

City name

Itinerary day-wise (card for each day)

Estimated trip cost

List of hotels → name + rating + area

List of tourist places → name + ticket_price

Taxi/cab price estimate

Restaurants → name + cuisine + rating

BUTTONS:

"Ask Assistant" → Opens chat screen

"Back"

SCREEN 3 — CHAT SCREEN
UI:

Scrollable chat list

User bubble (right)

AI bubble (left)

Text box

Send button

Loading indicator (3 dots)

Requirements:

Always send user message to /chat endpoint

Display AI structured JSON as text but prettified

API FILE — api.dart

Contains functions:

planTrip()

chatAI()


🔵 MEMBER 4 — BACKEND + GEN-AI INTEGRATOR

Works in:
backend/main.py
backend/src/ai_engine.py
backend/src/itinerary.py
backend/src/chat_ai.py
backend/src/data_loader.py

RESPONSIBILITIES (PURE LOGIC — NO CODE PROVIDED)
⭐ STEP 1 — Create FastAPI server

Endpoints:

/plan_trip

/chat

Requests must accept:

city

days

budget

user_message

⭐ STEP 2 — Connect Member 2 modules

Flow:

Clean query

Detect intent

Extract interests

Build prompt

⭐ STEP 3 — Load Member 1 CSV Data

Use in PROMPT when city exists.
If city not in CSV → fallback to AI-only mode.

⭐ STEP 4 — Build prompt with system instruction

ALWAYS include:

Travel-only restriction

Required JSON format

City info

Interests

CSV-supported data (places/hotels/etc)

⭐ STEP 5 — Call Nebius LLM

Process:

Make API call with prompt

Receive output text

Validate JSON

If malformed → retry prompt

⭐ STEP 6 — Return formatted JSON to Flutter

Exactly matching strict schema.

MEMBER 4 DONE.
9️⃣ FLUTTER UI TOTAL SPECIFICATION

(ALL SCREENS, ALL ELEMENTS — INCLUDED ABOVE)

🔟 BACKEND API — FULL SPECIFICATION
/plan_trip

Input:

city, days, budget, user_message


Output: strict JSON schema.

/chat

Input:

query


Output:

Either JSON (if travel)

Or rejection message (“I can only assist with travel-related questions.”)

1️⃣1️⃣ CSV + JSON FORMAT SPECIFICATIONS

(included earlier — must follow EXACTLY)

1️⃣2️⃣ PROMPT ENGINEERING RULES

ALWAYS include system prompt

ALWAYS instruct JSON format

ALWAYS restrict to travel

ALWAYS enforce structured response

ALWAYS provide fallback instructions for malformed output

NEVER let model generate code or non-travel info

1️⃣3️⃣ DATA-ASSISTED VS AI-ONLY MODE
If city IS IN 5-city dataset:

Add CSV data into prompt

Give AI real facts

More accurate plan

If city NOT in dataset:

Use AI-only general prompt

AI generates all details

No CSV used

1️⃣4️⃣ TESTING PLAN
Test cases:

City inside dataset

City outside dataset

Missing fields

Hotel request

Ticket price request

Modify itinerary request

Chat multiple rounds

Invalid city name

Non-travel question (should reject)

1️⃣5️⃣ RUN INSTRUCTIONS

(no code, just behavior)

Start backend

Start Flutter app

Fill city, days, budget

Wait for response

Check logs for JSON

Navigate to chat
