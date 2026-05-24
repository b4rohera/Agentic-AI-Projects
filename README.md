
PROBLEM STATEMENT
Slack Project Status Chatbot

1. Problem Statement
The Challenge
Teams track project status in spreadsheets, but retrieving specific information requires navigating to the sheet, scanning rows, and interpreting data manually. This creates friction: people default to pinging the PM directly, which doesn’t scale, introduces delay, and pulls PMs away from higher-value work.
What You Will Build
Design and build an intelligent project status assistant that gives any team member instant, conversational access to project status - directly from Slack, without opening a spreadsheet or pinging a PM.

The assistant should:
•	Listen for team messages in a designated Slack channel via webhook
•	Filter out bot replies, retries, and system events to process only human questions
•	Fetch live project data from a shared Google Sheets tracker
•	Use an LLM to interpret natural language queries and return structured, Slack-formatted answers
•	Handle both specific lookups (“What’s the status of Project X?”) and broad queries (“What’s at risk this week?”)
•	Gracefully handle errors and surface them to a dedicated channel for visibility
2. Architecture Overview
The diagram below shows the end-to-end flow of the chatbot. We will build this as an n8n workflow:
 
3. Data Model - Project Tracker
The Google Sheets tracker serves as the single source of truth for project data. Below is the schema and sample data you will use.
Schema
Field	Type	Description
Project Name	String	Name of the project
Owner	String	Team member responsible
Status	Enum	On Track | At Risk | Blocked | Done
Priority	Enum	High | Medium | Low
Phase	Enum	Planning | In Progress | Review | Complete
Due Date	Date	Target completion date
Last Updated	Date	Most recent status update
Notes	String	Free-text context on current state
Sample Data
Project Name	Owner	Status	Priority	Phase	Due Date
Website Redesign	Sarah Chen	On Track	High	In Progress	Apr 15, 2026
Mobile App Launch	James Rivera	At Risk	High	Review	Mar 30, 2026
Data Migration	Priya Patel	Blocked	Medium	Planning	May 1, 2026
Q2 Marketing Campaign	Tom Brooks	On Track	Medium	In Progress	Apr 1, 2026
Internal Tooling Upgrade	Aisha Nguyen	Done	Low	Complete	Mar 1, 2026
4. Example Queries & Expected Behavior
Once built, the chatbot should handle the following types of queries from any team member in Slack:
User Query	Query Type	Expected Behavior
“What’s the status of the Website Redesign?”	Specific Lookup	Return all fields for that project in a formatted Slack message
“What projects are at risk?”	Broad Filter	List all projects with Status = At Risk, summarized as bullets
“What’s due this week?”	Date-based Filter	Identify projects with Due Date within the current week
“Who owns the Data Migration project?”	Field Lookup	Return the Owner field for the matching project
“Are there any blocked projects?”	Status Filter	List all projects with Status = Blocked and include Notes for context
“Give me a full status update”	Summary	Provide a grouped summary by status across all projects
5. Prerequisites & Setup Requirements
Please ensure you have the following ready before the live session:
Component	Requirement	Notes
n8n Instance	Cloud or self-hosted n8n (v1.x+)	Free cloud account at n8n.io works
Slack App	Bot token with channels:history, chat:write scopes	Bot must be invited to the target channel
Google Sheets	OAuth2 credentials for Google Sheets API	Tracker sheet must be shared with the service account
OpenAI API Key	Valid API key with GPT-4o-mini access	Configured in n8n as an OpenAI credential
Slack Channels	A project questions channel + an errors channel	Bot must be invited to both channels
6. Success Criteria
Functional
•	Bot responds to human messages only (ignores bots, retries, system events)
•	Correctly answers specific project lookups with all relevant fields
•	Correctly answers broad queries (filter by status, priority, due date)
•	Returns “no matching projects” when appropriate
•	Never fabricates data beyond what’s in the sheet
Non-Functional
•	Response latency under 5 seconds for typical queries
•	Slack-formatted output (bold, bullets) renders correctly in Slack
•	Error handling posts to the errors channel without disrupting the main flow
•	Workflow handles Slack URL verification challenge on first setup
