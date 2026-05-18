# Shots Revision Flow

Revise a hosted screenshot job with targeted change instructions.

## Steps

### 1. Resolve The Base Job

Ask for a job id if the user did not provide one. Call `get_job` and confirm
that it is complete before revising.

### 2. Capture Feedback

Ask for precise revision feedback:

- which panel
- what should change
- what should stay the same
- whether the positioning, audience, or visual theme changed

### 3. Rebuild The Prompt

Use [prompting.md](prompting.md). Keep the original strategy unless the user is
explicitly changing it. Append a clear revision block:

- `Change`
- `Preserve`
- `Constraints`

### 4. Generate

Call `generate_screenshots` with the revised prompt and the same platform and
panel count as the base job unless the user asks otherwise. Wait 60 seconds, then
poll `get_job` every 15 seconds until complete.

### 5. Present Output

Present the revised panels side by side in an HTML table with screenshot ids:

```
<table><tr>
<td align="center"><img src="cdn-url-1" width="280"/><br/><sub>scr_abc123</sub></td>
<td align="center"><img src="cdn-url-2" width="280"/><br/><sub>scr_def456</sub></td>
<td align="center"><img src="cdn-url-3" width="280"/><br/><sub>scr_ghi789</sub></td>
</tr></table>

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the
job result. Then ask whether the user wants another targeted pass.
