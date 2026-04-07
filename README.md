# dbt Labs | Fivetran | Snowflake Hands-On Lab

Welcome to the dbt Labs Hands-On Lab! In this workshop, you'll transform raw data with dbt and deliver insights using Snowflake's AI capabilities.

This hands-on lab is a **full-stack, end-to-end walkthrough of how modern data teams go from raw data to AI-powered outcomes in Snowflake** — using dbt to build a clean, trusted semantic layer and Snowflake Cortex to power a natural language AI agent on top of it.

You'll see how this architecture delivers simplicity, speed to business outcomes, and why it wins in the agentic era.

We close with **what's new from dbt Labs**, including the **Fusion Engine**, **emerging patterns for dbt + Snowflake AI** (Semantics, OSI, dbt MCP Server), and **how dbt fits directly within Snowflake**.

---

<div style="background-color: #fff3cd; padding: 12px 16px; border-left: 6px solid #ffcc00; margin-bottom: 20px; margin-top: 10px;">
  <strong>⚠️ IMPORTANT:</strong><br>
  Before starting the lab, register for your Fivetran account at:<br>
  <strong><a href="https://fivetran-lab.web.app/">https://fivetran-lab.web.app/</a></strong>
</div>

---

## Prerequisites

- Web browser
- Valid email address (Snowflake, Fivetran, or dbt Labs)

## Getting Started

