Issues in build_package.yml

1. Missing run command for "Build package" step
   The build step is empty. It should run python -m build to actually build the package.

2. Missing dist/ directory creation
   If python -m build isn't run, the dist/ directory won't exist, causing the upload step to fail.

Changes made:
1.Added run: python -m build under "Build package" to ensure the package is actually built.
2.Ensured dist/ directory exists by executing the build command.

========================

Issues in promote_package.yml

1.Incorrect repo names in CLOUDSMITH_STAGING_REPO and CLOUDSMITH_PRODUCTION_REPO
  Staging should be staging, and production should be production, but they are swapped in the env block.

2.Potential issue with jq parsing in PACKAGE_DATA
  If the query returns an empty or unexpected result, .data[0].identifier_perm may not exist, leading to an error.

Changes & Fixes:
1.Corrected repo names: staging and production were swapped.
2.Handled empty jq result safely: Used // empty to prevent errors.
3.Added || echo "{}" for cloudsmith list package: Prevents errors if the command fails.

=========================

Issues in realease_package.yml

1.Typo in the filename
  realease_package.yml should be release_package.yml.

2.Incorrect usage of actions/download-artifact inputs
  repository, github-token, and run-id are not valid inputs for this action.
  The artifact name should be provided, and the path is correctly set.

3.Missing authentication for Cloudsmith CLI
  The workflow doesn't authenticate before using cloudsmith push


Changes & Fixes:
1.Fixed filename: Renamed to release_package.yml.
2.Fixed actions/download-artifact usage: Removed invalid parameters (repository, github-token, run-id).
3.Added authentication step: Cloudsmith API key authentication (secrets.CLOUDSMITH_API_KEY).
4.Improved error handling: Added explicit logging before upload.


============================

Modification in promote_package.yml


Changes Made:
1. Removed manual trigger (workflow_dispatch)
2. Added webhook trigger for package synchronization from Cloudsmith
3. Tagging packages with ready-for-production after synchronization
4. Modified PACKAGE_QUERY to filter for packages tagged ready-for-production
5. Used cloudsmith tag to add the tag before promotion


Summary of Fixes:
1. Webhook Trigger: Now listens for a Cloudsmith repository event (repository_dispatch) when a package is synchronized.
2. Automatic Tagging: New packages are tagged as ready-for-production before promotion.
3. Filter for Tagged Packages: The query now looks for all packages tagged ready-for-production.
4. Loop for Promotion: All ready-for-production packages are promoted in a batch instead of just one.
