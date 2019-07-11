# Unhappy Flagsubmission, Part 1

Immediately after we opened the network, multiple teams exploited `Voting`, and complained that our flag submission endpoint was always responding `INTERNAL_ERROR`.

As you have noticed, the issue was fixed during the CTF, and I mentioned on IRC that this error would not have happened in Rust.
Since you might be interested in details, here we go:

```c#
for (int i = 0; i < acceptedSubmissions.Count; i++)
{
    if (inserts[i].SubmissionsCount == 1)
    {
        var t = Task.Run(() => acceptedSubmissions[i].TrySetResult(FlagSubmissionResult.Ok));
        okFlags += 1;
    }
    else
    {
        var t = Task.Run(() => acceptedSubmissions[i].TrySetResult(FlagSubmissionResult.Duplicate));
        duplicateFlags += 1;
    }
}
```

This is the loop which processes the results of a batched `insert... on conflict do update` statement, and sets the result of the [TaskCompletionSources](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.taskcompletionsource-1?view=netcore-2.2) which the tcp connection handlers await.
Setting a TaskCompletionSource's result is the way to go, as the continuation  of whatever awaits it is executed synchronously.


The problem here is that, under the hood, `i` is **one** variable.
The spawned tasks which set the result are executed on the thread pool, so they might or might not start immediately - but the variable `i` will increase in the next loop, regardless of the spawned task has already captured `i` or not!


So some TaskCompletionSources were not covered by that loop, and were set to `INTERNAL_ERROR` by a "set everything else to error" block. The flags were inserted fine, though!
