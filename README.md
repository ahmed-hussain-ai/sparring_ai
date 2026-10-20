<p align="center">
  <img src="assets/hero.svg" alt="Sparring — QA and red-teaming for AI agents. 2 real defects found, 15 MCP security checks, leak rate cut 30% to 10%, $0.95 for the whole project" width="100%"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Red%20teaming-D06CC0?style=for-the-badge&labelColor=101018" alt="Red teaming"/>
  <img src="https://img.shields.io/badge/LLM%20judge-verified-9B8CFF?style=for-the-badge&labelColor=101018" alt="LLM judge, verified"/>
  <img src="https://img.shields.io/badge/MCP%20security-15%20checks-3BA4F0?style=for-the-badge&labelColor=101018" alt="MCP security, 15 checks"/>
  <img src="https://img.shields.io/badge/Tests-24%20passing-3DD68C?style=for-the-badge&labelColor=101018" alt="24 tests"/>
  <img src="https://img.shields.io/badge/Budget-%240.95%20of%20%241-F5A044?style=for-the-badge&labelColor=101018" alt="Budget"/>
</p>

<p align="center">
  <a href="#01"><b>Why</b></a> &nbsp;·&nbsp; <a href="#02"><b>Features</b></a> &nbsp;·&nbsp; <a href="#03"><b>How it works</b></a> &nbsp;·&nbsp; <a href="#04"><b>Screens</b></a> &nbsp;·&nbsp; <a href="#05"><b>Results</b></a> &nbsp;·&nbsp; <a href="#06"><b>Production</b></a> &nbsp;·&nbsp; <a href="#08"><b>Limits</b></a>
</p>

<br/>

<a id="01"></a>
<img src="assets/s01.svg" alt="01 · Why Sparring" width="100%"/>

**Sparring tests customer-facing AI agents the way real customers and attackers would.** Connect a chatbot, agent or MCP server, describe what it does and what it must never do, and Sparring writes a test plan, holds the conversations itself, grades them, checks its own grader, reports every failure with evidence and a suggested fix, and can gate a release in CI.

<p align="center"><img src="assets/problem-solution.svg" alt="How agents usually ship vs with Sparring" width="100%"/></p>

> [!NOTE]
> **Why I built it.** I build agents for real businesses (Vendra, LeadMaster, AI Persona). Each had its own ad-hoc eval script, and none tested the multi-turn failures that actually hurt a business: an invented price, a rule bent under pressure, a leak between tenants. So I built one harness that tests any agent, and proved it on my own multi-tenant agent first.

<br/>

<a id="02"></a>
<img src="assets/s02.svg" alt="02 · What it does" width="100%"/>

<p align="center"><img src="assets/features.svg" alt="Connect any agent, auto test plan, adaptive red-teaming, MCP security probe, verifiable judge, auto-fix and CI gate" width="100%"/></p>

<br/>

<a id="03"></a>
<img src="assets/s03.svg" alt="03 · How a run works" width="100%"/>

<p align="center"><img src="assets/architecture.svg" alt="Sparring run: profile, planner, test plan, conversation runner, agent under test, LLM judge, evidence check, report, auto-fix loop" width="100%"/></p>

<details>
<summary><b>How the auto-fix loop decides</b> (flowchart)</summary>
<br/>

```mermaid
%%{init: {'theme':'dark', 'themeVariables': {'primaryColor':'#181822','primaryTextColor':'#F5F5F8','primaryBorderColor':'#D06CC0','lineColor':'#9B8CFF'}}}%%
flowchart LR
    F[Failing scenarios] --> S{Split}
    S -->|train| T[Optimiser proposes<br/>an instruction change]
    S -->|unseen| U[Never shown<br/>to the optimiser]
    S -->|held out| H[Passing scenarios<br/>that must stay passing]
    T --> RR[Re-run all three sets]
    U --> RR
    H --> RR
    RR --> D{Fixes failures<br/>without regressions?}
    D -->|yes| K[Keep: propose the fix]
    D -->|no| X[Discard and say so]
```

</details>

<br/>

<a id="04"></a>
<img src="assets/s04.svg" alt="04 · Inside the dashboard" width="100%"/>

<p align="center"><img src="assets/run-detail.png" alt="A run against a test dental business on my sales agent: score, failures, spend, latency and every scenario with its verdict" width="100%"/></p>
<p align="center"><sub><b>A run.</b> 14 scenarios against a test business on my own sales agent, each with a verdict, a severity and a one-line reason.</sub></p>

<p align="center"><img src="assets/finding.png" alt="One finding: the attack goal, quoted evidence from the transcript, and a suggested fix" width="100%"/></p>
<p align="center"><sub><b>A finding.</b> The attack goal, the quoted evidence from the transcript, the turn it happened on, and a suggested fix.</sub></p>

