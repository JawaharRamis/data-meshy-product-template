# data-meshy-product-template

Template repository for Data Meshy domain teams.

**Domain teams author `product.yaml` and `infra.yaml` only. Platform workflows handle everything else.**

## How it works

1. Use this template to create your domain's data product repository.
2. Copy `products/example/` to `products/<your_product_name>/` and fill in the `REPLACE_ME` placeholders.
3. Push to `main` — the `on-push.yml` workflow calls the platform `provision-product.yml` reusable workflow, which validates your spec, registers the product in the mesh catalog, and applies the Terraform module.

## File reference

| File | Who authors it | Purpose |
|------|---------------|---------|
| `products/*/product.yaml` | Domain team | Data contract: schema, SLA, classification, quality rules |
| `products/*/infra.yaml` | Domain team | Infrastructure knobs: platform version, Glue settings, Iceberg config, S3 source |
| `.github/workflows/on-push.yml` | Platform (read-only) | Calls `provision-product.yml@{platform_version}` on push to main |
| `.github/workflows/deprecate.yml` | Platform (read-only) | Calls `deprecate-product.yml@{platform_version}` via `workflow_dispatch` |
| `.github/workflows/rollback.yml` | Platform (read-only) | Calls `rollback-product.yml@{platform_version}` via `workflow_dispatch` |
| `.github/workflows/upgrade-platform.yml` | Platform (read-only) | Bumps `platform_version` and workflow refs, opens a PR |
| `modules/` | Platform (read-only) | Terraform modules — do not edit |

## Upgrading the platform

When the platform team releases a new version, you will receive a GitHub Issue in this repo titled **"Platform vX.Y available — upgrade when ready"**.

To upgrade:
1. Go to **Actions → Upgrade Platform Version → Run workflow**.
2. Enter the `target_version` (e.g. `v1.3`).
3. Review and merge the PR that the workflow opens.

## Required secrets and variables

Configure these in your repository settings before the first push:

| Name | Type | Description |
|------|------|-------------|
| `AWS_ROLE_ARN` | Secret | ARN of the `DomainGitHubActionsRole` in your domain account |
| `MESH_API_ENDPOINT` | Secret | Base URL of the mesh governance API (no trailing slash) |
| `MESH_DOMAIN_NAME` | Secret | Your domain name (must match the registered domain in the mesh) |
| `AWS_REGION` | Variable | AWS region for your domain account (e.g. `us-east-1`) |
