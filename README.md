# travelagent

## Description
### Problem and Target Audience
This project solves the problem of planning the trip by yourself, when user considers various factors (check-in time, how to get to the airport, how to get from the airport, weather, which tourists attractions are opened and which are closed at the expected days of the trip). Right now this process requires manually working with lots of information having multiple tabs opened. This project automatize this process by searching the most convenient air flights and hotels/hostels according to user's prompt.

### Expected Proof-of-Concept features
1. Input: nonstructured user's prompt with trip dates. For example "Plan me a trip to Saint Petersburg from Moscow on weekends in the middle of April. My budget is 20000 rub."
2. System automatically extracts required information for trip planning: number of people, dates, budget, destination, departure, etc. If some information is missing, system will ask it from user.
3. System automatically calls API to search available air flights and hotels.
4. Brings together found information with automatically checking that no restrictions have been violated.
5. Demonstrates to user several possible options.
6. Once user has decided flights and hotel, the system researches and provides list of attractions that are available to visit with information about price and opening hours. 
7. Gives information about the weather and recommended clothes/accessories.

### Proof-of-Concept features that potentially could be added
1. System analyzes how to get from airport of arrival to the hotel.
2. Analyzes possible dining options based on user's budget.
3. Based on user's citizenship provides summarized information about country from the according embassy website.

### Out-of-Scope
1. The system does not book tickets or hotels, just provides information.
2. Does not support multiple trips, only round-trips.