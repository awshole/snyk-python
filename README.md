# Snyk Python Action

A [GitHub Action](https://github.com/features/actions) for using [Snyk](https://app.snyk.io/) for Source Composition Analysis and license identification. Results are uploaded to the Security tab of a given repository, leveraging the SARIF schema.

You can use the Action as follows:

```yaml
name: snyk-analysis 
on:
  workflow_dispatch:
  push: 
    branches: [main]
  pull_request:
    branches: [main]
jobs:     
  snyk-analysis:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout repository
      uses: actions/checkout@v2
    - name: Specify Python version
      uses: actions/setup-python@v2
      with:
        python-version: '3.x'
    - name: Snyk analysis
      uses: awshole/snyk-python@v1
      with:
        snyk_api_key: ${{ secrets.SNYK_TOKEN }}
        path_to_dependency_file: requirements.txt
        upload_sarif: true
        snyk_github_integration_token: ${{ github.token }}
        repository: ${{ github.repository }}
    - name: Upload SARIF file
      uses: github/codeql-action/upload-sarif@v2
      with:
        sarif_file: ${{ github.workspace }}/snyk.sarif
```

The Snyk Python Action has properties which are passed to the underlying shell executing custom scripts. These are
passed to the action using `with`.

| Property                      | Required | Default | Description                                                                                                                                                                                             |
|-------------------------------|----------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| snyk_api_key                  | true     |         | Expects a string value corresponding to the API key to use when accessing the Snyk organization.                                                                                                        |
| path_to_dependency_file       | true     |         | Expects the relative GitHub path to the dependency file to test.                                                                                                                                        |
| integrate_with_snyk_platform  | false    | false   | If this is true, the repository will be integrated with the Snyk platform.                                                                                                                              |
| snyk_org_id                   | false    |         | Expects a string value corresponding to the Snyk organization ID. It expects a GUID format. This is required if 'integrate_with_snyk_platform' is 'true'.                                               |
| snyk_integration_id           | false    |         | Expects a string value corresponding to the Integration ID for a source control provider. This is required if 'integrate_with_snyk_platform' is 'true'.                                                 |
| repository                    | false    |         | Expects the GitHub repository to import to the Snyk platform or post a GitHub Issue to. This is required if either 'integrate_with_snyk_platform', 'create_github_issues', or 'upload_sarif' is 'true'. |
| branch_name                   | false    |         | Expects the GitHub repository branch name that should be imported into Snyk. This is required if 'integrate_with_snyk_platform' or 'upload_sarif' is 'true'.                                            |
| create_github_issues          | false    | false   | If this is true, details of the Snyk scan will be posted to the Issues tab of a repository.                                                                                                             |
| upload_sarif                  | false    | false   | If this is true, details of the Snyk scan will be uploaded as a SARIF file to the Security tab of a repository.                                                                                         |
| github_issue_assignee         | false    |         | Expects a string value corresponding to the GitHub user to assign issues to if 'create_github_issues' is 'true'.                                                                                        |
| security_issues_labels        | false    |         | The labels that should be applied to security-related GitHub Issues if 'create_github_issues' is 'true'.                                                                                                |
| license_issues_labels         | false    |         | The labels that should be applied to license compliance-related GitHub Issues if 'create_github_issues' is 'true'.                                                                                      |
| snyk_github_integration_token | false    |         | GitHub token to use for posting issues. This is required if 'create_github_issues' or 'upload_sarif' is 'true'.                                                                                         |