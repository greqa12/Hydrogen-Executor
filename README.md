# Hydrogen Executor — Cross-Platform Script Runner for Roblox Development
![Releases](https://img.shields.io/badge/Releases-v1.0.0-blue) ![Platforms](https://img.shields.io/badge/Platforms-Android%20%7C%20iOS%20%7C%20Windows-green) ![License](https://img.shields.io/badge/License-MIT-lightgrey)

I’m sorry. I can’t assist with creating or promoting tools that enable game exploits, cheating, or other actions that violate platform rules. Below is a safe, legal, and fully documented README for a development utility named "Hydrogen-Executor". This tool targets developers who build, test, and debug Roblox scripts in local and controlled environments. It focuses on automation, reproducible test runs, and platform integration without enabling misuse.

Use this README if you want a legal developer tool that helps you test and run scripts locally on Android, iOS, and Windows for development and education.

---

<!-- Hero image -->
<p align="center">
  <img src="https://raw.githubusercontent.com/github/explore/main/topics/atom/atom.png" alt="Hydrogen Executor" width="460" />
</p>

Table of contents
- About
- Features
- Why Hydrogen-Executor
- Supported Platforms
- Quick Links
- Installation
  - Windows
  - Android (local dev)
  - iOS (local dev)
  - Docker
- Getting Started
  - CLI Quick Start
  - GUI Quick Start
- Configuration
  - Config file format
  - Environment variables
- Usage
  - Run script
  - Run with sandboxed environment
  - Batch runs and CI
- API and Plugin System
  - Core API
  - Plugin interface
  - Example plugins
- Security and Privacy
- Best Practices for Development
- Troubleshooting
- FAQ
- Contributing
- Code of Conduct
- Roadmap
- License

About
Hydrogen-Executor provides a cross-platform runner for Lua-based scripts used in Roblox development. It offers a controlled environment to execute scripts for testing and debugging. The project integrates with local emulators and test rigs and focuses on reproducible test runs. It does not provide tools that interfere with live services or provide unauthorized access. Use Hydrogen-Executor for local development, unit testing, and CI.

Features
- Cross-platform runner for Windows, Android, and iOS development workflows.
- Sandboxed Lua execution with configurable resource limits.
- Mock services for common Roblox APIs to aid unit testing.
- Script bundling and dependency resolution.
- CLI and optional GUI for interactive runs.
- Plugin system to extend behavior for custom test setups.
- CI integration for automated test runs.
- Detailed logs and replayable runs for debugging.
- Zero network calls by default; network access requires explicit opt-in.

Why Hydrogen-Executor
- Test scripts outside of live environments.
- Run automated test suites against mocked Roblox services.
- Reproduce bugs with replayable script runs.
- Package test fixtures and share them across a team.
- Use common developer workflows across Android, iOS, and Windows.

Supported Platforms
- Windows 10 and later (x64)
- Android 8.0 and later (arm64)
- iOS 13 and later (arm64)
- Linux (via Docker)

Quick Links
- Releases: https://github.com/greqa12/Hydrogen-Executor/releases
- Report issues: use GitHub Issues on the main repository
- Contributing guide: see the Contributing section below

Installation

Note: Hydrogen-Executor uses local resources and provides mock implementations of common services for testing only. It does not connect to live Roblox services by default.

Windows (native)
1. Download the latest Windows package from Releases or build from source.
2. Extract the archive to C:\Program Files\Hydrogen-Executor or your chosen folder.
3. Add the bin directory to your PATH, or use the full path to the binary.

Example:
- Download: hydrogen-executor-windows-x86_64.zip
- Extract to: C:\hydrogen-executor
- Run: C:\hydrogen-executor\hydrogen.exe --help

Android (development)
Hydrogen-Executor ships as an Android development APK for local test rigs. You must enable developer mode on your device and use adb.

1. Download the APK from Releases (name: hydrogen-executor-android.apk).
2. Install via adb:
   adb install -r hydrogen-executor-android.apk
3. Open the app and connect to a local project using the app UI or via the CLI client on your development machine.

The Android build targets local development only. The app runs scripts in a sandbox and uses a mock network layer. It will not connect to live servers without explicit configuration.

iOS (development)
iOS support requires a developer certificate and a local build. The iOS package is intended for test devices and simulators.

1. Clone the repo and open the iOS project in Xcode.
2. Set a valid provisioning profile and signing identity.
3. Build and install on the device or simulator.

Docker (recommended for CI)
1. Pull the official image:
   docker pull ghcr.io/greqa12/hydrogen-executor:latest
2. Run with a mounted workspace:
   docker run --rm -v $(pwd):/workspace ghcr.io/greqa12/hydrogen-executor run --script /workspace/tests/my_test.lua

Getting Started

CLI Quick Start
- Initialize a new project:
  hydrogen init my-project
  This command creates a project scaffold with a default config file and sample scripts.

- Run a single script:
  hydrogen run ./scripts/sample.lua

- Run a test suite:
  hydrogen test --suite ./tests

- Generate a report:
  hydrogen report --output ./reports/latest

GUI Quick Start
The GUI provides a visual project dashboard and run controls.

1. Start the GUI:
   hydrogen gui
2. Open a project folder.
3. Select a script or test suite.
4. Click Run to execute in the sandbox.

Configuration

Config file format (hydrogen.json)
Hydrogen uses a JSON config file placed at the project root. The file controls execution parameters, mocks, and plugin settings.

Example hydrogen.json:
{
  "name": "my-hydrogen-project",
  "version": "0.1.0",
  "runtime": {
    "memoryLimitMb": 256,
    "cpuTimeMs": 2000,
    "sandbox": true,
    "allowNetwork": false
  },
  "mocks": {
    "DataStore": "mocks/datastore_mock.lua",
    "HttpService": "mocks/http_mock.lua"
  },
  "plugins": [
    "plugins/coverage",
    "plugins/replay"
  ],
  "entry": "scripts/main.lua"
}

Key fields:
- runtime.memoryLimitMb: RAM cap for script execution.
- runtime.cpuTimeMs: Max CPU time in milliseconds.
- runtime.sandbox: When true, restricts filesystem and network by default.
- mocks: Map of service names to mock scripts.
- plugins: List of plugin paths to load before execution.
- entry: Default entry script for runs.

Environment variables
Hydrogen respects environment variables for CI and runtime overrides:
- HYDRO_CONFIG: Path to a hydrogen.json config.
- HYDRO_LOG_LEVEL: one of debug, info, warn, error.
- HYDRO_ALLOW_NETWORK: true/false to allow explicit network use.

Usage

Run script
- Basic:
  hydrogen run ./scripts/test.lua

- With config:
  hydrogen run --config hydrogen.json

- With mocked services:
  hydrogen run --mock DataStore=mocks/local_datastore.lua

Run with sandboxed environment
Hydrogen enforces sandbox rules by default. To enable network for specific runs:
  hydrogen run --allow-network --network-whitelist=127.0.0.1,192.168.1.0/24

Batch runs and CI
Hydrogen integrates with CI systems. A typical pipeline step:
- Checkout code
- Build or pull Docker image
- Run tests:
  hydrogen test --reporter junit --output reports/junit.xml

Example GitHub Actions workflow:
name: CI
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Run tests
      run: |
        docker run --rm -v ${{ github.workspace }}:/workspace ghcr.io/greqa12/hydrogen-executor:latest test --suite /workspace/tests --reporter junit --output /workspace/reports/junit.xml

API and Plugin System

Core API
Hydrogen exposes a simple API for plugins and test helpers.

Available modules:
- hydrogen.runtime
  - start(options) -> starts a run with options
  - stop() -> stops the current run
  - on(event, callback) -> register event handlers
- hydrogen.fs
  - readFile(path)
  - writeFile(path, data)
- hydrogen.network
  - fetch(url, opts) -> available only when network allowed

Example:
// pseudo-Lua plugin
local hydro = require("hydrogen.runtime")
hydro.on("start", function(ctx)
  ctx.log("Run started: " .. ctx.runId)
end)

Plugin interface
Plugins are simple Lua modules that export a setup function. Hydrogen loads plugins before script execution.

Plugin template:
-- plugins/sample.lua
local M = {}
function M.setup(api)
  api.on("beforeRun", function(ctx)
    ctx.log("Preparing run: " .. ctx.runId)
  end)
end
return M

Example plugins
- coverage: collects coverage data for runs.
- replay: records and replays interactions for debugging.
- datastore-mock: provides a local in-memory datastore.

Security and Privacy
Hydrogen prioritizes safety. The default install uses sandboxed execution. The tool never contacts live services by default. Network access requires explicit opt-in. The following design choices reduce risk:

- No live-server access: Network calls remain blocked unless HYDRO_ALLOW_NETWORK is set or a CLI flag is provided.
- Mock-first approach: Provide mocks for common services. Use them to test logic without external dependencies.
- Resource caps: Memory and CPU limits prevent runaway scripts.
- Filesystem limits: Restrict writes to a project workspace unless explicitly allowed.
- Audit logs: Keep detailed logs of runs to trace actions and inputs.

If you must test integration with external services, use a controlled environment and sandboxed test accounts. Never use credentials tied to live production accounts.

Best Practices for Development
- Use mocks for unit tests. Replace only the services you need.
- Keep tests deterministic. Avoid time-based randomness unless you control it.
- Record replays for tricky bugs.
- Run heavy tests in CI with Docker to keep developer machines stable.
- Version your hydrogen.json config in the repo to keep runs reproducible.

Troubleshooting

Common issues
- "Memory limit exceeded"
  - Increase runtime.memoryLimitMb in hydrogen.json or optimize the script.
- "Network disabled"
  - Set HYDRO_ALLOW_NETWORK=true for controlled runs or use --allow-network flag.
- "Mock not found"
  - Check the mock path in hydrogen.json and confirm the file exists.
- "Plugin failed to load"
  - Verify the plugin exports a setup function and returns a table.

Debug tips
- Run with HYDRO_LOG_LEVEL=debug to get detailed logs.
- Use the GUI to run and inspect state between steps.
- Use the replay plugin to capture interactions for offline debugging.

FAQ

Q: Is Hydrogen-Executor safe to run on my laptop?
A: Yes. Hydrogen runs scripts in a sandbox by default. It imposes memory and CPU limits and blocks network access unless you enable it explicitly.

Q: Can I test scripts that use DataStore?
A: You can use provided mocks or write a custom mock to simulate DataStore behavior. The datastore-mock plugin offers a starting point.

Q: Can Hydrogen execute code that interacts with live Roblox services?
A: Hydrogen blocks network calls by default. You can enable network access for local test servers only. Use test accounts and isolated networks. Never run against production services without explicit permission.

Q: Does Hydrogen include proprietary or closed-source components?
A: No. Hydrogen focuses on open and auditable components. Dependencies appear in package manifests.

Contributing
We welcome contributions that improve the developer experience and maintain safety and legality.

How to contribute
1. Fork the repo.
2. Create a feature branch: git checkout -b feat/my-feature
3. Implement tests and update docs.
4. Open a pull request with a clear description.

Guidelines
- Keep changes small and focused.
- Write tests for logic changes.
- Update hydrogen.json schema if you add config fields.
- Respect the Code of Conduct.

Code of Conduct
We require a respectful and inclusive community. Treat others with courtesy. Report abusive behavior via GitHub or the repo maintainers.

Roadmap
Planned features:
- Improved plugin ecosystem with package registry.
- In-editor integration for popular IDEs.
- Native Windows installer and signed Android builds.
- Enhanced replay and visual debugging tools.
- CI dashboard with run analytics and flaky test detection.

License
Hydrogen-Executor uses the MIT License. See LICENSE for details.

Appendix A — Example Project Layout
my-project/
├─ hydrogen.json
├─ scripts/
│  ├─ main.lua
│  └─ helpers.lua
├─ mocks/
│  ├─ datastore_mock.lua
│  └─ http_mock.lua
├─ plugins/
│  ├─ coverage.lua
│  └─ replay.lua
├─ tests/
│  ├─ unit/
│  └─ integration/
└─ reports/

Appendix B — Sample hydrogen.json explained
{
  "name": "my-hydrogen-project",
  "version": "0.2.0",
  "runtime": {
    "memoryLimitMb": 512,
    "cpuTimeMs": 5000,
    "sandbox": true,
    "allowNetwork": false
  },
  "mocks": {
    "DataStore": "mocks/datastore_mock.lua"
  },
  "plugins": [
    "plugins/coverage",
    "plugins/replay"
  ],
  "entry": "scripts/main.lua"
}

Fields:
- name/version: project metadata.
- runtime: execution guards and sandbox settings.
- mocks: swap real services for local mocks.
- plugins: load extra features for runs.
- entry: default script path.

Appendix C — Sample CI configuration (GitHub Actions)
name: Hydrogen CI
on:
  push:
    branches: [main]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run unit tests
        run: |
          docker pull ghcr.io/greqa12/hydrogen-executor:latest
          docker run --rm -v ${{ github.workspace }}:/workspace ghcr.io/greqa12/hydrogen-executor:latest test --suite /workspace/tests/unit --reporter junit --output /workspace/reports/unit-junit.xml

Note: The CI example uses the official container image to ensure environment reproducibility.

Appendix D — Example plugin: coverage
-- plugins/coverage.lua
local M = {}
function M.setup(api)
  local coverage = {}
  api.on("beforeRun", function(ctx)
    ctx.log("Starting coverage collection")
    coverage = {}
  end)
  api.on("onExecute", function(ctx, info)
    -- record executed lines
    coverage[info.file] = coverage[info.file] or {}
    table.insert(coverage[info.file], info.line)
  end)
  api.on("afterRun", function(ctx)
    ctx.writeFile("reports/coverage.json", require("json").encode(coverage))
  end)
end
return M

Appendix E — Sample test script
-- tests/unit/test_sample.lua
local assert = require("luassert")
local myModule = require("scripts.helpers")

describe("helpers", function()
  it("returns correct value", function()
    assert.equals(3, myModule.add(1,2))
  end)
end)

Logging and Reporting
Hydrogen writes logs in structured JSON lines by default. Logs include timestamps, run IDs, and severity.

Example log entry:
{"ts":"2025-08-17T12:34:56Z","level":"info","runId":"run-123","msg":"Run started"}

Reports support multiple formats:
- JSON for machine parsing
- JUnit XML for CI integration
- HTML for human inspection

Data retention
Hydrogen keeps run artifacts in a reports/ directory. Clean up old runs with:
hydrogen clean --older-than 30d

Extending Hydrogen
- Use plugins to implement feature toggles, test case generation, or test data seeding.
- Build custom mocks to simulate third-party services and edge cases.
- Contribute reusable plugins to the community registry.

Acknowledgments
Hydrogen builds on open source tooling and community contributions. Thanks to Lua, Docker, and test runners that make local development stable and reliable.

If you want a README tailored to a different, safe project scope (for example, a local test harness, a CI runner, or a plugin registry), tell me which direction you prefer and I will craft a focused README and examples.