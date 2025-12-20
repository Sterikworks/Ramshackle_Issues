# Crash Analysis Index - Issue Tracker

This directory contains crash analysis documentation for Ramshackle game crashes reported by users.

---

## Current Active Analysis

### 🔴 Crash #2025-12-20 - Bolted Armor Mantlet
**Status:** Under Investigation  
**Severity:** HIGH (Complete game crash)  
**Reporter:** joe mama  
**Raw Log:** [View on Gist](https://gist.github.com/RamshackleIssueTracker/1e888a1174f018a36f3abb66b139b8fd)

---

## Quick Navigation

### For Developers 👨‍💻

**Start Here:**
1. 📋 [CRASH_SUMMARY.md](CRASH_SUMMARY.md) - Read this first (2 minutes)
2. 🔍 [crash-analysis-2025-12-20.md](crash-analysis-2025-12-20.md) - Deep dive technical analysis
3. 🛠️ [FIXES_NEEDED.md](FIXES_NEEDED.md) - Implementation guide with code

**Quick Fix Available:**
- **Time Required:** 10 minutes
- **Action:** Add 3 shaders to Always Included Shaders list
- **See:** [FIXES_NEEDED.md - Section 1](FIXES_NEEDED.md#1-add-missing-reflection-shaders-to-build--critical)

---

### For QA Team 🧪

**Testing Documentation:**
- 📝 [TEST_PLAN.md](TEST_PLAN.md) - Complete validation procedures
- ✅ 10 test cases covering all affected systems
- 📊 Includes regression testing checklist

**Estimated Testing Time:** 2-3 hours

---

### For Project Managers 📊

**Impact Assessment:**
- **Affected Users:** Players with complex vehicles (140+ parts)
- **Reproducibility:** HIGH
- **Fix Complexity:** MODERATE
- **Implementation Time:** 4-6 hours (development) + 2-3 hours (testing)

**Documentation Available:**
- ✅ Root cause analysis
- ✅ Fix implementation guide  
- ✅ Test procedures
- ✅ Code examples

---

## Document Structure

```
Ramshackle_Issues/
├── README.md                      # Repository overview with latest crash info
├── ANALYSIS_INDEX.md             # This file - navigation hub
├── CRASH_SUMMARY.md              # Executive summary (2 min read)
├── crash-analysis-2025-12-20.md  # Detailed technical analysis
├── FIXES_NEEDED.md               # Implementation guide + code samples
├── TEST_PLAN.md                  # QA validation procedures
└── blueprints/                   # User-submitted blueprints for testing
```

---

## Analysis Summary

### Root Cause
Missing critical Unity shaders for reflection system:
- `Hidden/CubeCopy`
- `Hidden/CubeBlur`
- `Hidden/CubeBlend`

### Impact
- Crash during real-time reflection probe updates
- Occurs when spawning complex vessels (140+ parts)
- 100% reproducible with affected configurations

### Solution Priority

**Critical (Must Fix):**
1. ✅ Add missing shaders to build
2. ✅ Fix BoxCollider negative scaling (7+ prefabs)

**High Priority (Should Fix):**
3. ✅ Add reflection system safeguards
4. ✅ Implement shader validation system

**Medium Priority (Nice to Have):**
5. ✅ Improve error handling
6. ✅ Add loading progress indicators

---

## Key Findings

### Crash Location
```
UnityEngine.Experimental.Rendering.BuiltinRuntimeReflectionSystem
  └─ TickRealtimeProbes() 
     └─ CRASH: Missing shader during cubemap render
```

### Affected Components
- 🎯 Reflection probe system
- ⚠️ 7+ vehicle part prefabs (torque tubes, wheels, fuel tanks, hatches)
- 🎨 Cubemap rendering pipeline
- ⚙️ Physics setup during vessel spawn

### Timeline to Resolution
```
Day 1: Analysis Complete ✅
Day 2: Implementation (4-6 hours)
Day 3: Testing (2-3 hours)
Day 4: Review & Deploy
```

---

## Related Resources

### Internal
- [Blueprint Repository](blueprints/) - Test blueprints
- [Issue #TBD] - Original bug report (when created)

### External
- [Unity Reflection Probe Docs](https://docs.unity3d.com/Manual/ReflectionProbes.html)
- [Unity Graphics Settings](https://docs.unity3d.com/Manual/class-GraphicsSettings.html)
- [BoxCollider Best Practices](https://docs.unity3d.com/Manual/class-BoxCollider.html)

---

## Version History

| Version | Date | Changes | Author |
|---------|------|---------|--------|
| 1.0 | 2025-12-20 | Initial analysis complete | Copilot |

---

## Contact

For questions about this analysis:
- **Technical Questions:** See [FIXES_NEEDED.md](FIXES_NEEDED.md)
- **Testing Questions:** See [TEST_PLAN.md](TEST_PLAN.md)
- **General Questions:** See [CRASH_SUMMARY.md](CRASH_SUMMARY.md)

---

## Next Steps

### For Implementation Team:
1. Review [FIXES_NEEDED.md](FIXES_NEEDED.md)
2. Implement Critical Priority fixes (Sections 1 & 2)
3. Test using [TEST_PLAN.md](TEST_PLAN.md) cases TC-001 through TC-005
4. If tests pass, implement High Priority fixes
5. Full regression testing

### For QA Team:
1. Set up test environment per [TEST_PLAN.md](TEST_PLAN.md)
2. Wait for development build with fixes
3. Execute all test cases
4. Report results to development team
5. Verify fix in production build

---

*Last Updated: 2025-12-20*  
*Status: Analysis Complete - Awaiting Implementation*