<table>
  <tr>
    <td width="50%" align="center"><img src="assets/mcp-probe.png" alt="MCP security probe: 14 of 15 checks passed"/><br/><sub><b>MCP security probe.</b> 15 checks; 1 real finding.</sub></td>
    <td width="50%" align="center"><img src="assets/judge.png" alt="Judge check: label conversations blind and compare with the judge"/><br/><sub><b>Judge check.</b> Blind human labels vs the judge.</sub></td>
  </tr>
  <tr>
    <td width="50%" align="center"><img src="assets/agent-editor.png" alt="Connect an agent over HTTP or MCP and try the connection"/><br/><sub><b>Connect an agent.</b> HTTP or MCP, tested live.</sub></td>
    <td width="50%" align="center"><img src="assets/fixes.png" alt="Fixes: a candidate change discarded because it did not generalise"/><br/><sub><b>Fixes.</b> A candidate that didn't generalise, discarded.</sub></td>
  </tr>
</table>

<sub>All conversations shown are with fictional test businesses and fictional customers.</sub>

<br/>

<a id="05"></a>
<img src="assets/s05.svg" alt="05 · Measured results" width="100%"/>

<p align="center"><img src="assets/results.svg" alt="14 of 15 MCP checks passed, 9 of 14 conversations leaked internal sources, leak rate cut from 30% to 10%, $0.95 for the whole project" width="100%"/></p>

| What it found on my own sales agent | Detail |
|---|---|
| **Defect 1: insecure OAuth redirect rule** | The MCP probe registered a plain-http, non-loopback redirect URI and the server accepted it (HTTP 201). 14 of 15 checks passed. Found on a local copy of the server code. |
| **Defect 2: internal-source leak** | In 9 of 14 conversations (18 of 52 replies) the agent told customers about its "approved materials". |
| **Attacks resisted** | No fake discount was confirmed, no other business's data was shared. |
| **Auto-fix, honestly** | Prompt changes fixed 2 of 5 seen failures and 0 of 3 unseen ones, so no prompt fix was proposed. The evidence pointed at the retrieval code. |
| **The real fix** | A change in the retrieval code cut internal-source mentions from 6 of 20 replies to 2 of 20 (30% → 10%). |

<br/>

<a id="06"></a>
<img src="assets/s06.svg" alt="06 · Built for production" width="100%"/>

| Decision | Why |
|---|---|
| **Postgres-checkpointed runs** | Every turn is saved. A run killed with `kill -9` resumes mid-conversation; a message already sent is never sent twice. |
| **Budget reserved before every call** | The worst-case cost is reserved first, so a run can never exceed its cap. The whole project cost $0.95 of a $1.00 hard limit. |
| **Per-run advisory lock** | Two processes can never drive the same run. |
| **Evidence-checked judge** | Every finding must quote the transcript; code drops quotes that aren't there or that just restate the reference facts. |
| **Train / unseen / held-out split** | A fix only counts if it generalises and breaks nothing that was passing. |
| **No agent framework** | The harness is plain Python, so retries, checkpoints and budget rules are explicit and testable. |
| **24 automated tests + CI gate** | Tested end to end: a run started from the API, scored, and a pass/fail exit code for CI. |

<br/>

<a id="07"></a>
<img src="assets/s07.svg" alt="07 · Tech stack" width="100%"/>

<p align="center">
  <img src="https://skillicons.dev/icons?i=python,fastapi,react,postgres,docker,nginx,githubactions,linux&theme=dark" alt="Python, FastAPI, React, PostgreSQL, Docker, Nginx, GitHub Actions, Linux"/>
</p>
<p align="center">
  <img src="https://img.shields.io/badge/OpenAI-101018?style=for-the-badge&logo=openai&logoColor=F5F5F8" alt="OpenAI"/>
  <img src="https://img.shields.io/badge/MCP-101018?style=for-the-badge&logo=anthropic&logoColor=D06CC0" alt="MCP"/>
  <img src="https://img.shields.io/badge/OAuth%202.1-101018?style=for-the-badge&logo=auth0&logoColor=3DD68C" alt="OAuth 2.1"/>
  <img src="https://img.shields.io/badge/pytest-101018?style=for-the-badge&logo=pytest&logoColor=3BA4F0" alt="pytest"/>
</p>

<br/>

<a id="08"></a>
<img src="assets/s08.svg" alt="08 · Honest limits" width="100%"/>

- **Single runs.** The $1 budget allowed one run per experiment, so a one-scenario difference is noise and the 30% → 10% result is directional.
- **Judge agreement is not measured yet.** The blind labelling page is built; the agreement figures come after enough conversations are labelled.
- **Judge model matters.** Switching the judge from gpt-5-nano to gpt-5-mini changed 2 of 14 verdicts on identical transcripts.
- **Prompt auto-fix gains were modest** (2 of 5 seen, 0 of 3 unseen). That is why a fix is only proposed when re-runs prove it.

<br/>

<p align="center">
  <a href="https://ahmedhussain.tech"><img src="assets/footer.svg" alt="Built by Ahmed Hussain" width="100%"/></a>
</p>
