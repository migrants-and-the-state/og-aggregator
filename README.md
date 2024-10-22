# og-aggregator

Small repo to build a dashboard aggregating all A-Files published through aperitiiif regardless of Order Group.

## Getting Started

### Prerequisites
- Git
- Ruby

### Steps
1. Install gems
    ``` sh
    bundle install
    ```
2. (OPTIONAL) Add new order groups via OG_ID to the Rakefile
3. Build index & html
    ``` sh
    bundle exec rake build:all
    ```

