# Product Requirements Document

## 1. Document Information

**Product Name:** Asset Management Client Portal  
**Version:** 1.0  
**Status:** Draft  
**Date:** 2026-03-31  
**Author:** Product Manager  

---

## 2. Executive Summary

### 2.1 Product Overview

A mobile application for clients of an asset management company (управляющая компания), built using React Native for iOS and Android platforms. The application enables clients to view investment products, monitor their portfolio performance, and communicate with their personal asset managers through an in-app chat.

### 2.2 Problem Statement

Clients of asset management companies currently lack a convenient mobile solution to:
- Stay informed about available investment strategies and products
- Monitor their portfolio performance in real-time
- Maintain direct communication with their personal managers

### 2.3 Product Goals

**Primary Goals:**
1. Provide clients with easy access to the company's product showcase
2. Enable real-time portfolio monitoring for clients' assets under management
3. Facilitate seamless communication between clients and their personal managers

**Business Goals:**
1. Improve client engagement and satisfaction
2. Reduce communication barriers between clients and managers
3. Increase transparency of investment operations
4. Differentiate the company through digital client experience

---

## 3. Target Audience & Personas

### 3.1 Primary Persona: Affluent Investor

**Demographics:**
- Age: 35-65 years
- Income: High net worth individuals (HNWI)
- Education: Higher education, financially literate
- Location: Russia (Russian language primary)

**Characteristics:**
- Has significant assets under management with the company
- Values transparency and control over investments
- Prefers mobile-first solutions for convenience
- Expects premium service and personal attention

**Goals:**
- Monitor investment performance anytime, anywhere
- Stay informed about new investment opportunities
- Maintain direct contact with their asset manager
- Make informed decisions about their portfolio

**Pain Points:**
- Lack of real-time access to portfolio information
- Difficulty reaching managers via phone/email
- Limited visibility into investment products
- No consolidated view of their investment relationship

**User Stories:**
- "As a client, I want to view my current portfolio so I can track my investment performance"
- "As a client, I want to browse available investment strategies so I can consider new opportunities"
- "As a client, I want to chat with my manager so I can quickly discuss investment decisions"

---

## 4. Functional Requirements

### 4.1 Authentication & Onboarding [MUST HAVE]

**FR-1.1:** System shall provide secure authentication for existing clients  
**FR-1.2:** System shall support authentication via email/password combination  
**FR-1.3:** System shall provide "Forgot Password" functionality  
**FR-1.4:** System shall maintain secure session management  
**FR-1.5:** System shall support biometric authentication (Face ID/Touch ID) for convenience  

**Assumption:** Accounts are pre-created by the asset management company. Self-registration is not required for MVP.

### 4.2 Product Showcase Screen [MUST HAVE]

**FR-2.1:** System shall display a list of available trust management strategies  
**FR-2.2:** System shall display individual trust management product information  
**FR-2.3:** System shall show key product details for each strategy:
- Strategy name and description
- Risk level
- Expected returns
- Minimum investment amount
- Historical performance (if available)

**FR-2.4:** System shall allow users to view detailed product information  
**FR-2.5:** System shall provide contact/interest expression for products  

### 4.3 Portfolio View Screen [MUST HAVE]

**FR-3.1:** System shall display current portfolio overview with total value  
**FR-3.2:** System shall show asset allocation breakdown (by strategy, asset type)  
**FR-3.3:** System shall display individual positions with:
- Asset name
- Quantity/units
- Current value
- Performance (absolute and percentage)

**FR-3.4:** System shall show portfolio performance over time (basic charts)  
**FR-3.5:** System shall display transaction history  
**FR-3.6:** System shall support pull-to-refresh for updated data  

### 4.4 In-App Chat with Manager [MUST HAVE]

**FR-4.1:** System shall provide real-time chat interface  
**FR-4.2:** System shall display chat history with the assigned personal manager  
**FR-4.3:** System shall allow sending text messages  
**FR-4.4:** System shall support push notifications for new messages  
**FR-4.5:** System shall show message timestamps and read status  
**FR-4.6:** System shall indicate manager online/offline status  

### 4.5 Navigation & UI [MUST HAVE]

