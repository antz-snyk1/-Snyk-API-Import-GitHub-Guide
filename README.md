# Snyk API Import GitHub Guide - Full Auto

ref: https://github.com/snyk-tech-services/snyk-api-import/tree/master

## Installation (Mac/NPM)
Install Snyk-API-Import tool: 
```bash
npm install snyk-api-import@latest -g
```

Install JQ:
```bash
brew install jq
```

---

## Make directories
Ensure you are in your home directory:

```bash
cd ~
```

Make a parent directory:
```bash
mkdir github_snyk_import
```

Navigate into the directory you just created:
```bash
cd github_snyk_import
```

Make a subdirectory inside it:
```bash
mkdir snyk-log
```

---

## Make Variables

GitHub token:
```bash
export GITHUB_TOKEN=(paste gh token here)
```

Snyk token:
```bash
export SNYK_TOKEN=(paste snyk token here)
```

Snyk Group ID:

```bash
export SNYK_GROUP_ID=(paste snyk group id here)
```

Snyk "Template" org ID:
```bash
export SOURCE_ORG_PUBLIC_ID=(paste snyk template org id here)
```

Var to point the import tool at where to write logs:
```bash
export SNYK_LOG_PATH=~/github_snyk_import/snyk-log
```

For self-hosted GitHub deployments:
```bash
export SOURCE_URL=(paste url to Github server here, including 'https://')
```

---

## Commands

Query the GitHub instance for org data:
```bash
snyk-api-import orgs:data --groupId=$SNYK_GROUP_ID --source=github-enterprise --sourceUrl=$SOURCE_URL --sourceOrgPublicId=$SOURCE_ORG_PUBLIC_ID
```


Review/edit the newly created file:
```bash
jq . snyk-log/group-$SNYK_GROUP_ID-github-enterprise-orgs.json
```

Use the file created from the previous step to create like-for-like Snyk orgs:
```bash
DEBUG=snyk* snyk-api-import orgs:create --noDuplicateNames --file=snyk-log/group-$SNYK_GROUP_ID-github-enterprise-orgs.json
```

Review/edit the newly created file:
```bash
jq . snyk-log/snyk-created-orgs.json
```

Use the file created from the previous step to associate repos from GitHub orgs to corresponding Snyk orgs:
```bash
DEBUG=snyk* snyk-api-import import:data --orgsData=snyk-log/snyk-created-orgs.json --source=github-enterprise --sourceUrl=$SOURCE_URL --sourceOrgPublicId=$SOURCE_ORG_PUBLIC_ID
```

Review/edit the newly created file:
```bash
jq . snyk-log/github-enterprise-import-targets.json
```


Use the file created from the previous step to execute the import of repos to Snyk orgs:
```bash
DEBUG=snyk* snyk-api-import import --file=snyk-log/github-enterprise-import-targets.json
```
