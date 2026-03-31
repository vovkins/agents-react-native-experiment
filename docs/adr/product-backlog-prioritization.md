# Product Backlog Prioritization

**Date:** 2026-03-31  
**Version:** 1.0  
**Status:** Active  

---

## Overview

This document defines the prioritization of the product backlog for the Asset Management Client Portal application. Issues are prioritized using the P0-P3 scale based on MoSCoW analysis from the PRD.

---

## Priority Definitions

| Priority | Label | Definition | MoSCoW | Release Target |
|----------|-------|------------|--------|----------------|
| **P0** | `priority: p0-critical` | Critical - Must have for MVP | MUST HAVE | Sprint 1-3 (MVP) |
| **P1** | `priority: p1-high` | High - Should have for V1.1 | SHOULD HAVE | Sprint 4-5 (V1.1) |
| **P2** | `priority: p2-medium` | Medium - Could have | COULD HAVE | V2.0 (Future) |
| **P3** | `priority: p3-low` | Low - Nice to have | WON'T HAVE | Backlog |

---

## Prioritized Backlog

### P0 - Critical (Must Have for MVP)

#### Issue #11: [Epic] User Authentication & Onboarding
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/11
- **Story Points:** 13
- **Sprint:** 1
- **Milestone:** MVP - Sprint 1
- **Dependencies:** Backend authentication API
- **Rationale:** Authentication is the foundation - users cannot access any features without logging in. Must be completed first.

---

#### Issue #7: [Epic] Navigation & UI Foundation
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/7
- **Story Points:** 8
- **Sprint:** 1
- **Milestone:** MVP - Sprint 1
- **Dependencies:** None
- **Rationale:** Core navigation structure and UI foundation must be in place before feature development. Should be completed in Sprint 1 alongside authentication.

---

