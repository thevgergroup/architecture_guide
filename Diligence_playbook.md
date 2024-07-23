## Diligence Playbook

### Foreword

Occasionally we get asked to perform diligence on companies, so I want to provide insights into how we look at them. It's not just Technical diligence, the value of most IP is going down, it's harder to patent, it's fraught with long term costs for support and maintenance and produces the majority of SPOFs (Single Points of Failure). Instead we look at a larger ecosystem of the company to be able to effectively produce products and meet positive customer demands.

This does include the tech part, but also how are the products and services being sold, what's promised, what's delivered, are there customer insights and feedback, and how are those dealt with.

Theres the concept of Conway's Law, and if the company has the capability and maturity to grow and scale.
Some of this is beyond Technical Architecture, but not System Architecture. 


### Summary

The object of this document is to provide a consistent framework to perform analysis against a company. Its purpose is to gain insights into the products or services provided, the gap between sales pitch and reality, the internal operations, and gauge the risk, health and maturity of the organization and it’s offerings.

Key Areas

- Org structure alignment
- Goal alignment
- TAM definition and strategy
- Measurement and responsiveness
- Technical Debt and identification of any drivers to fix
- Any drivers for modernization – regulatory, risk, staffing, customer satisfaction.

A technical diligence with deeper reviews on architecture, stability, scale and performance of current offerings is needed to evaluate if there is a linear cost to current implementation or optimizations possible.

### Diligence Sections

#### 1\. Analysis of Clients, Based on Size / Sector

- **Reasoning**: Understanding the client distribution by size and sector helps gauge market penetration, dependency, and growth potential.
- **Questions**:
  - What are the major sectors your clients come from?
  - How is your client base distributed according to business size?
  - How do client needs differ based on business size?
- **Interpretation**:
  - A diverse client base suggests lower risk and greater stability.
  - High concentration in a particular sector or client size might indicate potential risk if that sector faces downturns.

#### 2\. Analysis of Marketing / Sales Material and Pitch

- **Reasoning**: Examines how the company positions itself in the market and its alignment with industry standards and client expectations.
- **Questions**:
  - Who do you see as your main competitors?
  - Can you show examples of your sales materials and pitch decks?
  - How do you differentiate your offerings from competitors?
- **Interpretation**:
  - Consistency in messaging across materials indicates a clear strategy.
  - Strong unique selling propositions (USPs) suggest competitive advantages.

#### 3\. Product Actuals - Identifying the Gap Between Sales Pitch and Reality

- **Reasoning**: Ensures product claims are accurate and meet customer expectations, maintaining trust and integrity.
- **Questions**:
  - How is product performance against tracked against sales claims?
  - Are there discrepancies between promised and delivered functionalities?
  - How plugged into strategy are Sales and Product owners?
- **Interpretation**:
  - Gaps may indicate issues in product management alignment or potential customer dissatisfaction.

#### 4\. Customer Success

- **Reasoning**: Assessing renewal rates and customer feedback to evaluate satisfaction and loyalty.
- **Questions**:
  - What are your renewal rates, and how have they changed over time?
  - What common feedback do you receive from win/loss reviews?
  - What happens to that feedback?
- **Interpretation**:
  - High renewal rates and positive win/loss feedback indicate strong product-market fit and customer satisfaction.

#### 5\. Customer Support

- **Reasoning**: Evaluates the effectiveness of support systems in place for sustaining customer satisfaction and retention.
- **Questions**:
  - What are the common issues reported by customers?
  - How do you handle repeat and unresolved issues?
  - Is there a feedback channel in the company?
- **Interpretation**:
  - Recurrent issues or many unresolved cases can indicate deeper systemic problems.
  - Lacking a feedback mechanism to Product Ownership may indicate experience issues.

#### 6\. Corporate Spend and Licensing for 3rd Party Software, Hosting, Development, Data Acquisition, BI, and CRM

- **Reasoning**: Understanding spending helps assess financial health and operational efficiency.
- **Questions**:
  - Is all corporate spend done through corporate cards and accounts?
  - Are the ledgers categorized and reviewed for CapEx, OpEx
  - Are vendors categorized?
    - Can a list of Software, Hosting, Data Acquisition, MIS and other licensing services be provided.
    - How has that spend changed over time?
    - What is linear to client and business growth?
  - How are prospects acquired, funneled and managed?
    - Cold Prospects, campaigned outreach