**FR-5.1:** System shall provide bottom tab navigation with three main sections:
- Products (Витрина)
- Portfolio (Портфель)
- Chat (Чат)

**FR-5.2:** System shall provide user profile section with logout functionality  
**FR-5.3:** System shall support Russian language interface  

### 4.6 Notifications [SHOULD HAVE]

**FR-6.1:** System shall send push notifications for new chat messages  
**FR-6.2:** System shall send notifications for portfolio changes  
**FR-6.3:** System shall allow users to manage notification preferences  

### 4.7 Document Access [COULD HAVE]

**FR-7.1:** System shall provide access to investment documents  
**FR-7.2:** System shall allow viewing and downloading reports  
**FR-7.3:** System shall support document signing (future consideration)  

---

## 5. Non-Functional Requirements

### 5.1 Performance

**NFR-1.1:** App launch time shall be under 3 seconds  
**NFR-1.2:** Screen transitions shall be under 300ms  
**NFR-1.3:** API calls shall timeout after 30 seconds  
**NFR-1.4:** App shall support offline viewing of cached portfolio data  

### 5.2 Security

**NFR-2.1:** All API communications shall use HTTPS/TLS 1.3  
**NFR-2.2:** Sensitive data shall be encrypted at rest  
**NFR-2.3:** Session tokens shall expire after configurable period  
**NFR-2.4:** App shall support remote logout capability  
**NFR-2.5:** Biometric data shall not leave the device  

### 5.3 Availability

**NFR-3.1:** App shall be available 99.5% during business hours  
**NFR-3.2:** App shall gracefully handle backend unavailability  
**NFR-3.3:** App shall display meaningful error messages  

### 5.4 Compatibility

**NFR-4.1:** App shall support iOS 14.0 and above  
**NFR-4.2:** App shall support Android 8.0 (API level 26) and above  
**NFR-4.3:** App shall support both phone and tablet form factors  
**NFR-4.4:** App shall support portrait and landscape orientations  

### 5.5 Maintainability

**NFR-5.1:** Code shall follow three-layer architecture (View, Domain, Adapters)  
**NFR-5.2:** Components shall be reusable and testable  
**NFR-5.3:** Code shall include comprehensive documentation  
**NFR-5.4:** Architecture shall support easy backend integration  

### 5.6 Localization

**NFR-6.1:** App shall support Russian language for MVP  
**NFR-6.2:** Architecture shall support future multi-language implementation  

---

## 6. Technical Architecture

### 6.1 Technology Stack

**Frontend:**
- Framework: React Native
- Language: TypeScript
- Styling: Tailwind CSS
- UI Components: shadcn/ui

**Backend:**
- Runtime: Node.js
- API: RESTful or GraphQL (to be determined)

**Architecture Pattern:** Clean Architecture with three layers:
1. **View Layer** - UI components, screens, navigation
2. **Domain Layer** - Business entities, use cases, business logic
3. **Adapters Layer** - API clients, data repositories, external integrations

### 6.2 Data Flow

```
View Layer (Screens/Components)
         ↓
    Domain Layer (Use Cases/Entities)
         ↓
  Adapters Layer (API/Repositories)
         ↓
       Backend
```

---

## 7. User Flow

### 7.1 Authentication Flow

```
App Launch → Check Session → Valid? → Home Screen
                           → Invalid? → Login Screen → Authenticate → Home Screen
```

### 7.2 Main User Journey

```
Login → View Portfolio → Check Products → Chat with Manager → Logout
```

### 7.3 Product Discovery Flow

```
Products Tab → Browse Strategies → View Details → Express Interest → Manager Follow-up
```

### 7.4 Portfolio Monitoring Flow

```
Portfolio Tab → View Overview → Select Position → View Details → Pull to Refresh
```

### 7.5 Communication Flow

```
Chat Tab → View History → Type Message → Send → Receive Response (push notification)
```

---

## 8. Success Criteria

### 8.1 MVP Success Metrics

**Technical Success:**
- ✅ Application builds successfully for iOS and Android
- ✅ All three core features are functional
- ✅ Application passes basic QA testing
- ✅ No critical bugs in production
- ✅ App published to TestFlight and Google Play Beta

