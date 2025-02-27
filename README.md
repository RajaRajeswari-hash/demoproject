# demoprojectname: Node.js CI
on:
  pull_request:
    branches:
      - main

jobs:
  ci:
    name: Lint / Build / Test
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18]
      fail-fast: false

    steps:
      - name: Checkout Source Files
        uses: actions/checkout@ee0669bd1cc54295c223e0bb666b733df41de1c5 # v2

      - name: Use Node.js v${{ matrix.node-version }}
        uses: actions/setup-node@7c12f8017d5436eb855f1ed4399f037a36fbd9e8 # v2
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install Dependencies
        run: npm ci

      - name: Lint Files
        run: npm run lint

      - name: Create .env File For CI Builds
        run: cp sample.env .env

      - name: Start Projects
        run: |
          npm run build -- --no-cache
          npm start

      - name: Sleep For 30 Seconds
        run: sleep 30

      - name: Run Tests
        run: npm run test
