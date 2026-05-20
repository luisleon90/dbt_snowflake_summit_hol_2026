# dbt Labs | Fivetran | Snowflake Hands-On Lab

Welcome to the Fivetran & dbt Labs Hands-On Lab! In this workshop, you'll move data from a source database into Snowflake using Fivetran, transform it into trusted, production-ready data products using dbt, and see how modern data capabilities like the Fusion engine and dbt State make that process faster, smarter, and more efficient.

This is a **full-stack, end-to-end walkthrough of how modern data teams go from raw data to AI-powered outcomes** — no prior experience with any of these tools is required.

---

**⚠️ IMPORTANT:**
Before starting the lab, register for your Fivetran account at:
**https://fivetran-lab.web.app/**

---

## Prerequisites

- Web browser
- Valid email address

## Getting Started

1. Register at https://fivetran-lab.web.app/
2. Check your email for your Fivetran invitation from `notifications@fivetran.com`
3. Accept the invitation and set your password
4. Get lab credentials: https://fivetran-lab.web.app/lab-credentials.html (passcode provided by instructor)
5. Log in to Fivetran and get ready to build!

---

# Part 1: Introduction to Fivetran

## What is Fivetran?

**Fivetran** is a data movement platform that automatically syncs data from source systems — databases, applications, cloud services — into your data warehouse. Instead of writing and maintaining custom ingestion scripts, Fivetran handles the pipeline for you: it connects to the source, extracts the data, and loads it into Snowflake on a schedule. Once set up, it runs automatically without any manual intervention.

Think of Fivetran as the on-ramp to your data platform — it gets raw data flowing into Snowflake so that tools like dbt can transform it into something useful.

---

## Step 1: Create Fivetran Connector to Snowflake

### 1.1 Configure PostgreSQL Source Connector

1. In Fivetran, click **+ Connector**
2. Search for and select **Google Cloud PostgreSQL**
3. Configure the connector:
   - **Destination**: `Snowflake_Summit_HOL_27_dbt` (pre-configured)
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
5. Find your schema (e.g., `yourfirstname_yourlastname_higher_education`)
6. Click **Tables**
7. Click on **HED\_RECORDS** table
8. Click **Data Preview** tab to view the Fivetran synced data and scroll all the way to the right to see `_fivetran_synced`

> **✅ Expected:** You should see rows of student records loaded by Fivetran. The `_fivetran_synced` column shows the timestamp of the last sync — Fivetran automatically adds this to every table it manages.

---

# Part 2: Introduction to dbt

## What is dbt?

**dbt** (data build tool) is a transformation framework that lets data teams write modular SQL models, test them, and document them — all in one place. dbt runs *inside* your data warehouse (Snowflake), so no data ever leaves the platform. Think of it as version-controlled, tested, production-grade SQL.

Where Fivetran handles *moving* raw data into Snowflake, dbt handles *transforming* that raw data into clean, trusted data products that analysts, dashboards, and AI tools can rely on.

**What makes dbt different from just writing SQL?**
- Every model is a reusable, testable building block
- Data quality tests run automatically alongside your transformations
- Lineage is tracked from source to output — you always know where data came from and how it is being used
- Documentation is generated automatically and stays in sync with the code
- Changes go through version control (Git), just like application software

---

## Step 2: Transform Data with dbt

### 2.1 Register for dbt platform Workshop Account

1. Navigate to the dbt Workshop registration page: https://workshops.us1.dbt.com/workshop/
2. Fill in the registration form:
   - **First Name**: Your first name
   - **Last Name**: Your last name
   - **Company Email**: Your email address
   - **Workshop Selection**: Select **Snowflake Summit Hands-On Lab** from the dropdown
   - **Workshop Passcode**: Enter the passcode provided by your instructor
3. Click **Complete Registration** and wait for the success pop-up. It will include generated dbt Platform credentials for the workshop.
4. Record these credentials — you may need them to log back in during the lab.

### 2.2 Access dbt platform

1. In dbt platform, locate the **Project dropdown** on the left-hand side
2. Select **Snowflake Summit (Higher Education)** from the dropdown
3. Click **Studio** to load dbt Platform

### 2.3 Build Faster with Fusion

> **What is Fusion?** Fusion is dbt's next-generation execution engine, built directly into dbt platform Studio. Unlike a traditional SQL editor, Fusion understands the structure of your entire dbt project as you type — it knows which models exist, which columns they contain, and how they relate to each other. This means it can surface errors, preview intermediate results, and provide column-level context *before* you ever run a job.

