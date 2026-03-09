# HealthBridge Clinic Journey Template

> A reusable open-source starter kit for automating patient intake, follow-up scheduling, and WhatsApp reminders using **Make.com** and **Airtable** — designed for small clinics and low-resource healthcare teams.

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Platform: Make.com](https://img.shields.io/badge/Platform-Make.com-purple)
![Database: Airtable](https://img.shields.io/badge/Database-Airtable-green)

---

## What This Does

This template gives any clinic a working automation foundation for:

- **Patient Intake** — Capture patient details via a form or webhook and automatically create a record in Airtable
- **Journey Tracking** — Assign each patient a journey status (`Intake`, `Open`, `Day5`, `Day10`, `Closed`, `OptedOut`)
- **Automated Follow-ups** — Trigger scheduled WhatsApp or SMS reminders based on journey day/status
- **Escalation Routing** — Flag patients who miss follow-up responses for manual nurse review

Built by a registered nurse + automation developer — so the logic mirrors how real OPD follow-up workflows operate.

---

## Who This Is For

- Small private clinics and OPD departments
- Health-tech builders and no-code developers
- Nurses and clinic managers exploring automation
- Anyone building a patient communication or reminder system with Make + Airtable

---

## Stack

| Tool | Purpose |
|------|----------|
| [Make.com](https://make.com) | Automation / workflow engine |
| [Airtable](https://airtable.com) | Patient database + journey state |
| WhatsApp Business API / Twilio | Message delivery |
| Webhooks | Intake trigger from forms or apps |

---

## Repository Structure

```
healthbridge-clinic-journey-template/
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── docs/
│   ├── setup.md           # Step-by-step setup guide
│   └── data-model.md      # Airtable field definitions
├── templates/
│   └── whatsapp-messages.md  # Sample message templates by journey day
├── examples/
│   ├── airtable-schema.json  # Exportable Airtable base schema
│   └── make-scenario-map.md  # Visual map of Make.com scenario flow
└── sample-data/
    └── fake-patients.csv     # Sanitized sample data for testing
```

---

## Quick Start

1. **Clone or fork this repo**
2. **Set up your Airtable base** using the schema in `examples/airtable-schema.json`
3. **Import the Make.com scenario** by following `docs/setup.md`
4. **Configure your WhatsApp or Twilio credentials** in Make.com
5. **Test with sample data** from `sample-data/fake-patients.csv`
6. **Go live** — point your intake form webhook to your Make.com webhook URL

Full step-by-step instructions are in [docs/setup.md](docs/setup.md).

---

## Journey States

```
Intake --> Open --> Day5 --> Day10 --> Closed
                              |
                              v
                           OptedOut
```

| Status | Meaning |
|--------|----------|
| `Intake` | Patient just registered, awaiting confirmation |
| `Open` | Active journey, scheduled follow-ups running |
| `Day5` | 5-day follow-up message sent |
| `Day10` | 10-day follow-up message sent |
| `Closed` | Journey completed successfully |
| `OptedOut` | Patient requested no more messages |

---

## Sample WhatsApp Message (Day 5)

```
Hello [Patient Name], this is HealthBridge following up on your visit to [Clinic Name].
How are you feeling today? Reply YES if doing well, or HELP if you need assistance.
Reply STOP to opt out.
```

---

## Contributing

Contributions are welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

Ideas for contributions:
- Add a Twilio SMS version of the workflow
- Add multilingual message templates (Swahili, French, Arabic)
- Add a Day 7 or Day 14 follow-up route
- Build a Google Sheets version of the data model
- Add appointment booking flow before intake

---

## Maintainer

Built and maintained by **Ramadhan Omar** — Registered Nurse (BScN) and healthcare automation developer.

Building [HealthBridge Solutions](https://github.com/Ramah254/healthbridge-solutions) — clinic automation systems for low-resource healthcare teams in East Africa.

---

## License

MIT License — free to use, modify, and distribute. See [LICENSE](LICENSE) for details.
