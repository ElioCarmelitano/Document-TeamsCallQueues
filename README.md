Connect to Teams and ExchangeOnline PowerShell Modules before running.

# Document-TeamsCallQueues
.SYNOPSIS
Generates an HTML documentation report for all Microsoft Teams Call Queues.

.DESCRIPTION
This read-only script enumerates all Teams Call Queues (CQs) and produces a structured HTML report.
It resolves friendly names for targets and agents, and includes bracketed audit details such as
raw IDs and resource account attachments.

The report includes:
 - Routing Method
 - Agent Alert Time
 - Agent Opt Out Allowed
 - Agents (Users and Distribution Lists), resolved to friendly names
 - Exceptions: Overflow (threshold, action, target, greeting) — each on its own line
 - Exceptions: Timeout (threshold, action, target, greeting) — each on its own line
 - Exception: No Agents (apply-to, action, target, greeting) — each on its own line
 - Callback Eligible (Enabled + detailed thresholds)
 - Callback Message (TTS: <text> | AudioFile: <name> | None)
 - Callback Key (DTMF: 0–9, *, #)
 - Callback Fail Notification (friendly target)
 - Authorised Users (friendly DisplayName)

TARGET RESOLUTION (best effort)
 - PSTN: strips 'tel:' and displays E.164
 - Call Queue: CQ name by Id
 - Auto Attendant: AA name by Id
 - Resource Account (ApplicationInstance): RA display name and, if mapped, the attached CQ/AA name
 - Group (Shared Voicemail / DL): Team/M365 Group display name where possible (Teams/EXO/Graph)
 - User: DisplayName (falls back to UPN/Id)
 - Otherwise: shows the raw Id as “Unknown”

REQUIREMENTS
 - Teams/Skype Online PowerShell with access to:
     * Get-CsCallQueue
     * Get-CsAutoAttendant (for target resolution)
     * Get-CsOnlineApplicationInstance (resource accounts)
     * Get-CsOnlineUser (agents/users)
 - Optional (for richer group names):
     * MicrosoftTeams: Get-Team
     * ExchangeOnlineManagement: Get-UnifiedGroup
     * Microsoft.Graph: Get-MgGroup

.PARAMETER OutputPath
Full or relative path to the HTML report file.
Default: .\Document-TeamsCallQueues-<timestamp>.html

.PARAMETER Open
If specified, opens the generated HTML report when complete.

.EXAMPLE
PS> .\Document-TeamsCallQueues.ps1
Generates ".\Document-TeamsCallQueues-YYYYMMDD-HHmmss.html".

.EXAMPLE
PS> .\Document-TeamsCallQueues.ps1 -OutputPath "C:\Temp\CQ-Report.html" -Open
Writes the report to C:\Temp\CQ-Report.html and opens it in the default browser.

.NOTES
 - Parser-safety: Hashtables use ';' between key/value pairs. Complex values are built in variables first.
 - Null-safety: All .ContainsKey / indexing operations guard against null/empty keys.
 - This script is read-only; it does not modify any tenant configuration.
