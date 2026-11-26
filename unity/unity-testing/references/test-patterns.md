# Unity test examples and isolation

## Writing Tests

Create test scripts via **Assets > Create > Testing > C# Test Script** or using the Test Runner's **Create a new Test Script** button.

### Basic Test Structure

```csharp
using NUnit.Framework;

[TestFixture]
public class HealthSystemTests
{
    private HealthSystem _health;

    [SetUp]
    public void SetUp() => _health = new HealthSystem(maxHealth: 100);

    [TearDown]
    public void TearDown() => _health = null;

    [Test]
    public void TakeDamage_ReducesHealth()
    {
        _health.TakeDamage(30);
        Assert.AreEqual(70, _health.CurrentHealth);
    }

    [Test]
    public void TakeDamage_CannotGoBelowZero()
    {
        _health.TakeDamage(150);
        Assert.AreEqual(0, _health.CurrentHealth);
    }

    [Test]
    public void Heal_IncreasesHealth()
    {
        _health.TakeDamage(50);
        _health.Heal(20);
        Assert.AreEqual(70, _health.CurrentHealth);
    }
}
```

### NUnit Attributes Reference

| Attribute | Purpose |
|-----------|---------|
| `[Test]` | Marks a method as a synchronous test |
| `[TestFixture]` | Marks a class as containing tests |
| `[SetUp]` | Runs before each test method |
| `[TearDown]` | Runs after each test method |
| `[OneTimeSetUp]` | Runs once before all tests in the fixture |
| `[OneTimeTearDown]` | Runs once after all tests in the fixture |
| `[TestCase(args)]` | Parameterized test with inline data |
| `[Values(args)]` | Provides values for a test parameter |
| `[Category("name")]` | Categorizes tests for filtering |
| `[Ignore("reason")]` | Skips a test with a reason |
| `[Timeout(ms)]` | Sets a timeout for the test |

### Unity-Specific Attributes

| Attribute | Purpose |
|-----------|---------|
| `[UnityTest]` | Coroutine-based test supporting yield instructions |
| `[UnitySetUp]` | Coroutine-based setup (supports yield) |
| `[UnityTearDown]` | Coroutine-based teardown (supports yield) |
| `[RequiresPlayMode]` | Forces test to run in Play Mode (`true`) or Edit Mode (`false`) |
| `[UnityPlatform]` | Restricts test to specific platforms |
| `[TestMustExpectAllLogs]` | Requires all log messages to be expected |
| `[PrebuildSetup]` | Runs setup before player build |
| `[PostBuildCleanup]` | Runs cleanup after player build |
| `[ConditionalIgnore]` | Conditionally ignores tests |
| `[TestRunCallback]` | Subscribes to test progress updates |

### Common Assertions

```csharp
Assert.AreEqual(expected, actual);           // Equality
Assert.IsTrue(condition);                    // Boolean
Assert.IsNotNull(obj);                       // Null check
Assert.Throws<ArgumentException>(() => F()); // Exception
Assert.AreEqual(1.0f, actual, 0.01f);       // Float tolerance
StringAssert.Contains("sub", actualString);  // String
CollectionAssert.Contains(list, item);       // Collection
```

See `references/test-framework.md` for the full assertion API reference.

