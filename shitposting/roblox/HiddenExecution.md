---

# Hidden Script Execution Vulnerability in Roblox Studio

## Summary
A long‑standing vulnerability in Roblox Studio allows arbitrary server‑side scripts to be hidden inside non‑script services. By exploiting the `AnimationFromVideoCreatorService` and manipulating the `RunContext` property, attackers can inject invisible server scripts that execute without detection.

---

## Technical Details

### Proof of Concept
```lua
local s = Instance.new("Script", game:GetService("AnimationFromVideoCreatorService"))
s.Source = [[
-- malicious payload here
]]
s.RunContext = Enum.RunContext.Server
```

- **Step 1:** Create a new `Script` instance and parent it to `AnimationFromVideoCreatorService`.  
- **Step 2:** Assign arbitrary Lua code to `s.Source`.  
- **Step 3:** Force execution by setting `RunContext = Enum.RunContext.Server`.  

### Behavior
- The script runs as a normal server script.  
- Because it is parented to `AnimationFromVideoCreatorService`, it does not appear in the Explorer hierarchy in a way developers expect.  
- This makes the script effectively invisible to most users, while still executing server‑side logic.  

---

## Impact
- **Persistence:** Malicious plugins or command bar injections can create hidden scripts that remain active in a place file.  
- **Stealth:** Developers may not realize a hidden script exists, making it extremely difficult to detect or remove.  
- **Abuse Potential:** Attackers can run arbitrary server code, exfiltrate data, or sabotage projects without being noticed.  
- **Trust Risk:** Plugin ecosystem integrity is undermined if hidden scripts can silently execute.  

---

## Expected vs Actual Behavior
- **Expected:** Roblox should prevent `Script` instances from being parented into non‑script services, or block execution when hidden in unusual contexts.  
- **Actual:** Scripts hidden in `AnimationFromVideoCreatorService` execute normally, bypassing visibility and auditing.  

---

## Closing Note
This vulnerability demonstrates how small oversights in service design can lead to serious security issues. By allowing hidden server scripts to run in unexpected places, Roblox Studio exposes developers to stealthy plugin malware and project compromise. After three years without a patch, it deserves broader attention from the security and developer community.

---
