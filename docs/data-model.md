# Data Model Reference

This document describes the full Airtable data model for the HealthBridge Clinic Journey Template.

---

## Table: Patients

The main table that stores one record per patient in an active or completed care journey.

| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| `patient_id` | Auto Number | Yes | Unique system-generated ID |
| `full_name` | Single line text | Yes | Patient's full name |
| `phone_number` | Phone | Yes | WhatsApp/SMS number with country code (e.g. +254712345678) |
| `age` | Number | No | Patient age in years |
| `gender` | Single select | No | Male / Female / Other |
| `diagnosis` | Single line text | No | Short clinical note e.g. "Hypertension follow-up" |
| `attending_nurse` | Single line text | No | Name of nurse or provider managing the journey |
| `intake_date` | Date | Yes | Date patient was registered into the system |
| `journey_status` | Single select | Yes | Current stage: Intake / Open / Day5 / Day10 / Closed / OptedOut |
| `follow_up_count` | Number | No | Auto-incremented each time a follow-up message is sent |
| `last_contacted` | Date | No | Date of last outbound message (updated by Make) |
| `next_follow_up_date` | Date | No | Calculated or manually set date for next message |
| `opted_out` | Checkbox | No | True if patient replied STOP or was manually opted out |
| `escalation_flag` | Checkbox | No | True if patient needs urgent manual review |
| `notes` | Long text | No | Nurse comments and clinical observations |
| `consent_given` | Checkbox | No | True if patient consented to receive automated messages |

### journey_status Values

| Value | Meaning |
|-------|----------|
| `Intake` | Newly registered, not yet in active follow-up |
| `Open` | Active patient — daily follow-up runner picks these up |
| `Day5` | Day 5 message has been sent |
| `Day10` | Day 10 message has been sent |
| `Closed` | Journey completed — patient discharged or recovered |
| `OptedOut` | Patient replied STOP — no further messages to be sent |

---

## Table: Journey Log

Append-only log of all events in a patient's journey. One record per event.

| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| `log_id` | Auto Number | Yes | Unique log entry ID |
| `patient_id` | Link to Patients | Yes | Linked patient record |
| `event_type` | Single select | Yes | Type of event (see below) |
| `event_date` | Date + time | Yes | Timestamp of the event |
| `message_content` | Long text | No | Full text of the message sent |
| `response` | Long text | No | Patient's reply text (if any) |
| `triggered_by` | Single line text | No | e.g. "Make scenario" or "Nurse (manual)" |

### event_type Values

| Value | Meaning |
|-------|----------|
| `MessageSent` | Outbound message sent to patient |
| `ReplyReceived` | Patient replied |
| `Escalated` | Patient flagged for manual nurse follow-up |
| `StatusChanged` | Journey status was updated |
| `OptOut` | Patient opted out of messages |
| `NurseNote` | Manual note added by a nurse |

---

## Relationships

```
Patients (1) ----< Journey Log (many)
```

Each patient can have many log entries. Log entries are linked back to the patient via the `patient_id` linked record field.

---

## Field Naming Conventions

- All field names use `snake_case` to make them compatible with Make.com variable mapping
- Fields that are auto-updated by Make.com are marked in the setup guide
- Fields with `Checkbox` type default to unchecked (false)
- All date fields use ISO format (YYYY-MM-DD) unless using Airtable's built-in date picker

---

## Notes for Developers

- The `journey_status` field drives the routing logic in Make.com — do not rename it
- The `phone_number` field must include the country code prefix for WhatsApp/SMS delivery
- The `opted_out` checkbox should always be checked before sending any message (Make.com filter)
- The `escalation_flag` can be used to build a nurse dashboard view in Airtable