#### Issue #9: [Epic] Product Showcase Screen
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/9
- **Story Points:** 8
- **Sprint:** 1-2
- **Milestone:** MVP - Sprint 1-2
- **Dependencies:** Navigation (#7), Authentication (#11)
- **Rationale:** One of three core features. Enables clients to explore investment opportunities. Read-only feature with lower technical complexity than chat or portfolio.

---

#### Issue #8: [Epic] Portfolio View Screen
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/8
- **Story Points:** 13
- **Sprint:** 2-3
- **Milestone:** MVP - Sprint 2-3
- **Dependencies:** Navigation (#7), Authentication (#11), Backend portfolio API
- **Rationale:** One of three core features. Primary screen for clients to monitor their investments. Complex data visualization requirements.

---

#### Issue #10: [Epic] In-App Chat with Manager
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/10
- **Story Points:** 13
- **Sprint:** 2-3
- **Milestone:** MVP - Sprint 2-3
- **Dependencies:** Navigation (#7), Authentication (#11), WebSocket server, Push notification service
- **Rationale:** One of three core features. Real-time messaging functionality with technical complexity. Differentiates the app from competitors.

---

### P1 - High (Should Have for V1.1)

#### Issue #13: [Epic] Push Notifications System
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/13
- **Story Points:** 8
- **Sprint:** 4-5
- **Milestone:** V1.1 - Sprint 4
- **Dependencies:** Chat feature (#10), Firebase/APNs setup
- **Rationale:** Enhances user engagement and ensures timely communication. Critical for chat feature adoption but can operate without in MVP.

---

#### Issue #12: [Epic] Biometric Authentication Enhancement
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/12
- **Story Points:** 3
- **Sprint:** 4
- **Milestone:** V1.1 - Sprint 4
- **Dependencies:** Authentication (#11)
- **Rationale:** Improves user experience significantly for premium clients. Quick win with low effort. Expected by HNWI clients.

---

#### Issue #14: [Epic] Performance Charts & Analytics
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/14
- **Story Points:** 5
- **Sprint:** 5
- **Milestone:** V1.1 - Sprint 5
- **Dependencies:** Portfolio feature (#8)
- **Rationale:** Enhances portfolio view with better visualizations. Basic charts available in MVP, enhanced version in V1.1.

---

### P2 - Medium (Could Have for V2.0)

#### Issue #15: [Epic] Document Access & Viewing
- **URL:** https://github.com/vovkins/agents-react-native-experiment/issues/15
- **Story Points:** 8
- **Sprint:** Future
- **Milestone:** V2.0 - Future
- **Dependencies:** Backend document storage system
- **Rationale:** Nice-to-have feature for document management. Not critical for core functionality. Deferred to V2.0 based on user feedback.

---

### P3 - Low (Nice to Have)

Currently no P3 issues in the backlog. Future P3 features from PRD include:
- Multi-language support
- Advanced portfolio analytics
- Investment calculator
- Market data integration

---

## Sprint Planning

### Sprint 1 (Week 1-2)
**Theme:** Foundation & Core Setup

| Issue | Title | Points | Priority |
|-------|-------|--------|----------|
| #11 | User Authentication & Onboarding | 13 | P0 |
| #7 | Navigation & UI Foundation | 8 | P0 |

**Total Story Points:** 21  
**Goal:** Establish authentication and navigation foundation

---

### Sprint 2 (Week 3-4)
**Theme:** Core Features - Part 1

| Issue | Title | Points | Priority |
|-------|-------|--------|----------|
| #9 | Product Showcase Screen | 8 | P0 |
| #8 | Portfolio View Screen (Part 1) | 7 | P0 |

**Total Story Points:** 15  
**Goal:** Deliver first core features, begin portfolio implementation

---

### Sprint 3 (Week 5-6)
**Theme:** Core Features - Part 2

| Issue | Title | Points | Priority |
|-------|-------|--------|----------|
| #8 | Portfolio View Screen (Part 2) | 6 | P0 |
| #10 | In-App Chat with Manager | 13 | P0 |

**Total Story Points:** 19  
**Goal:** Complete portfolio and chat features - MVP complete

---

### Sprint 4 (Week 7-8)
**Theme:** V1.1 Enhancements - Part 1

| Issue | Title | Points | Priority |
|-------|-------|--------|----------|
| #13 | Push Notifications System | 8 | P1 |
| #12 | Biometric Authentication Enhancement | 3 | P1 |

**Total Story Points:** 11  
**Goal:** Add notifications and biometric login

---

### Sprint 5 (Week 9-10)
**Theme:** V1.1 Enhancements - Part 2

| Issue | Title | Points | Priority |
|-------|-------|--------|----------|
| #14 | Performance Charts & Analytics | 5 | P1 |

**Total Story Points:** 5  
**Goal:** Enhanced analytics and visualizations

---

## Dependency Map

```
Authentication (#11) [P0]
    ↓
Navigation (#7) [P0]
    ↓
    ├── Product Showcase (#9) [P0]
    ├── Portfolio View (#8) [P0]
    │       ↓
    │       └── Performance Charts (#14) [P1]
    └── In-App Chat (#10) [P0]
            ↓
            └── Push Notifications (#13) [P1]

Authentication (#11)
    ↓
    └── Biometric Enhancement (#12) [P1]
```

---

## Risk Assessment

| Risk | Impact | Mitigation |
|------|--------|------------|
| Backend API delays for authentication | High - blocks all development | Start with mock data, parallel backend development |
| WebSocket complexity for chat | Medium - technical challenge | Use proven libraries (socket.io), fallback to polling |
| Push notification setup complexity | Medium - platform-specific | Test early on both iOS and Android |
| Performance with large portfolios | Medium - UX impact | Implement pagination, caching, optimize rendering |

---

## Acceptance Criteria

### MVP (Sprint 1-3) Completion Criteria
- [ ] All P0 issues completed and tested
- [ ] App builds successfully for iOS and Android
- [ ] All three core features functional
- [ ] Internal QA testing passed
- [ ] No critical or high-severity bugs
- [ ] Ready for beta testing

### V1.1 (Sprint 4-5) Completion Criteria
- [ ] All P1 issues completed and tested
- [ ] Push notifications working on both platforms
- [ ] Biometric authentication functional
- [ ] Enhanced performance charts
- [ ] Ready for production deployment

---

## Label Requirements

**Priority Labels to Apply:**
- `priority: p0-critical` - Apply to issues #7, #8, #9, #10, #11
- `priority: p1-high` - Apply to issues #12, #13, #14
- `priority: p2-medium` - Apply to issue #15

**Additional Labels:**
- `sprint: 1` - Issues #7, #11
- `sprint: 2` - Issues #8 (part 1), #9
- `sprint: 3` - Issues #8 (part 2), #10
- `sprint: 4` - Issues #12, #13
- `sprint: 5` - Issue #14
- `sprint: future` - Issue #15

**Milestone Labels:**
- `milestone: mvp` - Issues #7, #8, #9, #10, #11
- `milestone: v1.1` - Issues #12, #13, #14
- `milestone: v2.0` - Issue #15

---

## Metrics & Tracking

### Velocity Tracking
- Sprint 1 Target: 21 points
- Sprint 2 Target: 15 points
- Sprint 3 Target: 19 points
- Sprint 4 Target: 11 points
- Sprint 5 Target: 5 points
- **Total MVP Scope:** 55 points
- **Total V1.1 Scope:** 16 points

### Progress Indicators
- Burndown charts per sprint
- Velocity trends
- Blocked issues count
- Bug count by priority

---

## Review Schedule

- **Weekly:** Sprint progress review
- **Bi-weekly:** Backlog refinement
- **Monthly:** Priority reassessment based on stakeholder feedback
- **Per Sprint:** Retrospective and planning

---

## Notes

1. Priorities can be adjusted based on stakeholder feedback and technical discoveries
2. Story points are estimates and may be refined during sprint planning
3. Dependencies must be resolved before dependent issues can start
4. All P0 issues must be completed before MVP release
5. P1 issues can be released incrementally in V1.1

---

*Document created by Product Manager Agent*  
*Last updated: 2026-03-31*