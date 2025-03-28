# Code Coverage

There are 3 criteria that the test input must satisfy in order to ensure we're detecting faults in our code.

- **Reachability**: the test input must allow the test to reach or execute the faulty statement.
- **Necessity**: the test input must make the faulty statement produce an incorrect intermediate result.
- **Propagation**: the test input must propagate the incorrect intermediate result so that the end result is incorrect
  and observable.  
  *(Dianxiang Xu 330-334)*

---

## Code Coverage / Statement Coverage

Code coverage is a necessary condition for revealing faulty statements because it satisfies the reachability criteria.
But 100% coverage does not guarantee fault detection because it may not meet the necessity or the propagation criteria.

The local static function `MySqrt` has a fault in its conditional logic (where it incorrectly checks `x <= 0` instead of
`x < 0`). The test input of 1 **executes** all lines but the negative return path. The test input of -1 executes all
lines but the Math.Sqrt calculation path.

```C#
[TestCase(1, 1)]
[TestCase(-1, -1)]
public void NonRevealingFault(double input, double expected)
{
    static double MySqrt(double x)
    {
        if (x <= 0) // [FAULT] should be x < 0
        {
            return -1;
        }
        return Math.Sqrt(x);
    }

    var actual = MySqrt(input);
    Assert.AreEqual(expected, actual);
}
```

Together they achieve 100% coverage and have executed the fault line twice. However, they did not reveal the fault! A
test input of 0 would have revealed the fault since it meets all three criteria.

---

*This is because √0 = 0, but `MySqrt(0)` returns -1 instead of 0.*

---
See Also:

- [Finding Test Cases](Finding-Test-Cases.md)
- [Code Review Checklist](Code-Review-Checklist.md)
