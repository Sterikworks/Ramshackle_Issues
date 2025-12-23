# Ramshackle_Issues
Ramshackle Issue Tracker

## Recent Crash Analysis

### Crash #2025-12-20 - Bolted Armor Mantlet

**Status:** 🔴 Under Investigation  
**Severity:** HIGH - Complete game crash  
**Affected:** Complex vessel spawning (140+ parts)

**Quick Links:**
- 📋 [Quick Summary](CRASH_SUMMARY.md) - 2 minute read
- 🔍 [Detailed Analysis](crash-analysis-2025-12-20.md) - Full technical breakdown
- 🛠️ [Required Fixes](FIXES_NEEDED.md) - Implementation guide with code examples

**Root Cause:** Missing reflection shaders + complex vessel spawning  
**Critical Fix:** Add 3 shaders to Always Included Shaders list (~10 min fix)

---

## Blueprint Repository

This repository tracks user-submitted bug reports and stores associated blueprint files for reproduction and testing.
