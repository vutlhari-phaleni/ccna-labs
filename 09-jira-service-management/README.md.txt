# IT Service Management & Helpdesk Simulation (Jira)

## Overview
This project demonstrates practical experience using **Jira Service Management** to handle an IT support ticket from creation to resolution. It combines technical network troubleshooting with professional service management workflows.

## The Scenario
An employee reported that their workstation suddenly lost internet access. The goal was to diagnose the issue, document the findings, and resolve the ticket using ITIL best practices.

## Tools Used
- **Jira Service Management** (Ticketing, Queues, Workflows)
- **Windows Command Prompt** (Diagnostic tools: `ipconfig`, `ping`, `tracert`)
- **Cisco Packet Tracer** (Simulated network fault reproduction)

## Key Workflow Steps

### 1. Ticket Creation & Categorization
- Created ticket: "Employee unable to access the internet."
- Set Priority: High.
- Added Labels: `network-troubleshooting`.
- Used the "Add internal note" feature (instead of "Reply to customer") to document technician-only diagnostic steps.

### 2. Technical Troubleshooting & Diagnostics
Ran diagnostic commands to isolate the problem:
- `ipconfig`: Identified an incorrect Default Gateway on the affected PC (192.168.10.99 instead of 192.168.10.1).
- `ping 127.0.0.1`: Verified the local TCP/IP stack was working.
- `ping 192.168.10.1`: Failed (proving the PC could not reach its gateway).
- `ping 8.8.8.8`: Failed (Destination host unreachable).
- `tracert 8.8.8.8`: Showed the packet dying at the local router.

### 3. Fault Reproduction (Packet Tracer)
To verify the root cause, I built a simulated network in Cisco Packet Tracer with a Router, Switch, and PCs. I deliberately configured PC0 with the wrong default gateway (192.168.10.99) and reproduced the exact "Destination host unreachable" error seen in the ticket.

### 4. Resolution & Verification
- Corrected the default gateway on the affected PC to 192.168.10.1.
- Ran `ipconfig` to verify the change.
- Successful pings confirmed external connectivity was restored.

### 5. Ticket Closure
- Updated the Jira "Resolution" field (Required field for closing).
- Added a final internal note explaining the root cause, the action taken, and the verification results.
- Transitioned the ticket status to **Done/Resolved**.

## Key Lessons Learned
1. **Internal Notes vs. Customer Replies:** Internal notes are for documenting technical steps (like ping outputs). Customer replies should be simple, non-technical summaries.
2. **Workflow Management:** A Jira ticket cannot be resolved without filling in the required Resolution field. The status must accurately reflect reality (In Progress, Pending, Done).
3. **Root Cause Analysis:** A PC that can ping its local IP but cannot ping the internet usually has a Gateway or DNS issue.
4. **Documentation is Key:** Attaching evidence (like command prompt outputs) to a ticket saves time for L2/L3 escalation.

## Documentation
- [Jira Service Management Practice Record (PDF)](./Jira_Service_Management_Practice_Record.pdf)