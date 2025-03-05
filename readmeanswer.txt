build_package.yml
Issues:
Missing run command for "Build package" step
The build step is empty. It should run python -m build to actually build the package.
Missing dist/ directory creation
If python -m build isn't run, the dist/ directory won't exist, causing the upload step to fail.

Changes made:
Added run: python -m build under "Build package" to ensure the package is actually built.
Ensured dist/ directory exists by executing the build command.

================

Issues in promote_package.yml
Incorrect repo names in CLOUDSMITH_STAGING_REPO and CLOUDSMITH_PRODUCTION_REPO

Staging should be staging, and production should be production, but they are swapped in the env block.
Potential issue with jq parsing in PACKAGE_DATA

If the query returns an empty or unexpected result, .data[0].identifier_perm may not exist, leading to an error.

Changes & Fixes:
✅ Corrected repo names: staging and production were swapped.
✅ Handled empty jq result safely: Used // empty to prevent errors.
✅ Added || echo "{}" for cloudsmith list package: Prevents errors if the command fails.

=====================

Issues in realease_package.yml
Typo in the filename

realease_package.yml should be release_package.yml.
Incorrect usage of actions/download-artifact inputs

repository, github-token, and run-id are not valid inputs for this action.
The artifact name should be provided, and the path is correctly set.
Missing authentication for Cloudsmith CLI

The workflow doesn't authenticate before using cloudsmith push


Changes & Fixes:
✅ Fixed filename: Renamed to release_package.yml.
✅ Fixed actions/download-artifact usage: Removed invalid parameters (repository, github-token, run-id).
✅ Added authentication step: Cloudsmith API key authentication (secrets.CLOUDSMITH_API_KEY).
✅ Improved error handling: Added explicit logging before upload.


============================


Changes Made:
✅ Removed manual trigger (workflow_dispatch)
✅ Added webhook trigger for package synchronization from Cloudsmith
✅ Tagging packages with ready-for-production after synchronization
✅ Modified PACKAGE_QUERY to filter for packages tagged ready-for-production
✅ Used cloudsmith tag to add the tag before promotion


Summary of Fixes:
📌 Webhook Trigger: Now listens for a Cloudsmith repository event (repository_dispatch) when a package is synchronized.
🏷️ Automatic Tagging: New packages are tagged as ready-for-production before promotion.
🔎 Filter for Tagged Packages: The query now looks for all packages tagged ready-for-production.
🚀 Loop for Promotion: All ready-for-production packages are promoted in a batch instead of just one.