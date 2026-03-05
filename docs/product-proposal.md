# Product Proposal

## Motivation
When planning a trip independently, a user faces a high cognitive load: it is necessary not only to find tickets and accommodation but also to manually synchronize a multitude of disparate factors. This includes aligning flight arrival times with hotel check-in times, checking weather forecasts, and accounting for the opening hours of tourist attractions on the specific dates of the trip. The applied goal of this project is to automate this process, replacing dozens of open browser tabs with a single unified agent that aggregates data from various sources, mathematically and logically validates it (budget, timings), and outputs a ready-to-use, consistent travel plan.

## Goal and Metrics
### Goal 
Create a conversational Proof-of-Concept (PoC) capable of extracting trip parameters from unstructured text, requesting missing data, finding optimal "flight + hotel" combinations within a specified budget, and enriching the final output with personalized local context (weather, open attractions).
### Agentic metrics
* **Tool Call Success Rate:** % of successful calls to external APIs without typing errors.
### Product metrics
* **Route Generation Success Rate:** % of sessions that end with the delivery of a consistent travel plan.
* **Budget Compliance Accuracy:** The deviation of the total sum ($Price_{flight} + Price_{hotel}$) from the budget declared by the user strictly < 0.
* 
### Potential Use Cases and Edge Cases
* **Expected Scenario:** The user writes, "Plan me a trip to Saint Petersburg from Moscow on weekends in the middle of April. My budget is 20000 rub." The agent parses the dates, searches for flights and a hotel, fits within 20000 rub, requests the weather forecast, and outputs the top 3 open places to visit that may interest user.
* **Refinement Scenario (Missing Info):** The user writes, "I want to go to Saint Petersburg." The agent does not trigger the search APIs but enters chat mode: "Where are you departing from, what are your exact dates, and what is your budget?"
* **Edge Case 1 (Budget):** The cheapest found flights cost 18000 rub, and there are no cheap hotels available. Failover: The Evaluator blocks the route and asks the user to either increase the budget or shift the travel dates.
* **Edge Case 2 (Logistics and Timing):** The flight arrives at 01:00 AM, but standard check-in is at 14:00. Failover: The Evaluator sends the task back to the hotel search step with a strict requirement to "search for 24/7 front desk or early check-in options."

## Constraints
* **Technical:** High response times are expected (p95 latency ~30–60 seconds). This is due to the sequential calling of multiple third-party APIs (flights, hotels, weather, places) and the need to verify each step through the LLM.
* **Operational (Budget):** Heavy reliance on the free tier limits of external APIs (e.g., SerpApi, OpenWeather).

## Agentic System Architecture
To solve this task, a multi-agent graph with end-to-end State management is required. The state is updated at each step.
1. **Extractor Module (LLM):** Analyzes the prompt. If data is missing, it routes back to the user to ask clarifying questions.
2. **Logistics Module (Tools):** Integrates with flight and hotel aggregator APIs.
3. **Evaluator Module (LLM-as-a-judge):** Validates the budget and timings.
4. **Enrichment Module (Tools):** After the logistics are approved, it connects to weather APIs and places/leisure APIs to find attractions with their opening hours and prices.
5. **Generator Module (LLM):** Compiles the final data into a human-readable format, adding clothing recommendations.

## Potential Data Flow: Delegation (LLM vs. Code)
### Delegated to LLM (Agent):
1. Intent understanding (parsing cities, dates, budget, number of people).
2. Decision making: determining if there is enough input data or if the user needs to be queried.
3. Logical evaluation of the route: understanding that a night arrival requires specific check-in conditions.
4. Formatting a readable final response based on raw JSON data.
5. Suggesting suitable clothing/accessories based on the retrieved weather forecast.
### NOT Delegated to LLM (Deterministic Code):
1. Executing the actual HTTP requests to external APIs (Tools).
2. Mathematical calculation of the total cost.
3. Data type validation to ensure the API receives 2026-04-15 instead of the word "tomorrow".
4. Filtering and truncating massive API responses down to the top-3 or top-5 variants before passing them into the LLM's context window.