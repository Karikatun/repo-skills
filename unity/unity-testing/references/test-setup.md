# Test assembly setup and mode selection

## Setup and Configuration

### Installing the Test Framework

UTF ships with Unity by default. Open the Test Runner via **Window > General > Test Runner**.

### Creating a Test Assembly

Test code must live in its own assembly definition (`.asmdef`) that references NUnit.

Create via **Window > General > Test Runner** (click **Create a new Test Assembly Folder**) or **Assets > Create > Testing > Test Assembly Folder**.

This creates a `Tests` folder with an `.asmdef` file preconfigured with references to:
- `nunit.framework.dll`
- `UnityEngine.TestRunner`
- `UnityEditor.TestRunner` (Edit Mode only)

### Edit Mode Assembly Definition (.asmdef)

```json
{
    "name": "Tests",
    "references": [
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner"
    ],
    "includePlatforms": ["Editor"],
    "defineConstraints": ["UNITY_INCLUDE_TESTS"],
    "autoReferenced": false
}
```

### Play Mode Assembly Definition (.asmdef)

```json
{
    "name": "Tests.PlayMode",
    "references": [
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner",
        "MyGameAssembly"
    ],
    "includePlatforms": [],
    "optionalUnityReferences": ["TestAssemblies"],
    "defineConstraints": ["UNITY_INCLUDE_TESTS"],
    "autoReferenced": false
}
```

**Important:** To test your game code, add a reference to the assembly containing the code under test in the `references` array.

> **Source:** [Creating Test Assemblies](https://docs.unity3d.com/Packages/com.unity.test-framework@1.4/manual/workflow-create-test-assembly.html)

---

## Edit Mode vs Play Mode Tests

| Aspect | Edit Mode | Play Mode |
|--------|-----------|-----------|
| **Execution context** | `EditorApplication.update` callback loop | Coroutine on a MonoBehaviour |
| **Editor access** | Full Editor + game code | Game code only (unless in Editor) |
| **Coroutine support** | `yield return null` skips one update | Full yield instructions (`WaitForSeconds`, etc.) |
| **Platform targets** | Editor only | Editor Play Mode + standalone players |
| **Use cases** | Editor extensions, pure logic, serialization | Physics, gameplay, MonoBehaviour lifecycle |
| **Assembly platform** | `"includePlatforms": ["Editor"]` | `"includePlatforms": []` |

**When to use Edit Mode:**
- Testing editor tools, custom inspectors, ScriptableObject logic
- Pure C# logic that does not depend on runtime systems
- Tests that need access to `UnityEditor` APIs

**When to use Play Mode:**
- Testing MonoBehaviour lifecycle (Awake, Start, Update)
- Physics interactions requiring `WaitForFixedUpdate`
- Coroutine and async workflows
- Scene loading and object instantiation at runtime

**Best practice:** Use NUnit `[Test]` instead of `[UnityTest]` unless you need yield instructions. `[Test]` runs faster since it does not require coroutine overhead.

> **Source:** [Edit Mode vs Play Mode Tests](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/edit-mode-vs-play-mode-tests.html)

---
## Additional Resources

- [Unity Test Framework 2.0 Manual](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/index.html)
- [Custom Attributes Reference](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/reference-custom-attributes.html)
- [Command Line Reference](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/reference-command-line.html)
- [NUnit Documentation](https://docs.nunit.org/)
