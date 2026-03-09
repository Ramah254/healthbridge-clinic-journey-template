# Setup Guide

This guide walks you through setting up the HealthBridge Clinic Journey Template from scratch using Make.com and Airtable.

---

## Prerequisites

Before you begin, make sure you have:

- A free or paid [Make.com](https://make.com) account
- A free or paid [Airtable](https://airtable.com) account
- A WhatsApp Business API connection (via Meta, Twilio, or a BSP) **OR** a Twilio SMS account
- Basic familiarity with no-code tools (helpful but not required)

---

## Step 1: Set Up Your Airtable Base

1. Log into [Airtable](https://airtable.com) and create a new base called **HealthBridge OPD**
2. Create a table called **Patients** with the following fields:

   | Field Name | Field Type | Notes |
   |------------|------------|-------|
   | `patient_id` | Auto Number | Primary key |
   | `full_name` | Single line text | Patient full name |
   | `phone_number` | Phone number | With country code e.g. +254... |
   | `age` | Number | |
   | `diagnosis` | Single line text | Brief diagnosis note |
   | `intake_date` | Date | Date of registration |
   | `journey_status` | Single select | Options: Intake, Open, Day5, Day10, Closed, OptedOut |
   | `last_contacted` | Date | Auto-updated by Make |
   | `follow_up_count` | Number | Auto-incremented by Make |
   | `notes` | Long text | Nurse notes |
   | `opted_out` | Checkbox | Checked if patient replied STOP |

3. Create a second table called **Journey Log** with these fields:

   | Field Name | Field Type | Notes |
   |------------|------------|-------|
   | `log_id` | Auto Number | |
   | `patient_id` | Link to Patients | Linked record |
   | `event_type` | Single select | e.g. MessageSent, Reply, Escalated |
   | `event_date` | Date | |
   | `message_content` | Long text | |
   | `response` | Long text | Patient reply if any |

---

## Step 2: Create a Make.com Scenario

### Scenario 1: Patient Intake

1. In Make.com, create a new scenario
2. Add a **Webhooks > Custom Webhook** module as the trigger
3. Copy the webhook URL (you will use this in your intake form)
4. Add an **Airtable > Create Record** module
5. Map the webhook fields to the Airtable Patients table fields
6. Set `journey_status` to `Intake` on intake
7. Save and activate the scenario

### Scenario 2: Daily Follow-up Runner

1. Create a new scenario
2. Set the trigger to **Schedule > Every day at 8:00 AM**
3. Add **Airtable > Search Records** with filter: `{journey_status} = "Open"`
4. Add a **Router** module to split by journey day
5. For each route, add a **WhatsApp > Send Message** or **Twilio > Send SMS** module
6. After sending, update `last_contacted` and `follow_up_count` in Airtable
7. Save and activate the scenario

### Scenario 3: Response Handler (Optional)

1. Create a new scenario triggered by incoming WhatsApp/SMS webhook
2. Parse the message body for keywords: `YES`, `HELP`, `STOP`
3. Route:
   - `YES` → Update `journey_status` to next stage
   - `HELP` → Create an escalation record and notify nurse
   - `STOP` → Set `opted_out = true` and `journey_status = OptedOut`

---

## Step 3: Configure Credentials in Make.com

1. Go to **Connections** in Make.com
2. Add your **Airtable API key** and select your base
3. Add your **WhatsApp Business API** or **Twilio** credentials
4. Test each connection before activating scenarios

---

## Step 4: Test with Sample Data

1. Load the sample data from `sample-data/fake-patients.csv` into your Airtable Patients table
2. Manually trigger the Daily Follow-up Runner scenario
3. Check that messages are sent and records are updated correctly
4. Verify the Journey Log table is being populated

---

## Step 5: Connect Your Intake Form

1. Create a patient intake form (Tally, Typeform, Google Forms, or custom)
2. Set the form submission webhook to the URL from Scenario 1
3. Test a form submission end-to-end
4. Confirm a new Patients record is created with `journey_status = Intake`

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Make scenario not triggering | Check that the scenario is activated (toggle ON) |
| Airtable records not updating | Verify API key and base/table names are correct |
| Messages not sending | Check WhatsApp/Twilio credentials and message template approval |
| Wrong patients picked up | Review Airtable formula filter in Search Records module |

---

## Next Steps

- Review [docs/data-model.md](data-model.md) for full field definitions
- Check `templates/whatsapp-messages.md` for message template examples
- See `examples/make-scenario-map.md` for a visual flow diagram
