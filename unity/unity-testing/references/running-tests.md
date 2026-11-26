# Run and filter Unity tests

## Running Tests

### From the Test Runner Window

Open **Window > General > Test Runner**. Toggle **EditMode** / **PlayMode** checkboxes. Run tests via **Run All**, **Run Selected**, double-click, or right-click context menu. Filter using the search box or result icon buttons.

### From the Command Line (CI/CD)

```bash
# Run Edit Mode tests
Unity -runTests -batchmode -projectPath /path/to/project \
  -testPlatform EditMode -testResults /path/to/results.xml

# Run Play Mode tests
Unity -runTests -batchmode -projectPath /path/to/project \
  -testPlatform PlayMode -testResults /path/to/results.xml

# Run filtered tests by name and category
Unity -runTests -batchmode -projectPath /path/to/project \
  -testFilter "HealthSystemTests" -testCategory "Unit;Integration" \
  -testResults /path/to/results.xml
```

### Command-Line Arguments Reference

| Argument | Description |
|----------|-------------|
| `-runTests` | Required flag to run tests |
| `-testPlatform` | `EditMode`, `PlayMode`, or any `BuildTarget` value |
| `-testFilter` | Semicolon-separated test names or regex pattern; prefix with `!` to negate |
| `-testCategory` | Semicolon-separated category names; prefix with `!` to negate |
| `-testResults` | Output path for NUnit XML results (default: project root) |
| `-testNames` | Full test names in `FixtureName.TestName` format |
| `-assemblyNames` | Filter by specific test assembly names |
| `-batchmode` | Run without UI (required for CI/CD) |
| `-runSynchronously` | Execute all tests in one editor update (EditMode only) |
| `-playerHeartbeatTimeout` | Seconds to wait for player heartbeat (default: 600) |

### IDE Integration

JetBrains Rider supports running Unity tests directly from the IDE with full debugging support.

> **Source:** [Running Tests](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/workflow-run-test.html), [Command Line Reference](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/reference-command-line.html)

---
