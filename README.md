# Vocab CLI

A native CLI tool for vocabulary management with spaced repetition.

## Build

Requires Java 21+ (via SDKMAN) and MongoDB running locally.

```bash
# Build
./gradlew build

# Run
./gradlew run --args="<command> [args...]"

# Native image (requires GraalVM)
./gradlew nativeCompile
```

## Commands

### add `<file.json>`
Add new words from JSON file.

Input format:
```json
{
  "words": [
    {"word": "hello", "meaning": "你好"},
    {"word": "world", "meaning": "世界"}
  ]
}
```

Output:
```json
{"status":"ok","inserted":2,"words":["hello","world"]}
```

### query-due `[date]`
Query words due for review. Defaults to today.

```bash
./gradlew run --args="query-due 2026-04-29"
```

Output:
```json
{"status":"ok","date":"2026-04-29","count":2,"words":[{"word":"hello","nextReview":"2026-04-29"}]}
```

Use `--fetchAll` to include words beyond max review interval.

### review `<file.json>`
Review words and update intervals.

Input format:
```json
{
  "reviews": [
    {"word": "hello", "correct": true},
    {"word": "world", "correct": false}
  ]
}
```

Output:
```json
{"status":"ok","updated":2,"results":[{"word":"hello","correct":true,"nextReview":"2026-05-02","interval":3}]}
```

### import `<file.json>`
Import from old format or array format.

Supports:
- Old map format: `{"hello":{"meaning":"你好"}}`
- Array format: `{"words":[{"word":"hello","meaning":"你好"}]}`

Output:
```json
{"status":"ok","imported":2,"skipped":0}
```

## Configuration

Edit `src/main/resources/application.properties`:

```properties
mongodb.uri=mongodb://localhost:27017
mongodb.database=vocab
mongodb.collection=words
max_review_interval=60
logging.level=INFO
```

## Data Model

```json
{
  "word": "hello",
  "meaning": "你好",
  "correctStreak": 0,
  "nextReview": "2026-04-29",
  "interval": 3,
  "source": {"date": "2026-04-29", "context": ""},
  "reviewHistory": []
}
```

## IntelliJ Setup

1. Open project in IntelliJ
2. Set Project SDK to Java 21 (GraalVM CE)
3. Run Configuration → Program arguments: `query-due 2026-04-29`
