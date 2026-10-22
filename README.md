# Cyberion ThreatShield
## Detection Engineering & Threat Hunting Engagement

**Analyst:** Devyanshi (`devyanshisecurityhq`)  
**Programme:** Cyberion Defense Labs, Detection Engineering Individual Contributor Track  
**Assessment basis:** PRD v2.0, project code `CDL-DET-V2`  
**Scope:** analytical/content-engineering deliverables; no custom application is built.

Cyberion ThreatShield is a defensive detection-engineering portfolio project using public Windows attack telemetry. It contains standalone Sigma detections, Sigma correlation rules, event-level test manifests, ATT&CK coverage assessment, structured threat hunts, incident case reports, response playbooks, IOC research, methodology, and an executive presentation.

> For a concise deliverable-to-PRD map, start with [`SUBMISSION_CHECKLIST.md`](SUBMISSION_CHECKLIST.md).

> **Evidence boundary:** The dataset is a public, attack-focused sample corpus, not a production tenant or representative benign baseline. A match demonstrates that the rule detects the specified behaviour in the tested sample; it does not demonstrate production detection rate, attack impact, or successful credential theft. The local evaluation harness implements the subset of Sigma selection and correlation semantics used by this project. It is not the official Sigma CLI or a SIEM backend. Official Sigma schema/backend validation remains a separate check.

## Submission contents

| Deliverable | Included |
|---|---|
| Standalone Sigma rule library | 15 rules under `rules/` |
| Sigma correlation rules | 3 rules under `rules/correlation/` |
| ATT&CK coverage assessment | 22 unique techniques across multiple tactics, with support rule IDs and gaps |
| Structured threat hunts | 2 evidence-referenced hunt reports |
| Incident case reports | 2 evidence-backed case reports with timelines and bounded conclusions |
| Incident response playbooks | Credential compromise, lateral movement, C2/network beaconing |
| IOC research and operationalisation | Observed indicators, sample references, defensive use and handling caveats |
| Data dictionary and methodology | Included under `reports/` |
| Rule severity rationale | `reports/rule_rationale.md` maps all 18 rules to severity rationale and likely benign triggers |
| Rule-level testing evidence | 15 standalone and 3 correlation reports under `reports/` |
| False-positive review and tuning | 3 reports under `reports/tuning/`, with observed-vs-synthetic caveats |
| Automated tests | `tests/` |
| Final presentation | `presentation/Cyberion_ThreatShield_Final_Presentation.pptx` |
| Source telemetry | 278 EVTX samples, provenance and licence/readme information under `data/` |

## Project layout

```text
coverage/                     ATT&CK coverage matrix
data/                          dataset provenance, normalized export and public EVTX corpus
hunts/                         hypothesis-driven hunt reports
incidents/                     incident case reports
iocs/                          IOC research and operationalisation notes
playbooks/                     written incident response procedures
presentation/                  final PPTX and presentation source
reports/
  detection-testing/            standalone rule match evidence
  correlation-testing/          correlation evidence
  tuning/                       three tuning review reports
  data_dictionary.md
  executive_summary.md
  methodology.md
  target_techniques.md
  validation_summary.md
rules/                          15 standalone Sigma rules
rules/correlation/               3 multi-event Sigma correlations
scripts/                        analysis, evaluator and reproducible validation
tests/                          automated structural and telemetry tests
```

## Dataset provenance

The bundled telemetry is sourced from [EVTX-ATTACK-SAMPLES](https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES), pinned to upstream commit `4ceed2f4706daf601c212a8f91c113dd85349a2c`. See [`data/README.md`](data/README.md) and the included upstream README/licence file. A normalized event export is included as a test fallback for locked-down offline environments; the test harness prefers direct raw EVTX parsing when `python-evtx` is installed, and each validation report records the loader used. Samples were selected by event behaviour and rule intent. The files are public attack samples, not evidence from a real client environment.

## Setup

Recommended: Python 3.11 or newer on Linux, macOS, or Windows. Use a clean virtual environment.

```bash
python -m venv .venv
# Linux/macOS
source .venv/bin/activate
# Windows PowerShell: .venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

## Reproduce local checks

```bash
python -m pytest -q
python scripts/validate_project.py
```

The local automated suite currently records **26 passing tests** in the build environment. The validation command reads selected source samples and regenerates the per-rule reports and `reports/validation_summary.md`. It prefers parsing raw EVTX files with `python-evtx`; if that module is unavailable, it uses the bundled normalized export derived from those EVTX files. It does not scan every file in the dataset, and it does not claim a detection-rate estimate.

The declared requirements include `sigma-cli`. The environment used to prepare this ZIP was offline and could not retrieve packages from PyPI, so the official `sigma check rules/` output could not be captured here. This is disclosed rather than reported as a pass. The submitted package instead includes YAML parsing/structure checks, rule-level sample evidence, a project-local evaluator, and an automated test suite. If the assessor explicitly requires a Sigma CLI transcript, run the command below in an environment where `sigma-cli` can be installed and attach the real output:

```bash
sigma --version
sigma check rules/
```

This is an evidence-based analytical submission. Testing against a live SIEM, production baselines, and production-load validation are outside this project's PRD scope; they are not claimed.

## What “Covered” means in this project

`Covered` means the listed rule has a documented mapping and a positive match against at least one cited public sample using the project-local evaluator. It does not mean all variants of the technique are detected. `Partially Covered` means the available telemetry/rule logic provides narrow or incomplete visibility. `Not Covered` means the library does not currently provide a defensible detection for that technique. The detailed matrix and caveats are in [`coverage/attack_coverage_matrix.md`](coverage/attack_coverage_matrix.md). Severity rationale and per-rule benign trigger review are in [`reports/rule_rationale.md`](reports/rule_rationale.md).

## Important operational cautions

- Correlation thresholds and network-port selectors are lab examples and must be tuned against a representative benign baseline.
- The Kerberos correlation uses a low count threshold of two distinct target usernames because the selected demonstration sample is small. Do not deploy this threshold to production without baseline-driven tuning.
- IPs, domains, process paths and command lines in the IOC report are sample-observed artefacts. Treat them as research indicators, not current threat-intelligence verdicts. Validate age, ownership and context before blocking.
- This work uses offline public logs. It does not claim a live compromise, client-side impact, production-load testing, or an operational SOC deployment.

## PRD submission status

The package includes each deliverable category listed in the PRD: 15 standalone Sigma rules, 3 multi-event correlation definitions, a 22-technique ATT&CK matrix, 2 structured hunts, 2 incident cases, 3 response playbooks, IOC research, a data dictionary, methodology, tuning reviews, executive summary, per-rule evidence, automated tests, and a 16-slide presentation. The project-local test suite passes for the documented sample set. The official Sigma CLI was unavailable in the offline build environment and is not represented as passed. This package is prepared for submission as an analytical/content-engineering engagement, not as a production deployment or guarantee of perfect technique coverage.
