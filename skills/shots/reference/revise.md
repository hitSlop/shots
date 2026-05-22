# Shots Revision Flow

Revise a hosted screenshot with targeted change instructions.

## Steps

### 1. Resolve The Base Screenshot

Ask for a screenshot id if the user did not provide one. If the user provides a job id instead, call `jobs.get`, confirm that it is complete, and choose the relevant generated screenshot id.

### 2. Capture Feedback

Ask for precise revision feedback:

- which panel
- what should change
- what should stay the same
- whether the positioning, audience, or visual theme changed

### 3. Rebuild The Prompt

Use [prompting.md](prompting.md). Keep the original strategy unless the user is explicitly changing it. Append a clear revision block:

- `Change`
- `Preserve`
- `Constraints`

### 4. Generate

Call `shots` with `screenshots.revise` using the screenshot id and concise feedback. Wait 60 seconds, then poll the returned job with `jobs.get` every 15 seconds until complete.

### 5. Present Output

Present the revised panels in a markdown gallery with screenshot ids:

```markdown
| # | Preview | Screenshot ID | URL |
| --- | --- | --- | --- |
| 1 | ![](cdn-url-1) | scr_abc123 | cdn-url-1 |

[Open in Shots Studio →](https://shots.run/studio?app={appId}&tab=generations)
```

Replace the placeholder URLs, ids, and `{appId}` with actual values from the job result. Then ask whether the user wants another targeted pass.
