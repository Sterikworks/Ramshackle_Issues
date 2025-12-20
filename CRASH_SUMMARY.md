# Crash Summary - 2025-12-20 01:01:52

**Reporter:** joe mama  
**Activity:** Testing bolted armor mantlet  
**Result:** Complete game crash  

## What Happened

The game crashed while spawning a complex vehicle (148 parts) during the reflection system update phase.

## Root Cause

**Primary Issue:** Missing critical shaders for reflection system
- `Hidden/CubeCopy`
- `Hidden/CubeBlur`  
- `Hidden/CubeBlend`

These shaders are required for Unity's real-time reflection probe updates. When missing, the system crashes instead of gracefully degrading.

**Contributing Issues:**
- 100+ BoxCollider warnings due to negative scaling on parts
- Complex vessel with many reflective surfaces
- Reflection probe update triggered during intensive physics setup

## Critical Stack Trace

```
Crash in: UnityEngine.Experimental.Rendering.BuiltinRuntimeReflectionSystem:TickRealtimeProbes()
Location: Unity reflection probe update system
Phase: Post-vessel spawn, during first frame render
```

## Impact

- **Severity:** HIGH - Complete game crash
- **Reproducibility:** HIGH - Likely reproducible with similar vessel configurations
- **User Impact:** Loss of unsaved work, game instability

## Quick Fix (10 minutes)

1. Open Unity Project Settings → Graphics
2. Add to "Always Included Shaders":
   - Hidden/CubeCopy
   - Hidden/CubeBlur
   - Hidden/CubeBlend
3. Rebuild game

## Full Fix Required (4-6 hours)

1. Add missing shaders (Critical)
2. Fix BoxCollider negative scaling on 7+ prefabs (High Priority)
3. Add reflection system safeguards (High Priority)
4. Implement shader validation system (Medium Priority)

## Testing Required

- Load complex vessels (140+ parts)
- Verify bolted armor mantlet configuration
- Test on NVIDIA GTX 1650 / Vulkan
- Confirm no collider warnings
- Verify stable reflection updates

## Related Files

- Full Analysis: `crash-analysis-2025-12-20.md`
- Implementation Guide: `FIXES_NEEDED.md`
- Raw Log: [Gist](https://gist.github.com/RamshackleIssueTracker/1e888a1174f018a36f3abb66b139b8fd)

## Status

🔴 **OPEN** - Awaiting shader fix implementation

---

*For detailed technical analysis and code examples, see FIXES_NEEDED.md*