1. Register at [https://fivetran-lab.web.app/](https://fivetran-lab.web.app/)
2. Check your email for your Fivetran invitation from `notifications@fivetran.com`
3. Accept the invitation and set your password
4. Get lab credentials: [https://fivetran-lab.web.app/lab-credentials.html](https://fivetran-lab.web.app/lab-credentials.html) (passcode provided by instructor)
5. Log in to Fivetran and get ready to build!

---

## Step 1: Create Fivetran Connector to Snowflake

### 1.1 Configure PostgreSQL Source Connector
1. In Fivetran, click **+ Connector**
2. Search for and select **Google Cloud PostgreSQL**
3. Configure the connector:
   - **Destination**: `Snowflake_SKO_HOL_27_dbt` (pre-configured)
   - **Snowflake Destination Virtual Warehouse**: Keep default
   - **Destination schema prefix**: `yourfirstname_yourlastname` (lowercase, underscores only)
   - **Destination schema names**: Fivetran naming
   - **Host**: Use credentials from lab credentials page based on the first letter of `yourlastname`
   - **Port**: `5432`
   - **User**: Use credentials from lab credentials page
   - **Password**: Use credentials from lab credentials page
   - **Database**: `industry` (case-sensitive)
   - **Authentication Method**: Connect with a username and password
   - **Connection method**: Connect directly
   - **Update Method**: Query-based
4. Click **Save & Test**

### 1.2 Select Data to Sync
1. The `higher_education` schema and `hed_records` table will be pre-selected
2. Click **Continue**

### 1.3 Handle Schema Changes
1. Select **Allow all** (default)
2. Click **Continue**

### 1.4 Start Initial Sync
1. On the connector Status page, click **Start Initial Sync**

### 1.5 Verify Data in Snowflake (While Sync Runs)
1. Navigate to Snowflake Lab Account using credentials from lab credentials page
2. Log in with provided credentials
3. In Snowflake UI, click **Catalog** in the left navigation
4. Click on **RAW** database
6. Find your schema (e.g., `yourfirstname_yourlastname_higher_education`)
7. Click **Tables**
8. Click on **HED_RECORDS** table
9. Click **Data Preview** tab to view the Fivetran synced data and scroll all the way to the right to see `_fivetran_synced`

---

## Step 2: Transform Data with dbt

> **What is dbt?** dbt (data build tool) is a transformation framework that lets data teams write modular SQL models, test them, and document them — all in one place. dbt runs inside your data warehouse (Snowflake), so no data ever leaves the platform. Think of it as version-controlled, tested, production-grade SQL.

### 2.1 Register for dbt Cloud Workshop Account

1. Navigate to the dbt Workshop registration page: [https://workshops.us1.dbt.com/workshop/](https://workshops.us1.dbt.com/workshop/)
2. Fill in the registration form:
   - **First Name**: Your first name
   - **Last Name**: Your last name
   - **Company Email**: Your email address
   - **Workshop Selection**: Select **Snowflake SKO27 Hands-On Lab** from the dropdown
   - **Workshop Passcode**: Enter the passcode provided by your instructor
3. Click **Complete Registration** and wait for the success pop-up. It will include generated dbt Platform credentials for the workshop.
4. Please record these credentials for the remainder of the workshop — you may need them to log into dbt Platform.
5. Click **Complete Registration** — if you are redirected to a login page, use the automatically generated credentials above for access.

### 2.2 Access dbt Platform

1. In dbt Platform, locate the **Project dropdown** on the left-hand side
2. Select **Snowflake SKO (Higher Education)** from the dropdown
3. Click **Studio** to load dbt Platform

### 2.3 Explore dbt Packages Configuration

1. In the **Project Navigator** (left sidebar), locate and open the `packages.yml` file
2. Review the file contents and note how the `snowflake_semantic_view` package is defined
   - This package enables dbt to create Snowflake Semantic Views
3. For detailed information on this package and how it works, see: [https://hub.getdbt.com/Snowflake-Labs/dbt_semantic_view/latest/](https://hub.getdbt.com/Snowflake-Labs/dbt_semantic_view/latest/)

**Understanding Package Management:**
- When adding new packages, run `dbt deps` to install dependencies
- This command creates or updates `package-lock.yml`, which records specific package versions
- The lock file prevents compatibility issues when collaborating with other users

### 2.4 Examine and Run dbt Models

1. In the **Project Navigator**, expand the `models` folder
2. Expand the `HED` subfolder (contains all Higher Education models)
3. Open the `hed_at_risk_students.sql` file
4. Review the code:
   - This file contains the code to generate a Snowflake Semantic View
   - Note how the syntax looks identical to executing it directly in Snowflake
5. Click the **Run +model (Upstream)** button for this model to build the semantic view and its upstream dependencies in your local Dev schema
6. Wait for the model to complete successfully

### 2.5 Generate Tests with dbt Co-Pilot

1. In the **Project Navigator**, locate and open `vw_hed_retention_risk_analysis.sql`
   - This is an upstream dependency of the Semantic View
2. Click the **Co-Pilot dropdown** in the editor
3. Select **Generate Generic Test** from the dropdown menu
4. Wait for Co-Pilot to process and generate the associated YAML configuration
5. Review the generated test definitions

### 2.6 Save and Commit Changes

1. Locate the **Git Integration button** in the top-left corner of dbt Platform
2. Click the **Git Integration button** to commit and sync your changes
3. Follow the prompts to commit your work
4. Click to open a pull request in GitHub
5. Review the PR (no need to merge — lab changes won't be merged)

**Note:** This step saves your work and demonstrates the git workflow, but we won't be merging changes during the lab.

### 2.7 Run a Production dbt Job

1. In the left-hand menu, navigate to **Orchestration > Jobs**
2. Locate and select the preconfigured **Prod Job** (running in the **Prod Environment**)
3. Click **Settings** in the top right, then click **Edit**
4. Update the **Execution commands**:
   - Find the command that says `dbt build`
   - Change it to: `dbt build --select source:hed+`
   - This will only build models downstream of the Higher Education data source
5. Ensure **Generate docs on run** is checked
6. Click **Save** in the top right to save your changes

### 2.8 Execute the Production Job

1. Navigate back to the **Job Overview** page using the top navigation
2. Click the **Run Now** button to execute the job
3. Wait for the job to complete

---

## Step 3: Build and Activate a Snowflake Cortex Agent

Now that dbt has created a clean semantic layer in Snowflake, you'll build an AI agent on top of it. This section walks you through every step of creating the agent from scratch.

### What is Snowflake Cortex?

**Snowflake Cortex** is Snowflake's suite of built-in AI and ML capabilities. It runs entirely inside Snowflake — no data leaves the platform, no external APIs are needed. Key components we'll use today:

- **Cortex Analyst**: Lets users ask natural language questions about their data. You point it at a Semantic View and it automatically translates questions like *"Which students have critical retention risk?"* into SQL and returns results.
- **Cortex Agents**: Orchestration layer that combines Cortex Analyst with custom functions, giving the AI the ability to both answer questions *and* take actions (like routing an alert).
- **Snowflake Intelligence**: A hosted chat interface at [ai.snowflake.com](https://ai.snowflake.com) where end users can interact with Cortex Agents without writing any code.

### What is a Semantic View?

A **Semantic View** is a special Snowflake object that sits on top of your regular data tables or views. It adds a business-friendly layer of meaning — labeling columns, adding descriptions, defining metrics (called "facts"), and categorizing attributes (called "dimensions"). Cortex Analyst reads the Semantic View to understand your data and translate natural language questions into accurate SQL.

Think of it as giving the AI a map of your data: what every column means, what values are valid, and what filters make sense.

### What is Snowflake Snowsight?

**Snowsight** is Snowflake's modern web-based UI. It's where you run SQL, manage objects, and — as of recent releases — create and manage AI Agents. You'll access it using the Snowflake account URL and credentials provided on your lab credentials page.

---

### 3.1 Access Snowflake Snowsight

1. Navigate to the Snowflake lab account URL from your lab credentials page
2. Log in using the username and password provided
3. You will land on the Snowsight home screen

---

### 3.2 Verify Your Data is Ready

Before building the agent, confirm that the dbt production job from Step 1 successfully created the required views in Snowflake.

1. In Snowsight, click **Projects** in the left navigation, then click **Worksheets**
2. Click the **+** button in the top right to create a new SQL worksheet
3. Run the following queries one at a time, replacing `YOUR_DATABASE` with the database name from your lab credentials:

**Check the base view exists and has data:**
```sql
SELECT COUNT(*) FROM YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS;
```

**Check at-risk students exist:**
```sql
SELECT COUNT(*)
FROM YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS
WHERE AT_RISK_FLAG = 'TRUE';
```

**Grab a sample student ID for testing later:**
```sql
SELECT STUDENT_ID, OVERALL_RISK_ASSESSMENT, CURRENT_GPA
FROM YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS
WHERE AT_RISK_FLAG = 'TRUE'
LIMIT 1;
```

> **✅ Expected:** All queries should return results. Copy the `STUDENT_ID` value from the last query — you'll use it to test the agent later.

---

### 3.3 Create the Semantic View

The Semantic View is the "brain" that teaches Cortex Analyst what your data means. The dbt `hed_at_risk_students` model you ran in Step 1 already creates this object, but here you'll see exactly what it contains and verify it exists.

1. In your Snowsight worksheet, run the following to verify the semantic view was created:

```sql
SHOW SEMANTIC VIEWS LIKE 'HED_AT_RISK_STUDENTS' IN SCHEMA YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION;
```

> **✅ Expected:** You should see one row for `HED_AT_RISK_STUDENTS`.

2. If the semantic view is **not** present, you can create it manually by running the full DDL below. Replace `YOUR_DATABASE` with your actual database name before running:

```sql
CREATE OR REPLACE SEMANTIC VIEW YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.HED_AT_RISK_STUDENTS
	TABLES (
		AT_RISK_STUDENTS AS YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS UNIQUE (STUDENT_ID) COMMENT='View of students flagged as at-risk based on multiple retention indicators including GPA, course completion, engagement scores, login activity, and academic standing. Data flows from PostgreSQL through this Snowflake view via dbt transformations.'
	)
	FACTS (
		AT_RISK_STUDENTS.ASSIGNMENT_SUBMISSIONS AS ASSIGNMENT_SUBMISSIONS COMMENT='Number of assignments submitted',
		AT_RISK_STUDENTS.COURSE_COMPLETION_RATE AS COURSE_COMPLETION_RATE COMMENT='Percentage of courses completed (0.0-1.0 decimal). Multiply by 100 for percentage.',
		AT_RISK_STUDENTS.CURRENT_GPA AS CURRENT_GPA COMMENT='Current cumulative GPA on 4.0 scale. Below 2.0 is critical threshold.',
		AT_RISK_STUDENTS.DAYS_SINCE_LAST_LOGIN AS DAYS_SINCE_LAST_LOGIN COMMENT='Days elapsed since last LMS login. 21+ days is critical threshold.',
		AT_RISK_STUDENTS.DISCUSSION_POSTS AS DISCUSSION_POSTS COMMENT='Number of discussion forum posts',
		AT_RISK_STUDENTS.ENGAGEMENT_SCORE AS ENGAGEMENT_SCORE COMMENT='Composite engagement metric on 0-100 scale. Higher is better.',
		AT_RISK_STUDENTS.FINANCIAL_AID_AMOUNT AS FINANCIAL_AID_AMOUNT COMMENT='Total financial aid received in USD',
		AT_RISK_STUDENTS.INTERVENTION_COUNT AS INTERVENTION_COUNT COMMENT='Number of academic interventions received',
		AT_RISK_STUDENTS.PLAGIARISM_INCIDENTS AS PLAGIARISM_INCIDENTS COMMENT='Number of academic integrity violations',
		AT_RISK_STUDENTS.TOTAL_COURSE_VIEWS AS TOTAL_COURSE_VIEWS COMMENT='Total number of course material views',
		AT_RISK_STUDENTS.WRITING_QUALITY_SCORE AS WRITING_QUALITY_SCORE COMMENT='Writing assessment score (0-100 scale)'
	)
	DIMENSIONS (
		AT_RISK_STUDENTS.ACADEMIC_STANDING AS ACADEMIC_STANDING COMMENT='Current academic status. Critical values include Probation, Warning, and Suspension.',
		AT_RISK_STUDENTS.ADVISOR_ID AS ADVISOR_ID COMMENT='Assigned academic advisor identifier',
		AT_RISK_STUDENTS.AT_RISK_FLAG AS AT_RISK_FLAG COMMENT='Boolean indicating if student is flagged as at-risk (always TRUE in this view)',
		AT_RISK_STUDENTS.COMPLETION_RISK_LEVEL AS COMPLETION_RISK_LEVEL COMMENT='Risk categorization based on course completion rate',
		AT_RISK_STUDENTS.ENGAGEMENT_RISK_LEVEL AS ENGAGEMENT_RISK_LEVEL COMMENT='Risk categorization based on engagement score',
		AT_RISK_STUDENTS.ENROLLMENT_DATE AS ENROLLMENT_DATE COMMENT='Student enrollment date',
		AT_RISK_STUDENTS.GPA_RISK_LEVEL AS GPA_RISK_LEVEL COMMENT='Risk categorization based on GPA (Critical/High/Moderate/Low)',
		AT_RISK_STUDENTS.LAST_LOGIN_DATE AS LAST_LOGIN_DATE COMMENT='Most recent LMS login timestamp',
		AT_RISK_STUDENTS.LAST_UPDATED AS LAST_UPDATED COMMENT='Timestamp of last data sync',
		AT_RISK_STUDENTS.LOGIN_RECENCY_RISK_LEVEL AS LOGIN_RECENCY_RISK_LEVEL COMMENT='Risk categorization based on days since last login',
		AT_RISK_STUDENTS.MAJOR_CODE AS MAJOR_CODE COMMENT='Academic major code (e.g., ENGR, BUSN, NURS, PSYC)',
		AT_RISK_STUDENTS.OVERALL_RISK_ASSESSMENT AS OVERALL_RISK_ASSESSMENT COMMENT='Composite risk level with severity classification combining multiple risk factors. Critical and High levels require immediate intervention.',
		AT_RISK_STUDENTS.RECOMMENDED_ACTION AS RECOMMENDED_ACTION COMMENT='Specific recommended interventions based on student risk profile',
		AT_RISK_STUDENTS.STUDENT_ID AS STUDENT_ID COMMENT='Unique student identifier (e.g., STU_202400001)'
	)
	COMMENT='Student Retention semantic model for analyzing at-risk students. Contains multi-factor risk assessments, academic performance metrics, engagement analytics, financial aid information, and recommended interventions. Used for identifying students requiring support and routing retention alerts to appropriate academic staff.'
	WITH EXTENSION (CA='{"tables":[{"name":"at_risk_students","dimensions":[{"name":"academic_standing","sample_values":["Dean''s List","Excellent Standing","Good Standing","Satisfactory Progress","Conditional Standing","Academic Warning","Warning Status","Probationary Status","Academic Probation","Academic Suspension"]},{"name":"advisor_id"},{"name":"at_risk_flag"},{"name":"completion_risk_level","sample_values":["Critical","High","Moderate","Low"]},{"name":"engagement_risk_level","sample_values":["Critical","High","Moderate","Low"]},{"name":"enrollment_date"},{"name":"gpa_risk_level","sample_values":["Critical","High","Moderate","Low"]},{"name":"last_login_date"},{"name":"last_updated"},{"name":"login_recency_risk_level","sample_values":["Critical","High","Moderate","Low"]},{"name":"major_code","sample_values":["ENGR","BUSN","NURS","PSYC","COMP","BIOL"]},{"name":"overall_risk_assessment","sample_values":["Critical - Immediate Intervention","High - Priority Attention","Moderate - Monitor Closely","Low - Watch List"]},{"name":"recommended_action"},{"name":"student_id","unique":true}],"facts":[{"name":"assignment_submissions"},{"name":"course_completion_rate"},{"name":"current_gpa"},{"name":"days_since_last_login"},{"name":"discussion_posts"},{"name":"engagement_score"},{"name":"financial_aid_amount"},{"name":"intervention_count"},{"name":"plagiarism_incidents"},{"name":"total_course_views"},{"name":"writing_quality_score"}],"filters":[{"name":"critical_risk_only","description":"Filter to only Critical risk students","expr":"OVERALL_RISK_ASSESSMENT LIKE ''%Critical%''"},{"name":"high_financial_aid","description":"Students receiving $10,000+ in financial aid","expr":"FINANCIAL_AID_AMOUNT >= 10000"},{"name":"high_priority","description":"Filter to Critical and High risk students","expr":"OVERALL_RISK_ASSESSMENT LIKE ''%Critical%'' OR OVERALL_RISK_ASSESSMENT LIKE ''%High%''"},{"name":"inactive_14_days","description":"Students who haven''t logged in for 14+ days","expr":"DAYS_SINCE_LAST_LOGIN >= 14"},{"name":"inactive_21_days","description":"Students who haven''t logged in for 21+ days (critical threshold)","expr":"DAYS_SINCE_LAST_LOGIN >= 21"},{"name":"low_completion","description":"Students with course completion rate below 50%","expr":"COURSE_COMPLETION_RATE < 0.5"},{"name":"low_engagement","description":"Students with engagement score below 40","expr":"ENGAGEMENT_SCORE < 40"},{"name":"low_gpa","description":"Students with GPA below 2.0","expr":"CURRENT_GPA < 2.0"},{"name":"needs_immediate_attention","description":"Students requiring immediate Dean or Chair notification","expr":"OVERALL_RISK_ASSESSMENT LIKE ''%Critical%'' OR (CURRENT_GPA < 2.0) OR (DAYS_SINCE_LAST_LOGIN > 21)"},{"name":"needs_intervention","description":"Students with multiple intervention indicators","expr":"(CURRENT_GPA < 2.0 OR COURSE_COMPLETION_RATE < 0.5 OR DAYS_SINCE_LAST_LOGIN >= 14)"},{"name":"on_probation","description":"Students on academic probation or warning","expr":"ACADEMIC_STANDING IN (''Probationary Status'', ''Academic Probation'', ''Academic Warning'', ''Warning Status'')"}]}]}');
```

---

### 3.4 Create the Custom Agent Functions

> **What are custom functions?** In addition to answering natural language questions, Cortex Agents can be given tools that let them *take action*. These tools are backed by regular Snowflake SQL functions (also called UDFs — User Defined Functions). When the agent decides an action is needed, it calls the function and uses the result to form its response.

In this lab, the agent has two custom tools:
- **`ROUTE_STUDENT_ALERT`** — Takes a student ID and returns a full alert payload with risk details and routing recommendation (e.g., notify the Dean, Department Chair, or Academic Advisor)
- **`GET_DAILY_RETENTION_SUMMARY`** — Returns an aggregate summary of all at-risk students for a daily report

Run each SQL block below in your Snowsight worksheet. Remember to replace `YOUR_DATABASE` with your actual database name.

#### Create the Route Student Alert function:

```sql
CREATE OR REPLACE FUNCTION YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.ROUTE_STUDENT_ALERT("STUDENT_ID_INPUT" VARCHAR)
RETURNS OBJECT
LANGUAGE SQL
COMMENT='Prepares a student retention alert payload for routing to academic staff. Returns student risk data with routing recommendation based on severity. Used by Cortex Agent for alert activation.'
AS '
SELECT OBJECT_CONSTRUCT(
    -- Student identification
    ''student_id'', s.STUDENT_ID,
    ''major_code'', s.MAJOR_CODE,
    ''advisor_id'', s.ADVISOR_ID,
    ''academic_standing'', s.ACADEMIC_STANDING,

    -- Risk assessment
    ''overall_risk_assessment'', s.OVERALL_RISK_ASSESSMENT,
    ''at_risk_flag'', s.AT_RISK_FLAG,

    -- Academic metrics
    ''current_gpa'', s.CURRENT_GPA,
    ''gpa_risk_level'', s.GPA_RISK_LEVEL,
    ''course_completion_rate'', s.COURSE_COMPLETION_RATE,
    ''completion_risk_level'', s.COMPLETION_RISK_LEVEL,

    -- Engagement metrics
    ''engagement_score'', s.ENGAGEMENT_SCORE,
    ''engagement_risk_level'', s.ENGAGEMENT_RISK_LEVEL,
    ''days_since_last_login'', s.DAYS_SINCE_LAST_LOGIN,
    ''login_recency_risk_level'', s.LOGIN_RECENCY_RISK_LEVEL,

    -- Activity metrics
    ''total_course_views'', s.TOTAL_COURSE_VIEWS,
    ''assignment_submissions'', s.ASSIGNMENT_SUBMISSIONS,
    ''discussion_posts'', s.DISCUSSION_POSTS,

    -- Financial & intervention
    ''financial_aid_amount'', s.FINANCIAL_AID_AMOUNT,
    ''financial_aid_category'', s.FINANCIAL_AID_CATEGORY,
    ''intervention_count'', s.INTERVENTION_COUNT,
    ''intervention_category'', s.INTERVENTION_CATEGORY,
    ''plagiarism_incidents'', s.PLAGIARISM_INCIDENTS,
    ''writing_quality_score'', s.WRITING_QUALITY_SCORE,

    -- Pre-generated recommendations
    ''recommended_action'', s.RECOMMENDED_ACTION,

    -- ROUTING DECISION LOGIC
    ''routing_recommendation'',
        CASE
            WHEN s.OVERALL_RISK_ASSESSMENT LIKE ''%Critical%'' THEN ''NOTIFY_DEAN''
            WHEN s.CURRENT_GPA < 2.0 THEN ''NOTIFY_DEAN''
            WHEN (s.GPA_RISK_LEVEL = ''Critical'' AND s.COMPLETION_RISK_LEVEL = ''Critical'') THEN ''NOTIFY_DEAN''
            WHEN (s.GPA_RISK_LEVEL = ''Critical'' AND s.ENGAGEMENT_RISK_LEVEL = ''Critical'') THEN ''NOTIFY_DEAN''
            WHEN s.OVERALL_RISK_ASSESSMENT LIKE ''%High%'' THEN ''NOTIFY_DEPT_CHAIR''
            WHEN s.GPA_RISK_LEVEL = ''Critical'' OR s.COMPLETION_RISK_LEVEL = ''Critical'' THEN ''NOTIFY_DEPT_CHAIR''
            WHEN s.ENGAGEMENT_RISK_LEVEL = ''Critical'' OR s.LOGIN_RECENCY_RISK_LEVEL = ''Critical'' THEN ''NOTIFY_DEPT_CHAIR''
            WHEN s.DAYS_SINCE_LAST_LOGIN > 21 THEN ''NOTIFY_DEPT_CHAIR''
            WHEN s.ACADEMIC_STANDING IN (''Probationary Status'', ''Academic Probation'', ''Academic Warning'', ''Warning Status'') THEN ''NOTIFY_DEPT_CHAIR''
            ELSE ''NOTIFY_ADVISOR''
        END,

    ''escalation_minutes'',
        CASE
            WHEN s.OVERALL_RISK_ASSESSMENT LIKE ''%Critical%'' OR s.CURRENT_GPA < 2.0 THEN 10
            WHEN s.OVERALL_RISK_ASSESSMENT LIKE ''%High%'' OR s.GPA_RISK_LEVEL = ''Critical'' THEN 15
            ELSE 30
        END,

    ''priority_level'',
        CASE
            WHEN s.OVERALL_RISK_ASSESSMENT LIKE ''%Critical%'' OR s.CURRENT_GPA < 2.0 THEN ''Critical''
            WHEN s.OVERALL_RISK_ASSESSMENT LIKE ''%High%'' OR s.GPA_RISK_LEVEL = ''Critical'' THEN ''High''
            ELSE ''Medium''
        END,

    ''alert_timestamp'', CURRENT_TIMESTAMP(),
    ''alert_date'', TO_CHAR(CURRENT_DATE(), ''YYYY-MM-DD''),
    ''status'', ''ALERT_READY''
)
FROM YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS s
WHERE s.STUDENT_ID = student_id_input
';
```

#### Test the function with the student ID you saved earlier:

```sql
SELECT YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.ROUTE_STUDENT_ALERT('YOUR_STUDENT_ID');
```

> **✅ Expected:** You should see a JSON object with the student's risk details and a routing recommendation.

#### Create the Daily Retention Summary function:

```sql
CREATE OR REPLACE FUNCTION YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.GET_DAILY_RETENTION_SUMMARY()
RETURNS TABLE (
    "REPORT_DATE" VARCHAR,
    "CRITICAL_RISK_COUNT" NUMBER(38,0),
    "HIGH_RISK_COUNT" NUMBER(38,0),
    "MODERATE_RISK_COUNT" NUMBER(38,0),
    "TOTAL_AT_RISK_STUDENTS" NUMBER(38,0),
    "AVG_GPA" NUMBER(38,0),
    "AVG_ENGAGEMENT_SCORE" NUMBER(38,0),
    "STUDENTS_INACTIVE_7_DAYS" NUMBER(38,0),
    "STUDENTS_INACTIVE_14_DAYS" NUMBER(38,0),
    "STUDENTS_INACTIVE_21_DAYS" NUMBER(38,0),
    "STUDENTS_ON_PROBATION" NUMBER(38,0),
    "HIGH_INTERVENTION_COUNT" NUMBER(38,0),
    "UNIQUE_MAJORS" NUMBER(38,0),
    "TOP_RISK_MAJOR" VARCHAR,
    "ALERT_MESSAGE" VARCHAR
)
LANGUAGE SQL
COMMENT='Generates a daily summary of at-risk students. Returns counts, metrics, risk indicators, and a pre-formatted summary message.'
AS '
SELECT
    TO_CHAR(CURRENT_DATE(), ''YYYY-MM-DD'') AS REPORT_DATE,
    COUNT(CASE WHEN OVERALL_RISK_ASSESSMENT LIKE ''%Critical%'' OR CURRENT_GPA < 2.0 THEN 1 END) AS CRITICAL_RISK_COUNT,
    COUNT(CASE WHEN OVERALL_RISK_ASSESSMENT LIKE ''%High%'' AND CURRENT_GPA >= 2.0 THEN 1 END) AS HIGH_RISK_COUNT,
    COUNT(CASE WHEN OVERALL_RISK_ASSESSMENT LIKE ''%Moderate%'' THEN 1 END) AS MODERATE_RISK_COUNT,
    COUNT(*) AS TOTAL_AT_RISK_STUDENTS,
    ROUND(AVG(CURRENT_GPA), 2) AS AVG_GPA,
    ROUND(AVG(ENGAGEMENT_SCORE), 0) AS AVG_ENGAGEMENT_SCORE,
    COUNT(CASE WHEN DAYS_SINCE_LAST_LOGIN >= 7 THEN 1 END) AS STUDENTS_INACTIVE_7_DAYS,
    COUNT(CASE WHEN DAYS_SINCE_LAST_LOGIN >= 14 THEN 1 END) AS STUDENTS_INACTIVE_14_DAYS,
    COUNT(CASE WHEN DAYS_SINCE_LAST_LOGIN >= 21 THEN 1 END) AS STUDENTS_INACTIVE_21_DAYS,
    COUNT(CASE WHEN ACADEMIC_STANDING IN (''Probationary Status'', ''Academic Probation'', ''Academic Warning'', ''Warning Status'') THEN 1 END) AS STUDENTS_ON_PROBATION,
    COUNT(CASE WHEN INTERVENTION_COUNT >= 5 THEN 1 END) AS HIGH_INTERVENTION_COUNT,
    COUNT(DISTINCT MAJOR_CODE) AS UNIQUE_MAJORS,
    (SELECT MAJOR_CODE
     FROM YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS
     WHERE AT_RISK_FLAG = ''TRUE''
     GROUP BY MAJOR_CODE
     ORDER BY COUNT(*) DESC
     LIMIT 1) AS TOP_RISK_MAJOR,
    ''Daily summary generated on '' || TO_CHAR(CURRENT_DATE(), ''Month DD, YYYY'') AS ALERT_MESSAGE
FROM YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.VW_HED_RETENTION_RISK_ANALYSIS
WHERE AT_RISK_FLAG = ''TRUE''
';
```

> **✅ Verify both functions exist:**
> ```sql
> SHOW FUNCTIONS LIKE '%HED%' IN SCHEMA YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION;
> ```

---

### 3.5 Create the Cortex Agent

Now you'll put it all together by creating the agent in Snowsight. The agent connects the Semantic View (for answering questions) with the custom functions (for taking action) into a single conversational interface.

1. In Snowsight, click **AI & ML** in the left navigation panel
2. Click **Agents** in the submenu
3. Click **+ Agent** in the top right corner to open the agent builder

#### Set the Agent Identity

Fill in the following fields:

| Field | Value |
|---|---|
| **Agent Name** | `HED_STUDENT_SUCCESS_AGENT_LAB` |
| **Display Name** | `HED Student Success Agent_Lab` |
| **Available in Snowflake Intelligence** | Toggle **ON** |

**Description** — paste this into the Description field:
```
Student retention analytics agent that monitors at-risk students, answers natural language questions about retention risk factors, and routes intelligent alerts to academic staff (Dean, Department Chair, or Academic Advisor). Powered by real-time data from the HED pipeline (Snowflake → dbt → Cortex).
```

#### Add the Orchestration Instructions

The orchestration instructions tell the agent *how to behave* — which tool to use for which type of request, and any business rules it should follow. Paste the following into the **Orchestration Instructions** (or **System Prompt**) field:

```
You are a Student Retention agent that helps academic staff identify and respond to at-risk students.

TOOL SELECTION:
1. For questions about student data, risk scores, academic performance, or statistics → use query_student_risk_data (Cortex Analyst)
2. For routing an alert for a specific student → use route_student_alert (requires student_id)
3. For generating a daily summary or report → use get_daily_retention_summary

REASONING PROCESS:
1. Understand if the user wants information (query) or action (alert/summary)
2. For queries: Use query_student_risk_data to search student retention data
3. For alerts: Get the student_id first (ask if not provided), then use route_student_alert
4. For summaries: Use get_daily_retention_summary

IMPORTANT:
- Overall risk assessment values: Critical, High, Moderate, Low
- GPA below 2.0 is critical threshold
- 21+ days inactive requires immediate intervention
- Critical risk → routed to Dean (10-min escalation)
- High risk → routed to Department Chair (15-min escalation)
- Medium risk → routed to Academic Advisor (30-min escalation)
- Always include student_id, major, advisor, and risk assessment when discussing students
```

#### Add the Response Instructions

In the **Response Instructions** field, paste:

```
Tone: Professional and supportive, but urgent when discussing critical-risk students. Remember these are real students who need help.

Format:
- Lead with most important information (risk level, student count, priority)
- Include specific numbers (GPA, engagement scores, counts)
- For student details: always show Student ID, Major, Risk Assessment, GPA, Advisor

Alert Responses — when routing an alert, confirm:
- Student ID and major
- Routing destination (Dean/Department Chair/Academic Advisor)
- Key risk factors triggering the alert

Never:
- Skip confirming student_id before routing alerts
- Provide academic or counseling advice beyond the data
- Ignore critical risk indicators
```

#### Add the Cortex Analyst Tool

This tool connects the agent to your Semantic View so it can answer natural language questions about student data.

1. In the **Tools** section, click **+ Add Tool**
2. Select **Cortex Analyst** as the tool type
3. Configure it as follows:

| Field | Value |
|---|---|
| **Tool Name** | `query_student_risk_data` |
| **Database** | Your database name |
| **Schema** | `INDUSTRIES_HIGHER_EDUCATION` |
| **Semantic View** | `HED_AT_RISK_STUDENTS` |

**Tool Description** — paste this into the tool description field:
```
Query student retention data, risk scores, academic performance, and statistics using natural language. Use this tool to answer questions about at-risk students, filter by major or risk level, calculate aggregate metrics, or analyze retention trends.
```

#### Add Custom Tool 1: Route Student Alert

This tool lets the agent take action by routing an alert for a specific student.

1. Click **+ Add Tool** again
2. Select **Function** as the tool type
3. Configure it as follows:

| Field | Value |
|---|---|
| **Tool Name** | `route_student_alert` |
| **Resource Type** | `function` |
| **Function Reference** | `YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.ROUTE_STUDENT_ALERT(VARCHAR)` |

**Tool Description** — paste this:
```
Use this tool to route a retention alert for a specific student. Requires student_id parameter (e.g., STU_202400001). Returns a comprehensive alert payload with student details, risk assessment, academic performance, recommended actions, and routing destination (NOTIFY_DEAN, NOTIFY_CHAIR, or NOTIFY_ADVISOR). Use when the user asks to "route an alert", "send alert", "notify", or "create alert" for a specific student.
```

#### Add Custom Tool 2: Daily Retention Summary

This tool generates a high-level summary across all at-risk students.

1. Click **+ Add Tool** again
2. Select **Function** as the tool type
3. Configure it as follows:

| Field | Value |
|---|---|
| **Tool Name** | `get_daily_retention_summary` |
| **Resource Type** | `table_function` |
| **Function Reference** | `YOUR_DATABASE.INDUSTRIES_HIGHER_EDUCATION.GET_DAILY_RETENTION_SUMMARY()` |

**Tool Description** — paste this:
```
Use this tool to generate a summary of all at-risk students for daily alerting. Returns counts of critical and high-risk students, total at-risk population, engagement metrics, and an overview of the retention picture. Use when the user asks for a "summary", "daily report", or "overview of at-risk students".
```

#### Add Example Questions

Example questions help the agent understand what kinds of queries it should expect and help users get started. Add each of these in the **Example Questions** section:

```
Which students have critical retention risk right now?
```
```
Show me the top 10 highest risk students
```
```
Find Engineering students with low GPA
```
```
What's the average engagement score by major?
```
```
Which students are inactive for more than 21 days?
```
```
Summarize STU_202400465's risk factors
```
```
How many students are on academic probation today?
```
```
Route an alert for STU_202400465
```
```
What's the total financial aid at risk for critical students?
```
```
Generate a daily retention summary
```

#### Save the Agent

1. Review all settings
2. Click **Create** (or **Save**) to create the agent

> **✅ You should see your new agent listed under AI & ML → Agents.**

---

### 3.6 Test Your Agent

Now let's make sure everything works end to end.

#### Option A: Test in Snowflake Intelligence

1. Navigate to [ai.snowflake.com](https://ai.snowflake.com)
2. Log in using your lab credentials
3. Find and select **HED Student Success Agent_Lab**

#### Option B: Test Directly in Snowsight

1. In Snowsight, go to **AI & ML → Agents**
2. Click on **HED_STUDENT_SUCCESS_AGENT_LAB**
3. Click **Test Agent** or **Open Chat**

#### Try these test queries:

**Test 1 — Natural language data query (uses Cortex Analyst):**
> "Which students have critical retention risk right now?"

**Test 2 — Filtered query:**
> "Find Engineering students with low GPA"

**Test 3 — Alert routing (uses your custom function):**
> "Route an alert for [YOUR_STUDENT_ID]"
> *(Use the student ID you saved in Step 2.2)*

**Test 4 — Aggregate summary:**
> "Generate a daily retention summary"

> **✅ Expected for each test:**
> - Test 1 & 2: The agent queries the Semantic View and returns student records with risk details
> - Test 3: The agent calls `ROUTE_STUDENT_ALERT`, returns the student's details, and confirms the routing destination (Dean, Chair, or Advisor)
> - Test 4: The agent calls `GET_DAILY_RETENTION_SUMMARY` and returns counts and metrics across all at-risk students

---

## Need Help?

Ask a lab instructor for assistance.