> **Source:** [Creating Tests](https://docs.unity3d.com/Packages/com.unity.test-framework@2.0/manual/workflow-create-test.html)

---

## Testing MonoBehaviours

```csharp
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
using System.Collections;

[TestFixture]
public class PlayerControllerTests
{
    private GameObject _playerObject;
    private PlayerController _player;

    [UnitySetUp]
    public IEnumerator SetUp()
    {
        _playerObject = new GameObject("Player");
        _player = _playerObject.AddComponent<PlayerController>();
        yield return null; // Wait one frame for Awake/Start
    }

    [UnityTearDown]
    public IEnumerator TearDown()
    {
        Object.Destroy(_playerObject);
        yield return null; // Wait for destruction
    }

    [UnityTest]
    public IEnumerator Player_MovesForward_WhenInputApplied()
    {
        var startPos = _player.transform.position;
        _player.Move(Vector3.forward);

        yield return new WaitForFixedUpdate();

        Assert.Greater(_player.transform.position.z, startPos.z);
    }
}
```

---

## Testing Coroutines and Async

### Edit Mode UnityTest (yields in EditorApplication.update)

```csharp
[UnityTest]
public IEnumerator EditorUtility_WhenExecuted_ReturnsSuccess()
{
    var utility = RunEditorUtilityInTheBackground();
    while (utility.isRunning) { yield return null; }
    Assert.IsTrue(utility.isSuccess);
}
```

### Play Mode UnityTest (coroutine-based)

```csharp
[UnityTest]
public IEnumerator GameObject_WithRigidBody_WillBeAffectedByPhysics()
{
    var go = new GameObject();
    go.AddComponent<Rigidbody>();
    var originalPosition = go.transform.position.y;
    yield return new WaitForFixedUpdate();
    Assert.AreNotEqual(originalPosition, go.transform.position.y);
}
```

### Available Yield Instructions (Play Mode)

| Yield Instruction | Effect |
|-------------------|--------|
| `yield return null` | Skip one frame |
| `yield return new WaitForSeconds(t)` | Wait for `t` seconds (scaled time) |
| `yield return new WaitForSecondsRealtime(t)` | Wait for `t` real-time seconds |
| `yield return new WaitForFixedUpdate()` | Wait for next physics update |
| `yield return new WaitForEndOfFrame()` | Wait until end of frame |
| `yield return new WaitUntil(() => cond)` | Wait until condition is true |
| `yield return new WaitWhile(() => cond)` | Wait while condition is true |

### Testing with LogAssert

```csharp
[Test]
public void LogWarning_IsExpected()
{
    LogAssert.Expect(LogType.Warning, "Expected warning message");
    Debug.LogWarning("Expected warning message");
}
```

Use `LogAssert.NoUnexpectedReceived()` to assert no unexpected log messages were emitted. `Debug.LogError` and `Debug.LogException` cause automatic test failure unless expected.

---

## Common Test Patterns

### Arrange-Act-Assert (AAA)

```csharp
[Test]
public void Inventory_AddItem_IncreasesCount()
{
    // Arrange
    var inventory = new Inventory(maxSlots: 10);
    var item = new Item("Sword");

    // Act
    inventory.Add(item);

    // Assert
    Assert.AreEqual(1, inventory.Count);
}
```

### Parameterized Tests

```csharp
[TestCase(100, 30, 70)]
[TestCase(100, 100, 0)]
[TestCase(100, 150, 0)]
[TestCase(50, 10, 40)]
public void TakeDamage_CalculatesCorrectly(int maxHp, int damage, int expectedHp)
{
    var health = new HealthSystem(maxHp);
    health.TakeDamage(damage);

    Assert.AreEqual(expectedHp, health.CurrentHealth);
}
```

### Testing Exceptions

```csharp
[Test]
public void Inventory_AddNull_ThrowsException()
{
    var inventory = new Inventory(maxSlots: 10);

    Assert.Throws<ArgumentNullException>(() =>
    {
        inventory.Add(null);
    });
}
```

### Scene-Based Testing

```csharp
[UnityTest]
public IEnumerator Scene_Loads_Successfully()
{
    SceneManager.LoadScene("GameScene", LoadSceneMode.Single);
    yield return new WaitUntil(() => SceneManager.GetActiveScene().name == "GameScene");
    Assert.IsTrue(SceneManager.GetActiveScene().isLoaded);
}
```

### Testing with Fakes (Dependency Injection)

```csharp
public class FakeAudioService : IAudioService
{
    public bool PlaySoundCalled { get; private set; }
    public void PlaySound(string name) => PlaySoundCalled = true;
}

[Test]
public void Attack_PlaysSound()
{
    var fakeAudio = new FakeAudioService();
    var weapon = new Weapon(fakeAudio);
    weapon.Attack();
    Assert.IsTrue(fakeAudio.PlaySoundCalled);
}
```

---

## Anti-Patterns

### 1. Using [UnityTest] when [Test] suffices
`[UnityTest]` adds coroutine overhead. Use `[Test]` for synchronous logic.

```csharp
// BAD - unnecessary coroutine overhead
[UnityTest]
public IEnumerator Add_ReturnsSum()
{
    var result = Calculator.Add(2, 3);
    yield return null;
    Assert.AreEqual(5, result);
}

// GOOD - simple synchronous test
[Test]
public void Add_ReturnsSum()
{
    var result = Calculator.Add(2, 3);
    Assert.AreEqual(5, result);
}
```

### 2. Not cleaning up GameObjects
Leaked objects contaminate other tests. Always destroy GameObjects in `[UnityTearDown]` or `[TearDown]`. Create objects in setup, destroy in teardown.

### 3. Testing private methods directly
Test through public interfaces. If you must, use `[assembly: InternalsVisibleTo("Tests")]`.

### 4. Hardcoding time-based waits
Use `WaitUntil` or `WaitForFixedUpdate` instead of arbitrary `WaitForSeconds`.

```csharp
// BAD - brittle, slow
yield return new WaitForSeconds(5f);
Assert.IsTrue(enemy.IsDead);

// GOOD - deterministic
yield return new WaitUntil(() => enemy.IsDead);
Assert.IsTrue(enemy.IsDead);
```

### 5. Tests depending on execution order
Each test must be independent. Use `[SetUp]` / `[TearDown]` to establish clean state.

### 6. Ignoring log errors
Unexpected `Debug.LogError` calls cause test failures by default. Use `LogAssert.Expect()` for expected errors or `LogAssert.ignoreFailingMessages = true` only when necessary.

### 7. Placing tests in production assemblies
Test assemblies use `UNITY_INCLUDE_TESTS` define constraint and are excluded from production builds. Never mix test code with game code assemblies.

---