**Business Success:**
- ✅ Client onboarding process documented
- ✅ At least 10 beta users actively using the app
- ✅ User satisfaction score > 4.0/5.0
- ✅ Chat feature used at least once per user per week

**Quality Metrics:**
- ✅ Code coverage > 60%
- ✅ Zero critical security vulnerabilities
- ✅ App crash rate < 1%
- ✅ Average app launch time < 3 seconds

### 8.2 Success Criteria for V1.0 Launch

- All MUST HAVE features implemented and tested
- App Store and Google Play approval obtained
- Backend API integration complete and stable
- User documentation created
- Support process established

---

## 9. MoSCoW Prioritization

### MUST HAVE (MVP - Week 1-4)
- User authentication (existing clients)
- Product showcase screen
- Portfolio view screen
- In-app chat with manager
- Basic navigation and UI
- Russian language support

### SHOULD HAVE (V1.1 - Week 5-6)
- Push notifications
- Notification preferences
- Performance charts
- Transaction history details
- Biometric authentication

### COULD HAVE (V2.0 - Future)
- Document access and viewing
- Report downloads
- Multi-language support
- Advanced portfolio analytics
- Investment calculator

### WON'T HAVE (Out of Scope)
- Self-registration for new clients
- Payment processing
- Trading functionality
- Social features
- News feed
- Market data streaming
- Complex document signing workflows

---

## 10. Out of Scope Items

The following features are explicitly excluded from MVP and V1.0:

1. **Self-Registration:** New client onboarding is handled offline; app is for existing clients only
2. **Payment Processing:** No in-app payments or money transfers
3. **Trading:** No ability to execute trades or modify portfolio directly
4. **Advanced Analytics:** Detailed performance analytics deferred to future versions
5. **Document Signing:** Complex e-signature workflows out of scope
6. **Market Data:** Real-time market data and news not included
7. **Multi-language:** Only Russian supported for MVP

---

## 11. Assumptions & Dependencies

### 11.1 Assumptions

1. Client accounts are pre-created in the backend system
2. Each client is assigned to a personal manager
3. Backend API endpoints exist or will be developed in parallel
4. Design assets and brand guidelines are available
5. App Store developer accounts are set up

### 11.2 Dependencies

1. **Backend API:** Required for all data operations
2. **Authentication Service:** Required for user login
3. **Push Notification Service:** Required for chat notifications
4. **Asset Manager Assignment:** Backend must provide manager-client mapping
5. **Design System:** UI/UX designs needed before development

### 11.3 Risks

1. Backend API delays could impact development timeline
2. App Store approval process may require iterations
3. Chat real-time implementation complexity
4. Performance with large portfolios needs validation

---

## 12. Release Phases

### Phase 1: MVP (Week 1-4)
- Core authentication
- Product showcase (read-only)
- Portfolio view (read-only)
- Basic chat functionality
- Internal testing

### Phase 2: Beta Release (Week 5-6)
- Push notifications
- Performance optimizations
- Bug fixes
- Beta user testing

### Phase 3: Production Release (Week 7-8)
- App Store submission
- Production deployment
- User training
- Support documentation

---

## 13. Acceptance Criteria

### 13.1 Feature Acceptance

Each feature must meet the following criteria before acceptance:
- All functional requirements for that feature are implemented
- Code is reviewed and approved
- Unit tests pass with > 60% coverage
- Integration tests pass
- UI/UX review completed
- No critical or high-severity bugs

### 13.2 Sprint Acceptance

Each sprint is considered complete when:
- All planned user stories are done
- Demo to stakeholders completed
- Technical documentation updated
- Code merged to main branch
- Build artifacts created

---

## 14. Appendix

### 14.1 Glossary

- **Управляющая компания (Asset Management Company):** Company that manages client assets
- **Доверительное управление (Trust Management):** Service where manager controls client's assets
- **HNWI (High Net Worth Individual):** Person with significant investable assets
- **In-app Chat:** Real-time messaging within the application

### 14.2 References

- React Native Documentation
- Internal company brand guidelines (to be provided)
- Backend API specification (to be provided)

---

## 15. Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-03-31 | PM Agent | Initial PRD creation |

---

*Document created by Product Manager Agent*  
*Last updated: 2026-03-31*