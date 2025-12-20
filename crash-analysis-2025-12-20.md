# Crash Analysis Report - 2025-12-20 01:01:52

**Reported by:** joe mama  
**Crash ID:** crash-2025-12-20-010152  
**Activity:** Testing bolted armor mantlet  
**Platform:** Windows 10, Unity 6000.2.9f1, Vulkan renderer, NVIDIA GeForce GTX 1650

---

## Executive Summary

The game crashed during vessel spawning operations while the user was testing a vehicle with a bolted armor mantlet. The crash occurred in Unity's runtime reflection system during the physics setup phase of a vessel spawn.

---

## Technical Details

### Crash Location
- **Component:** UnityEngine.Experimental.Rendering.BuiltinRuntimeReflectionSystem
- **Method:** TickRealtimeProbes()
- **Thread:** Main render thread
- **Unity Version:** 6000.2.9f1

### Stack Trace Analysis
```
UnityEngine.Experimental.Rendering.BuiltinRuntimeReflectionSystem:TickRealtimeProbes()
  └─ Internal_ScriptableRuntimeReflectionSystemWrapper_TickRealtimeProbes()
     └─ Unity render loop
```

The crash originated in Unity's built-in reflection probe update system, which is called every frame to update real-time reflections in the scene.

### Context Leading to Crash

**Vessel Spawning Progress (lines 4900-5113):**
- Successfully spawned 148 parts
- Total LP (Load Points): 1672
- Parts included: plates, ammunition (8.8L56APCBC), wheels, fuel tanks, hatches, engines, transmission, drives, etc.
- Vessel centering and physics setup was in progress

**Last successful operations:**
1. ✅ Parts instantiated (148/148)
2. ✅ Reparenting complete
3. ✅ Welds loaded
4. ✅ Module rigidbodies destroyed
5. ✅ Grids loaded
6. ✅ Grid load callbacks done
7. ✅ LOD and physics setup initiated
8. ✅ Physics load complete
9. ✅ Vessel fully loaded and physics enabled
10. ❌ **CRASH** - During reflection probe update

---

## Warning Patterns

### BoxCollider Warnings (Critical Volume)
Throughout the session, there were **hundreds** of warnings about BoxColliders not supporting negative scale or size:

```
BoxCollider does not support negative scale or size.
The effective box size has been forced positive and is likely to give unexpected collision geometry.
If you absolutely need to use negative scaling you can use the convex MeshCollider.
```

**Affected Components:**
- Torque Tubes (multiple instances)
- Medium Wheels
- Fuel Tanks (A & B)
- Hatches
- Light fixtures
- Transfer components

This pattern suggests improper scaling or mirroring of parts during vessel construction or loading.

### Shader Warnings
Multiple missing shaders detected:
- Hidden/CubeCopy
- Hidden/CubeBlur
- Hidden/CubeBlend

These shaders are related to cubemap rendering and reflection systems, which correlates with the crash location in the reflection probe update system.

---

## Root Cause Analysis

### Primary Cause
**Reflection System Overload**

The crash occurred when Unity's reflection system attempted to update real-time reflection probes. The timing suggests:

1. Vessel with 148 parts spawned successfully
2. Physics system activated for all parts
3. Reflection probe system attempted to capture/update reflections
4. Missing shaders (Hidden/CubeCopy, Hidden/CubeBlur, Hidden/CubeBlend) caused the reflection system to fail
5. System crashed during `TickRealtimeProbes()`

### Contributing Factors

1. **Missing Reflection Shaders**
   - The absence of critical cubemap shaders prevented proper reflection rendering
   - These shaders are essential for the BuiltinRuntimeReflectionSystem

2. **Complex Vessel with Many Parts**
   - 148 parts with colliders
   - Multiple reflection-capable surfaces (metal plates, ammunition, etc.)
   - High polygon count likely stressed the rendering pipeline

3. **BoxCollider Scaling Issues**
   - Extensive negative scaling warnings indicate potential geometry issues
   - Could cause unexpected bounding boxes for reflection probe culling

4. **Previous Crash Detection**
   - Log shows `[CrashDetector] Previous crash detected!` (line 350)
   - Indicates instability pattern in recent sessions

---

## Recommendations

### Immediate Fixes

1. **Restore Missing Shaders**
   - Add `Hidden/CubeCopy`, `Hidden/CubeBlur`, and `Hidden/CubeBlend` to "Always Included Shaders" list
   - Verify all reflection-related shaders are included in build
   - **Priority:** CRITICAL

2. **Fix BoxCollider Scaling**
   - Review all parts with negative scale
   - Replace BoxColliders with MeshColliders where negative scaling is required
   - Specifically check: Torque Tubes, Fuel Tanks, Hatches, Wheels
   - **Priority:** HIGH

3. **Optimize Reflection System**
   - Consider disabling real-time reflections during vessel spawn
   - Re-enable after physics stabilization
   - Add reflection probe baking for static parts
   - **Priority:** MEDIUM

### Long-term Improvements

1. **Vessel Loading Optimization**
   - Implement progressive loading for complex vessels
   - Add loading screen during physics setup phase
   - Defer reflection updates until scene is stable

2. **Error Handling**
   - Add try-catch around reflection probe updates
   - Graceful degradation when reflection shaders are missing
   - Better logging for shader compilation issues

3. **Asset Validation**
   - Pre-load verification for all required shaders
   - Automated testing for vessels with high part counts
   - Warning system for negative-scaled colliders during blueprint validation

---

## Related Issues

This crash may be related to:
- Part scaling/mirroring system
- Blueprint loading pipeline
- Reflection probe management
- Shader compilation and inclusion

---

## Reproduction Steps

To potentially reproduce this crash:
1. Load a vessel blueprint with 140+ parts
2. Include parts with negative scaling (torque tubes, fuel tanks)
3. Ensure parts have metallic/reflective materials
4. Load vessel in a scene with real-time reflection probes
5. Observe crash during or immediately after physics setup

---

## Testing Checklist

Before marking as resolved:
- [ ] Verify all reflection shaders are included in build
- [ ] Test vessel loading with bolted armor mantlet configuration
- [ ] Confirm no BoxCollider negative scale warnings
- [ ] Load test with 150+ part vessels
- [ ] Verify reflection probes update without crashing
- [ ] Test on multiple graphics APIs (Vulkan, DX11, DX12)
- [ ] Verify fix on NVIDIA GTX 1650 (or similar mid-range GPU)

---

## Additional Notes

**User Report Context:**
> "nothing special, i was testing bolted armor mantlet"

This suggests:
- The crash is reproducible with specific vehicle configurations
- The bolted armor mantlet part may have unique reflection properties
- User was likely in the vehicle builder or test drive mode

**System Information:**
- OS: Windows 10 (10.0.19041.6456)
- GPU: NVIDIA GeForce GTX 1650
- Driver: 572.64.0 (32.0.15.7216)
- Graphics API: Vulkan 1.1.0
- RAM: Sufficient (no memory warnings)

---

## Status

**Crash Severity:** HIGH  
**User Impact:** Complete game crash, loss of unsaved work  
**Fix Priority:** CRITICAL  
**Assignee:** Development Team  
**Related Blueprint:** bolted armor mantlet configuration

---

*This analysis was generated automatically from crash log data. Review and validation by development team recommended.*
