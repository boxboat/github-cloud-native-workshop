---
# Page settings
layout: default
keywords:
comments: false

# Hero section
title: Lab - Codespaces requirements
description: Organizational setup that's needed for the Codespaces workshop

# Micro navigation
micro_nav: true

# Page navigation
page_nav:
    next: 
        content: Lab - Codespaces
        url: '/lab-codespaces'
---

## Pre-Requisites
- Modern Web Browser
- Azure Subscription with a Service Principal with Contributor permissions
  -`az ad sp create-for-rbac --name "codespaces-workshop" --role Contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth`
  - Save above output for GitHub setup
- Register the following providers with `az` if not alread registered
  - `az provider register --namespace Microsoft.App`
  - `az provider register --namespace Microsoft.OperationalInsights`
- Storage Account in the sub for state management
  - __Resource Group__: "codespaces-demo-resources"
  - __Storage Account Name__: "codespacesstate$POSTFIX" where $POSTFIX is some random slug
  - __Container Name__: "codespacesstate"
  - Public network access
  - Container should have private access only
- Organization w/Codespaces enabled
  - Azure Subscription SP details as GitHub Org Secrets
    - AZURE_CLIENT_ID
    - AZURE_CLIENT_SECRET
    - AZURE_SUBSCRIPTION
    - AZURE_TENANT_ID
  - Setup tested with "Workshop Setup Test" Action from workshop repo. Modify `setup-test/backend.tf`, in a copy of the workshop template, appropriately to reflect Storage Account name

