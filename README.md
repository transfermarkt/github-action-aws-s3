# AWS S3 GitHub Action

## Getting Started

```yml
name: 'Sync S3'

concurrency:
  group: prod_deploy
  cancel-in-progress: false

on:
  repository_dispatch:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  deploy:
    name: 'Sync'
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - uses: badsyntax/github-action-aws-s3@v0.0.1
        name: Sync to S3
        id: sync-s3
        with:
          bucket: 'example-bucket-us-east-1'
          action: 'sync'
          srcDir: './out'
          awsRegion: 'us-east-1'
          prefix: 'custom/folder'
          stripExtensionGlob: '**/**.html'

      - name: Output Synced Files
        run: |
          echo "Synced Files: $S3SyncedFiles"
        env:
          # Use outputs from the S3 step
          S3SyncedFiles: ${{ steps.sync-s3.outputs.S3SyncedFiles }}
```