- **Interpretation**:
  - Efficient spend management and strategic licensing suggest sound operational management.
  - Understanding cost overheads for scale, M&A and areas for optimization.
  - Maturity in outreach, risk aversion from corporate assets
    - E.g. NOT using corporate email domain for cold email campaigns

#### 7\. Regulation, Security Controls, Training, Posture, International Customers (e.g., EU / UK)

- **Reasoning**: Compliance with regulations and security practices are crucial for legal operations and customer trust.
- **Questions**:
  - Are there international customers? In EU / UK?
  - How do you comply with international regulations such as GDPR?
  - What training programs are in place for employees on security and compliance?
  - How is ITIL managed?
    - Email, Websites, CRM, Chat systems, Phones, Hardware, Licensing, DNS, Passwords, Keys, Physical Access & Security, Cloud Administration.
- **Interpretation**:
  - Strong compliance and training programs minimize risk and enhance reputation.
  - Identify SPOF
    - Retention needs

#### 8\. Technology Engineering, Operations, and Architecture Analysis

- **Reasoning**: Assesses the robustness of the technology stack and its scalability.
- **Questions**:
  - How is your technology architecture designed to scale with growth?
  - What are the major challenges in current operations?
  - Is delivery performed internally / externally?
  - How are security controls managed?
    - Versioning
    - Architecture compliance
    - Testing / Manual / Automated
    - Operations
      - Backup / recovery
      - Monitoring / Alerting
      - Access control / on-boarding / off-boarding
    - Cost control, budget and capacity planning
    - Documentation and asset management
    - Adoption of security frameworks?
      - E.g. BSIMM / OWASP SAMM
  - SOC needs? Any 3<sup>rd</sup> party analysis
- **Interpretation**:
  - Identifying models to reduce tech debt, getting ahead of the problem.
  - Is there inherent risk with PCI / DSS, PII, PHI and a lack of controls?
  - Is IP owned and managed correctly? Or is it **licensed**?
  - Are there single points of failure, personnel or technical?
    - Retention
    - Secure IP via documentation, cross training etc..

#### 9\. Product Owner / Management Review

- **Reasoning**: Evaluates leadership and strategic direction in product development.
- **Questions**:
  - How do product owners prioritize feature development?
  - What is the roadmap for future product development?
  - What is the corporate strategy and how does the product roadmap align?
  - How is feedback gathered for ideation?
  - How is it presented, budgeted and approved?
  - How is internal buy achieved?
    - Is there widespread understanding of directions and goals?
  - What is the completion rate for roadmaps?
- **Interpretation**:
  - Clear, strategic product planning indicates strong leadership
  - Changes to roadmaps often indicate market adaptiveness.
  - Buy in ensures cross team effectiveness, employee satisfaction and commitment to strategy.
  - Addressing feedback channels ensures customer asks and needs are included.
    - Verify if it’s raw asks or though through

#### 10\. Technology Team Review

- **Reasoning**: Reviews the skills, morale, and structure of the tech team to assess capability to meet current and future needs.
- **Questions**:
  - What’s working / not working?
  - Is there a rockstar? Or a CMMI level 0 superman?
  - Is there a diversity need?
    - Tech tends to be over indexed with singular views, occasional it’s immaterial.
  - How is the tech delivery measured?
  - How widespread is the tech understanding, or are there silos?
  - Are teams able to deliver end to end? Self-sufficient but not siloed?
  - What is the automation model and testing model?
    - CI/D, static analysis, dependency management, deployment and rollback
    - QA validation, automated testing? – there’s never automated testing
    - Cloud usage and orchestration.
  - 3<sup>rd</sup> Service usage.
  - SLA client obligations ?
- **Interpretation**:
  - Acquisition generates about 20-25% voluntary turnover in staff, due to change, unknown dilution of shares, or timing.
  - Is there enough automation to absorb any changes, or is it high risk with SPOFs?

### Org Analysis

Ensuring the delegation of responsibilities over the growth of a company is critical, and often missed as people struggle to grow into their role.

People take many hats in startups; few understand when to give those hats up.

On the opposite side might be someone who over delegates, owns the business unit but doesn’t work in the business.

Check for the following roles and responsibilities

#### Small Company < $3MM

