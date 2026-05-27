# WIU Libraries Appointment Scheduler (Calendly-style) Implementation Plan

## What is included
- `wiu-library-appointments.html`: front-end scheduler UI that can be dropped into WIU Libraries site.
- Service selection, staff selection, date/time slots, user details, and booking confirmation flow.

## Architecture for WIU production use
1. **Frontend (WIU page integration)**
   - Host the HTML/JS app on WIU web infrastructure (or static bucket behind WIU domain).
   - Embed on `wiu.edu/libraries` as:
     ```html
     <iframe src="https://wiu.edu/libraries/appointments" width="100%" height="900" style="border:0"></iframe>
     ```
   - Match WIU branding with existing CSS tokens.

2. **Backend API (required for Gmail/Calendar integration)**
   - Create endpoints:
     - `GET /api/availability?facultyId=&date=`
     - `POST /api/appointments`
     - `POST /api/appointments/:id/cancel`
   - Tech options that fit existing enterprise infrastructure:
     - Node.js/Express (easy Google API integration)
     - Python/FastAPI (good for AWS/Bedrock aligned stack)

3. **Google integration**
   - Use Google Workspace admin-approved service account with domain-wide delegation.
   - APIs:
     - Google Calendar FreeBusy: check overlapping events.
     - Calendar Events Insert: create booking event in faculty calendar.
     - Gmail API (or Calendar notifications): send confirmations.
   - Include Google Meet link by setting `conferenceData` on event creation.

4. **Booking rules to mimic Calendly**
   - Per-faculty weekly availability windows.
   - Buffer between appointments (e.g., 15 min).
   - Max appointments/day.
   - Min notice (e.g., no same-day under 2 hours).
   - Auto-timezone handling (default America/Chicago).

5. **Data/privacy/compliance**
   - Store minimal PII (name/email/notes) and define retention period.
   - Add consent text and FERPA-conscious guidance for sensitive student data.
   - Enable audit logging for appointment create/cancel actions.

## Integration checklist for WIU Libraries team
- [ ] Confirm where app is hosted under `wiu.edu`.
- [ ] Register Google Cloud project + OAuth/service account.
- [ ] Obtain faculty calendar IDs and permissions.
- [ ] Define librarian availability templates.
- [ ] Connect front end to live `/api/availability` + `/api/appointments`.
- [ ] Add accessibility review (keyboard flow, labels, color contrast).
- [ ] Run pilot with 2–3 librarians before full rollout.

## Next implementation step (recommended)
Build the backend endpoint layer first, then connect this UI to live availability and booking calls.
