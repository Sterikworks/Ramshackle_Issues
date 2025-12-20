# Required Fixes for Crash #2025-12-20

## Critical Priority Fixes

### 1. Add Missing Reflection Shaders to Build ⚠️ CRITICAL

**Issue:** The following shaders are missing from the "Always Included Shaders" list, causing reflection system crashes:
- `Hidden/CubeCopy`
- `Hidden/CubeBlur`
- `Hidden/CubeBlend`

**Fix Location:**
1. Open Unity Project
2. Navigate to `Edit > Project Settings > Graphics`
3. Scroll to "Always Included Shaders"
4. Add the three missing shaders:
   - `Hidden/CubeCopy`
   - `Hidden/CubeBlur`
   - `Hidden/CubeBlend`

**Why This Matters:**
These shaders are essential for Unity's BuiltinRuntimeReflectionSystem. When they're missing, the reflection probe update system crashes when attempting to render cubemaps.

**Testing:**
After adding shaders, test by:
1. Loading a complex vessel (140+ parts)
2. Verifying no shader warnings in console
3. Confirming reflection probes update correctly

---

### 2. Fix BoxCollider Negative Scaling Issues ⚠️ HIGH

**Issue:** Hundreds of warnings about BoxColliders with negative scale on:
- Torque Tubes (all instances)
- Medium Wheels (ST variant)
- Fuel Tanks A & B (all segments)
- Hatches (type 3)
- Light fixtures (3A, 5A)
- Transfer components (L-Transfer, T-Transfer Micro)

**Warning Message:**
```
BoxCollider does not support negative scale or size.
The effective box size has been forced positive and is likely to give unexpected collision geometry.
```

**Fix Options:**

**Option A: Remove Negative Scaling (Preferred)**
1. Review all prefabs listed above
2. Bake the negative scale into the mesh
3. Reset transform scale to positive values
4. Re-assign colliders

**Option B: Switch to MeshCollider**
1. For parts that MUST have negative scaling
2. Replace BoxCollider with MeshCollider
3. Enable "Convex" option
4. Test physics behavior

**Affected Prefabs (Minimum):**
- `Torque Tube` (all variants: 1787, 1794, 1795, 2187)
- `Medium Wheel (ST)` (instances: 1774, 1790, 1791, 2197)
- `Fuel Tank A` (BackSeg, CenterSeg, FrontSeg)
- `Fuel Tank B` (BackSeg, CenterSeg, FrontSeg)
- `Hatch 3` (mainCollider, Depth/weldCollider)
- `L-Transfer (Micro)` (weldCollider)
- `T-Transfer (Micro)` (weldCollider)

**Testing:**
- Load vessels with these parts
- Verify no BoxCollider warnings
- Test collision behavior is unchanged
- Verify welding still works correctly

---

## High Priority Improvements

### 3. Optimize Reflection System for Vessel Spawning

**Issue:** Reflection probe updates during complex vessel spawning can cause crashes or performance issues.

**Recommended Changes:**

**A. Defer Reflection Updates During Spawn**
```csharp
// In VesselSpawner or equivalent
public class VesselSpawner {
    void SpawnVessel() {
        // Disable auto-update for reflection probes
        foreach (var probe in FindObjectsOfType<ReflectionProbe>()) {
            probe.mode = ReflectionProbeMode.Baked;
        }
        
        // ... spawn vessel parts ...
        // ... setup physics ...
        
        // Re-enable after physics settle
        StartCoroutine(EnableReflectionsAfterSettle());
    }
    
    IEnumerator EnableReflectionsAfterSettle() {
        yield return new WaitForSeconds(0.5f); // Wait for physics
        yield return new WaitForEndOfFrame();
        
        foreach (var probe in FindObjectsOfType<ReflectionProbe>()) {
            probe.mode = ReflectionProbeMode.Realtime;
            probe.RenderProbe();
        }
    }
}
```

**B. Add Reflection Probe Culling for Performance**
```csharp
// Limit reflection probe updates during intensive operations
public class ReflectionManager : MonoBehaviour {
    private bool allowReflectionUpdates = true;
    
    public void SuspendReflections() {
        allowReflectionUpdates = false;
        // Optionally: pause all real-time probes
    }
    
    public void ResumeReflections() {
        allowReflectionUpdates = true;
        // Refresh all probes
    }
}
```

---

### 4. Add Shader Validation System

**Issue:** Missing shaders aren't detected until runtime crash occurs.

**Recommended Solution:**

