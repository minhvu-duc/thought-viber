---
name: verdict
description: Decide what happens to a thought-viber beta — keep, kill or extend — by comparing its usage to the bar written at idea. Use when its watch window ends, or earlier if the numbers already make the call. The beta's owner decides.
---

# verdict

1. **Fresh numbers.** If the latest Watch log snapshot is more than a day or two old, run `watch` first.
2. **Compare to "Keep if".** Use the bar as written, plus any change recorded in Notes. Don't move it to fit the numbers.
3. **Recommend:**
   - **keep:** the bar is met.
   - **kill:** it isn't met at the end of the window, or usage is clearly dead early.
   - **extend:** too few people saw it to tell. Set a new `watch_until` and note why. A beta that needs a second extension is usually a kill.

   If people used it for something other than the idea, say so. That's a new `idea`; this beta is still killed.
4. **The owner decides.** Record the decision, who made it, the date and one line on why under "Verdict".
5. **Follow through:**
   - **kill:** set `status: killed`, then `reap`.
   - **extend:** back to `watch`.
   - **keep:** set `status: kept`, then `transition`.

A beta nobody decides on shouldn't linger. If `watch_until` passed a while ago and no one has called it, recommend kill.
