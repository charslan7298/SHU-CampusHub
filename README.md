# SHU-CampusHub
A Resilient Student Services Platform on a Virtualised Enterprise Lab
Module: Enterprise Applications Management (55-601410) Group size: Four students Organisation used for requirements: Sheffield Hallam University (SHU) Budget: £0 cash spend, using the university lab, existing laptops and free or open-source software Status: Revised proposal for tutor approval
1. Project description
SHU CampusHub is a student-facing web application that combines three services: support-ticket management, study-space and computer booking, and a service-health dashboard. It uses shared login, role-based access (student, support staff, administrator) and one PostgreSQL database.
The application is deliberately small. The main aim is to deploy it on a clustered virtualised lab that the group has built, and to measure how deployment choices affect its availability, security and recovery.
Research question: How do deployment choices and recovery procedures affect the availability and recovery time of a student-facing enterprise application?
All data is synthetic (fictional users, tickets and resources). The project will not use live Blackboard, SHU production systems, SHU single sign-on or real student records. Any "Blackboard-like" service is a clearly labelled simulation.
2. Organisational need and requirements
Students report IT faults, look for study resources and want to know whether a service is down, often through separate channels. CampusHub shows how these can share one identity, one data layer and one monitoring view. Example: a student reports a faulty computer, support staff mark it unavailable, and it can no longer be booked.
The group does not claim SHU has a specific deficiency. These needs are treated as requirements to investigate and validate with the module tutor and, where possible, SHU technical staff.
SHU staff to be consulted (optional): [names or roles to be confirmed, for example SHU IT or lab technical staff].
3. Scope
Core modules
1. Accounts and roles: Django authentication; server-side permission checks; students cannot reach staff functions or other users' records.
2. Support tickets: students raise tickets; staff assign and update them; every status change is stored with who, when and what.
3. Booking: students reserve and cancel fictional study spaces and computers; staff can mark resources unavailable; conflicting bookings are rejected by the database.
4. Service-health dashboard: a scheduled worker checks at least two test endpoints (including a simulated Blackboard-like service), stores timestamped results and shows failures and recovery.
Optional, only after the core and experiments are complete: timetable import from a sample .ics file; Active Directory login through LDAP, if the lab permits it.
Excluded: AI study assistant, live Blackboard or SHU APIs, real student data, SHU single sign-on, cyber-lab provisioning, Kubernetes, mobile apps, paid services, and any claim of high availability that has not been configured and tested.
4. Hardware, software and systems for the proof of concept
Hardware (group-built lab and laptops)
Component
Count
Role
Proxmox nodes (clustered)
3
Virtualisation cluster; live migration experiments
Ubuntu VMs
3
Application, database and monitoring hosts, initially one per node
TrueNAS
1
Shared VM storage; database backup destination
VyOS
1
Router and firewall; network separation and port tests
Windows Server
1
Existing lab services; Active Directory only if used as an optional extension
Team laptops (about 16 GB RAM)
4
Development and local Docker testing
The group has administrative Proxmox access, has already tested live migration, and can configure the lab network. Any experiment that could disrupt shared services, such as powering off a node, needs prior supervisor approval. VM resources, versions and IP addresses will be recorded in an architecture diagram during the first week.
Software
Ubuntu Server, Proxmox VE, TrueNAS, VyOS, Docker, Docker Compose, Python, Django, PostgreSQL, Gunicorn, Nginx, Prometheus, Grafana, Git, GitHub, Replit, Slack and a Python test framework (Django tests or pytest).
Licences
All core software is free or open source. Windows Server and Active Directory will be used only if their evaluation licence and permitted use cover the project period [to be confirmed]. No paid cloud service, licence, API or credit is required.
5. Technical design
Architecture: a Django modular monolith (one application, separated modules), PostgreSQL with Django migrations, Docker Compose, Gunicorn behind Nginx. Each Ubuntu VM runs the containers assigned to it using its own documented Compose file, with explicit network addresses and restricted firewall rules. One Compose command does not manage all three VMs.
Deployment variants being compared. To answer the research question, the group will deploy two layouts and run the same tests on both:
• Variant 1: application, database and monitoring on separate VMs.
• Variant 2: application and database together on one VM, with monitoring on another.
Booking integrity: bookings use a start and end time for a resource. Overlaps are blocked by a PostgreSQL exclusion constraint on resource and time range (using the btree_gist extension), with a transaction-based fallback if that proves unworkable. A test sends simultaneous conflicting requests and counts how many are accepted (the target is zero).
Security controls: server-side role and ownership checks, input validation, CSRF protection, Django password hashing, secrets in environment variables with a .env.example placeholder file, ticket audit history, PostgreSQL reachable only from the application host, and VyOS rules allowing only required ports.
6. Evaluation plan
The group will first record a baseline, then agree numerical pass/fail thresholds before the final runs. Thresholds are set from the baseline and are not changed afterwards.
Experiment
Method
What is measured
A. Deployment and persistence
Deploy, create records, restart services
Reproducible steps; records intact
B. Simulated outage
Stop and restore the simulated endpoint
Detection time, displayed status, recovery time
C. Application failure
Restart the container or VM (approved method)
Failed requests; time to stable response
D. Backup and restore
Dump the database, restore into a clean database
Restore duration; record and relationship checks; data lost since backup
E. Booking concurrency
Send conflicting requests simultaneously
Conflicting bookings accepted
F. Authorisation and validation
Attempt unauthorised access and invalid input
Expected versus actual result; fix and retest
G. Live migration
Run a constant request loop while migrating the application VM, then the database VM
Success rate, interruption time, data integrity
H. Firewall
Test allowed and blocked ports through VyOS
Actual connectivity and logs
Experiments B, C, D and G are run on both deployment variants and compared. Important runs are repeated. Request intervals, health-check intervals and measurement limits are recorded.
Measures: request success rate; interruption duration (first failed to first successful request); recovery time (until responses stay successful for an agreed period); detection time; restore duration; data integrity.
Node-failure testing is not part of the core. It will be attempted only if Proxmox HA is configured and the supervisor approves in writing.
Stated limitations
• TrueNAS is a single point of failure for any VM disks stored on it.
• A backup stored only on TrueNAS cannot prove recovery from TrueNAS failure. The group will also keep a second copy of the backup on separate storage (a Proxmox node's local disk or a team laptop, since the data is synthetic) and will state that a copy on another VM that itself lives on TrueNAS does not count as independent.
• Live migration is not high availability. Only behaviour that is configured and measured will be claimed.
• Results come from one lab and may not generalise.
A successful backup command is not evidence of recovery. Only a verified restore counts.
7. Team organisation and project management
Member
Primary ownership
Supporting role
1
Django foundation, integration, requirements, evidence register, minutes, contact with SHU staff
Report structure and AI declaration
2
Accounts, roles, tickets, health-check worker and dashboard (named owner)
Permission tests, synthetic data
3
Booking module, database integrity, concurrency tests, backup and restore
Data verification
4
Lab infrastructure, deployment on Ubuntu VMs, VyOS rules, migration experiments
Lab diagram, runbooks, Prometheus and Grafana
Roles may change after the team-role assessment in week 1. Everyone contributes to testing, documentation and the presentation, understands the whole architecture, and speaks in the presentation.
Project management tools and records: a shared task board with owners and acceptance criteria; a weekly meeting with agenda, minutes and action list; a risk register; a milestone schedule with critical path analysis; a team-role assessment; a record of technical decisions; Slack for daily communication.
Development workflow: the group builds collaboratively in the shared Replit workspace and tests locally with Docker Compose. Replit is connected to the GitHub repository from the start, and each member commits their own work regularly so that individual contributions are visible. GitHub holds delivery versions and deployment evidence. Replit is a development environment and is not evidence of deployment, which is shown separately on the lab VMs.
8. Timeline
Key dates from the assessment brief: report, peer and self-assessment and presentation upload by Tuesday 15 December 2026, 15:00; presentation in the week commencing 11 January 2027.
Week commencing
Milestone
19 Oct
Roles and team-role assessment; lab permissions and architecture diagram; requirements, risk register and critical path; repository connected
26 Oct
Django, PostgreSQL and Docker working; login, roles and first ticket workflow with tests
2 Nov
First deployment to the Ubuntu VMs; reverse proxy; persistence checked
9 Nov
Booking conflict prevention; ticket history; permission tests
16 Nov
Health checks, dashboard and simulated endpoint; Prometheus and Grafana where justified
23 Nov
Baselines; outage, restart and backup and restore experiments on both variants
30 Nov
Concurrency, authorisation, firewall and migration experiments; fixes and retests
7 Dec
Feature freeze; evidence, evaluation, report and demonstration recording
14 Dec
Rehearsal; upload deliverables by 15 Dec 15:00
11 Jan
Presentation and demonstration
Optional features are dropped before any testing or evaluation time is reduced.
9. Risks
Risk
Mitigation
Scope too large for four people
Four core modules only; optional features deferred
Lab access or permissions differ from expectations
Verify in week 1; keep a local Docker fallback
Experiments disrupt shared lab services
Approved tests only; supervisor permission for anything disruptive
TrueNAS failure
Document as a single point of failure; keep a second backup copy on separate storage
Works in Replit but fails on Ubuntu
Test with Docker Compose locally and deploy early
Insufficient laptop or VM resources
Measure early; reduce services or change VM placement
Booking conflicts
Database exclusion constraint and concurrency tests
Backup cannot be restored
Restore into a clean environment and verify records
Firewall change breaks connectivity
Document rules; keep a rollback procedure
Secrets exposed
Environment variables; placeholders in the repository
Uneven contribution
Task ownership, individual commits and meeting records
Evidence collected too late
Log results, failures, fixes and retests as they happen
10. Expected deliverables
• Working Django application with accounts, tickets, booking and health monitoring
• Lab architecture diagram, IP plan and VM placement
• Repository with code, history, tests and tagged releases
• Deployment instructions and runbooks for startup, backup, restore and recovery
• Synthetic demonstration dataset
• Acceptance-test matrix, security test results and measured experiment results, with a comparison of the two deployment variants
• Verified restore evidence and an honest assessment of storage failure risk
• Group report of about 2,000 words, and a group presentation with demonstration and recorded fallback
• AI Transparency Scale declaration as an appendix, and individual peer and self-assessments
11. Approval requested
Approval to develop SHU CampusHub as a controlled, zero-cost proof of concept on the group's virtualised lab, using the scope, hardware, software and evaluation plan above. The group welcomes tutor feedback on scope, feasibility and which experiments may be run on shared infrastructure, and will adjust the design to the confirmed lab configuration.
