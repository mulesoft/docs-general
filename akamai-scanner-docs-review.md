# Akamai Scanner Docs — Review Draft

> Review draft generated from source AsciiDoc. Two pages are included below. Comments welcome.

**Contents**

1. [Scanner Prerequisites by Provider](#1-scanner-prerequisites-by-provider)
2. [Using Akamai for Risk Correlation](#2-using-akamai-for-risk-correlation)

---

# 1. Scanner Prerequisites by Provider

*Source: `exp-scanners-prerequisites-reference.adoc`*

Scanner prerequisites by provider help you confirm required roles, credentials, and permissions before creating a scanner. Use this reference to prevent connection test failures and incomplete discovery by validating provider-specific access in advance. Each scanner still requires Exchange Administrator permission and the correct business group context.

## Before You Begin

Before adding any scanner, make sure you have:

- Exchange Administrator permission.
- Access to, and active context in, the business group where you want to add the scanner.

## Scanner Prerequisites Cross-Reference

| Provider | Scanner Type | Required Credentials, Roles, and Setup |
| --- | --- | --- |
| Amazon Bedrock | Agent | **Credentials:** Access key ID and secret access key; AWS region<br>**Permissions:** `bedrock:ListAgents`; `bedrock:GetAgent`; `bedrock:ListAgentAliases`; `bedrock:GetAgentAlias`; `bedrock:ListAgentVersions`; `bedrock:GetAgentVersion`<br>**Optional (for agent invocation workflows):** `bedrock:InvokeModel`; `bedrock:InvokeAgent`; `bedrock:InvokeInlineAgent`<br>**Setup:** Agents must have an alias linked to a version and an invocable URL |
| Amazon Bedrock AgentCore Runtime | Agent | **Credentials:** Access key ID and secret access key; AWS region<br>**Account:** Active AWS account with AgentCore access<br>**Permissions:** `bedrock-agentcore:ListAgentRuntimes`; `bedrock-agentcore:ListAgentRuntimeEndpoints`; `bedrock-agentcore:GetAgentCard`; `bedrock-agentcore:GetAgentRuntime`; `bedrock-agentcore:ListAgentRuntimeVersions`; `bedrock:GetAgent`; `bedrock:ListAgents`<br>**Setup:** Agents must be published with an active endpoint/version |
| Anthropic Claude Managed Agents | Agent | **Credentials:** Claude API key<br>**Account:** Paid Anthropic account |
| Databricks Agent Bricks | Agent | **Credentials:** Workspace URL; client ID and client secret<br>**Account:** Databricks workspace access<br>**Permissions:** Service principal `CAN_QUERY` on serving endpoints; `CAN_VIEW` or higher on endpoint metadata APIs<br>**Setup:** Discoverable agents must be custom Unity Catalog models in `READY` state |
| GoDaddy ANS | Agent | **Credentials:** API key and API secret |
| Google Gemini Agent Enterprise Platform | Agent | **Credentials:** GCP project ID; service account email; private key<br>**Role:** Vertex AI Viewer |
| LangChain LangSmith | Agent | **Credentials:** LangSmith API key; LangSmith workspace ID<br>**Account:** LangSmith Plus plan (or higher) workspace<br>**Setup:** Optional API host for region routing (for example, US or EU cloud host) |
| Microsoft Azure Copilot | Agent | **Credentials:** Azure app registration; tenant ID, client ID, client secret<br>**Role:** Copilot Studio Scanner<br>**Setup:** App added as an Application User in Power Platform; scope set to Dataverse environment URL |
| Microsoft Foundry | Agent | **Credentials:** Azure app registration; tenant ID, client ID, client secret<br>**Account:** Active Azure subscription<br>**Role:** Azure AI Developer<br>**Setup:** Project endpoint URLs (discovery is project-specific) |
| Snowflake Cortex AI | Agent | **Credentials:** Snowflake account URL; programmatic access token (PAT)<br>**Account:** Snowflake account with Cortex Agents enabled (Enterprise edition)<br>**Role:** `ACCOUNTADMIN`, for one-time setup only<br>**Setup:** At least one Cortex Agent created in a schema to be scanned; scanner egress IP ranges from your Anypoint deployment team (`<SCANNER_EGRESS_CIDRS>`) |
| Amazon API Gateway | API | **Credentials:** Access key ID and secret access key; AWS region<br>**Permissions:** IAM read-only policy for API Gateway (for example, `apigateway:GET`) |
| Azure API Management | API | **Credentials:** Tenant ID; client ID; client secret; subscription ID; resource group; service name<br>**Role:** API Management Service Reader |
| Google Apigee | API | **Credentials:** GCP project ID; service account email; private key<br>**Role:** Apigee Read-only Admin |
| Kong Gateway | API | **Credentials:** Personal access token (PAT); Kong Gateway region<br>**Role:** Kong Control Plane Viewer |
| Akamai Security | API Security | **Credentials:** Akamai Security base URL; client ID; client secret; organization ID<br>**Permissions:** Access to create service accounts in Akamai Security; access to apply Akamai correlation policy in target environments<br>**Setup:** Existing services in *Portfolio* catalogs for correlation targets; create a connected app in MuleSoft; configure Akamai-side sync with the MuleSoft connected app. For details, see *Using Akamai for Risk Correlation*. |
| Amazon Bedrock AgentCore MCP | MCP | **Credentials:** Access key ID and secret access key; AWS region<br>**Account:** Active AWS account<br>**Permissions:** IAM user with an inline policy that allows: `bedrock-agentcore:ListAgentRuntimes`; `bedrock-agentcore:GetAgentRuntime`; `bedrock-agentcore:ListAgentRuntimeVersions`; `bedrock-agentcore:ListAgentRuntimeEndpoints`; `bedrock-agentcore:InvokeAgentRuntime` |
| Azure API Management MCP Server | MCP | **Credentials:** Tenant ID; client ID; client secret; subscription ID; resource group; service name<br>**Role:** API Management Service Reader |
| Snowflake MCP Server | MCP | **Credentials:** Snowflake account URL; programmatic access token (PAT)<br>**Account:** Snowflake Enterprise account with MCP servers enabled<br>**Role:** `ACCOUNTADMIN` |

## See Also

- Adding Scanners from Providers
- Viewing Providers
- Managing Scanners
- Using Akamai for Risk Correlation

---

# 2. Using Akamai for Risk Correlation

*Source: `exp-akamai-risk-correlation.adoc`*

Use Akamai for risk correlation to map external security findings to the right services in *Portfolio*. Teams get one view to triage risk, track incidents, and remediate faster. To enable this correlation, configure an Akamai API Security scanner that connects your Akamai account, runs scheduled scans, and surfaces mapped findings on related services in *Portfolio*. The scanner does not import or register third-party services. It correlates risk scores, findings, and incidents for APIs and MCP services in one governance workflow.

The integration relies on a bidirectional sync between your MuleSoft and Akamai API Security tenants: you connect the two tenants with credentials in each direction, Akamai reads your API assets and instances from MuleSoft so it can match its security observations to the correct APIs, and the scanner pulls the resulting findings and incidents back into *Portfolio*.

## How Akamai Scanner Differs from Import Scanners

Most provider scanners discover metadata in external platforms and import services into *Portfolio* catalogs. The Akamai API Security scanner works differently: instead of creating new services, it enriches existing ones with Akamai security data.

## Before You Begin

Before setting up the Akamai scanner, make sure you have:

- Exchange Administrator permission in the target business group.
- Akamai Security base URL, client ID, and client secret.
- Access to apply Akamai correlation policy in the environments you want to scan.
- Existing APIs and MCP services in *Portfolio* catalogs for correlation targets.

For credential and role details, see *Scanner Prerequisites by Provider*.

## Set Up Tenant Connectivity

The integration uses a bidirectional sync between your MuleSoft tenant and your Akamai API Security tenant. You provision and configure both tenants. Each MuleSoft customer tenant (root organization) connects to one Akamai API Security tenant (for example, `mulesoft-partner.example.com`).

Setup involves credentials in both directions:

- A **service account** in Akamai, which you configure on the MuleSoft side so the scanner can read findings and incidents from Akamai.
- A **connected app** in MuleSoft, which you configure on the Akamai side so Akamai can pull API asset and instance information from MuleSoft.

Complete these steps as an organization administrator:

1. **Create a service account in Akamai API Security.** In your Akamai API Security tenant, create a service account and note its client ID, client secret, and organization ID.
2. **Configure the scanner in MuleSoft.** Add an Akamai scanner and enter the Akamai service account credentials (client ID, client secret, and organization ID) and the Akamai base URL, along with a scan frequency. See *Set Up the Akamai Scanner*.
3. **Create a connected app in MuleSoft.** In Anypoint Platform, go to **Access Management > Connected Apps** and create an app that acts on its own behalf (client credentials). Add the **Exchange Viewer** or **Asset Viewer** scope so Akamai can read API instance and asset information, then save. Copy the client ID and client secret.
4. **Configure the Akamai-side sync.** In your Akamai API Security tenant, enter the MuleSoft connected app client ID and client secret so Akamai can pull API asset and instance information from MuleSoft.

## Set Up the Akamai Scanner

Before you set up the scanner, review the prerequisites for Akamai scanners in *Scanner Prerequisites by Provider*.

1. From **Platform > Providers**, select **Akamai**.
2. Enter connection values, and test the connection.
3. Enter scanner metadata, such as scanner name, description, frequency, and time.
4. Apply the Akamai correlation policy to selected environments.
5. Save the scanner and run a discovery scan.

## How the Sync Works

After both tenants are connected, data flows in two directions:

- **MuleSoft to Akamai:** Akamai periodically pulls API instance and asset information from MuleSoft (typically every few hours) and adds it to its API security inventory. Akamai correlates these API instances with the north-south traffic it observes, so it can attach MuleSoft context — such as organization ID, environment ID, and API instance ID — to the endpoints it monitors. The pull runs at the root organization level.
- **Akamai to MuleSoft:** The scanner pulls security findings and incidents from Akamai on the schedule you set, then correlates and stores them so they appear on the related services in *Portfolio*.

For Akamai to observe and correlate traffic, the Akamai correlation policy must be applied to your API instances. This out-of-the-box policy (for Flex Gateway and Mule gateways) stamps correlation headers on API responses so Akamai can match observed traffic to the correct MuleSoft API. Akamai observes north-south traffic only for domains you own and control; MuleSoft-owned domains such as `cloudhub.io` are excluded.

## Review Scanner Detail Tabs

After you select a configured Akamai scanner from the provider list, use scanner detail tabs to monitor scanner status, related services, and configuration values.

For common tab behavior across scanners, see *Viewing Scanner Details*.

- The **Overview** tab highlights correlation policy status and shows whether existing services are being updated with Akamai risk, findings, and incident data.
- The **Services** tab lists services associated with the scanner and shows which existing services are receiving correlated Akamai security data.
- The **Settings** tab shows scanner configuration values, including schedule and provider connection values, and provides options to edit or delete the scanner.

## Apply Missing Correlation Policies

If some environments show that correlation policy isn't applied, you can apply missing policies from the scanner detail page:

1. Open **Platform > Providers** and select the configured Akamai scanner.
2. In **Overview**, check the **Akamai Correlation Policy** status.
3. If status shows missing environments, click **Check again** to apply policy only to those environments.
4. Wait for status to change to **Applied** and confirm all target environments are covered.

If policy application fails, verify your Admin API write permissions and environment access, then retry.

## Where Security Results Appear

After a successful run, Akamai results appear on existing services:

- API list views show values in the **Security Risk** column.
- API detail pages show Akamai security data in **Conformance**.
- Security sections display violation totals, findings, incidents, and endpoint context.
- Finding detail views show fields such as status, type, endpoint path, and mapped frameworks.

## Interpret Risk Status Levels

The **Security Risk** column shows the risk level assigned to correlated Akamai findings:

- **Low:** Lower urgency risk. Review and remediate in your normal security lifecycle.
- **Medium:** Moderate risk. Prioritize remediation after high-risk issues.
- **High:** Elevated risk. Investigate and remediate first.
- **Critical:** Highest urgency risk. Remediate immediately.

## Remediate Risks from the API Conformance Tab

Use the API **Conformance** tab in *Portfolio* to triage and remediate Akamai findings:

1. Open the API from the **APIs** catalog in *Portfolio*.
2. Select **Conformance** and review the **Akamai** section, including findings and incidents.
3. Select a finding to open details, such as endpoint, severity, and mapped standards.
4. Apply recommended remediation policies directly from the finding detail view when available.
5. Re-run the scanner after remediation to confirm updated findings and risk levels.

If no direct remediation policy is available for a finding, use the finding details to update the API configuration in your gateway or upstream system, then scan again to verify the result.

## Troubleshoot Missing Akamai Findings

If a scan completes but results don't appear:

- Verify correlation policy is applied in the same environment as the service instance.
- Confirm the target service already exists in *Portfolio* catalogs.
- Confirm scanner scope and business group match the service location.
- Re-run the scanner after connection or policy changes.

## See Also

- Adding Scanners from Providers
- Viewing Providers
- Viewing Scanner Details
- Managing Scanners