This is a great place to get oriented in the dbt platform Studio IDE before we start building. You'll explore the project structure and see how Fusion makes working in dbt faster and safer than writing SQL in a traditional editor.

#### 2.3.1 Explore IntelliSense

The `vw_hed_data_quality` model is a great place to see Fusion's IntelliSense in action. It calculates a composite data quality score for student records by combining multiple upstream checks — making it easy to trace exactly where each component of the score comes from.

1. In the **Project Navigator**, open `vw_hed_data_quality.sql`
2. In the editor, hover over any column reference used in the composite score calculation — Fusion displays the column's data type and description inline, pulled directly from the upstream model that defines it
3. Click on a `ref()` call referencing an upstream model — Fusion lets you trace lineage directly, showing you which model and column the data flows from
4. Begin typing a column name in the editor — notice how Fusion suggests only valid column names from the models referenced in this file, preventing typos before they become errors

> **Note:** This is IntelliSense — the same kind of intelligent code completion used in modern software development tools, now applied to your dbt SQL. Instead of guessing column names or jumping between files, Fusion surfaces the full context of your data model as you write.

#### 2.3.2 See Real-Time Error Detection

1. In `vw_hed_data_quality.sql`, find any line that references an upstream model using `ref()`
2. Temporarily change the model name to something invalid — add a typo, such as changing `ref('stg_hed_records')` to `ref('stg_hed_recordz')`
3. Observe how Fusion immediately highlights the invalid reference with an error indicator — **without running the model**
4. Revert the change back to the correct model name

> **Note:** In a traditional SQL workflow, this error would only surface after a full job run — potentially minutes later, and after consuming warehouse compute. Fusion catches it instantly at the editor level.

#### 2.3.3 Preview a CTE

1. Still in `vw_hed_data_quality.sql`, locate one of the CTEs (the `WITH` blocks at the top of the file)
2. Click on the CTE name to place your cursor inside it
3. Click the **Preview** button in the editor toolbar
4. Review the intermediate output of that CTE — you're seeing the partial results of one component of the composite data quality score, without running the full model

> **✅ Expected:** A preview table appears showing the rows produced by that CTE. This is especially useful in a model like `vw_hed_data_quality` where multiple upstream checks feed into a single composite score — you can validate each component independently before running the full build.

### 2.4 Generate Tests with dbt Wizard

dbt Wizard is dbt's AI assistant, powered by a dbt-native agent that understands your project structure, your models, and how dbt works. You'll use it here to automatically generate and run data quality tests for the `vw_hed_data_quality` model. dbt Wizard is avaliable within dbt platform and for local development via a CLI agent.

1. In the **Project Navigator**, locate and open `vw_hed_data_quality.sql`
2. Open the **dbt Wizard** panel in dbt Platform Studio
3. In the dbt Wizard prompt, type a request such as:
   > *"Generate data quality tests for the vw_hed_data_quality model"*
4. Review the tests dbt Wizard proposes — it will suggest appropriate checks (e.g., `not_null`, `unique`, `accepted_values`) based on the columns and their types in the model
5. Ask dbt Wizard to write the tests directly to a file by following up with:
   > *"Write these tests to the schema YAML file for this model"*
6. dbt Wizard will create or update the relevant `.yml` file with the test definitions — review the file in the **Project Navigator** to confirm the tests were added correctly
7. Ask dbt Wizard to run the new tests:
   > *"Run the tests for this model"*

> **Note:** Notice that dbt Wizard runs `dbt test` rather than `dbt build`. Wizard understands the difference — `dbt build` would rebuild the model *and* run tests, consuming unnecessary warehouse compute. Since the model already exists and we only want to validate it, `dbt test` is the correct and more efficient command. Wizard makes this call automatically.

> **✅ Expected:** All generated tests pass, confirming the data in `vw_hed_data_quality` meets the quality rules Wizard defined. Any failures would surface specific rows or columns that don't meet the expected constraints.

### 2.5 Save and Commit Changes

