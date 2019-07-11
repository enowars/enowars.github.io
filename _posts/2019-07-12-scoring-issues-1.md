---
layout: post
title: Scoring Issues, Part 1
tags: ENOWARS3 C# EnoEngine Scoring
author: Trolldemorted
---

As all of you have witnessed, attack and lost defense points were completely broken for the first few rounds - every flag you lost took defense points from, and every flag you captured gave you attack points for **all** services.


This happened because some where statements were missing in our database queries:

```diff
await _context.SubmittedFlags
    .TagWith("CalculateServiceScores:allCapturesOfFlagsSinceSnapshot")
    .Where(sf => sf.RoundId > oldSnapshotsRoundId)
    .Include(sf => sf.Flag)
+   .Where(sf => sf.Flag.ServiceId == service.Id)
    .GroupBy(sf => sf.FlagId)
    .Select(g => new { g.Key, Amount = g.Count() })
    .AsNoTracking()
    .ToDictionaryAsync(g => g.Key);
```
so every flag was used during the calculation of every service.

We were able to address this issue quickly, and recalculated the points after ENOWARS3 finished.