- **CEO/Founder**:
  - Overall oversight and final review.
  - Direct involvement in Customer Success to maintain high engagement and personal touch with clients.
  - Investor relations
- **Chief Finance / Operating Officer (CFO / COO)**:
  - Corporate Spend and Licensing.
  - Regulation, Security Controls, and Compliance.
  - Investment, Debt
- **Chief Technology Officer (CTO)**:
  - Technology Engineering, Operations, and Architecture Analysis.
  - Technology Team Review.
  - Product Management often rolls up
    - Product Actuals.
    - Product Owner/Management Review.
  - Roadmap Champion and Owner, Delivery Owner
- **Sales and Marketing Manager**:
  - Analysis of Clients, Based on Size / Sector.
  - Analysis of Marketing / Sales Material and Pitch.
  - TAM definer and owner

#### Medium Company > $3MM – 10MM

- **CEO**:
  - Overall strategy and final review.
- **CFO / COO**:
  - EBITDA phase
  - Capital Structuring
    - Debt / Funding / Notes
  - Sales / Services Collections
- **CTO**:
  - Technology Engineering, Operations, and Architecture Analysis.
  - Customer Support (focus on process and SOP improvements).
  - Regulation and Compliance (ensuring adherence to international standards).
  - Capacity Planning
  - Budget Development
  - ITIL
- **VP of Sales**:
  - Analysis of Clients, Based on Size / Sector.
- **VP of Marketing**:
  - Analysis of Marketing / Sales Material and Pitch.
- **Director of Product Management**:
  - Product Actuals.
  - Product Owner/Management Review.
- **Director of Customer Success**:
  - Customer Success (managing renewals, win/loss analysis, NPS).
  - Customer Support should roll up
- **Finance Director**:
  - Corporate Spend and Licensing.
  - CapEX & OpEX spend
  - Budget Management

#### Large Company > $10MM

- **CEO**:
  - Overall corporate strategy and final decision-making.
- **CFO**:
  - Regulation, Security Controls, and Compliance.
- **CTO**:
  - Technology Engineering, Operations, and Architecture Analysis.
- **Chief Financial Officer (CFO)**:
  - Corporate Spend and Licensing.
  - Amortization
- **Chief Marketing Officer (CMO)**:
  - Analysis of Marketing / Sales Material and Pitch.
- **Chief Sales Officer (CSO)**:
  - Analysis of Clients, Based on Size / Sector.
- **VP of Product Management**:
  - Product Actuals.
  - Product Owner/Management Review.
- **VP of Customer Success**:
  - Customer Success.
- **VP of Customer Support**:
  - Customer Support.
- **VP of Technology Development**:
  - Technology Team Review.

### Corporate Diligence Scoring Rubric

| **Area of Focus** | **Responsible Role(s)** | **Score (1-5)** | **Criteria** |
| --- | --- | --- | --- |
| Analysis of Clients, Based on Size / Sector | VP of Sales (Medium/Large), Sales and Marketing Manager (Small) | 1-5 | Diversity of sectors, concentration risk, growth potential |
| Analysis of Marketing / Sales Material and Pitch | VP of Marketing (Medium/Large), Sales and Marketing Manager (Small) | 1-5 | Clarity, USP strength, market alignment |
| Product Actuals | Director of Product Management (Medium/Large), Product Manager (Small) | 1-5 | Accuracy of sales claims vs. product reality |
| Customer Success | VP of Customer Success (Medium/Large), CEO/Founder (Small) | 1-5 | Renewal rates, customer feedback, NPS |
| Customer Support | VP of Customer Support (Large), COO (Medium), CEO/Founder (Small) | 1-5 | Resolution rate, process efficiency, customer satisfaction |
| Corporate Spend and Licensing | CFO | 1-5 | Cost efficiency, ROI, licensing strategy |
| Regulation, Security Controls, and Compliance | COO, CFO (Large) | 1-5 | Compliance rate, security incidents, training efficacy |
| Technology Engineering, Operations, and Architecture Analysis | CTO | 1-5 | Scalability, robustness, innovation |
| Product Owner / Management Review | VP of Product Management (Medium/Large), Product Manager (Small) | 1-5 | Strategic alignment, roadmap clarity, market fit |
| Technology Team Review | VP of Technology Development (Large), CTO (Medium/Small) | 1-5 | Skill levels, team morale, turnover rates, SPOF, automation |

