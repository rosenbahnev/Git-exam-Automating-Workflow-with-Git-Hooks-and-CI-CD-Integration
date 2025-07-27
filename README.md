This is the precommit hook:

#!/bin/sh

echo "Checking code format with Prettier..."
npx prettier --check .

if [ $? -ne 0 ]; then
echo "Code is not formatted correctly. Commit aborted."
exit 1
fi

echo "✅ Code is formatted correctly."

==========================================
This is the commit-msg hook

#!/bin/sh

commit_msg=$(head -n1 "$1")

echo "Checking commit message format..."

if echo "$commit_msg" | grep -qE '^TASK-[0-9]+: .+'; then
echo "✅ Commit message format is valid: $commit_msg"
else
echo "❌ Invalid commit message format. $commit_msg"
echo "Expected format: TASK-<number>: Your message"
echo "Example: TASK-123: Fix issue format"
exit 1
fi

==========================================

This is the pre-push hook:

#!/bin/sh

echo "Running tests before push ..."
npm test

if [ $? -ne 0]; then
echo "Tests failed. Push aborted."
exit 1
fi

echo "Tests passed. Proceeding with push"

=========================================

This is the yml file:

name: CI

on:
pull_request:
branches: [ main ]

jobs:
test:
runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18' # or whatever version you use

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test
