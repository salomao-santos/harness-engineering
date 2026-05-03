# Requirements Phase Checklist Guide

---

## Initial Requirements Gathering

### Content Quality
- [ ] **Clear Introduction**: Feature overview explains the problem and solution
- [ ] **Business Value**: Clear articulation of why this feature is needed
- [ ] **Scope Definition**: What's included and excluded is explicitly stated
- [ ] **Stakeholder Identification**: All relevant stakeholders are identified

### User Stories
- [ ] **Complete Format**: All user stories follow "As a [role], I want [feature], so that [benefit]" format
- [ ] **Clear Roles**: User roles are specific and well-defined
- [ ] **Valuable Features**: Each feature provides clear user value
- [ ] **Measurable Benefits**: Benefits are specific and measurable where possible

### EARS Format Compliance
- [ ] **WHEN Statements**: Event-driven requirements use WHEN correctly
- [ ] **IF Statements**: Conditional requirements use IF appropriately
- [ ] **WHILE Statements**: Continuous behaviors use WHILE correctly
- [ ] **WHERE Statements**: Context-specific requirements use WHERE appropriately
- [ ] **SHALL Usage**: All system responses use SHALL for mandatory behavior
- [ ] **FOR ALL Usage**: Data transformation and serialization properties use FOR ALL
- [ ] **SHALL NOT Usage**: Prohibited behaviors use SHALL NOT

### Acceptance Criteria Quality
- [ ] **Testable**: Each criterion can be objectively tested
- [ ] **Specific**: Criteria avoid vague terms like "user-friendly" or "fast"
- [ ] **Complete**: All aspects of the requirement are covered (3–8 criteria per requirement)
- [ ] **Unambiguous**: Criteria have only one possible interpretation
- [ ] **Measurable**: Quantitative criteria include specific metrics (HTTP codes, pixel sizes, exact strings)
- [ ] **Glossary Terms**: All domain terms used in criteria appear in the Glossary

### Non-Functional Requirements
- [ ] **Performance**: Response time and throughput requirements specified
- [ ] **Security**: Authentication, authorization, and data protection covered
- [ ] **Usability**: User experience and accessibility requirements included
- [ ] **Reliability**: Error handling and recovery requirements defined
- [ ] **Scalability**: Growth and load requirements addressed

### Requirements Organization
- [ ] **Logical Grouping**: Related requirements are grouped together
- [ ] **Clear Numbering**: Hierarchical numbering system is consistent
- [ ] **Priority Assignment**: Requirements have clear priority levels
- [ ] **Dependency Mapping**: Dependencies between requirements are identified

---

## Requirements Review and Validation

### Completeness Check
- [ ] **All Scenarios Covered**: Positive, negative, and edge cases included
- [ ] **Integration Points**: External system interactions are specified
- [ ] **Data Requirements**: Data models and validation rules are defined
- [ ] **Error Conditions**: Error scenarios and handling are documented (specific messages, HTTP codes)

### Quality Assurance
- [ ] **No Conflicts**: Requirements don't contradict each other
- [ ] **Feasibility**: Technical feasibility has been considered
- [ ] **Consistency**: Terminology is used consistently throughout
- [ ] **Traceability**: Requirements can be traced to business objectives
- [ ] **No Exceeding 8 Criteria**: No single requirement has more than 8 acceptance criteria

### Glossary Completeness
- [ ] Every domain entity has a Glossary entry
- [ ] Every UI component name used in criteria has a Glossary entry
- [ ] Every technical component used in criteria has a Glossary entry
- [ ] All states and enums are defined in the Glossary

### Stakeholder Validation
- [ ] **Business Approval**: Business stakeholders have reviewed and approved
- [ ] **Technical Review**: Technical team has validated feasibility
- [ ] **User Validation**: End users have provided input where appropriate
- [ ] **Compliance Check**: Regulatory and policy requirements are met

---

## Quick Reference (delivery gate)

```markdown
## Document Structure
- [ ] Clear introduction and problem statement
- [ ] Glossary with all domain terms in `Underscore_Case`
- [ ] User stories: specific role, concrete action, distinct benefit
- [ ] EARS-formatted acceptance criteria (3–8 per requirement)
- [ ] Non-functional requirements
- [ ] Constraints and assumptions stated

## Quality Check
- [ ] Every criterion is objectively testable
- [ ] No vague language ("user-friendly", "fast", "as needed")
- [ ] Happy path and error path covered for each requirement
- [ ] Every domain term used in criteria is in the Glossary
- [ ] No requirement exceeds 8 criteria
- [ ] Error paths include HTTP codes, messages, or UI states
- [ ] All stakeholders have reviewed and approved
```
