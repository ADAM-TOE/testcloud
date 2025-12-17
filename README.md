# T20-Housing-Project
T20 Project Lauren, Adam, Bernard


Chatbot App Architecture: Service Interaction Plan

Project Overview
MarketLens enables users to ask open-ended questions like:

“What’s the median price in any City?”
“Compare walkability between Midtown and Downtown.”
“Which Houston neighborhood is best for young professionals?”
“Forecast rent in Museum District next year.”

The platform supports quick answers, visualizations, and forecasts for real estate data, with a focus on usability and extensibility.

Architecture
User → MarketLens.com → App Services → Web App → AI Foundry

Data Pipeline:
Real Estate CSV → Azure Blob Storage → Azure AI Search → Azure OpenAI → AI Foundry → Web App - Work in progress

Frontend: Custom web app (built with GitHub Copilot) - WIP
Backend: API endpoints for chat, search, analytics - WIP
AI Layer: Modular agents for query classification, clarification, data retrieval, reasoning, and analytics
Data Storage: Azure Blob Storage, Vector Database
Monitoring: Application Insights, Azure Monitor - WIP


Data Structure


USE CASE- Step 2: Initial Query
Jordan types:

“I’m looking for a place in Houston that’s walkable, safe, has good food, and is popular with young professionals. Should I rent or buy?”


Step 3: Query Classification

The Query Classification Agent analyzes the query and identifies it as a Lifestyle question with elements of Comparison and Clarification needed.


Step 4: Clarification Handling

The Clarification Agent detects ambiguity (Jordan hasn’t specified budget or rent/buy preference).
The chatbot asks:

“Great! What’s your budget range, and do you prefer renting or buying?”



Jordan replies:

“I’d like to rent, and my budget is around $1,800/month.”


Step 5: Data Retrieval

The Data Retrieval Agent searches the database for Houston neighborhoods matching:

Walk Score
Crime Grade
Food Scene Highlights
Young Professional Hotspot
1BR Avg Rent ≤ $1,800




Step 6: Reasoning & Response


The Reasoning Agent reviews the data and generates a response:

“Based on your preferences, Midtown and Montrose are excellent choices. Both are highly walkable, have vibrant food scenes, and are popular with young professionals. Midtown’s average rent is $1,655 and Montrose’s is $1,923. Midtown fits your budget best and offers great nightlife and transit access.”



The chatbot displays a visual comparison chart of Midtown vs. Montrose (walk score, rent, crime grade, food scene).



Step 7: Follow-Up & Exploration
Jordan asks:

“Can you show me more details about Midtown?”


The chatbot provides a detailed profile:

Walk Score: 86
Crime Grade: D
Food Scene: Nightlife, parks, transit
Notes: Walkable, bars, METRORail access




Step 8: Analytics & Monitoring

The Analytics Agent logs Jordan’s queries, clarifications, and engagement for future optimization.


Step 9: Decision & Next Steps
Jordan feels confident and requests a list of available apartments in Midtown.
MarketLens provides links to listings and offers to send a summary to Jordan’s email.

Step 10: Feedback
Jordan rates the chatbot experience as “Excellent” and leaves feedback for further improvements.

This journey demonstrates:

Seamless query handling and clarification
Data-driven recommendations
Visual insights
Personalization and follow-up
Analytics for continuous improvement



































NeighborhoodWalk Score1BR Avg RentCrime GradeMedian AgeMedian IncomeFood Scene HighlightsYoung Professional HotspotNotesMontrose861923D3465000Arts, dining, nightlifeYesLGBTQ+ friendly, vintage shops...........................
Additional datasets for other cities and attributes can be added in the same format.

Agent Workflow
MarketLens uses modular agents in AI Foundry:


Query Classification Agent

Detects query type (Market Trend, Forecast, Comparison, Lifestyle)
Routes workflow accordingly



Clarification Agent

Handles ambiguous queries
Asks follow-up questions for missing info



Data Retrieval Agent

Fetches relevant data from vector DB and structured sources



Reasoning & Response Agent

Integrates data and user context
Generates answers and chart-ready data



Analytics & Monitoring Agent

Tracks usage, performance, and errors. Tracks types of questions asked and frequency of questions asked



Agents are configured with detailed prompts and operate in sequence for each user query.

Technology Stack

Frontend: React/Angular (GitHub Copilot)
Backend: Node.js/Python/.NET (GitHub Copilot)
AI: Azure OpenAI GPT-4 Turbo (recommended), AI Foundry
Data: Azure Blob Storage, Azure AI Search, Vector Database
Monitoring: Application Insights, Azure Monitor
