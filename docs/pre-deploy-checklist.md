# Pre-Deploy Checklist

Complete these items before the first pipeline run. Details are in
[customer-prerequisites.md](./customer-prerequisites.md) and
[deployment-runbook.md](./deployment-runbook.md).

## 1. Repository values

- [ ] Set real `subscriptionId.dev`, `.test` and `.prod` in
      `azure-pipelines/templates/variables.yml` (shipped as all-zero placeholders).
- [ ] Confirm `runnerResourceGroup`, `resourceGroup.*`, `runnerLocation` and the VNet CIDRs fit your network.
- [ ] Set `deployPipelineId` and `synapseArtifactPipelineId` to the numeric IDs of the pipelines created in step 3.
- [ ] Decide the region per environment. Runner and dev use `westus3`; test and prod use `eastus2`.
- [ ] If prod diagnostics are enabled, replace the example `logAnalyticsWorkspaceId` with a real workspace ID.
- [ ] Review `publicNetworkAccess`. It is `Enabled` in all environments, which suits a PoC only.

## 2. Azure

- [ ] Subscription(s) and resource groups exist, including the shared runner resource group.
- [ ] Each environment's service principal has Contributor on its resource group, and on the shared runner
      resource group (needs `Microsoft.Resources/deployments/read` plus private endpoint, DNS and subnet permissions).
- [ ] Azure resource providers are registered (Synapse, Storage, Key Vault, Network, Compute).
- [ ] The Synapse service principal has a Synapse role (Administrator or Artifact Publisher) in Synapse Studio.

## 3. Azure DevOps

- [ ] Azure Resource Manager service connections (workload identity federation):
      `sc-synapse-runner-wif`, `sc-synapse-dev-wif`, `sc-synapse-test-wif`, `sc-synapse-prod-wif`.
- [ ] Agent pool `synapse-private` exists.
- [ ] Environments `dev`, `test` and `prod` exist, with approvals on test and prod.
- [ ] Pipelines created from `azure-pipelines/deploy.yml` and `azure-pipelines/publish-artifacts.yml`
      (and `rollback-artifacts.yml` if needed). Permit the service connections, pool and environments on first run.
- [ ] Secret pipeline variable `SYNAPSE_SQL_ADMIN_PASSWORD` is set. Otherwise a validation-only placeholder is used.

## 4. First run

- [ ] Run `deploy.yml` with `deploymentTarget = dev`. Confirm Bootstrap, Package and Deploy - dev succeed.
- [ ] Run `publish-artifacts.yml` with `deploymentTarget = dev`.
- [ ] In Synapse Studio, confirm `PL_CopyRawToCurated` appears under Integrate.
- [ ] Upload `sample/raw/sample_customers.csv` to the raw container and run the pipeline.
      The result appears in the curated container.
