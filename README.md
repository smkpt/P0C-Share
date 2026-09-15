# 🛡️ STATERA AI Customer Support & Troubleshooting: Automated Test & Evaluation Suite (POC)

[![CI - Azure DevOps & GitHub Actions](https://img.shields.io/badge/CI%2FCD-Azure%20DevOps%20%7C%20GitHub%20Actions-blue.svg)](#8-cicd-pipeline-integration)
[![Python 3.9+](https://img.shields.io/badge/python-3.9+-brightgreen.svg)](https://www.python.org/)
[![Evaluation Framework](https://img.shields.io/badge/Eval%20Framework-DeepEval%20Compatible-orange.svg)](#3-deepeval-evaluation-metrics--qa-sla-gates)
[![QA Sign-Off Status](https://img.shields.io/badge/QA%20Sign--Off-Automated%20Gatekeeper%20%5BGO%5D-success.svg)](#4-machine-enforced-qa-gatekeeper--go--no-go-matrix)
[![STATERA Domain](https://img.shields.io/badge/Domain-STATERA%20CIPS%20Passive%20Sampler-0284c7.svg)](https://www.statera.org/)

An enterprise-grade, production-ready AI test automation and LLM/RAG evaluation framework engineered specifically for **STATERA** ([www.statera.org](https://www.statera.org/)). This project validates and stress-tests an AI-powered diagnostic and customer support agent assisting field scientists and technicians deploying the **Composite Integrative Passive Sampler (CIPS)** and remote IoT monitoring stations worldwide.

---

## 📑 Table of Contents
- [1. Executive Summary & Domain Alignment](#1-executive-summary--domain-alignment)
- [2. System Architecture](#2-system-architecture)
- [3. DeepEval Evaluation Metrics & QA SLA Gates](#3-deepeval-evaluation-metrics--qa-sla-gates)
- [4. Machine-Enforced QA Gatekeeper & Go / No-Go Matrix](#4-machine-enforced-qa-gatekeeper--go--no-go-matrix)
- [5. Test Automation Suites](#5-test-automation-suites)
- [6. Project Structure](#6-project-structure)
- [7. Quickstart & Local Execution](#7-quickstart--local-execution)
- [8. CI/CD Pipeline Integration (Azure DevOps)](#8-cicd-pipeline-integration-azure-devops)
- [9. Evaluation Results & Sign-off Report](#9-evaluation-results--sign-off-report)

---

## 1. Executive Summary & Domain Alignment

**STATERA** manufactures the **Composite Integrative Passive Sampler (CIPS)**, a breakthrough environmental technology capable of capturing over 500 polar and non-polar organic contaminants in water, sediment, air, and biosoils across $\log K_{OW} < 0 - 8.3$ (PFAS, PCBs, PAHs, 6PPD, microcystin LR). 

In remote field deployments (e.g. Tijuana River, Cape Fear River, Kunming Lake, Puget Sound), field monitoring stations face complex environmental challenges:
- **Connectivity Failures**: Cellular LTE-M/NB-IoT attenuation, antenna submergence, MQTT broker timeouts.
- **Power Degradation**: Solar panel biofouling/shading, deep discharge below 3.0V, LiFePO4 battery cycle wear (>1500 cycles).
- **Analytical & Extraction Protocols**: Strict 4 mL solvent extraction protocol for GCMS/LCMS direct injection; PRC (Performance Reference Compound) dissipation recalibration under severe biofouling (index > 0.70).
- **Warranty & RMA Dispatch**: Validating 2-year warranty entitlements and issuing return shipping kits for unrecoverable hardware.

This POC provides a complete, automated quality assurance and evaluation suite testing the AI diagnostic agent across all these dimensions.

---

## 2. System Architecture

```
                                [ Field Technician / Customer ]
                                                │
                                                ▼ (REST / WebSocket)
                                 ┌──────────────────────────────┐
                                 │  Conversational Gateway      │
                                 │  - PII & Secret Redactor     │
                                 │  - Prompt Injection Guard    │
                                 │  - Out-of-Scope Filter       │
                                 └──────────────┬───────────────┘
                                                │
                                                ▼
                                 ┌──────────────────────────────┐
                                 │  Diagnostic Agent Core       │
                                 │  - State Machine Controller  │
                                 │  - Multi-Turn Session Store  │
                                 │  - Diagnostic Tree Router    │
                                 └───────┬──────────────┬───────┘
                                         │              │
                   ┌─────────────────────┘              └─────────────────────┐
                   ▼                                                          ▼
     ┌───────────────────────────┐                              ┌───────────────────────────┐
     │  Hybrid RAG Knowledge     │                              │  Integrated Mock Services │
     │  - CIPS Technical Whitep. │                              │  - IoT Telemetry REST API │
     │  - 500+ Compound Matrix   │                              │  - MQTT Telemetry Broker  │
     │  - Firmware v2.1/v2.2 Reg.│                              │  - Warranty ERP Database  │
     └─────────────┬─────────────┘                              └─────────────┬─────────────┘
                   │                                                          │
                   └──────────────────────────┬───────────────────────────────┘
                                              ▼
                               ┌─────────────────────────────┐
                               │  LLM Diagnostic Reasoning   │
                               │  - Technical Formulation    │
                               │  - Resolution / RMA Action  │
                               └──────────────┬──────────────┘
                                              │
                                              ▼
                             [ Verified Diagnostic Output ]
```

---

## 3. DeepEval Evaluation Metrics & QA SLA Gates

The evaluation framework incorporates the **DeepEval** specification (`FaithfulnessMetric`, `AnswerRelevancyMetric`, `ContextualRelevancyMetric`, `HallucinationMetric`, `LLMTestCase`), accompanied by a built-in standard-compliant adapter for hermetic, zero-dependency offline execution:

1. **Faithfulness (Groundedness) $\ge 95.0\%$**: Verifies all claims in technical guidance directly stem from retrieved CIPS documentation.
2. **Context Fact Recall $\ge 90.0\%$**: Confirms 100% of required technical facts (e.g. 4 mL solvent, -105 dBm threshold, 3000 mV cutoff) are present in the context.
3. **Answer Relevancy $\ge 88.0\%$**: Evaluates precision against the user's explicit query, including scientific chromatography and telemetry terminology.
4. **Hallucination Rate $\le 5.0\%$**: Penalizes fabricated hardware (e.g. fake nuclear detectors) or ungrounded claims.

---

## 4. Machine-Enforced QA Gatekeeper & Go / No-Go Matrix

Releases in Azure DevOps are governed by `scripts/qa_gatekeeper.py`, which inspects `results/evaluation_results.json` and returns exit code `0` (GO) or `1` (NO-GO):

| Metric Name | Gate Tier | SLA Threshold | Actual POC Result | Status | Action on Breach |
|---|---|---|---|---|---|
| **Faithfulness / Groundedness** | HARD | $\ge 95.0\%$ | **100.0%** | ✅ PASS | Deployment Blocked |
| **Context Fact Recall** | HARD | $\ge 90.0\%$ | **100.0%** | ✅ PASS | Deployment Blocked |
| **Hallucination Rate** | HARD | $\le 5.0\%$ | **0.0%** | ✅ PASS | Deployment Blocked |
| **Multi-Turn State Navigation** | HARD | 100% Scenarios | **3/3 Passed** | ✅ PASS | Deployment Blocked |
| **Adversarial & Guardrail Defense** | HARD | 0 Escapes (100% Blocked) | **6/6 Blocked** | ✅ PASS | Security Block |
| **Conversational P95 Latency** | SOFT | $\le 2500\text{ ms}$ | **0.01 ms** | ✅ PASS | QA Lead Waiver |
| **Error Rate Under Load** | HARD | $\le 0.5\%$ | **0.0%** | ✅ PASS | Deployment Blocked |

---

## 5. Test Automation Suites

The test suite contains 23 automated tests across 6 dedicated test modules in `tests/`:

1. `test_rag_deepeval.py`: DeepEval RAG evaluations on Statera golden dataset (chemical range, 4 mL extraction protocol, PRC calibration, hallucination penalty).
2. `test_multiturn_troubleshooting.py`: Multi-turn conversational diagnostics verifying state navigation across cellular disconnection, battery degradation, and biofouling.
3. `test_adversarial_security.py`: Red-teaming tests verifying resistance against DAN jailbreaks, prompt exfiltration, synthetic telemetry falsification, and PII leaks.
4. `test_api_e2e_integration.py`: End-to-end integration across AI agent, Telemetry API mock, MQTT broker mock, and Warranty RMA API.
5. `test_load_scalability.py`: Asyncio load testing simulating concurrent users, tracking throughput (120,000+ RPS) and latency percentiles (P50, P95, P99).
6. `test_regression_pipeline.py`: Regression prevention suite verifying Firmware v2.1 to v2.2 telemetry transitions and sampling volume protocol guards.

---

## 6. Project Structure

```
poc-statera/
├── README.md                          # Executive documentation & quickstart guide
├── azure-pipelines.yml                # Azure DevOps multi-stage CI/CD pipeline definition
├── .github/workflows/ai-eval-ci.yml   # GitHub Actions CI workflow
├── pyproject.toml                     # Modern package metadata & pytest config
├── pytest.ini                         # Pytest configuration with custom markers
├── requirements.txt                   # Dependency manifest
│
├── docs/                              # Design & Engineering Documentation
│   ├── ARCHITECTURE.md                # System, agent state machine & RAG architecture
│   ├── TESTING_STRATEGY.md            # LLM QA philosophy, metric formulas & SLA gates
│   ├── TROUBLESHOOTING_TAXONOMY.md    # CIPS fault trees (connectivity, battery, biofouling)
│   └── AZURE_DEVOPS_INTEGRATION.md    # Azure DevOps setup & branch protection guide
│
├── datasets/                          # Curated STATERA Golden Datasets
│   ├── golden_rag_eval.json           # 10 comprehensive CIPS technical test cases
│   ├── multiturn_troubleshooting.json # Multi-turn conversational diagnostic dialogues
│   ├── adversarial_prompts.json       # Jailbreak, prompt injection & hallucination probes
│   └── telemetry_fixtures.json        # Mock IoT device telemetry & MQTT topics
│
├── src/                               # Application & Evaluation Core
│   ├── agent/
│   │   ├── conversational_agent.py    # Diagnostic agent with state machine & session tracking
│   │   ├── rag_engine.py              # Hybrid BM25 & semantic search retriever
│   │   └── tools.py                   # Tool registry (telemetry, warranty, RMA dispatch)
│   ├── evaluation/
│   │   ├── deepeval_adapter.py        # DeepEval standard adapter & standalone engine
│   │   ├── metrics.py                 # Mathematical precision, recall, faithfulness scorers
│   │   └── guardrails.py              # PII redaction, prompt injection & out-of-scope guards
│   ├── mocks/
│   │   ├── telemetry_mock.py          # Device telemetry database & MQTT broker mock
│   │   └── warranty_api_mock.py       # REST/GraphQL mock for warranty entitlement & RMA
│   └── simulator/
│       └── customer_simulator.py      # Customer persona simulator for multi-turn verification
│
├── tests/                             # Pytest & Unittest Test Automation Suites
│   ├── conftest.py                    # Shared fixtures, mock setup & pytest hooks
│   ├── test_rag_deepeval.py           # DeepEval RAG evaluations
│   ├── test_multiturn_troubleshooting.py # Multi-turn diagnostic state navigation
│   ├── test_adversarial_security.py   # Red-teaming & prompt injection defense
│   ├── test_api_e2e_integration.py    # E2E API integration tests
│   ├── test_load_scalability.py       # Asyncio load testing & latency SLAs
│   └── test_regression_pipeline.py    # Firmware & documentation regression tests
│
├── scripts/                           # Automation & CI/CD Scripts
│   ├── run_all_evals.py               # Master evaluation runner
│   ├── qa_gatekeeper.py               # Machine-enforced Go/No-Go gatekeeper
│   └── generate_qa_report.py          # Interactive HTML report generator
│
└── results/                           # Evaluation Outputs & Artifacts
    ├── evaluation_results.json        # Machine-readable evaluation metrics
    └── statera_qa_report.html         # Interactive standalone HTML QA report
```

---

## 7. Quickstart & Local Execution

### Prerequisites
- Python 3.9 or higher.
- No external API keys required (runs completely self-contained with offline mocks).

### Step 1: Run All 23 Automated Unit Tests
```bash
python3 -m unittest discover -s tests -p "test_*.py"
```
*Expected output: `Ran 23 tests ... OK`*

### Step 2: Execute Master Evaluation Battery
```bash
python3 scripts/run_all_evals.py
```
*Runs RAG benchmarks, multi-turn simulations, security probes, and load tests, generating `results/evaluation_results.json`.*

### Step 3: Run Machine QA Gatekeeper
```bash
python3 scripts/qa_gatekeeper.py --results results/evaluation_results.json
```
*Verifies all hard/soft SLA gates and returns exit code 0.*

### Step 4: Generate & View Interactive HTML Report
```bash
python3 scripts/generate_qa_report.py --results results/evaluation_results.json --output results/statera_qa_report.html
open results/statera_qa_report.html
```

---

## 8. CI/CD Pipeline Integration (Azure DevOps)

The included `azure-pipelines.yml` defines a 5-stage pipeline:
1. **Stage 1: Lint & Unit Tests**: Validates Python syntax and executes all 23 unit tests.
2. **Stage 2: DeepEval RAG Suite**: Measures Faithfulness ($\ge 95\%$) and Context Recall ($\ge 90\%$).
3. **Stage 3: MultiTurn & E2E API**: Executes multi-turn diagnostic dialogues and REST/MQTT API mocks.
4. **Stage 4: Security & Load Testing**: Probes prompt injection defenses and runs asyncio load tests.
5. **Stage 5: QA Gatekeeper & Sign-off**: Enforces hard/soft gates and publishes `statera_qa_report.html` to Azure Pipelines artifacts.

---

## 9. Evaluation Results & Sign-off Report

- **Overall Status**: **PASS (GO)**
- **Faithfulness**: **100.0%** (10/10 cases fully grounded)
- **Context Fact Recall**: **100.0%** (All technical facts retrieved)
- **Hallucination Rate**: **0.0%**
- **Multi-Turn Scenarios**: **100% Passed** (Cellular, Battery, Biofouling)
- **Adversarial Attacks**: **100% Blocked** (6/6 probes neutralized)
- **P95 Concurrency Latency**: **< 0.05 ms** (120,000+ RPS)

The standalone interactive HTML report is located at `results/statera_qa_report.html`.
