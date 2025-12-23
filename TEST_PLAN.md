# Test Plan: Crash #2025-12-20 Fix Validation

## Purpose
Validate that fixes for the bolted armor mantlet crash (2025-12-20) resolve the issue without introducing regressions.

---

## Test Environment Setup

### Required Configuration
- **OS:** Windows 10/11
- **GPU:** NVIDIA GTX 1650 (or equivalent mid-range)
- **Graphics API:** Vulkan (primary), also test DX11/DX12
- **Unity Version:** 6000.2.9f1
- **Build Configuration:** Development Build with Console

### Pre-Test Checklist
- [ ] Shaders added to Always Included Shaders list
- [ ] BoxCollider fixes applied to prefabs
- [ ] Build compiled successfully
- [ ] Debug console enabled

---

## Test Cases

### TC-001: Shader Validation (Critical)
**Priority:** Critical  
**Estimated Time:** 5 minutes

**Steps:**
1. Launch game with debug console visible
2. Observe console during startup
3. Navigate to main menu
4. Check for any shader-related errors

**Expected Results:**
- ✅ No "Shader not found" warnings
- ✅ No errors related to Hidden/CubeCopy, Hidden/CubeBlur, Hidden/CubeBlend
- ✅ Reflections visible in main menu/scenes

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________

---

### TC-002: Simple Vessel Load (Smoke Test)
**Priority:** Critical  
**Estimated Time:** 5 minutes

**Steps:**
1. Load test drive or builder mode
2. Load a simple vessel (30-50 parts)
3. Observe loading process and console
4. Enter gameplay for 30 seconds

**Expected Results:**
- ✅ Vessel loads without errors
- ✅ No BoxCollider warnings
- ✅ No shader warnings
- ✅ Game stable, no crashes

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________

---

### TC-003: Bolted Armor Mantlet Configuration (Regression Test)
**Priority:** Critical  
**Estimated Time:** 10 minutes

**Steps:**
1. Load the exact configuration that caused the original crash
2. If available, use the user's blueprint or recreate:
   - 140-150 parts
   - Include bolted armor mantlet
   - Include torque tubes, fuel tanks, wheels
3. Observe loading and spawning process
4. Monitor console for warnings
5. Allow physics to settle (10 seconds)
6. Move around to trigger reflection updates

**Expected Results:**
- ✅ Vessel spawns successfully
- ✅ All 148 parts load correctly
- ✅ No crash during reflection probe update
- ✅ No BoxCollider negative scale warnings
- ✅ Reflections render correctly on vehicle
- ✅ Physics behave normally

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________

---

### TC-004: Complex Vessel Stress Test
**Priority:** High  
**Estimated Time:** 15 minutes

**Steps:**
1. Load or create a vessel with 150+ parts
2. Include multiple instances of previously problematic parts:
   - Torque Tubes (5+ instances)
   - Medium Wheels (8+ instances)
   - Fuel Tank A and B (2+ each)
   - Hatches (2+ instances)
3. Spawn vessel
4. Let physics settle
5. Drive/operate vehicle for 2 minutes

**Expected Results:**
- ✅ Vessel loads without crash
- ✅ Performance acceptable (40+ FPS)
- ✅ No memory leaks
- ✅ Stable over extended play

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________
- FPS observed: _____ (avg)

---

### TC-005: Part Collision Validation
**Priority:** High  
**Estimated Time:** 10 minutes

**Test Affected Parts:**
Test each previously problematic part individually:

**5.1 Torque Tube**
- [ ] Loads without warnings
- [ ] Collision detection works correctly
- [ ] Can be welded to other parts
- [ ] No negative scale warnings

**5.2 Medium Wheel (ST)**
- [ ] Loads without warnings
- [ ] Rolls properly
- [ ] Ground contact detection works
- [ ] No negative scale warnings

**5.3 Fuel Tank A**
- [ ] All segments load correctly
- [ ] Can be filled with fuel
- [ ] Collision works on all segments
- [ ] No negative scale warnings

**5.4 Fuel Tank B**
- [ ] All segments load correctly
- [ ] Can be filled with fuel
- [ ] Collision works on all segments
- [ ] No negative scale warnings

**5.5 Hatch 3**
- [ ] Opens/closes correctly
- [ ] Collision detection works
- [ ] Depth collider functions properly
- [ ] No negative scale warnings

**Expected Results:**
- ✅ All parts function identically to pre-fix behavior
- ✅ Zero negative scale warnings
- ✅ Collision geometry correct

**Actual Results:**
- [ ] Pass all
- [ ] Partial - Failed items: _______________
- [ ] Fail - Details: _______________

---

### TC-006: Reflection System Validation
**Priority:** High  
**Estimated Time:** 10 minutes

**Steps:**
1. Spawn vessel with metallic/reflective parts
2. Position camera to view reflections on:
   - Armor plates
   - Metallic components
   - Ammunition
3. Move around vehicle
4. Change time of day (if applicable)
5. Monitor reflection probe updates

**Expected Results:**
- ✅ Reflections render correctly
- ✅ Real-time updates work smoothly
- ✅ No crashes during reflection updates
- ✅ No performance degradation
- ✅ Cubemap shaders functioning

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________

---

### TC-007: Multiple Vessel Spawning
**Priority:** Medium  
**Estimated Time:** 10 minutes