#### Scoring System

- **1 - Poor**: Does not meet expectations; significant improvements needed.
- **2 - Fair**: Slightly below expectations; some elements need refinement.
- **3 - Good**: Meets expectations; performs adequately but not exceptionally.
- **4 - Very Good**: Exceeds expectations; displays strong capabilities and efficiency.
- **5 - Excellent**: Far exceeds expectations; industry-leading performance.

### Technology Evaluation Rubric

| **Category** | **Criteria** | **Score (1-5)** | **Comments** |
| --- | --- | --- | --- |
| **Process Efficiency** |     |     |     |
| Standardization | Degree to which processes are standardized | 1-5 |     |
| Automation | Level of automation integrated into processes | 1-5 |     |
| Documentation | Quality and comprehensiveness of process documentation | 1-5 |     |
| Review and Improvement | Frequency and effectiveness of process reviews | 1-5 |     |
| **Software Quality** |     |     |     |
| Usability | Ease of use and user interface design | 1-5 |     |
| Functionality | Appropriateness and comprehensiveness of features | 1-5 |     |
| Performance | Speed, reliability, and resource efficiency | 1-5 |     |
| Integration | Ease of integration with other systems | 1-5 |     |
| Security | Compliance with security standards and practices | 1-5 |     |
| **Team Dynamics** |     |     |     |
| Skill Diversity | Range and balance of skills within the team | 1-5 |     |
| Collaboration | Effectiveness of teamwork and communication | 1-5 |     |
| Innovation | Capacity for and track record of innovation | 1-5 |     |
| Responsiveness | Speed and effectiveness in addressing issues | 1-5 |     |
| Training and Development | Ongoing training and professional development | 1-5 |     |

### Scoring System

- **1 - Poor**: Significantly below expectations, major improvements needed.
- **2 - Fair**: Below expectations, noticeable issues that need attention.
- **3 - Good**: Meets basic expectations, competent but not exceptional.
- **4 - Very Good**: Exceeds expectations, demonstrates superior capabilities.
- **5 - Excellent**: Exceptional performance, industry-leading standards.

### Technical Asset & Vendor Evaluation

| **Asset Type** | **Asset Description** | **Cost (Annual)** | **Stability** | **Capacity** | **Scalability** | **Owner/Department** |
| --- | --- | --- | --- | --- | --- | --- |
| Cloud Service | AWS EC2 Instances | $50,000 | High | 20 TB | Scalable | IT Infrastructure |
| Software | Microsoft Office 365 | $30,000 | High | 200 Users | Not Scalable | IT Support |
| Hardware | Dell Servers | $80,000 | Medium | 64 GB RAM | Moderately | IT Operations |
| Vendor Software | Salesforce CRM | $40,000 | High | 300 Users | Scalable | Sales Department |
| Cloud Service | Google Cloud Storage | $20,000 | High | 100 TB | Highly Scalable | Data Management |

### Consolidated Scoring

| **Evaluation Area** | **Criteria** | **Score (1-5)** | **Comments** |
| --- | --- | --- | --- |
| **Corporate Strategy and Client Analysis** | Diversity and concentration of clients, strategic alignment | 1-5 |     |
| **Marketing and Sales Effectiveness** | Clarity and strength of marketing/sales materials and pitch | 1-5 |     |
| **Product Performance** | Gap between sales pitch and product actuals, feature delivery | 1-5 |     |
| **Customer Engagement** | Customer success metrics, support effectiveness, feedback handling | 1-5 |     |
| **Operational Efficiency** | Process automation, standardization, review effectiveness | 1-5 |     |
| **Financial Health and Compliance** | Cost efficiency, ROI, compliance with financial and security regulations | 1-5 |     |
| **Technology and Infrastructure** | Software quality, integration, asset scalability, architecture stability | 1-5 |     |
| **Team and Leadership** | Skill diversity, collaboration, innovation, management effectiveness | 1-5 |     |

### Scoring System

- **1 - Poor**: Does not meet expectations; significant improvements needed.
- **2 - Fair**: Slightly below expectations; some elements need refinement.
- **3 - Good**: Meets expectations; performs adequately but not exceptionally.
- **4 - Very Good**: Exceeds expectations; displays strong capabilities and efficiency.
- **5 - Excellent**: Far exceeds expectations; industry-leading performance.
