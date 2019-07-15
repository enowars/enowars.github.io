---
layout: post
title: Scoring Issues, Part 2
tags: ENOWARS3 C# EnoEngine Scoring
author: Trolldemorted
---

During ENOWARS 3, SLA points for recovering teams and the scaled portion of attack points were not properly distributed due to an unintended rounding down in the calculation.

The issue was addressed in [this commit](https://github.com/enowars/EnoEngine/commit/6363675e725130502324c53c288af454eeab5cc7) and all points were recalculated. Sorry for the inconvenience!
