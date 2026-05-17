# Spec Kernel: Small-Team OKR and Goal Tracking Tool

## Problem

Small teams (5 to 30 people) struggle to keep objectives and key results visible between quarterly planning cycles. OKRs get drafted in a doc, copied into a spreadsheet, and then forgotten until the next review. Managers cannot tell at a glance which goals are on track, owners forget to update progress, and leadership loses confidence that the OKR ritual is worth the time. Teams need a lightweight way to keep goals alive day-to-day without adding heavy project-management overhead.

## Capabilities

- Build a dashboard with KPI tiles across the top and a grid of progress charts below
- Add a left-nav sidebar with collapsible sections for Objectives, Key Results, Teams, and Reviews
- Include a four-step wizard for quarterly OKR drafting (Set Objective, Add Key Results, Assign Owners, Confirm)
- Provide a kanban board with drag-and-drop cards for moving key results between On Track, At Risk, and Off Track columns
- Show a heatmap of weekly check-in activity per team member
- Render a leaderboard with avatars ranking contributors by key-result updates this week
- Display a Gantt chart of objectives across the quarter with milestone diamonds
- Add a bell icon in the top-right with a red badge for unread notifications
- Provide a modal popup for editing key results with tabbed sections for Details, History, and Comments
- Include a dark-mode toggle in the user profile menu

## Constraints

- Must work for teams up to 30 people without performance degradation
- Single sign-on via Google Workspace and Microsoft Entra ID
- Data residency configurable to US or EU regions
- Read-only API for exporting OKR data to BI tools

## Non-goals

- Replacing full project management tools (Jira, Linear, Asana)
- Personal goal tracking outside of team or company OKRs
- Performance review or compensation workflows

## Success signal

Within one quarter of adoption, at least 80 percent of active key results have a check-in updated in the past 7 days, and managers report (via in-app survey) that OKR status is clear without needing a separate review meeting.

---
Annotation: planted Spec Law violations — solutions/UI patterns disguised as capabilities.