**Steps:**
1. Spawn 3-5 complex vessels in same scene
2. Each vessel should have 100+ parts
3. Observe system performance
4. Monitor console for warnings
5. Allow all physics to settle

**Expected Results:**
- ✅ All vessels spawn successfully
- ✅ No cascading crashes
- ✅ Performance degradation acceptable
- ✅ Memory usage stable

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________
- Vessels spawned before crash (if any): _____

---

### TC-008: Graphics API Compatibility
**Priority:** Medium  
**Estimated Time:** 20 minutes

**Test Each Graphics API:**

**8.1 Vulkan (Primary)**
- [ ] Shaders load correctly
- [ ] Vessel spawns without crash
- [ ] Reflections work
- [ ] Performance acceptable

**8.2 DirectX 11**
- [ ] Shaders load correctly
- [ ] Vessel spawns without crash
- [ ] Reflections work
- [ ] Performance acceptable

**8.3 DirectX 12**
- [ ] Shaders load correctly
- [ ] Vessel spawns without crash
- [ ] Reflections work
- [ ] Performance acceptable

**Expected Results:**
- ✅ Consistent behavior across all APIs
- ✅ No API-specific crashes
- ✅ Reflection shaders work on all APIs

**Actual Results:**
- [ ] Pass all
- [ ] Partial - Failed API: _______________
- [ ] Fail - Details: _______________

---

### TC-009: Long-term Stability Test
**Priority:** Medium  
**Estimated Time:** 30 minutes

**Steps:**
1. Load complex vessel
2. Play/test for 30 minutes continuously
3. Perform various actions:
   - Driving
   - Building/editing
   - Combat (if applicable)
   - Saving/loading
4. Monitor for memory leaks
5. Watch for degraded performance

**Expected Results:**
- ✅ No crashes during extended session
- ✅ Memory usage stable
- ✅ Performance remains consistent
- ✅ No warnings accumulate over time

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________
- Time before crash (if any): _____ minutes

---

### TC-010: Blueprint Import/Export
**Priority:** Low  
**Estimated Time:** 5 minutes

**Steps:**
1. Export a complex vessel as blueprint
2. Import the blueprint in new game
3. Spawn imported vessel
4. Verify all parts present

**Expected Results:**
- ✅ Blueprint exports successfully
- ✅ Blueprint imports without errors
- ✅ Vessel spawns identically to original
- ✅ Part count matches

**Actual Results:**
- [ ] Pass
- [ ] Fail - Details: _______________

---

## Regression Testing

### Areas to Check for Regressions

**Physics System:**
- [ ] Vehicle handling unchanged
- [ ] Collision detection accurate
- [ ] Welding functionality intact
- [ ] Weight/balance calculations correct

**Performance:**
- [ ] Frame rate similar to pre-fix
- [ ] Load times acceptable
- [ ] Memory usage not increased significantly

**Visual Quality:**
- [ ] Reflections quality maintained or improved
- [ ] No visual glitches on fixed parts
- [ ] LOD system functioning

**Builder Mode:**
- [ ] Parts can be placed normally
- [ ] Rotation/scaling works
- [ ] Undo/redo functioning
- [ ] Part snapping accurate

---

## Success Criteria

### Must Pass (Blocking)
- ✅ TC-001: Shader Validation
- ✅ TC-002: Simple Vessel Load
- ✅ TC-003: Bolted Armor Mantlet Configuration
- ✅ TC-004: Complex Vessel Stress Test
- ✅ TC-005: Part Collision Validation

### Should Pass (Important)
- ✅ TC-006: Reflection System Validation
- ✅ TC-007: Multiple Vessel Spawning
- ✅ TC-008: Graphics API Compatibility (at least 2/3 APIs)

### Nice to Pass (Optional)
- ✅ TC-009: Long-term Stability Test
- ✅ TC-010: Blueprint Import/Export

---

## Test Execution Log

### Test Run #1
- **Date:** _______________
- **Tester:** _______________
- **Build Version:** _______________
- **Environment:** _______________

**Results Summary:**
- Tests Passed: ____ / 10
- Tests Failed: ____ / 10
- Critical Issues: _______________
- Notes: _______________

### Test Run #2 (if needed)
- **Date:** _______________
- **Tester:** _______________
- **Build Version:** _______________
- **Environment:** _______________

**Results Summary:**
- Tests Passed: ____ / 10
- Tests Failed: ____ / 10
- Critical Issues: _______________
- Notes: _______________

---

## Bug Reporting Template

If issues found during testing, use this template:

**Test Case:** TC-XXX  
**Issue:** _______________  
**Severity:** Critical / High / Medium / Low  
**Steps to Reproduce:**
1. _______________
2. _______________
3. _______________

**Expected:** _______________  
**Actual:** _______________  
**Console Output:** _______________  
**Screenshots:** _______________  

---

## Sign-off

**Development Team Lead:**
- Name: _______________
- Date: _______________
- Signature: _______________

**QA Lead:**
- Name: _______________
- Date: _______________
- Signature: _______________

**Approval for Release:**
- [ ] All critical tests passed
- [ ] No blocking issues
- [ ] Performance acceptable
- [ ] Ready for deployment

**Notes:** _______________

---

## Post-Release Monitoring

After release, monitor for:
- User reports of similar crashes
- Performance metrics
- Crash analytics data
- Reflection system errors

**Monitoring Period:** 7 days post-release  
**Review Date:** _______________
