 # The Legacy Bank Audit — CTF Play Box

**SLIIT IE3132 Penetration Testing · Year 3, Semester 1 · 2026 · Group 78**

The Legacy Bank Audit is a Docker-based Capture The Flag (CTF) lab that simulates an authorized red-team assessment of a fictional bank's forgotten staging environment. Players work through six connected challenges covering reconnaissance, web security, cryptography, authentication, Linux enumeration, and privilege escalation.

> [!CAUTION]
> This is an **intentionally vulnerable educational lab**. Run it only on a machine and network you control. Do not deploy it to a public server or use its techniques against systems without explicit authorization. The repository includes challenge flags and solution clues in its source, so keep it **private** unless your lecturer and all group members approve public release.

## Learning path

The challenge sequence follows **Discover → Access → Decode → Authenticate → Enumerate → Escalate**.

| Stage | Challenge | Topic | Goal |
|---|---|---|---|
| 1 | The Forgotten Server | Networking and reconnaissance | Discover the legacy staging service and its clues. |
| 2 | The Legacy Employee Portal | Web application security | Identify the unsafe login behavior and retrieve the stage flag. |
| 3 | The Encoded Credentials | Encoding and classical cryptography | Recover credentials protected by multiple reversible layers. |
| 4 | The Broken Second Factor | Authentication | Analyze and reproduce a predictable legacy second-factor calculation. |
| 5 | Inside the Legacy Server | Linux and system enumeration | Connect to the lab server and inspect scheduled tasks and permissions. |
| 6 | The Root Scheduler | Linux privilege escalation | Demonstrate the impact of a misconfigured scheduled task in the isolated lab. |

## Technology stack

- Python and Flask for the dashboard and web challenge
- SQLite for dashboard and challenge data
- Docker and Docker Compose for repeatable, isolated services
- Linux, OpenSSH, cron, `nmap`, and `curl` for the lab exercises
- HTML, CSS, and JavaScript for the web interfaces

## Architecture and local ports

| Component | Purpose | Local access |
|---|---|---|
| `dashboard` | CTF instructions, stage progression, hints, and scoring | `http://localhost:5001` |
| `web_challenge` | Intentionally vulnerable employee portal | `http://127.0.0.1:8082` (bound to localhost) |
| `kali_player` | Container with network tools for Stage 1 | Open a shell with `docker compose exec kali_player bash` |
| `linux_challenge` | Isolated Linux/SSH target for later stages | No host port is published; access it from the lab network |

The Compose project creates an internal `ctf_isolated_net` network for the lab. The web challenge is also attached to the dashboard ingress network and its host port is bound to loopback (`127.0.0.1`) for local testing. This reduces accidental exposure, but it does **not** make the intentionally vulnerable application suitable for public deployment.

## Prerequisites

- Docker Desktop running with Linux containers enabled (Windows), or Docker Engine on Linux/macOS
- Docker Compose v2 (`docker compose`)
- Git, if cloning the repository
- Python 3.10+ only if you want to run the Python unit tests outside Docker

## Run locally

1. Clone the repository or open the project directory containing `docker-compose.yml`.
2. Create a local environment file from the example.

   **PowerShell:**
   ```powershell
   Copy-Item .env.example .env
   notepad .env
   ```

   Replace the placeholder secret values and set a local team username and password before the first start. To generate a secret in PowerShell, run this command twice and use different outputs:

   ```powershell
   python -c "import secrets; print(secrets.token_hex(32))"
   ```

   **macOS/Linux:**
   ```bash
   cp .env.example .env
   nano .env
   ```

3. Build and start the services:
   ```bash
   docker compose up -d --build
   ```
4. Check container status:
   ```bash
   docker compose ps
   ```
5. Open `http://localhost:5001` in your browser and sign in using the team credentials configured in `.env`.
6. For Stage 1 network tools, open the player container shell:
   ```bash
   docker compose exec kali_player bash
   ```

Do not commit `.env`. It contains local secrets and is excluded by `.gitignore`.

## Health checks and useful commands

Check the dashboard from PowerShell:

```powershell
Invoke-RestMethod http://localhost:5001/health
Invoke-RestMethod http://127.0.0.1:8082/health
```

View service logs:

```bash
docker compose logs dashboard
docker compose logs web_challenge
docker compose logs linux_challenge
```

Stop the services without deleting saved dashboard progress:

```bash
docker compose down
```

**Data warning:** `docker compose down -v` also removes the persistent dashboard volume, including stored teams, scores, and progress. Use it only when you intentionally want to erase that data.

## Run automated tests

From the project root, using PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r dashboard\requirements.txt
python -m unittest discover -s tests -v
```

See [`docs/testing.md`](docs/testing.md) for the current test scope and manual validation checklist. Do not describe the whole six-stage CTF as fully tested unless you have run and recorded an end-to-end validation yourself.

## Repository layout

```text
legacy-bank-ctf/
├── dashboard/       # Flask control dashboard, templates, and seeding
├── web/             # Employee portal challenge
├── linux/           # Linux challenge container configuration
├── player/          # Player tools container
├── flags/           # Stage flag files (keep the repository private)
├── tests/           # Automated unit tests
├── docs/            # Architecture, deployment, security, and testing notes
├── scripts/         # PowerShell helper scripts
├── docker-compose.yml
├── .env.example     # Safe template; copy to .env and replace placeholders
└── README.md
```

## Individual contribution — Member 4

My assigned role was **Integration, Testing & Documentation**. My contribution focused on integration and validation planning across the six-stage flow, reviewing dependencies and risks, documenting test cases and results, considering reset/recovery behavior, and helping assemble the final report. Update this section if your final submitted contribution record differs.
