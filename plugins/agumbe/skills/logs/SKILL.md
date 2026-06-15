---
name: logs
description: Read and investigate Agumbe application logs. Use when the user asks to troubleshoot, debug, inspect errors, or view runtime logs for an Agumbe app.
---

# Investigate Agumbe Logs

1. Resolve the workspace, environment, and component.
2. Call `agumbe_get_logs` with a bounded recent time range.
3. Look for the first causal error, not only repeated downstream failures.
4. Correlate the error with deployment status and the deployed revision.
5. Propose or implement a code/configuration fix when the current repository
   contains the affected source.
6. Re-deploy only after validating the fix.

Never print secret values, bearer tokens, cookies, or cloud credentials.