```csharp
#if UNITY_EDITOR
using UnityEditor;
using UnityEngine;

[InitializeOnLoad]
public class ShaderValidator {
    static ShaderValidator() {
        ValidateRequiredShaders();
    }
    
    static void ValidateRequiredShaders() {
        string[] requiredShaders = {
            "Hidden/CubeCopy",
            "Hidden/CubeBlur", 
            "Hidden/CubeBlend"
        };
        
        var graphicsSettings = GraphicsSettings.GetGraphicsSettings();
        var alwaysIncludedShaders = graphicsSettings.GetAlwaysIncludedShaders();
        
        foreach (string shaderName in requiredShaders) {
            var shader = Shader.Find(shaderName);
            if (shader == null) {
                Debug.LogError($"CRITICAL: Required shader '{shaderName}' not found!");
                continue;
            }
            
            if (!alwaysIncludedShaders.Contains(shader)) {
                Debug.LogWarning($"Required shader '{shaderName}' not in Always Included Shaders list!");
            }
        }
    }
}
#endif
```

---

## Medium Priority Enhancements

### 5. Improve Vessel Loading Error Handling

**Add Try-Catch Around Critical Systems:**

```csharp
public class VesselPhysicsSetup {
    void SetupPhysics() {
        try {
            // Physics setup code
            EnablePhysics();
            
            // Reflection update
            if (updateReflections) {
                try {
                    UpdateReflectionProbes();
                } catch (System.Exception e) {
                    Debug.LogError($"Reflection update failed (non-critical): {e.Message}");
                    // Continue without reflections rather than crash
                }
            }
        } catch (System.Exception e) {
            Debug.LogError($"Physics setup failed: {e.Message}");
            CleanupFailedVessel();
            throw;
        }
    }
}
```

---

### 6. Add Loading Progress Indicators

**For Better UX During Complex Spawns:**

```csharp
public class VesselSpawnProgress : MonoBehaviour {
    public event Action<string, float> OnProgressUpdate;
    
    void SpawnVesselWithProgress() {
        OnProgressUpdate?.Invoke("Loading parts...", 0.0f);
        // ... instantiate parts ...
        
        OnProgressUpdate?.Invoke("Setting up physics...", 0.6f);
        // ... physics setup ...
        
        OnProgressUpdate?.Invoke("Updating reflections...", 0.9f);
        // ... reflection updates ...
        
        OnProgressUpdate?.Invoke("Complete!", 1.0f);
    }
}
```

---

## Testing Checklist

After implementing fixes, verify:

- [ ] No shader warnings in console when game starts
- [ ] All 3 reflection shaders present in Always Included Shaders
- [ ] Can load vessel with 150+ parts without crash
- [ ] No BoxCollider negative scale warnings
- [ ] Reflections render correctly on vessels
- [ ] Physics behavior unchanged for all parts
- [ ] Welding still works on all affected parts
- [ ] Performance acceptable (60+ FPS) during vessel spawn
- [ ] Test on NVIDIA GTX 1650 or similar mid-range GPU
- [ ] Test with Vulkan renderer specifically
- [ ] Can spawn bolted armor mantlet configuration

---

## Verification Build Test Cases

1. **Shader Validation Test**
   - Start game
   - Check console for shader errors
   - Verify reflections render in main menu

2. **Simple Vessel Test** (30-50 parts)
   - Load small vessel
   - Verify quick load time
   - Check for any warnings

3. **Complex Vessel Test** (100-150 parts)
   - Load large vessel (like the crash example)
   - Monitor console for warnings
   - Verify smooth loading

4. **Stress Test** (150+ parts)
   - Load maximum complexity vessel
   - Include all problematic parts (torque tubes, fuel tanks, etc.)
   - Monitor performance and stability

5. **Bolted Armor Mantlet Test**
   - Recreate user's configuration
   - Test load/spawn multiple times
   - Verify no crash on reflection update

---

## Files to Modify

### Unity Project Files:
- `ProjectSettings/GraphicsSettings.asset` - Add shaders to Always Included list

### Prefab Files (if using Option A for colliders):
- `Assets/Prefabs/Parts/TorqueTube.prefab`
- `Assets/Prefabs/Parts/MediumWheel_ST.prefab`
- `Assets/Prefabs/Parts/FuelTankA.prefab`
- `Assets/Prefabs/Parts/FuelTankB.prefab`
- `Assets/Prefabs/Parts/Hatch3.prefab`
- `Assets/Prefabs/Parts/LTransferMicro.prefab`
- `Assets/Prefabs/Parts/TTransferMicro.prefab`

### Script Files (for improvements):
- `Scripts/Vessel/VesselSpawner.cs` (or equivalent)
- `Scripts/Managers/ReflectionManager.cs` (create if needed)
- `Scripts/Editor/ShaderValidator.cs` (create new)

---

## Estimated Implementation Time

- **Critical Fixes (1 & 2):** 2-4 hours
- **High Priority (3 & 4):** 4-6 hours
- **Medium Priority (5 & 6):** 2-3 hours
- **Testing:** 3-4 hours

**Total:** ~11-17 hours development + testing time

---

## Notes

- Prioritize fixes in order listed (Critical → High → Medium)
- Test after each major change
- Consider beta testing with affected users
- Monitor for similar crashes in other scenarios
