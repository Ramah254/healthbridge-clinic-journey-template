# Make.com Scenario Map

This document describes the three core Make.com scenarios for the HealthBridge Clinic Journey Template and how they connect.

---

## Overview

```
[Intake Form] --> [Webhook] --> Scenario 1: Patient Intake
                                      |
                                      v
                               Airtable: Create Patient Record
                               (journey_status = Intake)

[Schedule: Daily 8AM] --> Scenario 2: Follow-up Runner
                                      |
                                      v
                               Airtable: Search Records
                               (journey_status = Open)
                                      |
                                      v
                               Router: Check follow_up_count
                              /              \
                    count < 1            count >= 1
                     Day 5 msg           Day 10 msg
                         |                    |
                         v                    v
                  Send WhatsApp         Send WhatsApp
                  Update Airtable       Update Airtable
                  (status = Day5)       (status = Day10)

[Incoming WhatsApp/SMS] --> Scenario 3: Response Handler
                                      |
                                      v
                             Parse message body
                            /        |        \
                          YES       HELP      STOP
                           |         |         |
                           v         v         v
                     Update       Create    Set opted_out
                     status     Escalation  = true
                     to next     Record     status =
                     stage      + Notify    OptedOut
                               Nurse
```

---

## Scenario 1: Patient Intake

**Trigger:** Webhooks > Custom Webhook

**Modules:**
1. Webhooks: Custom Webhook (trigger)
2. Airtable: Create Record in Patients table
   - Map: full_name, phone_number, age, diagnosis, intake_date
   - Set: journey_status = `Intake`, follow_up_count = `0`
3. (Optional) WhatsApp/Twilio: Send intake confirmation message

**Webhook payload expected:**
```json
{
  "full_name": "Jane Doe",
  "phone_number": "+254712345678",
  "age": 34,
  "diagnosis": "Hypertension follow-up",
  "attending_nurse": "Nurse Wanjiku"
}
```

---

## Scenario 2: Daily Follow-up Runner

**Trigger:** Schedule > Every day at 8:00 AM EAT

**Modules:**
1. Schedule trigger
2. Airtable: Search Records
   - Table: Patients
   - Filter: `{journey_status} = "Open"`
   - Max records: 20 (adjust as needed)
3. Iterator: Loop through each patient record
4. Filter: Check `opted_out` is NOT checked
5. Router: Branch by `follow_up_count`
   - Route A: follow_up_count < 1 → Day 5 message
   - Route B: follow_up_count >= 1 → Day 10 message
6. WhatsApp/Twilio: Send message from template
7. Airtable: Update Record
   - Increment `follow_up_count` by 1
   - Set `last_contacted` to today's date
   - Route A → Set `journey_status` to `Day5`
   - Route B → Set `journey_status` to `Day10`

---

## Scenario 3: Response Handler

**Trigger:** Webhooks > Custom Webhook (WhatsApp incoming message webhook)

**Modules:**
1. Webhook trigger: receives incoming message payload
2. Tools: Text parser to extract phone number and message body
3. Airtable: Search Records to find patient by phone number
4. Router: Branch by message content (case-insensitive)
   - Route YES: Update journey_status to next stage
   - Route HELP: Create escalation record + send nurse alert
   - Route STOP: Set opted_out = true, journey_status = OptedOut
   - Route Other: Log to Journey Log as ReplyReceived
5. Airtable: Create Journey Log entry for every incoming message

---

## Notes

- All three scenarios should be activated independently
- Scenario 2 will pick up patients set to `Open` — after intake, manually or via scenario change `journey_status` from `Intake` to `Open` once the patient is confirmed
- The opted_out filter in Scenario 2 prevents messages to patients who have replied STOP
- Scenario 3 depends on your WhatsApp provider supporting incoming message webhooks (Meta Cloud API, Twilio, WATI, etc.)