1. Locate the **Git Integration button** in the top-left corner of dbt platform
2. Click the **Git Integration button** to commit and sync your changes
3. Follow the prompts to commit your work
4. Click to open a pull request in GitHub
5. Review the PR (no need to merge — lab changes won't be merged into the main branch)

**Note:** This step demonstrates the git workflow built into dbt platform. In a real production environment, all model changes go through a PR review process before being promoted — the same engineering discipline used in software development.

### 2.6 Explore dbt Packages Configuration

1. In the **Project Navigator** (left sidebar), locate and open the `packages.yml` file
2. Review the file contents and note how the `snowflake_semantic_view` package is defined
   - This package enables dbt to create Snowflake Semantic Views
3. For more on this package, see: https://hub.getdbt.com/Snowflake-Labs/dbt_semantic_view/latest/

**Understanding Package Management:**

- When adding new packages, run `dbt deps` to install dependencies
- This command creates or updates `package-lock.yml`, which records specific package versions
- The lock file prevents compatibility issues when collaborating with other users

### 2.7 Examine and Run dbt Models

This project showcases two complementary approaches to defining semantic meaning on top of your data — both supported natively by dbt.

**Approach 1: Snowflake Semantic Views**
The `sv_hed_at_risk_students.sql` model creates a Snowflake Semantic View — a native Snowflake object that adds a business-friendly layer directly inside Snowflake. It labels columns, defines metrics (called "facts"), and categorizes attributes (called "dimensions"). This is what powers Cortex AI's ability to understand and query your student retention data using natural language later in the lab.

**Approach 2: dbt Semantic Layer**
dbt also has its own semantic and metric layer, defined using YAML-based metric files within the project. Open `sem_vw_hed_engagement_analytics.yml` in the **Project Navigator** to see an example — this file defines structured metrics on top of the HED engagement data (things like engagement rates, login activity, and risk distributions) using dbt's MetricFlow framework.

The key difference: Snowflake Semantic Views live inside Snowflake and are consumed by Snowflake-native tools like Cortex Analyst. dbt Semantic Layer metrics are defined and governed within dbt itself, making them accessible to any downstream tool that connects via the **dbt MCP server** — including AI agents, BI tools, and coding assistants like Claude. When a tool connects to the dbt MCP server, it can query these metrics directly using natural language or structured requests, with dbt handling the SQL generation and ensuring metric definitions stay consistent across every consumer — including Snowflake Cortex Agents, other AI agents, BI tools, and coding assistants like Claude.

1. In the **Project Navigator**, expand the `models` folder
2. Expand the `HED` subfolder, then expand the `semantic_models` subfolder inside it
3. Open `sv_hed_at_risk_students.sql` and review the Snowflake Semantic View definition
4. Open `sem_vw_hed_engagement_analytics.yml` and review the dbt metric definitions alongside it — note how the two approaches complement each other within the same project
5. Select `sv_hed_at_risk_students.sql` and click the **Run +model (Upstream)** button to build the semantic view and its upstream dependencies in your local Dev schema
6. Wait for the model to complete successfully

> **✅ Expected:** You should see a green success status for `sv_hed_at_risk_students` and its upstream model `vw_hed_retention_risk_analysis`.

### 2.8 Run a Production dbt Job

1. In the left-hand menu, navigate to **Orchestration > Jobs**
2. Locate and select the preconfigured **Prod Job** (running in the **Prod Environment**)
3. Click **Settings** in the top right, then click **Edit**
4. Confirm the execution command is set to `dbt build`
5. Ensure **Generate docs on run** is checked
6. Click **Save** in the top right to save your changes
7. Navigate back to the **Job Overview** page using the top navigation
8. Click **Run Now** to execute the job
9. Wait for the job to complete successfully

> **✅ Expected:** All HED models build successfully, including `vw_hed_retention_risk_analysis` and `sv_hed_at_risk_students`. You'll see green checkmarks next to each model in the run logs.

---

### 2.9 dbt State

> **What is dbt State?** By default, every time a dbt job runs it rebuilds *every* model from scratch — even if the underlying source data hasn't changed. dbt State changes this behavior. dbt compares the current state of your source data against the last known state, and skips rebuilding any model whose inputs haven't changed. This means you only pay for warehouse compute on models that actually need to run — and your jobs complete faster.

#### 2.9.1 Enable Fusion Cost Optimization Features

1. Navigate to **Orchestration > Jobs** and open the **Prod Job**
2. Click **Settings**, then **Edit**
3. Locate the **Enable Fusion cost optimization features** toggle and turn it **ON**
4. Confirm both sub-options are enabled:
   - ✅ **dbt State orchestration** — dbt will skip models whose inputs haven't changed since the last run
   - ✅ **Efficient testing** — dbt will skip tests on models that were skipped, avoiding unnecessary test compute
5. Under **Advanced Settings**, locate the **Compare Changes** setting and set it to **Environment**
6. In the environment dropdown that appears, select your **Production** environment (the environment you used in Step 2.8) — this tells dbt to compare the current run against the last successful production run when determining what to skip
7. Click **Save**

#### 2.9.2 What Happens on Run #1

This is the first execution with Fusion cost optimization enabled. Because there is no previous production run state to compare against yet, dbt has no baseline — it treats everything as new and builds all models from scratch.

**What you'll see:** All models execute with `CREATE` or `OK` status in the run logs. This is your **baseline run** — dbt records the current state of your data so it can make smart skip decisions on every subsequent run.

#### 2.9.3 Execute Run #1

1. Navigate to the **Prod Job Overview** page
2. Click **Run Now**
3. Watch the run logs as the job executes — all models should build
4. Note the total run time displayed when the job completes

> **✅ Expected:** All models execute successfully. Note the run time — you'll compare this to Run #2.

#### 2.9.4 What Happens on Run #2

No new data has been loaded since Run #1 — the Fivetran sync has not run again, so `hed_records` is unchanged.

When you trigger the job a second time, dbt compares the current state of the source data against the baseline recorded in Run #1. Because nothing has changed, dbt determines there is nothing new to build and **skips every downstream model**. With efficient testing also enabled, tests on skipped models are skipped too — no unnecessary warehouse compute is used at all.

**What you'll see:** All models show `SKIP` status in the run logs. The job completes in seconds rather than minutes and with no warehouse compute.

#### 2.9.5 Execute Run #2

1. Click **Run Now** again on the **Prod Job Overview** page
2. Watch the run logs carefully — compare what you see to Run #1

> **✅ Expected:** All models are skipped. The total run time should be a fraction of Run #1's time, and zero Snowflake compute is consumed on model execution or testing.

#### 2.9.6 Reflect

Take a moment to consider what just happened:

- **Run #1** built everything from scratch — this is the full cost of a traditional dbt job on every run, regardless of whether data changed
- **Run #2** completed in seconds with no compute consumed on transformations or tests — this is the value of Fusion cost optimization

In a real production environment where dbt jobs run on a schedule (hourly, daily), this means you're only paying for warehouse compute when data has actually changed. For large projects with hundreds of models, that's a significant reduction in both cost and job runtime.

---
## Step 3: Interact with this Data from Cortex
Now that dbt has created a clean semantic layer, you'll build an AI agent on top of it. This section walks you through every step of creating the agent from scratch.

#### What is Snowflake Cortex?
Snowflake Cortex is Snowflake's suite of built-in AI and ML capabilities. It runs entirely inside Snowflake — no data leaves the platform, no external APIs are needed. Key components we'll use today:

Cortex Analyst: Lets users ask natural language questions about their data. You point it at a Semantic View and it automatically translates questions like "Which students have critical retention risk?" into SQL and returns results.
Cortex Agents: Orchestration layer that combines Cortex Analyst with custom functions, giving the AI the ability to both answer questions and take actions (like routing an alert).
Snowflake Intelligence: A hosted chat interface at ai.snowflake.com where end users can interact with Cortex Agents without writing any code.

#### What is a Semantic View?
A Semantic View is a special Snowflake object that sits on top of your regular data tables or views. It adds a business-friendly layer of meaning — labeling columns, adding descriptions, defining metrics (called "facts"), and categorizing attributes (called "dimensions"). Cortex Analyst reads the Semantic View to understand your data and translate natural language questions into accurate SQL.

Think of it as giving the AI a map of your data: what every column means, what values are valid, and what filters make sense.

#### What is Snowflake Snowsight?
Snowsight is Snowflake's modern web-based UI. It's where you run SQL, manage objects, and — as of recent releases — create and manage AI Agents. You'll access it using the Snowflake account URL and credentials provided on your lab credentials page.

#### Agent Details:
For information about the agent configuration, see: Snowflake Agent Config Reference

You can interact with it using a preconfigured Cortex Agent in two ways.
Access Options:
Option 1: Snowflake Intelligence - ai.snowflake.com
Option 2: Snowflake Account Direct Access - Log in to your Snowflake account

Use credentials from the lab credentials page for either access method

#### 3.1 Access the Cortex Agent
Choose your preferred access method (Snowflake Intelligence or direct Snowflake login)
Log in using credentials from the lab credentials page
Locate the HED_STUDENT_SUCCESS_AGENT_LAB agent

#### You can now ask questions about:
- Student retention
- At-risk students
- Suggested action plans


## Need Help?

Ask a lab instructor for assistance.
